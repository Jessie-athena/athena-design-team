---
name: DateTimePicker
category: 輸入 — 日期
tier: lite
status: ✅ 已產出
authority: ERP CLAUDE.md（Syncfusion DateTimePicker, @syncfusion/ej2-vue-calendars）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16989-139036
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs（node 16989-139036，50 vars）+ get_metadata（同節點，variant 軸確認）+ get_design_context（node 16989-139345，Small/Default/Filled）+ get_screenshot（元件集 16989-139036）
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺值一律 token-reference；raw px 量測值標 🎨 指向值權威 `prototyper/assets/app.css`，不在此重印。
> §3–§5 已用 Figma `get_variable_defs` + `get_design_context`（節點 16989-139345，Filled / Small / Default）校準；token gap 見文末「Figma 補入紀錄」。

---

## 1. 概述　📋

日期時間選擇器。觸發框樣式與 `DatePicker` / `TextBox` 同族（Material 3 Filled 底線式），右側固定掛 **calendar icon**（展開日期面板）與 **clock icon**（展開時間面板）兩個 action button。點擊後由 Syncfusion 提供的組合 popup 供使用者選取日期與時間。

**何時使用**：表單中需要同時輸入**日期 + 時間**的欄位（如排程時間、預約記錄、系統事件時戳）。  
**何時不用**：
- 僅需日期 → `DatePicker`（`DsDatePicker`）  
- 僅需時間 → `TimePicker`  
- 日期範圍 → `DateRangePicker`  
- 純文字日期輸入（無 popup）→ `TextBox` + input mask

---

## 2. Anatomy　🎨

> 來源：`get_design_context`（節點 16989-139345，Small / Default / Filled）

```
DateTimePicker（vertical stack, gap = {ds-space-margin-small}）
├── label-row?（horizontal, gap = 2px = 🎨 量測值）
│   ├── label-text
│   ├── required-asterisk?（*）
│   └── label-icon?（info-circle, 11×11 = 🎨 量測值）
├── segmented-picker（h = 40px，Filled 底線式，horizontal）
│   ├── text-input-container（flex-1, pl = 10px = 🎨 量測值）
│   │   ├── left-icon?（16×16, opt）
│   │   └── placeholder / value-text（gap left-icon ↔ text = 6px = 🎨 量測值）
│   └── action-container（right side, flex-shrink-0）
│       ├── clear-icon-btn?（32×32, rounded-full, opt；clearIcon prop 控制）
│       ├── calendar-icon-btn（32×32, rounded-full, **固定**；展開日期面板）
│       │   └── timeline-today icon（16×16，material-symbols:calendar-today）
│       └── clock-icon-btn（32×32, rounded-full, **固定**；展開時間面板）
│           └── schedule icon（16×16，material-symbols:schedule）
└── helper-row?（horizontal, gap = {ds-space-margin-small}）
    ├── helper-text
    └── alert-icon?（circle-check, 10×10, opt）
```

> **與 DatePicker 的 Anatomy 差異**：
> - `action-container` 有**兩個**固定 icon button（calendar + clock），而 DatePicker 只有一個 calendar icon
> - 兩個 icon button 各自觸發對應面板（日期 / 時間），為 Syncfusion runtime 行為
> - Figma 設計層中這兩個 icon 以 `TimelineToday`（calendar）和 `Clock`（clock）作為具名元件，而非一般 slot
> - `clearIcon` optional button 位於 calendar btn 左側，同 DatePicker 邏輯

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── 尺寸（size: Small）──────────────────────────────────────────────────────
height_small:           40px                                 # 🎨 量測值（segmented-picker）
height_extra_small:     32px                                 # 🎨 metadata 推導（Size=Extra Small/Filled 總高 52px 含 label；trigger ≈ 32px）
height_medium:          48px                                 # 🎨 metadata 推導（Size=Medium/Filled 總高 70px 含 label；trigger ≈ 48px）
height_large:           56px                                 # 🎨 metadata 推導（Size=Large/Filled 總高 78px 含 label；trigger ≈ 56px）
padding_left:           10px                                 # 🎨 量測值；無 {ds-space-*} token；值見 app.css
inner_icon_gap:          6px                                 # 🎨 left-icon ↔ placeholder 量測值；無 token；值見 app.css
label_input_gap:        "{ds-space-margin-small}"            # 🔗 4px（Default）/ 8px（Comfortable）
action_btn_size:        32px                                 # 🎨 calendar / clock / clear icon btn 框尺寸；值見 app.css
action_btn_radius:      "{ds-radius-2extra-large}"           # 🔗 16px（全圓 ripple 區域）
calendar_icon_size:     16px                                 # 🎨 timeline-today svg；值見 app.css
clock_icon_size:        16px                                 # 🎨 schedule svg；值見 app.css
left_icon_size:         16px                                 # 🎨 optional left-icon；值見 app.css

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
label_line_height:       1.3                                 # 🎨 Label/14pt/Regular（letterSpacing=0）
label_letter_spacing:    0px                                 # 🎨
required_color:         "{color-sf-danger}"                  # 🔗 rgb(244 73 62)
required_font_size:     "{font-size-sf-text-sm}"             # 🔗 12px（Label/12pt/Regular）

# ── Placeholder / Value 文字 ───────────────────────────────────────────────
placeholder_text:       "Choose date & time"                 # 📋 預設佔位文字（選取後顯示格式化日期時間值）
placeholder_color:      "{ds-color-placeholder}"             # 🔗 rgb(103 113 126)
value_color:            "{color-sf-on-surface}"              # 🔗 rgb(15 23 42)
content_font_size:      "{font-size-sf-text-md}"             # 🔗 14px（Body Content/14pt/Regular）
content_font_weight:    "{font-weight-sf-normal}"            # 🔗 400
content_line_height:     1.5                                 # 🎨 Body Content/14pt/Regular（lineHeight: 1.5）
content_letter_spacing:  0.24px                              # 🎨 Body Content/14pt/Regular（letterSpacing: 0.24）

# ── 輔助文字（helper / alert）─────────────────────────────────────────────
helper_color:           "{color-sf-on-surface-variant}"      # 🔗 rgb(60 74 91)（default 態）
helper_font_size:       "{font-size-sf-text-sm}"             # 🔗 12px（Label/12pt/Regular）
helper_font_weight:     "{font-weight-sf-normal}"            # 🔗 400
helper_line_height:      1.3                                 # 🎨 Label/12pt/Regular（lineHeight≈1.3）
helper_letter_spacing:   0.1px                               # 🎨 Label/12pt/Regular（letterSpacing: 0.1）

# ── Icon 色 ───────────────────────────────────────────────────────────────
icon_color:             "{color-sf-on-surface-variant}"      # 🔗 rgb(60 74 91)（calendar / clock / left-icon 預設；`Icon/$icon-color`）

# ── 字型 ──────────────────────────────────────────────────────────────────
font_family:            "{font-family-sf-fontfamily}"        # 🔗 Roboto（EN）/ Noto Sans TC（ZH）
```

---

## 4. Variants　🎨🔗

> Figma 元件集（節點 16989-139036）metadata 抽取：4 Size × 11 State × 3 Shape = 132 variants。

```yaml
size:
  extra-small: { trigger_height: 32px }                     # 🎨 推導值
  small:       { trigger_height: 40px }                     # 🎨 ← ERP 主用
  medium:      { trigger_height: 48px }                     # 🎨 推導值
  large:       { trigger_height: 56px }                     # 🎨 推導值

shape:
  filled:                                                    # 🎨 主要 shape
    bg:         "{color-sf-surface-variant}"                 # 🔗
    border:     bottom only                                  # 🎨 Filled 底線式
  outlined:                                                  # 🎨 次要 shape（Figma 有軸，未精確量測）
    bg:         "{color-sf-surface}"                         # 🔗 白底
    border:     all sides                                    # 📋（border token 同 filled，全框）
  line:                                                      # 🎨 Figma 有軸，未精確量測
    border:     bottom only, no bg fill                      # 📋

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
  true:  calendar btn 左側加 ×（清除）按鈕                   # 📋

alertMessage:
  false: 無 helper-row（預設）                               # 📋
  true:  顯示 helper-row（含文字 ± alert icon）              # 📋
```

---

## 5. States　🎨🔗

> 底線寬度：default → `{ds-borderwidth-small}`（1px）；focus/validation → `{ds-borderwidth-medium}`（2px）。

```yaml
default:                                                     # 🎨 node 16989-139345 量測確認
  container_bg:  "{color-sf-surface-variant}"                # 🔗
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}" # 🔗 1px
  placeholder:   "{ds-color-placeholder}"                    # 🔗
  label:         "{color-sf-on-surface-variant}"             # 🔗
  icon:          "{color-sf-on-surface-variant}"             # 🔗（calendar + clock 雙 icon 同色）

hover:                                                       # 📋（Figma hover 態未精確量測；對齊 DatePicker / TextBox 推導）
  container_bg:  "{color-sf-surface-variant}"                # 🔗（底色保留，主題透明覆蓋層）
  border_bottom: "{ds-borderwidth-small} {color-sf-on-surface-variant}"  # 🔗 略深底線

focus:                                                       # 🔗（Figma $primary 確認，50 vars 含 ColorSf/primary）
  border_bottom: "{ds-borderwidth-medium} {color-sf-primary}" # 🔗 2px rgb(40 119 238)
  label:         "{color-sf-primary}"                        # 🔗 Material 3 focus label 啟動色

active:                                                      # 🎨（= dropdown 展開中，等同 focus）
  border_bottom: "{ds-borderwidth-medium} {color-sf-primary}" # 🔗

filled:                                                      # 📋（已有選取值，非 focus 態）
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}" # 🔗
  value_color:   "{color-sf-on-surface}"                     # 🔗 rgb(15 23 42)

read-only:                                                   # 📋
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}" # 🔗
  text:          "{color-sf-on-surface}"                     # 🔗

disabled:                                                    # 🎨（`Icon/$icon-color-disabled` 確認；50 vars 含 ColorSf/on-surface-opacity38）
  label:         "{color-sf-on-surface-opacity38}"           # 🔗 38% opacity
  placeholder:   "{color-sf-on-surface-opacity38}"           # 🔗
  icon:          "{color-sf-on-surface-opacity38}"           # 🔗（calendar + clock 雙 icon 皆淡化）
  container_bg:  "{color-sf-surface-variant}"                # 🔗（底色維持）

success:                                                     # 🎨（Figma $success / $border-success 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-success}" # 🔗 2px rgb(18 183 106)
  label:         "{color-sf-success}"                        # 🔗
  helper:        "{color-sf-success}"                        # 🔗

warning:                                                     # 🎨（Figma $warning / $border-warning 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-warning}" # 🔗 2px rgb(247 144 9)
  label:         "{color-sf-warning}"                        # 🔗
  helper:        "{color-sf-warning}"                        # 🔗

error:                                                       # 🎨（Figma $danger / $border-error 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-error}"  # 🔗 2px rgb(244 73 62)
  label:         "{color-sf-error}"                          # 🔗
  helper:        "{color-sf-error}"                          # 🔗
```

---

## 9. a11y　📋

- 輸入框為 `<input type="text">`，由 Syncfusion 管理；`<label>` 透過 `for` / `id` 關聯。
- `required` 欄位加 `aria-required="true"`；error 態加 `aria-invalid="true"` + `aria-describedby` 指向 helper text。
- **Calendar popup 鍵盤導航**（同 DatePicker）：
  - `Enter` / `Space` / 點擊 calendar icon → 展開日期面板
  - `↑↓←→` → 在日期格間移動；`Enter` → 選取
  - `Page Up` / `Page Down` → 上/下月切換
  - `Esc` → 關閉 popup，焦點回到 input
- **Time popup 鍵盤導航**：
  - 點擊 clock icon → 展開時間面板（時間 scroll list）
  - `↑` / `↓` → 在時間選項間移動；`Enter` → 選取
  - `Esc` → 關閉 popup，焦點回到 input
- Focus ring 由 Syncfusion Material 3 主題提供（`{ds-shadow-focus-ring1}`）。
- Popup 開啟時焦點移入 popup，關閉時焦點回到 trigger input（Syncfusion 內建行為）。
- `action-container` 內兩個 icon button 各自具備獨立焦點順序（Tab 可到達），均需 `aria-label`（如 `aria-label="選取日期"` / `aria-label="選取時間"`）。
- Mobile min touch target：calendar / clock icon btn 均為 32×32，低於建議 44×44；實際觸控需 Syncfusion runtime 或 `DsDateTimePicker` 包裝層補足 padding。

---

## 12. API / Props　📋

### Figma 設計層 props（`get_design_context` 節點 16989-139345）

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `label` | boolean | true | 是否顯示 label |
| `label1` | string | "Label" | label 文字 |
| `labelIcon` | boolean | false | 顯示 label 右側 info icon |
| `required` | boolean | false | 顯示必填 `*` |
| `text` | string | "Choose date & time" | placeholder 文字 |
| `leftIcon` | boolean | false | 顯示左側 icon 槽位 |
| `leftIcon1` | slot | — | 自訂左側 icon 內容 |
| `clearIcon` | boolean | false | 顯示清除（×）按鈕 |
| `alertMessage` | boolean | false | 顯示 helper / alert text |
| `alertMessage1` | string | "Helping text" | helper 文字內容 |
| `alertIcon` | boolean | true | 顯示 helper 區域的 alert icon |
| `alertIcon1` | slot | — | 自訂 alert icon |
| `shape` | "Filled" \| "Outlined" \| "Line" | "Filled" | 輸入框樣式 |
| `size` | "Extra Small" \| "Small" \| "Medium" \| "Large" | "Small" | 尺寸 |
| `state` | "Default" \| "Hover" \| "Focus" \| "Active" \| "Filled" \| "Read Only" \| "Disabled" \| "Success" \| "Warning" \| "Error" | "Default" | 狀態 |

### Syncfusion Runtime props（`<ejs-datetimepicker>`，`@syncfusion/ej2-vue-calendars`）

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `value` | Date \| string | — | 目前選取的日期時間（v-model） |
| `format` | string | "M/d/yyyy h:mm a" | 日期時間顯示格式 |
| `placeholder` | string | — | 觸發框 placeholder |
| `min` | Date | — | 最早可選日期時間 |
| `max` | Date | — | 最晚可選日期時間 |
| `step` | number | 30 | 時間面板分鐘步進（分鐘） |
| `showClearButton` | boolean | false | 顯示觸發框清除按鈕 |
| `enabled` | boolean | true | 啟用 / 停用 |
| `readonly` | boolean | false | 唯讀 |
| `strictMode` | boolean | false | 嚴格模式（不允許手動輸入超出 min/max） |
| `floatLabelType` | "Auto" \| "Always" \| "Never" | "Never" | floating label 行為 |

---

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface §On-* §Outline §Status §Border Width §Space §Radius §Typography §`--ds-*`
- 同族元件：`DatePicker.md`（Anatomy / token 規格高度共用，差異在 action-container 多一個 clock icon）；`TextBox.md`（Filled 底線式觸發框共用規格）；`TimePicker`（時間面板 popup 共用）
- Prototype 落地（值權威 / class 命名）：`prototyper/assets/app.css`（Syncfusion Material 3 theme override）
- Code：`@syncfusion/ej2-vue-calendars`（`<ejs-datetimepicker>`）；playground 參考 `syncfusion-playground/app/pages/playground/datepicker.vue`（含 DateTimePicker 用法）
- Figma：`https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/` 節點 `16989-139036`（元件集）/ `16989-139345`（Small / Default / Filled 量測節點）

---

## Figma 補入紀錄（2026-06-29）

> Layer 1–4 依 `figma-to-component-doc.md` Token Matching workflow 執行。  
> 來源：`get_variable_defs`（節點 16989-139036，50 變數）+ `get_metadata`（同節點，variant 軸結構）+ `get_design_context`（節點 16989-139345，Small / Default / Filled，含完整觸發框結構）+ `get_screenshot`（元件集 16989-139036）。

**✅ 已確認對映（Figma 變數 → `athena-tokens.md`）**

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
| `ColorSf/on-surface-opacity38` | `{color-sf-on-surface-opacity38}` | disabled 文字 / icon |
| `DsColor/placeholder` | `{ds-color-placeholder}` | placeholder 文字色 |
| `Icon/$icon-color` | `{color-sf-on-surface-variant}` | calendar / clock icon 預設色 |
| `Icon/$icon-color-disabled` | `{color-sf-on-surface-opacity38}` | disabled icon 色 |
| `Text/$content-text-color` | `{color-sf-on-surface}` | value 文字色 |
| `Text/$placeholder-text-color` | `{ds-color-placeholder}` | placeholder 色 |
| `FontSizeSf/TextMd` | `{font-size-sf-text-md}` | 14px content |
| `FontSizeSf/TextSm` | `{font-size-sf-text-sm}` | 12px label-small / helper |
| `FontSizeSf/TextLg` | `{font-size-sf-text-lg}` | 16px（Outlined Large 備用） |
| `FontWeightSf/Normal` | `{font-weight-sf-normal}` | 400 |
| `FontFamilySf/Fontfamily` | `{font-family-sf-fontfamily}` | Roboto / Noto Sans TC |
| `DsSpace/Margin/Small` | `{ds-space-margin-small}` | label ↔ input gap |
| `Radius/RadiusSm` | `{ds-radius-small}` | top radius 4px |
| `Radius/RadiusNone` | — | 0（CSS 預設，無需 token） |

**50 個變數全數找到對應，無漂移。**（與 DropDownTree 同節點變數集完全一致，確認為共用 token 集）

**✅ 與 DatePicker 的關鍵差異確認（`get_design_context` 節點 16989-139345）**

| 項目 | DatePicker | DateTimePicker |
|---|---|---|
| action-container icon 數 | 1（calendar only） | **2（calendar + clock）** |
| calendar icon 元件名 | `Calendar`（material-symbols:calendar-today 系） | `TimelineToday`（timeline-today 圖示） |
| clock icon 元件名 | 無 | **`Clock`（schedule 圖示）** |
| placeholder 預設文字 | "Choose Date" | **"Choose date & time"** |
| action-container 寬度 | ~32px（1 btn）| **~64px（2 btn）** |
| overlay | calendar popup | **calendar popup + time popup（Syncfusion 組合面板）** |

**⚠️ Token gap（非阻塞，已標 🎨 量測值）**

1. **`padding_left: 10px`**：`athena-tokens.md` 無 10px 間距 token（scale 跳 8→12px）→ 值權威見 `prototyper/assets/app.css`（與 DatePicker / TextBox 同）
2. **`inner_icon_gap: 6px`**：同上，scale 無 6px → 值權威見 `app.css`
3. **hover 觸發框底線色**：Figma hover 態節點未精確量測，依 DatePicker / TextBox 慣例推導（`{color-sf-on-surface-variant}`）

**⬜ 待補（次要）**

- size: Extra Small / Medium / Large 的精確 trigger height 量測（本文以 metadata 框高推導，需驗證）
- Outlined / Line shape 的 border-all-sides 與 radius 精確值
- Time popup 的詳細 token 規格（Syncfusion 內建 overlay，Figma 未設計；待 DS 另案補）
- hover 觸發框底線色精確量測（補後更新 §5）
- 兩個 icon button 的 icon 精確 Iconify id 確認（`timeline-today` / `schedule` 系）
