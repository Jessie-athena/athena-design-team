---
name: ComboBox
category: 輸入 — 選擇與輸入
tier: lite
status: ✅ 已採用
authority: ERP CLAUDE.md（Syncfusion ComboBox, @syncfusion/ej2-vue-dropdowns）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16953-47911
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs（50 vars, node 16953-47911）+ get_design_context（nodes 16953-48220 / 16953-48388）+ get_screenshot（node 16953-47911）
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺值一律 token-reference；raw px 量測值指向值權威 `prototyper/assets/app.css`，不在此重印。
> §3–§5 已用 Figma `get_variable_defs` + `get_design_context`（節點 16953-48220 Filled/Small/Default、16953-48388 Filled/Small/Filled）校準；token gap 見文末「Figma 補入紀錄」。

## 1. 概述　📋

表單選擇下拉框，**同時支援鍵盤輸入過濾選項**。採 Material 3 **Filled** 樣式（surface-variant 底色 + 底線），與 TextBox / DropDownList 同族，右側固定 arrow-down icon。

**何時使用**：使用者需從預定選項擇一，但也允許透過鍵入關鍵字快速過濾清單（如幣別、倉庫、廠商代碼等中大型清單）。  
**何時不用**：
- 純選擇（無需鍵入過濾）→ `DropDownList`
- 純自由輸入（無選項清單）→ `TextBox`
- 模糊比對且允許輸入值不在清單內（完全自由）→ `AutoComplete`
- 數值/金額 → `NumericTextBox`
- 多選 → `MultiSelect`

**與 DropDownList 的主要差異**：

| 項目 | DropDownList | ComboBox |
|---|---|---|
| 鍵盤輸入 | 不可編輯（純選） | 可輸入過濾（input role） |
| 展開時頂部搜尋框 | 無 | **有**（dropdown panel 頂端嵌入 TextBox） |
| `clearIcon` prop（Figma 層） | 無 | **有**（可選顯示清除按鈕） |
| 允許自訂值（不在清單） | 否 | 可設定（`allowCustom` Syncfusion prop） |
| `$border-warning` var set | 未出現 | **有**（50 vars 完整，含全部驗證態） |

**與 AutoComplete 的差異**：ComboBox 仍以預定選項集為主（多為精確比對），dropdown 面板顯示完整清單；AutoComplete 強調模糊搜尋建議，無固定選項集。

---

## 2. Anatomy　🎨

> `get_design_context`（節點 16953-48220，Filled / Small / Default）結構樹

```
ComboBox（vertical stack, gap = {ds-space-margin-small}）
├── label-row?（horizontal, gap = 2px = {ds-space-margin-extra-small}）
│   ├── label-text
│   ├── required-asterisk?（*）
│   └── label-icon?（circle-info, 11×11）
├── input-container（h = 40px，Filled 底線式）
│   ├── left-icon?（16×16, opt）
│   ├── text-area（placeholder / selected-value / typed-input）   ← ComboBox 可鍵入
│   ├── clear-icon-btn?（32×32, opt）                             ← ComboBox 特有
│   └── arrow-down-btn（32×32，固定）
│       └── arrow-down-icon（16×16）
└── helper-row?（horizontal, gap = {ds-space-margin-small}）
    ├── helper-text
    └── alert-icon?（circle-check, 10×10, opt）

── dropdown panel?（dropdown = true 時）
├── search-textbox（h = 40px，border-bottom 2px primary，無 label 無 helper）
├── group-header（label 字重 500, font-size {font-size-sf-text-md}）
├── menu-item（h = 24px, padding 水平 8px, 垂直 3px）
│   └── hover-item（bg = {color-sf-on-surface-opacity5}）
└── add-button（h = 32px, border-top {color-sf-outline-variant}，text = {color-sf-primary}）
```

> **與 TextBox Anatomy 差異**：
> - 右側 `arrow-down-btn` 固定（不可省略、不可替換，與 DropDownList 同）
> - 多出 `clear-icon-btn`（Figma 層 `clearIcon` prop 控制；DropDownList Figma 層無此槽位）
> - `text-area` 具 input 語意（可鍵入），而非純顯示
>
> **Dropdown panel 特徵**：頂端嵌入一個無標籤、無 helper 的 TextBox（focus 態，底線 2px primary），作為即時搜尋輸入框；底部可選「新增」按鈕（ghost 樣式，帶 plus icon）。

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── 尺寸（size: Small）──────────────────────────────────────────────────────
height_small:            40px                                 # 🎨 量測值；其他 size 見 §4
padding_left:            10px                                 # 🎨 量測值，無 {ds-space-*} token；值見 app.css
inner_icon_gap:           6px                                 # 🎨 left-icon ↔ text 量測值，無 token；值見 app.css
text_px_padding:          4px                                 # 🎨 text-area 左右各 4px；值見 app.css
label_input_gap:         "{ds-space-margin-small}"            # 🔗 4px（Default）/ 8px（Comfortable）
helper_gap:              "{ds-space-margin-small}"            # 🔗 4px（Default）/ 8px（Comfortable）
icon_size:               16px                                 # 🎨 left-icon 量測值
arrow_icon_size:         16px                                 # 🎨 arrow-down icon
arrow_btn_size:          32px                                 # 🎨 arrow-down 按鈕框（固定）
clear_btn_size:          32px                                 # 🎨 clear-icon 按鈕框（clearIcon = true）
arrow_btn_radius:        "{ds-radius-2extra-large}"           # 🔗 16px（ripple 圓形）；與 DropDownList / TextBox 一致
arrow_btn_bg:            "{color-sf-transparent}"             # 🔗 透明背景（靜態態）
clear_btn_bg:            "{color-sf-transparent}"             # 🔗 透明背景（靜態態）

# ── 容器外形（shape: Filled）────────────────────────────────────────────────
container_bg:            "{color-sf-surface-variant}"         # 🔗 rgb(237 240 247)
radius_top:              "{ds-radius-small}"                  # 🔗 4px（top-left + top-right）
radius_bottom:           0                                    # 🎨 bottom 無圓角（底線式，CSS 預設 0）
border_side:             bottom only                          # 🎨 Filled 特徵：僅底邊框
border_width_default:    "{ds-borderwidth-small}"             # 🔗 1px（default / rest 態）
border_width_active:     "{ds-borderwidth-medium}"            # 🔗 2px（focus / validation 態）
border_color_default:    "{color-sf-outline}"                 # 🔗 rgb(127 137 150)

# ── 標籤文字 ──────────────────────────────────────────────────────────────
label_color:             "{color-sf-on-surface-variant}"      # 🔗 rgb(60 74 91)
label_font_size:         "{font-size-sf-text-md}"             # 🔗 14px（Label/14pt/Regular）—— 注意 label 字級
label_font_weight:       "{font-weight-sf-normal}"            # 🔗 400
label_line_height:        1.3                                 # 🎨 Label/14pt/Regular
label_letter_spacing:     0px                                 # 🎨
required_color:          "{color-sf-danger}"                  # 🔗 rgb(244 73 62)
required_font_size:      "{font-size-sf-text-sm}"             # 🔗 12px

# ── 輸入文字（placeholder / 已選值 / 鍵入輸入）──────────────────────────
placeholder_color:       "{ds-color-placeholder}"             # 🔗 rgb(103 113 126)（Default 態顯示）
selected_value_color:    "{color-sf-on-surface}"              # 🔗 rgb(15 23 42)（Filled 態顯示，已選或鍵入後）
content_font_size:       "{font-size-sf-text-md}"             # 🔗 14px（Body Content/14pt/Regular）
content_font_weight:     "{font-weight-sf-normal}"            # 🔗 400
content_line_height:      1.5                                 # 🎨 Body Content/14pt/Regular
content_letter_spacing:   0.24px                              # 🎨 Body Content/14pt/Regular

# ── 輔助文字（helper / alert）─────────────────────────────────────────────
helper_color:            "{color-sf-on-surface-variant}"      # 🔗 rgb(60 74 91)（default）
helper_font_size:        "{font-size-sf-text-sm}"             # 🔗 12px（Label/12pt/Regular）
helper_font_weight:      "{font-weight-sf-normal}"            # 🔗 400
helper_line_height:       1.3                                 # 🎨
helper_letter_spacing:    0.1px                               # 🎨

# ── 字型 ──────────────────────────────────────────────────────────────────
font_family:             "{font-family-sf-fontfamily}"        # 🔗 Roboto（EN）/ Noto Sans TC（ZH）

# ── 圖示色 ────────────────────────────────────────────────────────────────
icon_color:              "{color-sf-on-surface-variant}"      # 🔗 rgb(60 74 91)（arrow-down 及 left-icon）

# ── Dropdown panel ──────────────────────────────────────────────────────
dropdown_bg:             "{color-sf-surface}"                 # 🔗 rgb(255 255 255)
dropdown_shadow:         "{ds-shadow-md}"                     # 🔗 2px/6px 雙層陰影（popup/menu 等級）
dropdown_radius:         "{ds-radius-small}"                  # 🔗 4px（四角）
search_border_bottom:    "{ds-borderwidth-medium} {color-sf-primary}"  # 🔗 2px rgb(40 119 238)
group_header_font_weight:"{font-weight-sf-medium}"            # 🔗 500；字色 {color-sf-on-surface}
menu_item_hover_bg:      "{color-sf-on-surface-opacity5}"     # 🔗 rgb(15 23 42 / 0.05)
add_btn_separator:       "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗 1px；color 見 gap
add_btn_text_color:      "{color-sf-primary}"                 # 🔗 rgb(40 119 238)
```

> **`color-sf-outline-variant` 補充**：Figma `drop-shadow-[0px_2px_3px_rgba(0,0,0,0.15),0px_1px_1px_rgba(0,0,0,0.3)]` 對齊 `{ds-shadow-md}`（`prototyper/assets/app.css` 值權威）。add button separator 色 `#d7dae0` = `{color-sf-outline-variant}` = `rgb(215 218 224)`，已在 athena-tokens.md §Outline 確認。

---

## 4. Variants　🎨🔗

> Figma 元件集（節點 16953-47911）暴露的 variant props，共 3 軸。

```yaml
shape:
  filled:                                                      # 🎨（本 ERP 主 shape）
    bg:          "{color-sf-surface-variant}"
    border_side: bottom only
  outlined:                                                    # 🎨 full border（非 ERP 主 shape）
    border: "{ds-borderwidth-small} {color-sf-outline}"
  line:                                                        # 🎨 underline 無背景（非 ERP 主 shape）
    bg: transparent
    border_side: bottom only

size:
  extra_small: { height: 32px }                               # 🎨 量測確認（Filled Extra Small）
  small:       { height: 40px }                               # 🎨 量測確認（Filled Small）
  medium:      { height: 48px }                               # 🎨 量測確認（Filled Medium）
  large:       { height: 56px }                               # 🎨 量測確認（Filled Large）

# ── 右側 icon 行為 ─────────────────────────────────────────────────────
arrow_down:
  static: always visible                                       # 📋 固定顯示，非 prop 控制
  icon:   material-symbols:arrow-drop-down                    # 📋 Iconify 對應
  btn_bg: "{color-sf-transparent}"                            # 🔗 靜態透明

clear_icon:
  visible_when: clearIcon = true                              # 📋 Figma prop 控制（ComboBox 特有）
  icon:   material-symbols:close                              # 📋 Iconify 對應
  btn_bg: "{color-sf-transparent}"                            # 🔗 靜態透明
```

> **ERP 實際使用 shape 為 Filled**；Outlined / Line 為 DS library 備用，ERP 通常不直接套用。

---

## 5. States　🎨🔗

> 底線寬度：default → `{ds-borderwidth-small}`（1px）；focus/validation → `{ds-borderwidth-medium}`（2px）。
> Figma var set 50 個，含完整驗證態（`$border-warning` 已確認出現，與 TextBox 對齊）。

```yaml
default:                                                       # 🎨 node 16953-48220 量測
  container_bg:  "{color-sf-surface-variant}"                  # 🔗
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}"   # 🔗 1px rgb(127 137 150)
  placeholder:   "{ds-color-placeholder}"                      # 🔗
  label:         "{color-sf-on-surface-variant}"               # 🔗
  arrow_icon:    "{color-sf-on-surface-variant}"               # 🔗

filled:                                                        # 🎨 node 16953-48388（有已選值 / 輸入值）
  container_bg:  "{color-sf-surface-variant}"                  # 🔗（容器色不變）
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}"   # 🔗（底線色不變）
  text:          "{color-sf-on-surface}"                       # 🔗 rgb(15 23 42)（已選值顯示）
  label:         "{color-sf-on-surface-variant}"               # 🔗（label 色不變）

hover:                                                         # 📋（Figma 節點含 Hover state，State layer 疊層）
  container_bg:  "{color-sf-surface-variant}"                  # 🔗（底色保留，疊層由 app.css 實作）

focus:                                                         # 🔗（Figma $primary 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-primary}"  # 🔗 2px rgb(40 119 238)
  label:         "{color-sf-primary}"                          # 📋 Material 3 focus label 啟動色

active:                                                        # 📋（dropdown 展開中；視覺同 focus）
  border_bottom: "{ds-borderwidth-medium} {color-sf-primary}"  # 🔗

read_only:                                                     # 📋（Figma State=Read Only；顯示值不可編輯）
  container_bg:  "{color-sf-surface-variant}"                  # 🔗
  text:          "{color-sf-on-surface}"                       # 🔗

info:                                                          # 🎨（Figma $info 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-info}"     # 🔗 2px rgb(46 144 250)
  label:         "{color-sf-info}"                             # 📋

success:                                                       # 🎨（Figma $border-success 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-success}"  # 🔗 2px rgb(18 183 106)

warning:                                                       # 🎨（Figma $border-warning 確認，50 vars 中已出現）
  border_bottom: "{ds-borderwidth-medium} {color-sf-warning}"  # 🔗 2px rgb(247 144 9)

error:                                                         # 🎨（Figma $border-error / $danger 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-error}"    # 🔗 2px rgb(244 73 62)
  label:         "{color-sf-error}"                            # 📋
  helper:        "{color-sf-error}"                            # 📋

disabled:                                                      # 📋（Figma State=Disabled；Material 3 慣例）
  text:          "{color-sf-on-surface-opacity38}"             # 🔗 38%（Figma Icon/$icon-color-disabled 同色）
  placeholder:   "{color-sf-on-surface-opacity38}"             # 🔗
  container_bg:  "{color-sf-surface-variant}"                  # 🔗（底色保留）
  arrow_icon:    "{color-sf-on-surface-opacity38}"             # 🔗
```

> **`info` 態**：ComboBox Figma var set 含 `$info` / `$border-info` = `{color-sf-info}`，DropDownList 節點未出現此 var；ComboBox 多出 Info 驗證態。

---

## 6. Behavior　📋

ComboBox 的核心行為與 DropDownList 的差異：

| 行為 | 說明 |
|---|---|
| 輸入過濾 | 使用者鍵入字元時，dropdown 清單即時過濾（contains / startsWith 由 `filterType` prop 控）|
| Dropdown panel 頂部搜尋框 | Figma 設計中展開後頂端有一個 focus 態的 TextBox，供鍵入搜尋；與輸入框同步 |
| 清除值 | `clearIcon = true` 時，有值狀態（Filled）顯示 ✕ 按鈕；點擊清空並還原 placeholder |
| `allowCustom` | Syncfusion prop：`true` 允許使用者輸入清單以外的值；`false`（預設）只允許選清單內項目 |
| 選項分群 | Dropdown panel 支援 group-header（`{font-weight-sf-medium}` 標籤，底線分隔）|
| 新增按鈕 | Dropdown bottom 可選「新增」按鈕（`addItem` event），用於動態擴展清單 |

ERP 情境（幣別、倉庫）通常設 `allowCustom = false`，讓 ComboBox 只作為「可輸入過濾的 DropDownList」。

---

## 9. a11y　📋

- `<label>` 透過 `for` / `id` 與 combobox 輸入框關聯；`required` 欄位加 `aria-required="true"`。
- 輸入框使用 `role="combobox"`，`aria-expanded` 反映 dropdown 開/關；`aria-autocomplete="list"` 或 `"both"` 依模式。
- Error 態需 `aria-invalid="true"` + `aria-describedby` 指向 helper text。
- Dropdown 選項列表 `role="listbox"`，各項 `role="option"`；群組頭用 `role="group"` + `aria-label`。
- 鍵盤：Tab 進入、`↓` 展開清單、`↑↓` 移動選項、`Enter` 確認、`Esc` 關閉、`Backspace` 清除字元。
- clear icon 按鈕需 `aria-label="清除"`；mobile min touch target 44×44（按鈕 32×32 需外補 padding）。
- Focus ring 由 Syncfusion Material 3 主題提供（`{ds-shadow-focus-ring1}` 依 DS shadow 規格）。

---

## 12. API / Props　📋

**Figma 設計層 props**（TypeScript interface 從 `get_design_context` 抽取）

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `label` | boolean | true | 是否顯示 label |
| `label1` | string | "Label" | label 文字 |
| `labelIcon` | boolean | false | 顯示 label 右側 info icon |
| `required` | boolean | false | 顯示必填 `*` |
| `text` | string | "Select a vegetable" | placeholder 文字 |
| `leftIcon` | boolean | false | 顯示左側 icon |
| `leftIcon1` | slot | — | 自訂左側 icon 內容 |
| `clearIcon` | boolean | false | 顯示清除（✕）按鈕（ComboBox **特有**，DropDownList Figma 層無此 prop） |
| `dropdown` | boolean | false | 顯示 dropdown panel 展開狀態 |
| `alertMessage` | boolean | false | 顯示 helper / alert text |
| `alertMessage1` | string | "Helping text" | helper 文字內容 |
| `alertIcon` | boolean | false | 顯示 helper 區域的 alert icon |
| `alertIcon1` | slot | — | 自訂 alert icon |
| `shape` | "Filled" | "Filled" | 輸入框樣式（ERP 使用 Filled） |
| `size` | "Extra Small" / "Small" / "Medium" / "Large" | "Small" | 尺寸（4 段） |
| `state` | "Default" / "Hover" / "Focus" / "Active" / "Filled" / "Read Only" / "Disabled" / "Info" / "Success" / "Warning" / "Error" | "Default" | 視覺狀態（11 態，比 DropDownList 多 Info） |

> `rightIcon` 在 TextBox 有暴露，ComboBox 與 DropDownList Figma 層均**無此 prop**：右側固定為 arrow-down + 可選 clear（不可替換 arrow）。

**Syncfusion Runtime props（`<ejs-combobox>`，playground 確認）**

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `dataSource` | Array / Object | — | 下拉選項資料來源 |
| `fields` | Object | — | 物件資料來源的 `value` / `text` 欄位映射 |
| `value` | string / number | — | 目前選取值（v-model） |
| `placeholder` | string | — | 未選取時的佔位文字 |
| `floatLabelType` | "Auto" / "Always" / "Never" | "Never" | floating label 行為 |
| `showClearButton` | boolean | false | 顯示清除（✕）按鈕（對應 Figma `clearIcon`） |
| `allowCustom` | boolean | true | 允許輸入清單以外的值（ERP 通常設 `false`） |
| `filterType` | "StartsWith" / "EndsWith" / "Contains" | "StartsWith" | 輸入過濾比對方式 |
| `enabled` | boolean | true | 啟用 / 停用元件 |
| `readonly` | boolean | false | 唯讀（對應 Figma State=Read Only） |
| `popupHeight` | string | — | 下拉選單高度（如 "200px"） |
| `popupWidth` | string | — | 下拉選單寬度（如 "300px"） |

---

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface §On-* §Outline §Status §Border Width §Space §Radius §Shadow §Typography §`--ds-*`
- 同族元件（Filled 底線式輸入族群）：
  - `TextBox.md`（基礎輸入框，Anatomy 與 token 高度共用）
  - `DropDownList.md`（純選擇，無 clearIcon，無 dropdown 頂搜尋框）
- Prototype 落地（值權威 / class 命名）：`prototyper/assets/app.css`（Syncfusion Material 3 theme override）
- Code：`@syncfusion/ej2-vue-dropdowns`；playground 參考 `syncfusion-playground/app/pages/playground/`（combobox.vue）
- Figma：`https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/` 節點 `16953-47911`（元件集）/ `16953-48220`（Filled Small Default 量測）/ `16953-48388`（Filled Small Filled 量測）

---

## Figma 補入紀錄（2026-06-29）

> Layer 1–4 依 Token Matching 五層工作流程執行。  
> 來源：`get_variable_defs`（節點 16953-47911，50 變數）+ `get_design_context`（節點 16953-48220 Default 態、16953-48388 Filled 態）+ `get_screenshot`（元件集 16953-47911）。

**✅ 已確認對映（Figma 變數 → `athena-tokens.md`）**

`ColorSf/*`（surface-variant / outline / on-surface-variant / on-surface / primary / info / success / warning / danger / error / surface / transparent / on-surface-opacity5 / on-surface-opacity38）、`DsColor/placeholder`、字型（FontSizeSf/TextSm|TextMd|TextLg、FontWeightSf/Normal、FontFamilySf/Fontfamily）、`Label/12pt|14pt/Regular`、`Body Content/12pt|14pt|16pt/Regular`、間距（DsSpace/Margin/Small）、圓角（Radius/RadiusSm、DsRadius/None）、邊框寬（DsBorderwidth/Small|Medium）。  
**50 個變數全數找到對應，無漂移。**

**✅ ComboBox vs DropDownList 變數差異確認**

| 項目 | DropDownList（44 vars） | ComboBox（50 vars） |
|---|---|---|
| `$border-warning` | ⚠️ 未出現 | ✅ 出現（`$warning` / `$border-warning`） |
| `$info` / `$border-info` | ⚠️ 未出現 | ✅ 出現（Info 驗證態） |
| `ColorSf/on-surface-opacity5` | ⬜ 未確認 | ✅ 出現（menu-item hover bg） |
| `ColorSf/outline-variant` | ⬜ 未確認 | ✅ 出現（add-button separator） |
| `FontWeightSf/Medium` | ⬜ 未確認 | ✅ 出現（group-header 字重） |

**✅ Dropdown panel 設計特徵確認**

Figma `dropdown = true` 展開狀態含：
1. 頂端 search TextBox（border-bottom 2px `{color-sf-primary}`，與 focus 態同）
2. Group header（`{font-weight-sf-medium}`，底部 divider）
3. 選項行 hover bg（`{color-sf-on-surface-opacity5}`）
4. 底部「新增」按鈕（border-top `{color-sf-outline-variant}`，text `{color-sf-primary}`,  icon plus）
5. Dropdown shadow 對齊 `{ds-shadow-md}`

**⚠️ Token gap（非阻塞，已標 🎨 量測值）**

1. **`padding_left: 10px`**：`athena-tokens.md` 無 10px 間距 token（scale 跳 8→12px）→ 與 TextBox / DropDownList 同 gap，值權威見 `prototyper/assets/app.css`
2. **`inner_icon_gap: 6px`**：同上，scale 無 6px → 值權威見 `app.css`
3. **`text_px_padding: 4px`**：text-area 左右各 4px → 值權威見 `app.css`

**⬜ 待補（次要）**

- size: Extra Small / Medium / Large 的詳細內部 padding 量測（本次只量測 Filled Small）
- hover 態 state-layer 確切疊層色（Figma Hover 節點未深入 get_design_context，依 Material 3 慣例推導）
- `allowCustom = true` 時 Figma 是否有對應視覺狀態（custom value 與 list value 顯示一致）
- focus label 色是否有截圖驗證（截圖未含 focus；為 Material 3 filled 慣例推導，與 TextBox / DropDownList 一致）
