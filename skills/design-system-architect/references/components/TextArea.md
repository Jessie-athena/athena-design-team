---
name: TextArea
category: 輸入 — 文字
tier: lite
status: ✅ 已產出
authority: ERP CLAUDE.md（Syncfusion TextArea, @syncfusion/ej2-vue-inputs）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16806-1291   # 🎨 FAI2 Library · TextArea 元件集
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs（node 16806-1291，30 vars）+ get_design_context（node 16806-1591）
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺值一律 token-reference；raw px 量測值指向值權威 `prototyper/assets/app.css`，不在此重印。
> §3–§5 已用 Figma `get_variable_defs` + `get_design_context`（節點 16806-1591，Filled / Small / Default）校準；token gap 見文末「Figma 補入紀錄」。

## 1. 概述　📋

表單多行文字輸入框。採 Material 3 **Filled** 樣式（surface-variant 底色 + 底線），與 TextBox 共用視覺語言。提供標籤、佔位文字、輔助文字、字元計數器、resize handle。

**何時使用**：需要使用者輸入多行自由文字的表單欄位（備註、說明、摘要、留言…）。  
**何時不用**：單行文字 → `TextBox`；數值 / 金額 → `NumericTextBox`；帶選項輸入 → `AutoComplete` / `ComboBox`。

## 2. Anatomy　🎨

> `get_design_context`（節點 16806-1591）結構樹

```
TextArea（vertical stack, gap = {ds-space-margin-small}）
├── label-row?（horizontal, gap = 2px = 同 TextBox）
│   ├── label-text
│   ├── required-asterisk?（*）
│   └── label-icon?（info-circle, 11×11）
├── textarea-container（min-h 可充填；Filled 底線式）
│   ├── text-content（flex-1；placeholder / value）
│   └── resizer?（右下角 8×8 resize handle；預設顯示）
└── message-row?（horizontal, gap = 40px；helper 左 + counter 右）
    ├── helper-container?（helper-text + alert-icon?）
    └── character-counter?（「0/200」right-aligned，預設隱藏）
```

**與 TextBox Anatomy 的差異：**
- **無** `clear-icon-btn`（TextBox 有；TextArea 不含）
- **無** `right-icon-btn`（TextBox 有；TextArea 不含）
- **新增** `resizer` 右下角 resize handle（TextBox 無）
- **新增** `character-counter` 右側字元計數（TextBox 無）
- `message-row` 有 40px gap，左右分別放 helper 與 counter

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── 尺寸（size: Small）──────────────────────────────────────────────────────
min_height:           100px                                # 🎨 Figma 外容器量測值（含 label）；textarea 本身 flex-grow 充填
padding_left:         12px                                 # 🎨 量測值（TextBox = 10px，TextArea 多 2px）；無 token；值見 app.css
padding_top:          8px                                  # 🎨 量測值；{ds-space-margin-medium} 為 8px Default 可對應
inner_gap:            "{ds-space-margin-medium}"           # 🔗 8px（Default）／ text ↔ resizer 水平 gap
label_input_gap:      "{ds-space-margin-small}"            # 🔗 4px（Default）/ 8px（Comfortable）— 同 TextBox
helper_gap:           "{ds-space-margin-small}"            # 🔗 同 TextBox
resizer_size:         8px                                  # 🎨 resize handle 量測值，無 token；值見 app.css
resizer_padding_br:   4px                                  # 🎨 右下留白；值見 app.css
helper_counter_gap:   40px                                 # 🎨 helper ↔ character-counter 水平 gap；無 token；值見 app.css

# ── 容器外形（shape: Filled）────────────────────────────────────────────────
container_bg:         "{color-sf-surface-variant}"         # 🔗 rgb(237 240 247)  — 同 TextBox
radius_top:           "{ds-radius-small}"                  # 🔗 4px（top-left + top-right）— 同 TextBox
radius_bottom:        0                                    # 🎨 bottom 無圓角（Filled 底線式，CSS 預設 0，無 token）
border_side:          bottom only                          # 🎨 Filled 特徵：僅底邊框 — 同 TextBox
border_width_default: "{ds-borderwidth-small}"             # 🔗 1px（default / rest 態）— 同 TextBox
border_width_active:  "{ds-borderwidth-medium}"            # 🔗 2px（focus / validation 態）— 同 TextBox
border_color_default: "{color-sf-outline}"                 # 🔗 rgb(127 137 150)  — 同 TextBox

# ── 標籤文字 ─────────────────────────────────────────────────────────────────
# 以下同 TextBox，以「同 TextBox」標注，不重印值
label_color:          "{color-sf-on-surface-variant}"      # 🔗 同 TextBox
label_font_size:      "{font-size-sf-text-md}"             # 🔗 同 TextBox
label_font_weight:    "{font-weight-sf-normal}"            # 🔗 同 TextBox
label_line_height:     1.3                                 # 🎨 同 TextBox
label_letter_spacing:  0px                                 # 🎨 同 TextBox
required_color:       "{color-sf-danger}"                  # 🔗 同 TextBox
required_font_size:   "{font-size-sf-text-sm}"             # 🔗 同 TextBox

# ── 輸入文字 ─────────────────────────────────────────────────────────────────
text_color:           "{color-sf-on-surface}"              # 🔗 同 TextBox
placeholder_color:    "{ds-color-placeholder}"             # 🔗 同 TextBox
content_font_size:    "{font-size-sf-text-md}"             # 🔗 同 TextBox（Body Content/14pt/Regular）
content_font_weight:  "{font-weight-sf-normal}"            # 🔗 同 TextBox
content_line_height:   1.3                                 # 🎨 TextArea 取 Label/14pt/Regular；TextBox = 1.5（差異點）

# ── 輔助文字（helper）────────────────────────────────────────────────────────
helper_color:         "{color-sf-on-surface-variant}"      # 🔗 同 TextBox
helper_font_size:     "{font-size-sf-text-sm}"             # 🔗 同 TextBox（12px）
helper_font_weight:   "{font-weight-sf-normal}"            # 🔗 同 TextBox
helper_line_height:    1.3                                 # 🎨 同 TextBox
helper_letter_spacing: 0.1px                               # 🎨 同 TextBox

# ── 字元計數器（characterCounter）────────────────────────────────────────────
# 與 helper text 同字級，右對齊
counter_color:        "{color-sf-on-surface-variant}"      # 🔗 同 helper；rgb(60 74 91)
counter_font_size:    "{font-size-sf-text-sm}"             # 🔗 12px
counter_font_weight:  "{font-weight-sf-normal}"            # 🔗 400
counter_line_height:   1.3                                 # 🎨 Label/12pt/Regular
counter_letter_spacing: 0.1px                              # 🎨
counter_align:        right                                # 📋 靠右對齊

# ── 字型 ─────────────────────────────────────────────────────────────────────
font_family:          "{font-family-sf-fontfamily}"        # 🔗 Roboto（EN） / Noto Sans TC（ZH）— 同 TextBox
```

## 4. Variants　🎨🔗

> Figma 元件集（節點 16806-1291）暴露的 variant props。

```yaml
shape:
  filled:                                              # 🎨 唯一 shape（同 TextBox）
    bg: "{color-sf-surface-variant}"
    border_side: bottom only

size:
  small: { min_height: 100px }                         # 🎨 量測確認（含 label 整體容器高）
  # medium / large: ⬜ 未見於本 Figma 節點，待補

resize:
  resizer: true / false                                # 🎨 Figma prop；true = 右下角顯示 resize handle
  # Syncfusion resizeMode（Vertical/Horizontal/Both/None）在 code 層控制，Figma 以 resizer boolean 為 toggle
```

## 5. States　🎨🔗

> 同 TextBox states 結構；底線寬 default → `{ds-borderwidth-small}`（1px），focus/validation → `{ds-borderwidth-medium}`（2px）。
> Figma 節點 16806-1591 為 Default state；其餘 state 由 Figma 變數確認（與 TextBox 相同）。

```yaml
default:                                               # 🎨 node 16806-1591 量測（同 TextBox）
  container_bg: "{color-sf-surface-variant}"           # 🔗
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}"   # 🔗 1px
  text: "{color-sf-on-surface}"                        # 🔗
  placeholder: "{ds-color-placeholder}"                # 🔗
  label: "{color-sf-on-surface-variant}"               # 🔗

focus:                                                 # 🔗（Figma $primary；同 TextBox）
  border_bottom: "{ds-borderwidth-medium} {color-sf-primary}"  # 🔗 2px rgb(40 119 238)
  label: "{color-sf-primary}"                          # 📋 Material 3 focus label 啟動色

success:                                               # 🎨（Figma $warning 同系；同 TextBox）
  border_bottom: "{ds-borderwidth-medium} {color-sf-success}"  # 🔗 2px（同 TextBox）

warning:                                               # 🎨（Figma $warning；同 TextBox）
  border_bottom: "{ds-borderwidth-medium} {color-sf-warning}"  # 🔗 2px rgb(247 144 9)

error:                                                 # 🎨（Figma $danger；同 TextBox）
  border_bottom: "{ds-borderwidth-medium} {color-sf-error}"    # 🔗 2px rgb(244 73 62)
  label: "{color-sf-error}"                            # 📋
  helper: "{color-sf-error}"                           # 📋

disabled:                                              # 📋（Figma 節點未含 disabled 態；同 TextBox Material 3 慣例）
  text: "{color-sf-on-surface-variant-opacity38}"      # 🔗 38% opacity
  placeholder: "{color-sf-on-surface-variant-opacity38}"  # 🔗
  container_bg: "{color-sf-surface-variant}"           # 🔗（底色保留，透明度由文字體現）
```

## 9. a11y　📋

- `<textarea>` 使用 `aria-multiline="true"`（screen reader 識別多行輸入）
- `<label>` 透過 `for` / `id` 關聯；`required` 欄位加 `aria-required="true"`
- Error 態需 `aria-invalid="true"` + `aria-describedby` 指向 helper text
- **字元計數器**：當 `characterCounter` 開啟且使用者輸入時，計數器文字需 `aria-live="polite"` 通知 screen reader；以免每個字都觸發（選 `polite` 非 `assertive`）
- Focus ring 由 Syncfusion Material 3 主題提供
- 鍵盤：Tab 進入，Enter / Shift+Enter 插入換行（不觸發表單提交）；Esc 無特定行為
- resize handle 純視覺，不影響 tab order；鍵盤使用者以 cursor + drag 之外的方式控制高度時，應由父層 form 給定 `rows` 預設值

## 12. API / Props　📋

> 以下 props 反映 Figma 設計層（TypeScript interface 從 `get_design_context` 抽取）。Syncfusion 實際 API 詳 playground `textarea.vue`。

**Figma 設計層 Props（元件視覺控制）：**

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `label` | boolean | true | 是否顯示 label |
| `label1` | string | "Label" | label 文字 |
| `labelIcon` | boolean | false | 顯示 label 右側 info icon |
| `required` | boolean | false | 顯示必填 `*` |
| `text` | string | "Write a message..." | placeholder 文字 |
| `resizer` | boolean | true | 顯示右下角 resize handle（**TextArea 特有，TextBox 無**） |
| `message` | boolean | false | 是否顯示 message row（helper + counter 共用容器） |
| `alertMessage` | boolean | false | 顯示 helper text（需 `message=true`） |
| `alertMessage1` | string | "Helping text" | helper 文字內容 |
| `alertIcon` | boolean | true | 顯示 helper 區域的 alert icon |
| `characterCounter` | boolean | false | 顯示字元計數器（**TextArea 特有，TextBox 無**；需 `message=true`） |
| `shape` | "Filled" | "Filled" | 輸入框樣式（目前僅 Filled） |
| `size` | "Small" | "Small" | 尺寸（Large 待補） |
| `state` | "Default" / "Error" / "Success" / "Warning" | "Default" | 驗證狀態 |

**Syncfusion `<ejs-textarea>` 額外 Props（code 層行為控制）：**

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `rows` | number | — | 可見行數（控制預設高度） |
| `cols` | number | — | 欄位寬度（以字元數計） |
| `maxLength` | number | — | 最大字元數限制（搭配 characterCounter 使用） |
| `resizeMode` | "Vertical" / "Horizontal" / "Both" / "None" | — | 縮放方向（Figma `resizer=true` 對應 `Vertical`） |
| `floatLabelType` | "Never" / "Auto" / "Always" | "Never" | 浮動 label 模式 |
| `readonly` | boolean | false | 唯讀（不可編輯） |
| `enabled` | boolean | true | 啟用 / 停用 |

> ERP 慣例：多行備註欄通常設 `rows="4"` + `resizeMode="Vertical"` + `floatLabelType="Auto"`。

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface §On-* §Outline §Status §Border Width §Space §Radius §Typography
- 同族參考：`./TextBox.md`（共用 Filled 底線式容器結構；TextArea 差異見 §2）
- Prototype 落地（值權威 / class 命名）：`prototyper/assets/app.css`（Syncfusion Material 3 theme override）
- Code：`@syncfusion/ej2-vue-inputs`（`TextAreaComponent as EjsTextarea`）；playground 參考 `syncfusion-playground/app/pages/playground/textarea.vue`
- Figma：`https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/` 節點 `16806-1291`（元件集）/ `16806-1591`（Filled Small Default 量測節點）

---

## Figma 補入紀錄（2026-06-29）

> Layer 1–4 依 `figma-to-component-doc.md` Token Matching workflow 執行。來源：`get_variable_defs`（節點 16806-1291，30 變數）+ `get_design_context`（節點 16806-1591）。

**✅ 已確認對映（Figma 變數 → `athena-tokens.md`）**：
與 TextBox.md 完全相同的 30 個變數全數找到對應，無漂移：`ColorSf/on-surface-variant`、`FontSizeSf/TextMd|Sm|Lg`、`FontWeightSf/Normal`、`FontFamilySf/Fontfamily`、`DsColor/placeholder`、`ColorSf/outline`、`DsSpace/Margin/Small`、`Radius/RadiusSm|None`、`ColorSf/surface-variant`、`ColorSf/on-surface`、`ColorSf/primary`、`ColorSf/warning`、`ColorSf/danger`、`ColorSf/surface`。

**TextArea vs TextBox 主要視覺差異（Layer 4 發現）**：
1. **`padding_left: 12px`**（TextBox = 10px）→ 🎨 量測值，值見 `app.css`
2. **`padding_top: 8px`**（TextBox 無獨立 top padding）→ 🎨 量測值；`{ds-space-margin-medium}` = 8px Default 可對應，但 token 與 px scale 非完全對齊
3. **`content_line_height: 1.3`**（TextArea 用 `Label/14pt/Regular`）vs TextBox = 1.5（`Body Content/14pt/Regular`）→ 差異點，兩者 font-size 同為 14px
4. **`characterCounter`**：Figma 確認有此 prop，字型 = `Label/12pt/Regular`（12px / 0.1px tracking），與 helper text 完全相同 token
5. **`message-row gap: 40px`**：helper ↔ counter 之間無 token，`athena-tokens.md` 無 40px → 🎨 量測值，值見 `app.css`

**⚠️ Token gap（非阻塞，已標 🎨 量測值）**：
1. **`padding_left: 12px`**：scale 跳 8→16px，無 12px token → 值權威見 `prototyper/assets/app.css`
2. **`padding_top: 8px`**：`{ds-space-margin-medium}` = 8px Default，但 Comfortable = 12px；若需精確 token 對應，8px Default 可用 `{ds-space-margin-medium}`，需確認語意是否對
3. **`helper_counter_gap: 40px`**：scale 無 40px → 值權威見 `app.css`
4. **`resizer_size: 8px`**：無 token → 值權威見 `app.css`

**⬜ 待補（次要）**：
- size: Medium / Large 的 height 量測（本節點僅有 Small 外容器高 100px）
- disabled 態 Figma 節點驗證（依 Material 3 慣例填入 `opacity38`）
- focus 態 Figma screenshot 確認（label 色是否為 `{color-sf-primary}`）
- `resizeMode` Syncfusion prop 與 Figma `resizer` boolean 的 1:1 對應確認（推測 `resizer=true` ↔ `resizeMode="Vertical"`；待 code 驗證）
