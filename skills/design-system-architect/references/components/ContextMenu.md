---
name: ContextMenu（情境右鍵選單）
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

**情境式彈出選單**：由右鍵點擊或長按觸發，提供與目標元素相關的快捷操作。常見於 DataGrid 行右鍵操作（檢視 / 編輯 / 複製 / 作廢）、TreeView 節點操作。

何時不用 ContextMenu：
- 需持久顯示的導覽 → `Menu.md`
- 按鈕觸發的操作清單 → `DropDownButton.md`
- 批次操作（多選後）→ `Toolbar.md` 批次操作區

## 2. Anatomy　🎨

```
.e-contextmenu-wrapper
  └─ .e-contextmenu（ul，viewport 內定位）
      └─ .e-menu-item（li）            ← 選單項目
          ├─ .e-menu-icon             ← 選配 icon（16px）
          ├─ .e-menu-text             ← 文字
          ├─ .e-menu-caret-icon       ← chevron-right（有子選單時）
          └─ .e-separator             ← 分隔線（特殊 item）
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
popup:
  bg:             "{color-sf-surface}"                       # 🔗
  border:         "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗
  radius:         "{ds-radius-medium}"                       # 🔗 4px
  shadow:         "{ds-shadow-md}"                           # 🔗
  min-width:      "180px"                                    # 🎨
  max-width:      "280px"                                    # 🎨

item:
  height:         "36px"                                     # 🎨（比 Menu 頂層略緊湊）
  padding-h:      "{ds-space-padding-extra-large}"           # 🔗 16px
  font:           "{font-size-sf-text-md}"                   # 🔗 14px
  fg:             "{color-sf-on-surface}"                    # 🔗

icon:
  size:           "16px"                                     # 🎨
  fg:             "{color-sf-on-surface-variant}"            # 🔗

separator:
  height:         "{ds-borderwidth-small}"                   # 🔗 1px
  color:          "{color-sf-outline-variant}"               # 🔗
  margin-v:       "{ds-space-padding-small}"                 # 🔗 4px

danger-item:
  fg:             "{color-sf-error}"                         # 🔗（作廢 / 刪除等破壞性操作）
```

## 4. Variants　📋

```yaml
default:           "純文字項目（無 icon）"
with-icons:        "每項帶 16px icon；icon 欄固定寬 32px 對齊"
with-groups:       "separator 分隔操作群組"
with-submenu:      "子選單向右飛出（不超出 viewport 時；否則向左）"
```

## 5. States　🎨🔗

```yaml
item:
  default:        { bg: "{color-sf-surface}", fg: "{color-sf-on-surface}" }
  hover:          { bg: "見 app.css" }                       # 🎨 opacity8 疊層
  active:         { bg: "{color-sf-primary-container}" }     # 🔗
  disabled:       { fg: "{color-sf-on-surface-opacity38}", cursor: "not-allowed" }  # 🔗
  danger-hover:   { bg: "{color-sf-error-container}" }       # 🔗（破壞性動作）

popup:
  entering:       "fade-in + scale（200ms）"                 # 📋
  exiting:        "fade-out（150ms）"                        # 📋
```

## 9. a11y　📋

- 彈出層帶 `role="menu"` + `aria-label="<目標元素描述> 操作選單"`。
- 觸發元素（右鍵區域或觸發按鈕）帶 `aria-haspopup="true"`。
- 每個項目 `role="menuitem"`；破壞性項目建議帶 `aria-label`（含操作後果描述）。
- 分隔線 `role="separator"`。
- 鍵盤操作：
  - `↑↓`：在項目間移動
  - `Enter` / `Space`：執行操作
  - `Esc`：關閉選單，焦點返回觸發位置
  - `→`：開啟子選單
  - `←`：關閉子選單，返回上層

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| items | MenuItemModel[] | [] | 同 Menu；支援 `separator: true` |
| target | string（CSS selector） | — | 右鍵目標元素；可綁定 Grid row / TreeView node |
| animationSettings | Object | `{ effect: 'SlideDown', duration: 400 }` | 彈出動效 |
| filter | string | — | target 內子元素篩選器（如只在 `tr` 上右鍵有效） |

事件：`@select(e)` / `@beforeOpen(e)` / `@onOpen(e)` / `@onClose(e)`

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface / §On-* / §Primary / §Error / §Outline / §Space / §Shadow
- 值權威：`prototyper/assets/app.css`
- 相關：`Menu.md`（持久導覽選單）；`DropDownButton.md`（按鈕觸發操作清單）；`DataGrid.md`（行右鍵常見目標）；`TreeView.md`（節點右鍵）
- Code：`@syncfusion/ej2-vue-navigations`（`ContextMenuPlugin`，`<ejs-contextmenu>`）
