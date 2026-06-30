---
name: DsSmartBtn（關聯單據導覽列）
category: 財務模組元件
tier: lite
status: ✅ 已產出（ERP 客製複合元件；Form View Smart Bar；1–8 個 card-btn；count/unit/label/arrow 四區；零 token gap）
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: myT6eqCVioMvVMf4PXnSlS/1029:79839
version: v0.3
last-synced: 2026-06-30
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺字面值（高度 / padding / 顏色實值）權威＝`prototyper/assets/app.css`；本檔＝契約（what/why/token-reference/a11y）。
> 來源 Figma 檔：ERP 財務作業模組（`myT6eqCVioMvVMf4PXnSlS`），非 FAI2 library。

## 1. 概述　📋

ERP Form View 中 **SummaryCard 下方**的關聯單據導覽列。每個 `card-btn` 顯示一筆關聯單據的筆數（count）+ 計數單位 + 單據名稱 + `arrow-outward` icon，點擊跳轉至對應模組。

- **何時用**：財務或進銷存模組 Form View，有 1 個以上關聯單據時，緊接在 `DsAmountSummaryCard`（或 SummaryCard）下方。
- **何時不用**：無關聯單據時整個元件**不渲染**（`v-if="visibleRelations.length > 0"`）；設定檔頁面；List View 列表行。
- **容器結構**：整條列是一個 DsSmartBtn（`.smart-bar`）；每個關聯單據是一個 `card-btn`（SmartBtn）；各 `card-btn` 之間以右側 1px primary 分隔線分隔，最後一個 `card-btn` 無分隔線。

---

## 2. Anatomy　🎨

```
DsSmartBtn（.smart-bar）
├─ bg-white，border 1px primary，radius 8px
├─ pl-16 pr-4 py-8，flex row，gap-12
│
├─ .card-btn（1..n-1 個，有 border-r）
│   ├─ flex row items-center，gap-8，pr-12
│   ├─ border-r 1px primary（右側分隔線）
│   │
│   ├─ .number 區（count + unit，兩個獨立 div，gap-2px，items-end 底部對齊）
│   │   ├─ .card-btn__count（「12」，18px/700/primary）
│   │   └─ .card-btn__unit（「/筆」，16px/400/{primary-btn-primary-bg-color-pressed}→#4287f0）
│   │
│   ├─ .card-btn__title（「銷售訂單」，16px/400/primary，nowrap）
│   │
│   └─ .card-btn__arrow
│       └─ material-symbols:arrow-outward（18×18px，container py-3px）
│
└─ .card-btn（最後一個，無 border-r）
    └─ ...（同上，pr-12，無 border-r）
```

> `count === null` 的條目（如「會計傳票」尚未產生）：省略 count+unit 文字區，僅顯示 title + arrow；card-btn 永遠渲染。
> `count === 0` 的條目：整個 card-btn 不渲染（`visibleRelations` computed 過濾掉）。

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── DsSmartBtn 容器 ──
container:
  bg:             "{color-sf-surface}"                                  # 🔗 white
  border:         "{ds-borderwidth-small} solid {color-sf-primary}"    # 🔗 1px #2877EE
  radius:         "{ds-radius-large}"                                   # 🔗 8px
  padding-left:   "{ds-space-padding-extralarge}"                      # 🔗 16px
  padding-right:  "{ds-space-margin-small}"                            # 🔗 4px
  padding-y:      "{ds-space-margin-medium}"                           # 🔗 8px
  gap:            "{ds-space-margin-large}"                            # 🔗 12px（各 card-btn 間）

# ── card-btn（各關聯單據項目）──
card-btn:
  layout:         "flex row items-center"                              # 📋
  gap:            "{ds-space-margin-medium}"                           # 🔗 8px（count 區 + title + icon 間）
  padding-right:  "{ds-space-margin-large}"                           # 🔗 12px（內容區右距，分隔線在此之後）
  divider-right:  "{ds-borderwidth-small} solid {color-sf-primary}"   # 🔗 1px #2877EE（最後項無）

# ── count（數字）──
count:
  font-size:      "{font-size-sf-h6}"                                  # 🔗 18px
  weight:         "{font-weight-sf-bold}"                              # 🔗 700
  line-height:    "1.5"                                                # 🔗
  color:          "{color-sf-primary}"                                 # 🔗 #2877EE

# ── number 區（count + unit 的容器）──
number-block:
  layout:         "flex row items-end"                                 # 📋 底部對齊（count 18px 與 unit 16px 視覺基線對齊）
  gap:            "{ds-space-margin-extrasmall}"                       # 🔗 2px

# ── unit（計數單位，緊接 count 右側）──
unit:
  font-size:      "{font-size-sf-text-lg}"                             # 🔗 16px
  weight:         "{font-weight-sf-normal}"                            # 🔗 400
  line-height:    "1.5"                                                # 🔗
  color:          "{primary-btn-primary-bg-color-pressed}"             # 🔗 var(--primary-btn-primary-bg-color-pressed, var(--ColorSf-primary, #2877EE)) → 渲染值 #4287f0（primary + white 12% overlay）

# ── title（單據名稱）──
title:
  font-size:      "{font-size-sf-text-lg}"                             # 🔗 16px
  weight:         "{font-weight-sf-normal}"                            # 🔗 400
  line-height:    "1.5"                                                # 🔗
  color:          "{color-sf-primary}"                                 # 🔗 #2877EE
  whitespace:     "nowrap"                                             # 📋

# ── arrow icon 容器 ──
arrow-container:
  size:           "18×18px"                                            # 🎨
  padding-y:      "3px"                                                # 🎨（令 icon 視覺垂直置中）
  icon:           "material-symbols:arrow-outward（18×18px）"          # 📋
  color:          "inherit（primary，同 title）"                        # 📋
```

---

## 4. Variants　🎨🔗

```yaml
# ── Figma Variant 軸：number（1–8）──
# 控制容器內 card-btn 的數量；結構與 token 跨所有 number 值完全一致

number=1:  1 個 card-btn（無 border-r）
number=2:  2 個 card-btn（第 1 個有 border-r）
number=3:  3 個 card-btn
number=4:  4 個 card-btn
number=5:  5 個 card-btn
number=6:  6 個 card-btn
number=7:  7 個 card-btn
number=8:  8 個 card-btn

# prototype（app.js）中 card-btn 數量由 visibleRelations.length 決定（data-driven）；
# Figma number 軸僅用於設計稿展示各配置，production 不需要靜態 number prop。
```

---

## 5. States　🎨🔗

| 狀態 | 呈現 |
|---|---|
| default | 白底 + primary 邊框；count 18px/700/primary；title 16px/400/primary |
| hover（card-btn） | bg 輕提亮（值見 app.css；預期 primary-opacity-8 overlay） |
| focus（card-btn） | focus ring `{ds-borderwidth-medium} {color-sf-primary}`（keyboard 導覽） |
| disabled | 本元件無 disabled 態——不可連結的單據直接不渲染（count=0 過濾） |

---

## 9. a11y　📋

- 容器根元素：`<nav aria-label="關聯單據">`（提供 landmark 語意）。
- 各 card-btn：`<a>` 或 `role="link"` + `@click.prevent`；建議 `aria-label="前往 {title}，共 {count} {unit}"` 組合供 SR 朗讀完整資訊。
- arrow icon：`aria-hidden="true"`（語意已由連結文字傳達，裝飾性）。
- 色彩對比：`{color-sf-primary}` (#2877EE) on white ≈ 3.5:1，低於 WCAG AA 一般文字要求（4.5:1）；⚠️ title（16px/400）與 unit（16px/400）均未達標。建議 DS owner 評估是否改用 `color-sf-primary-strong`（`colors_and_type.css` 已定義）。count（18px/700）屬大字（≥ 18pt bold），符合大字 3:1 標準 ✅。

---

## 11. Content　📋

- **count**：整數，來自關聯單據實際筆數；`null` 時省略 count+unit 文字區（如會計傳票尚未產生），card-btn 仍顯示。
- **unit**：通常 `/筆`；業務有特定計量單位時由各模組定義（如 `/張`、`/次`）。
- **title**：關聯單據中文名稱（如「銷售訂單」「應收單」「收款單」「會計傳票」）；建議 ≤ 10 字，`white-space: nowrap`。
- **count = 0**：整個 card-btn 不渲染，不顯示「0 筆」條目（`visibleRelations` computed filter）。

---

## 12. API / Props　📋

> prototype 為 `v-for` 資料驅動，下表為設計指引層；production 為 ERP 客製元件。

各 card-btn 的資料結構（`rel` 物件）：

| 欄位 | 型別 | 說明 |
|---|---|---|
| `type` | string | 關聯單據類型識別（如 `'sale_order'`）；用於 @click handler routing |
| `count` | number \| null | 筆數；`null` = 不顯示 count+unit，card-btn 永遠渲染 |
| `unit` | string | 計數單位（如 `'/筆'`） |
| `title` | string | 關聯單據名稱（如 `'銷售訂單'`） |

容器本身無 prop（由父層 computed `visibleRelations` 控制整個陣列，filter 掉 count=0 的條目）。

事件：`@click` → `openRelated(rel)` → deeplink modal 或 route push 至關聯模組。

---

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §Surface / §Border / §Space / §Typography
- 值權威：`prototyper/assets/app.css`（`.smart-bar` / `.card-btn` 選擇器）
- 同層：`DsAmountSummaryCard.md`（直接上方的摘要卡；DsSmartBtn 緊接其後）
- Prototyper usage：`prototyper/profiles/erp-transaction.md §Smart Bar card-btn 結構`（用法規範；HTML 結構 / visibleRelations 邏輯）；starter template `templates/module-page.html`；`REFERENCE.md §11 visibleRelations computed`
- Figma：ERP 財務作業模組 `myT6eqCVioMvVMf4PXnSlS` node `1029:79839`
- Code：ERP 客製元件，選擇器 `.smart-bar`（容器）/ `.card-btn`（各項目）
