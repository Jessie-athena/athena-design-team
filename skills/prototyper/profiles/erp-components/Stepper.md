# 狀態 Stepper（作業檔）

> 屬於 `profiles/erp-transaction.md` 的延伸元件規格。
> 載入時機：**作業檔**（transaction documents）必載；設定檔無 Stepper（`erp-setup.md` 已明定），免載（由 `SKILL.md §支援檔案` 規定——該處為載入規則的**單一來源**）。
>
> 上層 profile：`profiles/erp-transaction.md`
> 同層元件：`ListSearch.md` / `DataGrid.md` / `FormGroup.md` / `FormFooter.md` / `SummaryCard.md` / `Stepper.md`

---

作業檔 Form View Summary Card 內的流程指示器（Layout A 上區右側 / Layout B 右區，見 `SummaryCard.md`），以固定步驟呈現單據生命週期。
**Read-only**——純反映單據狀態，不接受點擊；狀態推進一律由 Form Footer 動作按鈕（或下游單據回寫 / 結轉結果）驅動。
終止分支狀態（`voided` 已作廢 / `cancelled` 已取消）**不進入 Stepper**：整段以 `.voided-banner` 取代（見 §voided-banner）。

## 步數與適用 pattern

步數與狀態集**依各模組 PRD 的狀態機決定**（R1 PRD 完整性：PRD 沒列的狀態禁自動補）。已驗證 pattern：

| Pattern | 步數 | 狀態集 | 規格出處 |
|---|---|---|---|
| 基本型 | 3 步 | canonical 4 值（`draft` / `submitted` / `approved` + `voided` 分支） | 本檔 |
| 動態第 ④ 步・**驗收模型** | 4 步 | 七值（+ `partial` / `received` / `settled`），由下游驗收單回寫；首見於採購單 | 本檔 + `erp-transaction.md §進銷存擴充狀態機（七值驗收模型）` |
| 動態第 ④ 步・**結轉模型** | 4 步 | 6 值（+ `partial` / `done` / `cancelled`），由本單結轉進度驅動；首見於請購單 | 第 ④ 步插槽與 Layout 見 `SummaryCard.md`；狀態機見 `erp-transaction.md §進銷存擴充狀態機` |

> 兩個動態變體共用本檔的結構命名、判定邏輯與樣式 token；差異只在第 ④ 步插槽的狀態集 / 標籤 / 配色。**不可混用**於同一模組。

## 七狀態總表（驗收模型）

| state | 標籤 | Chip class | 說明 |
|---|---|---|---|
| `draft` | 草稿 | `st-chip--draft` | 建立、編輯中，尚未取號 |
| `submitted` | 已提交 | `st-chip--submitted` | 已送出待核准；可作廢 |
| `approved` | 已核准 | `st-chip--approved` | 核准生效；可取消核准、作廢、產生驗收單 |
| `partial` | 部分驗收 | `st-chip--partial` | 部分到貨；**唯一可「結清」的狀態** |
| `received` | 已驗收 | `st-chip--received` | 全部到齊（終態） |
| `settled` | 已結清 | `st-chip--settled` | 放棄剩餘未到量後結清（終態） |
| `voided` | 已作廢 | `st-chip--voided` | 作廢（終態），**不進入 Stepper** |

> Chip 視覺規格：List 表格內（28px 緊湊版）見 `DataGrid.md §狀態 Chip`；獨立使用（36px DS Chips 本體，如 voided-banner 內徽章）見本檔 §voided-banner。兩種尺寸並存，色彩模式共用。

## 狀態 → 步序映射（stepCur）

```js
// 驗收模型（七值）
{ draft:1, submitted:2, approved:3, partial:4, received:5, settled:4, voided:3 }
// 結轉模型（6 值）——done 映射 5（比照 received）：已結案時三條連接線全綠
{ draft:1, submitted:2, approved:3, partial:4, done:5, cancelled:3 }
```

驗收模型各狀態的呈現：

| state | 步序 | 第 1–3 步 | 第 ④ 步呈現 |
|---|---|---|---|
| `draft` | 1 | ① 當前 | placeholder「已驗收」灰 |
| `submitted` | 2 | ① 完成、② 當前 | placeholder |
| `approved` | 3 | ①② 完成、③ 當前 | placeholder |
| `partial` | 4 | ①②③ 完成 | `--partial` 藍「部分驗收」 |
| `received` | 5 | ①②③ 完成 | `--final` 藍「已驗收」 |
| `settled` | 4 | ①②③ 完成 | `--settled` 藍「已結清」 |
| `voided` | — | 不顯示 Stepper，整段改 `.voided-banner` | — |

**判定邏輯**（每步 `n` 相對 `stepCur`；兩變體與基本型共用）：

| n vs stepCur | 步驟 class | 左側連接線 class |
|---|---|---|
| n < 當前 | `--done`（綠 + check 圖示） | `.is-done`（綠） |
| n = 當前 | `--current`（藍 + 內白環，顯示數字） | `.is-current`（藍） |
| n > 當前 | 預設灰（顯示數字） | 預設灰 |

- **IMPORTANT:** 第 ④ 步是例外——永遠以 `step4Class` / `step4Label` 計算，**不套用**上表的 `--current`。
- 驗收模型的「部分驗收 / 已驗收」**並非按鈕觸發**，由驗收單的驗收量決定——尚有未到量為部分驗收，全部到齊為已驗收。

**基本型（3 步）**：同一套判定邏輯，`stepCur = { draft:1, submitted:2, approved:3 }`，無第 ④ 步與 `step4*` helper；`voided` 同樣整段改 `.voided-banner`。

## Anatomy

```html
<!-- voided / cancelled 時整段以 .voided-banner 取代（見 §voided-banner） -->
<div class="stepper">
  <div class="stepper__step stepper__step--done">
    <span class="stepper__bubble"><iconify-icon icon="material-symbols:check"></iconify-icon></span>
    <span class="stepper__label">草稿</span>
  </div>
  <span class="stepper__line is-done"></span>
  <div class="stepper__step stepper__step--done"> <!-- … 已提交 --> </div>
  <span class="stepper__line is-current"></span>
  <div class="stepper__step stepper__step--current">
    <span class="stepper__bubble">3</span>
    <span class="stepper__label">已核准</span>
  </div>
  <span class="stepper__line"></span>
  <div class="stepper__step stepper__step--placeholder">
    <span class="stepper__bubble">4</span>
    <span class="stepper__label">已驗收</span>
  </div>
</div>
```

## 樣式規則

### 步驟狀態

| class | Bubble 內容 | Bubble 色 | Label 色 | 用途 |
|---|---|---|---|---|
| `--done` | check 圖示 | `rgb(var(--color-sf-success))` /* #12B76A 綠 */ | `var(--text-primary)` /* #0F172A */ | 已通過的步驟 |
| `--current` | 數字 | `rgb(var(--color-sf-primary))` /* #2877EE 藍 */ + 內白環 `inset 0 0 0 1px #fff` | `var(--text-primary)` / 500 | 進行中的步驟 |
| `--placeholder` | 數字 4 | `rgba(var(--color-sf-primary), .08)` over `var(--bg-surface-default)`，框 `var(--border-default)` /* #D7DAE0 */ | `var(--text-secondary)` /* #67717E */ | 第 ④ 步尚未確定 |
| `--partial` | 數字 4 | 驗收模型：`rgb(var(--color-sf-primary))` 藍 + 內白環／結轉模型：**靛色**填充（見 `SummaryCard.md`） | `var(--text-primary)` / 500 | 部分驗收 / 部分採購（進行中） |
| `--final` | 數字 4 | 驗收模型：`rgb(var(--color-sf-primary))` 藍 + 內白環／結轉模型：**灰色**填充（已結案；刻意不用 `--done` 綠，避免與步驟綠底混淆） | `var(--text-primary)` / 500 | 已驗收 / 已結案（終態） |
| `--settled` | 數字 4 | `rgb(var(--color-sf-primary))` + 內白環（僅驗收模型） | `var(--text-primary)` / 500 | 已結清（終態） |
| （無 modifier，pending） | 數字 | `rgba(var(--color-sf-primary), .08)` over `var(--bg-surface-default)`，框 `var(--border-default)` | `var(--text-secondary)` | 未達的步驟 |

### 連接線 `.stepper__line`

| class | 色彩 | 規則 |
|---|---|---|
| （無） | `var(--border-default)` /* #D7DAE0 灰 */ | 區段在當前步驟之後（未達） |
| `.is-done` | `rgb(var(--color-sf-success))` /* #12B76A 綠 */ | 區段在當前步驟之前（已過） |
| `.is-current` | `rgb(var(--color-sf-primary))` /* #2877EE 藍 */ | 區段正連向當前步驟 |

### 尺寸與字體

| 項目 | 規格 |
|---|---|
| Bubble | 32 × 32px，圓角 999px（`--radius-full`），`1px solid var(--border-default)` |
| Bubble 內字 / 圖示 | Roboto 14px/400；check 圖示 18px（`material-symbols:check`，筆畫型無 `-outline`） |
| Label | Noto Sans TC 13px/400（**當前與終態 500**） |
| Step 最小寬 | 64px（`flex: 0 0 auto`），bubble 與 label gap 6px |
| Line | 高 2px，`flex: 1 1 28px`，min 28 / max 56px，`margin-top: 16px` 對齊圓心 |
| 容器 | `.stepper { display: flex; align-items: flex-start; gap: 16px; }` |
| 轉場 | `background / color 200ms ease-out` |

> **語意色僅五種**：綠（完成）、藍（當前 / 進行中 / 驗收模型終態）、靛（結轉模型 partial）、紅（作廢 / 取消）、中性灰（未達 / 結轉模型已結案）。**禁**為個別狀態另造新色。

## voided-banner

`voided`（驗收模型 / canonical）或 `cancelled`（結轉模型）時整個 Stepper 隱藏，原位置改顯示 `.voided-banner`，內含一顆紅色 `st-chip` 徽章（`st-chip--voided`「已作廢」/ `st-chip--cancelled`「已取消」）：

```html
<div class="voided-banner">
  <span class="st-chip st-chip--voided voided-banner__pill">已作廢</span>
</div>
```

徽章視覺基準 = **DS Chips 元件（danger variant）**：

- Anatomy：DS Chips（Avatar / Left Icon / Label / Right Icon 槽位皆 `display: none`，僅 Label）
- 高 36px、padding `8px 16px`、圓角 48px（`--radius-full`）
- 背景 `rgba(var(--color-sf-error), .12)` /* $danger 12% */
- 邊框 `1px solid rgb(var(--color-sf-error))` /* #F4493E */
- Label：Roboto 12px / Medium / line-height 130% / letter-spacing 0.1px / `rgb(var(--color-sf-error))`

## 狀態流轉（驗收模型）

```
主線：  草稿 ──提交──▶ 已提交 ──核准──▶ 已核准 ──產生驗收單──▶ 部分驗收
驗收：  部分驗收 ──全部到齊──▶ 已驗收（終態）
        部分驗收 ──結清（放棄未到量）──▶ 已結清（終態）
回退：  已核准 ──取消核准──▶ 已提交
終止：  已提交 / 已核准 ──作廢──▶ 已作廢（終態）
```

- **IMPORTANT:**（再次核准警示）單據曾被「取消核准」（`was_unapproved`）後回到已提交狀態時，表單需顯示警示橫幅：「此單曾被取消核准，請重新確認明細後再次提交核准。」
- 結轉模型的流轉（含 `action_convert_po` / `action_cancel`）見 `erp-transaction.md §進銷存擴充狀態機`。

## 引用程式碼（驗收模型）

helper 命名沿用 `SummaryCard.md §JS helper 約定`（`stepState` / `stepClass` / `lineClass` / `step4Class` / `step4Label`），輸出為本檔新命名 class：

```js
const STATE_META = {
  draft:    { label:'草稿',     chip:'st-chip--draft' },
  submitted:{ label:'已提交',   chip:'st-chip--submitted' },
  approved: { label:'已核准',   chip:'st-chip--approved' },
  partial:  { label:'部分驗收', chip:'st-chip--partial' },
  received: { label:'已驗收',   chip:'st-chip--received' },
  settled:  { label:'已結清',   chip:'st-chip--settled' },
  voided:   { label:'已作廢',   chip:'st-chip--voided' },
};

function stepCur(s){ return {draft:1,submitted:2,approved:3,partial:4,received:5,settled:4,voided:3}[s]||1; }
function stepState(n,s){ const c=stepCur(s); return n<c?'done':n===c?'current':'pending'; }
function stepClass(n,s){ const st=stepState(n,s); return st==='pending'?'':'stepper__step--'+st; }
function lineClass(n,s){ const c=stepCur(s); return n<c?'is-done':n===c?'is-current':''; }
function stepIsDone(n,s){ return n<stepCur(s); }
function step4Class(s){ return s==='received'?'stepper__step--final':s==='partial'?'stepper__step--partial':s==='settled'?'stepper__step--settled':'stepper__step--placeholder'; }
function step4Label(s){ return s==='partial'?'部分驗收':s==='settled'?'已結清':'已驗收'; }
```

> 基本型（3 步）省略第 ④ 步與 `step4Class` / `step4Label`；結轉模型把 `stepCur` 換成 6 值映射、`step4Class/Label` 換成 partial/done/placeholder 三選一（`SummaryCard.md`）。
> Template 內的 Vue binding 寫法見 `templates/module-page.html`（基本型）與 `templates/psi-transaction-page.html`（4 步動態）。
