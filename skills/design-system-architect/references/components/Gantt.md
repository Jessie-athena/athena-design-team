---
name: Gantt（甘特圖）
category: 資料展示與表格
tier: full           # 雙面板（樹狀 Grid + Timeline）+ 任務依賴連線 + 進度管理生命週期
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: —        # 🎨 Figma 補入時填 <FILE_KEY>/<NODE_ID>（FAI2 componentKey: c8d75f4b195b55adf22999ed51d24baacaa7baf4）
version: v0.2
last-synced: 2026-06-30
---

> 依 `../component-doc-schema.md`（Full 層）產出。視覺字面值（任務條高度 / 進度色 / 依賴線色）權威＝`prototyper/assets/app.css`；本檔＝契約。

## 1. 概述　📋

**雙面板甘特圖**：左側 TreeGrid 列出任務 / 里程碑，右側 Timeline 以時間軸顯示對應的任務條（task bar）與依賴關係連線。適用場景：專案排程、生產計劃、採購交期視覺化、里程碑追蹤。

何時**不用**：無時間軸需求（單純任務列表）→ DataGrid；無階層關係的日曆事件 → Schedule；任務量極少（< 5 筆）→ 靜態表格即可。

## 2. Anatomy　🎨

```
.gantt                            ← 根容器（水平 flex）
  ├─ .gantt__grid                 ← 左側 TreeGrid（繼承 TreeGrid.md 結構）
  │   ├─ .gantt__grid-header      ← 欄表頭
  │   └─ .gantt__grid-body        ← 任務列（可展開/收合）
  └─ .gantt__chart                ← 右側 Timeline（overflow-x:auto）
       ├─ .gantt__timeline-header ← 時間刻度（雙層：月/週 或 週/日）
       ├─ .gantt__chart-body      ← 任務條區域
       │   ├─ .gantt__task-bar    ← 任務條（可拖曳左右 / 右側拉伸 resize）
       │   ├─ .gantt__progress    ← 進度條（任務條內左側填色）
       │   ├─ .gantt__milestone   ← 里程碑菱形符號
       │   └─ .gantt__connector   ← 依賴關係連線（FS/FF/SS/SF）
       └─ .gantt__today-line      ← 今日垂直標記線
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
grid:
  width:          "40% of container"                           # 🎨 量測值，可拖分隔線調整
  继承:           "TreeGrid.md §3 全部 token"

timeline-header:
  tier1-height:   "見 app.css .gantt__timeline-header"         # 🎨（月/週 level）
  tier2-height:   "見 app.css"                                 # 🎨（週/日 level）
  bg:             "見 app.css"                                 # 🎨 primary 疊白實色
  font:           "{font-size-sf-text-sm} / {font-weight-sf-medium}"  # 🔗 12px / 500
  fg:             "{color-sf-on-surface-variant}"               # 🔗

task-bar:
  height:         "見 app.css .gantt__task-bar"                # 🎨 量測值（含上下 margin）
  radius:         "{ds-radius-md}"                             # 🔗 4px
  bg:             "{color-sf-primary}"                         # 🔗 主色任務條
  fg:             "{color-sf-on-primary}"                      # 🔗 任務名白字（短條時隱藏）
  progress-bg:    "見 app.css"                                 # 🎨 primary 加深實色，app.css 權威

milestone:
  size:           "12px (diamond)"                             # 🎨 量測值
  fill:           "{color-sf-primary}"                         # 🔗

connector:
  color:          "{color-sf-on-surface-variant}"              # 🔗 灰色連線
  arrow-size:     "8px"                                        # 🎨
  type-critical:  "{color-sf-danger}"                          # 🔗 關鍵路徑改紅線

today-line:
  color:          "{color-sf-danger}"                          # 🔗 紅色垂直線
  width:          "{ds-borderwidth-small}"                     # 🔗 1px
  label-bg:       "{color-sf-danger}"                          # 🔗 今日標籤底色

weekend:
  bg:             "見 app.css .gantt__weekend"                 # 🎨 週末淡灰底，app.css 權威

row-height:       "見 app.css .gantt__row"                     # 🎨 量測值（與 grid 列高同步）
```

## 4. Variants / Types　🎨🔗

```yaml
project-view:     "展示任務條 + 里程碑 + 依賴線（完整甘特圖）"
resource-view:    "額外顯示 swimlane（負責人維度）；任務條依資源分組"
read-only:        "隱藏 resize handle / drag cursor；不可修改"
```

## 5. States　🎨🔗

| 狀態 | 呈現 |
|---|---|
| task-default | `{color-sf-primary}` 任務條 |
| task-hover | 任務條略深（見 app.css），浮現 tooltip（起迄 / 進度 / 負責人） |
| task-selected | 任務條外框 `{ds-borderwidth-medium} {color-sf-primary}` |
| task-dragging | 拖曳中半透明（opacity 0.7）+ 拖影；落點更新左側 Grid 日期 |
| task-resize | resize handle hover 改 `{color-sf-primary}` + ew-resize cursor |
| task-overdue | 進度不足且已過今日 → 任務條右側殘留段改 `{color-sf-danger}` |
| milestone-done | 菱形填色改 `{color-sf-success}` |
| critical-path | 關鍵路徑任務條 + 連線改 `{color-sf-danger}`（需啟用 `enableCriticalPath`） |
| loading | Skeleton 列 + Skeleton 任務條 |
| empty | inbox-outline icon + 「尚未設定任何任務」 |

## 6. Behavior　📋

| 互動 | 行為 |
|---|---|
| 任務條拖曳（左右移） | 起迄日期同步平移；左側 Grid 日期欄即時更新 |
| 任務條右側 resize | 調整結束日期；最小持續時間 1 天 |
| 任務條左側 resize | 調整開始日期；不可超過子任務最早開始 |
| 依賴連線拖曳 | 從任務條末端拖至另一任務條，建立 Finish-to-Start 依賴；預設 FS |
| 展開 / 收合（TreeGrid） | 同 TreeGrid.md §6；子任務隱藏時任務條與連線同步隱藏 |
| Zoom in / out | Timeline 刻度切換（日/週/月/季/年）；維持水平軸位置比例 |
| 今日快捷 | toolbar 「今日」按鈕，Timeline scroll 至今日線 |
| 進度更新 | 點任務條內進度 handle 拖曳（或右鍵「更新進度」）|

## 7. RWD / 斷點　📋

| 斷點 | 呈現 |
|---|---|
| ≥ 1440px | Grid 40% + Timeline 60%；Timeline 可顯示 3–6 個月 |
| 1024–1439px | Grid 35% + Timeline 65%；Grid 欄位收斂至 P0–P1 |
| ≤ 1024px | Grid 收合至最小（只留任務名）；Timeline 佔滿；橫捲 |
| < 768px | **不做**（甘特圖為桌面功能）|

## 8. Keyboard　📋

- 任務條 focus（Tab 進入）：`←→` 平移 1 天；`Shift+←→` 平移 7 天；`Enter` 開詳情。
- resize：focus 任務條 → `Alt+←→` 縮放結束日期（1 天步進）。
- 依賴建立：任務 focus → `C` → 選取目標任務 focus → `Enter` 建立 FS 依賴（鍵盤替代拖曳）。
- Zoom：`+` / `-` 切換 timeline scale。

## 9. a11y　📋

- 任務條 `role="img"` + `aria-label="[任務名]：[起始日] 至 [結束日]，進度 [N]%"`。
- 依賴連線為裝飾性 SVG，對 screen reader 隱藏（`aria-hidden="true"`）；依賴關係以 Grid 欄位文字表示。
- 今日線 `role="separator"` + `aria-label="今日：[日期]"`。
- 拖曳替代：鍵盤修改日期；screen reader 播報「[任務名] 已更新至 [新日期] 至 [新日期]」。
- 對比：任務條白字需在 primary bg 上 ≥ 4.5:1。

## 10. 跨平台 Adaptive　📋

- **桌面**：完整雙面板 + 拖曳 + resize + 依賴連線。
- **平板（M）**：Grid 收合；Timeline 橫捲；取消拖曳 resize（改長按 context menu）。
- **手機（S）**：**不做**，改以 DataGrid 列表展示任務資料。

## 11. Content　📋

- 任務名稱：截斷時 tooltip 全文；任務條過短時，名稱顯示在條外右側。
- 日期格式：`YYYY/MM/DD`（依系統 locale）。
- 進度：`N%`（整數）；100% = 已完成（改 success 色）。
- empty state：「尚未建立任何任務，點『新增任務』開始排程」。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| dataSource | Object[] | [] | 任務資料（巢狀或扁平 parentID 模式） |
| taskFields | TaskFieldsModel | — | 欄位名對應：`{ id, name, startDate, endDate, duration, progress, parentID?, child?, dependency?, resourceInfo? }` |
| treeColumnIndex | number | 1 | 左側 Grid 展開欄位索引（0-based） |
| viewType | 'ProjectView' \| 'ResourceView' | 'ProjectView' | 顯示模式 |
| timelineSettings | TimelineSettingsModel | — | 刻度設定：`{ topTier: { unit, format }, bottomTier: { unit, format } }` |
| editSettings | EditSettingsModel | — | `{ allowAdding, allowEditing, allowDeleting, allowTaskbarEditing, showDeleteConfirmDialog }` |
| highlightWeekends | boolean | false | 標記週末底色 |
| projectStartDate | Date | — | 時間軸顯示起始日 |
| projectEndDate | Date | — | 時間軸顯示結束日 |
| splitterSettings | `{ position }` | — | 左右面板分割比例（如 `'40%'`） |
| enableCriticalPath | boolean | false | 顯示關鍵路徑（需注入 `CriticalPath` 模組） |
| renderBaseline | boolean | false | 顯示基線比較 |
| resources | Object[] | — | 資源集合（ResourceView 時使用） |
| resourceFields | `{ id, name }` | — | 資源欄位對應 |

> **注意**：任務條拖曳 (`allowTaskbarEditing`) 在 `editSettings` 內，**不是**頂層 prop。需 `provide('gantt', [Edit, Selection, CriticalPath, ...])` 注入模組。

## 13. 關聯　🔗

- 上游：**`TreeGrid.md`**（左側 Grid 繼承）
- Tokens：`../athena-tokens.md` §Primary / §Surface / §On-* / §Danger / §Success / §Space / §Radius
- 值權威：`prototyper/assets/app.css`（canonical CSS）
- 同層：`Schedule.md`（日曆事件場景）；`Kanban.md`（狀態流場景）
- Code：`@syncfusion/ej2-vue-gantt`
