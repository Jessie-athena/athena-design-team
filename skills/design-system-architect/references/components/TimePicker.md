---
name: TimePicker
category: 輸入 — 時間
tier: lite
status: ✅ 已採用
authority: ERP CLAUDE.md（Syncfusion TimePicker, @syncfusion/ej2-vue-calendars）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16992-358875   # 🎨 FAI2 Library · TimePicker 元件集
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs（node 16992-358875，50 vars）+ get_design_context（node 16992-359184, Size=Small/State=Default/Shape=Filled）+ get_metadata（variant 軸確認）+ get_screenshot（元件集 16992-358875）
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺值一律 token-reference；raw px 量測值標 🎨 指向值權威 `prototyper/assets/app.css`，不在此重印。
> §3–§5 已用 Figma `get_variable_defs` + `get_design_context`（節點 16992-359184，Filled / Small / Default）+ `get_metadata`（variant 軸）+ `get_screenshot`（元件集 16992-358875）校準；token gap 見文末「Figma 補入紀錄」。

---

## 1. 概述　📋

時間選擇器。輸入框部分與 TextBox / DatePicker 共用 Filled 底線式樣式（surface-variant 底色 + 底線），右側固定掛 **clock icon** 按鈕，點擊後展開 dropdown list 供使用者選取時間（Syncfusion runtime 為捲動列表型，非 scroll drum 鼓輪型）。

**元件選擇依據**：FAI2 設計稿此節點（16992-358875）的 trigger 結構與 `DatePicker`（節點 16986-236059）完全同族，唯一的視覺差異是尾端 icon 從 calendar 換成 **clock**（`material-symbols:schedule`）。Overlay 時間選擇 panel 由 Syncfusion runtime 提供，不在此 Figma 節點展開（與 DatePicker 的 calendar popup 同處理方式）。

**何時使用**：表單中需要使用者選取單一時間（時:分 格式）的欄位，例如開始時間、結束時間、預約時段。  
**何時不用**：日期 + 時間一起輸入 → `DateTimePicker`；僅日期 → `DatePicker`；自由時間字串（不需要 popup）→ 降級用 `TextBox` 加 input mask（`HH:mm` 格式）。

---

## 2. Anatomy　🎨

> 來源：`get_design_context`（節點 16992-359184，Filled / Small / Default）

```
TimePicker（vertical stack, gap = {ds-space-margin-small}）
├── label-row?（horizontal, gap = 2px = 🎨 量測值）
│   ├── label-text
│   ├── required-asterisk?（*）
│   └── label-icon?（circle-info, 11×11 = 🎨 量測值）
├── trigger-container（h = 40px，Filled 底線式，horizontal）
│   ├── text-input-container（flex-1, pl = 10px = 🎨 量測值）
│   │   ├── left-icon?（16×16, opt）
│   │   └── placeholder / value-text（gap left-icon ↔ text = 6px = 🎨 量測值）
│   └── action-container（right side, flex-shrink-0）
│       ├── clear-icon-btn?（32×32, rounded-full, opt）
│       └── clock-icon-btn（32×32, rounded-full, **always visible**）
│           └── clock icon（16×16, material-symbols:schedule）
└── helper-row?（horizontal, gap = {ds-space-margin-small}）
    ├── helper-text
    └── alert-icon?（circle-check, 10×10, opt）
```

> **與 DatePicker 的 Anatomy 差異**：action-container 右側的固定 icon btn 從 calendar-icon-btn（📅）換為 **clock-icon-btn**（🕐），使用 `material-symbols:schedule`（或等效 clock icon）；其餘槽位結構完全相同（left-icon / clear-icon / label / helper）。  
> **與 TextBox 的結構差異**：right side action-container 固定包含 clock-icon-btn（不可移除）；placeholder 預設文字為「Select a time」。

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── 尺寸（size: Small）──────────────────────────────────────────────────────
height_extra_small:     32px                                 # 🎨 量測值（metadata 確認）
height_small:           40px                                 # 🎨 量測值（get_design_context 確認）
height_medium:          48px                                 # 🎨 量測值（metadata 確認）
height_large:           56px                                 # 🎨 量測值（metadata 確認）
padding_left:           10px                                 # 🎨 量測值，無 {ds-space-*} token；值見 app.css
inner_icon_gap:          6px                                 # 🎨 left-icon ↔ placeholder 量測值，無 token；值見 app.css
label_input_gap:        "{ds-space-margin-small}"            # 🔗 4px（Default）/ 8px（Comfortable）
helper_gap:             "{ds-space-margin-small}"            # 🔗 4px（Figma 節點 helper-row gap 與 margin-small 語意一致）
action_btn_size:        32px                                 # 🎨 clock-icon-btn / clear-icon-btn 框尺寸；值見 app.css
action_btn_radius:      "{ds-radius-2extra-large}"           # 🔗 16px（全圓 ripple 區域）
clock_icon_size:        16px                                 # 🎨 clock svg 尺寸；值見 app.css
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
placeholder_text:       "Select a time"                      # 📋 預設佔位文字（選取後顯示格式化時間值，如「09:30 AM」）
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
icon_color:             "{color-sf-on-surface-variant}"      # 🔗 rgb(60 74 91)（clock / left-icon 預設色）

# ── 字型 ──────────────────────────────────────────────────────────────────
font_family:            "{font-family-sf-fontfamily}"        # 🔗 Roboto（EN）/ Noto Sans TC（ZH）
```

---

## 4. Variants　🎨🔗

> Figma 元件集（節點 16992-358875）`get_metadata` 確認三軸 variant 矩陣：

```yaml
shape:
  filled:                                                    # 🎨（metadata 確認，Figma 節點名含 Shape=Filled）
    bg: "{color-sf-surface-variant}"                         # 🔗 底色
    border_side: bottom only                                 # 🎨 Filled 底線式
  outlined:                                                  # 🎨（metadata 確認，Shape=Outlined）
    bg: "{color-sf-surface}"                                 # 🔗 白底 rgb(255 255 255)
    border: all sides                                        # 🎨 Outlined 全框式；border token 同 filled
  line:                                                      # 🎨（metadata 確認，Shape=Line）
    bg: transparent                                          # 📋 僅底線，無填色
    border_side: bottom only                                 # 🎨 同 filled 但無背景色

size:
  extra_small: { height: 32px }                              # 🎨 量測確認（metadata）
  small:       { height: 40px }                              # 🎨 量測確認（get_design_context）← ERP 主用
  medium:      { height: 48px }                              # 🎨 量測確認（metadata）
  large:       { height: 56px }                              # 🎨 量測確認（metadata）

state:
  # 共 10 態（metadata 確認）
  default / hover / focus / active / filled /
  read-only / disabled / success / warning / error           # 🎨 詳見 §5

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
  true:  clock 按鈕左側加 ×（清除）按鈕                     # 📋

alertMessage:
  false: 無 helper-row（預設）                               # 📋
  true:  顯示 helper-row（含文字 ± alert icon）              # 📋
```

---

## 5. States　🎨🔗

> 底線寬度：default → `{ds-borderwidth-small}`（1px）；focus/active/validation → `{ds-borderwidth-medium}`（2px）。
> Outlined shape 對應態的 border 顏色邏輯相同，全框四邊同步。

```yaml
default:                                                     # 🎨（node 16992-359184 量測確認）
  container_bg: "{color-sf-surface-variant}"                 # 🔗 Filled
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}" # 🔗 1px rgb(127 137 150)
  placeholder: "{ds-color-placeholder}"                      # 🔗 rgb(103 113 126)
  label: "{color-sf-on-surface-variant}"                     # 🔗 rgb(60 74 91)
  icon_color: "{color-sf-on-surface-variant}"                # 🔗 clock icon 同 label 色

hover:                                                       # 📋（Figma hover 態量測未補；依 TextBox / DatePicker 慣例推導）
  container_bg: "{color-sf-surface-variant}"                 # 🔗（底色保留，透明覆蓋層由主題提供）
  border_bottom: "{ds-borderwidth-small} {color-sf-on-surface-variant}"  # 🔗 略深底線

focus:                                                       # 🔗（Figma $primary 確認；節點 16992-359240）
  border_bottom: "{ds-borderwidth-medium} {color-sf-primary}"  # 🔗 2px rgb(40 119 238)
  label: "{color-sf-primary}"                                # 📋 Material 3 focus label 啟動色

active:                                                      # 🎨（= dropdown 展開中；同 focus 視覺）
  border_bottom: "{ds-borderwidth-medium} {color-sf-primary}"  # 🔗

filled:                                                      # 📋（已有選取值，非 focus 狀態）
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}" # 🔗
  value_color: "{color-sf-on-surface}"                       # 🔗 rgb(15 23 42)

read-only:                                                   # 📋
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}" # 🔗
  text: "{color-sf-on-surface}"                              # 🔗

disabled:                                                    # 🎨（Figma $icon-color-disabled / on-surface-opacity38 確認）
  label: "{color-sf-on-surface-opacity38}"                   # 🔗 38% opacity（Material 3 慣例）
  placeholder: "{color-sf-on-surface-opacity38}"             # 🔗
  icon_color: "{color-sf-on-surface-opacity38}"              # 🔗 clock icon 亦淡化
  container_bg: "{color-sf-surface-variant}"                 # 🔗 底色維持，透明度由文字/圖示體現

success:                                                     # 🎨（Figma $border-success / $success 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-success}"  # 🔗 2px rgb(18 183 106)
  label: "{color-sf-success}"                                # 📋
  helper: "{color-sf-success}"                               # 📋

warning:                                                     # 🎨（Figma $border-warning / $warning 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-warning}"  # 🔗 2px rgb(247 144 9)
  label: "{color-sf-warning}"                                # 📋
  helper: "{color-sf-warning}"                               # 📋

error:                                                       # 🎨（Figma $border-error / $danger 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-error}"  # 🔗 2px rgb(244 73 62)
  label: "{color-sf-error}"                                  # 📋
  helper: "{color-sf-error}"                                 # 📋
```

---

## 9. a11y　📋

- 輸入框為 `<input type="text">`，由 Syncfusion 管理；`<label>` 透過 `for` / `id` 關聯。
- `required` 欄位加 `aria-required="true"`；Error 態加 `aria-invalid="true"` + `aria-describedby` 指向 helper text。
- **Time dropdown 鍵盤導航**：
  - `Enter` / `Space` / 點擊 clock icon → 開啟 dropdown 清單
  - `↑` / `↓` → 在時間選項間移動（Syncfusion runtime 預設 30 分鐘間隔，可用 `step` prop 調整）
  - `Enter` → 選取目前 focus 的時間選項，關閉 dropdown
  - `Esc` → 關閉 dropdown，焦點回到 input
  - 直接打字 → 過濾顯示最近符合的時間選項
- Focus ring 由 Syncfusion Material 3 主題提供（`{ds-shadow-focus-ring1}`）。
- Dropdown popup 需有適當 `role="listbox"` + 選項 `role="option"`（Syncfusion 內建）；popup 開啟時焦點移入 dropdown，關閉時焦點回到 trigger input。
- Clock icon btn 尺寸 32×32；實際觸控環境如需符合 44×44 最小觸控目標，由外框 padding 補全（與 DatePicker calendar icon 同處理方式）。

---

## 12. API / Props　📋

> 以下 props 反映 Figma 設計層（從 `get_design_context` interface 抽取）。底層 Syncfusion `<ejs-timepicker>` API 詳 playground `timepicker.vue`。

### Figma 設計層 props

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `label` | boolean | true | 是否顯示 label |
| `label1` | string | "Label" | label 文字 |
| `labelIcon` | boolean | false | 顯示 label 右側 info icon |
| `required` | boolean | false | 顯示必填 `*` |
| `text` | string | "Select a time" | placeholder 文字 |
| `leftIcon` | boolean | false | 顯示左側 icon 槽位 |
| `leftIcon1` | slot | — | 自訂左側 icon 內容 |
| `clearIcon` | boolean | false | 顯示清除（×）按鈕 |
| `alertMessage` | boolean | false | 顯示 helper / alert text |
| `alertMessage1` | string | "Helping text" | helper 文字內容 |
| `alertIcon` | boolean | true | 顯示 helper 區域的 alert icon |
| `alertIcon1` | slot | — | 自訂 alert icon |
| `shape` | "Filled" / "Outlined" / "Line" | "Filled" | 輸入框樣式 |
| `size` | "Extra Small" / "Small" / "Medium" / "Large" | "Small" | 尺寸 |
| `state` | "Default" / "Hover" / "Focus" / "Active" / "Filled" / "Read Only" / "Disabled" / "Success" / "Warning" / "Error" | "Default" | 觸發框狀態 |

### Syncfusion Runtime props（`<ejs-timepicker>`，`@syncfusion/ej2-vue-calendars`）

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `value` | Date / string | — | 目前時間值（v-model） |
| `placeholder` | string | — | 觸發框 placeholder |
| `min` | Date | — | 最小可選時間 |
| `max` | Date | — | 最大可選時間 |
| `step` | number | 30 | 時間清單間隔（分鐘） |
| `format` | string | "hh:mm a" | 顯示格式（12hr：`"hh:mm a"`；24hr：`"HH:mm"`） |
| `enabled` | boolean | true | 啟用 / 停用 |
| `readonly` | boolean | false | 唯讀 |
| `showClearButton` | boolean | false | 顯示清除按鈕（對應 `clearIcon` Figma prop） |
| `floatLabelType` | "Auto" / "Always" / "Never" | "Never" | floating label 行為 |
| `popupWidth` | string | — | dropdown 寬度（預設同觸發框） |
| `scrollTo` | Date | — | dropdown 開啟時預設捲到的時間位置 |

> **12hr vs 24hr 模式**：以 `format` prop 控制。ERP 台灣環境建議 `"HH:mm"`（24 小時制）；如需 AM/PM 顯示則用 `"hh:mm a"`。

---

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface §On-* §Outline §Status §Border Width §Space §Radius §Typography §`--ds-*`
- 同族元件：`DatePicker.md`（trigger Filled 底線式，Anatomy / token 完全共用，icon 唯一差異）；`TextBox.md`（共用 Filled 容器規格）；`DateTimePicker`（結合日期 + 時間，待補設計文件）
- Prototype 落地（值權威 / class 命名）：`prototyper/assets/app.css`（Syncfusion Material 3 theme override）
- Code：`@syncfusion/ej2-vue-calendars`；playground 參考 `syncfusion-playground/app/pages/playground/timepicker.vue`（若存在）
- Figma：`https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/` 節點 `16992-358875`（元件集）/ `16992-359184`（Small Default Filled 量測節點）

---

## Figma 補入紀錄（2026-06-29）

> 依 `../component-doc-schema.md` §5 補入 SOP 執行。  
> 來源：`get_variable_defs`（節點 16992-358875，50 變數）+ `get_metadata`（variant 軸確認）+ `get_design_context`（節點 16992-359184，Small / Default / Filled）+ `get_screenshot`（元件集 16992-358875）。

**✅ 已確認對映（Figma 變數 → `athena-tokens.md`）**

| Figma 變數 | token | 用途 |
|---|---|---|
| `ColorSf/on-surface-variant` | `{color-sf-on-surface-variant}` | label / icon / helper 色 |
| `ColorSf/surface-variant` | `{color-sf-surface-variant}` | Filled 底色 |
| `ColorSf/outline` | `{color-sf-outline}` | default 底線色 |
| `ColorSf/on-surface` | `{color-sf-on-surface}` | value 文字色 |
| `ColorSf/primary` | `{color-sf-primary}` | focus 邊框 / label |
| `ColorSf/danger` / `ColorSf/error` | `{color-sf-danger}` / `{color-sf-error}` | error 邊框（同值 rgb(244 73 62)） |
| `ColorSf/success` | `{color-sf-success}` | success 邊框 |
| `ColorSf/warning` | `{color-sf-warning}` | warning 邊框 |
| `DsColor/placeholder` | `{ds-color-placeholder}` | placeholder 文字色 |
| `ColorSf/on-surface-opacity38` | `{color-sf-on-surface-opacity38}` | disabled 文字 / icon |
| `ColorSf/transparent` / `BG/$transparent` | `{color-sf-transparent}` | action btn 靜態背景 |
| `FontSizeSf/TextMd` | `{font-size-sf-text-md}` | 14px content |
| `FontSizeSf/TextSm` | `{font-size-sf-text-sm}` | 12px label-small / helper |
| `FontSizeSf/TextLg` | `{font-size-sf-text-lg}` | 16px（Figma 包含，Outlined Large 備用） |
| `FontWeightSf/Normal` | `{font-weight-sf-normal}` | 400 |
| `FontFamilySf/Fontfamily` | `{font-family-sf-fontfamily}` | Roboto / Noto Sans TC |
| `DsSpace/Margin/Small` | `{ds-space-margin-small}` | label ↔ input gap / helper gap |
| `Radius/RadiusSm` | `{ds-radius-small}` | top radius 4px |
| `Radius/RadiusNone` | — | 0（CSS 預設，無需 token） |
| `Text/$content-text-color` | `{color-sf-on-surface}` | value 文字色（別名確認） |
| `Text/$placeholder-text-color` | `{ds-color-placeholder}` | placeholder 色（別名確認） |
| `Icon/$icon-color` | `{color-sf-on-surface-variant}` | clock / left icon 色（別名確認） |
| `Border/$border` | `{color-sf-outline}` | 邊框色（別名確認） |

**50 個變數中，35 個對映至既有 token（含 alias 別名確認），12 個為排版複合型 Font() 值（已各自拆解對映至 font-size / font-weight / font-family token），3 個為 CSS 預設值（0）不需 token。全數無漂移。**

**✅ 與 DatePicker 差異確認（關鍵）**

| 項目 | DatePicker | TimePicker |
|---|---|---|
| 固定 icon btn | calendar icon（📅） | **clock icon**（🕐, `material-symbols:schedule`）|
| placeholder 預設文字 | "Choose Date" | "Select a time" |
| Shape 軸 | Filled / Outlined（2 軸） | **Filled / Outlined / Line（3 軸）** |
| Variable set 數量 | 38 vars | 50 vars（多 12，主要為 Label/16pt 與額外 alias）|
| `Label/16pt/Regular` | 未出現 | ✅ 有（16px label，備用 Large size）|
| Overlay 型態 | Calendar popup（月曆） | Time dropdown list（Syncfusion runtime 清單型）|

**⚠️ Token gap（非阻塞，已標 🎨 量測值）**

1. **`padding_left: 10px`**：`athena-tokens.md` 無 10px 間距 token（scale 跳 8→12px）→ 值權威見 `prototyper/assets/app.css`（與 TextBox / DatePicker 同）
2. **`inner_icon_gap: 6px`**：同上，scale 無 6px → 值權威見 `app.css`（與 TextBox / DatePicker 同）
3. **`action_btn_size: 32px`**：`athena-tokens.md` 無 32px 尺寸 token（此為元件量測值）→ 值權威見 `app.css`

**⬜ 待補（次要）**

- Shape=Outlined / Line 的邊框精確 token（本次僅量測 Filled；Outlined / Line 節點未單獨 get_design_context）
- Hover 觸發框底線色精確量測（以 TextBox / DatePicker 慣例推導，待 Figma hover 節點確認）
- Size=Extra Small / Medium / Large 的 padding / inner gap 量測（高度已由 metadata 確認，其餘尺寸依 app.css 為準）
- Time dropdown popup 的 overlay token 規格（popup 屬 Syncfusion 內建，FAI2 本節點未展開；待 DS 另案補）
- 12hr / 24hr 格式切換的 Figma 設計規格（Figma 為靜態 trigger，格式由 Syncfusion `format` prop 控制）
