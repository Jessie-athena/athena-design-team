---
name: Schedule（排程日曆）
category: 資料展示與表格
tier: full           # 多視圖日曆（日/週/月/議程）+ 事件拖曳 / resize + 跨斷點佈局切換
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: JhcpyIEEzSChcEXMRJUiIm/14088:2756
version: v0.3
last-synced: 2026-06-30
---

> 依 `../component-doc-schema.md`（Full 層）產出。視覺字面值（事件條高度 / 時間格寬 / 多色事件）權威＝`prototyper/assets/app.css`；本檔＝契約（what/why/token-reference/state/a11y）。

## 1. 概述　📋

**多視圖排程日曆**：以日 / 週 / 月 / 議程（Agenda）4 種視圖切換顯示事件。適用場景：班表管理、會議室預約、交期 / 到期日提醒、生產排程視覺化。

何時**不用**：任務有依賴關係或需要甘特條 → Gantt；無時間維度的工作流程 → Kanban；大量扁平資料查閱 → DataGrid。

## 2. Anatomy　🎨

```
.schedule                         ← 根容器（flex column）
  ├─ .sc__toolbar                 ← 視圖切換（日/週/月/議程）+ 日期導航 + 今日按鈕
  ├─ .sc__header                  ← 星期列（週/月視圖）
  └─ .sc__body                    ← 視圖內容（依 viewType 切換）

日視圖 / 週視圖（time-grid）：
  .sc__body
    ├─ .sc__time-axis             ← 時間軸（左側，0:00–23:30）
    └─ .sc__day-col (×7)          ← 每日欄
         ├─ .sc__all-day          ← 全天事件橫條（置頂）
         └─ .sc__time-slots       ← 30 分鐘一格（overflow-y:auto）
              └─ .sc__event       ← 事件條（absolute 定位，依起迄時間）

月視圖：
  .sc__body
    └─ .sc__month-grid (7×N)      ← 每格一天
         ├─ .sc__date-label       ← 日期數字（今日底色 {color-sf-primary}）
         └─ .sc__event (×N)       ← 事件橫條；超過 3 筆顯示「+N 筆」

議程視圖：
  .sc__body
    └─ .sc__agenda-group (per day)
         ├─ .sc__agenda-date      ← 日期標題
         └─ .sc__agenda-item      ← 事件一列（起迄時間 + 標題 + 地點）
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
time-grid:
  slot-height:    "62px"                              # 🎨 30 分鐘格（Figma 確認）
  time-axis-w:    "102px"                             # 🎨 時間軸欄寬
  date-header-h:  "70px"                              # 🎨 日期欄標題高
  col-min-width:  "120px"                             # 🎨 量測值（週視圖每欄最小寬）
  time-font:      "{font-size-sf-text-md}"            # 🎨 14px（Figma 確認；非 12px）
  time-fg:        "{color-sf-on-surface}"             # 🎨 修正：on-surface（非 on-surface-variant）
  date-label-day: "{font-size-sf-text-sm} / 400"      # 🎨 12px 星期幾標籤
  date-label-num: "{font-size-sf-text-lg} / 400"      # 🎨 16px 日期數字
  grid-line:      "0.5px {color-sf-outline-variant}"  # 🎨 0.5px（非 1px）

month-grid:
  cell-min-height: "100px"                            # 🎨 量測值
  date-font:     "{font-size-sf-text-sm} / {font-weight-sf-medium}"  # 🔗 12px / 500
  today-bg:      "{color-sf-primary}"                 # 🔗 今日圓形底色
  today-fg:      "{color-sf-on-primary}"              # 🔗

event:
  height:        "見 app.css .sc__event"              # 🎨 量測值（time-grid 依持續時間）
  radius:        "{ds-radius-md}"                     # 🔗 4px
  font:          "{font-size-sf-text-sm}"             # 🔗 12px
  fg:            "{color-sf-on-primary}"              # 🔗 白字（事件條預設主色底）
  multi-color:   "各 calendar/resource 對應不同 hue（見 app.css 事件色組）"  # 🎨 值權威
  now-indicator:
    color:       "{color-sf-danger}"                  # 🔗 紅色「現在」橫線
    width:       "{ds-borderwidth-medium}"            # 🔗 2px

toolbar:
  height:        "48px"                               # 🎨 h-[48px]（Figma 確認）
  bg:            "rgba({color-sf-primary}, 0.08)"     # 🎨 primary-8%+white
  btn-h:         "32px"                               # 🎨 view button height
  btn-font:      "{font-size-sf-text-md} / {font-weight-sf-medium}"  # 🔗 14px/500
  nav-btn-radius: "20px"                              # 🎨 circular chevron buttons
  active-view-bg: "rgba({color-sf-on-surface}, 0.08)"  # 🎨 on-surface-8%（非 primary！）
  active-view-fg: "{color-sf-on-surface}"             # 🎨 修正：非 on-primary
```

## 4. Variants / Types　🎨🔗

```yaml
# ── Figma Variants（FAI2 node 14088:2756） ──
Sizes=Small, Mode=Days:
  viewport:      "1080×648px"
  toolbar-h:     "48px"
  date-header-h: "70px"
  slot-h:        "62px"   # 30 min
  time-axis-w:   "102px"

modes: "Days / Work Week / Month / Agenda × Size 2 = 8+ variants"

# ── 行為 Variants ──
day-view:     "單日時間格（0:00–23:59）"
week-view:    "7 日時間格（預設）"
month-view:   "月曆格（預設首頁視圖）"
agenda-view:  "議程列表（連續 N 天事件）"
timeline-view: "橫向時間軸 + 資源（負責人 / 房間）swimlane（企業排班使用）"
```

## 5. States　🎨🔗

| 狀態 | 呈現 |
|---|---|
| event-default | 主色底 + 白字；短事件（< 30 min）改細橫條 |
| event-hover | 略深（見 app.css）+ tooltip（完整標題 / 時間 / 地點）|
| event-selected | 外框 `{ds-borderwidth-medium} {color-sf-primary-darken}` |
| event-dragging | 被拖事件 opacity 0.5；目標時間槽底色 `{color-sf-primary-opacity-12}`；tooltip 顯示預計新時間 |
| event-resize | resize handle（底部）hover → ew-resize cursor |
| event-cancelled | `text-decoration: line-through`；opacity 0.5 |
| time-slot-hover | 時間格底色 `{color-sf-surface-variant}`（可點新增） |
| more-events | 「+N 筆」chip，點選後 popover 顯示完整列表 |
| today | 時間格底色 `{color-sf-primary-opacity-12}`；日期標籤圓底 `{color-sf-primary}` |
| loading | Skeleton 佔位（保留格線結構） |
| empty | 月視圖：各格無事件（不顯示 empty-state；格子即空態載體）；議程視圖：「選取的期間沒有任何事件」|

## 6. Behavior　📋

| 互動 | 行為 |
|---|---|
| 新增事件 | 點擊空時間槽 → quick-add popover（標題 + 時間）；雙擊 → 完整 Form View |
| 編輯事件 | 點擊事件條 → 快速編輯 popover；雙擊 → Form View |
| 拖曳移動 | 拖曳事件至新時間槽 / 日期；`onDragStop` 更新起迄時間 |
| 拖曳 resize | 拖底部 handle 調整結束時間 |
| 視圖切換 | Toolbar 按鈕切換；維持目前日期 scroll 位置 |
| 日期導航 | ← → 按鈕：週視圖移 7 天，月視圖移 1 個月；今日按鈕跳回今日 |
| 重複事件 | 拖曳 / 修改時詢問「僅此事件 / 此後所有 / 全部」|
| 事件顏色 | 依 calendar/resource/category 分色（見 app.css 多色組） |

## 7. RWD / 斷點　📋

| 斷點 | 呈現 |
|---|---|
| ≥ 1280px | 週視圖預設；所有欄完整 |
| 1024–1279px | 週視圖縮至 5 欄（週一至週五）|
| ≤ 1024px | 預設切為**月視圖**；週視圖改 3 欄日視圖輪播 |
| < 768px | **月視圖** + 議程視圖為主；時間格視圖改日視圖 |

## 8. Keyboard　📋

- 日期格 / 時間槽：`Tab` / `Shift+Tab` 在格間移動；`Enter` 新增事件；`Delete` 刪除選中事件（需確認）。
- 事件 focus：`←→↑↓` 移動（週/日視圖）；`Enter` 開詳情；`Esc` 取消選取。
- 視圖切換：Toolbar 按鈕 `Tab` 可 focus，`Enter` 切換。
- 拖曳替代：選中事件 → context menu / `M` 鍵 → 輸入新日期時間。

## 9. a11y　📋

- `role="grid"` 搭配 `aria-label="[月份 / 週次] 排程日曆"`；時間格 `role="gridcell"` + `aria-label="[日期 時間]"`。
- 事件條 `role="button"` + `aria-label="[標題]，[起迄時間]，[地點]"`。
- 今日指示：`aria-current="date"` 標記今日格。
- 拖曳替代鍵盤方案必須可用（screen reader 無法操作拖曳）。
- 重複事件修改對話框：focus trap 於 dialog 內；`Esc` 關閉。

## 10. 跨平台 Adaptive　📋

- **桌面**：完整週/月視圖 + 拖曳 + resize + timeline-view。
- **平板（M）**：預設月視圖；週視圖收為 3 欄；拖曳保留（精準觸控需測試）。
- **手機（S）**：月視圖 + 點日期顯示下方議程列表；拖曳改以「移動事件」dialog 替代。

## 11. Content　📋

- 事件標題過長時截斷 + `...`；tooltip 顯示完整標題。
- 時間格式：24h（`09:00–10:30`）或 12h（依系統 locale）。
- 重複事件修改詢問：「僅修改此事件」/「修改此後的所有事件」/「修改所有重複事件」。
- empty（議程視圖）：「[日期範圍] 沒有任何已排程的事件」。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| eventSettings | EventSettings | — | `{ dataSource: Event[], fields?: {...} }` 事件資料設定 |
| selectedDate | Date | new Date() | 目前顯示日期 |
| currentView | 'Day' \| 'Week' \| 'WorkWeek' \| 'Month' \| 'Agenda' | 'Week' | 預設視圖 |
| height | string \| number | 'auto' | 元件高度 |
| readonly | boolean | false | 唯讀模式（禁止新增 / 編輯 / 刪除） |
| startHour \| endHour | string | '00:00' / '24:00' | time-grid 起迄時間 |
| allowDragAndDrop | boolean | true | 事件拖曳 |
| allowResizing | boolean | true | 事件 resize |
| group | GroupModel | — | 泳道分組（負責人 / 房間） |

**視圖設定**：透過 `<e-views>/<e-view option="Day|Week|WorkWeek|Month|Agenda|TimelineDay">` 指令指定；模組需 `provide('schedule', [Day, Week, Month, Agenda, ...])` 注入。`views: string[]` 頂層 prop **不適用**此元件。

事件：`@eventClick` / `@cellClick` / `@dragStop` / `@resizeStop`

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §Surface / §On-* / §Danger / §Space / §Radius
- 值權威：`prototyper/assets/app.css`（canonical CSS，事件多色組在此定義）
- 同層：`Gantt.md`（依賴 / 排程場景）；`Kanban.md`（狀態流場景）
- Code：`@syncfusion/ej2-vue-schedule`
