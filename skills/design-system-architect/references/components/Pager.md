---
name: Pager（分頁器）
category: 資料展示與表格
tier: lite           # 單一導航控制，無資料列生命週期
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）；用法＝prototype 各 List View 頁尾
figma-node: —        # 🎨 Figma 補入時填 <FILE_KEY>/<NODE_ID>
version: v0.2
last-synced: 2026-06-30
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺字面值（高度 / 間距）權威＝`prototyper/assets/app.css`；本檔＝契約（what/why/token-reference）。

## 1. 概述　📋

列表資料的分頁導航：每頁筆數選擇、目前範圍顯示、前後頁切換。固定出現在 `.dg`（DataGrid）或 ListView 下方，與表格連體，**不獨立存在**。

何時**不用**：資料量極少（< 20 筆）可全部顯示時省略分頁；無限捲動場景改 virtual scroll（非此元件）。

## 2. Anatomy　🎨

```
.pager
  ├─ .pager__size       ← 每頁筆數 <select>（20/50/100）
  ├─ .pager__range      ← 顯示範圍 span（「1–20 / 共 348 筆」）
  └─ .pager__nav        ← 上下頁按鈕組
       ├─ <button> prev  ← chevron-left icon；disabled 時 pointer-events:none
       └─ <button> next  ← chevron-right icon；disabled 時 pointer-events:none
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
height:    "見 app.css .pager"                # 🎨 量測值，單一來源在 app.css
bg:        "{color-sf-surface}"               # 🔗 白底，與 DataGrid 連體不畫額外邊框
border:    "top: none"                        # 與 DataGrid 共用 outer border，不補頂線
font:      "{font-size-sf-text-md}"           # 🔗 14px
fg:        "{color-sf-on-surface-variant}"    # 🔗 次要灰字（範圍文字）
padding:   "{ds-space-padding-extra-large}"   # 🔗 16px 左右內距
gap:       "{ds-space-padding-medium}"        # 🔗 8px 元素間距

size-select:
  appearance: "styled select（對齊 input--inline 底線式）"
  min-width: "見 app.css"                     # 🎨 量測值

nav-btn:
  size:    "見 app.css .pager button"         # 🎨 量測值
  icon:    "20px"                             # 🎨 chevron icon size
  radius:  "{ds-radius-small}"                # 🔗 2px
```

## 4. Variants　🎨🔗

```yaml
default:     "每頁 20/50/100；右側上下頁"    # 唯一型，無額外 variant
compact:     "隱藏 .pager__range；只保留 prev/next（嵌入 smart-bar 等窄空間用）"
```

## 5. States　🎨🔗

```yaml
prev-btn:
  default:   { fg: "{color-sf-primary}" }
  hover:     { bg: "{color-sf-primary-opacity-12}" }   # 🔗
  disabled:  { fg: "{color-sf-on-surface-opacity38}", cursor: "not-allowed" }  # 🔗 已在第一頁

next-btn:
  disabled:  { fg: "{color-sf-on-surface-opacity38}", cursor: "not-allowed" }  # 🔗 已在最後頁

size-select:
  focus:     { outline: "{ds-borderwidth-medium} {color-sf-primary}", offset: "2px" }  # 🔗
```

## 9. a11y　📋

- 前後頁按鈕需 `aria-label="上一頁"` / `"下一頁"`；disabled 時補 `aria-disabled="true"`。
- 每頁筆數 `<select>` 需 `<label>`（可 `sr-only`）: `"每頁顯示"`.
- 範圍文字（`1–20 / 共 348 筆`）提供 `aria-live="polite"` 讓 screen reader 在頁面切換後播報新範圍。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| currentPage | number | 1 | 目前頁碼（1-based） |
| pageSize | number | 10 | 每頁筆數 |
| pageSizes | number[] \| boolean | false | 頁數下拉選項；`true` 使用預設選項；`[20,50,100]` 自訂選項 |
| totalRecordsCount | number | — | 總筆數（計算 totalPages 與 range 文字） |
| pageCount | number | 10 | 頁碼按鈕數量（可見的頁碼 link 個數） |

事件：`@click({ currentPage, pageSize })` — 頁碼或每頁筆數改變時觸發

> **注意**：元件為 Syncfusion EjsPager（`@syncfusion/ej2-vue-grids` 的 `PagerComponent`）；需額外 `Pager.Inject(PagerDropDown)` 才能啟用 `pageSizes` 下拉功能。獨立自訂 `.pager` markup 可依此介面命名 prop，但須自行實作分頁邏輯。

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface / §On-* / §Space / §Radius
- 值權威：`prototyper/assets/app.css`（canonical CSS）
- 上游：`DataGrid.md`（Pager 固定配置於 DataGrid 下方）；`ListView.md`（ListView 尾部）
- Code：DataGrid.vue 內建 Syncfusion Grid Pager；獨立分頁由 `@syncfusion/ej2-vue-grids` pagerTemplate 或客製 `.pager` markup
