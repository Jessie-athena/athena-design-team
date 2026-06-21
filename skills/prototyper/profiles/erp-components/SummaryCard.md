# Summary Card 結構與互動

> 屬於 `profiles/erp-transaction.md` 的延伸元件規格。
> 載入時機：**作業檔**（transaction documents）必載；設定檔無 Summary Card（`erp-setup.md` 已明定），免載（由 `SKILL.md §支援檔案` 規定——該處為載入規則的**單一來源**）。
>
> 上層 profile：`profiles/erp-transaction.md`
> 同層元件：`ListSearch.md` / `DataGrid.md` / `FormGroup.md` / `FormFooter.md` / `SummaryCard.md` / `Stepper.md`
>
> **上游設計文件**：`../../../design-system-architect/references/components/SummaryCard.md`（格式見 `component-doc-schema.md`；複合元件，Full 層）。分工——**設計文件**是 what/why/token/state/a11y 的權威；**本 profile** 是「如何用單檔 HTML/CSS class 落地」的實作層。本檔的 layout / token 決策待收編進設計文件後改為引用，避免兩處漂移。

---

作業檔 Form View 頂部的摘要卡。依模組需求有**兩種 layout**，共用「sticky 置頂、無 shadow、內含狀態 stepper」三大原則。

## 兩種 Layout 對照

| Layout | 適用 | 結構 | stepper |
|---|---|---|---|
| **A — 多指標（上下兩塊）** | 財務作業檔（如付款 / 請款；金額需拆稅額 / 互抵等多指標） | 上區：標題 + stepper；下區：多個指標（總金額 / 幣別 / 建立者）+「已產生傳票」chip | canonical 3 步（詳 `Stepper.md`） |
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

> canonical 3 步 stepper（財務 4 值狀態機）與結構 / 判定邏輯 / 樣式 token 見 `Stepper.md`；本節是其延伸，**沿用三狀態（pending / current / done）的 bubble 與連接線視覺**，僅多一個動態插槽步。驗收模型（七值）的第 ④ 步插槽見 `Stepper.md §七狀態總表`。

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
| `partial`（部分採購） | 部分採購 | `stepper__step--partial` | **primary 藍**填充 + 內白環 | 進行中（當前步） |
| `done`（已結案） | 已結案 | `stepper__step--final` | **primary 藍**填充 + 內白環 | 終態（2026-06-21 起與當前步同色；靠 label 區分，不再用灰） |
| `draft` / `submitted` / `approved`（前三步階段） | 「待結轉」或「—」 | `stepper__step--placeholder` | 圓底**淺灰** placeholder | 尚未達到 |

> `cancelled`（已取消）**不進 stepper**：整條 stepper 改顯示 `.voided-banner` 內一顆 `st-chip st-chip--cancelled`「已取消」pill（紅色），與 canonical `voided` 同理（徽章視覺詳 `Stepper.md §voided-banner`）。

### 狀態 × step 對應矩陣

| `form.state` | ① 草稿 | line(②) | ② 已提交 | line(③) | ③ 已核准 | line(④) | ④ 插槽 |
|---|---|---|---|---|---|---|---|
| `draft` | current | （灰） | pending | （灰） | pending | （灰） | placeholder |
| `submitted` | done ✓ | is-current | current | （灰） | pending | （灰） | placeholder |
| `approved` | done ✓ | is-done | done ✓ | is-current | current | （灰） | placeholder |
| `partial` | done ✓ | is-done | done ✓ | is-done | done ✓ | is-current | **partial（primary 藍，當前）** |
| `done` | done ✓ | is-done | done ✓ | is-done | done ✓ | is-done | **final（primary 藍，終態；class `--final`，色同當前步）** |
| `cancelled` | ⛔ 整個 stepper 隱藏，改顯示 `.voided-banner` →「已取消」pill | — | — | — | — | — | — |

> 連接線索引 = 其**右側** step 的序號（`lineClass(2)` 是 ①→② 之間那條）；`is-current` 藍線永遠**連向**當前步，非從當前步出發。

> `was_cancelled = true` 且回到 `submitted` 時：stepper 維持四步（當前 active 落在第 ②），第 ④ 步仍 placeholder；另於 Summary Card 下方插入「再次核准警示」`form-banner.is-warning`（詳 `erp-transaction.md §進銷存擴充狀態機`）。

### JS helper 約定

`app.js` 提供下列 helper（命名沿用實際設計）：

| helper | 回傳 | 說明 |
|---|---|---|
| `stepState(n)` | `'done'` / `'current'` / `'pending'` | 第 n 步（1–3）的三狀態；對照上表 |
| `stepClass(n)` | step modifier class（`stepper__step--done` / `--current`） | 由 `stepState(n)` 映射（pending 無 modifier） |
| `lineClass(n)` | `'is-done'` / `'is-current'` / `''` | 第 n 步**左側**連接線 `.stepper__line`：n < 當前 → `is-done`（綠）、n = 當前 → `is-current`（藍，正連向當前步）、之後 → 灰 |
| `step4Class` | `stepper__step--partial` / `--final` / `--placeholder` | 第 ④ 步插槽 class，依 `form.state`（`--partial` / `--final` 皆 primary 藍、僅 label 異；`--placeholder` 灰；**皆非** `--done` 綠） |
| `step4Label` | `'部分採購'` / `'已結案'` / `'待結轉'` | 第 ④ 步文字 |
| `isCancelled` | boolean | `form.state === 'cancelled'`，true 時整條 stepper 換 pill |

### 容器與視覺 token

圓圈 `.stepper__bubble`（32×32, radius full）、連接線 `.stepper__line`（高 2px / flex 28–56px）的 pending / current / done 三狀態 token 與容器規則，沿用 **`Stepper.md §樣式規則`**。第 ④ 步三 modifier 配色（2026-06-21 統一）：`--partial`（進行中）與 `--final`（終態）**皆 primary 藍 + 內白環**（= 當前步視覺，靠 label 區分）、`--placeholder` 淺灰（待定）；驗收模型亦同（一律藍），詳 `Stepper.md`。

---

## 自檢

- [ ] Summary Card `sticky` 置頂、無 shadow
- [ ] Layout B：左單指標（放大 / 加粗 / 主色）+ 右 stepper
- [ ] 動態 stepper（最少 3 步、上不封頂）前段固定、終態步依 `form.state` 三選一（`--partial` / `--final` 皆 primary 藍、`--placeholder` 灰）；終態步**不**用 `--done` 綠（避免與已通過中間步混淆）
- [ ] `cancelled` 時整條 stepper 換「已取消」pill，**不**塞進第 ④ 步當綠底
- [ ] `was_cancelled && submitted` 時下方出現再次核准警示 banner
