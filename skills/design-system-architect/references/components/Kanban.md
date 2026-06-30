---
name: Kanban（看板）
category: 資料展示與表格
tier: full           # 多欄卡片板 + 拖曳狀態流轉 + 游泳道生命週期
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: —        # 🎨 Figma 補入時填 <FILE_KEY>/<NODE_ID>（FAI2 componentKey: a875a18b0a6746470418c15aec20d117868691d1）
version: v0.1
last-synced: —
---

> 依 `../component-doc-schema.md`（Full 層）產出。視覺字面值（尺寸 / 欄寬 / 卡片陰影）權威＝`prototyper/assets/app.css`；本檔＝契約（what/why/token-reference/state/a11y）。

## 1. 概述　📋

**欄導向卡片板**：每欄代表一個狀態（如「待辦 / 進行中 / 完成」），每張卡片代表一筆任務或單據。使用者可拖曳卡片跨欄（即等同狀態流轉）。常見場景：任務追蹤、審批佇列視覺化、客服工單流程。

何時**不用**：狀態超過 6 個或卡片量大（> 200 筆）→ 改 DataGrid 列表篩選；需甘特式時程 → Gantt；需 timeline 日曆 → Schedule。

## 2. Anatomy　🎨

```
.kanban                          ← 根容器（水平 flex，overflow-x:auto）
  └─ .kb__col                   ← 欄（每欄一個狀態）
       ├─ .kb__col-header        ← 欄標題 + 計數 badge + 可選 WIP limit
       └─ .kb__col-body          ← 卡片垂直堆疊容器（overflow-y:auto）
            └─ .kb__card         ← 卡片（draggable）
                 ├─ .kb__card-header   ← 標題 + 優先級 chip + 更多操作選單
                 ├─ .kb__card-meta     ← 到期日 / 負責人 avatar / 標籤列
                 └─ .kb__card-footer   ← 可選；進度條 / 子任務計數 / 附件數
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
col:
  width:        "280px"                                        # 🎨 量測值，單一來源 app.css
  bg:           "{color-sf-surface-variant}"                   # 🔗 淡灰欄底
  header-height: "見 app.css .kb__col-header"                  # 🎨
  header-font:  "{font-size-sf-text-md} / {font-weight-sf-medium}"  # 🔗 14px/500
  header-fg:    "{color-sf-on-surface}"                        # 🔗
  count-chip:   "{font-size-sf-text-sm}; bg:{color-sf-outline}; fg:{color-sf-on-surface-variant}"  # 🔗
  gap:          "{ds-space-padding-medium}"                    # 🔗 8px 欄間距
  padding:      "{ds-space-padding-medium}"                    # 🔗 8px 欄內距

card:
  bg:           "{color-sf-surface}"                           # 🔗 白卡
  shadow:       "見 app.css .kb__card"                         # 🎨 sm shadow，單一來源 app.css
  radius:       "{ds-radius-large}"                            # 🔗 8px
  padding:      "{ds-space-padding-large}"                     # 🔗 12px
  gap:          "{ds-space-padding-medium}"                    # 🔗 8px header/meta/footer 間距
  min-height:   "80px"                                         # 🎨 量測值
  title-font:   "{font-size-sf-text-md} / {font-weight-sf-medium}"  # 🔗 14px / 500
  title-fg:     "{color-sf-on-surface}"                        # 🔗
  meta-font:    "{font-size-sf-text-sm}"                       # 🔗 12px
  meta-fg:      "{color-sf-on-surface-variant}"                # 🔗
  avatar-size:  "24px"                                         # 🎨

wip-limit:
  exceeded-col-bg: "見 app.css .kb__col--overflow"             # 🎨 exceeded 警示底色（danger tint）
  exceeded-count-fg: "{color-sf-danger}"                       # 🔗
```

## 4. Variants / Types　🎨🔗

```yaml
standard:         "固定欄順序；欄數由 columns 設定決定"
collapsible-col:  "欄可收合為細條（節省水平空間）；collapsed=chevron-right + 欄名縱向"
swimlane:         "欄內以 swimlane（如「負責人」）再分組；每 lane 有小標題行"
```

## 5. States　🎨🔗

| 狀態 | 呈現 |
|---|---|
| card-default | 白底 + sm shadow |
| card-hover | shadow 升級（md）+ 輕微浮起（`translateY(-2px)`，見 app.css） |
| card-dragging | 被拖卡 opacity 0.6 + `{color-sf-primary}` 2px 外框；拖影 clone 半透明跟隨指標 |
| col-drop-target | 欄底色換 `{color-sf-primary-opacity-12}`；插入位置顯示 `{color-sf-primary}` 2px 橫線 |
| wip-limit-exceeded | 欄底色改 danger tint（見 app.css）；計數 chip 改 `{color-sf-danger}` |
| card-overdue | 到期日文字改 `{color-sf-danger}` |
| empty-col | 「+ 新增項目」ghost 按鈕佔位（不顯示 inbox icon，欄本身即空態承載） |
| loading | Skeleton 卡片（保留 card 尺寸） |

## 6. Behavior　📋

| 互動 | 行為 |
|---|---|
| 拖曳卡片 | 跨欄拖放 → 狀態流轉；觸發 `onDragStart` / `onDrop` / `onDragStop` |
| WIP 上限 | `maxCards` 設定後，超過時警示 + 可選阻止放置（`allowOverflow=false`） |
| 新增卡片 | 點欄右上「＋」→ 快速新增表單（行內）或跳至 Form View |
| 編輯卡片 | 點卡片標題 → 快速編輯（標題 / 到期日 / 負責人）或跳 Form View |
| 刪除卡片 | 卡片右上「⋮」→「刪除」→ `confirm` dialog |
| 欄排序 | 拖曳欄標題重排欄順序（可選關閉） |
| 欄收合 | 點欄標題左側 icon 收合為細條（顯示計數，不顯示卡片） |
| 篩選 | Toolbar 篩選條件（負責人 / 到期日 / 標籤）→ 不符的卡片 `opacity:0.3` 未移除 |
| 搜尋 | 關鍵字 highlight 卡片標題；不符卡片不隱藏（僅 dim） |

## 7. RWD / 斷點　📋

| 斷點 | 呈現 |
|---|---|
| ≥ 1440px | 所有欄橫排；欄固定 280px；不足寬橫捲 |
| 1024–1439px | 同上；欄寬可縮至 240px |
| ≤ 1024px | **Kanban 降為 ListView 堆疊**：所有卡片垂直列表，依欄分組（群組行）；可切換回 Kanban |
| < 768px | 同上；群組列表為主；無拖放（改「移至」context menu）|

## 8. Keyboard　📋

- 卡片 focus 後：`Enter` 開啟卡片詳情；`Delete` / `Backspace` 刪除（需確認）。
- 拖曳替代：卡片 focus → `M` 鍵（或 context menu）→ 選擇「移至 [欄名]」。
- 欄 focus：`←→` 在欄間移動；`↑↓` 在欄內卡片移動。

## 9. a11y　📋

- 根容器 `role="region"` + `aria-label="看板"`；每欄 `role="group"` + `aria-label="[欄名]（[計數] 筆）"`。
- 卡片 `role="article"` + `tabindex="0"`；drag-and-drop 提供鍵盤替代（「移至」選單）。
- WIP 超限警示：`role="alert"` 播報「[欄名] 已超過上限」。
- 到期警示（overdue）：紅色文字需搭配 icon 或 `aria-label="逾期：[到期日]"`。

## 10. 跨平台 Adaptive　📋

- **桌面**：完整 Kanban + 拖曳 + WIP + 欄收合。
- **平板（M）**：移除欄拖曳重排；卡片觸控拖曳保留（精準拖放須測試）；WIP 警示顯示。
- **手機（S）**：降為 ListView 堆疊（按欄分群組），以 context menu 移至不同欄替代拖曳。

## 11. Content　📋

- 欄名：以狀態動詞為主（「待分配」「處理中」「已完成」），而非技術 key（`draft`）。
- WIP 超限警示：「[欄名] 已達上限（[max] 筆），請先完成現有項目」。
- empty-col（無卡片）：「目前沒有 [欄名] 的項目」+ 「＋ 新增」。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| dataSource | Object[] | [] | 卡片資料 |
| columns | KanbanColumn[] | [] | `{ key, headerText, maxCards? }` |
| keyField | string | 'status' | 卡片所屬欄的欄位名 |
| cardSettings | CardSettings | — | `{ contentField, headerField }` |
| allowDragAndDrop | boolean | true | 是否允許拖曳 |
| swimlaneSettings | SwimlaneSettings | — | `{ keyField }` 啟用游泳道 |
| enableCollapse | boolean | false | 允許欄收合 |

事件：`@cardDragStop({ data, dropIndex, columnKey })`

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §Surface / §On-* / §Danger / §Space / §Radius / §Shadow
- 值權威：`prototyper/assets/app.css`（canonical CSS）
- 同層：`DataGrid.md`（大量資料改用）；`st-chip.md`（優先級 / 狀態 chip）；`Schedule.md`（時程視覺）
- Code：`@syncfusion/ej2-vue-kanban`
