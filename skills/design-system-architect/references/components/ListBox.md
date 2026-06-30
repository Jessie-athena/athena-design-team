---
name: ListBox（清單選取框）
category: 選取與輸入
tier: full
status: ✅ 已產出（Syncfusion ListBox；`@syncfusion/ej2-vue-dropdowns`）
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）；class·行為＝prototyper/profiles（用法權威）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16967-52258
last-synced: 2026-06-29
---

> 依 `../component-doc-schema.md`（Full 層）產出。三權威分工：**視覺值（尺寸 / 互動色 / 疊白實色）權威＝`prototyper/assets/app.css`**；**class·互動行為·markup 權威＝prototyper profile**；**本檔＝契約**（what/why/token-reference/state/a11y）。重疊處引用不重寫。
>
> ⚠️ **已知 token 缺口**：item 垂直 padding（Small=7px、Large=9px）無對應 `--ds-space-*` token（最近的 `--ds-space-padding-small`=4px / `--ds-space-padding-medium`=8px）；selected bg / hover 互動疊層（primary 8% / 12%）無 pre-wrap token。依 schema §2.2 **不臆造**，字面值以 `prototyper/assets/app.css` 為值權威，本檔僅標語意與缺口。

---

## 1. 概述　📋

ListBox 是**獨立列表容器**，用於在有限選項集中進行單選或多選，尤其適合以下場景：

**何時用**：
- 需要在「可選清單」與「已選清單」之間搬移項目（雙清單搬移 / dual transfer）
- 需要對已選項目進行拖拉重新排序
- 選項多且需搜尋過濾（配 searchBox）
- 選項需分組顯示（配 groupHeading）
- ERP 情境：權限設定搬移（左側可選 → 右側已選）、欄位顯示排序設定

**何時不用**：
- 簡單單選且選項 ≤ 8 個 → 用 `DropDownList`
- 核取多選且選項 ≤ 5 個 → 用 `CheckBox` 群組
- 選項需要摺疊收合層級 → 用 `TreeView`

---

## 2. Anatomy　🎨

```
.listbox-wrap                  ← 容器（flex column；外框 border + radius）
  ├─ .listbox-trigger?         ← 僅 Size=Small* variant：Label + 必填 * + 底線式觸發器（ArrowDown icon）
  ├─ .listbox-search?          ← 可選：SearchBox（底線式 input + 搜尋 icon；Small=32px / Large=40px）
  ├─ .listbox-header?          ← 可選：Group Header（分組標題列）
  └─ .listbox-items             ← 項目列容器（flex column）
       └─ .listbox-item         ← 單一項目列（text label；水平 padding 16px；垂直 padding 見 §3）
            ├─ [checkbox]?      ← Syncfusion 內建，Figma DS 無靜態呈現
            ├─ label text       ← 主要文字
            └─ [drag-handle]?   ← Syncfusion 內建，Figma DS 無靜態呈現
```

**Figma 節點資訊**（`get_design_context` 2026-06-29）：
- 頂層：`16967:52258` `List Box` frame（986×414px，含三 variant 並排展示）
- `16967:52259` `Size=Small`（298×280px）— 無觸發器，可選 searchBox / groupHeading
- `25425:195728` `Size=Small*`（298×366px）— 有 Label + 底線觸發器，可選 searchBox / groupHeading
- `16967:52371` `Size=Large`（298×336px）— 無觸發器，可選 searchBox / groupHeading

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── 容器 ──────────────────────────────────────────────
container:
  border:   "{ds-borderwidth-small} solid {color-sf-outline-variant}"   # 🔗 1px #d7dae0
  radius:   "4px"                                                        # 🎨 量測值（Figma: rounded-[4px]）；最近 token {ds-radius-small}=4px → 可對齊
  bg:       "{color-sf-surface}"                                         # 🔗 #ffffff
  shadow:   none                                                          # 無 elevation（非 overlay）

# ── Item 列（Atom / List Box）─────────────────────────
item:
  font:     "{font-size-sf-text-md} / {font-weight-sf-normal}"          # 🔗 14px / 400（Small）
  fg:       "{color-sf-on-surface}"                                      # 🔗 #0f172a
  padding-h: "{ds-space-padding-extra-large}"                            # 🔗 16px（左右）
  padding-v:
    small:  "7px"                                                        # 🎨 量測值；無對應 token（見待確認）
    large:  "9px"                                                        # 🎨 量測值；無對應 token（見待確認）
  # → item 高度（自動計算）：Small ≈ 34px（14*1.5+7*2）；Large ≈ 44px（16*1.5+9*2）

item_large:
  font:     "{font-size-sf-text-lg} / {font-weight-sf-normal}"          # 🔗 16px / 400（Large）
  fg:       "{color-sf-on-surface}"                                      # 🔗 #0f172a

# ── SearchBox ────────────────────────────────────────
searchbox:
  height:
    small:  "32px"                                                       # 🎨 量測值
    large:  "40px"                                                       # 🎨 量測值
  border:   "{ds-borderwidth-small} solid {color-sf-outline}"            # 🔗 底線式 1px #7f8996
  bg:       transparent                                                  # 透明底
  font:     "{font-size-sf-text-lg} / {font-weight-sf-normal}"          # 🔗 16px / 400（placeholder）
  placeholder-fg: "{ds-color-placeholder}"                               # 🔗 #67717e
  padding-h:
    small:  "8px"                                                        # 🎨 量測值
    large:  "12px"                                                       # 🎨 量測值

# ── Group Header ─────────────────────────────────────
group-header:
  font:     "{font-size-sf-text-md} / {font-weight-sf-medium}"          # 🔗 14px / 500（Small）
  font-lg:  "{font-size-sf-text-lg} / {font-weight-sf-medium}"          # 🔗 16px / 500（Large）
  fg:       "{color-sf-on-surface}"                                      # 🔗 #0f172a
  min-h:
    small:  "38px"                                                       # 🎨 量測值
    large:  "40px"                                                       # 🎨 量測值

# ── Trigger（Size=Small* 專有）────────────────────────
trigger:
  label-fg:     "{color-sf-on-surface-variant}"                         # 🔗 #3c4a5b
  label-font:   "{font-size-sf-text-md} / {font-weight-sf-normal}"      # 🔗 14px / 400
  required-fg:  "{color-sf-danger}"                                      # 🔗 #f4493e
  required-font: "{font-size-sf-text-sm}"                               # 🔗 12px
  input-h:      "40px"                                                   # 🎨 量測值
  input-border: "{ds-borderwidth-small} solid {color-sf-outline}"       # 🔗 底線式 1px #7f8996
  input-fg:     "{ds-color-placeholder}"                                 # 🔗 #67717e（placeholder 態）
  input-font:   "{font-size-sf-text-lg} / {font-weight-sf-normal}"      # 🔗 16px / 400
  icon-fg:      "{color-sf-on-surface-variant}"                         # 🔗 #3c4a5b（ArrowDown icon）
  padding:      "12px {ds-space-padding-medium} 8px {ds-space-padding-medium}" # 🔗 top 12px / h 8px
  gap:          "{ds-space-margin-extra-small}"                         # 🔗 4px（label ↔ required *）
```

> ⚠️ **token 缺口清單**（待 DS owner 確認後補入 `athena-tokens.md`）：
> 1. `item.padding-v.small = 7px`：介於 `--ds-space-padding-small`(4px) 與 `--ds-space-padding-medium`(8px) 之間，無單一 token
> 2. `item.padding-v.large = 9px`：介於 `--ds-space-padding-medium`(8px) 與 `--ds-space-padding-large`(12px) 之間，無單一 token
> 3. selected bg / hover 互動疊層（primary 8% / 12%）：無 `--color-sf-primary-opacity-8` 等 pre-wrap token；與 DataGrid 相同缺口，落地以 `rgba(var(--color-sf-primary), .08/.12)` 表示

---

## 4. Variants　🎨🔗

Figma 元件集的 variant 軸為 **Size**（Small / Small* / Large）。`searchBox` 與 `groupHeading` 為布林 prop，與 Size 正交。

```yaml
# ── Variant 軸 1：Size ────────────────────────────────
size=small:
  item-font:     "{font-size-sf-text-md}"     # 🔗 14px
  item-padding-v: "7px"                       # 🎨 量測值（缺 token，見 §3）
  searchbox-h:   "32px"                       # 🎨
  search-padding-h: "8px"                     # 🎨

size=small_with_trigger:                       # Figma 名稱 "Size=Small*"
  # 繼承 size=small 的 item 規格
  # 額外：Label row + 底線觸發器（見 §3 trigger 區段）
  note: "用於 dropdown-open 態或獨立 select-then-list 複合元件；非雙清單"

size=large:
  item-font:     "{font-size-sf-text-lg}"     # 🔗 16px
  item-padding-v: "9px"                       # 🎨 量測值（缺 token，見 §3）
  searchbox-h:   "40px"                       # 🎨
  search-padding-h: "12px"                    # 🎨
  group-header-font: "{font-size-sf-text-lg} / {font-weight-sf-medium}"  # 🔗

# ── Variant 軸 2（布林 props，正交）──────────────────
searchBox:
  true:   "listbox 頂部加搜尋欄（底線式 input + search icon）"
  false:  "無搜尋欄（預設）"

groupHeading:
  true:   "各分組上方顯示 Header 列（font-weight medium）"
  false:  "無分組標題（預設）"

# ── Syncfusion 擴充 variant（Figma DS 未呈現，由 ejs-listbox API 控制）────
checkbox:
  true:   "每項目前置 checkbox（multiselect 模式）；Syncfusion selectionType: 'Multiple'"
  false:  "無 checkbox（預設）"

drag-to-reorder:
  true:   "顯示 drag handle icon（right side）；Syncfusion allowDragAndDrop: true"
  false:  "無 drag handle（預設）"

dual-transfer:
  note:   "雙清單搬移：兩個 <ejs-listbox> + toolbarSettings scope；非獨立 variant，
           而是兩個 ListBox 實例的組合佈局；中間 toolbar 含 ▶ / ◀ / ▲ / ▼ 四個操作鈕"
```

---

## 5. States　🎨🔗

```yaml
# ── Item 互動 states ──────────────────────────────────
item_default:
  bg:   transparent                                          # {color-sf-surface} 繼承自容器
  fg:   "{color-sf-on-surface}"                             # 🔗 #0f172a

item_hover:
  bg:   "primary 8% 疊層（缺 token；見 §3 缺口 #3）"
  # 落地：rgba(var(--color-sf-primary), .08)
  fg:   "{color-sf-on-surface}"                             # 🔗 不變

item_selected:
  bg:   "primary 12% 疊層（缺 token；見 §3 缺口 #3）"
  # 落地：rgba(var(--color-sf-primary), .12)
  fg:   "{color-sf-on-surface}"                             # 🔗 不變

item_selected_hover:
  bg:   "{color-sf-primary-opacity-14}"                     # 🔗 #2877ee / 0.14（有 token）

item_focus:
  outline: "{ds-shadow-focus-ring1}"                        # 🔗 3px 黑外環 + 1px 白內環
  # 落地時 outline-offset: 2px

item_disabled:
  fg:   "{color-sf-on-surface-variant-opacity38}"           # 🔗 #3c4a5b / 0.38
  cursor: not-allowed

item_drag:
  bg:   "{color-sf-primary-container}"                      # 🔗 #d5e4ff（拖拉中的 placeholder 高亮）
  opacity: "0.5"                                             # 被拖拉的 item 本身半透明

# ── 容器層 states ─────────────────────────────────────
container_focus-within:
  border: "{ds-borderwidth-small} solid {color-sf-primary}" # 🔗 focus 態外框轉 primary

# ── 資料生命週期（Full 層必含）───────────────────────
empty:
  # 無項目時顯示 empty state：inbox-outline icon + 說明文字（如「無可選項目」）
  icon:    "{color-sf-on-surface-variant}"                  # 🔗
  text-fg: "{color-sf-on-surface-variant}"                  # 🔗

loading:
  # Skeleton 佔位列（使用 Skeleton 元件，非 spinner）；保留列高避免跳版

error:
  # 載入失敗時顯示 error state + 重試入口；不靜默空白
```

---

## 6. Behavior 行為　📋

| 互動 | 行為 |
|---|---|
| 點擊 item | 選取（single 模式切換；multiple 模式加入 / 移除） |
| `Shift+Click` | 範圍選取（multiple 模式） |
| `Ctrl/⌘+Click` | 個別加選 / 去選（multiple 模式） |
| SearchBox 輸入 | 即時過濾顯示的 items（client-side filter，不走 API） |
| Drag item | 拖起時 item 半透明、目的地顯示 drop indicator；落下後排序更新 |
| Dual transfer ▶ | 將左側選取項目搬移至右側清單 |
| Dual transfer ◀ | 將右側選取項目搬回左側清單 |
| Dual transfer ▲ / ▼ | 在同一清單內上移 / 下移選取項目（排序調整） |
| Select All | `Ctrl/⌘+A` 全選（multiple 模式）；若有 toolbar 可提供「全選」鈕 |
| Keyboard Arrow | `↑` / `↓` 在 items 間移動焦點；`Space` 切換選取 |

---

## 7. RWD 響應式　📋

ListBox 本身為**固定寬度容器**（Figma 示範寬 298px），由父層佈局決定寬度是否彈性：

- **桌面（XL/L）**：雙清單（dual transfer）並排，中間 toolbar 垂直排列；高度通常固定並啟用 overflow-y scroll
- **平板（M）**：雙清單可收為上下堆疊（available → selected 由上而下）
- **手機（S）**：同平板堆疊；toolbar 由垂直改為水平排列（▶ ◀ 而非 ▲ ▼）；不建議在 S 斷點使用 drag-to-reorder（觸控難操作）

---

## 8. Keyboard　📋

| 按鍵 | 行為 |
|---|---|
| `Tab` | 進入 / 離開 ListBox 焦點 |
| `↑` / `↓` | 移動焦點至上 / 下一個 item |
| `Space` | 切換當前 focused item 的選取狀態 |
| `Enter` | 確認選取（與 Space 等效；single 模式可關閉 trigger dropdown） |
| `Escape` | 取消 drag；若 trigger 展開則收合 |
| `Ctrl/⌘+A` | 全選（multiple 模式） |
| `Shift+↑/↓` | 範圍選取延伸（multiple 模式） |
| `Home` / `End` | 跳至第一 / 最後一個 item |

Dual transfer 的 toolbar 鈕（▶ ◀ ▲ ▼）需可 Tab 到並以 Enter / Space 觸發。

---

## 9. a11y 無障礙　📋

- 容器加 `role="listbox"`；multiple 模式加 `aria-multiselectable="true"`
- 每個 item 加 `role="option"`；選取態加 `aria-selected="true"`；disabled 加 `aria-disabled="true"`
- SearchBox 加 `aria-label="搜尋"` 或關聯 `<label>`（`sr-only`）
- Group Header 加 `role="group"` + `aria-labelledby` 指向 Header 文字節點
- Drag 排序：提供 `aria-grabbed` + `aria-dropeffect`；每次 drop 後 toast 通知（如「已將 X 移至第 3 位」）
- Dual transfer toolbar 鈕加 `aria-label`（「移至已選」「移至可選」「上移」「下移」）
- focus ring 使用 `{ds-shadow-focus-ring1}`（3px 黑外環 + 1px 白內環，符合 WCAG 2.1 §2.4.11 focus appearance）
- 顏色對比：`{color-sf-on-surface}` 於 `{color-sf-surface}` 對比 ≥ 16:1（遠超 AA 4.5:1）
- 觸控最小目標：item 高度（Small ≈34px）低於建議 44px；如需 App 佈局，改用 Large size（≈44px）
- 空狀態使用文字說明，不只靠 icon 傳達意義

---

## 10. 跨平台 Adaptive　📋

- **桌面 Web**：完整功能（drag / dual / search / grouping）；推薦 Small size（資料密集）
- **App Web（M 斷點）**：dual transfer 改上下堆疊；drag-to-reorder 保留（觸控拖拉可行）
- **App 手機（S 斷點）**：建議改用 Bottom Sheet + Chip 多選取代複雜 ListBox；若保留，使用 Large size（44px 觸控目標）

---

## 11. Content 指引　📋

- **Empty state 主訊息**：說明為何空 + 引導下一步（如「尚無可選項目」「請先建立選項後再設定」）
- **Group Header 文字**：名詞（「財務模組」「銷售模組」），不用動詞
- **搜尋無結果**：「無符合『XXX』的結果」，顯示當前搜尋詞
- 一般 microcopy 詳 `ux-writer`

---

## 12. API / Props　📋

以下為設計指引層 props；Syncfusion `<ejs-listbox>` API 以 [官方文件](https://ej2.syncfusion.com/vue/documentation/listbox/getting-started) 為準。

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `size` | `small` \| `large` | `small` | 對映 Figma Size variant（item 高度 / 字級） |
| `searchBox` | Boolean | false | 顯示搜尋欄 |
| `groupHeading` | Boolean | false | 顯示分組標題 |
| `withTrigger` | Boolean | false | 顯示 Label + 底線觸發器（對映 Size=Small*） |
| `label` | String | — | withTrigger 模式的 label 文字 |
| `required` | Boolean | false | withTrigger 模式顯示必填 `*` |
| `placeholder` | String | `請選擇` | withTrigger 模式觸發器 placeholder |
| `allowDragAndDrop` | Boolean | false | 啟用拖拉排序（Syncfusion `allowDragAndDrop`） |
| `selectionType` | `Single` \| `Multiple` | `Multiple` | 單選 / 多選 |
| `toolbarSettings` | Object | — | 雙清單搬移（Syncfusion `toolbarSettings`）；設定 `items` 與 `scope` |
| `dataSource` | Array | [] | 選項資料 |
| `fields` | Object | `{ text:'text', value:'value' }` | dataSource 欄位映射 |

**Dual Transfer 設定範例（ERP 情境：權限設定搬移）**

```vue
<!-- 可選清單 -->
<ejs-listbox
  id="available"
  :dataSource="availableItems"
  :fields="{ text: 'name', value: 'id' }"
  :toolbarSettings="{ items: ['MoveTo', 'MoveFrom', 'MoveAllTo', 'MoveAllFrom'], position: 'Right' }"
  scope="#selected"
/>
<!-- 已選清單 -->
<ejs-listbox
  id="selected"
  :dataSource="selectedItems"
  :fields="{ text: 'name', value: 'id' }"
  :allowDragAndDrop="true"
/>
```

---

## 13. 關聯 Links　🔗

- Tokens：`../athena-tokens.md`
  - §Primary（interactive states：primary-opacity-14、primary-container）
  - §Surface / On-* 前景文字色（item text、group header）
  - §Outline（container border、searchbox border）
  - §Space（padding-h 16px = extra-large）
  - §Typography（text-md / text-lg / text-sm / weight-normal / weight-medium）
  - §Shadow（focus-ring1）
- 語意對照：`../athena-design.md`
- 子元件：`../components/st-chip.md`（選取 chip 如需在清單外展示已選項目）
- **值權威（尺寸 / 互動色，勿在此重寫）**：`prototyper/assets/app.css`（canonical CSS）
- Code：`@syncfusion/ej2-vue-dropdowns`（`<ejs-listbox>`）
  - [Syncfusion ListBox Vue 文件](https://ej2.syncfusion.com/vue/documentation/listbox/getting-started)
  - [Dual ListBox / toolbarSettings](https://ej2.syncfusion.com/vue/documentation/listbox/dual-listbox)
  - [Drag and Drop](https://ej2.syncfusion.com/vue/documentation/listbox/drag-and-drop)
- 相關元件：`DropDownList.md`（單選 dropdown，ListBox 的輕量替代）

---

## Figma 補入紀錄

| 日期 | 動作 | 結果 |
|---|---|---|
| 2026-06-29 | `get_variable_defs(JhcpyIEEzSChcEXMRJUiIm, 16967:52258)` | 取得 token 映射（20 個變數）；確認色彩 / 字型 / 間距 token 名稱 |
| 2026-06-29 | `get_screenshot(JhcpyIEEzSChcEXMRJUiIm, 16967:52258)` | 確認 3 個 variant 視覺外觀：Small / Small*（with trigger）/ Large；無 checkbox / drag handle 在靜態畫面 |
| 2026-06-29 | `get_metadata(JhcpyIEEzSChcEXMRJUiIm, 16967:52258)` | 取得 3 個子節點 ID 與尺寸 |
| 2026-06-29 | `get_design_context(JhcpyIEEzSChcEXMRJUiIm, 16967:52259)` | Size=Small 完整 DOM；確認 item padding / 搜尋欄 / 分組 header 量測值 |
| 2026-06-29 | `get_design_context(JhcpyIEEzSChcEXMRJUiIm, 25425:195728)` | Size=Small*（with trigger）完整 DOM；確認 label / required / placeholder / ArrowDown icon |
| 2026-06-29 | `get_design_context(JhcpyIEEzSChcEXMRJUiIm, 16967:52371)` | Size=Large 完整 DOM；確認 item padding 9px / 16px 字級差異 |
| 2026-06-29 | token 缺口登記 | item padding-v（7px/9px）、selected/hover 互動疊層（primary 8%/12%）無對應 token；待 DS owner 決定是否新增（見 §3 缺口清單） |
| 2026-06-29 | Dual ListBox 確認 | 此節點集無雙清單 variant；雙清單為 Syncfusion `toolbarSettings + scope` 組合佈局，非獨立 Figma variant |
