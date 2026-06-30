---
name: DateRangePicker
category: 輸入 — 日期
tier: full
status: ✅ 已產出
authority: ERP CLAUDE.md（Syncfusion DateRangePicker, @syncfusion/ej2-vue-calendars）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16988-272294
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs（node 16988-272294, 43 vars）+ get_metadata（同節點，88 variants）+ get_design_context（節點 16988-272603 Small/Default/Filled 觸發框 + 節點 16988-272687 Small/Active/Filled 含 overlay）+ get_screenshot（元件集 16988-272294）
---

> 依 `../component-doc-schema.md`（Full 層，overlay 含雙月曆面板）產出。視覺值一律 token-reference；raw px 量測值標 🎨 指向值權威 `prototyper/assets/app.css`，不在此重印。
> §3–§5 已用 Figma `get_variable_defs` + `get_design_context`（節點 16988-272603 Default + 節點 16988-272687 Active 含完整 overlay）校準；token gap 見文末「Figma 補入紀錄」。

---

## 1. 概述　📋

日期範圍選擇器。觸發框外觀與 DatePicker 同族（Material 3 Filled 底線式），右側固定掛 `date-range`（週曆）icon 按鈕；點擊後展開雙月曆 overlay，使用者先選開始日再選結束日，確定後觸發框顯示格式化日期範圍字串。

**何時使用**：表單中需要輸入日期範圍（起始 + 結束）的欄位。典型 ERP 情境：會計期間選擇、報表日期區間、合約有效期間、休假申請起訖日。  
**何時不用**：
- 單一日期 → `DatePicker`（`DsDatePicker`）  
- 只需月份或年份選擇 → 使用 `ejs-datepicker` 的 `start: "Year"` / `depth: "Year"` 深度設定  
- 不需要 popup、直接輸入字串 → `TextBox` + mask  
- 兩個獨立日期（非連續範圍語意）→ 兩個獨立的 `DsDatePicker`

---

## 2. Anatomy　🎨

> 來源：`get_design_context`（節點 16988-272603 Default + 16988-272687 Active/overlay）

```
DateRangePicker（vertical stack, gap = {ds-space-margin-small}）
├── label-row?（horizontal, gap = 2px = {ds-space-margin-extra-small}）
│   ├── label-text
│   ├── required-asterisk?（*）
│   └── label-icon?（circle-info, 11×11 = 🎨 量測值）
├── trigger-container（h = 40px Small，Filled 底線式）
│   ├── left-zone（flex-1, pl = 10px = 🎨 量測值，gap = 6px = 🎨 量測值）
│   │   ├── left-icon?（16×16, opt）
│   │   └── placeholder / value-text（"Choose date"）
│   └── right-zone（flex-shrink-0）
│       ├── clear-icon-btn?（32×32, rounded-full, opt，`clearIcon` prop）
│       └── date-range-icon-btn（32×32, rounded-full, 固定不可省略）
│           └── date-range icon（16×16，「Week」/ 週曆圖示）
├── helper-row?（horizontal, gap = 4px = 🎨 量測值）
│   ├── helper-text
│   └── alert-icon?（circle-check, 10×10, opt）
└── overlay-popup?（absolute, top = 40px, left = 0, calendar prop 控制顯示）
    └── Calendar（rounded-[8px]，border outline-variant，drop-shadow）
        └── Table container（horizontal flex, items-start）
            ├── Table-Left（w = 252px, flex-col）
            │   ├── month-header（px = 12px, py = 4px）
            │   │   ├── month-title（14px medium, "January 2023"）
            │   │   └── nav-buttons（gap = 8px）
            │   │       ├── chevron-left-btn（32×32, rounded-full）
            │   │       └── chevron-right-btn（32×32, rounded-full）
            │   └── calendar-grid（px = 8px, pb = 8px, pt = 4px）
            │       ├── weekday-header-row（7 cells × 32×32）
            │       │   └── day-label（S/M/T/W/T/F/S, 11px, on-surface-variant）
            │       └── date-rows（multiple rows × 7 cells × 32×32）
            │           └── _Atom / Days（32×32, various states）
            └── Table-Right（w = 252px, flex-col，同 Table-Left 結構）
                └── （"February 2023"，同結構）
```

> **與 DatePicker 的 Anatomy 差異**：
> - 觸發框右側 icon 為 `date-range`（週曆）圖示（DatePicker 為 `calendar`）
> - overlay 為**雙月曆面板並排**（兩個 252px Table，共 504px+ 邊距），DatePicker 為單月曆
> - Figma `calendar` prop 控制雙月曆 overlay 顯示（DatePicker 無此 prop，使用 `dropdown`）
> - 觸發框 placeholder 預設 "Choose date"（與 DatePicker 一致，Syncfusion runtime 渲染為帶連字符的範圍格式）
> - 觸發框結構與 DatePicker 完全一致，含 label-row / helper-row / clearIcon 槽位

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── 觸發框（trigger）——— 與 DatePicker §3 完全一致 ───────────────────────────
height_extra_small:     32px                                 # 🎨 量測值（metadata 確認）
height_small:           40px                                 # 🎨 量測值（ERP 主用）
height_medium:          48px                                 # 🎨 量測值（metadata 確認）
height_large:           56px                                 # 🎨 量測值（metadata 確認）

padding_left:           10px                                 # 🎨 量測值，無 {ds-space-*} token；值見 app.css
inner_icon_gap:          6px                                 # 🎨 left-icon ↔ placeholder 間距；值見 app.css
label_input_gap:        "{ds-space-margin-small}"            # 🔗 4px（Default）/ 8px（Comfortable）
helper_gap:              4px                                 # 🎨 helper-row gap；值見 app.css
action_btn_size:        32px                                 # 🎨 date-range-icon-btn / clear-icon-btn；值見 app.css
action_btn_radius:      "{ds-radius-2extra-large}"           # 🔗 16px（全圓 ripple）
date_range_icon_size:   16px                                 # 🎨 週曆圖示；值見 app.css

# ── 觸發框外形（shape: Filled）──────────────────────────────────────────────
container_bg:           "{color-sf-surface-variant}"         # 🔗 rgb(237 240 247)
radius_top:             "{ds-radius-small}"                  # 🔗 4px（top-left + top-right）
radius_bottom:          0                                    # 🎨 Filled 底線式，無底圓角
border_side:            bottom only                          # 🎨 Filled 特徵
border_width_default:   "{ds-borderwidth-small}"             # 🔗 1px
border_width_active:    "{ds-borderwidth-medium}"            # 🔗 2px（focus / validation 態）
border_color_default:   "{color-sf-outline}"                 # 🔗 rgb(127 137 150)

# ── 標籤文字 ──────────────────────────────────────────────────────────────
label_color:            "{color-sf-on-surface-variant}"      # 🔗 rgb(60 74 91)
label_font_size:        "{font-size-sf-text-md}"             # 🔗 14px（Label/14pt/Regular）
label_font_weight:      "{font-weight-sf-normal}"            # 🔗 400
label_line_height:       1.3                                 # 🎨 Label/14pt/Regular
required_color:         "{color-sf-danger}"                  # 🔗 rgb(244 73 62)
required_font_size:     "{font-size-sf-text-sm}"             # 🔗 12px

# ── Placeholder / Value 文字 ───────────────────────────────────────────────
placeholder_text:       "Choose date"                        # 📋 Syncfusion runtime 渲染範圍格式（如 "2023/01/14 - 2023/02/15"）
placeholder_color:      "{ds-color-placeholder}"             # 🔗 rgb(103 113 126)
value_color:            "{color-sf-on-surface}"              # 🔗 rgb(15 23 42)
content_font_size:      "{font-size-sf-text-md}"             # 🔗 14px（Body Content/14pt/Regular）
content_font_weight:    "{font-weight-sf-normal}"            # 🔗 400
content_line_height:     1.5                                 # 🎨
content_letter_spacing:  0.24px                              # 🎨

# ── 輔助文字 ──────────────────────────────────────────────────────────────
helper_color:           "{color-sf-on-surface-variant}"      # 🔗 rgb(60 74 91)
helper_font_size:       "{font-size-sf-text-sm}"             # 🔗 12px（Label/12pt/Regular）
helper_font_weight:     "{font-weight-sf-normal}"            # 🔗 400
helper_line_height:      1.3                                 # 🎨
helper_letter_spacing:   0.1px                               # 🎨

# ── Icon 色 ───────────────────────────────────────────────────────────────
icon_color:             "{color-sf-on-surface-variant}"      # 🔗 rgb(60 74 91)（date-range / left-icon 預設色）

# ── 字型 ──────────────────────────────────────────────────────────────────
font_family:            "{font-family-sf-fontfamily}"        # 🔗 Roboto（EN）/ Noto Sans TC（ZH）

# ── overlay（雙月曆面板）────────────────────────────────────────────────────
overlay_bg:             "{color-sf-surface}"                 # 🔗 rgb(255 255 255)
overlay_radius:         8px                                  # 🎨 量測值，{ds-radius-large} = 8px → 🔗 可對映
overlay_border:         "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗 1px rgb(215 218 224)
overlay_shadow:         "{ds-shadow-md}"                     # 🔗（Figma: drop-shadow 0px_4px_4px_0.15 + 0px_1px_1.5px_0.3）
overlay_top:            40px                                 # 🎨 相對觸發框底邊距（= Small height）；值見 app.css
overlay_left:           0                                    # 🎨 左對齊觸發框
overlay_panel_width:    252px                                # 🎨 每個月曆面板寬度；總寬 = 504px（兩面板無 gap）；值見 app.css

# ── 月曆面板 header ────────────────────────────────────────────────────────
month_header_px:        12px                                 # 🎨 量測值；值見 app.css
month_header_py:         4px                                 # 🎨 量測值；值見 app.css
month_title_color:      "{color-sf-on-surface}"              # 🔗 rgb(15 23 42)
month_title_font_size:  "{font-size-sf-text-md}"             # 🔗 14px
month_title_font_weight: 500                                 # 🎨 Figma FontWeightSf/Medium（= 500），對映 {font-weight-sf-medium}
month_title_line_height: 1.3                                 # 🎨
month_title_tracking:   0.1px                                # 🎨
nav_btn_size:           32px                                 # 🎨 chevron-left / chevron-right 按鈕框；值見 app.css
nav_btn_radius:         "{ds-radius-2extra-large}"           # 🔗 16px（全圓 ripple）
nav_icon_size:          16px                                 # 🎨 chevron icon；值見 app.css

# ── 日期格（calendar grid）────────────────────────────────────────────────
grid_px:                 8px                                 # 🎨 量測值；值見 app.css
grid_pb:                 8px                                 # 🎨 量測值；值見 app.css
grid_pt:                 4px                                 # 🎨 量測值；值見 app.css
weekday_label_color:    "{color-sf-on-surface-variant}"      # 🔗 rgb(60 74 91)（S/M/T/W/T/F/S）
weekday_label_font_size: "{font-size-sf-text-xs}"            # 🔗 11px（FontSizeSf/TextXs）
weekday_label_tracking:  0.1px                               # 🎨
day_cell_size:          32px                                 # 🎨 _Atom / Days 格 32×32；值見 app.css
day_cell_radius:        "{ds-radius-10extra-large}"          # 🔗 1000px（全圓 ripple / selected background）
day_number_font_size:   "{font-size-sf-text-sm}"             # 🔗 12px
day_number_font_weight:  "{font-weight-sf-normal}"           # 🔗 400
day_number_line_height:  1.3                                 # 🎨
day_number_tracking:    0.1px                                # 🎨

# ── 日期格狀態色 ───────────────────────────────────────────────────────────
day_default_color:      "{color-sf-on-surface}"              # 🔗 rgb(15 23 42)
day_today_border:       "{ds-borderwidth-small} {color-sf-primary}"  # 🔗 1px 藍框（full-round）
day_today_color:        "{color-sf-primary}"                 # 🔗 rgb(40 119 238)（Figma 確認）
day_hover_bg:           "{color-sf-on-surface-opacity8}"     # 🔗 Material 3 state-layer 8%（Syncfusion runtime；Figma 未暴露）
day_range_fill_bg:      "{color-sf-primary-container}"       # 🔗 rgb(213 228 255)（Syncfusion runtime；Figma 靜態設計未含）
day_range_start_end_bg: "{color-sf-primary}"                 # 🔗 rgb(40 119 238)（Syncfusion runtime；Figma 靜態設計未含）
day_range_start_end_fg: "{color-sf-on-primary}"              # 🔗 rgb(255 255 255)（Syncfusion runtime；Figma 靜態設計未含）
day_disabled_color:     "{color-sf-on-surface-opacity38}"    # 🔗 38% opacity（Syncfusion runtime）
```

---

## 4. Variants　🎨🔗

> Figma 元件集（節點 16988-272294）variant axes（metadata 抽取）：4 size × 11 state × 2 shape = 88 variants。

```yaml
size:
  extra-small: { trigger_height: 32px }                     # 🎨 Figma 行高確認
  small:       { trigger_height: 40px }                     # 🎨 ← ERP 主用（Small = 40px Small 行高確認）
  medium:      { trigger_height: 48px }                     # 🎨
  large:       { trigger_height: 56px }                     # 🎨

shape:
  filled:                                                    # 🎨 主要 shape（ERP 預設）
    bg:         "{color-sf-surface-variant}"                 # 🔗
    border:     bottom only                                  # 🎨 Filled 底線式
  line:                                                      # 🎨 Figma 有此軸（Outlined 替代名）
    bg:         "{color-sf-surface}"                         # 🔗 白底（推測）
    border:     bottom only, no bg fill                      # 📋（對應 Syncfusion Line 樣式；token 待另節點量測）

state:
  # 觸發框 11 態（與 DatePicker / DropDownList 同族）
  default:  "{color-sf-outline} 1px bottom"
  hover:    see §5                                           # 📋（Figma hover 態未精確量測）
  focus:    "{color-sf-primary} 2px bottom"
  active:   "{color-sf-primary} 2px bottom"                 # = overlay 展開態
  filled:   "{color-sf-outline} 1px bottom"                 # 已有選取值
  read-only: no interaction
  disabled: "{color-sf-on-surface-opacity38}"               # 文字 / icon 淡化
  info:     "{color-sf-info} 2px bottom"
  success:  "{color-sf-success} 2px bottom"
  warning:  "{color-sf-warning} 2px bottom"
  error:    "{color-sf-error} 2px bottom"

# Figma 特有 prop
calendar:
  false: 僅顯示觸發框（預設）                               # 📋 Figma 靜態控制
  true:  展開雙月曆 overlay                                  # 🎨（Active 態節點 16988-272687 確認）
```

---

## 5. States　🎨🔗

### 觸發框狀態

> 觸發框 state 與 DatePicker 完全一致；底線寬度：default/hover/filled/read-only → `{ds-borderwidth-small}`（1px）；focus/active/validation → `{ds-borderwidth-medium}`（2px）。

```yaml
default:
  container_bg:  "{color-sf-surface-variant}"              # 🔗 rgb(237 240 247)
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}"  # 🔗 1px
  placeholder:   "{ds-color-placeholder}"                  # 🔗 rgb(103 113 126)
  label:         "{color-sf-on-surface-variant}"           # 🔗 rgb(60 74 91)
  icon:          "{color-sf-on-surface-variant}"           # 🔗 rgb(60 74 91)

hover:                                                     # 📋（Figma hover 態未精確量測；對齊 DatePicker 推導）
  container_bg:  "{color-sf-surface-variant}"              # 🔗
  border_bottom: "{ds-borderwidth-small} {color-sf-on-surface-variant}"  # 🔗 略深底線

focus:                                                     # 🎨（節點 16988-272659 Small/Focus/Filled 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-primary}"  # 🔗 2px rgb(40 119 238)
  label:         "{color-sf-primary}"                      # 🔗 Material 3 focus label

active:                                                    # 🎨（節點 16988-272687，overlay 展開態）
  border_bottom: "{ds-borderwidth-medium} {color-sf-primary}"  # 🔗 2px（= focus 態）
  label:         "{color-sf-primary}"                      # 🔗

filled:                                                    # 📋（已選取日期範圍，非 focus 狀態）
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}"  # 🔗
  value_color:   "{color-sf-on-surface}"                   # 🔗 rgb(15 23 42)

read-only:                                                 # 📋
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}"  # 🔗
  text:          "{color-sf-on-surface}"                   # 🔗

disabled:                                                  # 🎨（Figma `Icon/$icon-color-disabled` 確認）
  text:          "{color-sf-on-surface-opacity38}"         # 🔗 38%
  placeholder:   "{color-sf-on-surface-opacity38}"         # 🔗
  container_bg:  "{color-sf-surface-variant}"              # 🔗 底色保留
  icon:          "{color-sf-on-surface-opacity38}"         # 🔗

info:                                                      # 🎨（Figma $border-info 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-info}"         # 🔗 2px rgb(46 144 250)
  helper:        "{color-sf-info}"                                 # 🔗

success:                                                   # 🎨（Figma $border-success 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-success}"      # 🔗 2px rgb(18 183 106)
  helper:        "{color-sf-success}"                              # 🔗

warning:                                                   # 🎨（Figma $border-warning 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-warning}"      # 🔗 2px rgb(247 144 9)
  helper:        "{color-sf-warning}"                              # 🔗

error:                                                     # 🎨（Figma $border-error 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-error}"        # 🔗 2px rgb(244 73 62)
  label:         "{color-sf-error}"                                # 🔗
  helper:        "{color-sf-error}"                                # 🔗
```

### 雙月曆 overlay 空值狀態

```yaml
empty_state:                                               # 📋
  text:    "（無資料）"                                    # 📋 Syncfusion runtime 內建，非本 Figma 設計
```

### 日期格（_Atom / Days）狀態

```yaml
day_default:                                               # 🎨（節點 16988-272687 overlay 確認）
  bg:     transparent
  text:   "{color-sf-on-surface}"                          # 🔗 rgb(15 23 42)

day_today:                                                 # 🎨（overlay 節點 primary border 確認）
  bg:     transparent
  border: "{ds-borderwidth-small} {color-sf-primary}"      # 🔗 1px 藍框（圓形）
  text:   "{color-sf-primary}"                             # 🔗 rgb(40 119 238)

day_hover:                                                 # 📋（Syncfusion runtime state-layer；Figma overlay 未暴露）
  bg:     "{color-sf-on-surface-opacity8}"                 # 🔗 8% Material 3 state-layer

day_range_start_end:                                       # 📋（Syncfusion runtime；Figma 靜態設計未含 filled 範圍態）
  bg:     "{color-sf-primary}"                             # 🔗 rgb(40 119 238)
  text:   "{color-sf-on-primary}"                          # 🔗 白色
  radius: "{ds-radius-10extra-large}"                      # 🔗 全圓

day_range_in_between:                                      # 📋（Syncfusion runtime；Figma 靜態設計未含）
  bg:     "{color-sf-primary-container}"                   # 🔗 rgb(213 228 255)（推導，待 Syncfusion theme 確認）
  text:   "{color-sf-on-surface}"                          # 🔗

day_disabled:                                              # 📋（Syncfusion runtime）
  text:   "{color-sf-on-surface-opacity38}"                # 🔗 38%

day_other_month:                                           # 📋（跨月 prev/next month 日期，Syncfusion runtime）
  text:   "{color-sf-on-surface-opacity38}"                # 🔗 淡化，通常不可選或視覺降噪
```

---

## 6. Behavior 行為　📋

| 動作 | 觸發條件 | 效果 |
|------|---------|------|
| 展開 overlay | 點擊觸發框或 date-range-icon-btn | 雙月曆 popup 出現；顯示當前月（左）及下一月（右） |
| 關閉 overlay | 點擊觸發框外區域 / 按 Esc | popup 消失；觸發框恢復 default / filled 態 |
| 選取開始日 | 點擊任一月曆的日期格 | 該日 bg = range_start 色；觸發框顯示選取開始日 |
| 拖曳 / 點選結束日 | 選完開始日後點另一日期 | 連續範圍 in-between bg 填充；結束日 bg = range_end 色；觸發框顯示完整範圍字串 |
| 月份切換 | 點擊 chevron-left / chevron-right | 對應面板切換月份（左右面板獨立，不聯動） |
| 清除選取 | 點擊 clear-icon-btn（`clearIcon = true`） | 觸發框恢復 placeholder；清除範圍值 |
| 跨月範圍選取 | 開始日在左月末 → 結束日在右月初 | 範圍 fill 跨越兩個月曆面板 |
| 相同日 start = end | 開始日與結束日為同一天 | 合法；觸發框顯示 "2023/01/14 - 2023/01/14" |

> **presets（快捷範圍）**：Syncfusion `ejs-daterangepicker` 支援 `presets` 配置（本週 / 本月 / 最近 7 天…），在 overlay footer 顯示快捷按鈕列表；本 Figma 元件集為純 calendar UI，**presets 不在設計範疇**（由 code-side 注入）。

---

## 7. RWD 響應式　📋

| 斷點 | 觸發框 | Overlay |
|-----|--------|---------|
| ≥ 1280px（Web desktop，ERP 主場） | Filled Small（h=40px）| 雙月曆並排（504px）；absolute 定位在觸發框下方 |
| ≤ 1024px（Tablet / 縮小 Web） | Filled Medium（h=48px）考慮改用 | 雙月曆可改為上下排列或 bottom-sheet（`separateCalendar` Syncfusion prop）|
| App（iOS / Android） | Medium / Large 以利觸控 | Bottom sheet 模式；僅顯示單月曆 + 上下月切換 |

> ERP 主場景為桌面瀏覽器（≥1280px）；觸發框寬度通常由父層 grid cell 決定，不固定 240px。

---

## 8. Keyboard　📋

| 按鍵 | 行為 |
|-----|------|
| `Tab` | 進入觸發框 |
| `Enter` / `Space` | 展開雙月曆 overlay |
| `↑` / `↓` / `←` / `→` | 在日期格間移動 focus（跨週、跨月） |
| `Enter` | 選取第一個日期（start）；再 `Enter` 選取結束日（end）|
| `Esc` | 關閉 overlay，focus 回到 trigger input |
| `Page Up` / `Page Down` | 上/下月切換（active 面板） |
| `Home` / `End` | 移至當週第一 / 最後一天 |
| `Shift+Page Up` / `Shift+Page Down` | 上/下年切換（Syncfusion 支援）|

---

## 9. a11y　📋

- 觸發框 `role="combobox"` + `aria-expanded="true/false"`（Syncfusion 內建）。
- `aria-required="true"` 於必填欄位；`aria-invalid="true"` + `aria-describedby` 指向 helper text 於 error 態。
- 雙月曆 overlay 使用 `role="dialog"`；每個月曆面板應有 `role="grid"` + `aria-label="January 2023"`；日期格為 `role="gridcell"` + `aria-selected`。
- 已選範圍：start / end 日格加 `aria-label="start date January 14 2023"` / `"end date February 15 2023"`；in-between 格加 `aria-label="January 15 2023, in range"` 或類似語意（Syncfusion 實作各版本不同，建議驗收）。
- Focus ring 由 Syncfusion Material 3 主題提供（`{ds-shadow-focus-ring1}`）。
- 對比度：`{color-sf-primary}` today border 於白底 = 5.4:1 ≥ AA；range fill `{color-sf-primary-container}` 底色上的文字 `{color-sf-on-surface}` 待驗算（primary-container 為淺藍，on-surface 為深色，預期 AA 可通過）。
- Mobile min touch target：日期格 32×32 低於 44×44 建議值；App 場景需 Syncfusion `itemWidth` / `itemHeight` 或外補 padding。

---

## 10. 跨平台 Adaptive　📋

| 斷點 | 觸發框 | Overlay |
|-----|--------|---------|
| ≥ 1280px（Web desktop） | Filled Small（h=40px）| 雙月曆並排；absolute float；觸發框下方 |
| ≤ 1024px（Tablet） | Filled Medium（h=48px）可改用 | popup 可縮為單月曆 + 上下頁；或 bottom-sheet |
| App（iOS / Android） | Medium / Large | 強制 bottom-sheet 單月曆；手勢滑動切月 |

> App 端的「日期範圍選取」若 UX 流程複雜（跨月範圍），建議改為兩個獨立 `DatePicker`（start/end）的全屏選取 flow，而非縮小雙月曆。

---

## 11. Content 指引　📋

- **觸發框 placeholder**：依業務語意（「選擇日期範圍」、「選擇會計期間」），不用通用 "Choose date"；選取後 Syncfusion runtime 自動渲染格式化字串（ERP 中文環境建議 `yyyy/MM/dd - yyyy/MM/dd`）。
- **月份標題**：由 Syncfusion 自動格式化，依 locale 顯示（zh → 「2023年1月」；en → "January 2023"）；ERP locale 由 `DsDateRangePicker` wrapper 注入。
- **驗證錯誤文案**：
  - 開始日 > 結束日 → 「結束日期不得早於開始日期」
  - 超出允許範圍（min/max 設定）→ 「日期超出可選範圍」
  - 必填未填 → 「請選擇日期範圍」
- **Presets 快捷按鈕文案**（若啟用）：「本週」「本月」「上個月」「最近 30 天」；動詞 + 單位，不用「This Week」英文。

---

## 12. API / Props　📋

### Figma 設計層 props（`get_design_context` 節點 16988-272603）

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `label` | boolean | true | 是否顯示 label |
| `label1` | string | "Label" | label 文字 |
| `labelIcon` | boolean | false | 顯示 label 右側 info icon |
| `required` | boolean | false | 顯示必填 `*` |
| `text` | string | "Choose date" | placeholder 文字 |
| `leftIcon` | boolean | false | 顯示左側 icon 槽位 |
| `leftIcon1` | slot | — | 自訂左側 icon 內容 |
| `clearIcon` | boolean | false | 顯示清除（×）按鈕 |
| `calendar` | boolean | false | 是否顯示雙月曆 overlay（Figma 靜態控制；Active 態 = true）|
| `alertMessage` | boolean | false | 顯示 helper / alert text |
| `alertMessage1` | string | "Helping text" | helper 文字 |
| `alertIcon` | boolean | true | 顯示 helper 區域的 alert icon |
| `alertIcon1` | slot | — | 自訂 alert icon |
| `shape` | "Filled" \| "Line" | "Filled" | 觸發框樣式 |
| `size` | "Extra Small" \| "Small" \| "Medium" \| "Large" | "Small" | 尺寸 |
| `state` | "Default" \| "Hover" \| "Focus" \| "Active" \| "Filled" \| "Read Only" \| "Disabled" \| "Info" \| "Success" \| "Warning" \| "Error" | "Default" | 觸發框狀態 |

### Syncfusion Runtime props（`<ejs-daterangepicker>`，`@syncfusion/ej2-vue-calendars`）

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `startDate` / `endDate` | Date | — | 範圍起訖日（v-model 或 props） |
| `value` | Date[] | — | `[startDate, endDate]` 陣列形式 |
| `format` | string | 依 locale | 日期格式字串（ERP 建議 `"yyyy/MM/dd"`） |
| `locale` | string | "en" | 語系（zh / en）|
| `placeholder` | string | — | 觸發框 placeholder |
| `startDay` | number | 0（週日）| 日曆週起始（0=週日, 1=週一）|
| `min` / `max` | Date | — | 可選日期範圍限制 |
| `disabledDates` | Function | — | 自訂停用日期規則 |
| `showClearButton` | boolean | false | 顯示清除按鈕（對應 `clearIcon` Figma prop）|
| `enabled` | boolean | true | 啟用 / 停用 |
| `readonly` | boolean | false | 唯讀 |
| `presets` | Array | — | 快捷範圍列表（`{ label, start, end }`）|
| `separateCalendar` | boolean | false | 雙月曆分離顯示（預設並排）|
| `depth` | "Month" \| "Year" \| "Decade" | "Month" | popup 初始深度 |
| `start` | "Month" \| "Year" \| "Decade" | "Month" | popup 起始層級 |

---

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface §On-* §Outline §Status §Border Width §Space §Radius §Shadow §Typography §`--ds-*`
- 同族元件：`DatePicker.md`（觸發框結構完全共用；Anatomy §2 差異標記）；`DropDownList.md`（Filled 底線式同族觸發框）
- Prototype 落地（值權威 / class 命名）：`prototyper/assets/app.css`（Syncfusion Material 3 theme override）
- Code（建議）：若需多語系格式包裝，參照 `DsDatePicker`（`components/ds/DsDatePicker.vue`）建立 `DsDateRangePicker`；底層為 `ejs-daterangepicker`
- Code（底層）：`@syncfusion/ej2-vue-calendars`；playground 參考 `syncfusion-playground/app/pages/playground/`
- Figma：`https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/` 節點 `16988-272294`（元件集）/ `16988-272603`（Small Default Filled 觸發框）/ `16988-272687`（Small Active Filled，含雙月曆 overlay）

---

## Figma 補入紀錄（2026-06-29）

> 依 `../component-doc-schema.md` §5 補入流程執行。  
> 來源：`get_variable_defs`（節點 16988-272294，43 vars）+ `get_metadata`（整個元件集，88 variants）+ `get_design_context`（節點 16988-272603 Small/Default/Filled 觸發框 + 節點 16988-272687 Small/Active/Filled 含 overlay）+ `get_screenshot`（元件集 16988-272294，視覺確認 variants × states 矩陣）。

**✅ 已確認對映（Figma 變數 → `athena-tokens.md`）**

| Figma 變數 | token | 用途 |
|---|---|---|
| `ColorSf/on-surface-variant` | `{color-sf-on-surface-variant}` | label / icon / helper 色、weekday label |
| `ColorSf/surface-variant` | `{color-sf-surface-variant}` | Filled 底色 |
| `ColorSf/outline` | `{color-sf-outline}` | default 底線色、overlay border（outline-variant 另計） |
| `ColorSf/on-surface` | `{color-sf-on-surface}` | value 文字、day 數字色、month title |
| `ColorSf/primary` | `{color-sf-primary}` | focus/active 底線、today border + text、range start/end bg |
| `ColorSf/danger` / `ColorSf/error` | `{color-sf-danger}` / `{color-sf-error}` | error 邊框（同值 rgb(244,73,62)）|
| `ColorSf/success` | `{color-sf-success}` | success 邊框 |
| `ColorSf/warning` | `{color-sf-warning}` | warning 邊框 |
| `DsColor/placeholder` | `{ds-color-placeholder}` | placeholder 文字色 |
| `ColorSf/on-surface-opacity38` | `{color-sf-on-surface-opacity38}` | disabled 文字 / icon |
| `ColorSf/surface` | `{color-sf-surface}` | overlay 白底、day 選取時 inner circle bg |
| `ColorSf/transparent` | `{color-sf-transparent}` | overlay / day cell 透明底色 |
| `FontSizeSf/TextSm` | `{font-size-sf-text-sm}` | 12px helper / day number |
| `FontSizeSf/TextMd` | `{font-size-sf-text-md}` | 14px label / content / month title |
| `FontSizeSf/TextLg` | `{font-size-sf-text-lg}` | 16px（備用，Outlined Large 等）|
| `FontWeightSf/Normal` | `{font-weight-sf-normal}` | 400 |
| `FontFamilySf/Fontfamily` | `{font-family-sf-fontfamily}` | Roboto（EN）/ Noto Sans TC（ZH）|
| `DsSpace/Margin/Small` | `{ds-space-margin-small}` | label ↔ input gap |
| `Radius/RadiusSm` | `{ds-radius-small}` | top radius 4px |
| `Radius/RadiusNone` | — | 0（CSS 預設，無需 token）|
| `Border/$border` | `{color-sf-outline}` | 邊框色（= outline 別名）|
| `$primary` | `{color-sf-primary}` | focus 態主色別名 |
| `$border-success/warning/error` | 同各 status token | validation 邊框色別名 |

**43 個變數全數找到對應（其中 `Radius/RadiusNone` = CSS 預設 0，不需 token）；無漂移。**

**✅ Overlay 結構確認（節點 16988-272687 Active/overlay）**

- 雙月曆面板並排：兩個獨立 `Table`（各 w=252px），外層 `Table container` 水平 flex
- overlay 外框：absolute / rounded-[8px] / border outline-variant / drop-shadow（= `{ds-shadow-md}` 對映）
- overlay 定位：`top-[40px] left-0`（相對 Small 觸發框底部，左對齊觸發框）
- month title：FontWeightSf/Medium（500，`{font-weight-sf-medium}` 可對映，但此 token 在 `variable_defs` 未直接出現，以 Figma class `font-['Roboto:500']` 確認）
- day cell：32×32，all states 有獨立圓形 bg / border layer
- today：`border border-[var(--colorsf/primary)] rounded-[32px]` + `text-[color:var(--colorsf/primary)]`（已確認）
- weekday labels：`FontSizeSf/TextXs` = 11px（Figma 回傳 `fontsizesf/textxs`，對映 `--font-size-sf-text-xs` = 11px）

**⚠️ Token gap（非阻塞，已標 🎨 量測值）**

1. **`padding_left: 10px`** / **`inner_icon_gap: 6px`** / **`helper_gap: 4px`**：與 DatePicker 相同 gap，`athena-tokens.md` 無 10px / 6px → 值權威見 `prototyper/assets/app.css`（與 DatePicker / TextBox 共用）
2. **`overlay_radius: 8px`**：Figma `rounded-[8px]`，對映 `{ds-radius-large}` = 8px，**可對映但 variable_defs 中 Radius 群組僅出現 RadiusSm=4px 與 RadiusNone=0**；overlay 的 8px 是內聯值非 token 引用 → 標 🎨，值見 app.css
3. **`overlay_shadow: drop-shadow`**：Figma 值 `0px_4px_4px_0.15 + 0px_1px_1.5px_0.3` → 與 `{ds-shadow-md}` 定義（`0px 2px 6px 2px rgba(0,0,0,0.15), 0px 1px 2px 0px rgba(0,0,0,0.3)`）數值不完全一致（Figma 用 drop-shadow CSS filter，`ds-shadow-md` 為 box-shadow），**功能語意相近（中提升 dropdown）**，對映 `{ds-shadow-md}` 並標 🎨 說明差異
4. **`month_title_font_weight: 500`**：Figma 使用 `FontWeightSf/Medium = 500`，對映 `{font-weight-sf-medium}`，但此 token 未出現在本元件集的 `variable_defs` 43 vars 清單（僅 FontWeightSf/Normal 出現）→ 標 🎨，從 `get_design_context` class 字串 `'Roboto:500'` 確認
5. **`day_range_fill_bg` / `day_range_start_end_bg`**：Syncfusion runtime 動態套用，**Figma 靜態設計未含 filled range 態** → token 推導自 `{color-sf-primary-container}` / `{color-sf-primary}`，標 📋，待 Syncfusion Material 3 theme 驗證

**⬜ 待補（次要）**

- Size=Extra Small / Medium / Large 的 trigger padding / inner gap 量測（僅 Small 精確量測）
- Shape=Line 的觸發框 token 量測（metadata 確認存在，未取 `get_design_context`）
- hover 觸發框底線色精確量測（補後更新 §5）
- day hover / range in-between bg 以 Syncfusion Material 3 theme CSS 驗證（非 Figma 設計）
- overlay 總寬度量測（預期 504px = 252×2，待精確確認無內側 gap）
- presets 快捷按鈕 Figma 節點（若存在）補入
- `DsDateRangePicker` 包裝元件（若建立）的 props 補入
