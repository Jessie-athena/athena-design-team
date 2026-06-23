# DataGrid 結構與互動

> 屬於 `profiles/erp-transaction.md` 的延伸元件規格。
> 載入時機：隨 ERP profile **自動載入**（無論作業檔 / 設定檔；由 `SKILL.md §支援檔案` 規定——該處為載入規則的**單一來源**）。
>
> 上層 profile：`profiles/erp-transaction.md`
> 同層元件：`ListSearch.md` / `DataGrid.md` / `FormGroup.md` / `FormFooter.md` / `SummaryCard.md` / `Stepper.md` / `Permissions.md` / `RelBanner.md` / `Skeleton.md`
>
> **三權威分工**：
> - **值權威＝`../../assets/app.css`（canonical CSS，產出時複製不重寫）**——所有尺寸 / 色 / 疊白實色的字面值以此為準；本檔**不複印 CSS 數值**（要改數值改 app.css）。
> - **本 profile＝用法權威**——負責 class 套用、凍結欄 / 行內編輯互動行為、markup 結構、app.js 邏輯。
> - **契約權威＝上游設計文件** `../../../design-system-architect/references/components/DataGrid.md`（格式見 `component-doc-schema.md`）：what/why/token-reference/state/a11y/跨平台 adaptive。
> 本檔保留 class 名與行為說明、查 app.css 區塊的導引；遇具體 px/hex 一律指 app.css，避免兩處漂移。

---

List View 的表格本體，也用於 Form View 內的明細列。依職責分為**兩種 grid**，請依情境選用：

| 類型 | Class | 用途 | 特徵 |
|---|---|---|---|
| **顯示型 Grid** | `.dg` | 唯讀列表、明細檢視、可選取 | 斑馬列、可選取、左右凍結欄 |
| **行內編輯 Grid** | `.dg-lines` | 產品明細／採購明細編輯 | 行內輸入、序號凍結左、操作凍結右 |

兩者皆是其所在容器**唯一的橫向／縱向捲軸來源**（捲軸落在 `.dg__scroll` / `.dg-lines__scroll`，外層鎖高）。

## 命名收斂（legacy → 現行）

DS 對齊命名以 `.dg` / `.dg-lines` 為**權威**。早期財務範本（`module-page.html`）使用的舊別名一律對應如下，新範本（含 `psi-transaction-page.html`）**只用右欄**：

| Legacy（已淘汰別名） | 現行（權威） |
|---|---|
| `.data-grid-block` / `.grid-wrap` | `.dg` / `.dg__scroll` |
| `table.grid` | `.dg table` |
| `.sticky-left`（第 1 欄） | `.sticky-l`（`left: 0`） |
| `.sticky-left`（第 2 欄，`left: 50px`） | `.sticky-l-2`（`left: 50px`） |
| `.sticky-right` | `.sticky-r` |

> 維護既有財務範本時舊別名仍可運作，但**不要**在新檔混用兩套；審查時若見 `.data-grid-block` 於新範本，回報為待收斂。

---

## 一、共同基礎（Foundations）

兩種 grid 共用的尺寸、框線與表頭規則。

> 尺寸 / 框線 / 表頭實色的字面值見 app.css `.dg`（表頭列高 / 資料列高 / cell padding / 字級）；本節僅記設計**規則**。

### 尺寸與邊框（規則）

- 表頭列、資料列、cell 水平內距、字級：值見 app.css；cell 文字垂直置中
- Grid **無圓角、無外框**，只在頂端帶一條分隔線（`border-top`）
- 列間 `border-bottom` 分隔；**最後一列不畫底線**（避免與外層下緣疊出兩條）
- 儲存格內容 `white-space: nowrap`（不換行，超出以水平捲動處理）

### 表頭（`th`）規則

- 背景用**疊白後實心色**——sticky 表頭必須實色，避免捲動時下層內容透出（色值見 app.css `.dg thead th`）
- 字重 medium、`text-align: left`（**含「狀態」欄**）
- `position: sticky; top: 0`（捲動時固定）
- 欄與欄以**垂直分隔線**區隔（`th:not(:last-child)::after`；尺寸 / 色見 app.css）；最後一欄不畫
- **無下邊框**

---

## 二、顯示型 DataGrid（`.dg`）

### DOM Anatomy

```
.dg                    ← flex column 容器（V-grid 模式佔滿剩餘高度）；border-top
  └─ .dg__scroll       ← overflow: auto；唯一的捲軸來源（X/Y）；自身 border: 0
      └─ table         ← border-collapse: separate; min-width: 880px
  └─ .pager            ← 與 grid 相連，無上下額外邊框
```

### RWD 與寬度規則

- `table` → `width: max-content; min-width: 100%`
  - 容器寬時：表格 = 容器 100%，欄位依 `min-width` 平均分配剩餘空間
  - 容器窄時：表格寬度 = 各欄 `min-width` 總和，`.dg__scroll` 顯示水平捲軸
- **不使用** `table-layout: fixed`（讓欄位依內容自然撐開）
- 一般欄位 HTML **不要寫死** `style="width:..."`，由 CSS 統一控制

### 欄位寬度（class 對應；px 值見 app.css）

| 欄位類型 | class | 規則 |
|---|---|---|
| 一般資料欄（`th` / `td`） | — | 有 min-width、width auto |
| Checkbox 欄 | `.col-check` | 固定寬、置中，**禁**自適應 |
| 操作欄（1 顆按鈕） | `.col-actions.col-actions--single` | 固定窄寬 |
| 操作欄（2 顆按鈕） | `.col-actions`（預設） | 固定寬 |

> **操作欄寬度鎖定**：用 `width / min-width / max-width + box-sizing: border-box` 三件套鎖死，避免 grid auto-fit 在窄視窗下擠壓按鈕（值見 app.css `.col-actions`）。

### 欄位優先級與 RWD 顯示規則

> 採「優先級欄位 + 黏性欄位 + 橫向捲動」三層策略。重要欄位（勾選、代號、操作）永遠可見；次要欄位於窄視窗下隱藏；對應 §RWD 4 斷點 XL / L / M / S。

| 優先級 | 規則 | 典型欄位 |
|---|---|---|
| **P0** 必要 | 任何斷點皆顯示 | 勾選欄（`.sticky-l`）、主鍵 / 代號（`.sticky-l-2`）、名稱、狀態、操作欄（`.sticky-r`） |
| **P1** 重要 | M 橫向捲動可見；S 隱藏 | 主要業務欄位（如「應收科目」「對應科目」） |
| **P2** 次要 | M 橫向捲動可見；S 隱藏 | 補充業務欄位（如「銷貨價格表」「預設區域」） |
| **P3** 輔助 | L 橫向捲動可見；M / S 隱藏 | 多公司 / 跨組織欄位（如「公司別」） |

實作要點：

1. 低優先級欄位以 `data-prio="p1|p2|p3"` 標記，透過 `@media` 設定 `display: none`
2. 黏性欄位（勾選 / 代號 / 操作）採 `position: sticky`，背景色**必須不透明**（見 §列狀態與 Hover），避免捲動時穿透
3. 欄位最小寬度遵守上表；超出時 `text-overflow: ellipsis` 截斷並提供 tooltip
4. 「設定」按鈕（toolbar 右側 `tune` icon）提供「自訂顯示欄位」開關，可覆寫斷點預設值並持久化至 `localStorage`

> 各模組的具體欄位 → 優先級對照，由該模組 prototype 製作時依本表分類，並於 chat handoff 附上「P0–P3 欄位分配」清單供 reviewer 對照。

### Sticky 凍結欄

- **左凍結**（`position: sticky`）：第 1 欄 checkbox `.sticky-l`（`left: 0`）；第 2 欄主鍵 `.sticky-l-2`（緊接 checkbox 寬，offset 見 app.css）
- **右凍結**：`.sticky-r`（`right: 0`，操作欄）
- z-index 階層：`thead th.sticky-*` 最高、`tbody td.sticky-*` 最低、一般 `thead th` 居中（值見 app.css）
- **依設計指示：凍結欄不使用深度陰影提示**（`box-shadow` 強調凍結邊會視覺破碎）

### 列狀態與 Hover（核心）

互動層次由淺到深，皆以品牌藍為基底逐級加深：**斑馬 → Hover → 選取 → 選取+Hover**（各態實際色值見 app.css `.dg` 列狀態區塊）。每態分兩套：

| 狀態 | 普通 cell（非 sticky） | Sticky cell |
|---|---|---|
| 一般列（奇數） | 白 | 白（實色） |
| 斑馬列（偶數） | 半透明疊層 | **疊白後實色** |
| Hover | primary 半透明疊層 | **疊白後實色** |
| 選取 `.is-selected` | primary 半透明疊層（較深） | **疊白後實色** |
| 選取 + Hover | primary 半透明疊層（最深） | **疊白後實色** |

> **為什麼 sticky 必須補實色？** Sticky cell 浮在下方滾動內容之上；若用 `rgba()` 透明色，scroll 時下層 cell 會穿透顯現，破壞 hover/selected 的視覺反饋。每個互動狀態都必須有對應的「疊白後固體色」配套。
> **禁**改用不同色相讓 sticky 欄看起來是獨立區塊；色相要與一般 cell 一致，只是 alpha 換實色。表頭凍結沿用表頭實心色。（疊白實色字面值全在 app.css。）

### 特殊列

- **群組列 `.group-row`**（preset 分群）：primary 淺底、字重 medium、藍字（值見 app.css）
- **焦點還原閃爍 `.row-flash`**：捲動位置還原時，目標列由藍底漸退至透明（動畫 / 色見 app.css）

### 特殊資料型態

- **主鍵 / 代碼欄** `.code-cell`：`font-family: var(--font-family-mono); font-feature-settings: 'tnum'; font-weight: 500`
- **可點擊連結** `.dg__link`：藍字 `var(--color-sf-primary)`、`font-weight: 500`；hover 加底線
  - **唯讀變體** `.link-cell--static`：外觀同 `.dg__link` 但 `cursor: default`、hover **不**加底線——用於「看起來像連結但當前無權限 / 無目標可跳」的情境（如唯讀角色）
  - **關聯標籤** `.dg__rtag`：連結文字後綴的灰字註記（如「（退料單）」，尺寸 / 色見 app.css）；標示該列是衍生 / 沖銷單
- **次要灰字** `.dg__muted` / `.text-secondary`（如電話）：`color: var(--text-secondary)`（電話另加 `font-family: var(--font-family-mono)`）
- **空值顯示**：一律 `—`（em dash），**禁**用「無」「N/A」
- **關聯欄位顯示**：只顯示**名稱**，**不顯示代碼前綴**（如「北區」而非「N · 北區」）

### 狀態 Chip `.st-chip`

- 表格內緊湊版（尺寸 / 字級見 app.css `.st-chip`）；獨立使用的 36px DS Chips 本體見 `Stepper.md §voided-banner`，色彩模式兩者共用
- 色彩模式統一：背景 = 狀態色 12% tint、邊框 + 文字 = 狀態色實色（每態實際色見 app.css）
- chip 在 cell 中**靠左**（隨欄位 `text-align: left`），不置中

**設定檔兩態**：`.st-chip--active`（success 綠）/ `.st-chip--inactive`（error 紅），三件套同上（色見 app.css）

**作業檔狀態（canonical 4 值 + 進銷存擴充；狀態定義詳 `Stepper.md` / `erp-transaction.md §State Machine`）**

| class | 標籤 | 狀態色 |
|---|---|---|
| `.st-chip--draft` | 草稿 | 中性灰：背景 `var(--bg-surface-variant)`、邊框 `var(--border-strong)`、文字 `var(--text-secondary)` |
| `.st-chip--waiting` | 等待前置作業 | `rgb(var(--color-sf-warning))` 橘（庫存單過場態；詳 `Stepper.md §庫存單 5 步`） |
| `.st-chip--submitted` | 已提交 | `rgb(var(--color-sf-primary))` 藍 |
| `.st-chip--assigned` | 就緒 | `rgb(var(--color-sf-warning))` 橘（庫存單過場態） |
| `.st-chip--approved` | 已核准 | `rgb(var(--color-sf-success))` 綠 |
| `.st-chip--partial` | 部分驗收 / 部分採購 | `rgb(var(--color-sf-primary))` 藍 |
| `.st-chip--received` | 已驗收 | `rgb(var(--color-sf-primary))` 藍 |
| `.st-chip--settled` | 已結清 | `rgb(var(--color-sf-primary))` 藍 |
| `.st-chip--done` | 已結案 | 中性灰（同 draft 模式） |
| `.st-chip--voided` | 已作廢 | `rgb(var(--color-sf-error))` 紅 |
| `.st-chip--cancelled` | 已取消 | `rgb(var(--color-sf-error))` 紅 |

> 各態色相為**語意色推導**（對齊 Stepper「綠=完成、藍=進行中/驗收終態、紅=作廢/取消、灰=未取號/已結案」），尚未逐態經 DS 正式定義；DS 補定義後以 DS 為準。各模組依其狀態機取用對應 class 即可。

### 操作按鈕 `.ico-btn`

- 方形按鈕、icon 置中、圓角、預設背景透明；Hover 加 primary 淺底（尺寸 / 色見 app.css `.ico-btn`）
- `.is-edit` / `.is-view`（唯讀模式切 chevron）共用同一規格

### 互動規則

| 互動 | 行為 |
|---|---|
| 表頭 checkbox | 部分選取 → `indeterminate`；全選 / 全不選切換 |
| 列 checkbox | 勾選後該列加 `.is-selected` |
| 列 hover | 整列背景，sticky 欄補實色（見 §列狀態與 Hover） |
| 列點擊 | **禁**整列可點（避免誤觸）。進詳細只有兩個入口：① 主鍵欄 `.dg__link`、② 操作欄按鈕 |
| 唯讀模式 | 操作欄按鈕從鉛筆（`.is-edit`）切換成右箭頭 chevron（`.is-view`），title 改「檢視」；**禁直接隱藏按鈕**，保留入口 |
| 橫向捲動 | 左 / 右凍結欄保持可見；sticky 欄一定要明確指定實色 `background` |
| 進入「批次模式」 | 由父層判斷 `selectedIds.length > 0` 切換 toolbar，與表格本身解耦 |

### 空狀態

無資料時隱藏整個 `.dg`，改顯示 `.empty-state`（大尺寸 `material-symbols:inbox-outline` icon + 標題 + 說明；尺寸見 app.css）；**禁**保留空表頭。

---

## 三、行內編輯 Grid（`.dg-lines`）

> 適用情境：產品明細／採購明細的列編輯，需保留同列／鄰列比對視角。複雜分支（如 formula 計算）仍走 Drawer，詳 §與 Drawer 模式取捨。

### 結構

```
.dg-lines
  .dg-lines__header   ← 標題列，primary 淺底，底部分隔線；右側 add 鈕（色見 app.css）
  .dg-lines__scroll   ← overflow-x: auto（唯一橫向捲軸）
    table             ← table-layout: auto
```

### 列狀態與 Hover（色值見 app.css `.dg-lines`）

| 狀態 | 背景 |
|---|---|
| 一般列 | 白 |
| 斑馬列（偶數） | 半透明疊層 |
| 編輯中 `.editing` | 淡白 flyout 疊層（帶上下分隔線標示編輯態） |

### 凍結欄

> 採購單規範 PRD §6.3.4：**僅序號凍結左、操作凍結右**。

- 序號 `.col-idx`：置中、`sticky left: 0`
- 商品 `.col-prod`：有 min-width、`sticky left:`（緊接序號寬，部分情境凍結）
- 操作 `.col-ops`：`sticky right: 0`
- 凍結欄各狀態實心底（表頭 / 偶數列 / 編輯列疊層）：色值見 app.css

### 行內輸入元件

`.input--inline` / `.select--inline`：

- **底線式輸入**（`border-bottom`，圓角上圓下平）；尺寸 / 色見 app.css
- `.select--inline` 右側留空給箭頭；placeholder 用 `.select-placeholder`
- `:focus` → 底線轉 focus 色；`.is-error` → 紅底線 + helper（見 §驗證）

### 採購單專屬

- `.col-center`：序號／贈品欄置中
- `.gift-yes`：贈品**文字式**標記（採購明細「是否贈品」欄；橘字，色見 app.css）
- `.gift-tag`：贈品**徽章式**標記（緊貼商品名後），對齊基準 `庫存模組`（出庫 / 領料明細）——紫色 inline-flex 小徽章，與狀態語意色不衝突（完整樣式見 app.css `.gift-tag`）。文字式 `.gift-yes` 用於獨立「贈品」欄；徽章式 `.gift-tag` 用於商品名行內標註，**依版面擇一**
- 金額欄唯讀值靠右、`tabular-nums`；總金額套 `.is-amount-total`（加粗，見 app.css）

### 必填標記

- `th .required`：紅色星號標記（樣式見 app.css `.required`）

### 進入 / 離開編輯（互動行為）

#### 進入編輯

| 觸發方式 | 行為 |
|---|---|
| 點該列右側 ✏️「編輯」icon | 整列進入編輯態，所有可編輯欄變輸入控制元件，焦點落第一個可編輯欄 |
| 點表頭右側 `material-symbols:add-circle` | 在 `tbody` 末插入空白編輯列，焦點落第一個必填下拉 |
| 鍵盤 `Enter` 於選取列 | 等同點編輯 icon |
| 雙擊某儲存格（選用） | 進入該列編輯態，焦點落被雙擊的欄位 |

**互斥規則**：同一時間僅允許**一列**編輯。嘗試編輯第二列時——當前列「未變更」直接切換；「已變更」彈 `modal--warning`：「尚有未儲存變更，是否放棄？」

#### 欄位聯動（Field Cascade）

以「銷貨價格表」為示意；模組實作時依語意調整：

| 主欄位 | 變更後副作用 |
|---|---|
| `適用範圍 → 全部產品` | `適用項目` 置灰、清空並顯示「全部產品」 |
| `適用範圍 → 產品類別 / 產品 / 變體` | `適用項目` 重新載入選項；原值不在新清單則清空並紅框提示 |
| `計算方式 → 固定價格` | 顯示「固定價格」欄、隱藏「折扣百分比」；前者必填 |
| `計算方式 → 折扣` | 顯示「折扣百分比」（`%`、`min=0`、`max=100`），隱藏「固定價格」 |
| `最小數量` | 0–9999 整數；非整數四捨五入；負值阻擋 |
| `有效期` | 起訖成對；僅填一邊離開欄自動填補（起 = `today`、迄 = `2099/12/31`）並 info toast |

#### 驗證

- **即時驗證**：`@blur` 時驗證；錯誤欄加 `.is-error`，下方 helper 顯紅字
- **儲存時驗證**：點「✓ 確認」整列重驗，第一個錯誤欄自動 focus 並 scroll 到視野
- 錯誤訊息採既有 `fieldErrors` 樣板（例：「適用項目」為必填、「固定價格」須 > 0、生效日不可晚於失效日）
- Helper 緊貼 cell 下方**不撐高列高**：`position: absolute`（不佔列高，定位 / 字級見 app.css）；被表格邊界裁切則翻轉往上

#### 確認與取消

| 動作 | 行為 |
|---|---|
| ✓ 或 `Enter`（非 textarea 焦點） | 通過驗證 → 寫回 `form.lines[idx]` → 收合 readonly → success toast |
| ✕ 或 `Esc` | 「新增中且未填」直接移除；「有變更」彈確認 modal；「無變更」直接收合 |
| `Tab` 於最後一欄 | 焦點循環回該列第一欄；不離開列（focus-trap within row） |
| 點列外空白 | 視同 `Esc` |

鍵盤：`Enter` 確認、`Esc` 取消、`Tab`/`Shift+Tab` 列內前後欄迴圈不外溢、`Ctrl/⌘ + Enter` 確認並續新增下一列。

#### 列內動作區

| 狀態 | 顯示 |
|---|---|
| Readonly | ✏️ edit（`.is-edit`）＋ 🗑 delete（`.is-delete`） |
| Editing | ✓ check 主色實心（`.is-confirm`）＋ ✕ close 灰邊框（`.is-cancel`）；小間距 gap |
| Loading（儲存中） | `progress_activity` 旋轉，主色 |

### RWD 與寬度

- 列內控制元件有最小寬（見 app.css）；`≥ 1280px` 正常顯示；`< 1280px` 自動橫向滾動
- 編輯列右側動作欄 `.col-ops` 維持 `sticky right: 0`，避免操作鈕被裁切

### 無障礙（A11y）

- 編輯列 `role="row"` + `aria-rowindex`，編輯態加 `aria-busy="true"`、`aria-label="編輯中：<名稱>"`
- 每個輸入控制元件保留 `<label>` 對應原欄頭，視覺以 `sr-only` 隱藏
- `Esc` / `Enter` 焦點不可逃出該列（focus-trap within row）
- 動畫尊重 `prefers-reduced-motion: reduce` → 全部改 `0ms`

---

## 四、唯讀／封存／鎖定檢視（read-only states）

當 form 進入 `.is-archived-view` / `.is-locked-view` / `.is-readonly-view`：

- `.dg-lines` 整體 `opacity: .85`
- table 內容 `pointer-events: none`（停用互動），但 `.dg-lines__scroll` **保留可捲動**（水平捲軸仍可用）
- 隱藏操作欄 `.col-ops`、標題列的 icon 按鈕
- 隱藏倒數第二欄（單位）右側分隔線，使表格右緣齊平

> 與權限正交：唯讀檢視是「整檔狀態」造成；個別列的「結轉後鎖列」見 §進銷存明細擴充。

---

## 五、Checkbox（`.cb`）

- 方形、細邊框、圓角（尺寸見 app.css `.cb`）
- Hover：邊框轉藍，並顯圓形 halo（`::before`，primary 淺底）
- Focus：primary 淺色 focus ring
- 勾選 / 半選：填入品牌藍

---

## 六、動畫規範（Motion）

> 動畫場景一覽（時間 / 緩動 / 位移值見 app.css 對應 transition / keyframes）：

| 場景 | 動畫 |
|---|---|
| 色彩／背景轉場 | ease-out 過場 |
| 進入編輯態 | 列高展開、底色 fade-in |
| 離開編輯態 | 反向 |
| 確認成功 | 列底色閃綠後漸退 |
| 驗證錯誤 | 錯誤欄 shake |
| 焦點還原列閃爍 | `.row-flash` |
| Checkbox 狀態 | 短過場 |

> 一律遵守 `prefers-reduced-motion`（無彈跳、無視差；reduce 時改 `0ms`）。

---

## 七、元件層級互動規則（行為層，來自 `DataGrid.vue`）

供搭配 production 實作參考（行為層，非樣式層）：

- **選取**：`selectionSettings.type` 為 `Single` / `Multiple`；`checkboxOnly: true` 時僅能由 checkbox 選取
- **操作欄**：`actions: ['add','edit','delete']`；`add` 會在操作欄表頭顯示新增鈕，點擊進入行內編輯
- **新增攔截** `beforeAdd`：回傳 `false` 取消、回傳物件作為預設值（如 `{ status: 'draft' }`）
- **排序／分頁／篩選**：`allowSorting`、`allowPaging`、`allowFiltering`（預設 Excel filter）
- **行拖拉** `allowRowDragAndDrop`：Syncfusion 自動更新 `dataSource`，**勿手動 `splice`**
- **Dropdown 欄位**：非編輯態自動將 `value(ID)` 轉為 text 顯示，底層仍存原始 `value`
- **驗證**：透過 `useGridValidation` 整合 Zod／Syncfusion 規則，行內編輯即時校驗

---

## 與 Drawer 模式取捨

| 維度 | 行內編輯（`.dg-lines`） | Drawer 編輯 |
|---|---|---|
| 適用情境 | 欄位少（≤ 6）、修改快速、批量微調 | 欄位多、含進階參數（formula、捨入等） |
| 上下文 | 保留同列 ／ 鄰列比對視角 | 全屏專注，隱藏列上下文 |
| 操作步數 | 1 步入編輯、`Enter` 存檔 | 2 步（開抽屜 → 按鈕） |
| 建議 | 模組欄位 ≤ 5 且分支簡單時，可取代 80% Drawer 開啟 | 保留「更多操作」按鈕，作為複雜分支的進階入口 |

**落地策略**：先行內編輯；當該列觸發複雜分支（如 `compute_price === 'formula'`）時於該列顯示「📋 進階」icon，自動降級回 Drawer，避免欄位過多塞爆列高。

> **例外——交易明細寬表**：採購／銷貨明細天生欄位多（> 6），仍維持 inline（水平捲動 + `.col-ops` sticky-right），**不**降級 Drawer。詳下節。

---

## 進銷存明細 DataGrid（交易明細擴充）

> 適用進銷存作業檔（如請購單 / 採購單 / 銷貨單）Form View 內的明細 grid。搭配 `templates/psi-transaction-page.html`，載體為 `.dg-lines`。

### 與設定檔式行內編輯的差異

| 維度 | 設定檔式快速編輯 | 交易明細（本節） |
|---|---|---|
| 欄位數 | ≤ 6 才建議 inline；多則走 Drawer | **可超過 6 欄仍維持 inline**；水平捲動 + `.col-ops` sticky-right，**不**降級 Drawer |
| 新增列入口 | 「＋ 新增規則」文字列 | 表頭右側 `material-symbols:add-circle`（實心）icon（`ico-btn is-add-circle`）；**flat primary** 樣式（icon primary 色、無底色、無邊框） |

### Computed / related 欄位（唯讀，不可編）

| 欄位 | 規則 | 顯示 |
|---|---|---|
| 商品編號 / 商品名稱 | related，選商品後自動帶入 | `dim` 淡化色，readonly |
| 採購單位 / 上次進價 / 庫存 | 選商品 onchange 帶入（單位可改、其餘唯讀） | 唯讀欄 `dim` |
| **預估小計 / 金額** | computed = `數量 × 單價`（即時重算） | readonly，`num` 右對齊；**不**做成可編輯欄 |

- 即時算式：數量或單價變動時 `金額` 立即更新（範本以 `{{ (qty || 0) * (price || 0) }}` 行內示意；production 由 computed 欄落地）
- 數值欄一律 `class="num"` 右對齊；千分位顯示；空值 `—`；編輯態 input 也加 `.num`

### 結轉後鎖列（carry-forward lock）

明細結轉下游單據後，該列不可再改（避免來源與下游不一致）：

- 判斷依據：下游關聯鍵存在（如 `po_line_id != null`）
- 視覺：列加 `is-row-locked`；`.col-ops` 的 `edit` / `delete` **改為單一 `material-symbols:lock-outline` icon**（`dim`，tooltip「已結轉，不可修改」）
- 「採購單號」等 related 欄：結轉後回填單號；尚未結轉顯示 `—`（`dim`）
- 與權限正交：即使有 `canEditLines`，已鎖列仍不可改

### 合計列（tfoot）

- 只 Σ 金額 / 預估小計欄，其餘欄空白；`form.lines.length > 0` 才渲染
- 該 Σ 同步為 Summary Card 的單指標（詳 `SummaryCard.md` Layout B「預估總金額」）；兩處數字必一致；總金額套 `.is-amount-total`（字重 700）
