---
name: Menu（導覽選單）
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

**多層導覽選單**：水平（橫向 navigation bar）或垂直（縱向側邊選單）的多層清單，支援子選單飛出（submenu）。ERP 目前主導覽採客製 `DsSideNavMenu`（垂直 nav-rail），Menu 元件用於橫向 navigation bar 或飛出式多層選單。

何時用 ContextMenu 替代：由右鍵 / 長按觸發的情境選單 → `ContextMenu.md`。
何時用 DropDownButton 替代：單一按鈕觸發的少量操作清單（< 5 項）→ `DropDownButton.md`。

## 2. Anatomy　🎨

```
.e-menu-wrapper
  └─ .e-menu（ul）
      └─ .e-menu-item（li）            ← 頂層項目
          ├─ .e-menu-icon             ← 選配 icon（16px）
          ├─ .e-menu-text             ← 文字
          ├─ .e-menu-caret-icon       ← chevron-right（有子選單時出現）
          └─ .e-menu（ul.e-menu-popup）← 子選單（飛出）
              └─ .e-menu-item（li）
                  ├─ .e-separator     ← 分隔線（特殊 item）
                  └─ [遞迴子選單]
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
top-level-item:
  height:         "48px"                                     # 🎨 水平 nav；垂直 nav 可為 40px
  padding-h:      "{ds-space-padding-extra-large}"           # 🔗 16px
  font:           "{font-size-sf-text-md} / {font-weight-sf-medium}"  # 🔗 14px / 500
  fg:             "{color-sf-on-surface}"                    # 🔗
  bg:             "transparent"                              # 📋 水平模式透明

submenu-popup:
  bg:             "{color-sf-surface}"                       # 🔗
  border:         "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗
  radius:         "{ds-radius-medium}"                       # 🔗 4px
  shadow:         "{ds-shadow-md}"                           # 🔗 下拉面板陰影
  min-width:      "180px"                                    # 🎨
  item-height:    "36px"                                     # 🎨
  item-padding-h: "{ds-space-padding-extra-large}"           # 🔗 16px

icon:
  size:           "16px"                                     # 🎨
  fg:             "{color-sf-on-surface-variant}"            # 🔗

separator:
  height:         "{ds-borderwidth-small}"                   # 🔗 1px
  color:          "{color-sf-outline-variant}"               # 🔗
  margin-v:       "{ds-space-padding-small}"                 # 🔗 4px
```

## 4. Variants　🎨🔗

```yaml
orientation-horizontal:
  desc: "頂部橫向 nav bar；項目水平排列；子選單向下飛出"
  open-trigger: "hover（desktop）"

orientation-vertical:
  desc: "側邊垂直清單；子選單向右飛出；scrollable 若項目超出視窗高度"
  open-trigger: "click"

hamburger（RWD）:
  desc: "< 1024px 時橫向 Menu 收合為漢堡按鈕；點擊展開 overlay 垂直選單"
  trigger:  "IconButton（menu icon）"
  overlay:  "Sidebar 模式（Over，見 Sidebar.md）"
```

## 5. States　🎨🔗

```yaml
top-level-item:
  default:        { bg: "transparent", fg: "{color-sf-on-surface}" }
  hover:          { bg: "見 app.css" }                       # 🎨 opacity8 疊層
  active（open）: { fg: "{color-sf-primary}", bg: "{color-sf-primary-container}" }  # 🔗
  disabled:       { fg: "{color-sf-on-surface-opacity38}", cursor: "not-allowed" }  # 🔗

submenu-item:
  default:        { bg: "{color-sf-surface}", fg: "{color-sf-on-surface}" }
  hover:          { bg: "見 app.css" }                       # 🎨
  selected:       { fg: "{color-sf-primary}", bg: "{color-sf-primary-container}" }  # 🔗
  disabled:       { fg: "{color-sf-on-surface-opacity38}" }  # 🔗
```

## 6. Behavior 行為　📋

| 動作 | 觸發 | 結果 | 條件 |
|------|------|------|------|
| 開啟子選單（hover） | 滑鼠 hover 頂層有子項目的項目 | 子選單面板飛出（水平 Menu） | orientation=horizontal |
| 開啟子選單（click） | 點擊有子項目的項目 | 子選單面板展開 | orientation=vertical |
| 關閉子選單 | 點擊選單外區域 / Esc | 所有子選單關閉 | — |
| 選取項目 | 點擊最底層葉節點 | 執行 `@select` 事件 + 選單關閉 | 無子選單 |
| 漢堡展開 | 點擊漢堡 IconButton | Sidebar Overlay 開啟（垂直 Menu） | viewport < 1024px |
| 連結導覽 | 點擊 url 型項目 | 路由跳轉 | items[].url 有值 |

## 7. RWD 響應式　📋

| 斷點 | 行為 |
|------|------|
| ≥ 1280px | 水平 Menu 全展開；垂直側邊 Menu 固定 |
| 1024–1279px | 水平 Menu 可能截斷尾端項目 → overflow popup |
| < 1024px | 水平 Menu 收合為漢堡按鈕 + Overlay sidebar |

## 8. Keyboard 鍵盤　📋

| 按鍵 | 行為 |
|------|------|
| `Tab` / `Shift+Tab` | 在頂層項目間移動 |
| `←` `→` | 水平 Menu：在頂層項目間移動；垂直：收合 / 展開子選單 |
| `↑` `↓` | 在子選單項目間移動 |
| `Enter` / `Space` | 展開子選單 / 選取葉節點 |
| `Esc` | 關閉最近層子選單；逐層向上關閉 |
| `Home` / `End` | 移到目前選單的第一 / 最後項目 |

## 9. a11y　📋

- 頂層容器 `role="menubar"`（水平）或 `role="menu"`（垂直）。
- 每個項目 `role="menuitem"`；有子選單者加 `aria-haspopup="true"` + `aria-expanded`。
- 子選單容器 `role="menu"` + `aria-label="<父項目文字>"`。
- 分隔線 `role="separator"`。
- 圖示僅裝飾時 `aria-hidden="true"`。

## 10. 跨平台 Adaptive　📋

- Web（≥ 1024px）：水平 / 垂直兩種模式皆可用。
- Web（< 1024px）：自動降為漢堡 + Sidebar Overlay。
- App：底部 BottomNavigation 替代水平 Menu；垂直 Menu 以 Sidebar Sheet 呈現。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| items | MenuItemModel[] | [] | `{ text, iconCss?, items?, url?, separator?, disabled? }` |
| orientation | `Horizontal` \| `Vertical` | `Horizontal` | 排列方向 |
| enableScrolling | boolean | false | 垂直模式超出時可捲動 |
| showItemOnClick | boolean | false | true = click 開啟子選單（vs hover） |

事件：`@select(e)` / `@beforeOpen(e)` / `@onOpen(e)` / `@onClose(e)`

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface / §On-* / §Primary / §Outline / §Space / §Shadow
- 值權威：`prototyper/assets/app.css`
- 相關：`ContextMenu.md`（右鍵觸發）；`DropDownButton.md`（按鈕觸發少量操作）；`Sidebar.md`（< 1024px 漢堡展開時）
- Code：`@syncfusion/ej2-vue-navigations`（`MenuPlugin`，`<ejs-menu>`）
