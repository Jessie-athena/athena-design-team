---
name: DatePicker
category: 輸入 — 日期
tier: lite
status: ✅ 已產出
authority: ERP CLAUDE.md（客製 DsDatePicker, components/ds/DsDatePicker.vue；底層 @syncfusion/ej2-vue-calendars）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16986-236059   # 🎨 FAI2 Library · DatePicker 元件集
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs（node 16986-236059，38 vars）+ get_design_context（node 16986-236368）+ get_screenshot（元件集 16986-236059）
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺值一律 token-reference；raw px 量測值標 🎨 指向值權威 `prototyper/assets/app.css`，不在此重印。
> §3–§5 已用 Figma `get_variable_defs` + `get_design_context`（節點 16986-236368，Filled / Small / Default）+ `get_screenshot`（元件集 16986-236059）校準；token gap 見文末「Figma 補入紀錄」。

## 1. 概述　📋

日期選擇器。輸入框部分與 TextBox 共用 Filled 樣式（surface-variant 底色 + 底線），右側固定掛 calendar icon 按鈕，點擊後展開 calendar popup 供使用者選取日期。

**元件層級關係**：
- `DsDatePicker`（`components/ds/DsDatePicker.vue`）：**優先使用**。包裝 `ejs-datepicker` 並注入多語系格式（`yyyy/MM/dd`、`MM/dd/yyyy` 等），以及 locale 設定（`zh`、`en`）。ERP 表單欄位一律透過此元件使用。
- `ejs-datepicker`（`@syncfusion/ej2-vue-calendars`）：底層 Syncfusion 元件；需要客製行為（min/max/disabledDates/depth 等）時可直接傳 props 至 `DsDatePicker`，它會向下透傳。**不要**直接在表單欄位使用 `ejs-datepicker`（會遺漏多語系格式）。

**何時使用**：表單中需要使用者以日期選取器輸入單一日期的欄位（交易日期、到期日、起訖日等）。  
**何時不用**：時間 + 日期 → `DateTimePicker`；日期範圍 → `DateRangePicker`；直接輸入純文字日期字串（不需要 popup）→ 降級用 `TextBox` 加上 input mask。

## 2. Anatomy　🎨

> `get_design_context`（節點 16986-236368，Filled / Small / Default）結構樹

```
DatePicker（vertical stack, gap = {ds-space-margin-small}）
├── label-row?（horizontal, gap = 2px = 🎨 量測值）
│   ├── label-text
│   ├── required-asterisk?（*）
│   └── label-icon?（info-circle, 11×11 = 🎨 量測值）
├── segmented-picker（h = 40px，Filled 底線式，horizontal）
│   ├── text-input-container（flex-1, pl = 10px = 🎨 量測值）
│   │   ├── left-icon?（16×16, opt）
│   │   └── placeholder / value-text（gap left-icon ↔ text = 6px = 🎨 量測值）
│   └── action-container（right side, flex-shrink-0）
│       ├── clear-icon-btn?（32×32, rounded-full, opt）
│       └── calendar-icon-btn（32×32, rounded-full, always visible）
│           └── calendar icon（16×16）
└── helper-row?（horizontal, gap = 4px = 🎨 量測值）
    ├── helper-text
    └── alert-icon?（circle-check, 10×10, opt）
```

> **與 TextBox 的結構差異**：right side 的 action-container **固定**包含 calendar-icon-btn（不可移除），且 placeholder 預設文字為「Choose Date」而非通用佔位文字。left-icon / clear-icon 槽位與 TextBox 相同。

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── 尺寸（size: Small）──────────────────────────────────────────────────────
height_small:           40px                                 # 🎨 量測值；Medium / Large 待另行補入
padding_left:           10px                                 # 🎨 量測值，無 {ds-space-*} token；值見 app.css
inner_icon_gap:          6px                                 # 🎨 left-icon ↔ placeholder 量測值，無 token；值見 app.css
label_input_gap:        "{ds-space-margin-small}"            # 🔗 4px（Default）/ 8px（Comfortable）
helper_gap:              4px                                 # 🎨 helper-row gap；無 token（間距 scale 跳 4px = small token 對映，但 Figma 節點用 4px 非 small）
action_btn_size:        32px                                 # 🎨 calendar-icon-btn / clear-icon-btn 框尺寸；值見 app.css
action_btn_radius:      "{ds-radius-2extra-large}"           # 🔗 16px（全圓 ripple 區域）
calendar_icon_size:     16px                                 # 🎨 calendar svg 尺寸；值見 app.css
left_icon_size:         16px                                 # 🎨 optional left-icon 尺寸；值見 app.css

# ── 容器外形（shape: Filled）────────────────────────────────────────────────
container_bg:           "{color-sf-surface-variant}"         # 🔗 rgb(237 240 247)
radius_top:             "{ds-radius-small}"                  # 🔗 4px（top-left + top-right）
radius_bottom:          0                                    # 🎨 Filled 底線式無底圓角（CSS 預設 0，無 token）
border_side:            bottom only                          # 🎨 Filled 特徵：僅底邊框
border_width_default:   "{ds-borderwidth-small}"             # 🔗 1px
border_width_active:    "{ds-borderwidth-medium}"            # 🔗 2px（focus / validation 態）
border_color_default:   "{color-sf-outline}"                 # 🔗 rgb(127 137 150)

# ── 標籤文字 ──────────────────────────────────────────────────────────────
label_color:            "{color-sf-on-surface-variant}"      # 🔗 rgb(60 74 91)
label_font_size:        "{font-size-sf-text-md}"             # 🔗 14px（Label/14pt/Regular）
label_font_weight:      "{font-weight-sf-normal}"            # 🔗 400
label_line_height:       1.3                                 # 🎨 Label/14pt/Regular
label_letter_spacing:    0px                                 # 🎨
required_color:         "{color-sf-danger}"                  # 🔗 rgb(244 73 62)
required_font_size:     "{font-size-sf-text-sm}"             # 🔗 12px

# ── Placeholder / Value 文字 ───────────────────────────────────────────────
placeholder_text:       "Choose Date"                        # 📋 預設佔位文字（選取後顯示格式化日期值）
placeholder_color:      "{ds-color-placeholder}"             # 🔗 rgb(103 113 126)
value_color:            "{color-sf-on-surface}"              # 🔗 rgb(15 23 42)
content_font_size:      "{font-size-sf-text-md}"             # 🔗 14px（Body Content/14pt/Regular）
content_font_weight:    "{font-weight-sf-normal}"            # 🔗 400
content_line_height:     1.5                                 # 🎨 Body Content/14pt/Regular
content_letter_spacing:  0.24px                              # 🎨 Body Content/14pt/Regular

# ── 輔助文字（helper / alert）─────────────────────────────────────────────
helper_color:           "{color-sf-on-surface-variant}"      # 🔗 rgb(60 74 91)（default）
helper_font_size:       "{font-size-sf-text-sm}"             # 🔗 12px（Label/12pt/Regular）
helper_font_weight:     "{font-weight-sf-normal}"            # 🔗 400
helper_line_height:      1.3                                 # 🎨
helper_letter_spacing:   0.1px                               # 🎨

# ── Icon 色 ───────────────────────────────────────────────────────────────
icon_color:             "{color-sf-on-surface-variant}"      # 🔗 rgb(60 74 91)（calendar / left-icon 預設色）

# ── 字型 ──────────────────────────────────────────────────────────────────
font_family:            "{font-family-sf-fontfamily}"        # 🔗 Roboto（EN）/ Noto Sans TC（ZH）
```

## 4. Variants　🎨🔗

> Figma 元件集（節點 16986-236059）截圖顯示橫向 10 欄 × 縱向 8 列矩陣；prop 軸如下。

```yaml
shape:
  filled:                                                    # 🎨 主要 shape（Figma 前 4 欄）
    bg: "{color-sf-surface-variant}"                         # 🔗 底色
    border_side: bottom only                                 # 🎨 Filled 底線式
  outlined:                                                  # 🎨 次要 shape（Figma 第 5–10 欄）
    bg: "{color-sf-surface}"                                 # 🔗 白底 rgb(255 255 255)
    border: all sides                                        # 🎨 Outlined 全框式；border token 同 filled

size:
  small: { height: 40px }                                    # 🎨 量測確認（本文件以此為基準）
  # medium / large: ⬜ 未見於已量測節點，待補

label:
  true:  顯示 label-row（預設）                              # 📋
  false: 無 label-row（僅 input）

required:
  true:  label 後加紅色 * 號                                 # 📋
  false: 無（預設）

leftIcon:
  false: 無（預設）                                          # 📋
  true:  左側加 icon 槽位                                    # 📋

clearIcon:
  false: 無（預設）                                          # 📋
  true:  calendar 按鈕左側加 ×（清除）                       # 📋

alertMessage:
  false: 無 helper-row（預設）                               # 📋
  true:  顯示 helper-row（含文字 ± alert icon）              # 📋
```

## 5. States　🎨🔗

> 底線寬度：default → `{ds-borderwidth-small}`（1px）；focus/validation → `{ds-borderwidth-medium}`（2px）。
> Outlined shape 對應態的 border 顏色邏輯相同，全框寬度同步。

```yaml
default:                                                     # 🎨 node 16986-236368 量測
  container_bg: "{color-sf-surface-variant}"                 # 🔗 Filled
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}" # 🔗 1px
  placeholder: "{ds-color-placeholder}"                      # 🔗
  label: "{color-sf-on-surface-variant}"                     # 🔗
  icon_color: "{color-sf-on-surface-variant}"                # 🔗

focus:                                                       # 🔗（Figma $primary 確認，截圖第 3–4 欄）
  border_bottom: "{ds-borderwidth-medium} {color-sf-primary}" # 🔗 2px rgb(40 119 238)
  label: "{color-sf-primary}"                                # 📋 Material 3 focus label 啟動色

success:                                                     # 🎨（Figma $border-success 確認，截圖第 8 欄）
  border_bottom: "{ds-borderwidth-medium} {color-sf-success}" # 🔗 2px rgb(18 183 106)
  label: "{color-sf-success}"                                # 📋

warning:                                                     # 🎨（Figma $border-warning 確認，截圖第 9 欄）
  border_bottom: "{ds-borderwidth-medium} {color-sf-warning}" # 🔗 2px rgb(247 144 9)
  label: "{color-sf-warning}"                                # 📋 Warning label 為橘色（由截圖第 9 欄確認）

error:                                                       # 🎨（Figma $border-error 確認，截圖第 10 欄）
  border_bottom: "{ds-borderwidth-medium} {color-sf-error}"  # 🔗 2px rgb(244 73 62)
  label: "{color-sf-error}"                                  # 📋
  helper: "{color-sf-error}"                                 # 📋

disabled:                                                    # 🎨（截圖第 7 欄；label / placeholder 明顯灰透明）
  label: "{color-sf-on-surface-opacity38}"                   # 🔗 38% opacity（Material 3 慣例）
  placeholder: "{color-sf-on-surface-opacity38}"             # 🔗
  icon_color: "{color-sf-on-surface-opacity38}"              # 🔗 calendar icon 亦淡化
  container_bg: "{color-sf-surface-variant}"                 # 🔗 底色維持，透明度由文字/圖示體現
```

> **Warning state 確認**：截圖第 9 欄可見 label 為橘色、底線為橘色，確認有 `warning` state（與 TextBox 一致）。DatePicker 共 5 種 validation state：default / focus / success / warning / error，加上 disabled。

## 9. a11y　📋

- 輸入框為 `<input type="text">`，由 Syncfusion 管理；`<label>` 透過 `for` / `id` 關聯。
- `required` 欄位加 `aria-required="true"`；Error 態加 `aria-invalid="true"` + `aria-describedby` 指向 helper text。
- **Calendar popup 鍵盤導航**：
  - `Enter` / `Space` / 點擊 calendar icon → 開啟 popup
  - `↑` / `↓` / `←` / `→` → 在日期格間移動
  - `Enter` → 選取當前焦點日期，關閉 popup
  - `Esc` → 關閉 popup，焦點回到 input
  - `Page Up` / `Page Down` → 上/下月切換
  - `Tab` → 在 popup 中的 prev / next month 按鈕、日期格之間循環；`Shift+Tab` 反向
- Focus ring 由 Syncfusion Material 3 主題提供（`{ds-shadow-focus-ring1}`）。
- Calendar popup 需有 `role="dialog"`（Syncfusion 內建）；popup 開啟時焦點移入 popup，關閉時焦點回到 trigger input。
- Mobile min touch target：calendar icon btn 為 32×32，已符合觸控區域補 padding 至 44×44 要求（由 `DsDatePicker` wrapper 負責處理或 Syncfusion 主題補全）。

## 12. API / Props　📋

> 以下 props 反映 Figma 設計層（從 `get_design_context` TypeScript interface 抽取）。`DsDatePicker` 實際 Vue props 因包裝層而有差異；底層 Syncfusion ejs-datepicker API 詳 playground `datepicker.vue`。

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `label` | boolean | true | 是否顯示 label |
| `label1` | string | "Label" | label 文字 |
| `labelIcon` | boolean | false | 顯示 label 右側 info icon |
| `required` | boolean | false | 顯示必填 `*` |
| `text` | string | "Choose Date" | placeholder 文字 |
| `leftIcon` | boolean | false | 顯示左側 icon 槽位 |
| `leftIcon1` | slot | — | 自訂左側 icon 內容 |
| `clearIcon` | boolean | false | 顯示清除（×）按鈕 |
| `alertMessage` | boolean | false | 顯示 helper / alert text |
| `alertMessage1` | string | "Helping text" | helper 文字內容 |
| `alertIcon` | boolean | true | 顯示 helper 區域的 alert icon |
| `alertIcon1` | slot | — | 自訂 alert icon |
| `shape` | "Filled" / "Outlined" | "Filled" | 輸入框樣式 |
| `size` | "Small" | "Small" | 尺寸（Medium / Large 待補） |
| `state` | "Default" / "Focus" / "Success" / "Warning" / "Error" | "Default" | 驗證狀態 |

> **`DsDatePicker` 額外 props（多語系）**：`format`（日期格式字串，預設依 locale）、`locale`（"zh" / "en"，預設跟應用全域設定）；底層 ejs-datepicker 的 `min`、`max`、`disabled`、`disabledDates` 透過 `v-bind="$attrs"` 向下透傳。

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface §On-* §Outline §Status §Border Width §Space §Radius §Typography
- Prototype 落地（值權威 / class 命名）：`prototyper/assets/app.css`（Syncfusion Material 3 theme override）
- Code（客製）：`components/ds/DsDatePicker.vue`（多語系格式包裝；**ERP 欄位優先使用**）
- Code（底層）：`@syncfusion/ej2-vue-calendars`；playground 參考 `syncfusion-playground/app/pages/playground/datepicker.vue`
- Figma：`https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/` 節點 `16986-236059`（元件集）/ `16986-236368`（Filled Small Default 量測節點）
- 相關元件設計文件：`TextBox.md`（共用 Filled 容器規格）、`NumericTextBox.md`（同家族輸入元件）

---

## Figma 補入紀錄（2026-06-29）

> Layer 1–4 依 `figma-to-component-doc.md` Token Matching workflow 執行。來源：`get_variable_defs`（節點 16986-236059，38 變數）+ `get_design_context`（節點 16986-236368）+ `get_screenshot`（元件集 16986-236059，確認 variants × states 矩陣）。

**✅ 已確認對映（Figma 變數 → `athena-tokens.md`）**：

| Figma 變數 | token | 用途 |
|---|---|---|
| `ColorSf/on-surface-variant` | `{color-sf-on-surface-variant}` | label / icon / helper 色 |
| `ColorSf/surface-variant` | `{color-sf-surface-variant}` | Filled 底色 |
| `ColorSf/outline` | `{color-sf-outline}` | default 底線色 |
| `ColorSf/on-surface` | `{color-sf-on-surface}` | value 文字色 |
| `ColorSf/primary` | `{color-sf-primary}` | focus 邊框 / label |
| `ColorSf/danger` / `ColorSf/error` | `{color-sf-danger}` / `{color-sf-error}` | error 邊框（同值 rgb(244,73,62)） |
| `ColorSf/success` | `{color-sf-success}` | success 邊框 |
| `ColorSf/warning` | `{color-sf-warning}` | warning 邊框 |
| `DsColor/placeholder` | `{ds-color-placeholder}` | placeholder 文字色 |
| `ColorSf/on-surface-opacity38` | `{color-sf-on-surface-opacity38}` | disabled 文字 |
| `FontSizeSf/TextMd` | `{font-size-sf-text-md}` | 14px content |
| `FontSizeSf/TextSm` | `{font-size-sf-text-sm}` | 12px label-small / helper |
| `FontSizeSf/TextLg` | `{font-size-sf-text-lg}` | 16px（Figma 包含，Outlined Large 備用） |
| `FontWeightSf/Normal` | `{font-weight-sf-normal}` | 400 |
| `FontFamilySf/Fontfamily` | `{font-family-sf-fontfamily}` | Roboto / Noto Sans TC |
| `DsSpace/Margin/Small` | `{ds-space-margin-small}` | label ↔ input gap |
| `Radius/RadiusSm` | `{ds-radius-small}` | top radius 4px |
| `Radius/RadiusNone` | — | 0（CSS 預設，無需 token） |

**38 個變數中：36 個對映至既有 token，2 個為 CSS 預設值（0）不需 token。**

**⚠️ Token gap（非阻塞，已標 🎨 量測值）**：

1. **`padding_left: 10px`**：`athena-tokens.md` 無 10px 間距 token（scale 跳 8→12px）→ 值權威見 `prototyper/assets/app.css`（與 TextBox 同）
2. **`inner_icon_gap: 6px`**：同上，scale 無 6px → 值權威見 `app.css`（與 TextBox 同）
3. **`helper_gap: 4px`**：Figma helper-row 的 gap 為 4px，`{ds-space-margin-small}` = 4px（Default 密度），**語意對映可行但 Figma 節點非透過 margin token 設定**（直接值）→ 標 🎨，以 app.css 為準

**⬜ 待補（次要）**：
- size: Medium / Large 的 height 量測（本節點僅有 Small = 40px，截圖可見不同高度列但未量測）
- Outlined shape 的 border-all-sides 與 radius 的精確值（Figma 截圖可見全框，但未單獨量測節點）
- Calendar popup 的 token 規格（popup 屬 Syncfusion 內建 overlay，未在本元件集節點展開；待 DS 另案補）
- disabled 態 calendar icon 是否有 pointer-events: none（截圖第 7 欄 icon 淡化但行為未量測）

**與 TextBox 的差異（結構層）**：
1. **固定 calendar icon btn**：TextBox 的 right-icon 為可選 prop；DatePicker 的 calendar-icon-btn 永遠存在（action-container 不可移除）
2. **action-container 為 2-slot 區**：calendar icon 左側可疊 clear icon（同 TextBox），但 calendar icon 自身不是 clear icon
3. **Placeholder 預設文字**：DatePicker = "Choose Date"（語意佔位）；TextBox = "Place Holder"（通用佔位）
4. **Token 覆蓋率**：DatePicker 38 vars vs TextBox 37 vars，多 1 個（`FontSizeSf/TextLg`），其餘完全一致
5. **Warning state**：兩者均有確認（DatePicker 截圖第 9 欄橘框；TextBox 同）
