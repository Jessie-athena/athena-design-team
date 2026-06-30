---
name: DsSectionHeader（區段標題）
category: 佈局與導覽
tier: lite           # 色條 + 標題文字，無互動、無狀態機
status: ✅ 已產出（ERP 客製原子元件；左側藍色指示條 + 粗體標題；用於 Form / Card 區段分組）
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: JhcpyIEEzSChcEXMRJUiIm/29069:10005
version: v0.1
last-synced: 2026-06-30
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺字面值（寬度 / 顏色實值）權威＝`prototyper/assets/app.css`；本檔＝契約（what/why/token-reference/a11y）。

## 1. 概述　📋

ERP Form / Card 的**區段標題**：以左側藍色垂直指示條（accent bar）+ 粗體文字標示一個邏輯分區，提升表單層次感。

- **何時用**：Form View 的 `<section>` 標題（如「基本資料」「明細」「備註」）；Card 內的分區標題；設定檔頁面的群組標題。
- **何時不用**：Dialog 頂部標題（改用 `DsDialogHeader`）；Tab 標題（改用 `Tab` 元件）；頁面主標題（改用 heading/md token）。
- **只有一種視覺型**：accent 藍條 + 粗體文字，不分 size / variant。

## 2. Anatomy　🎨

```
.section-header（flex-col，gap=8px）
  └─ .top-title（flex，h=21px，gap=10px，items-center）
      ├─ .accent-bar（w=5px，h=100%，bg=primary）
      └─ .label（14px/700/primary）
```

> gap=8px（.section-header 的 flex-col gap）為元件與其下方內容之間的預設間距，由父層使用時可覆蓋。`.top-title` 本身的 h=21px = 14px × line-height 1.5。

## 3. 視覺規格 Tokens　🎨🔗

```yaml
top-title:
  height:       "21px"                                               # 🎨 = 14px × 1.5（一行高）
  gap:          "10px"                                               # 🎨 bar 與 label 間距（無對應 token）
  align:        "items-center"                                       # 📋

accent-bar:
  width:        "5px"                                                # 🎨 量測值
  height:       "100%（等於 top-title 高度）"                         # 📋
  bg:           "{color-sf-primary}"                                 # 🔗 #2877EE（accent/accent alias）
  radius:       "none"                                               # 📋

label:
  font:         "{font-size-sf-text-md}"                             # 🔗 14px
  weight:       "{font-weight-sf-bold}"                              # 🔗 700
  line-height:  "1.5"                                                # 🔗 對應 body/md line-height
  color:        "{color-sf-primary}"                                 # 🔗 #2877EE（與 accent-bar 同色）
  whitespace:   "nowrap"                                             # 📋（單行；超長由父層決定行為）

section-header（間距）:
  gap-below:    "{ds-space-margin-medium}"                           # 🔗 8px（元件本身 flex-col gap；與下方內容的間距）
```

## 4. Variants　🎨🔗

```yaml
# ── Figma Variants（FAI2 node 29069:10005） ──
# 本元件無 variant 維度，僅一種視覺型。
# label 文字為唯一可變內容（透過 prop 傳入）。
```

## 5. States　🎨🔗

本元件為純顯示（display-only），無 hover / active / focus / disabled 狀態。父層 `<section>` 可有 focus-within 效果（由父控制，非本元件責任）。

## 9. a11y　📋

- 本元件語意上等同區段標題：建議在 HTML 中使用 `<h2>` / `<h3>`（根據頁面層級決定），不要純用 `<div>`。
- 標題層級由父層 Form / Card 決定（prototype 可用 `<h2>` 作為預設）。
- 色彩對比：label primary (#2877EE) on white ≈ 3.5:1（14px/700 = large text ≥ 3:1 ✅）。
- accent-bar 為裝飾性，`aria-hidden="true"`（色彩訊息已由文字重複傳達）。

## 11. Content　📋

- Label 文字：區段功能名稱（如「基本資料」「明細」「收件資訊」「備註」）；建議 ≤ 10 字。
- 無 icon、無 badge、無操作按鈕（如需右側操作請在父層 `<section>` 的 header 另行放置）。
- 多語系：繁中為主；英文顯示時同一行，字型自動 fallback。

## 12. API / Props　📋

> production 為 ERP 客製原子元件；本表為設計指引層。

| Prop | Type | Default | 說明 |
|---|---|---|---|
| label | string | 'SectionHeader' | 區段標題文字 |
| level | 2 \| 3 \| 4 | 2 | HTML heading 層級（`<h2>` / `<h3>` / `<h4>`） |

無事件（純顯示元件）。

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §Typography / §Space
- 值權威：`prototyper/assets/app.css`（canonical CSS）
- 同層：`DsDialogHeader.md`（Dialog 標題，用途不同）；`SummaryCard.md`（Form 頂部卡片，DsSectionHeader 在 SummaryCard 之下的 section 使用）；`Tab.md`（Tab 切換，與 DsSectionHeader 分工：Tab=功能切換；DsSectionHeader=靜態分區）
- Code：ERP 客製原子元件
