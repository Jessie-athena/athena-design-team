---
name: TextBox
category: 輸入 — 文字
tier: lite
status: ✅ 已採用
authority: ERP CLAUDE.md（Syncfusion TextBox, @syncfusion/ej2-vue-inputs）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16791-26050   # 🎨 FAI2 Library · TextBox 元件集
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs（37 vars, node 16791-26050/32323）+ get_design_context（node 16791-32323）
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺值一律 token-reference；raw px 量測值指向值權威 `prototyper/assets/app.css`，不在此重印。
> §3–§5 已用 Figma `get_variable_defs` + `get_design_context`（節點 16791-32323，Filled / Small / Default）校準；token gap 見文末「Figma 補入紀錄」。

## 1. 概述　📋

表單文字輸入框。採 Material 3 **Filled** 樣式（surface-variant 底色 + 底線），**不**用 Outlined（full border）。提供標籤、佔位文字、輔助文字、清除按鈕、左/右 icon 槽位，以及必填標記。

**何時使用**：需要使用者輸入單行文字的任何表單欄位（姓名、摘要、備註…）。  
**何時不用**：多行輸入 → `TextArea`；數值/金額 → `NumericTextBox`；帶選項的輸入 → `AutoComplete` / `ComboBox`。

## 2. Anatomy　🎨

> `get_design_context`（節點 16791-32323）結構樹

```
TextBox（vertical stack, gap = {ds-space-margin-small}）
├── label-row?（horizontal, gap = 2px = {ds-space-margin-extra-small}）
│   ├── label-text
│   ├── required-asterisk?（*）
│   └── label-icon?（info-circle, 11×11）
├── input-container（h = 40px，Filled 底線式）
│   ├── left-icon?（16×16, opt）
│   ├── text-area（placeholder / value）
│   ├── clear-icon-btn?（32×32, opt）
│   └── right-icon-btn?（32×32, opt）
└── helper-row?（horizontal, gap = {ds-space-margin-small}）
    ├── helper-text
    └── alert-icon?（circle-check, 10×10, opt）
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── 尺寸（size: Small）──────────────────────────────────────────────────────
height_small:         40px                                 # 🎨 量測值；Medium / Large 待另行補入
padding_left:         10px                                 # 🎨 量測值，無 {ds-space-*} token；值見 app.css
inner_icon_gap:        6px                                 # 🎨 left-icon ↔ text 量測值，無 token；值見 app.css
label_input_gap:      "{ds-space-margin-small}"            # 🔗 4px（Default）/ 8px（Comfortable）
helper_gap:           "{ds-space-margin-small}"            # 🔗 4px（Default）/ 8px（Comfortable）
icon_size:            16px                                 # 🎨 left-icon 量測值
icon_btn_size:        32px                                 # 🎨 clear / right-icon 按鈕框
icon_btn_radius:      "{ds-radius-2extra-large}"           # 🔗 16px（ripple 圓形）

# ── 容器外形（shape: Filled）────────────────────────────────────────────────
container_bg:         "{color-sf-surface-variant}"         # 🔗 rgb(237 240 247)
radius_top:           "{ds-radius-small}"                  # 🔗 4px（top-left + top-right）；解 Button.md 待確認項
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

# ── 輸入文字 ──────────────────────────────────────────────────────────────
text_color:           "{color-sf-on-surface}"              # 🔗 rgb(15 23 42)（使用者輸入值）
placeholder_color:    "{ds-color-placeholder}"             # 🔗 rgb(103 113 126)
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
font_family:          "{font-family-sf-fontfamily}"        # 🔗 Roboto（EN） / Noto Sans TC（ZH）
```

## 4. Variants　🎨🔗

> Figma 元件集（節點 16791-26050）暴露的 variant props（TypeScript interface 抽取）。

```yaml
shape:
  filled:                                              # 🎨 唯一 shape（本節點）
    bg: "{color-sf-surface-variant}"                   # 🔗 底色
    border_side: bottom only                           # 🎨 Filled 底線式

size:
  small: { height: 40px }                              # 🎨 量測確認
  # medium / large: ⬜ 未見於本 Figma 節點，待補
```

## 5. States　🎨🔗

> 底線寬度：default → `{ds-borderwidth-small}`（1px）；focus/validation → `{ds-borderwidth-medium}`（2px）。
> 各驗證態的底線色由對應 Figma 變數（`$primary` / `$border-success` / `$border-warning` / `$border-error`）確認。

```yaml
default:                                               # 🎨 node 16791-32323 量測
  container_bg: "{color-sf-surface-variant}"           # 🔗
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}"   # 🔗 1px
  text: "{color-sf-on-surface}"                        # 🔗
  placeholder: "{ds-color-placeholder}"               # 🔗
  label: "{color-sf-on-surface-variant}"               # 🔗

focus:                                                 # 🔗（Figma $primary 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-primary}"  # 🔗 2px rgb(40 119 238)
  label: "{color-sf-primary}"                          # 📋 Material 3 focus label 啟動色

success:                                               # 🎨（Figma $border-success 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-success}"  # 🔗 2px rgb(18 183 106)

warning:                                               # 🎨（Figma $border-warning 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-warning}"  # 🔗 2px rgb(247 144 9)

error:                                                 # 🎨（Figma $border-error 確認；$danger 同色）
  border_bottom: "{ds-borderwidth-medium} {color-sf-error}"    # 🔗 2px rgb(244 73 62)
  label: "{color-sf-error}"                            # 📋
  helper: "{color-sf-error}"                           # 📋

disabled:                                              # 📋（Figma 節點未含 disabled 態）
  text: "{color-sf-on-surface-variant-opacity38}"      # 🔗 38% opacity（Material 3 慣例）
  placeholder: "{color-sf-on-surface-variant-opacity38}"  # 🔗
  container_bg: "{color-sf-surface-variant}"           # 🔗（底色保留，透明度由文字體現）
```

## 9. a11y　📋

- `<label>` 透過 `for` / `id` 與 input 關聯；`required` 欄位加 `aria-required="true"`。
- Error 態需 `aria-invalid="true"` + `aria-describedby` 指向 helper text。
- Focus ring 由 Syncfusion Material 3 主題提供；鍵盤：Tab 進入、Enter / Space 不觸發提交（由父層 form 管）。
- Mobile min touch target：44×44；icon 按鈕（clear / right）已為 32×32，套 `padding` 補滿點擊區。

## 12. API / Props　📋

> 以下 props 反映 Figma 設計層（TypeScript interface 從 `get_design_context` 抽取）。Syncfusion 實際 API 詳 playground `textbox.vue` 。

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `label` | boolean | true | 是否顯示 label |
| `label1` | string | "Label" | label 文字 |
| `labelIcon` | boolean | false | 顯示 label 右側 info icon |
| `required` | boolean | false | 顯示必填 `*` |
| `text` | string | "Place Holder" | placeholder 文字 |
| `leftIcon` | boolean | false | 顯示左側 icon |
| `leftIcon1` | slot | — | 自訂左側 icon 內容 |
| `rightIcon` | boolean | false | 顯示右側 icon（預設 arrow-down） |
| `rightIcon1` | slot | — | 自訂右側 icon 內容 |
| `clearIcon` | boolean | false | 顯示清除（×）按鈕 |
| `alertMessage` | boolean | false | 顯示 helper / alert text |
| `alertMessage1` | string | "Helping text" | helper 文字內容 |
| `alertIcon` | boolean | true | 顯示 helper 區域的 alert icon |
| `alertIcon1` | slot | — | 自訂 alert icon |
| `shape` | "Filled" | "Filled" | 輸入框樣式（目前僅 Filled） |
| `size` | "Small" | "Small" | 尺寸（Large 待補） |
| `state` | "Default" / "Error" / "Success" / "Warning" | "Default" | 驗證狀態 |

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface §On-* §Outline §Status §Border Width §Space §Radius §Typography
- Prototype 落地（值權威 / class 命名）：`prototyper/assets/app.css`（Syncfusion Material 3 theme override）
- Code：`@syncfusion/ej2-vue-inputs`；playground 參考 `syncfusion-playground/app/pages/playground/textbox.vue`
- Figma：`https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/` 節點 `16791-26050`（元件集）/ `16791-32323`（Filled Small Default 量測節點）

---

## Figma 補入紀錄（2026-06-29）

> Layer 1–4 依 `figma-to-component-doc.md` Token Matching workflow 執行。來源：`get_variable_defs`（兩節點 16791-26050 / 16791-32323，共 37 變數）+ `get_design_context`（節點 16791-32323）+ `get_screenshot`（元件集 16791-26050）。

**✅ 已確認對映（Figma 變數 → `athena-tokens.md`）**：`ColorSf/*`（on-surface-variant / surface-variant / outline / on-surface / primary / success / warning / danger / error / surface / transparent）、`DsColor/placeholder`、字型三件（FontSizeSf/TextMd|Sm|Lg、FontWeightSf/Normal、FontFamilySf/Fontfamily）、間距（DsSpace/Margin/Small）、圓角（DsRadius/Small / Radius/RadiusSm）、框線寬（DsBorderwidth/Small|Medium）。**37 個變數全數找到對應，無漂移。**

**✅ 解決 Button.md 待確認項**：TextBox 頂部圓角 = Figma `Radius/RadiusSm` = 4px = `{ds-radius-small}`，底部 = 0（Filled 底線式無底圓角）。確認輸入框圓角 **4px**（與按鈕同；非舊散文 8px）。

**⚠️ Token gap（非阻塞，已標 🎨 量測值）**：
1. **`padding_left: 10px`**：`athena-tokens.md` 無 10px 間距 token（scale 跳 8→12px）→ 值權威見 `prototyper/assets/app.css`
2. **`inner_icon_gap: 6px`**：同上，scale 無 6px → 值權威見 `app.css`
3. **`Radius/RadiusNone: 0`**：無 `--ds-radius-none` token；0 為 CSS 預設值不需 token，無需補入。

**⬜ 待補（次要）**：
- size: Medium / Large 的 height 量測（本節點僅有 Small = 40px）
- disabled 態 Figma 節點驗證（截圖未清楚呈現 disabled；已依 Material 3 慣例填入 `opacity38`）
- focus label 色是否真為 `{color-sf-primary}`（截圖未含 focus；為 Material 3 filled 慣例推導）
