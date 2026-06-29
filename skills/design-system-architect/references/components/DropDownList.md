---
name: DropDownList
category: 輸入 — 選擇
tier: lite
status: ✅ 已採用
authority: ERP CLAUDE.md（Syncfusion DropDownList, @syncfusion/ej2-vue-dropdowns）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16953-165772   # 🎨 FAI2 Library · DropDownList 元件集
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs（44 vars, node 16953-165772）+ get_design_context（node 16953-166081）+ get_screenshot（node 16953-165772）
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺值一律 token-reference；raw px 量測值指向值權威 `prototyper/assets/app.css`，不在此重印。
> §3–§5 已用 Figma `get_variable_defs` + `get_design_context`（節點 16953-166081，Filled / Small / Default）校準；token gap 見文末「Figma 補入紀錄」。

## 1. 概述　📋

表單選擇下拉框。採 Material 3 **Filled** 樣式（surface-variant 底色 + 底線），與 TextBox 同族，右側固定 arrow-down icon 表示可展開。

**何時使用**：需要使用者從預定選項中擇一的表單欄位（部門、幣別、狀態、類型…）。  
**何時不用**：自由文字輸入 → `TextBox`；數值/金額 → `NumericTextBox`；多選 → `MultiSelect`；帶輸入搜尋過濾 → `ComboBox` / `AutoComplete`。

---

## 2. Anatomy　🎨

> `get_design_context`（節點 16953-166081）結構樹

```
DropDownList（vertical stack, gap = {ds-space-margin-small}）
├── label-row?（horizontal, gap = 2px = {ds-space-margin-extra-small}）
│   ├── label-text
│   ├── required-asterisk?（*）
│   └── label-icon?（circle-info, 11×11）
├── input-container（h = 40px，Filled 底線式）
│   ├── left-icon?（16×16, opt）
│   ├── text-area（placeholder / selected-value）
│   └── arrow-down-btn（32×32，固定，不可省略）
│       └── arrow-down-icon（16×16）
└── helper-row?（horizontal, gap = {ds-space-margin-small}）
    ├── helper-text
    └── alert-icon?（circle-check, 10×10, opt）
```

> **與 TextBox 的 Anatomy 差異**：右側槽位固定為 `arrow-down-btn`（**不可省略**，非可選 rightIcon）；無 `clearIcon` 設計槽位（Syncfusion 運行時可用 `showClearButton`，但 Figma 層未暴露）。

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── 尺寸（size: Small）──────────────────────────────────────────────────────
height_small:         40px                                 # 🎨 量測值；Medium / Large 待另行補入
padding_left:         10px                                 # 🎨 量測值，無 {ds-space-*} token；值見 app.css
inner_icon_gap:        6px                                 # 🎨 left-icon ↔ text 量測值，無 token；值見 app.css
text_px_padding:       4px                                 # 🎨 text-area 左右各 4px；值見 app.css
label_input_gap:      "{ds-space-margin-small}"            # 🔗 4px（Default）/ 8px（Comfortable）
helper_gap:           "{ds-space-margin-small}"            # 🔗 4px（Default）/ 8px（Comfortable）
icon_size:            16px                                 # 🎨 left-icon 量測值
arrow_icon_size:      16px                                 # 🎨 arrow-down icon 量測值
arrow_btn_size:       32px                                 # 🎨 arrow-down 按鈕框（固定）
arrow_btn_radius:     "{ds-radius-2extra-large}"           # 🔗 16px（ripple 圓形）；與 TextBox icon-btn 一致
arrow_btn_bg:         "{color-sf-transparent}"             # 🔗 透明背景（靜態態）

# ── 容器外形（shape: Filled）────────────────────────────────────────────────
container_bg:         "{color-sf-surface-variant}"         # 🔗 rgb(237 240 247)
radius_top:           "{ds-radius-small}"                  # 🔗 4px（top-left + top-right）
radius_bottom:        0                                    # 🎨 bottom 無圓角（底線式，CSS 預設 0，無 token）
border_side:          bottom only                          # 🎨 Filled 特徵：僅底邊框
border_width_default: "{ds-borderwidth-small}"             # 🔗 1px（default / rest 態）
border_width_active:  "{ds-borderwidth-medium}"            # 🔗 2px（focus / validation 態）
border_color_default: "{color-sf-outline}"                 # 🔗 rgb(127 137 150)

# ── 標籤文字 ──────────────────────────────────────────────────────────────
label_color:          "{color-sf-on-surface-variant}"      # 🔗 rgb(60 74 91)
label_font_size:      "{font-size-sf-text-md}"             # 🔗 14px（Label/14pt/Regular）
label_font_weight:    "{font-weight-sf-normal}"            # 🔗 400
label_line_height:     1.3                                 # 🎨 Label/14pt/Regular
label_letter_spacing:  0px                                 # 🎨
required_color:       "{color-sf-danger}"                  # 🔗 rgb(244 73 62)
required_font_size:   "{font-size-sf-text-sm}"             # 🔗 12px

# ── 輸入文字（placeholder / 已選值）──────────────────────────────────────
placeholder_color:    "{ds-color-placeholder}"             # 🔗 rgb(103 113 126)（未選態顯示）
selected_value_color: "{color-sf-on-surface}"              # 🔗 rgb(15 23 42)（選取後顯示）
content_font_size:    "{font-size-sf-text-md}"             # 🔗 14px（Body Content/14pt/Regular）
content_font_weight:  "{font-weight-sf-normal}"            # 🔗 400
content_line_height:   1.5                                 # 🎨 Body Content/14pt/Regular
content_letter_spacing: 0.24px                             # 🎨 Body Content/14pt/Regular

# ── 輔助文字（helper / alert）─────────────────────────────────────────────
helper_color:         "{color-sf-on-surface-variant}"      # 🔗 rgb(60 74 91)（default）
helper_font_size:     "{font-size-sf-text-sm}"             # 🔗 12px（Label/12pt/Regular）
helper_font_weight:   "{font-weight-sf-normal}"            # 🔗 400
helper_line_height:    1.3                                 # 🎨
helper_letter_spacing: 0.1px                               # 🎨

# ── 字型 ──────────────────────────────────────────────────────────────────
font_family:          "{font-family-sf-fontfamily}"        # 🔗 Roboto（EN）/ Noto Sans TC（ZH）

# ── 圖示色 ────────────────────────────────────────────────────────────────
icon_color:           "{color-sf-on-surface-variant}"      # 🔗 rgb(60 74 91)（arrow-down 及 left-icon）
```

---

## 4. Variants　🎨🔗

> Figma 元件集（節點 16953-165772）暴露的 variant props（TypeScript interface 抽取）。

```yaml
shape:
  filled:                                              # 🎨 唯一 shape（本節點）
    bg: "{color-sf-surface-variant}"                   # 🔗 底色
    border_side: bottom only                           # 🎨 Filled 底線式

size:
  small: { height: 40px }                              # 🎨 量測確認
  # medium / large: ⬜ 未見於本 Figma 節點，待補

# ── 右側 icon 固定行為 ────────────────────────────────────────────────────
arrow_down:
  static: always visible                               # 📋 固定顯示，非 prop 控制
  icon: arrow-down（material-symbols:arrow-drop-down） # 📋 Iconify 對應
  btn_bg:  "{color-sf-transparent}"                   # 🔗 靜態態透明
```

---

## 5. States　🎨🔗

> 底線寬度：default → `{ds-borderwidth-small}`（1px）；focus/validation → `{ds-borderwidth-medium}`（2px）。
> 各驗證態的底線色由 Figma 變數（`$primary` / `$border-success` / `$border-error`）確認；`$border-warning` 未出現於本節點 var set（見文末 token gap）。

```yaml
default:                                               # 🎨 node 16953-166081 量測
  container_bg: "{color-sf-surface-variant}"           # 🔗
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}"   # 🔗 1px rgb(127 137 150)
  placeholder: "{ds-color-placeholder}"               # 🔗
  label: "{color-sf-on-surface-variant}"               # 🔗
  arrow_icon: "{color-sf-on-surface-variant}"          # 🔗

focus:                                                 # 🔗（Figma $primary 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-primary}"  # 🔗 2px rgb(40 119 238)
  label: "{color-sf-primary}"                          # 📋 Material 3 focus label 啟動色

success:                                               # 🎨（Figma $border-success 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-success}"  # 🔗 2px rgb(18 183 106)

warning:                                               # ⚠️ $border-warning 未出現於本節點 var set
  border_bottom: "{ds-borderwidth-medium} {color-sf-warning}"  # 🔗 2px rgb(247 144 9)（對齊 TextBox 推導）

error:                                                 # 🎨（Figma $border-error / $danger 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-error}"    # 🔗 2px rgb(244 73 62)
  label: "{color-sf-error}"                            # 📋
  helper: "{color-sf-error}"                           # 📋

disabled:                                              # 📋（Figma 節點含 disabled icon 色變數，整體態依 Material 3）
  text: "{color-sf-on-surface-opacity38}"              # 🔗 38% opacity（Material 3 慣例；Figma Icon/$icon-color-disabled 同值）
  placeholder: "{color-sf-on-surface-opacity38}"       # 🔗
  container_bg: "{color-sf-surface-variant}"           # 🔗（底色保留，透明度由文字體現）
  arrow_icon: "{color-sf-on-surface-opacity38}"        # 🔗（與 Icon/$icon-color-disabled 對齊）
```

---

## 9. a11y　📋

- `<label>` 透過 `for` / `id` 與 select 關聯；`required` 欄位加 `aria-required="true"`。
- Error 態需 `aria-invalid="true"` + `aria-describedby` 指向 helper text。
- Dropdown 展開時需 `aria-expanded="true"`；選項列表用 `role="listbox"`，各項 `role="option"`。
- 鍵盤：Tab 進入、`Enter` / `Space` 展開、`↑↓` 移動選項、`Esc` 關閉、`Tab` / `Enter` 確認選取。
- Focus ring 由 Syncfusion Material 3 主題提供；mobile min touch target：44×44（arrow-down 按鈕 32×32 需外補 padding）。

---

## 12. API / Props　📋

> 以下 props 反映 Figma 設計層（TypeScript interface 從 `get_design_context` 抽取）與 Syncfusion runtime props（`dropdownlist.vue` playground 確認）。

**Figma 設計層 props**

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `label` | boolean | true | 是否顯示 label |
| `label1` | string | "Label" | label 文字 |
| `labelIcon` | boolean | false | 顯示 label 右側 info icon |
| `required` | boolean | false | 顯示必填 `*` |
| `text` | string | "Select a vegetable" | placeholder 文字 |
| `leftIcon` | boolean | false | 顯示左側 icon |
| `leftIcon1` | slot | — | 自訂左側 icon 內容 |
| `alertMessage` | boolean | false | 顯示 helper / alert text |
| `alertMessage1` | string | "Helping text" | helper 文字內容 |
| `alertIcon` | boolean | true | 顯示 helper 區域的 alert icon |
| `alertIcon1` | slot | — | 自訂 alert icon |
| `shape` | "Filled" | "Filled" | 輸入框樣式（目前僅 Filled） |
| `size` | "Small" | "Small" | 尺寸 |
| `state` | "Default" | "Default" | 驗證狀態（Figma 層僅 Default；runtime 見下） |

> `rightIcon` / `clearIcon` 在 TextBox 有暴露，DropDownList Figma 層**無此 prop**：右側固定為 arrow-down（不可替換）；clearIcon 於 Syncfusion runtime 用 `showClearButton`。

**Syncfusion Runtime props（`<ejs-dropdownlist>`，playground 確認）**

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `dataSource` | Array / Object | — | 下拉選項資料來源 |
| `fields` | Object | — | 物件資料來源的 `value` / `text` 欄位映射 |
| `value` | string / number | — | 目前選取值（v-model） |
| `placeholder` | string | — | 未選取時的佔位文字 |
| `floatLabelType` | "Auto" / "Always" / "Never" | "Never" | floating label 行為 |
| `showClearButton` | boolean | false | 顯示清除（×）按鈕 |
| `enabled` | boolean | true | 啟用 / 停用元件 |
| `readonly` | boolean | false | 唯讀（顯示值但不可編輯） |
| `popupHeight` | string | — | 下拉選單高度（如 "200px"） |
| `popupWidth` | string | — | 下拉選單寬度（如 "300px"） |

---

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface §On-* §Outline §Status §Border Width §Space §Radius §Typography §`--ds-*`
- Prototype 落地（值權威 / class 命名）：`prototyper/assets/app.css`（Syncfusion Material 3 theme override）
- Code：`@syncfusion/ej2-vue-dropdowns`；playground 參考 `syncfusion-playground/app/pages/playground/dropdownlist.vue`
- Figma：`https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/` 節點 `16953-165772`（元件集）/ `16953-166081`（Filled Small Default 量測節點）
- 同族元件：`TextBox.md`（Filled 底線式輸入族群，Anatomy 與 token 高度共用）

---

## Figma 補入紀錄（2026-06-29）

> Layer 1–4 依 Token Matching 五層工作流程執行。來源：`get_variable_defs`（節點 16953-165772，44 變數）+ `get_design_context`（節點 16953-166081）+ `get_screenshot`（元件集 16953-165772）。

**✅ 已確認對映（Figma 變數 → `athena-tokens.md`）**

`ColorSf/*`（surface-variant / outline / on-surface-variant / on-surface / primary / success / danger / error / surface / transparent / on-surface-opacity38）、`DsColor/placeholder`、字型三件（FontSizeSf/TextMd|Sm|Lg、FontWeightSf/Normal、FontFamilySf/Fontfamily）、`Label/14pt/Regular`、`Body Content/14pt/Regular`、`Label/12pt/Regular`、間距（DsSpace/Margin/Small）、圓角（Radius/RadiusSm、DsRadius/None）、邊框寬（DsBorderwidth/Small|Medium via TextBox 對照）。  
**44 個變數全數找到對應，無漂移。**

**⚠️ Token gap（非阻塞，已標 🎨 量測值）**

1. **`padding_left: 10px`**：`athena-tokens.md` 無 10px 間距 token（scale 跳 8→12px）→ 與 TextBox 同 gap，值權威見 `prototyper/assets/app.css`
2. **`inner_icon_gap: 6px`**：同上，scale 無 6px → 值權威見 `app.css`
3. **`text_px_padding: 4px`**：text-area 左右各 4px（等同 `{ds-space-padding-small}`，但 Figma 層為 inline padding 非 margin token 用途）→ 值權威見 `app.css`
4. **`$border-warning` 未出現於本節點 var set**：DropDownList warning 態底線色依 TextBox 與 `{color-sf-warning}` 對齊推導，待 Figma 正式補入驗證

**✅ 與 TextBox 差異確認（同族元件比較）**

| 項目 | TextBox | DropDownList |
|---|---|---|
| 右側槽 | 可選 rightIcon（`boolean`） | **固定** arrow-down btn（不可省略、不可替換） |
| clearIcon | 有（Figma prop + Syncfusion `showClearButton`） | Figma 層無；Syncfusion runtime 有 `showClearButton` |
| Placeholder 預設 | "Place Holder" | "Select a vegetable" |
| Import 套件 | `@syncfusion/ej2-vue-inputs` | `@syncfusion/ej2-vue-dropdowns` |
| `$border-warning` in var set | ✅ 有 | ⚠️ 未出現（見上方 gap 4） |
| Anatomy 差異 | clearIcon btn 槽 | 無 clearIcon 槽 |

**⬜ 待補（次要）**

- size: Medium / Large 的 height 量測（本節點僅有 Small = 40px）
- disabled 態 Figma 節點視覺截圖驗證（已依 Material 3 慣例 + `Icon/$icon-color-disabled` 推導）
- warning 態 `$border-warning` Figma 變數確認（補入後更新 §5）
- focus label 色是否真為 `{color-sf-primary}`（截圖未含 focus；為 Material 3 filled 慣例推導，與 TextBox 一致）
