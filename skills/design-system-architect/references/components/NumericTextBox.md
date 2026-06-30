---
name: NumericTextBox
category: 輸入 — 文字
tier: lite
status: ✅ 已產出
authority: ERP CLAUDE.md（Syncfusion NumericTextBox, @syncfusion/ej2-vue-inputs）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16812-4625   # 🎨 FAI2 Library · Numeric TextBox 元件集
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs（node 16812-4625）+ get_design_context（node 16812-4925）
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺值一律 token-reference；raw px 量測值指向值權威 `prototyper/assets/app.css`，不在此重印。
> §3–§5 已用 Figma `get_variable_defs` + `get_design_context`（節點 16812-4925，Filled / Small / Default）校準。
> 與 TextBox 共用底層容器結構；相同的 token 不重複列舉，以 **「同 TextBox」** 標注，差異處完整展開。

## 1. 概述　📋

數值輸入框。右側固定一組 up / down spinner 箭頭（`numericArrow: "Left & Right"`）。用於金額、數量、序號等數值型欄位。

**何時使用**：需要輸入整數或小數、有上下界限制、或需要 ±1 步進的數值欄位（金額、數量、百分比）。  
**何時不用**：自由文字 → `TextBox`；選項有限 → `DropDownList`；日期 → `DatePicker`。

## 2. Anatomy　🎨

> `get_design_context`（節點 16812-4925）結構樹

```
NumericTextBox（vertical stack）
├── label-row?（horizontal, gap = {ds-space-margin-extra-small} = 2px）
│   ├── paragraph-container（label-text + required-asterisk? 同行）
│   └── label-icon?（info-circle, 11×11）
├── input-container（h = 40px，Filled 底線式；同 TextBox 容器）
│   ├── left-icon?（16×16, lock 預設，opt）
│   ├── text-area（numericValue / placeholder）
│   └── spinner-controls（右側固定）
│       ├── down-arrow-btn（32×32）
│       └── up-arrow-btn（32×32）
└── helper-row?（h = 20px，帶 28px spinner spacer）
    ├── helper-text（pl = {ds-space-padding-large} = 12px）
    └── alert-icon?（circle-check, 10×10, opt）
```

> **與 TextBox anatomy 的差異**：
> - label-row：label-text + asterisk 合為 `paragraph-container`（同 DOM 節點）；TextBox 為兩個獨立 `<p>`
> - 右側控制區：spinner up/down 取代 TextBox 的 clear + right-icon
> - helper-row：加 `pl-[12px]` 縮排 + 28px 右側 spacer 對齊 spinner 區域

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── 尺寸（size: Small）──────────────────────────────────────────────────────
height_small:          40px                               # 🎨 量測值；同 TextBox
padding_left:          10px                               # 🎨 量測值，無 {ds-space-*} token；值見 app.css
inner_icon_gap:        10px                               # 🎨 left-icon ↔ text 量測值（TextBox 為 6px），無 token；值見 app.css
label_input_gap:       # 🎨 設計稿未見明確 gap；TextBox = {ds-space-margin-small}，待確認
icon_size:             16px                               # 🎨 left-icon
spinner_btn_size:      32px                               # 🎨 up / down 按鈕框（同 TextBox icon-btn）
spinner_btn_radius:    "{ds-radius-2extra-large}"         # 🔗 16px（ripple 圓形）
helper_row_height:     20px                               # 🎨 量測值
helper_padding_left:   "{ds-space-padding-large}"         # 🔗 12px（Default）；對齊 spinner spacer
helper_padding_top:    "{ds-space-padding-extra-small}"   # 🔗 4px（Default）

# ── 容器外形（shape: Filled）── 同 TextBox ──────────────────────────────────
container_bg:          "{color-sf-surface-variant}"       # 🔗 rgb(237 240 247)；同 TextBox
radius_top:            "{ds-radius-small}"                # 🔗 4px（top-left + top-right）；同 TextBox
radius_bottom:         0                                  # 🎨 底無圓角；同 TextBox
border_side:           bottom only                        # 🎨 Filled 底線式；同 TextBox
border_width_default:  "{ds-borderwidth-small}"           # 🔗 1px；同 TextBox
border_width_active:   "{ds-borderwidth-medium}"          # 🔗 2px（focus / validation）；同 TextBox
border_color_default:  "{color-sf-outline}"               # 🔗 rgb(127 137 150)；同 TextBox

# ── 標籤文字 ⚠️ 與 TextBox 不同 ────────────────────────────────────────────
label_color:           "{color-sf-on-surface-variant}"    # 🔗 rgb(60 74 91)；同 TextBox
label_font_size:       "{font-size-sf-text-sm}"           # 🔗 12px（Label/12pt/Regular）← TextBox 為 14px
label_font_weight:     "{font-weight-sf-normal}"          # 🔗 400
label_line_height:      1.3                               # 🎨 Label/12pt/Regular
label_letter_spacing:   0.1px                             # 🎨 Label/12pt/Regular（TextBox label = 0px）
required_color:        "{color-sf-danger}"                # 🔗 rgb(244 73 62)；同 TextBox

# ── Icon 色彩（spinner 箭頭 / left icon）────────────────────────────────────
icon_color:            "{color-sf-on-surface-variant}"    # 🔗 rgb(60 74 91)（$icon-color = #3C4A5B）
icon_color_disabled:   "{color-sf-on-surface}"            # 🔗 base（opacity 由 disabled 態疊加）

# ── 輸入文字 / 佔位文字 ── 同 TextBox ──────────────────────────────────────
text_color:            "{color-sf-on-surface}"            # 🔗 rgb(15 23 42)；同 TextBox
placeholder_color:     "{ds-color-placeholder}"           # 🔗 rgb(103 113 126)；同 TextBox
content_font_size:     "{font-size-sf-text-md}"           # 🔗 14px；同 TextBox
content_font_weight:   "{font-weight-sf-normal}"          # 🔗 400；同 TextBox
content_line_height:    1.5                               # 🎨；同 TextBox
content_letter_spacing: 0.24px                            # 🎨；同 TextBox

# ── 輔助文字 ── 同 TextBox ───────────────────────────────────────────────
helper_color:          "{color-sf-on-surface-variant}"    # 🔗；同 TextBox
helper_font_size:      "{font-size-sf-text-sm}"           # 🔗 12px；同 TextBox
helper_font_weight:    "{font-weight-sf-normal}"          # 🔗 400；同 TextBox
helper_line_height:     1.3                               # 🎨；同 TextBox
helper_letter_spacing:  0.1px                             # 🎨；同 TextBox

font_family:           "{font-family-sf-fontfamily}"      # 🔗 Roboto（EN）/ Noto Sans TC（ZH）；同 TextBox
```

## 4. Variants　🎨🔗

> Figma 元件集（節點 16812-4625）props：

```yaml
shape:
  filled:                                              # 🎨 唯一 shape（同 TextBox）
    bg: "{color-sf-surface-variant}"
    border_side: bottom only

size:
  small: { height: 40px }                              # 🎨 量測確認
  # medium / large: ⬜ 未見於本 Figma 節點，待補

numericArrow:
  left_and_right: { right_controls: [down-arrow-btn, up-arrow-btn] }  # 🎨 唯一 variant
  # 其他箭頭模式（如 right-only）待確認是否存在
```

## 5. States　🎨🔗

> **重要差異**：NumericTextBox 變數集**無 `$warning` / `$border-warning`**，僅支援 success / error / danger；已明確含 `ColorSf/on-surface-opacity38` disabled 色。

```yaml
default:                                               # 🎨 node 16812-4925 量測
  container_bg: "{color-sf-surface-variant}"           # 🔗；同 TextBox
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}"   # 🔗 1px；同 TextBox
  text: "{color-sf-on-surface}"                        # 🔗；同 TextBox
  placeholder: "{ds-color-placeholder}"                # 🔗；同 TextBox
  label: "{color-sf-on-surface-variant}"               # 🔗；同 TextBox
  icon: "{color-sf-on-surface-variant}"                # 🔗 spinner / left-icon 色

focus:                                                 # 🔗（$primary / Primary_btn/$primary-border-color 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-primary}"  # 🔗 2px rgb(40 119 238)；同 TextBox
  label: "{color-sf-primary}"                          # 📋 Material 3 focus label

success:                                               # 🎨（$border-success 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-success}"  # 🔗 2px；同 TextBox

error:                                                 # 🎨（$border-error 確認；$danger 同色）
  border_bottom: "{ds-borderwidth-medium} {color-sf-error}"    # 🔗 2px；同 TextBox
  label: "{color-sf-error}"                            # 📋；同 TextBox
  helper: "{color-sf-error}"                           # 📋；同 TextBox

# warning: ⚠️ Figma 變數集未含 $warning / $border-warning，NumericTextBox 不支援 warning 狀態
# （TextBox 有；兩元件狀態集不同）

disabled:                                              # 🎨（ColorSf/on-surface-opacity38 顯式確認）
  text: "{color-sf-on-surface-opacity38}"              # 🔗 rgb(15 23 42 / 0.38)← TextBox 推論，此處 Figma 明確
  placeholder: "{color-sf-on-surface-opacity38}"       # 🔗
  icon: "{color-sf-on-surface-opacity38}"              # 🔗（$icon-color-disabled 底色 + opacity token 疊）
  container_bg: "{color-sf-surface-variant}"           # 🔗（保留底色，不換色）
```

## 9. a11y　📋

- 與 TextBox 相同：`<label>` 與 input 關聯；`aria-required` / `aria-invalid` / `aria-describedby`。
- 數值 spinner：up / down 按鈕需各自 `aria-label`（「增加」/ 「減少」）；鍵盤 ↑ / ↓ 步進。
- 步進量（min / max / step）需明示於 `title` 或視覺 hint，避免僅用 placeholder 傳達。
- Mobile：spinner 按鈕 32×32（低於 44px 建議），可考慮加 padding 補點擊區。

## 12. API / Props　📋

> 以下 props 反映 Figma 設計層（TypeScript interface 從 `get_design_context` 抽取）。Syncfusion 實際 API 詳 playground `numerictextbox.vue`。

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `label` | boolean | true | 是否顯示 label |
| `label1` | string | "Label" | label 文字 |
| `labelIcon` | boolean | false | 顯示 label 右側 info icon |
| `required` | boolean | false | 顯示必填 `*` |
| `numericValue` | string | "00" | 顯示的數值（placeholder 值） |
| `numericArrow` | "Left & Right" | "Left & Right" | spinner 模式（目前僅此一種） |
| `leftIcon` | boolean | true | 顯示左側 icon（預設 lock） |
| `leftIcon1` | slot | — | 自訂左側 icon |
| `alertMessage` | boolean | false | 顯示 helper / alert text |
| `alertMessage1` | string | "Helping text" | helper 文字 |
| `alertIcon` | boolean | false | 顯示 helper 區域 alert icon（預設 false，與 TextBox 不同） |
| `alertIcon1` | slot | — | 自訂 alert icon |
| `shape` | "Filled" | "Filled" | 輸入框樣式（目前僅 Filled） |
| `size` | "Small" | "Small" | 尺寸（Large 待補） |
| `state` | "Default" / "Error" / "Success" | "Default" | 驗證狀態（無 Warning） |

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface §On-* §Outline §Status §Border Width §Space §Radius §Typography
- 容器/底線/佔位文字 token 對齊：見 `components/TextBox.md`（共用底層 Filled 容器）
- Prototype 落地（值權威 / class 命名）：`prototyper/assets/app.css`
- Code：`@syncfusion/ej2-vue-inputs`；playground 參考 `syncfusion-playground/app/pages/playground/numerictextbox.vue`
- Figma：`https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/` 節點 `16812-4625`（元件集）/ `16812-4925`（Filled Small Default 量測節點）

---

## Figma 補入紀錄（2026-06-29）

**✅ 已確認對映（Figma 變數 → `athena-tokens.md`）**：色彩、字型、圓角、框線寬全數命中（與 TextBox 高度重疊）。額外確認：`ColorSf/on-surface-opacity38`（disabled 色明確出現，非推論）、`Icon/$icon-color` → `{color-sf-on-surface-variant}`、`Primary_btn/$primary-border-color` → `{color-sf-primary}`。

**⚠️ 與 TextBox 的差異點（設計文件標注）**：
1. **Label 12px vs TextBox 14px**：NumericTextBox 標籤用 `{font-size-sf-text-sm}` 12px（Label/12pt）；TextBox 用 `{font-size-sf-text-md}` 14px（Label/14pt）。
2. **無 warning 驗證態**：Figma 變數集中不存在 `$warning` / `$border-warning`；元件僅支援 default / focus / success / error / danger。
3. **icon↔text inner gap 10px**（TextBox = 6px）：同樣無對應 token，值見 app.css。
4. **helper row 結構**：加 `pl-[12px]`（`{ds-space-padding-large}` ✅）+ 28px spacer 對齊 spinner 控制區。

**⚠️ token gap（非阻塞）**：
1. `padding_left: 10px` → 同 TextBox，無 token，見 app.css
2. `inner_icon_gap: 10px` → 同類，無 token，見 app.css
3. `BG/$transparent = #FFFBFE`：Material 3 白（非純 #FFFFFF），但僅以 `opacity: 0` 使用，視覺無差；對應 `{color-sf-transparent}` 語意一致。

**⬜ 待補**：size Medium / Large 量測；numericArrow 其他模式是否存在。
