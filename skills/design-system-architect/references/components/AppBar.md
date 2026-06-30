---
name: AppBar（頂部應用列）
category: 佈局與導覽
tier: lite
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: —       # 🎨 Figma 補入時填 <FILE_KEY>/<NODE_ID>
version: v0.1
last-synced: —
---

> 依 `../component-doc-schema.md`（Lite 層）產出。ERP 實作對應 prototyper 的 `.erp-header`；本檔為其設計契約。

## 1. 概述　📋

**頂部固定應用列**：固定於視窗頂部，承載應用 Home 入口、麵包屑導覽、使用者動作（收藏、通知、設定、頭像）。ERP 一律使用 Top 固定模式；無 scrollable / scroll-to-hide。

何時不用：ERP 禁止 Bottom AppBar；禁止 ColorMode=Dark / Primary（固定 Light）。

## 2. Anatomy　🎨

```
.erp-header（.e-appbar）
  ├─ .erp-header__home              ← Home IconButton（實心 home icon；見 IconButton.md）
  ├─ .erp-breadcrumb                ← 麵包屑導覽（見 Breadcrumb.md）
  │   ├─ [span] module              ← 模組分類
  │   ├─ .erp-breadcrumb__sep       ← chevron-right icon
  │   ├─ [span] feature             ← 功能名稱
  │   ├─ .erp-breadcrumb__sep
  │   └─ [span] docNo               ← 單號（或 '—'）
  └─ .erp-header__actions           ← 右側操作群
      ├─ [button] favorite          ← 收藏 toggle（star / star-outline）
      ├─ [button] notifications     ← 通知
      ├─ [button] settings          ← 設定
      └─ .erp-header__avatar        ← 使用者首字母徽章（見 Avatar.md 待建立）
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
bar:
  height:         "56px"                                     # 🎨 量測值（ERP Desktop）
  bg:             "{color-sf-surface}"                       # 🔗 白底，無透明度
  border-b:       "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗
  padding-h:      "{ds-space-padding-extra-large}"           # 🔗 16px 兩端
  position:       "sticky; top: 0; z-index: 100"            # 📋

breadcrumb:
  font:           "{font-size-sf-text-md} / {font-weight-sf-medium}"  # 🔗 14px / 500
  fg:             "{color-sf-on-surface}"                    # 🔗 通用文字
  sep-fg:         "{color-sf-on-surface-variant}"            # 🔗 分隔符號略淡

action-icon:
  size:           "24px"                                     # 🎨
  fg:             "{color-sf-on-surface-variant}"            # 🔗
  hover-bg:       "見 app.css"                               # 🎨 opacity8 疊層

avatar:
  size:           "32px"                                     # 🎨
  bg:             "{color-sf-primary}"                       # 🔗（預設 primary；可覆寫）
  fg:             "{color-sf-on-primary}"                    # 🔗
  font:           "{font-size-sf-text-sm} / {font-weight-sf-semi-bold}"  # 🔗 12px / 600
  radius:         "50%"                                      # 📋 圓形
```

## 4. Variants　📋

```yaml
top（ERP 唯一模式）:
  position:     "sticky; top: 0"
  colorMode:    "Light（bg white + border-b）"              # 禁 Dark / Primary mode
```

## 5. States　🎨🔗

```yaml
default:          { bg: "{color-sf-surface}" }               # 🔗 無 shadow
scrolled:         { border-b: "{ds-borderwidth-small} {color-sf-outline-variant}" }  # 🔗 border 已有故不加 shadow
favorite-on:      { icon: "material-symbols:star（實心）", fg: "{color-sf-primary}" }  # 📋
favorite-off:     { icon: "material-symbols:star-outline", fg: "{color-sf-on-surface-variant}" }  # 📋
```

## 9. a11y　📋

- 元素為 `<header role="banner">`。
- Home 按鈕帶 `aria-label="首頁"`。
- 右側 action 各帶明確 `aria-label`（`aria-label="通知"` / `aria-label="設定"` / `aria-label="使用者選單"`）。
- 收藏 toggle 帶 `aria-pressed="true/false"`。
- 麵包屑最後一個節點標 `aria-current="page"`。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| colorMode | `Light` \| `Dark` \| `Primary` | `Light` | ERP 固定 Light；禁其他 |
| isSticky | boolean | `true` | ERP 固定 true |

ERP 實作：直接使用 prototyper 的 `.erp-header` HTML 結構，不透過 `ejs-appbar` Vue plugin；`ejs-appbar` 可作為 CSS 基底參考。

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface / §On-* / §Outline / §Space / §Typography
- 值權威：`prototyper/assets/app.css`（`.erp-header`）
- 子元件：`Breadcrumb.md`；`IconButton.md`；Avatar.md（待建立）
- 相關：`Sidebar.md`（App Shell 側邊 nav-rail）；`Breadcrumb.md`
- Code：`@syncfusion/ej2-vue-navigations`（`AppBarPlugin`，`<ejs-appbar>`）；ERP 直接 HTML
