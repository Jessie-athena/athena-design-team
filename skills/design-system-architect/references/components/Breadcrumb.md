---
name: Breadcrumb（麵包屑導覽）
category: 佈局與導覽
tier: lite
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: —       # 🎨 Figma 補入時填 <FILE_KEY>/<NODE_ID>
version: v0.1
last-synced: —
---

> 依 `../component-doc-schema.md`（Lite 層）產出。ERP 實作對應 prototyper `.erp-breadcrumb`；本檔為其設計契約。

## 1. 概述　📋

**階層路徑導覽**：顯示用戶當前位置於應用層級中的完整路徑，輔助定向與回溯。ERP 三層固定結構：**模組分類 > 功能名稱 > 單號**，位於 AppBar 中間區。

何時不用 Breadcrumb：單層頁面無需導覽路徑；深度超過 4 層時考慮折疊中間節點。

## 2. Anatomy　🎨

```
.erp-breadcrumb（.e-breadcrumb）
  ├─ [span] 模組分類               ← 第一層（純文字，不可點擊）
  ├─ .erp-breadcrumb__sep          ← chevron-right icon（分隔符號）
  ├─ [span] 功能名稱               ← 第二層（可點擊，跳回功能列表）
  ├─ .erp-breadcrumb__sep          ← chevron-right icon
  └─ [span] 單號 / '—'             ← 第三層（當前頁，不可點擊）
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
item:
  font:           "{font-size-sf-text-md} / {font-weight-sf-medium}"  # 🔗 14px / 500
  fg-default:     "{color-sf-on-surface}"                    # 🔗 可點擊節點
  fg-current:     "{color-sf-on-surface-variant}"            # 🔗 最後節點（當前頁）略淡
  fg-hover:       "{color-sf-primary}"                       # 🔗 hover 變主色
  gap:            "{ds-space-padding-small}"                 # 🔗 4px（item ↔ sep ↔ item）

separator:
  icon:           "material-symbols:chevron-right"           # 📋
  size:           "16px"                                     # 🎨
  fg:             "{color-sf-on-surface-variant}"            # 🔗

overflow（> 4 層時）:
  collapsed-icon: "material-symbols:more-horiz"              # 📋 折疊中間節點
```

## 4. Variants　📋

```yaml
erp-standard:    "3 層（模組 > 功能 > 單號）；固定於 AppBar"
with-home-icon:  "第一層以 home icon 替代文字（僅首頁 / Dashboard 模組）"
collapsed:       "4+ 層時折疊中間節點為 '…'（ERP 目前無需）"
```

## 5. States　🎨🔗

```yaml
clickable-item:
  default:        { fg: "{color-sf-on-surface}" }            # 🔗
  hover:          { fg: "{color-sf-primary}", text-decoration: "underline" }  # 🔗
  focus:          { outline: "見 app.css" }                  # 🎨

current-item（最後節點）:
  default:        { fg: "{color-sf-on-surface-variant}", cursor: "default" }  # 🔗 不可點擊
```

## 9. a11y　📋

- 外層容器為 `<nav aria-label="breadcrumb">`。
- 內層列表 `<ol>`（語意有序路徑）。
- 可點擊節點為 `<a>` 或 `<button>`；最後節點（當前頁）加 `aria-current="page"`，且不包含互動角色。
- 分隔符號 icon 帶 `aria-hidden="true"`（裝飾性）。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| items | BreadcrumbItem[] | [] | `{ text, url?, iconCss?, disabled? }`；最後一項自動加 `aria-current` |
| maxItems | number | — | 超出時折疊中間節點（省略號）|
| itemTemplate | string / slot | — | 自訂項目渲染 |
| separatorTemplate | string / slot | — | 自訂分隔符號（ERP 固定 chevron-right icon）|

事件：`@itemClick(e)` / `@beforeItemRender(e)`

ERP 實作：直接寫 `.erp-breadcrumb` HTML 結構（`span + icon + span` 組合），不透過 `ejs-breadcrumb` plugin。

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §On-* / §Primary / §Space / §Typography
- 值權威：`prototyper/assets/app.css`（`.erp-breadcrumb`）
- 父元件：`AppBar.md`（Breadcrumb 位於 AppBar 中間區）
- Code：`@syncfusion/ej2-vue-navigations`（`BreadcrumbPlugin`，`<ejs-breadcrumb>`）；ERP 直接 HTML
