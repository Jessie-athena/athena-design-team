# 狀態 Stepper（作業檔）

> 屬於 `profiles/erp-transaction.md` 的延伸元件規格。
> 載入時機：**作業檔**（transaction documents）必載；設定檔無 Stepper（`erp-setup.md` 已明定），免載（由 `SKILL.md §支援檔案` 規定——該處為載入規則的**單一來源**）。
>
> 上層 profile：`profiles/erp-transaction.md`
> 同層元件：`ListSearch.md` / `DataGrid.md` / `FormGroup.md` / `FormFooter.md` / `SummaryCard.md` / `Stepper.md` / `Permissions.md` / `RelBanner.md` / `Skeleton.md`
>
> **上游設計文件**：`../../../design-system-architect/references/components/Stepper.md`（格式見 `component-doc-schema.md`）。分工——**設計文件**是 what/why/token/state/a11y 的權威；**本 profile** 是「如何用單檔 HTML/CSS class 落地」的實作層。本檔的語意色 / 步序狀態決策待收編進設計文件後改為引用，避免兩處漂移。

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
| **庫存單・含過場步** | 5 步 | Odoo 原生 5 階，5 標籤固定（草稿 / 等待前置作業 / 已提交 / 就緒 / 已核准），含 2 個過場顯示步；唯讀型恆停終態、可編輯型另有 voided 分支 | 本檔 §庫存單 5 步（含過場步）+ `erp-transaction.md §進銷存庫存異動單狀態機` |

> 前三個變體共用本檔的結構命名、判定邏輯與樣式 token；差異只在第 ④ 步插槽的狀態集 / 標籤 / 配色。**不可混用**於同一模組。庫存單 5 步另立規則（見專節），其過場步橘色與前三者不同。
>
> **庫存模組內部仍分流，勿一律套 5 步**：5 步**只**用於 **stock.picking 實體異動單**（入庫 / 出庫 / 領料 / 調撥——有 waiting/assigned 過場態）；**差異調整單**（盤點 / 耗用，`psi.stock.count` 類，無實體 picking 流程）走**基本型 3 步 + voided**（`草稿 / 已提交 / 已核准` + 已作廢分支），**不套過場步**。判斷依據：單據 PRD 的狀態機是否含 `waiting` / `assigned`——有才用 5 步。以 PRD / 既有 prototype 的狀態流程為準（資料來源權重最高）。

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

> 步數**動態**：依各模組狀態機而定，最少 3 步、上不封頂（非固定 4 步）。以下狀態通則適用任意步數的每一步。

| class | Bubble 內容 | Bubble 色 | Label 色 | 用途 |
|---|---|---|---|---|
| `--done` | check 圖示 | `rgb(var(--color-sf-success))` /* #12B76A 綠 */ | `var(--text-primary)` /* #0F172A */ | 已通過的步驟 |
| `--current` | 數字 | `rgb(var(--color-sf-primary))` /* #2877EE 藍 */ + 內白環 `inset 0 0 0 1px var(--bg-surface-default)` | `var(--text-primary)` / 500 | 進行中（當前）步驟 |
| `--partial` / `--final` / `--settled` | 數字 | **一律 `rgb(var(--color-sf-primary))` 藍 + 內白環**（= `--current` 視覺；驗收 / 結轉模型皆同，不再分靛 / 灰） | `var(--text-primary)` / 500 | 終態步：部分驗收·採購 / 已驗收·已結案 / 已結清；色相同，僅 label 由 `step4Label` 區分 |
| `--placeholder` | 數字 | `rgba(var(--color-sf-primary), .08)` over `var(--bg-surface-default)`，框 `var(--border-default)` /* #D7DAE0 */ | `var(--text-secondary)` /* #67717E */ | 終態步尚未達到（未確定） |
| （無 modifier，pending） | 數字 | `rgba(var(--color-sf-primary), .08)` over `var(--bg-surface-default)`，框 `var(--border-default)` | `var(--text-secondary)` | 未達的步驟 |

> **當前步 / 終態步 bubble 完整規格**（primary 藍實心）：
> ```css
> border-radius: 100px;                                   /* 全圓 = var(--radius-full) */
> border: 1px solid var(--ColorSf-primary, #2877EE);
> fill:   var(--ColorSf-primary, #2877EE);                /* 落地以 background 實作 */
> box-shadow: 0 0 0 1px var(--ColorSf-surface, #FFF) inset;  /* 內白環 */
> ```
> **設計修正（2026-06-21）**：取消「結轉模型 partial 靛色 / done 灰色」分歧——當前步與終態步**一律 primary 藍**。理由：步數隨表單動態（3～N 步），不宜以「第 ④ 步特定色」綁死；終態步用 primary 藍與當前步一致，靠 label 區分語意即可。

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

> **語意色（基本型 / 結轉 / 驗收模型）僅四種**：綠（已完成的中間步）、藍（當前步 + 所有終態步，含 partial / received / settled / done）、紅（作廢 / 取消，走 voided-banner 不進 stepper）、中性灰（未達 / placeholder）。**禁**為個別狀態另造新色（已於 2026-06-21 取消「靛」「結轉已結案灰」兩種變體色）。
> **唯一例外**：**庫存單 5 步**的**過場步**（`waiting` 等待前置作業 / `assigned` 就緒）當前態用**橘**（`rgb(var(--color-sf-warning))` #F79009 + 內白環），對應 Odoo stock.picking 過場態的既有設計，**僅限該變體**（見 §庫存單 5 步）；其餘步序仍走上述四色。

## 庫存單 5 步（含過場步）

> 對齊基準：`design-prototype/web-erp/庫存模組`（入庫 / 出庫 / 領料 / 調撥）。**stock.picking 實體異動單**——不論唯讀或可編輯——採 Odoo 原生 stock.picking 的 5 階生命週期，5 個顯示標籤固定為 **草稿 / 等待前置作業 / 已提交 / 就緒 / 已核准**；其中第 2、4 階是**過場顯示步**（單據不停留、僅反映系統流程位置，橘色）。狀態機本體見 `erp-transaction.md §進銷存庫存異動單狀態機`。

> **適用範圍（重要，勿一律套用）**：本 5 步**只**用於庫存模組的 **stock.picking 實體異動單**（入庫 / 出庫 / 領料 / 調撥，狀態含 `waiting` / `assigned` 過場態）。**判斷依據＝該單 PRD 的狀態機是否含 `waiting` / `assigned`**。
> - **庫存模組的差異調整單**（盤點 / 耗用，`psi.stock.count` 類，狀態為 `草稿 / 已提交 / 已核准 / 已作廢`、無過場態）→ 走**基本型 3 步 + voided**（見本檔頂部基本型），**不套**本 5 步。
> - **非庫存模組**（財務、請購、採購、銷貨折讓等）→ 各依其狀態機（基本型 / 結轉 / 驗收），**禁用**本 5 步過場步模型。
> - 衝突時以 PRD / 既有 prototype 的狀態流程為準（資料來源權重最高，見 `SKILL.md §資料來源權重`）。

### 兩種庫存單變體（共用 5 標籤、共用過場橘色）

| 變體 | 典型單據 | state keys | 使用者動作 | voided 分支 |
|---|---|---|---|---|
| **唯讀（系統產生）** | 入庫單 / 出庫單 | `draft` / `waiting` / `confirmed` / `assigned` / `done` | 無（上游核准後系統產生、恆停 `done`） | 無（沖銷改產生沖銷單，非作廢） |
| **可編輯** | 領料單 / 耗用單 | `draft` / `waiting` / `submitted` / `assigned` / `approved` | 有（`submit` / `approve` / `void`，過場態為核准流程中的瞬時態） | **有**：`voided` 時整段改 `.voided-banner`（見 §voided-banner） |

> 兩變體**標籤相同、過場步配色相同**，差別只在：state key 命名（confirmed↔submitted、done↔approved）、有無使用者動作、有無 voided 分支。List 狀態 chip 對應見 `DataGrid.md §狀態 Chip`（含 `st-chip--waiting` / `st-chip--assigned` 橘色過場 chip）。

**5 步標籤與性質**

| n | label | 唯讀 state | 可編輯 state | 性質 |
|---|---|---|---|---|
| 1 | 草稿 | `draft` | `draft` | 一般步 |
| 2 | 等待前置作業 | `waiting` | `waiting` | **過場步**（transitional，橘） |
| 3 | 已提交 | `confirmed` | `submitted` | 一般步 |
| 4 | 就緒 | `assigned` | `assigned` | **過場步**（transitional，橘） |
| 5 | 已核准 | `done` | `approved` | 一般步（終態 / 可編輯型再分 voided 分支） |

- **唯讀型恆停終態**：系統產生即為終態（唯讀 `done` / 可編輯歷史單 `approved`），stepper 顯示前 4 步 `--done` 綠、第 5 步 `--current`。可編輯型在 draft / submitted 等中間態時照下方判定逐步呈現。
- **過場步配色**：過場步（waiting / assigned）**當其為當前步**時，bubble 與 label 用**橘色** `rgb(var(--color-sf-warning))`（#F79009）+ 內白環，標示「系統處理中、非使用者待辦」；一旦越過則照常規 `--done` 綠。這是與前三變體最大的差異——前三者沒有橘色過場步。
- **判定邏輯**：沿用通用規則（`n < stepCur` → `--done` 綠 / `n === stepCur` → current / `n > stepCur` → 灰），唯「current 且該步是過場步」時 current 色由藍改橘。
- **可編輯型 voided**：`form.state === 'voided'` 時整段 Stepper 改 `.voided-banner`（紅色「已作廢」徽章），不顯示 5 步（見 §voided-banner）。

```js
// state keys 依變體選一組；標籤與過場判定共用
const STK_STEPS = [
  { n:1, label:'草稿' },
  { n:2, label:'等待前置作業', transitional:true },
  { n:3, label:'已提交' },
  { n:4, label:'就緒',         transitional:true },
  { n:5, label:'已核准' },
];
// 唯讀：{draft:1,waiting:2,confirmed:3,assigned:4,done:5}；可編輯：{draft:1,waiting:2,submitted:3,assigned:4,approved:5}
function stkStepCur(s, map){ return map[s] || 1; }
// current 且該步 transitional → 額外掛 .stepper__step--waiting（橘）；其餘同通用 stepClass
function stkStepClass(n,s,map){
  const c = stkStepCur(s,map);
  if (n < c) return 'stepper__step--done';
  if (n === c) return STK_STEPS[n-1].transitional ? 'stepper__step--current stepper__step--waiting' : 'stepper__step--current';
  return '';
}
```

> 對應樣式：`.stepper__step--waiting .stepper__bubble { background: rgb(var(--color-sf-warning)); box-shadow: inset 0 0 0 1px #fff; }` 與 `.stepper__step--waiting .stepper__label { color: var(--text-primary); font-weight: 500; }`。橘色**只**用於過場步的 current 態，不擴及連接線（連接線仍依 is-done / is-current 綠 / 藍）。

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
> **簽名差異**：本檔為可單測的純函式版（顯式傳 `s` 狀態）；`app.js` 實作慣例是閉包版（內部讀 `form.status`，呼叫端只傳 `n`，如 `stepClass(1)`），見 `REFERENCE.md §app.js 骨架`。
> Template 內的 Vue binding 寫法見 `templates/module-page.html`（基本型）與 `templates/psi-transaction-page.html`（4 步動態）。
