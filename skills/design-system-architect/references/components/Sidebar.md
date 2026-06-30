---
name: Sidebar（側邊抽屜）
category: 佈局與導覽
tier: full
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: JhcpyIEEzSChcEXMRJUiIm/f9251a21ca6ed856e0308588c47a1e8b64a9605c
version: v0.1
last-synced: 2026-03-20
---

> 依 `../component-doc-schema.md`（Full 層）產出。ERP nav-rail（`.nav-rail` / `DsSideNavMenu`）為 Sidebar 的客製固定態實作；本檔為通用 Sidebar 設計契約。

## 1. 概述　📋

**側邊抽屜面板**：從頁面側邊滑入的覆蓋或推移式面板，用於主導覽、篩選面板、詳情側欄。ERP 三種用途：

1. **Nav Rail（固定）**：App Shell 左側固定導覽欄（客製 `DsSideNavMenu`）
2. **Filter Drawer（Over）**：列表篩選條件側欄（暫未實作）
3. **Detail Panel（Push）**：主從檢視右側詳情（暫未實作）

何時不用 Sidebar：內容量少（< 4 項）且不需抽屜式展開 → `Menu.md` 或 `Accordion.md`。

## 2. Anatomy　🎨

```
.e-sidebar（position fixed/absolute）
  ├─ .sidebar__header              ← 可選；title + close 按鈕
  ├─ .sidebar__content             ← 主要內容（nav list / form / tree）
  └─ .sidebar__footer              ← 可選；固定底部（版本、使用者資訊）

.e-overlay（背景遮罩，僅 Over / Slide 模式）
```

ERP Nav Rail 結構：

```
.nav-rail
  └─ [button × 5]                  ← 5 個固定導覽項（我的最愛 / 財務 / 進銷存 / 人事 / 設定檔）
      ├─ [iconify-icon]             ← 實心 icon（無後綴）
      └─ .nav-rail__label           ← 文字標籤
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
sidebar（Over / Push 模式）:
  width:          "280px"                                    # 🎨（抽屜寬度）
  bg:             "{color-sf-surface}"                       # 🔗
  border-r:       "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗（Left Sidebar）
  shadow-over:    "{ds-shadow-lg}"                           # 🔗（Over 模式才有 shadow）
  z-index:        "200"                                      # 📋

nav-rail（ERP 固定）:
  width:          "72px"                                     # 🎨
  bg:             "{color-sf-surface}"                       # 🔗
  border-r:       "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗

nav-item:
  height:         "56px"                                     # 🎨
  icon-size:      "24px"                                     # 🎨
  label-font:     "{font-size-sf-text-sm}"                   # 🔗 12px
  fg:             "{color-sf-on-surface-variant}"            # 🔗
  active-fg:      "{color-sf-primary}"                       # 🔗
  active-bg:      "{color-sf-primary-container}"             # 🔗
  active-indicator-width: "56px"                             # 🎨 pill indicator
  active-indicator-height: "32px"                            # 🎨
  active-indicator-radius: "16px"                            # 🎨

overlay:
  bg:             "{color-sf-scrim}"                         # 🔗（Over / Slide 模式遮罩）
  opacity:        "0.32"                                     # 📋
```

## 4. Variants　🎨🔗

```yaml
type-Push:
  desc: "Sidebar 開啟時將主內容向右推移（無遮罩）"
  use:  "固定篩選面板、永久側欄"

type-Over:
  desc: "Sidebar 浮動在主內容上方 + 半透明遮罩"
  use:  "RWD 小螢幕 nav、臨時篩選"

type-Auto:
  desc: "≥ 1024px = Push；< 1024px = Over（自動切換）"
  use:  "ERP 預設建議模式"

type-Slide（deprecated in ERP）:
  desc: "僅 Sidebar 自身滑入，主內容不移動、無遮罩"

position-Left:   "左側滑入（ERP nav-rail / filter drawer）"
position-Right:  "右側滑入（detail panel）"
```

## 5. States　🎨🔗

```yaml
sidebar:
  closed:         { transform: "translateX(-100%)（Left）", visibility: "hidden" }
  open:           { transform: "translateX(0)", transition: "250ms ease" }
  opening:        { transition: "transform 250ms ease" }

nav-item:
  default:        { fg: "{color-sf-on-surface-variant}", bg: "transparent" }
  hover:          { bg: "見 app.css" }                       # 🎨
  active:         { fg: "{color-sf-primary}", bg: "{color-sf-primary-container}", indicator: "pill" }  # 🔗
  focused:        { outline: "見 app.css" }                  # 🎨

overlay:
  closed:         { opacity: 0, pointer-events: none }
  open:           { opacity: 0.32, pointer-events: auto }
```

## 6. Behavior 行為　📋

| 動作 | 觸發 | 結果 | 條件 |
|------|------|------|------|
| 開啟 Sidebar | 呼叫 `.show()` / 漢堡按鈕 | Sidebar 從側邊滑入；Over 模式加遮罩 | — |
| 關閉 Sidebar | 點擊遮罩 / Esc / 呼叫 `.hide()` | Sidebar 滑出；Over 模式移除遮罩 | Over 模式 |
| 點擊 Nav item | 點擊導覽項目 | active 狀態切換；執行 `@change` | — |
| Resize 觸發 Auto | viewport 寬度跨越 1024px 閾值 | Over ↔ Push 自動切換 | type=Auto |
| Close-on-backdrop | 點擊主內容遮罩 | Sidebar 關閉 | Over 模式、`closeOnDocumentClick=true` |

## 7. RWD 響應式　📋

| 斷點 | 行為 |
|------|------|
| ≥ 1024px | Push 模式固定展開；nav-rail 常駐可見 |
| < 1024px | Over 模式，預設關閉；漢堡觸發開啟 |
| Nav Rail | 72px 固定寬，不隨斷點折疊（ERP desktop-only）|

## 8. Keyboard 鍵盤　📋

| 按鍵 | 行為 |
|------|------|
| `Tab` / `Shift+Tab` | 在 Sidebar 內容中移動焦點 |
| `Esc` | 關閉 Sidebar（Over 模式）；焦點返回觸發元素 |
| `↑↓` | Nav list 項目間移動 |
| `Enter` / `Space` | 選取 Nav item |

Over 模式開啟時：焦點鎖定（focus trap）於 Sidebar 內，`Tab` 不可逃出。

## 9. a11y　📋

- Sidebar 元素帶 `role="navigation"` + `aria-label="<功能描述>"`（如 `"主導覽"`）。
- Over 模式背景主內容帶 `aria-hidden="true"` 防止輔助技術讀取。
- Nav item 帶 `aria-current="page"` 標示當前活躍項目。
- 關閉按鈕帶 `aria-label="關閉導覽"` + `aria-expanded` 反映狀態。

## 10. 跨平台 Adaptive　📋

- Web：Push（大螢幕）/ Over（小螢幕）。
- App：Bottom Navigation bar 替代頂層 nav-rail；詳情面板以 Sheet 呈現，不用 Sidebar。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| type | `Push` \| `Over` \| `Auto` \| `Slide` | `Auto` | 開啟模式 |
| position | `Left` \| `Right` | `Left` | 滑入位置 |
| width | string | `280px` | Sidebar 寬度 |
| isOpen | boolean | false | 受控開關 |
| closeOnDocumentClick | boolean | false | Over 模式點擊遮罩關閉 |
| showBackdrop | boolean | false | Over 模式是否顯示遮罩 |

事件：`@open` / `@close` / `@created`；Nav Rail 另有 `@change(activeKey)`

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface / §On-* / §Primary / §Outline / §Scrim / §Space / §Shadow
- 值權威：`prototyper/assets/app.css`（`.nav-rail`）
- 相關：`AppBar.md`（App Shell 搭配）；`Menu.md`（小螢幕時 Sidebar 內包 Menu）
- 客製：`DsSideNavMenu`（ERP nav-rail 客製元件）
- Code：`@syncfusion/ej2-vue-navigations`（`SidebarPlugin`，`<ejs-sidebar>`）
