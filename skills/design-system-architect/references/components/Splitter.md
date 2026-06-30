---
name: Splitter（分割面板）
category: 佈局與導覽
tier: lite
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: —       # 🎨 Figma 補入時填 <FILE_KEY>/<NODE_ID>
version: v0.1
last-synced: —
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺字面值權威＝`prototyper/assets/app.css`；本檔＝契約。

## 1. 概述　📋

**可調整比例的分割版面**：在兩個（或多個）相鄰面板之間放置可拖動的分隔條，讓使用者自訂各區域大小。ERP 使用場景：主從檢視（Left = 清單 / Right = 詳情）、程式碼 / 預覽並排。

何時不用 Splitter：面板比例固定不需調整 → 直接用 CSS `grid`；需要 overlay 抽屜 → `Sidebar.md`。

## 2. Anatomy　🎨

```
.e-splitter（水平或垂直）
  ├─ .e-pane                       ← 面板 A
  ├─ .e-split-bar                  ← 拖動分隔條
  │   ├─ .e-resize-handler         ← 視覺抓點（中央 dots / grip）
  │   └─ .e-collapse-btn（選配）   ← 快速折疊 / 展開按鈕
  └─ .e-pane                       ← 面板 B（可繼續串接 n 個）
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
split-bar（水平 Splitter）:
  width:          "4px"                                      # 🎨 分隔條寬度
  bg:             "{color-sf-outline-variant}"               # 🔗 預設顏色
  hover-bg:       "見 app.css"                               # 🎨 hover 加深
  dragging-bg:    "{color-sf-primary}"                       # 🔗 拖動中主色

split-bar（垂直 Splitter）:
  height:         "4px"                                      # 🎨
  bg:             "{color-sf-outline-variant}"               # 🔗

resize-handler:
  size:           "24px"                                     # 🎨
  fg:             "{color-sf-on-surface-variant}"            # 🔗
  dots-count:     3                                          # 📋 3 dots grip indicator

pane:
  min-size:       "0px（可設定，禁止強制 0）"                # 📋 建議設 120px 以上
  bg:             "{color-sf-surface}"                       # 🔗
```

## 4. Variants　📋

```yaml
horizontal:   "左右分割（面板並排）；split-bar 垂直條"
vertical:     "上下分割（面板堆疊）；split-bar 水平條"
nested:       "Splitter 巢狀（最多 2 層，避免複雜度）"
with-collapse:"分隔條含 collapse button（快速折疊至 min-size）"
```

## 5. States　🎨🔗

```yaml
split-bar:
  default:        { bg: "{color-sf-outline-variant}", cursor: "col-resize / row-resize" }  # 🔗
  hover:          { bg: "見 app.css", cursor: "col-resize" }  # 🎨
  dragging:       { bg: "{color-sf-primary}", user-select: "none" }  # 🔗
  collapsed:      { width: "0px; cursor: default" }          # 📋

pane:
  resizing:       { transition: "none（拖動時關閉 transition）" }
  collapsed:      { width: "0px; overflow: hidden" }         # 📋 min-size 設 0 才可完全折疊
```

## 9. a11y　📋

- 分隔條元素帶 `role="separator"` + `aria-label="調整面板大小"` + `aria-orientation`（horizontal/vertical）。
- 帶 `aria-valuenow`（當前百分比）/ `aria-valuemin` / `aria-valuemax`（0–100）。
- 鍵盤操作：
  - 焦點移至 split-bar 後：`←` `→`（水平）或 `↑` `↓`（垂直）移動分隔條
  - `Home` / `End`：面板最小 / 最大尺寸
  - `Enter`：折疊 / 展開（若有 collapse button）
- 拖動過程帶 `aria-busy="true"`。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| orientation | `Horizontal` \| `Vertical` | `Horizontal` | 分割方向 |
| paneSettings | PanePropertiesModel[] | [] | 每個面板的 `{ size, min, max, content?, collapsible? }` |
| separatorSize | number | 4 | 分隔條寬 / 高（px） |
| enableResizing | boolean | true | 是否可拖動調整 |

事件：`@resizeStart` / `@resizing` / `@resizeStop` / `@collapsed` / `@expanded`

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface / §On-* / §Outline / §Primary / §Space
- 值權威：`prototyper/assets/app.css`
- 相關：`Sidebar.md`（overlay 抽屜；Splitter 是永久分割）；`DataGrid.md`（常作為 Splitter 的 Left Pane 內容）
- Code：`@syncfusion/ej2-vue-layouts`（`SplitterPlugin`，`<ejs-splitter>`）
