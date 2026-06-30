---
name: TreeView（樹狀導覽）
category: 佈局與導覽
tier: lite
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: —       # 🎨 Figma 補入時填 <FILE_KEY>/<NODE_ID>
version: v0.1
last-synced: —
---

> 依 `../component-doc-schema.md`（Lite 層）產出。資料密集型階層表格改用 `TreeGrid.md`；本元件專注於**導覽 / 選取**場景。

## 1. 概述　📋

**階層式樹狀導覽**：以縮排 + 展開 / 收合符號呈現樹狀結構，支援單選 / 多選 / 拖拉排序。ERP 使用場景：科目樹（會計科目階層選擇）、部門組織樹（選取部門）、分類階層管理、檔案 / 資料夾結構瀏覽。

何時不用 TreeView：
- 需多欄比較、排序、篩選的階層資料 → `TreeGrid.md`
- 扁平清單（無巢狀）→ `ListView.md`
- 頁面導覽路徑 → `Breadcrumb.md`

## 2. Anatomy　🎨

```
.e-treeview
  └─ .e-list-root（ul）
      └─ .e-list-item（li）         ← 節點（含展開符號 + icon + label + 操作）
          ├─ .e-fullrow              ← hover / selected 背景全寬條（click target）
          ├─ .e-icon-wrapper         ← 縮排 + 展開 / 收合 icon（chevron）
          ├─ .e-text-content         ← 節點內容
          │   ├─ .e-icons            ← 節點 icon（選配）
          │   └─ .e-list-text        ← 節點文字
          └─ .e-list-parent（ul）    ← 子節點容器（展開時可見）
              └─ .e-list-item（li）  ← 子節點（遞迴）
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
node:
  height:         "40px"                                     # 🎨 量測值
  indent:         "16px"                                     # 🎨 每層縮排量
  padding-h:      "{ds-space-padding-extra-large}"           # 🔗 16px（第一層）；後續 +16px/層
  font:           "{font-size-sf-text-md}"                   # 🔗 14px
  fg:             "{color-sf-on-surface}"                    # 🔗
  bg:             "transparent"                              # 📋 fullrow hover 才有背景

expand-icon:
  size:           "16px"                                     # 🎨
  fg:             "{color-sf-on-surface-variant}"            # 🔗
  expanded-rotation: "90deg"                                 # 📋 chevron-right 旋轉

node-icon:
  size:           "20px"                                     # 🎨（選配；資料夾 / 檔案 icon）
  fg:             "{color-sf-on-surface-variant}"            # 🔗

drag-indicator:
  height:         "{ds-borderwidth-small}"                   # 🔗 1px 插入線
  color:          "{color-sf-primary}"                       # 🔗
```

## 4. Variants　📋

```yaml
default:           "展開 / 收合 + 單選點擊"
with-checkbox:     "多選（每節點 Checkbox；見 Checkbox.md）；showCheckBox=true"
with-icons:        "每節點帶圖示 icon（資料夾 / 項目區分）"
drag-and-drop:     "節點可拖拉重排；allowDragAndDrop=true"
editable:          "雙擊節點文字可 in-place 編輯；allowEditing=true"
async-load:        "子節點按需載入（loadOnDemand=true）；載入中顯示 Skeleton"
```

## 5. States　🎨🔗

```yaml
node:
  default:        { bg: "transparent", fg: "{color-sf-on-surface}" }
  hover:          { bg: "見 app.css" }                       # 🎨 fullrow opacity8 疊層
  selected:       { bg: "{color-sf-primary-container}", fg: "{color-sf-primary}" }  # 🔗
  selected-multiple: "依 Checkbox 勾選狀態（indeterminate 時 Checkbox 顯示 dash）"  # 📋
  focused:        { outline: "見 app.css" }                  # 🎨
  disabled:       { fg: "{color-sf-on-surface-opacity38}", cursor: "not-allowed" }  # 🔗
  loading:        "spinner 或 Skeleton row（子節點 loadOnDemand 時）"  # 📋
  dragging:       { opacity: "0.5", cursor: "grabbing" }     # 📋
  drop-target:    { bg: "{color-sf-primary-container}", indicator: "藍色橫線" }  # 🔗

node-expand:
  expanding:      "chevron 旋轉 0→90°（150ms）；子樹 SlideDown"
  collapsing:     "chevron 旋轉 90→0°（150ms）；子樹 SlideUp"
```

## 9. a11y　📋

- 根容器 `role="tree"` + `aria-label="<描述>"`（如 `"科目樹"`）；可收合子樹容器 `role="group"`。
- 每個節點 `role="treeitem"` + `aria-level="<深度>"` + `aria-setsize="<兄弟數>"` + `aria-posinset="<位置>"`。
- 有子節點者加 `aria-expanded`（true/false）；葉節點省略。
- Checkbox 模式：每節點帶 `aria-checked`（true / false / mixed）。
- 鍵盤操作（完整 ARIA Tree Pattern）：

| 按鍵 | 行為 |
|------|------|
| `↑` / `↓` | 移到上 / 下一個可見節點 |
| `→` | 已展開節點：移到第一個子節點；已收合節點：展開 |
| `←` | 有子且已展開：收合；有子且已收合 / 葉節點：移到父節點 |
| `Enter` / `Space` | 選取節點；Checkbox 模式：toggle 勾選 |
| `Home` / `End` | 移到第一 / 最後可見節點 |
| `*`（數字鍵盤）| 展開目前節點的所有子節點 |
| F2 | 進入 in-place 編輯（allowEditing=true）|

- 拖拉中帶 `aria-grabbed`；放置目標帶 `aria-dropeffect`。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| fields | FieldsModel | — | `{ dataSource, id, parentID, text, hasChildren?, iconCss?, child?, isChecked? }` |
| allowDragAndDrop | boolean | false | 節點可拖拉重排 |
| allowEditing | boolean | false | 雙擊 in-place 編輯 |
| allowMultiSelection | boolean | false | 允許多選（Ctrl/Shift+click） |
| showCheckBox | boolean | false | 每節點顯示 Checkbox |
| loadOnDemand | boolean | false | 子節點按需載入 |
| expandOn | `Click` \| `DblClick` \| `None` | `Click` | 展開觸發方式 |
| selectedNodes | string[] | [] | 受控已選節點（by id） |

方法：`.addNodes()` / `.removeNodes()` / `.expandAll()` / `.collapseAll()` / `.checkAll()`

事件：`@nodeClicked(e)` / `@nodeSelected(e)` / `@nodeExpanded(e)` / `@nodeCollapsed(e)` / `@nodeDragStop(e)`

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface / §On-* / §Primary / §Outline / §Space / §Typography
- 值權威：`prototyper/assets/app.css`
- 相關：`TreeGrid.md`（多欄資料型階層改用）；`ListView.md`（扁平清單）；`Checkbox.md`（showCheckBox 模式）；`DropDownTree.md`（Tree 嵌入下拉選擇）
- Code：`@syncfusion/ej2-vue-navigations`（`TreeViewPlugin`，`<ejs-treeview>`）
