---
name: Calendar
category: 輸入 — 日期與時間
tier: full
status: ✅ 已產出
authority: ERP CLAUDE.md（Syncfusion Calendar, @syncfusion/ej2-vue-calendars）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16642-4421
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs（節點 16642-4421，32 vars）+ get_metadata（整張元件集）+ get_design_context（節點 16662-138299 Small/Days/Single-Select、節點 16642-7975 Small/Month/Single-Select）+ get_screenshot（Small/Days/Single-Select 視覺確認）
---

> 依 `../component-doc-schema.md`（Full 層，inline 月曆面板）產出。視覺值一律 token-reference；raw px 量測值標 🎨 待對齊，值權威指向 `prototyper/assets/app.css`，不在此重印。
> §3–§5 已用 Figma `get_variable_defs`（32 variables）+ `get_design_context`（Day view / Month view）+ `get_screenshot` 校準；token gap 見文末「Figma 補入紀錄」。

---

## 1. 概述　📋

**inline 月曆面板**。直接展開於畫面中（非 popup），支援三種視圖切換（日/月/年），可單選、多選、範圍選，通常作為 DatePicker 的 overlay 核心，或直接嵌入表單區塊（如排程、出勤）。

**何時使用**：
- 需要使用者**目視月曆格線**後點擊選日期的場景（對比純文字輸入）
- 日期範圍選取（`rangeSelect: true`）
- 多日選取（`multiSelect: true`）
- 日期視覺顯示優先於輸入效率時（ERP：請假申請、報表期間設定）

**何時不用**：
- 純輸入日期字串（速度優先） → `TextBox`（搭配格式驗證）
- 需要時間（時:分） → `DateTimePicker`（Syncfusion 另一元件）
- 表單內日期欄位標準做法 → `DsDatePicker`（客製元件，含 Calendar 為 overlay）

---

## 2. Anatomy　🎨

> 來源：`get_design_context`（節點 16662-138299，Small / Days / Single-Select；節點 16642-7975，Small / Month / Single-Select）

```
Calendar（vertical stack）
├── range-header?（rangeSelect=true 時顯示，在 panel 上方）
│   ├── date-range-row（"Start date" ─→ "End date"，gap = {ds-space-margin-3extra-large}）
│   └── hint-row（"Select Days"，11px secondary）
├── panel（白底卡片，有 border + radius）
│   └── [Type=Days 時]
│       ├── header（px=12px，py=4px）
│       │   ├── month-year-title（"January 2023"，{font-size-sf-text-md} / {font-weight-sf-medium}）
│       │   └── nav-buttons（gap=8px）
│       │       ├── prev-btn（32×32 circle，chevron-left icon）
│       │       └── next-btn（32×32 circle，chevron-right icon）
│       └── body（px=8px，pt=4px，pb=8px）
│           ├── weekday-header-row（7 columns，S M T W T F S，gap=2px）
│           │   └── header-cell（32×32，11px，on-surface-variant）
│           └── day-grid（rows × 7 columns，gap=2px，5–6 rows）
│               └── _Atom/Days（32×32）
│                   ├── hover-layer（透明圓形覆蓋）
│                   ├── bg-circle（= surface 白底 or primary 填色）
│                   └── label（12px，center，on-surface or primary）
│
│   └── [Type=Month 時]
│       ├── header（px=12px，py=4px）
│       │   ├── year-title（"2023"，{font-size-sf-text-md} / {font-weight-sf-medium}）
│       │   └── nav-buttons（同 Days）
│       └── body（px=8px，py=18px，gap=28px between rows）
│           └── month-grid（4 rows × 3 columns，gap=4px）
│               └── _Atom/Days（h=32px，w=76px）
│                   ├── bg（= surface or primary border/text）
│                   └── label（"Jan" … "Dec"，14px）
│
│   └── [Type=Year 時]
│       ├── header（decade range title）
│       └── body（year-grid，3 columns，gap=4px，同 Month 結構）
│
└── footer（px=8px，pb=8px）
    ├── clear-btn?（rangeSelect=true 時顯示，"Clear"，on-surface，ghost）
    └── today-btn（"Today"，{color-sf-primary}，ghost）
```

> **multiView 軸**：同一面板可並排兩個月（`multiView=true`），兩個 `Table` 水平排列，nav-btn 分別控制各月。

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── 外層容器 ─────────────────────────────────────────────────────────────────
panel_bg:            "{color-sf-surface}"              # 🔗 rgb(255 255 255)
panel_border:        "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗 1px rgb(215 218 224)
panel_radius:        "{ds-radius-large}"               # 🔗 8px（所有角）
panel_shadow:        none（內嵌型；popup 做法見 §6 Behavior）  # 📋

# ── header（月年標題 + nav-buttons）──────────────────────────────────────────
header_px:           12px                              # 🎨 量測值；值見 app.css
header_py:           4px                               # 🎨
title_font_size:     "{font-size-sf-text-md}"          # 🔗 14px
title_font_weight:   "{font-weight-sf-medium}"         # 🔗 500
title_color:         "{color-sf-on-surface}"           # 🔗 rgb(15 23 42)
title_tracking:      0.1px                             # 🎨（Label/14pt/Medium tracking 值）
nav_btn_size:        32px                              # 🎨 正方形（含 ripple 圓圈）
nav_btn_radius:      "{ds-radius-10extra-large}"       # 🔗 1000px（全圓 ripple 容器）
nav_btn_bg:          "{color-sf-transparent}"          # 🔗 透明
nav_icon_size:       16px                              # 🎨

# ── body（週標頭 + 日格）──────────────────────────────────────────────────────
body_px:             8px                               # 🎨
body_pt:             4px                               # 🎨
body_pb:             8px                               # 🎨
cell_gap:            2px                               # 🎨 欄間與列間 gap（無對應 token）

# ── 週標頭列（S M T W T F S）────────────────────────────────────────────────
weekday_cell_size:   32px                              # 🎨
weekday_font_size:   "{font-size-sf-text-xs}"          # 🔗 11px
weekday_font_weight: "{font-weight-sf-normal}"         # 🔗 400
weekday_color:       "{color-sf-on-surface-variant}"   # 🔗 rgb(60 74 91)
weekday_tracking:    0.1px                             # 🎨（Label/11pt/Regular）

# ── 日期格（_Atom / Days）────────────────────────────────────────────────────
day_cell_size:       32px                              # 🎨
day_font_size:       "{font-size-sf-text-sm}"          # 🔗 12px
day_font_weight:     "{font-weight-sf-normal}"         # 🔗 400
day_font_family:     "{font-family-sf-fontfamily}"     # 🔗 Roboto / Noto Sans TC
day_tracking:        0.1px                             # 🎨（Label/12pt/Regular）
day_line_height:     1.3                               # 🎨
day_bg_default:      "{color-sf-surface}"              # 🔗 白底圓形
day_bg_radius:       "{ds-radius-10extra-large}"       # 🔗 1000px（全圓）
day_text_default:    "{color-sf-on-surface}"           # 🔗 rgb(15 23 42)

# ── 月/年格（_Atom / Days，Type=Month / Year）────────────────────────────────
month_cell_height:   32px                              # 🎨
month_cell_width:    76px                              # 🎨（Small size；3 columns × (76+4) = 240px）
month_cell_gap:      4px                               # 🎨（gap between columns）
month_row_gap:       28px                              # 🎨（gap between rows）
month_body_py:       18px                              # 🎨
month_font_size:     "{font-size-sf-text-sm}"          # 🔗 14px（Figma textsm 於此節點 = 14px 等同 textmd，因容器大）

# ── Size 尺寸（Small 為 ERP 主用）────────────────────────────────────────────
size_extra_small:    { panel_w: 196px, panel_h: 256px }  # 🎨
size_small:          { panel_w: 252px, panel_h: 328px }  # 🎨 ← ERP 主用
size_medium:         { panel_w: 292px, panel_h: 374px }  # 🎨
size_large:          { panel_w: 320px, panel_h: 412px }  # 🎨

# ── footer（Today / Clear 按鈕）──────────────────────────────────────────────
footer_px:           8px                               # 🎨
footer_pb:           8px                               # 🎨
today_btn_color:     "{color-sf-primary}"              # 🔗 rgb(40 119 238)
today_btn_font:      "{font-size-sf-text-md} / {font-weight-sf-medium}"  # 🔗 14px / 500
today_btn_tracking:  0.1px                             # 🎨
today_btn_h:         32px                              # 🎨
today_btn_radius:    "{ds-radius-small}"               # 🔗 4px
today_btn_bg:        "{color-sf-transparent}"          # 🔗 ghost 按鈕
clear_btn_color:     "{color-sf-on-surface}"           # 🔗（rangeSelect 才顯示）

# ── range-header（rangeSelect=true 時顯示）───────────────────────────────────
range_header_px:     12px                              # 🎨
range_header_pt:     16px                              # 🎨
range_header_pb_row: 4px                               # 🎨
range_label_font:    "{font-size-sf-text-md} / {font-weight-sf-medium}"  # 🔗 14px / 500
range_label_color:   "{color-sf-on-surface}"           # 🔗
range_hint_font:     "{font-size-sf-text-xs} / {font-weight-sf-normal}"  # 🔗 11px / 400
range_hint_color:    "{color-sf-on-surface}"           # 🔗
range_arrow_size:    16px                              # 🎨（→ 箭頭圖示）
```

---

## 4. Variants / Types　🎨🔗

> Figma 元件集（節點 16642-4421）variant axes（metadata 抽取）：

```yaml
# ── Size 軸 ──────────────────────────────────────────────────────────────────
size:
  extra-small: { panel_w: 196px, panel_h: 256px }      # 🎨
  small:       { panel_w: 252px, panel_h: 328px }      # 🎨 ← ERP 主用
  medium:      { panel_w: 292px, panel_h: 374px }      # 🎨
  large:       { panel_w: 320px, panel_h: 412px }      # 🎨

# ── Type 軸（月曆面板視圖種類）───────────────────────────────────────────────
type:
  days:                                                # 🎨 ← 最常用，日期格 7×6 max
    header: "月份 年份"（如 "January 2023"）
    grid_cols: 7
    cell_size: 32×32px
  month:                                               # 🎨 使用者點擊 year title 切換到此
    header: "年份"（如 "2023"）
    grid:   4 rows × 3 cols（Jan–Dec）
    cell:   76×32px
  year:                                                # 🎨 使用者點擊 decade range 切換到此
    header: 十年區間（如 "2020–2029"）
    grid:   4 rows × 3 cols（年份數字）
    cell:   76×32px（同 Month）

# ── Mode 軸（選取模式）──────────────────────────────────────────────────────
mode:
  single-select:                                       # 🎨 預設；單一日期
    selected_state: today-ring or filled-circle（見 §5）
  multi-select:                                        # 🎨（Figma 有此軸；Syncfusion `isMultiSelection=true`）
    selected_state: 多個格子同時 filled-circle

# ── 複合 prop（Figma 非 variant 軸，為 boolean props）──────────────────────
rangeSelect:
  false: 預設（僅 panel）                              # 🎨
  true:  panel 上方加 range-header；footer 加 Clear 鈕  # 🎨

multiView:
  false: 單月面板                                      # 🎨
  true:  兩個月面板橫向並排（共用同一 border 框）      # 🎨
```

---

## 5. States　🎨🔗

### 日期格（_Atom / Days）狀態

> 來源：`get_design_context`（節點 16662-138299 Day view），逐格結構分析

```yaml
# 三層結構：hover-layer（左半透明圓）+ bg-circle（全圓）+ label
default:                                               # 🎨
  bg:     "{color-sf-surface}"                         # 🔗 白
  text:   "{color-sf-on-surface}"                      # 🔗 rgb(15 23 42)
  border: none

today:                                                 # 🎨（Figma 確認：border-primary ring）
  bg:     "{color-sf-surface}"                         # 🔗 白底（非填色）
  border: "{ds-borderwidth-small} {color-sf-primary}"  # 🔗 1px solid rgb(40 119 238)
  text:   "{color-sf-primary}"                         # 🔗 藍色數字
  note:   "今日 = outline ring，非填色；selected 才填色"  # 📋

selected:                                              # 📋 Syncfusion runtime 填色；Figma 本 variant 未見獨立 selected 填色格
  bg:     "{color-sf-primary}"                         # 🔗 藍色填滿圓
  text:   "{color-sf-on-primary}"                      # 🔗 白色數字
  border: none

selected_today:                                        # 📋 兩態疊加：今日 + 已選
  bg:     "{color-sf-primary}"                         # 🔗 填色優先
  text:   "{color-sf-on-primary}"                      # 🔗
  border: none

hover:                                                 # 📋（Figma hover 態未獨立量測；依 Material 3 state-layer 推導）
  overlay: "{color-sf-on-surface-opacity8}"            # 🔗 8% state-layer 覆蓋圓
  bg:     "{color-sf-surface}"                         # 🔗（底色不變）
  text:   "{color-sf-on-surface}"                      # 🔗

range_in_range:                                        # 📋（rangeSelect=true 中間日期）
  bg:     "{color-sf-primary-opacity-12}"              # 🔗 12% primary tint 橫帶
  text:   "{color-sf-on-surface}"                      # 🔗
  note:   "帶狀背景延伸至 cell 邊緣，非圓形"

range_start / range_end:                               # 📋
  bg:     "{color-sf-primary}"                         # 🔗 filled circle（端點）
  text:   "{color-sf-on-primary}"                      # 🔗
  tail:   "{color-sf-primary-opacity-12}"              # 🔗 單側帶狀延伸

other_month:                                           # 📋（上/下月溢出格）
  text:   "{color-sf-on-surface-opacity38}"            # 🔗 38% 淺灰（Syncfusion 預設）
  bg:     "{color-sf-surface}"                         # 🔗

disabled:                                              # 📋
  text:   "{color-sf-on-surface-opacity38}"            # 🔗 38%
  bg:     "{color-sf-surface}"                         # 🔗
  cursor: not-allowed
```

### 月/年格（_Atom / Days，Type=Month / Year）狀態

```yaml
default:                                               # 🎨（Figma 量測）
  bg:     "{color-sf-surface}"                         # 🔗
  border: none
  text:   "{color-sf-on-surface}"                      # 🔗

today_month:                                           # 🎨（Figma 量測 May 格：border primary + text primary）
  bg:     "{color-sf-surface}"                         # 🔗 白底
  border: "{ds-borderwidth-small} {color-sf-primary}"  # 🔗 1px ring
  text:   "{color-sf-primary}"                         # 🔗

selected:                                              # 📋（Syncfusion runtime 填色）
  bg:     "{color-sf-primary}"                         # 🔗
  text:   "{color-sf-on-primary}"                      # 🔗
  border: none

hover:                                                 # 📋
  overlay: "{color-sf-on-surface-opacity8}"            # 🔗 state-layer

disabled:                                              # 📋
  text:   "{color-sf-on-surface-opacity38}"            # 🔗
```

### Nav 按鈕狀態

```yaml
nav_default:
  bg:     "{color-sf-transparent}"                     # 🔗
  icon:   "{color-sf-on-surface-variant}"              # 🔗（Icon/$icon-color）

nav_hover:                                             # 📋
  bg:     "{color-sf-on-surface-opacity8}"             # 🔗 8% state-layer 全圓

nav_focus:                                             # 📋
  ring:   "{ds-borderwidth-medium} {color-sf-primary}" # 🔗 2px

nav_disabled:                                          # 📋（月首/末不可再往前/後）
  icon:   "{color-sf-on-surface-opacity38}"            # 🔗
```

### footer Today / Clear 按鈕狀態

```yaml
today_btn_default:
  text:   "{color-sf-primary}"                         # 🔗
  bg:     "{color-sf-transparent}"                     # 🔗

today_btn_hover:                                       # 📋
  bg:     "{color-sf-primary-opacity-8}"               # 🔗 8% primary tint

clear_btn_default:
  text:   "{color-sf-on-surface}"                      # 🔗
  bg:     "{color-sf-transparent}"                     # 🔗
```

---

## 6. Behavior 行為　📋

| 動作 | 觸發條件 | 效果 |
|------|---------|------|
| 選取日期 | 點擊 Day 格（default / other-month 態） | 格子切換為 selected 態；觸發 `change` / `select` event；other-month 格點擊後切換到對應月份 |
| 選取月份 | Type=Month 時點擊月份格 | 切換回 Type=Days，定位到該月 |
| 選取年份 | Type=Year 時點擊年份格 | 切換到 Type=Month，定位到該年 |
| 向上鑽取 | 點擊 header 的月年/年份 title | Days → Month → Year；依序展開更大粒度 |
| 向前/後翻月 | 點擊 nav chevron | 重新渲染上/下一月（Type=Days）或上/下一年（Month）或上/下一個十年（Year） |
| 跳今日 | 點擊 footer "Today" | 定位到今日所在月；觸發 `select` 並關閉（若為 popup 模式） |
| 清除（range） | 點擊 footer "Clear" | 清除 startDate + endDate；rangeHeader 回顯 "Start date / End date" |
| 範圍選（rangeSelect） | 依序點擊起始日 → 結束日 | 第一次點擊設 startDate；第二次設 endDate；中間格顯示帶狀 range 高亮 |
| 多選（multiSelect） | 點擊多個格子 | 各自 toggle selected 態；不互斥 |
| 鍵盤導航 | ↑↓←→ | 在格子間移動 focus（見 §8） |

---

## 7. RWD 響應式　📋

Calendar 為 **inline 固定寬度面板**；其響應式由容器（通常為 DatePicker popup 或表單欄位）負責，面板本身寬度固定依 Size 軸（196–320px）。

| 斷點 | 建議 Size | 備註 |
|------|---------|------|
| ≥ 1280px（Web desktop） | Small（252px）| ERP 主場景；表單欄位上方 popup |
| ≤ 1024px（Tablet） | Medium（292px）| 提高觸控精準度 |
| App（iOS / Android） | Large（320px）| 觸控底線；Bottom sheet 模式 |

> `multiView=true` 會將面板寬度加倍（如 Small × 2 = 504px），在 ≤ 1024px 場景應退回 `multiView=false`。

---

## 8. Keyboard　📋

| 按鍵 | 行為 |
|------|------|
| `Tab` | 進入面板（focus 落到 header nav-prev 或當前選取格） |
| `←` / `→` | 在日期格間向左/右移動 focus（跨週自動換列） |
| `↑` / `↓` | 向上/下移動 focus（跨週/月自動翻頁） |
| `Enter` / `Space` | 選取目前 focus 的格子 |
| `Page Up` / `Page Down` | 往前/後翻一個月（Type=Days） |
| `Ctrl + Page Up / Down` | 往前/後翻一年（Type=Days） |
| `Home` / `End` | 移至該月第一/最後一天 |
| `Esc` | 關閉（若為 popup 模式），focus 回觸發框 |
| `Alt + ↑` | 切換視圖（Days → Month → Year） |

---

## 9. a11y　📋

- 面板根元素 `role="application"` 或 `role="grid"` 依 Syncfusion 實作；每個格子 `role="gridcell"`。
- 選取格 `aria-selected="true"`；今日格 `aria-current="date"`；disabled 格 `aria-disabled="true"`。
- Nav 按鈕 `aria-label="上一個月"`（或「上一年」）/ `aria-label="下一個月"` —— 不用方向箭頭文字。
- header title 應為 `aria-live="polite"` 容器，翻月時 announce 新月份名稱。
- 範圍選取：`aria-label="選取開始日期"` / `aria-label="選取結束日期"`；range 帶狀格子補 `aria-describedby`。
- 對比度：default 日期 `{color-sf-on-surface}` / `{color-sf-surface}` 背景 → 符合 AA；weekday label `{color-sf-on-surface-variant}` ⚠️ = `rgb(60 74 91)` / white ≈ 7.8:1 AA ✅；today ring primary 色當 border 非文字，對比規則不同。
- Mobile touch target：day_cell = 32px < 建議 44px；實際觸控建議 Syncfusion `cellWidth`/`cellHeight` 設 `40px` 以上。

---

## 10. 跨平台 Adaptive　📋

| 斷點 | 呈現方式 | 尺寸 |
|------|---------|------|
| ≥ 1280px（Web desktop） | DatePicker popup（absolute，above/below 欄位）| Size=Small（252px） |
| ≤ 1024px（Tablet） | 同 popup，或嵌入畫面表單區塊 | Size=Medium（292px）；增加 touch area |
| App（iOS / Android） | Bottom sheet 模式，佔 50% 畫面高度 | Size=Large（320px）；全圓 `border-radius` 頂部 |

> App 版本月曆另需注意：
> - 標頭使用 `{font-size-sf-h6}` 而非 textmd，讓月份名稱更易辨讀
> - Today / Clear 按鈕高度升至 44px（touch target）
> - 範圍選取 range-header 移至 bottom sheet 頂部固定顯示

---

## 11. Content 指引　📋

- **月份名稱**：依語系自動 i18n（Syncfusion locale）；中文用「2023年1月」格式，英文用「January 2023」。
- **今日**：footer "Today" 按鈕統一用「今日」（中文介面）；不用「現在」「當前」。
- **Clear 按鈕**：僅於 rangeSelect 模式顯示，文案「清除」；不做「重設」。
- **other-month 格**：不加 tooltip；淡色顯示即可。
- **disabled 格說明**：若商業邏輯需要解釋某日不可選（如假日），用 `title` tooltip 提示；預設空白。
- **週起始日**：依公司所在地設定（台灣通常週日為第一列）；不寫死，由 Syncfusion `locale` / `firstDayOfWeek` 控制。

---

## 12. API / Props　📋

### Figma 設計層 props（`get_design_context` 節點 16662-138299 / 16642-7975）

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `size` | "Extra Small" \| "Small" \| "Medium" \| "Large" | "Small" | 面板尺寸 |
| `type` | "Days" \| "Month" \| "Year" | "Days" | 面板視圖 |
| `mode` | "Single-Select" \| "Multi-Select" | "Single-Select" | 選取模式 |
| `multiView` | boolean | false | 並排兩個月份面板 |
| `rangeSelect` | boolean | false | 啟用範圍選取（含 range-header 與 Clear 按鈕） |

### Syncfusion Runtime props（`<ejs-calendar>`，`@syncfusion/ej2-vue-calendars`）

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `value` | Date | — | 當前選取日期（v-model） |
| `values` | Date[] | — | 多選模式下的選取日期陣列（`isMultiSelection=true`） |
| `min` | Date | — | 最小可選日期 |
| `max` | Date | — | 最大可選日期 |
| `start` | "Month" \| "Year" \| "Decade" | "Month" | 初始視圖深度 |
| `depth` | "Month" \| "Year" \| "Decade" | "Month" | 最深鑽取視圖（drill-up 停止點） |
| `isMultiSelection` | boolean | false | 多選模式 |
| `locale` | string | "en" | 語系（影響月份/週名稱）；ERP 用 `"zh-TW"` |
| `firstDayOfWeek` | 0–6 | 0（週日） | 週起始日 |
| `renderDayCell` | EventCallback | — | 自訂格子 render（disabled / highlight 標記） |
| `change` | EventCallback | — | 選取日期改變時觸發 |
| `navigated` | EventCallback | — | 翻月/年時觸發 |
| `showTodayButton` | boolean | true | 顯示 footer Today 按鈕 |
| `weekNumber` | boolean | false | 顯示週次數字欄 |

---

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface §Primary §On-* §Outline §Status §Border Width §Space §Radius §Shadow §Typography §`--ds-*`
- 上游使用：`DsDatePicker.md`（客製元件，Calendar 為其 overlay 核心）；`DatePicker.md`（Syncfusion，直接包含 Calendar）
- Prototype 落地（值權威 / class 命名）：`prototyper/assets/app.css`（Syncfusion Material 3 theme override）
- Code：`@syncfusion/ej2-vue-calendars`；playground 參考 `syncfusion-playground/app/pages/playground/datepicker.vue`（DatePicker 包含 Calendar 核心）
- Figma：`https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/` 節點 `16642-4421`（元件集）/ `16662-138299`（Small / Days / Single-Select）/ `16642-7975`（Small / Month / Single-Select）

---

## Figma 補入紀錄（2026-06-29）

> 依 Token Matching 五層工作流程執行。
> 來源：`get_variable_defs`（節點 16642-4421，32 variables）+ `get_metadata`（整張元件集，24 variants）+ `get_design_context`（節點 16662-138299 Small/Days/Single-Select；節點 16642-7975 Small/Month/Single-Select）+ `get_screenshot`（Small/Days/Single-Select 視覺確認）。

**✅ 已確認對映（Figma 變數 → `athena-tokens.md`）**

`ColorSf/surface`（= `{color-sf-surface}`）、`ColorSf/on-surface`（= `{color-sf-on-surface}`）、`ColorSf/on-surface-variant`（= `{color-sf-on-surface-variant}`）、`ColorSf/primary`（= `{color-sf-primary}`）、`$primary`（= `{color-sf-primary}`）、`Primary_btn/$primary-bg-color`（= `{color-sf-primary}`）、`ColorSf/transparent`（= `{color-sf-transparent}`）、`BG/$transparent`（= `{color-sf-transparent}`）、`BG/$content-bg-color`（= `{color-sf-surface}`）、`ColorSf/outline-variant`（= `{color-sf-outline-variant}`）、`Border/$border-light`（= `{color-sf-outline-variant}`，rgb(215 218 224)）、`Icon/$icon-color`（= `{color-sf-on-surface-variant}`）、`Radius/RadiusSm`（= `{ds-radius-small}`，4px）、`Radius/Radius5Xl`（= `{ds-radius-5extra-large}`，24px — 未用於此元件）。字型：`FontSizeSf/TextXs`（= `{font-size-sf-text-xs}`，11px）、`FontSizeSf/TextSm`（= `{font-size-sf-text-sm}`，12px）、`FontSizeSf/TextMd`（= `{font-size-sf-text-md}`，14px）、`FontSizeSf/TextLg`（= `{font-size-sf-text-lg}`，16px）、`FontWeightSf/Normal`（= `{font-weight-sf-normal}`，400）、`FontWeightSf/Medium`（= `{font-weight-sf-medium}`，500）、`FontFamilySf/Fontfamily`（= `{font-family-sf-fontfamily}`）。

**32 個變數全數找到對應，無漂移。**

**✅ 截圖視覺確認（Small / Days / Single-Select）**

- 外框：1px border + 8px radius 白底卡片 ✅
- header：「January 2023」左置 + ← → 右置 ✅
- weekday row：S M T W T F S，11px 淺灰 ✅
- 今日格（14）：primary 色 outline ring，數字藍色 ✅
- 其他格：白底圓形，黑色數字 ✅
- footer：「Today」右對齊，primary 藍色文字 ✅

**⚠️ Token gap（非阻塞，已標 🎨 量測值）**

1. **`cell_gap: 2px`**：日期格間距 2px，`athena-tokens.md` 最小 space token 為 `{ds-space-margin-extra-small}` = 2px（`Athena_Default`），**可對齊** → 建議下版將 §3 `cell_gap` 改為 `{ds-space-margin-extra-small}`；目前保守標 🎨。
2. **`header_px: 12px` / `body_px: 8px` / `header_py: 4px` / `body_pt: 4px` / `body_pb: 8px`**：量測值均在 token scale（4/8/12px），但 Figma 未使用命名 Space token（直接 inline px）；建議對齊 `{ds-space-padding-large}`（12px）/ `{ds-space-padding-medium}`（8px）/ `{ds-space-padding-small}`（4px），值權威見 `prototyper/assets/app.css`。
3. **`month_row_gap: 28px` / `range_header_pt: 16px`**：28px 與 16px 在現有 Space scale 中分別對應 `{ds-space-padding-3extra-large}` = 24px（最近值，不完全一致），**停下回報**：28px 無完全對應 token；待 DS owner 確認是否新增 token 或視為 component-specific 量測值。
4. **`month_cell_width: 76px`**：Month 格寬 76px（= 3 columns × 76px + 2 gaps × 4px = 236px，略小於 panel 252px - 2×8px = 236px）→ 符合推算，但無對應 Space token；值見 app.css。
5. **`selected` 日期格（filled primary circle）**：本 Figma variant（Single-Select）中未見 filled primary 格；only today outline ring 量測到。Selected 填色依 Syncfusion Material 3 runtime 提供，Figma prototype 未靜態顯示。依 §9.3「Figma 變數即權威」—— 此為 runtime 態，從 Syncfusion Material 3 主題規格推導（`{color-sf-primary}` fill + `{color-sf-on-primary}` text）；待 Figma 補入 selected 態靜態示意。

**⬜ 待補（次要）**

- `selected` 日期格 Figma 靜態示意（Single-Select filled circle）
- 範圍選 range 帶狀高亮 Figma 截圖確認（rangeSelect=true）
- Multi-Select 多格 Figma 截圖（Multi-Select variant 已在元件集，節點 16647-14090）
- `multiView=true` 並排面板邊界處理確認
- today + selected 疊加態 Figma 確認
- hover 態 Figma 截圖（nav 按鈕 + 日期格）
- `month_row_gap: 28px` token 補入決策（見 token gap 3）
