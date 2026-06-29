---
name: DropDownTree
category: 輸入 — 選擇
tier: full
status: ✅ 已採用
authority: ERP CLAUDE.md（Syncfusion DropDownTree, @syncfusion/ej2-vue-dropdowns）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16953-250920   # 🎨 FAI2 Library · DropDownTree 元件集
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs（50 vars, node 16953-250920）+ get_design_context（node 16953-251285, Size=Small/State=Focus/Shape=Filled，含完整 overlay）+ get_screenshot（node 16953-250920）
---

> 依 `../component-doc-schema.md`（Full 層，overlay 含樹狀結構）產出。視覺值一律 token-reference；raw px 量測值指向值權威 `prototyper/assets/app.css`，不在此重印。
> §3–§5 已用 Figma `get_variable_defs` + `get_design_context`（節點 16953-251285，Filled / Small / Focus，含完整 overlay）校準；token gap 見文末「Figma 補入紀錄」。

---

## 1. 概述　📋

下拉樹狀選擇框。overlay 展開後顯示可展開/收合的階層節點列表，頂部內嵌即時搜尋欄，觸發框樣式與 DropDownList 同族（Material 3 Filled 底線式）。

**何時使用**：需要使用者從**有明確層級關係**的資料中擇一的表單欄位。典型 ERP 情境：科目樹狀選擇（母科目→子科目）、組織架構部門選擇（公司→部門→組）。  
**何時不用**：
- 扁平列表（無層級）→ `DropDownList`  
- 層級超過 3 層且需同時選多節點 → 考慮 `TreeView` 直接內嵌  
- 自由文字輸入 → `TextBox`；數值 → `NumericTextBox`

---

## 2. Anatomy　🎨

> 來源：`get_design_context`（節點 16953-251285，Small / Focus / Filled，`dropdown=true`）

```
DropDownTree（vertical stack, gap = {ds-space-margin-small}）
├── label-row?（horizontal, gap = 2px = {ds-space-margin-extra-small}）
│   ├── label-text
│   ├── required-asterisk?（*）
│   └── label-icon?（circle-info, 11×11）
├── trigger-container（h = 40px，Filled 底線式，與 DropDownList 同結構）
│   ├── left-icon?（16×16, opt）
│   ├── text-area（placeholder / selected-value）
│   ├── clear-icon-btn?（32×32, opt，`clearIcon` prop 控制）
│   └── arrow-down-btn（32×32，固定，不可省略）
│       └── arrow-down-icon（16×16）
├── helper-row?（horizontal）
│   ├── helper-text
│   └── alert-icon?（10×10）
└── overlay-popup?（absolute，展開時顯示，bottom=-232px of trigger）
    └── dropdown-list（rounded 4px，drop-shadow）
        ├── search-textbox（Filled, h=40px，bound to filter tree）
        └── tree-items-list（vertical stack）
            └── _Atom / Dropdown Tree（min-h=32px，逐節點）
                ├── [variant A: has-children node]
                │   ├── left-zone（px=8px）
                │   │   ├── chevron-icon（16×16，右指 = 收合 / 下指 = 展開）
                │   │   ├── node-icon（folder, 16×16）
                │   │   └── label-text
                │   └── right-zone（空，px=8px，寬 65px）
                └── [variant B: leaf node（child）]
                    ├── left-zone（pl=38px，較 parent 縮排 30px）
                    │   ├── node-icon（folder/file, 16×16）
                    │   └── label-text
                    └── right-zone（空，px=8px，寬 65px）
```

> **與 DropDownList 的 Anatomy 差異**：
> - overlay 頂部多一個 **inline search TextBox**（Filled / Small，無 label）
> - overlay 選項列為 **樹狀節點**（`_Atom / Dropdown Tree`），非扁平 `option` 列表
> - 觸發框多一個 `clearIcon`（`boolean` prop，`clearIcon` = true 時顯示 close-icon-btn）
> - 連接線（connector lines）：Figma 設計未包含樹狀縱橫連線；Syncfusion runtime 可選

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── 觸發框（trigger）────────────────────────────────────────────────────────
# 與 DropDownList 完全一致，以下僅列差異點，共用值見 DropDownList.md §3

height_small:         40px                                 # 🎨 量測值
height_medium:        48px                                 # 🎨 量測值（Figma metadata 確認）
height_large:         56px                                 # 🎨 量測值（Figma metadata 確認）
height_extra_small:   32px                                 # 🎨 量測值（Figma metadata 確認）

# 觸發框新增欄位（vs DropDownList）
clear_icon_btn_size:  32px                                 # 🎨 close-icon 按鈕框
clear_icon_size:      16px                                 # 🎨 close icon 圖示大小
clear_btn_bg:         "{color-sf-transparent}"             # 🔗 透明背景（靜態態）
clear_btn_radius:     "{ds-radius-2extra-large}"           # 🔗 16px（ripple 圓形）

# ── 觸發框 shape / color（Filled，與 DropDownList §3 共用）─────────────────
container_bg:         "{color-sf-surface-variant}"         # 🔗 rgb(237 240 247)
radius_top:           "{ds-radius-small}"                  # 🔗 4px（top-left + top-right）
radius_bottom:        0                                    # 🎨 底線式，無圓角
border_side:          bottom only                          # 🎨 Filled 特徵
border_width_default: "{ds-borderwidth-small}"             # 🔗 1px
border_width_active:  "{ds-borderwidth-medium}"            # 🔗 2px（focus / validation 態）
border_color_default: "{color-sf-outline}"                 # 🔗 rgb(127 137 150)

# ── overlay（popup）────────────────────────────────────────────────────────
overlay_bg:           "{color-sf-surface}"                 # 🔗 rgb(255 255 255)（白底）
overlay_radius:       "{ds-radius-small}"                  # 🔗 4px
overlay_shadow:       "{ds-shadow-md}"                     # 🔗（Figma: drop-shadow 0px 2px 3px 0.15 + 0px 1px 1px 0.3，對齊 --ds-shadow-md）
overlay_top_padding:  0                                    # 🎨 無上 padding
overlay_bottom_padding: 4px                                # 🎨 量測值；值見 app.css

# ── 搜尋欄（embedded search TextBox）──────────────────────────────────────
search_height:        40px                                 # 🎨 Filled Small，與觸發框同
search_border_bottom: "{ds-borderwidth-medium} {color-sf-primary}"  # 🔗 2px focus 態（固定顯示 focus 底線）
search_placeholder:   "{ds-color-placeholder}"             # 🔗 rgb(103 113 126)

# ── 樹狀節點（_Atom / Dropdown Tree）─────────────────────────────────────
node_height:          32px                                 # 🎨 min-height（文字可撐高）
node_px_root:         8px                                  # 🎨 根節點水平 padding（px=8px）；值見 app.css
node_pl_child_l1:     38px                                 # 🎨 第一層子節點左 padding（縮排 = 38px）；值見 app.css
node_pl_child_delta:  30px                                 # 🎨 每深一層再縮排 30px（推導值）；待下版確認
node_icon_gap:        8px                                  # 🎨 chevron / node-icon / label 間距（gap=8px）；值見 app.css
node_icon_size:       16px                                 # 🎨 chevron 與 folder/file icon 皆 16×16
node_label_color:     "{color-sf-on-surface}"              # 🔗 rgb(15 23 42)
node_label_font:      "{font-size-sf-text-md}"             # 🔗 14px
node_label_weight:    "{font-weight-sf-normal}"            # 🔗 400
node_label_lineheight: 1.5                                 # 🎨 Body Content/14pt/Regular
node_label_tracking:  0.24px                               # 🎨 Body Content/14pt/Regular
node_icon_color:      "{color-sf-on-surface-variant}"      # 🔗 rgb(60 74 91)（`Icon/$icon-color`）
chevron_icon:         chevron-right（收合）/ chevron-down（展開）  # 📋 Iconify: material-symbols:chevron-right / material-symbols:expand-more

# ── 字型（共用）───────────────────────────────────────────────────────────
font_family:          "{font-family-sf-fontfamily}"        # 🔗 Roboto（EN）/ Noto Sans TC（ZH）
```

---

## 4. Variants　🎨🔗

> Figma 元件集（節點 16953-250920）variant axes（metadata 抽取）：

```yaml
size:
  extra-small: { trigger_height: 32px }                   # 🎨
  small:       { trigger_height: 40px }                   # 🎨 ← ERP 主用
  medium:      { trigger_height: 48px }                   # 🎨
  large:       { trigger_height: 56px }                   # 🎨

shape:
  filled:                                                  # 🎨 唯一 shape（與 DropDownList 同族）
    bg:         "{color-sf-surface-variant}"               # 🔗
    border:     bottom only                                # 🎨
  outlined:                                                # 🎨 Figma 有此軸，未量測
    border:     all sides 1px                              # 📋（對應 Syncfusion Outline 樣式，token 待補）
  line:                                                    # 🎨 Figma 有此軸，未量測
    border:     bottom only, no bg fill                    # 📋

state:
  # 觸發框狀態（同 DropDownList §4）
  default:  "{color-sf-outline} 1px"
  hover:    see §5
  focus:    "{color-sf-primary} 2px"
  active:   "{color-sf-primary} 2px"    # 等同 focus
  filled:   "{color-sf-outline} 1px"    # 已有選取值
  read-only: no interaction
  disabled: "{color-sf-on-surface-opacity38}"
  info:     "{color-sf-info} 2px"
  success:  "{color-sf-success} 2px"
  warning:  "{color-sf-warning} 2px"
  error:    "{color-sf-error} 2px"

# ── tree node 獨立 variant ─────────────────────────────────────────────────
tree_node_type:
  has-children:                                            # 📋 具 chevron + 節點 icon
    chevron: visible                                       # 🎨
    icon:    folder（預設）                                # 📋
  leaf:                                                    # 📋 無 chevron，縮排
    chevron: hidden                                        # 🎨（左 padding 補足對齊）
    icon:    folder / file（依資料型態）                   # 📋
```

> **checkbox 軸**：Figma 本節點設計**未暴露** checkbox prop；單選模式為預設。Syncfusion runtime 支援多選（`showCheckBox: true`），多選 Figma 設計若存在應在另一節點——本文件以當前節點為準，多選待補。

---

## 5. States　🎨🔗

### 觸發框狀態

> 觸發框 state 與 DropDownList 完全一致；底線寬度：default → `{ds-borderwidth-small}`（1px）；focus/validation → `{ds-borderwidth-medium}`（2px）。

```yaml
default:
  container_bg:  "{color-sf-surface-variant}"              # 🔗
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}"  # 🔗 1px
  placeholder:   "{ds-color-placeholder}"                  # 🔗
  label:         "{color-sf-on-surface-variant}"           # 🔗
  icon:          "{color-sf-on-surface-variant}"           # 🔗（`Icon/$icon-color`）

hover:                                                     # 📋（Figma hover 態未精確量測；對齊 TextBox / DropDownList 推導）
  container_bg:  "{color-sf-surface-variant}"              # 🔗（底色保留，透明覆蓋層由主題提供）
  border_bottom: "{ds-borderwidth-small} {color-sf-on-surface-variant}"  # 🔗 略深底線

focus:                                                     # 🎨（Figma node 16953-251285 量測確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-primary}"  # 🔗 2px rgb(40 119 238)
  label:         "{color-sf-primary}"                      # 🔗（$primary，Focus 態 label 啟動色）

active:                                                    # 🎨（Figma node，=dropdown 展開中）
  border_bottom: "{ds-borderwidth-medium} {color-sf-primary}"  # 🔗

filled:                                                    # 📋（已有選取值，非 focus 狀態）
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}"  # 🔗
  selected_value_color: "{color-sf-on-surface}"            # 🔗 rgb(15 23 42)

read-only:                                                 # 📋
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}"  # 🔗
  text:          "{color-sf-on-surface}"                   # 🔗

disabled:                                                  # 🎨（Figma `Icon/$icon-color-disabled` 確認）
  text:          "{color-sf-on-surface-opacity38}"         # 🔗 38% opacity
  placeholder:   "{color-sf-on-surface-opacity38}"         # 🔗
  container_bg:  "{color-sf-surface-variant}"              # 🔗（底色保留）
  icon:          "{color-sf-on-surface-opacity38}"         # 🔗

info:                                                      # 🎨（Figma $border-info 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-info}"         # 🔗 2px rgb(46 144 250)
  helper:        "{color-sf-info}"                                 # 🔗

success:                                                   # 🎨（Figma $border-success 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-success}"      # 🔗 2px rgb(18 183 106)
  helper:        "{color-sf-success}"                              # 🔗

warning:                                                   # 🎨（Figma $border-warning 確認，本節點 var set 包含）
  border_bottom: "{ds-borderwidth-medium} {color-sf-warning}"      # 🔗 2px rgb(247 144 9)
  helper:        "{color-sf-warning}"                              # 🔗

error:                                                     # 🎨（Figma $border-error / $danger 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-error}"        # 🔗 2px rgb(244 73 62)
  label:         "{color-sf-error}"                                # 🔗
  helper:        "{color-sf-error}"                                # 🔗
```

### 樹狀節點狀態

```yaml
node_default:                                              # 🎨
  bg:     transparent
  text:   "{color-sf-on-surface}"                          # 🔗 rgb(15 23 42)
  icon:   "{color-sf-on-surface-variant}"                  # 🔗 rgb(60 74 91)

node_hover:                                                # 📋（Figma overlay 節點態未精確量測；依 Material 3 state-layer 推導）
  bg:     "{color-sf-on-surface-opacity8}"                 # 🔗 state-layer 8%（Material 3 hover）
  text:   "{color-sf-on-surface}"                          # 🔗

node_selected:                                             # 📋（Syncfusion Material 3 主題；Figma 未見選取態節點）
  bg:     "{color-sf-primary-opacity-12}"                  # 🔗 12% primary tint
  text:   "{color-sf-primary}"                             # 🔗

node_expanded:                                             # 🎨（chevron icon 旋轉 = 視覺態，非色彩變化）
  chevron_icon: expand-more（material-symbols:expand-more）# 📋 Iconify
  bg: transparent                                          # 🎨（父節點展開後背景無變化）

node_disabled:                                             # 📋
  text:   "{color-sf-on-surface-opacity38}"                # 🔗
  icon:   "{color-sf-on-surface-opacity38}"                # 🔗
```

---

## 6. Behavior 行為　📋

| 動作 | 觸發條件 | 效果 |
|------|---------|------|
| 展開 overlay | 點擊觸發框或 arrow-down-btn | popup 出現；搜尋欄自動 focus |
| 關閉 overlay | 點擊觸發框外區域 / 按 Esc | popup 消失；觸發框恢復 default 態 |
| 輸入搜尋 | 在 overlay 頂部搜尋欄輸入 | 即時過濾樹狀節點（含子節點名稱）；無結果時顯示 empty state |
| 展開父節點 | 點擊 chevron-right icon | 子節點展開顯示；chevron 旋轉為 expand-more |
| 收合父節點 | 點擊 expand-more chevron | 子節點隱藏；chevron 旋轉回 chevron-right |
| 選取節點 | 點擊節點列（任意位置） | overlay 關閉；觸發框顯示選取值；`node_selected` 狀態；觸發 `change` event |
| 清除選取 | 點擊 clear-icon-btn（`clearIcon=true` 時顯示） | 觸發框恢復 placeholder；清除選取值 |
| 多選（Syncfusion runtime） | `showCheckBox: true` | 節點左側出現 checkbox；支援 parent 勾選聯動子節點；觸發框顯示已選數量或逗號列表 |
| 選取父節點 | 點擊有子節點的父節點 label | 若允許父節點選取（`allowFiltering` + `treeSettings`），行為依設定；預設只能選葉節點 |

---

## 8. Keyboard　📋

| 按鍵 | 行為 |
|-----|------|
| `Tab` | 進入觸發框 |
| `Enter` / `Space` | 展開 overlay |
| `↑` / `↓` | 在節點間移動 focus（不分層級，按視覺順序） |
| `→` | 展開目前 focus 的父節點（若已收合） |
| `←` | 收合目前 focus 的父節點（若已展開）；或移到父節點 |
| `Enter` | 選取目前 focus 節點 |
| `Esc` | 關閉 overlay，focus 回觸發框 |
| `Home` / `End` | 移至第一 / 最後可見節點 |
| Overlay 搜尋欄 | 直接輸入字元自動 redirect 到搜尋欄（Syncfusion `filtering`） |

---

## 9. a11y　📋

- `role="combobox"` 於觸發框；`aria-expanded="true/false"` 隨 overlay 狀態更新。
- Overlay 使用 `role="tree"`；每個節點 `role="treeitem"`；有子節點的節點加 `aria-expanded="true/false"`；葉節點無此屬性。
- `aria-selected="true"` 於已選節點。
- 鍵盤 `←→` 展開/收合；`↑↓` 在 treeitem 間移動（跨層，依視覺順序）。
- `required` 欄位加 `aria-required="true"`；error 態加 `aria-invalid="true"` + `aria-describedby` 指向 helper text。
- 搜尋欄 input `aria-label="搜尋"` 或 `aria-controls` 指向 tree 容器。
- Focus ring：由 Syncfusion Material 3 主題提供，與 DropDownList 一致。
- 對比度：節點文字 `{color-sf-on-surface}` / `{color-sf-surface}` 背景 → 符合 AA；disabled 態 38% overlay 為 Material 3 慣例（< 4.5:1，屬 disabled 例外）。
- Mobile min touch target：節點列 `min-h=32px` 低於建議 44px；實際觸控需 Syncfusion runtime 設定 `itemHeight` 或外補 padding。

---

## 10. 跨平台 Adaptive　📋

| 斷點 | 觸發框 | Overlay |
|-----|--------|---------|
| ≥ 1280px（Web desktop） | Filled Small（h=40px）為主 | popup absolute，定位在觸發框下方 |
| ≤ 1024px（Tablet / 縮小 Web） | Filled Medium（h=48px）考慮改用以提高 touch 精準度 | popup 可改為 bottom-sheet（Syncfusion `popupHeight`） |
| App（iOS / Android） | Medium / Large 以利觸控 | Bottom sheet 模式強制覆蓋底部 |

> ERP 主場景為桌面瀏覽器（≥1280px）；App 版本若啟用，Size=Medium 為最低觸控底線。

---

## 11. Content 指引　📋

- **觸發框 placeholder**：點名選擇對象（「選擇科目」「選擇部門」），不用「請選擇」通用語。
- **搜尋欄 placeholder**：「搜尋…」（中性，Syncfusion 預設；可依情境換「搜尋科目代號或名稱」）。
- **無結果 empty state**：「找不到「XXX」」+ 副文案「請確認搜尋關鍵字或清除篩選」。
- **節點 label**：優先用中文全名（科目：「1141 應收帳款」；部門：「業務一部」），代號前置方便搜尋對中。
- **父節點選取**：若父節點本身可選（如「全公司」），label 後加 `（全選）`。

---

## 12. API / Props　📋

### Figma 設計層 props（`get_design_context` 節點 16953-251285）

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `label` | boolean | true | 是否顯示 label |
| `label1` | string | "Label" | label 文字 |
| `labelIcon` | boolean | false | 顯示 label 右側 info icon |
| `required` | boolean | true | 顯示必填 `*` |
| `text` | string | "Select a folder or file" | placeholder 文字 |
| `leftIcon` | boolean | false | 顯示左側 icon |
| `leftIcon1` | slot | — | 自訂左側 icon 內容 |
| `clearIcon` | boolean | false | 顯示清除（×）按鈕 |
| `dropdown` | boolean | false | 是否顯示 overlay（Figma 靜態控制） |
| `alertMessage` | boolean | false | 顯示 helper text 列 |
| `alertMessage1` | string | "Helping text" | helper 文字 |
| `alertIcon` | boolean | false | 顯示 helper 右側 alert icon |
| `alertIcon1` | slot | — | 自訂 alert icon |
| `shape` | "Filled" \| "Outlined" \| "Line" | "Filled" | 觸發框樣式 |
| `size` | "Extra Small" \| "Small" \| "Medium" \| "Large" | "Small" | 尺寸 |
| `state` | "Default" \| "Hover" \| "Focus" \| "Active" \| "Filled" \| "Read Only" \| "Disabled" \| "Info" \| "Success" \| "Warning" \| "Error" | "Default" | 觸發框狀態 |

### Syncfusion Runtime props（`<ejs-dropdowntree>`，`@syncfusion/ej2-vue-dropdowns`）

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `fields` | Object | — | 資料來源映射（`value` / `text` / `child` / `expanded` / `selected` / `iconCss`） |
| `dataSource` | Array / DataManager | — | 樹狀資料來源（扁平需指定 `parentValue` / `hasChildren`） |
| `value` | string \| string[] | — | 目前選取值（v-model） |
| `placeholder` | string | — | 觸發框 placeholder |
| `showCheckBox` | boolean | false | 多選 checkbox 模式 |
| `treeSettings` | Object | — | `expandOn`（Click/DblClick/None）/ `autoCheck`（父子聯動）/ `loadOnDemand` |
| `allowFiltering` | boolean | false | 啟用 overlay 內搜尋欄（`true` 時頂部出現 TextBox） |
| `filterBarPlaceholder` | string | — | 搜尋欄 placeholder |
| `showClearButton` | boolean | false | 顯示觸發框清除按鈕（對應 `clearIcon` Figma prop） |
| `enabled` | boolean | true | 啟用 / 停用 |
| `readonly` | boolean | false | 唯讀 |
| `popupHeight` | string | — | overlay 高度（如 "300px"） |
| `popupWidth` | string | — | overlay 寬度（預設同觸發框） |
| `floatLabelType` | "Auto" \| "Always" \| "Never" | "Never" | floating label 行為 |

---

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface §On-* §Outline §Status §Border Width §Space §Radius §Shadow §Typography §`--ds-*`
- 同族元件：`DropDownList.md`（觸發框 Filled 底線式，Anatomy / token 高度共用）；`TextBox.md`（overlay 內嵌搜尋欄結構）
- Prototype 落地（值權威 / class 命名）：`prototyper/assets/app.css`（Syncfusion Material 3 theme override）
- Code：`@syncfusion/ej2-vue-dropdowns`；playground 參考 `syncfusion-playground/app/pages/playground/`（dropdowntree 若有）
- Figma：`https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/` 節點 `16953-250920`（元件集）/ `16953-251285`（Small Focus Filled，含 overlay）

---

## Figma 補入紀錄（2026-06-29）

> Layer 1–4 依 Token Matching 五層工作流程執行。
> 來源：`get_variable_defs`（節點 16953-250920，50 變數）+ `get_metadata`（整張元件集）+ `get_design_context`（節點 16953-251285，Small/Focus/Filled，含完整 overlay tree）+ `get_screenshot`（元件集 16953-250920）。

**✅ 已確認對映（Figma 變數 → `athena-tokens.md`）**

`ColorSf/*`（surface-variant / outline / on-surface-variant / on-surface / primary / danger / info / success / warning / error / surface / transparent / on-surface-opacity38）、`DsColor/placeholder`、`Text/$content-text-color`、`Text/$placeholder-text-color`、`Icon/$icon-color`（= on-surface-variant）、`Icon/$icon-color-disabled`（= on-surface-opacity38 → Material 3 disabled）、字型（FontSizeSf/TextSm|TextMd|TextLg、FontWeightSf/Normal、FontFamilySf/Fontfamily）、排版（Label/12pt/Regular、Label/14pt/Regular、Body Content/12pt|14pt|16pt/Regular）、間距（DsSpace/Margin/Small=4px）、圓角（Radius/RadiusSm=4px、Radius/RadiusNone=0）。  
**50 個變數全數找到對應，無漂移。**  

**✅ 本節點 vs DropDownList 關鍵差異確認**

| 項目 | DropDownList | DropDownTree |
|---|---|---|
| `clearIcon` prop | Figma 層**無** | ✅ **有**（boolean prop，close-icon-btn 32×32） |
| `dropdown` prop | 無 | ✅ **有**（靜態控制 overlay 顯示） |
| overlay 頂部 | 無 | ✅ **inline search TextBox**（Filled, h=40px, focus-border） |
| overlay 選項 | 扁平 listbox | ✅ **樹狀節點**（`_Atom / Dropdown Tree`，含 chevron） |
| `$border-warning` in var set | ⚠️ 未出現 | ✅ **有**（`$warning`+`$border-warning` 皆確認） |
| shape 軸 | Filled only（本節點） | Filled / Outlined / Line（三軸） |
| size 軸 | Small only（本節點） | Extra Small / Small / Medium / Large（四軸） |

**⚠️ Token gap（非阻塞，已標 🎨 量測值）**

1. **`node_px_root: 8px`** / **`node_pl_child_l1: 38px`** / **`overlay_bottom_padding: 4px`** / **`node_icon_gap: 8px`**：`athena-tokens.md` 現有 scale（4/8/12/16px）中 8px 有 token（`{ds-space-margin-medium}` = 8px）但 38px 無。縮排值（8+16+14 組合）與 4px padding 均為 Figma 量測值，值權威見 `prototyper/assets/app.css`。
2. **`hover` 觸發框狀態底線色**：Figma hover 態節點（16953-251257）量測未補，以 TextBox / DropDownList 慣例推導；待補入。
3. **多選 checkbox 態**：本 Figma 節點未含 checkbox variant；Syncfusion runtime 有 `showCheckBox`，Figma 設計待另節點確認。
4. **Outlined / Line shape token 值**：metadata 確認兩 shape 存在，但 `get_design_context` 僅量測 Filled；Outlined / Line 的邊框 token 待補。
5. **節點 hover/selected bg**：Figma overlay 未見獨立 hover/selected 態節點；依 Material 3 state-layer 推導（8% / 12% primary tint），待 Figma 補入驗證。

**⬜ 待補（次要）**

- Shape=Outlined / Line 的觸發框 token 量測
- 多選（showCheckBox=true）overlay Figma 節點確認
- 節點 hover / selected 態 Figma 截圖驗證
- hover 觸發框底線色精確量測（補後更新 §5）
- Size=Extra Small / Medium / Large 的 trigger height 以外的 padding 量測
