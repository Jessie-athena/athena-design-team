---
name: DashboardLayout（儀表板網格）
category: 佈局與導覽
tier: full
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: —       # 🎨 Figma 補入時填 <FILE_KEY>/<NODE_ID>
version: v0.1
last-synced: —
---

> 依 `../component-doc-schema.md`（Full 層）產出。視覺字面值權威＝`prototyper/assets/app.css`；本檔＝契約。

## 1. 概述　📋

**可拖拉調整的儀表板網格**：以欄數為基準的響應式網格，讓使用者拖動 Panel 調整位置 / 拖動邊緣調整尺寸。ERP 用途：Dashboard 首頁（KPI 卡、圖表 Panel 自由排列）、個人化報表佈局。

何時不用 DashboardLayout：固定佈局不需用戶拖動 → 直接 CSS Grid；需要固定比例分割 → `Splitter.md`。

## 2. Anatomy　🎨

```
.e-dashboardlayout
  └─ .e-panel                      ← 儀表板 Panel（可拖動）
      ├─ .e-panel-header（選配）   ← Panel 標題列（拖動手柄）
      │   ├─ .e-panel-title        ← Panel 名稱
      │   └─ .e-action-buttons     ← 最大化 / 關閉按鈕
      ├─ .e-panel-content          ← Panel 主要內容插槽
      └─ .e-resize-handler         ← 右下角 resize 抓手
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
layout:
  cell-size:      "100px"                                    # 🎨 基準格子尺寸（可設定）
  cell-spacing:   "{ds-space-padding-extra-large}"           # 🔗 16px Panel 間距
  bg:             "{color-sf-background}"                    # 🔗

panel:
  bg:             "{color-sf-surface}"                       # 🔗
  border:         "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗
  radius:         "{ds-radius-large}"                        # 🔗 8px
  shadow:         "none"                                     # 📋 ERP 不用 shadow

panel-header:
  height:         "40px"                                     # 🎨
  bg:             "{color-sf-surface-variant}"               # 🔗
  title-font:     "{font-size-sf-text-md} / {font-weight-sf-medium}"  # 🔗 14px / 500
  title-fg:       "{color-sf-on-surface}"                    # 🔗
  cursor:         "grab（拖動中：grabbing）"                 # 📋

resize-handler:
  size:           "16px"                                     # 🎨
  fg:             "{color-sf-on-surface-variant}"            # 🔗
  cursor:         "se-resize"                                # 📋
```

## 4. Variants　🎨🔗

```yaml
columns-12（default）: "桌面 12 欄；最大靈活度"
columns-8:             "中等螢幕 8 欄"
columns-4:             "小螢幕 4 欄"
columns-2:             "手機 2 欄（Panel 垂直堆疊）"
fixed（no-drag）:      "禁止拖動 / resize（allowDragging=false）；純響應式佈局"
```

## 5. States　🎨🔗

```yaml
panel:
  default:        { border: "{ds-borderwidth-small} {color-sf-outline-variant}" }  # 🔗
  hover:          { border-color: "{color-sf-outline}", cursor: "default" }  # 🔗
  dragging:       { shadow: "{ds-shadow-lg}", opacity: "0.85", cursor: "grabbing" }  # 🔗
  resizing:       { border-color: "{color-sf-primary}", cursor: "se-resize" }  # 🔗
  placeholder:    { bg: "{color-sf-surface-variant}", border: "dashed {ds-borderwidth-small} {color-sf-outline}" }  # 🔗 拖動時佔位格

layout:
  loading:        "Skeleton Panel 填充（見 Skeleton.md）"
  empty:          "inbox icon + '新增 Panel'（見 Empty State）"
```

## 6. Behavior 行為　📋

| 動作 | 觸發 | 結果 | 條件 |
|------|------|------|------|
| 拖動 Panel | 按住 `.e-panel-header` 拖動 | Panel 位置移動；其他 Panel 自動迴避 | allowDragging=true |
| Resize Panel | 拖動 `.e-resize-handler` | Panel 尺寸調整（依 cellSize 對齊格子）| — |
| Panel 最大化 | 點擊 maximize button | Panel 展開至全部 layout 寬度 | showMaximizeIcon=true |
| Panel 關閉 | 點擊 close button | Panel 從 layout 移除；觸發 `@panelClose` | showCloseIcon=true |
| 響應式調整 | viewport resize | columns 數自動切換；Panel 位置重排 | — |
| 儲存佈局 | 呼叫 `.serialize()` | 返回 Panel 陣列（含位置 / 尺寸）→ 存入後端 | — |

## 7. RWD 響應式　📋

| 斷點 | 欄數 | 行為 |
|------|------|------|
| ≥ 1280px | 12 | 完整儀表板；拖動 / resize 啟用 |
| 960–1279px | 8 | Panel 自動重排；縮減 cellSpacing |
| 768–959px | 4 | 多數 Panel 佔滿寬度 |
| < 768px | 2 | Panel 垂直堆疊；禁止拖動 / resize |

## 8. Keyboard 鍵盤　📋

| 按鍵 | 行為 |
|------|------|
| `Tab` | 在 Panel header 間移動焦點 |
| `Enter` / `Space` | 在 Panel header 上按下：觸發拖動模式（limited keyboard drag） |
| 方向鍵 | 拖動模式下：移動 Panel 位置 |
| `Esc` | 取消鍵盤拖動 |

注：Keyboard drag 為輔助支援，主要互動仍為滑鼠。

## 9. a11y　📋

- Panel 帶 `role="region"` + `aria-label="<Panel 標題>"`。
- 拖動手柄帶 `aria-grabbed`（拖動中 = true）。
- 關閉 / 最大化按鈕帶明確 `aria-label`。
- 拖動完成後焦點返回 Panel header。

## 10. 跨平台 Adaptive　📋

- Web（桌面）：完整拖動 / resize 體驗。
- Web（< 768px）：禁止拖動，Panel 垂直堆疊。
- App：無 DashboardLayout；以 `ScrollView + Card 列表` 替代；讓使用者收藏特定卡片。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| columns | number | 5 | 欄數（建議依斷點動態設定）|
| cellSpacing | number[] | [5, 5] | Panel 間距 [水平, 垂直]（px）|
| allowDragging | boolean | true | 允許拖動 Panel |
| allowResizing | boolean | true | 允許 resize Panel |
| panels | DashboardLayoutPanel[] | [] | `{ id, row, col, sizeX, sizeY, header?, content? }` |

方法：`.serialize()` / `.addPanel(panel)` / `.removePanel(id)` / `.movePanel(id, row, col)`

事件：`@dragStart` / `@drag` / `@dragStop` / `@resizeStart` / `@resizeStop` / `@panelClose`

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface / §On-* / §Outline / §Primary / §Shadow / §Space / §Radius
- 值權威：`prototyper/assets/app.css`
- 子元件：`Card.md`（Panel 內常用卡片外殼）；`Skeleton.md`（Panel loading 狀態）；`IconButton.md`（Panel 動作按鈕）
- Code：`@syncfusion/ej2-vue-layouts`（`DashboardLayoutPlugin`，`<ejs-dashboardlayout>`）
