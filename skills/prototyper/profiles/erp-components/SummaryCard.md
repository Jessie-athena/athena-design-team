# Summary Card 結構與互動

> 屬於 `profiles/erp-transaction.md` 的延伸元件規格。
> 載入時機：實作作業檔 Form View（或審查含 Summary Card 的 prototype）時依需求載入。
>
> 上層 profile：`profiles/erp-transaction.md`
> 同層元件：`ListSearch.md` / `DataGrid.md` / `FormGroup.md` / `FormFooter.md` / `SummaryCard.md`

---

作業檔 Form View 頂部的摘要卡。依模組需求有**兩種 layout**，共用「sticky 置頂、無 shadow、內含狀態 stepper」三大原則。

## 兩種 Layout 對照

| Layout | 適用 | 結構 | stepper |
|---|---|---|---|
| **A — 多指標（上下兩塊）** | 財務作業檔（如付款 / 請款；金額需拆稅額 / 互抵等多指標） | 上區：標題 + stepper；下區：多個指標（總金額 / 幣別 / 建立者）+「已產生傳票」chip | canonical 3 步（詳 `erp-transaction.md §Stepper 三狀態結構`） |
| **B — 單指標 + stepper（左右兩區）** | 進銷存作業檔（如請購單；單一關鍵金額即足） | 左 `summary-card__left`：單一強調金額；右 `summary-card__right`：stepper | 視狀態機而定，6 值用 4 步動態 stepper（見下） |

> Layout A 的完整規格見 `erp-transaction.md §Summary Bar 結構`（本檔不重複）。
> 本檔主述 **Layout B** 與其 **4 步動態 stepper**。

---

## Layout B — 單指標 + stepper

### DOM Anatomy

```
.summary-card-wrap                 (外層：負責 padding 與不透明背景，sticky 置頂)
└─ .summary-card                   (flex row · 左右兩區)
   ├─ .summary-card__left          (單指標金額)
   │  ├─ .summary-card__label      (指標名稱，如「預估總金額」)
   │  └─ .summary-card__amount     (金額：字級放大、加粗、主色強調)
   └─ .summary-card__right         (stepper 或 cancelled/voided pill)
      └─ .stepper / .voided-banner
```

### 視覺原則

- `position: sticky; top: 0`，滾動時固定在 main panel 頂部；**無 shadow**（DS 卡片無陰影）
- 左指標是「本單據最關鍵數字」：字級放大、加粗、主色強調；唯讀；幣別符號 + 千分位
- 左右兩區之間靠 `justify-content: space-between` 分置；不加分隔線
- padding 由 `.summary-card-wrap` 控制（上下 24px，左右隨 main panel）

> **單指標設計理由**：當單據階段尚未涉及稅額 / 互抵等複雜計算（如請購階段），只需呈現單一彙總金額供核准參考。需要多指標時改用 Layout A。

---

## 4 步動態 Stepper（搭配進銷存擴充狀態機）

當狀態機為 6 值（`draft / submitted / approved / partial / done / cancelled`，詳 `erp-transaction.md §進銷存擴充狀態機`）時，stepper 為 **4 步**，前 3 步固定，第 ④ 步是**三選一動態插槽**。

> canonical 3 步 stepper（財務 4 值狀態機）見 `erp-transaction.md §Stepper 三狀態結構`；本節是其延伸，**沿用三狀態（pending / active / done）的圓圈與連接線視覺**，僅多一個動態插槽步。

### 步驟結構

| 步 | 名稱 | 性質 |
|---|---|---|
| ① | 草稿 | 固定 |
| ② | 已提交 | 固定 |
| ③ | 已核准 | 固定 |
| ④ | **動態插槽** | 依當前狀態值渲染對應名稱與配色（三選一互斥） |

### 第 ④ 步三選一插槽

| 當前狀態 | 第 ④ 步顯示 | class modifier | 配色 | 性質 |
|---|---|---|---|---|
| `partial`（部分採購） | 部分採購 | `stepper__step--partial` | **靛色**填充 | 進行中（當前步） |
| `done`（已結案） | 已結案 | `stepper__step--final` | **灰色**填充 | 終態（**刻意不用 `--done`**——`--done` 是步驟 1–3 的「已通過綠底」；已結案是終態灰，須用獨立 modifier 與綠底區隔） |
| `draft` / `submitted` / `approved`（前三步階段） | 「待結轉」或「—」 | `stepper__step--placeholder` | 圓底**淺灰** placeholder | 尚未定 |

> `cancelled`（已取消）**不進 stepper**：整條 stepper 改顯示 `.voided-banner` 內一顆 `st-chip st-chip--cancelled`「已取消」pill（紅色），與財務 `voided → .pill-voided` 同理。

### 狀態 × step 對應矩陣

| `form.state` | ① 草稿 | bar1 | ② 已提交 | bar2 | ③ 已核准 | bar3 | ④ 插槽 |
|---|---|---|---|---|---|---|---|
| `draft` | active | pending | pending | pending | pending | pending | placeholder |
| `submitted` | done ✓ | done | active | active | pending | pending | placeholder |
| `approved` | done ✓ | done | done ✓ | done | active | active | placeholder |
| `partial` | done ✓ | done | done ✓ | done | done ✓ | done | **partial（靛，當前）** |
| `done` | done ✓ | done | done ✓ | done | done ✓ | done | **final（灰，終態；class `--final` 非 `--done`）** |
| `cancelled` | ⛔ 整個 stepper 隱藏，改顯示 `.voided-banner` →「已取消」pill | — | — | — | — | — | — |

> `was_cancelled = true` 且回到 `submitted` 時：stepper 維持四步（當前 active 落在第 ②），第 ④ 步仍 placeholder；另於 Summary Card 下方插入「再次核准警示」`form-banner.is-warning`（詳 `erp-transaction.md §進銷存擴充狀態機`）。

### JS helper 約定

`app.js` 提供下列 helper（命名沿用實際設計）：

| helper | 回傳 | 說明 |
|---|---|---|
| `stepState(n)` | `'done'` / `'active'` / `'pending'` | 第 n 步（1–3）的三狀態；對照上表 |
| `stepClass(n)` | step modifier class | 由 `stepState(n)` 映射（pending 無 modifier） |
| `lineClass(n)` | bar modifier class | 連接線狀態 = 左側 step 狀態（done / active / 無） |
| `step4Class` | `stepper__step--partial` / `--final` / `--placeholder` | 第 ④ 步插槽 class，依 `form.state`（已結案用 `--final` 灰，**非** `--done` 綠） |
| `step4Label` | `'部分採購'` / `'已結案'` / `'待結轉'` | 第 ④ 步文字 |
| `isCancelled` | boolean | `form.state === 'cancelled'`，true 時整條 stepper 換 pill |

### 容器

```css
.stepper { display: flex; align-items: flex-start; gap: 16px; }
```

> 圓圈 `.stepper__num`（32×32, radius 100px）、連接線 `.stepper__bar`（width 40 / border-top 2px）的 pending / active / done 三狀態 token，沿用 `erp-transaction.md §Stepper 三狀態結構`。本檔擴充三個第 ④ 步專用 modifier（皆非 canonical）：`--partial` 靛色（進行中）、`--final` 灰色（已結案終態，**與 `--done` 綠底區隔**）、`--placeholder` 淺灰（待定）；配色對齊 DS 對應語意色。

---

## 自檢

- [ ] Summary Card `sticky` 置頂、無 shadow
- [ ] Layout B：左單指標（放大 / 加粗 / 主色）+ 右 stepper
- [ ] 4 步 stepper 前 3 步固定、第 ④ 步依 `form.state` 三選一（partial 靛 `--partial` / 已結案灰 `--final` / placeholder 淺灰 `--placeholder`）；已結案**不**用 `--done`（避免與步驟綠底混淆）
- [ ] `cancelled` 時整條 stepper 換「已取消」pill，**不**塞進第 ④ 步當綠底
- [ ] `was_cancelled && submitted` 時下方出現再次核准警示 banner
