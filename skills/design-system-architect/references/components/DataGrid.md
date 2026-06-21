---
name: DataGrid（資料表格 / Grid）
category: 資料展示與表格
tier: full           # 承載資料列、多層 states、跨斷點欄位優先級、行內編輯生命週期 → Full
status: ✅ 已採用（Syncfusion Grid；ERP 落地以 .dg / .dg-lines class）
authority: prototyper DataGrid.md（class / 尺寸 / 互動色 / 凍結欄 / 行內編輯落地權威）；行為層 DataGrid.vue
figma-node: —        # 🎨 Figma 補入時填 <FILE_KEY>/<NODE_ID>
version: v0.1
last-synced: —
---

> 依 `../component-doc-schema.md`（Full 層）產出。**class / 尺寸 / 互動色 / 凍結欄 / 行內編輯的落地權威是 `prototyper/profiles/erp-components/DataGrid.md`**（見 §13）；本檔做 what/why/token/state/a11y 整理與 token-reference，重疊處引用、不重寫決策（schema §8）。
> ⚠️ **已知 token 缺口**：表格的斑馬 / hover / selected 多層互動色多為「品牌色透明疊白後的實心色」或非標準 alpha（如 `.06` / `.10`），`athena-tokens.md` 無單一對應 token。依 schema §2.2 **不臆造**，這些值以 prototyper profile 為落地權威，本檔僅標語意與缺口（見 §5）。

## 1. 概述　📋

資料密集列表的主要載體：List View 表格本體，也用於 Form View 內的明細列。依職責分**兩型**，依情境選用：

- **顯示型 Grid（`.dg`）**：唯讀列表、明細檢視、可選取；斑馬列、可選取、左右凍結欄。
- **行內編輯 Grid（`.dg-lines`）**：產品 / 採購明細編輯；行內輸入、序號凍結左、操作凍結右。

何時**不用**：欄位多且含進階參數（formula、捨入）的單列編輯走 Drawer（取捨見 profile §與 Drawer 模式取捨）；單純鍵值對展示用 description list，非表格。

## 2. Anatomy　🎨

```
.dg                    ← flex column 容器（V-grid 佔滿剩餘高度）；border-top
  ├─ .dg__scroll       ← overflow:auto；容器唯一的 X/Y 捲軸來源
  │   └─ table         ← border-collapse: separate; min-width: 880px
  └─ .pager            ← 與 grid 相連，無額外上下邊框
```

子元素：表頭 `th` / 資料列 `tr > td` / 凍結欄（`.sticky-l` `.sticky-l-2` `.sticky-r`）/ 選取欄 `.col-check` / 操作欄 `.col-actions` / 狀態欄（`.st-chip`，見 `st-chip.md`）。行內編輯型另含 `.dg-lines__header`（標題列 + 新增鈕）與列內輸入控制元件 `.input--inline` / `.select--inline`。

> 完整 DOM 與 class 命名（含 legacy → 現行收斂表）以 profile 為權威，勿在此重列。

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# data-dense 元件：以 Default（緊湊）為主；表單內明細可採 Comfortable padding
header:
  height:    45px                              # 🎨 量測值，待對齊 token
  bg:        "狀態色：primary 5% 疊白實心色 ≈ #F4F8FE"   # ⚠️ 無單一 token（sticky 表頭須實色，禁 rgba）；落地見 profile §表頭
  font:      "{font-size-sf-text-md} / {font-weight-sf-medium}"  # 🔗 14px / 500
  divider:   "{ds-borderwidth-small} {color-sf-outline}"         # 🔗 1px 欄間垂直分隔（高 26px）
row:
  height:    50px                              # 🎨 顯示型；行內編輯態 40→56px（見 §6）
  cell-pad:  "{ds-space-padding-extra-large}"  # 🔗 水平 16px（padding: 0 16px）
  font:      "{font-size-sf-text-md}"          # 🔗 14px
  fg:        "{color-sf-on-surface}"           # 🔗 主要文字 rgb(15 23 42)
border:
  outer:     none                              # 無圓角、無外框
  top:       "{ds-borderwidth-small} {color-sf-outline-variant}"   # 🔗 1px 頂端分隔線
  row:       "{ds-borderwidth-small} {color-sf-outline-variant}"   # 🔗 列間 border-bottom；最後一列不畫
special-cell:
  code:      "font-mono + tnum + {font-weight-sf-medium}"   # 主鍵 / 代碼欄
  link:      "{color-sf-primary} / {font-weight-sf-medium}"  # 🔗 可點連結 .dg__link；hover 底線
  muted:     "{color-sf-on-surface-variant}"   # 🔗 次要灰字（電話加 font-mono）
  empty:     "—（em dash）"                     # 禁「無」/「N/A」
```

> 表頭實色 `#F4F8FE`＝primary 5% 疊白後實心色：sticky 表頭浮在滾動內容上，若用 `rgba()` 會透出下層。此「疊白後實色」無單一 token，落地以 profile 值為準。

## 4. Types / Variants　🎨🔗

兩型共用 §3 基礎尺寸與表頭規則，差異在互動模型與凍結策略：

```yaml
display_grid:        # .dg 顯示型
  scroll:   "table width:max-content; min-width:100%（容器寬→平均分配；窄→各欄 min-width 總和 + 橫捲）"
  freeze:   "左：.sticky-l(left:0 勾選) / .sticky-l-2(left:50px 主鍵)；右：.sticky-r(操作)"
  col-width:
    data:    "min-width 200px / width auto"
    check:   "50px 固定置中（禁自適應）"
    actions: "單鈕 56px / 雙鈕 96px（width+min+max 三件套鎖死）"
inline_edit_grid:    # .dg-lines 行內編輯型
  scroll:   "table-layout:auto；.dg-lines__scroll 唯一橫捲"
  freeze:   "序號 .col-idx(60px, left:0) / 操作 .col-ops(56px, right:0)；商品欄部分情境凍結 left:60px"
  input:    "底線式 .input--inline / .select--inline，高 40px、border-bottom 1px {color-sf-outline}"
```

> **凍結欄不使用深度陰影**提示凍結邊（box-shadow 會視覺破碎）；以實色背景區隔即可。z-index：表頭 sticky=3、表身 sticky=1、一般表頭=2。

## 5. States　🎨🔗

資料展示型元件，**empty / loading / error 為一等公民**（schema §5 要求），互動列另有多層 states。

### 列互動多層（淺→深，品牌藍為基底逐級加深）

| 狀態 | 普通 cell（非 sticky） | Sticky cell（**須實色**） | token 對映 |
|---|---|---|---|
| 一般列（奇數） | `{color-sf-surface}` | `#ffffff` | 🔗 white |
| 斑馬列（偶數） | `{color-sf-on-surface-opacity4}` | `#F5F6F8`（疊白實色） | 🔗 普通 cell 對得上；sticky 實色⚠️無 token |
| Hover | primary 6% | `#F2F7FE` | ⚠️ `.06` 非標準 alpha，無 token（介於 `-5`/`-8`） |
| 選取 `.is-selected` | primary 10% | `#E6F1FD` | ⚠️ `.10` 無 token（有 `-8`/`-11`/`-12`） |
| 選取 + Hover | `{color-sf-primary-opacity-14}` | `#E1ECFC` | 🔗 普通 cell `.14` 對得上；sticky 實色⚠️無 token |

> **為什麼 sticky 必須補實色**：sticky cell 浮在滾動內容上，rgba 透明色會讓下層穿透、破壞 hover/selected 反饋。每個互動態都需「疊白後固體色」配套；色相須與一般 cell 一致（只是 alpha 換實色），**禁**用不同色相讓凍結欄像獨立區塊。
> **缺口處置**：上表 ⚠️ 標記項以 prototyper profile §列狀態與 Hover 為落地權威；要正式收進 token 需 DS 補定「互動疊層階梯（5/6/8/10/12/14%）」與「對應疊白實色」兩組，目前不臆造。

### 資料生命週期（必含）

| 狀態 | 呈現 | 規則 |
|---|---|---|
| empty | 隱藏整個 `.dg`，改 `.empty-state`（48px `inbox-outline` icon + 標題 + 說明） | **禁**保留空表頭 |
| loading | Skeleton 列佔位（非 spinner，對齊 Carbon「skeleton 非 spinner」） | 保留欄結構避免跳版 |
| error | 區塊級錯誤訊息 + 重試入口 | 載入失敗時取代列區，不靜默空白 |

### 行內編輯態（`.dg-lines`）

`default → editing（.editing 淡白 flyout 疊層 + 上下分隔線）→ confirm/cancel`；同一時間僅一列可編輯。詳互動見 §6。

### 特殊列

- 群組列 `.group-row`：primary 6% 底、字重 500、藍字 `{color-sf-primary}`、列高 38px。
- 焦點還原閃爍 `.row-flash`：捲動位置還原時，目標列由 primary 22% 漸退至透明（`1.2s`）。

## 6. Behavior　📋

互動規則（落地細節以 profile 為權威，本表為 what/why）：

| 互動 | 行為 |
|---|---|
| 表頭 checkbox | 部分選取→`indeterminate`；全選 / 全不選切換 |
| 列 checkbox | 勾選後該列加 `.is-selected` |
| 列點擊 | **禁整列可點**（避免誤觸）；進詳細只有兩入口：主鍵欄 `.dg__link`、操作欄按鈕 |
| 唯讀模式 | 操作鈕鉛筆（`.is-edit`）→ 右箭頭 chevron（`.is-view`），**禁直接隱藏**、保留入口 |
| 批次模式 | 由父層判斷 `selectedIds.length > 0` 切換 toolbar，與表格解耦 |
| 行內編輯·進入 | 點 ✏️ / 表頭 ＋ / 選取列 Enter / 雙擊 cell；**互斥**：嘗試編輯第二列時，未變更直接切換、已變更彈確認 |
| 行內編輯·欄位聯動 | 主欄位變更觸發副作用（適用範圍→適用項目、計算方式→固定價/折扣…），依模組語意 |
| 行內編輯·驗證 | `@blur` 即時驗證 + 儲存時整列重驗，第一個錯誤欄 focus；helper 絕對定位不撐列高 |
| 行內編輯·確認/取消 | ✓/Enter 寫回收合 + success toast；✕/Esc 新增未填直接移除、有變更彈確認；`Tab` 列內迴圈不外溢 |
| 結轉後鎖列 | 下游關聯鍵存在（如 `po_line_id≠null`）時列加 `is-row-locked`，操作改單一 `lock-outline`；與權限正交 |

> Syncfusion 行為層（`selectionSettings` / `actions` / `beforeAdd` / `allowSorting·Paging·Filtering` / `allowRowDragAndDrop` / `useGridValidation`）見 profile §七。

## 7. RWD / 欄位優先級　📋

採「優先級欄位 + 黏性欄位 + 橫向捲動」三層策略；對應 4 斷點 XL / L / M / S：

| 優先級 | 規則 | 典型欄位 |
|---|---|---|
| **P0** 必要 | 任何斷點皆顯示 | 勾選、主鍵 / 代號、名稱、狀態、操作 |
| **P1** 重要 | M 橫捲可見；S 隱藏 | 主要業務欄位 |
| **P2** 次要 | M 橫捲可見；S 隱藏 | 補充業務欄位 |
| **P3** 輔助 | L 橫捲可見；M / S 隱藏 | 多公司 / 跨組織欄位 |

低優先級欄位以 `data-prio="p1|p2|p3"` + `@media display:none` 實作；超寬欄位 `text-overflow:ellipsis` + tooltip；「自訂顯示欄位」開關可覆寫斷點預設並持久化 `localStorage`。各模組需於 handoff 附「P0–P3 欄位分配」清單供 reviewer 對照。

## 8. Keyboard　📋

- 列選取：Space 切換 checkbox；行內編輯 Enter 進入 / 確認、Esc 取消、`Tab` / `Shift+Tab` 列內前後欄**迴圈不外溢**（focus-trap within row）、`Ctrl/⌘+Enter` 確認並續新增下一列。
- 排序欄表頭可 focus，Enter 切換排序。
- 焦點還原：捲動位置還原後目標列 `.row-flash` 提示。

## 9. a11y　📋

- 編輯列 `role="row"` + `aria-rowindex`；編輯態 `aria-busy="true"` + `aria-label="編輯中：<名稱>"`。
- 每個行內輸入控制元件保留 `<label>`（視覺 `sr-only`）對應原欄頭。
- 狀態欄不可僅靠顏色——`.st-chip` label 文字即狀態名（見 `st-chip.md §9`）。
- 凍結欄對比：實色背景需維持文字對比 ≥ 4.5:1。
- 動畫尊重 `prefers-reduced-motion: reduce` → 全部改 `0ms`。
- Checkbox `.cb` focus：`{color-sf-primary-opacity-16}` 3px ring；hover halo `{color-sf-primary-opacity-8}`；邊框 `{color-sf-outline}`、radius `{ds-radius-small}`。

## 10. 跨平台 Adaptive　📋

App ≠ 縮小的 Web。資料表在窄螢幕的替代佈局：

- **桌面（XL/L）**：完整表格 + 凍結欄 + 橫捲；P0–P3 依斷點顯隱。
- **平板（M）**：保留表格但收斂至 P0–P1；操作欄維持 sticky-right。
- **手機（S）**：表格降級為**卡片列表（pop-in）**——每列轉一張卡，P0 欄位為卡片主資訊、其餘轉「label：value」堆疊；批次選取改卡片左側 checkbox。避免在手機強塞橫向捲動寬表。

> 具體斷點像素與卡片化落地由各模組 prototype 製作，遵循上表優先級分配。

## 11. Content　📋

- 空值一律 `—`（em dash），**禁**「無」/「N/A」。
- 關聯欄位只顯示**名稱**，不顯示代碼前綴（「北區」非「N · 北區」）。
- empty state 文案：說明為何空 + 下一步行動（如「尚無資料，點『新增』建立第一筆」）。
- 批次操作鈕用 verb+noun（「刪除選取」「匯出選取」）。一般 microcopy 詳 `ux-writer`。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| type | display / inline-edit | display | 對映 §4 兩型 |
| selectionType | single / multiple / none | none | `checkboxOnly` 時僅 checkbox 可選 |
| columns | Column[] | — | 含 `prio: p0–p3`、`freeze: left/right`、`width` |
| actions | ('add'\|'edit'\|'delete')[] | — | 操作欄按鈕集 |
| allowSorting / allowPaging / allowFiltering | boolean | false | Syncfusion Grid 能力 |
| density | default / comfortable | default | 對映雙密度 padding |

> production 行為以 `DataGrid.vue` / Syncfusion Grid API 為準（profile §七）；本表為設計指引層，與 API 分離（Ant 式）。

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §Surface / §On-* / §Outline / §Space / §Radius / §Typography
- 語意對照：`../athena-design.md` §中性·背景·文字·邊框 / §間距（雙密度）
- 子元件：`st-chip.md`（狀態欄徽章，色 / class 落地引用 profile）
- **落地權威（class / 尺寸 / 互動色 / 凍結欄 / 行內編輯，勿在此重寫）**：`prototyper/profiles/erp-components/DataGrid.md`
- 上層 profile：`prototyper/profiles/erp-transaction.md`；同層 `SummaryCard.md` / `Stepper.md` / `FormGroup.md` / `FormFooter.md` / `ListSearch.md`
- Code：`@syncfusion/ej2-vue-grids`、行為層 `DataGrid.vue`、驗證 `useGridValidation`

---

## 待 DS 正式定義（缺口彙整）

> 依 schema §2.2，以下對不上既有 token，**未臆造**；列此供 DS owner 補定後回填，並把 §5 ⚠️ 項改為 token-reference。

1. **互動疊層階梯**：斑馬 4% / hover 6% / selected 10% / selected+hover 14%——`athena-tokens.md` 僅有 primary `-5/-8/-11/-12/-14/-16` 與 `on-surface-opacity4`，缺 `.06` / `.10` 兩階。
2. **疊白後實色（sticky 配套）**：表頭 `#F4F8FE`、斑馬 `#F5F6F8`、hover `#F2F7FE`、selected `#E6F1FD`、selected+hover `#E1ECFC`——sticky 欄必需的「不透明」版本，無對應實色 token。
3. **列高 45 / 50px、表頭欄間分隔線高 26px**：量測值，待對齊 space / size token。
