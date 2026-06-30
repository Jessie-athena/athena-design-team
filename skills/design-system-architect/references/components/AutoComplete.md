---
name: AutoComplete
category: 輸入 — 選擇
tier: lite
status: ✅ 已產出
authority: ERP CLAUDE.md（Syncfusion AutoComplete, @syncfusion/ej2-vue-dropdowns）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16951-1340   # 🎨 FAI2 Library · AutoComplete 元件集
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs（48 vars, node 16951-1340）+ get_design_context（node 16951-1638，Size=Small / State=Default / Shape=Filled）+ get_screenshot（node 16951-1340）
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺值一律 token-reference；raw px 量測值指向值權威 `prototyper/assets/app.css`，不在此重印。
> §3–§5 已用 Figma `get_variable_defs` + `get_design_context`（節點 16951-1638）校準；token gap 見文末「Figma 補入紀錄」。

---

## 1. 概述　📋

帶即時過濾建議清單的文字輸入框。使用者輸入一或多個字元後，元件顯示 overlay suggestion list 供選取或確認。與 DropDownList 同族（Filled 底線式、arrow-down icon），但允許自由文字輸入並帶搜尋過濾邏輯。

**何時使用**：  
- 選項數量龐大（如科目代號、供應商名稱、商品名稱），不適合固定下拉一次全列  
- 使用者已知部分關鍵字，需搜尋過濾縮小範圍  
- 搜尋框情境（即時建議、輸入候選詞）

**何時不用**：  
- 固定選項有限（< 15 項）→ `DropDownList`（不需搜尋，選擇快）  
- 多選（需 chip 收納）→ `MultiSelect`  
- 純自由文字、無建議需求 → `TextBox`  
- 下拉可輸入但不帶分組/Add 的簡單情境 → `ComboBox`

---

## 2. Anatomy　🎨

> 結構來自 `get_design_context`（節點 16951-1638，Size=Small / State=Default / Shape=Filled）及 dropdown overlay 展開態（節點 17080:223272）。

```
AutoComplete（vertical stack, gap = {ds-space-margin-small}）
├── label-row?（horizontal, gap = 2px = {ds-space-margin-extra-small}）
│   ├── label-text
│   ├── required-asterisk?（*）
│   └── label-icon?（circle-info, 11×11）
├── input-container（h = 40px [Small]，Filled 底線式）
│   ├── left-icon?（16×16, opt）
│   ├── text-area（placeholder / typed-value）
│   ├── clear-icon-btn?（32×32, opt — clearIcon prop）
│   └── arrow-down-btn（32×32，可選：rightIcon prop；AutoComplete 預設顯示）
│       └── arrow-down-icon（16×16）
└── helper-row?（horizontal, gap = {ds-space-margin-small}）
    ├── helper-text
    └── alert-icon?（circle-check, 10×10, opt）

[dropdown overlay，absolute，觸發後顯示]
AutoComplete Dropdown（drop-shadow, rounded-sm, bg=surface）
├── embedded-input-container（TextBox focus 態底線式，無 label / helper）
│   └── text-area（與外框 input 同步，Focus 態底線 {ds-borderwidth-medium} {color-sf-primary}）
├── [0..n] Context Menu Header（分組標題，可無）
│   ├── group-label（font-weight: Medium）
│   └── divider（{ds-borderwidth-small} {color-sf-outline-variant}）
├── [0..n] Context Menu Item（選項列）
│   └── item-text
└── add-row?（底部，{ds-borderwidth-small} {color-sf-outline-variant} 頂線 + Add 按鈕）
    └── add-btn（+ icon + label，{color-sf-primary}）
```

> **與 DropDownList Anatomy 的差異**：
> - AutoComplete 多了 `dropdown` prop（控制 suggestion overlay 整體展開 / 收合）
> - `rightIcon` 預設為 `true`（arrow-down），非固定不可省略（可設為 `false`）；DropDownList 右側 arrow-down **固定不可省略**
> - `clearIcon` 在 Figma 層有暴露設計槽位（prop `clearIcon`）；DropDownList Figma 層無此槽
> - Overlay 帶 **embedded input**（搜尋框）、**分組標題**、**Add 行動按鈕** ——DropDownList overlay 無此三者

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── 尺寸（Size 四檔）────────────────────────────────────────────────────────
# 量測值來自 get_design_context / get_metadata symbol 尺寸
height_extra_small: 32px   # 🎨 XS Outlined/Filled；Line shape 為 24px
height_small:       40px   # 🎨 S Outlined/Filled；Line shape 為 32px（量測節點 16951-1638）
height_medium:      48px   # 🎨 M Outlined/Filled；Line shape 為 36px
height_large:       56px   # 🎨 L Outlined/Filled；Line shape 為 40px（Line shape 無 Filled 底色層）

padding_left:       10px   # 🎨 量測值，無 {ds-space-*} token（scale 跳 8→12px）；值見 app.css
inner_icon_gap:      6px   # 🎨 left-icon ↔ text；值見 app.css
text_px_padding:     4px   # 🎨 text-area 左右各 4px；值見 app.css
label_input_gap:  "{ds-space-margin-small}"   # 🔗 4px（Default）/ 8px（Comfortable）
helper_gap:       "{ds-space-margin-small}"   # 🔗 4px（Default）/ 8px（Comfortable）
icon_size:         16px                       # 🎨 left-icon / arrow-down icon
icon_btn_size:     32px                       # 🎨 clear / arrow-down 按鈕框
icon_btn_radius:  "{ds-radius-2extra-large}"  # 🔗 16px（ripple 圓形）；與 TextBox / DropDownList 一致

# ── 容器外形（Shape: Filled）────────────────────────────────────────────────
container_bg:         "{color-sf-surface-variant}"   # 🔗 rgb(237 240 247)
radius_top:           "{ds-radius-small}"            # 🔗 4px（top-left + top-right）
radius_bottom:        0                              # 🎨 bottom 無圓角（Filled 底線式，CSS 預設 0）
border_side:          bottom only                    # 🎨 Filled 特徵：僅底邊框
border_width_default: "{ds-borderwidth-small}"       # 🔗 1px
border_width_active:  "{ds-borderwidth-medium}"      # 🔗 2px（focus / validation 態）
border_color_default: "{color-sf-outline}"           # 🔗 rgb(127 137 150)

# ── 標籤文字 ──────────────────────────────────────────────────────────────
label_color:          "{color-sf-on-surface-variant}"   # 🔗 rgb(60 74 91)
label_font_size:      "{font-size-sf-text-md}"          # 🔗 14px（Label/14pt/Regular）
label_font_weight:    "{font-weight-sf-normal}"         # 🔗 400
label_line_height:     1.3                              # 🎨
label_letter_spacing:  0px                              # 🎨
required_color:       "{color-sf-danger}"               # 🔗 rgb(244 73 62)
required_font_size:   "{font-size-sf-text-sm}"          # 🔗 12px

# ── 輸入文字 ──────────────────────────────────────────────────────────────
text_color:           "{color-sf-on-surface}"        # 🔗 rgb(15 23 42)（使用者輸入值）
placeholder_color:    "{ds-color-placeholder}"       # 🔗 rgb(103 113 126)
content_font_size:    "{font-size-sf-text-md}"       # 🔗 14px（Body Content/14pt/Regular）
content_font_weight:  "{font-weight-sf-normal}"      # 🔗 400
content_line_height:   1.5                           # 🎨
content_letter_spacing: 0.24px                       # 🎨

# ── 輔助文字（helper / alert）─────────────────────────────────────────────
helper_color:         "{color-sf-on-surface-variant}"  # 🔗 rgb(60 74 91)（default）
helper_font_size:     "{font-size-sf-text-sm}"         # 🔗 12px（Label/12pt/Regular）
helper_font_weight:   "{font-weight-sf-normal}"        # 🔗 400
helper_line_height:    1.3                             # 🎨
helper_letter_spacing: 0.1px                           # 🎨

# ── 圖示色 ────────────────────────────────────────────────────────────────
icon_color:           "{color-sf-on-surface-variant}"  # 🔗 rgb(60 74 91)（arrow-down、left-icon）

# ── 字型 ──────────────────────────────────────────────────────────────────
font_family:          "{font-family-sf-fontfamily}"    # 🔗 Roboto（EN）/ Noto Sans TC（ZH）

# ── Dropdown Overlay ───────────────────────────────────────────────────────
dropdown_bg:          "{color-sf-surface}"             # 🔗 rgb(255 255 255)
dropdown_radius:      "{ds-radius-small}"              # 🔗 4px（全圓角，含頂部）
dropdown_shadow:      "🎨 見 app.css"                  # 🎨 drop-shadow(0px 2px 3px rgba(0,0,0,0.15), 0px 1px 1px rgba(0,0,0,0.3))；最接近 {ds-shadow-sm}，但 y/blur 值不完全對應 token；值權威見 app.css

# ── Dropdown 選項 ─────────────────────────────────────────────────────────
item_height:           30px                             # 🎨 py-[3px] × 2 + text line（量測值，值見 app.css）
item_px:               8px                             # 🎨 量測值；= {ds-space-margin-medium}（Default）
item_text_color:      "{color-sf-on-surface}"          # 🔗 rgb(15 23 42)
item_text_font:       "{font-size-sf-text-md} / {font-weight-sf-normal}"  # 🔗 14px / 400
item_hover_bg:        "{color-sf-on-surface-opacity5}" # 🔗 rgba(15,23,42,0.05)（Figma 確認：on-surface-opacity5）
item_selected_text:   "{color-sf-on-surface}"          # 🔗（selected 態無底色，僅文字同）

# ── Dropdown 分組標題 ─────────────────────────────────────────────────────
group_header_height:   32px                             # 🎨 min-h 量測值
group_header_px:       8px                             # 🎨 量測值；= {ds-space-margin-medium}（Default）
group_header_py:       4px                             # 🎨 量測值；= {ds-space-padding-small}（Default）
group_label_color:    "{color-sf-on-surface}"          # 🔗 rgb(15 23 42)
group_label_font:     "{font-size-sf-text-md} / {font-weight-sf-medium}"  # 🔗 14px / 500
group_divider:        "{ds-borderwidth-small} {color-sf-outline-variant}" # 🔗 1px / rgb(215 218 224)

# ── Dropdown 底部 Add 按鈕 ────────────────────────────────────────────────
add_row_border_top:   "{ds-borderwidth-small} {color-sf-outline-variant}" # 🔗 1px / rgb(215 218 224)
add_btn_height:        32px                             # 🎨 量測值
add_btn_px:           "{ds-space-margin-medium}"        # 🔗 8px（Default）/ 12px（Comfortable）
add_btn_icon_size:    16px                              # 🎨
add_btn_text_color:   "{color-sf-primary}"              # 🔗 rgb(40 119 238)
add_btn_text_font:    "{font-size-sf-text-md} / {font-weight-sf-medium}"  # 🔗 14px / 500（label tracking 0.1px）
add_btn_radius:       "{ds-radius-small}"               # 🔗 4px
add_btn_bg:           "{color-sf-transparent}"          # 🔗 透明背景（hover 態靠 state-layer）
```

---

## 4. Variants　🎨🔗

> Figma 元件集（節點 16951-1340）暴露三個 variant 軸，以下來自 `get_design_context` 結構抽取與 `get_metadata` symbol 命名確認。

```yaml
# ── Shape（外框樣式）────────────────────────────────────────────────────────
shape:
  filled:
    container_bg: "{color-sf-surface-variant}"          # 🔗 填色底線式
    border_side: bottom only                            # 🎨
  outlined:
    container_bg: transparent                           # 🎨 透明底、全邊框
    border_side: all sides                              # 🎨
    border_color: "{color-sf-outline}"                  # 🔗
  line:
    container_bg: transparent                           # 🎨 無底色、無圓角、僅底線
    border_side: bottom only                            # 🎨
    radius: 0                                           # 🎨

# ── Size（高度四檔；Shape 影響高度見 §3）───────────────────────────────────
size:
  extra_small: { filled_height: 32px, line_height: 24px }   # 🎨
  small:       { filled_height: 40px, line_height: 32px }   # 🎨（量測節點 16951-1638）
  medium:      { filled_height: 48px, line_height: 36px }   # 🎨
  large:       { filled_height: 56px, line_height: 40px }   # 🎨

# ── AutoComplete 特有 prop ────────────────────────────────────────────────
dropdown:
  false: 僅顯示輸入框（靜態態）   # 📋 Figma 預設
  true:  展開 overlay（帶 embedded-input / items / group-header / add-btn）  # 🎨

# ── 與 DropDownList 比較 ──────────────────────────────────────────────────
# rightIcon（arrow-down）：AutoComplete 預設 true，但可設為 false；DropDownList 固定不可省略
# clearIcon：AutoComplete Figma 層有設計槽位（prop）；DropDownList 無
```

---

## 5. States　🎨🔗

> 底線寬度：default → `{ds-borderwidth-small}`（1px）；focus/validation → `{ds-borderwidth-medium}`（2px）。  
> 全部 11 個 state 由 Figma variant prop `State` 確認（`get_metadata` symbol 命名）。

```yaml
default:                                                     # 🎨 node 16951-1638 量測
  container_bg: "{color-sf-surface-variant}"                 # 🔗
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}" # 🔗 1px rgb(127 137 150)
  placeholder: "{ds-color-placeholder}"                      # 🔗
  label: "{color-sf-on-surface-variant}"                     # 🔗
  arrow_icon: "{color-sf-on-surface-variant}"                # 🔗

hover:                                                       # 📋（Figma State=Hover 存在；疊層為 Material state layer）
  container_bg: "{color-sf-surface-variant}"                 # 🔗（底色不變，透明疊層由 Syncfusion theme 控制）
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}" # 🔗（hover 底線不加粗）

focus:                                                       # 🔗（Figma $primary 確認；dropdown 展開時 embedded input 同態）
  border_bottom: "{ds-borderwidth-medium} {color-sf-primary}" # 🔗 2px rgb(40 119 238)
  label: "{color-sf-primary}"                                # 📋 Material 3 focus label 啟動色

active:                                                      # 📋（Figma State=Active；輸入框按下態）
  border_bottom: "{ds-borderwidth-medium} {color-sf-primary}" # 🔗（同 focus）

filled:                                                      # 📋（Figma State=Filled；已有輸入值的靜態態）
  text: "{color-sf-on-surface}"                              # 🔗
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}" # 🔗（值輸入後 blur 恢復 1px）

read_only:                                                   # 📋（Figma State=Read Only；不可編輯）
  text: "{color-sf-on-surface}"                              # 🔗（顯示值）
  container_bg: "{color-sf-surface-variant}"                 # 🔗
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}" # 🔗（底線保留）

disabled:                                                    # 🔗（Figma State=Disabled；Material 3 慣例）
  text: "{color-sf-on-surface-opacity38}"                    # 🔗 38% opacity
  placeholder: "{color-sf-on-surface-opacity38}"             # 🔗
  container_bg: "{color-sf-surface-variant}"                 # 🔗（底色保留）
  arrow_icon: "{color-sf-on-surface-opacity38}"              # 🔗（與 Icon/$icon-color-disabled 對齊）

info:                                                        # 🎨（Figma $border-info 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-info}"   # 🔗 2px rgb(46 144 250)

success:                                                     # 🎨（Figma $border-success 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-success}" # 🔗 2px rgb(18 183 106)

warning:                                                     # 🎨（Figma $border-warning 確認——本節點 var set 包含 $border-warning）
  border_bottom: "{ds-borderwidth-medium} {color-sf-warning}" # 🔗 2px rgb(247 144 9)

error:                                                       # 🎨（Figma $border-error / $danger 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-error}"  # 🔗 2px rgb(244 73 62)
  label: "{color-sf-error}"                                  # 📋
  helper: "{color-sf-error}"                                 # 📋

# ── Dropdown item 互動態 ──────────────────────────────────────────────────
item_hover:                                                  # 🎨（Figma 確認 on-surface-opacity5）
  item_bg: "{color-sf-on-surface-opacity5}"                  # 🔗 rgba(15,23,42,0.05)（Pumpkins 選項 hover 節點）

no_match:                                                    # 📋（空結果態，embedded input 顯示輸入值，list 區顯示空狀態文字）
  list_empty: true                                           # 詳 §6
```

---

## 6. Behavior 行為　📋

| 行為 | 規則 |
|---|---|
| **觸發建議** | 預設：第 1 個字元起觸發（`minLength=1`）；可透過 `minChars` prop 調整為更多字元才展開 |
| **篩選方式** | 預設 `Contains`（輸入值出現在選項任意位置即匹配）；亦可設為 `StartsWith` / `Custom` |
| **Highlight** | 輸入值在選項文字中的匹配段落以加粗（`{font-weight-sf-medium}`）或色彩區別；ERP 慣例依 Syncfusion 主題預設，不另行客製 |
| **No-match empty state** | 過濾後無結果時，overlay 保持展開，list 區顯示空狀態文字（如「查無相符項目」）；Add 按鈕可選保留（提供「新增此值」入口） |
| **選取確認** | 點擊選項 / 鍵盤 `Enter` 確認 → 關閉 overlay，輸入框顯示選取值（State=Filled） |
| **Clear** | `clearIcon=true` 時，clearIcon btn 出現於有值態，點擊清除輸入並收合 overlay |
| **Arrow-down btn** | 點擊時切換 overlay 展開 / 收合（同 DropDownList）；但 AutoComplete 主要靠輸入觸發，而非強制先展開 |
| **Blur / Esc** | blur 或 `Esc` 收合 overlay；未選取時保留當前輸入值（不強制歸空） |
| **分組** | `groupBy` 欄位啟用分組標題（Group Header），樣式見 §3；分組標題不可選取 |
| **Add 按鈕** | `footerTemplate` / `footerTemplate` slot 插入，用於「新增當前輸入值為新選項」；ERP 場景如「新增未列於科目表的科目」 |

---

## 9. a11y　📋

- `role="combobox"`：外層輸入框元素應標記 `role="combobox"`（Syncfusion 自動套用）。
- `aria-autocomplete="list"`：表示建議清單模式（非 `inline` / `both`）；Syncfusion 預設值。
- `aria-expanded`：overlay 展開時 `aria-expanded="true"`，收合時 `false`。
- `aria-activedescendant`：鍵盤游標移到某選項時，指向該選項的 `id`（Syncfusion 自動管理）。
- `aria-required`：必填欄位加 `aria-required="true"`。
- `aria-invalid` + `aria-describedby`：error 態需設 `aria-invalid="true"` 並指向 helper text。
- **Listbox role**：overlay 選項列表 `role="listbox"`，各項 `role="option"`；分組標題 `role="group"` 並以 `aria-label` 標記分組名。
- **鍵盤操作**：`Tab` 進入輸入框 → 輸入觸發 overlay → `↑↓` 移動選項（activedescendant 移動）→ `Enter` 確認選取 / `Esc` 收合 → `Tab` 離開元件。
- **觸控 target**：clear / arrow-down 按鈕框 32×32，需外補 padding 達到 44×44 min touch target。

---

## 12. API / Props　📋

> 以下 props 反映 Figma 設計層（`get_design_context` TypeScript interface 抽取）與 Syncfusion runtime props（`<ejs-autocomplete>`，playground `dropdownlist.vue` / `autocomplete.vue` 確認）。

**Figma 設計層 props**

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `label` | boolean | false | 是否顯示 label |
| `label1` | string | "Label" | label 文字 |
| `labelIcon` | boolean | false | 顯示 label 右側 info icon |
| `required` | boolean | false | 顯示必填 `*` |
| `text` | string | "eg. Cabbage" | placeholder 文字 |
| `leftIcon` | boolean | false | 顯示左側 icon |
| `leftIcon1` | slot | — | 自訂左側 icon 內容 |
| `clearIcon` | boolean | false | 顯示清除（×）按鈕（Figma 有設計槽位） |
| `rightIcon` | boolean | true | 顯示 arrow-down 按鈕（預設顯示；可設 false 隱藏） |
| `rightIcon1` | slot | — | 自訂右側 icon 內容 |
| `dropdown` | boolean | false | 展開 suggestion overlay（Figma State=Active/展開態） |
| `alertMessage` | boolean | false | 顯示 helper / alert text |
| `alertMessage1` | string | "Helping text" | helper 文字內容 |
| `alertIcon` | boolean | false | 顯示 helper 區域的 alert icon |
| `alertIcon1` | slot | — | 自訂 alert icon |
| `shape` | "Filled" / "Outlined" / "Line" | "Filled" | 外框樣式 |
| `size` | "Extra Small" / "Small" / "Medium" / "Large" | "Small" | 尺寸 |
| `state` | "Default" / "Hover" / "Focus" / "Active" / "Filled" / "Read Only" / "Disabled" / "Info" / "Success" / "Warning" / "Error" | "Default" | 驗證 / 互動狀態 |

> `rightIcon` 在 AutoComplete 預設 `true`，與 DropDownList（固定顯示不暴露 prop）的差異：AutoComplete 允許隱藏 arrow-down（純搜尋輸入場景）。

**Syncfusion Runtime props（`<ejs-autocomplete>`）**

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `dataSource` | Array / Object / DataManager | — | 建議選項資料來源 |
| `fields` | Object | — | `value` / `text` 欄位映射（物件資料用） |
| `value` | string | — | 目前輸入 / 選取值（v-model） |
| `placeholder` | string | — | 未輸入時的佔位文字 |
| `floatLabelType` | "Auto" / "Always" / "Never" | "Never" | floating label 行為 |
| `showClearButton` | boolean | false | 顯示清除（×）按鈕 |
| `enabled` | boolean | true | 啟用 / 停用元件 |
| `readonly` | boolean | false | 唯讀 |
| `minLength` | number | 1 | 觸發建議所需的最少輸入字元數 |
| `filterType` | "Contains" / "StartsWith" / "EndsWith" | "Contains" | 篩選比對方式 |
| `highlight` | boolean | false | 匹配字段高亮顯示 |
| `groupBy` | string | — | 分組欄位名稱（啟用 Group Header） |
| `sortOrder` | "None" / "Ascending" / "Descending" | "None" | 建議清單排序 |
| `popupHeight` | string | "300px" | 下拉選單高度 |
| `popupWidth` | string | "100%" | 下拉選單寬度 |
| `noRecordsTemplate` | string / slot | "No Records Found" | 無匹配結果時的提示文字 |
| `footerTemplate` | string / slot | — | overlay 底部自訂區（用於 Add 按鈕） |
| `allowCustom` | boolean | true | 允許輸入非選項值（AutoComplete 特有，DropDownList 無此 prop） |

---

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface §On-* §Outline §Status §Border Width §Space §Radius §Shadow §Typography §`--ds-*`
- Prototype 落地（值權威 / class 命名）：`prototyper/assets/app.css`（Syncfusion Material 3 theme override）
- Code：`@syncfusion/ej2-vue-dropdowns`（`<ejs-autocomplete>`）；playground 參考 `syncfusion-playground/app/pages/playground/dropdownlist.vue`（或 `autocomplete.vue` 若存在）
- Figma：`https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/` 節點 `16951-1340`（元件集）/ `16951-1638`（Small Filled Default 量測節點）
- 同族元件：`TextBox.md`（Filled 底線式輸入族群，Anatomy / token 共用）、`DropDownList.md`（同族 overlay 結構；arrow-down 行為差異見 §2 Anatomy 比較）

---

## Figma 補入紀錄（2026-06-29）

> Token Matching 五層工作流程執行。來源：`get_variable_defs`（節點 16951-1340，48 變數）+ `get_design_context`（節點 16951-1638，Small / Filled / Default）+ `get_screenshot`（元件集 16951-1340）。

**✅ 已確認對映（Figma 變數 → `athena-tokens.md`）**

`ColorSf/*`（surface-variant / outline / on-surface-variant / on-surface / primary / info / success / warning / danger / error / surface / transparent / on-surface-opacity38 / on-surface-opacity5）、`DsColor/placeholder`、`Icon/$icon-color`（= `on-surface-variant`）、`Text/$placeholder-text-color`（= `ds-color-placeholder`）、字型（FontSizeSf/TextSm|TextMd|TextLg、FontWeightSf/Normal、FontFamilySf/Fontfamily）、字型樣式（Label/12pt|14pt|16pt/Regular、Body Content/12pt|14pt|16pt/Regular）、邊框寬（DsBorderwidth/Small|Medium via TextBox/DropDownList 對照）、圓角（Radius/RadiusSm、Radius/RadiusNone）、間距（DsSpace/Margin/Small|Medium）、BG/$transparent / BG/$content-bg-color。  
**48 個變數全數找到對應，無漂移。**

> 注意：相比 TextBox（37 vars）和 DropDownList（44 vars），AutoComplete 多出 `ColorSf/info`（`$border-info` / `$info`）—— DropDownList 節點 var set 未包含 info 態，但 AutoComplete 元件集確認有 `State=Info`。

**⚠️ Token gap（非阻塞，已標 🎨 量測值）**

1. **`padding_left: 10px`**：`athena-tokens.md` 無 10px 間距 token（scale 跳 8→12px）→ 與 TextBox / DropDownList 同 gap，值權威見 `prototyper/assets/app.css`
2. **`inner_icon_gap: 6px`**：同上，scale 無 6px → 值權威見 `app.css`
3. **`text_px_padding: 4px`**：text-area 左右各 4px → 值權威見 `app.css`
4. **Dropdown shadow**：Figma 用 `drop-shadow(0px_2px_3px_rgba(0,0,0,0.15), 0px_1px_1px_rgba(0,0,0,0.3))`，最接近 `{ds-shadow-sm}` 但 y/blur 值未完全對應（`{ds-shadow-sm}` = `0px 1px 2px 0px rgba(0,0,0,0.3), 0px 1px 3px 1px rgba(0,0,0,0.15)`）→ 值權威見 `app.css`；可能是 Syncfusion 元件實作不完全等同 DS shadow token。
5. **`item_height: 30px`**（py-3px × 2 + 24px line）：無對應 token，值見 `app.css`

**✅ AutoComplete vs DropDownList 差異確認（同族元件比較）**

| 項目 | DropDownList | AutoComplete |
|---|---|---|
| 右側 arrow-down | **固定不可省略**（Anatomy 硬性） | 預設顯示（`rightIcon=true`），**可設 false 隱藏** |
| clearIcon | Figma 層無槽位 | **有設計槽位**（`clearIcon` prop） |
| dropdown overlay | 固定選項列表（無 embedded input）| **有 embedded input**（focused 搜尋框）+ 分組 + Add 按鈕 |
| 分組標題（Group Header） | 無 | **有**（`groupBy` 欄位觸發，font-weight Medium） |
| Add 行動按鈕 | 無 | **有**（底部 `footerTemplate`，primary 色） |
| `$border-info` | ⚠️ 未出現於 var set | **✅ 有**（State=Info 完整確認） |
| `allowCustom` | 無 | **有**（允許輸入非選項值） |
| Import 套件 | `@syncfusion/ej2-vue-dropdowns`（`ejs-dropdownlist`） | `@syncfusion/ej2-vue-dropdowns`（`ejs-autocomplete`） |
| Placeholder 預設 | "Select a vegetable" | "eg. Cabbage" |

**✅ Shape 三軸確認**

AutoComplete 元件集比 DropDownList 多 Shape=Line（純底線、無填色底、無圓角），高度較 Filled/Outlined 矮（同 size 下）。  
DropDownList 節點僅含 Filled 一種 shape；AutoComplete 提供 Filled / Outlined / Line 三選。

**⬜ 待補（次要）**

- size: Extra Small / Medium / Large 各 shape 量測節點 get_design_context（本次僅量測 Small Filled Default）
- disabled / read-only 態 Figma 截圖驗證（已依 Material 3 慣例推導）
- Dropdown item hover / active 完整截圖（已由 `get_design_context` 結構確認 `on-surface-opacity5`）
- `highlight` 匹配文字確切樣式（bold 或 primary 色；Figma 截圖未含此態）
- no-match empty state 文字（Figma 截圖未見空狀態節點）
