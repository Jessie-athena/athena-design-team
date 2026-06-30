---
name: PivotView（樞紐分析視圖）
category: 資料展示與表格
tier: full           # 資料密集：多維交叉分析 + drill-down + 欄列動態重組生命週期
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: —        # 🎨 Figma 補入時填 <FILE_KEY>/<NODE_ID>（FAI2 componentKey: 3bae2bd5b322767b45757e00cafbf11a849155cd）
version: v0.2
last-synced: 2026-06-30
---

> 依 `../component-doc-schema.md`（Full 層）產出。視覺字面值權威＝`prototyper/assets/app.css`；本檔＝契約（what/why/token-reference/state/a11y）。

## 1. 概述　📋

**多維交叉分析表**：使用者可拖放欄位至列維度（Rows）/ 欄維度（Columns）/ 值（Values）/ 篩選器（Filters），即時交叉統計。適用場景：銷售報表分析、庫存分類統計、財務多維彙總。

何時**不用**：固定格式報表（無需使用者拖放重組）→ 靜態 DataGrid 加合計列即可；需要公式計算 → Spreadsheet。

## 2. Anatomy　🎨

```
.pv                              ← PivotView 根容器（flex column）
  ├─ .pv__field-list             ← 欄位清單面板（可收起至右側）
  │   ├─ .pv__field-panel--rows      ← 「列維度」拖放區
  │   ├─ .pv__field-panel--cols      ← 「欄維度」拖放區
  │   ├─ .pv__field-panel--values    ← 「值」拖放區（可選聚合函式）
  │   └─ .pv__field-panel--filters   ← 「篩選器」拖放區
  └─ .pv__table-wrap             ← 交叉表格捲動容器（overflow:auto）
       └─ .pv__table             ← 交叉 table（border-collapse:separate）
            ├─ .pv__col-header   ← 欄表頭（可多層，如 年 → 季 → 月）
            ├─ .pv__row-header   ← 列表頭（可展開 / 收合 drill-down）
            └─ .pv__value-cell   ← 交叉值格子（數值右對齊）
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
table:
  header-bg:   "見 app.css .pv__col-header / .pv__row-header"  # 🎨 primary 疊白實色
  header-font: "{font-size-sf-text-md} / {font-weight-sf-medium}"  # 🔗 14px / 500
  header-fg:   "{color-sf-on-surface}"                         # 🔗
  value-font:  "{font-size-sf-text-md}"                        # 🔗 14px
  value-align: "right"                                         # 數值右對齊
  row-border:  "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗 列間 1px
  grand-total:
    bg:        "見 app.css .pv__grand-total"                   # 🎨 略深底色，單一來源 app.css
    font-weight: "{font-weight-sf-medium}"                     # 🔗 500

field-list:
  width:       "280px"                                         # 🎨 量測值
  bg:          "{color-sf-surface-variant}"                    # 🔗 灰底面板
  item-height: "36px"                                          # 🎨 量測值
  chip-bg:     "{color-sf-primary-opacity-12}"                 # 🔗 已拖入欄位 chip 底色
  chip-fg:     "{color-sf-primary}"                            # 🔗

drill-down:
  indent:      "16px per level"                                # 🎨 量測值
  icon:        "chevron-right / chevron-down，16px"            # 🎨
```

## 4. Variants / Types　🎨🔗

```yaml
pivot-table:     "預設；交叉表格 + 欄位清單面板"
pivot-chart:     "切換至圖表模式（Bar/Line/Column）；由 toolbar 切換 icon 控制"
compact:         "欄位清單收起（側邊面板 slide-in 呼叫）；只顯示 .pv__table-wrap"
```

## 5. States　🎨🔗

| 狀態 | 呈現 |
|---|---|
| empty（無欄位拖入） | `.pv__table-wrap` 顯示 `inbox-outline` icon + 「請拖放欄位至左側面板以建立報表」 |
| loading | Skeleton 格子覆蓋 `.pv__table-wrap`（保留表頭結構） |
| error | 區塊錯誤訊息 + 「重新整理」；替換 `.pv__table-wrap` 內容 |
| drill-down collapsed | 列表頭 chevron-right；子維度隱藏 |
| drill-down expanded | 列表頭 chevron-down；子維度列展開 |
| sorting | 欄 / 列表頭出現排序 icon；active 排序欄底色略深（見 app.css） |
| drag-over（欄位拖入） | 目標 panel 邊框換 `{color-sf-primary}` 2px + 底色 `{color-sf-primary-opacity-12}` |

## 6. Behavior　📋

| 互動 | 行為 |
|---|---|
| 拖放欄位 | 從欄位清單拖至 Rows / Columns / Values / Filters；改變後即時重算交叉表 |
| 排序 | 點欄表頭 → 值依該欄升/降排序；再點切換；三態（預設/升/降） |
| Drill-down | 點列表頭中的展開鈕，展開下一個維度層；再點收合；最多三層 |
| 篩選器 | 拖至 Filters 後，點 chip 開 filter dropdown 設定值域；支援多值 |
| 移除欄位 | chip 右側 × 移除；或拖回欄位清單區 |
| 聚合函式 | 值區 chip 右鍵選 Sum/Count/Avg/Max/Min/CountDistinct |
| 小計 / 總計 | toolbar 切換 showRowTotals / showColTotals / showGrandTotal |

## 7. RWD / 斷點　📋

| 斷點 | 呈現 |
|---|---|
| ≥ 1440px | 欄位清單常駐左側；交叉表格佔剩餘寬 |
| 1024–1439px | 欄位清單預設收起；toolbar icon 呼叫 slide-in panel |
| < 1024px | **欄位清單改為底部 sheet**；交叉表格滿版；橫向 scroll 處理多欄 |
| < 768px | 不做（樞紐分析為桌面功能）|

## 8. Keyboard　📋

- 表頭可 focus；`Enter` 觸發排序；`↑↓` 在行內移動。
- Drill-down expander：`Enter` / `Space` 展開/收合。
- 欄位清單鍵盤：欄位 `focus` + `Enter` 彈 dropdown 選取目標 panel。
- Drag-and-drop 鍵盤替代：欄位 context menu（右鍵 / `Menu` 鍵）→「移至 [panel]」。

## 9. a11y　📋

- `role="grid"` 搭配 `aria-rowcount` / `aria-colcount`（動態更新）。
- 每個交叉值格子需 `aria-label="[列維度] × [欄維度]: [值]"`。
- 排序表頭 `aria-sort="ascending"` / `"descending"` / `"none"`。
- 數值右對齊但需 `dir="ltr"` 確保 RTL 環境下數值顯示正確。
- 對比：表頭 bg 需文字對比 ≥ 4.5:1（primary 疊白實色，見 `reference_ds_aa_strong_tokens` memory）。

## 10. 跨平台 Adaptive　📋

- **桌面**：欄位清單常駐；完整 pivot table + drill-down + 排序。
- **平板**：欄位清單改 slide-in；交叉表格橫捲；drill-down 改觸控友善 44px 展開鈕。
- **手機**：改以固定格式報表（DataGrid + 合計列）替代，不做可互動 pivot。

## 11. Content　📋

- 空值一律 `—`；**禁**「null」/ 「0」（0 值應顯示 0，區別於 null）。
- grand-total 標籤：「總計」；小計：「[維度名] 小計」。
- 聚合函式標籤：「總和」「計數」「平均」「最大」「最小」「不重複計數」。
- error state：「報表資料載入失敗，請重新整理或聯繫系統管理員」。

## 12. API / Props　📋

> ⚠️ **重要**：PivotView 的維度設定全部集中在 `dataSourceSettings` 單一物件中，**不是**頂層分離 prop。

| Prop | Type | Default | 說明 |
|---|---|---|---|
| dataSourceSettings | DataSourceSettings | — | **主設定物件**，包含以下所有子屬性 |
| ↳ dataSource | Object[] \| DataManager | [] | 原始資料 |
| ↳ rows | FieldOptions[] | [] | 列維度欄位（左側維度） |
| ↳ columns | FieldOptions[] | [] | 欄維度欄位（上方維度） |
| ↳ values | FieldOptions[] | [] | 值欄位 + 聚合函式 |
| ↳ filters | FieldOptions[] | [] | 篩選維度 |
| ↳ expandAll | boolean | false | 預設展開所有群組 |
| ↳ formatSettings | FormatSettings[] | [] | 數值格式（貨幣 / 百分比等） |
| showFieldList | boolean | false | 顯示欄位清單面板（拖放調整維度，需注入 `FieldList` 模組） |
| showGroupingBar | boolean | false | 顯示分組列（需注入 `GroupingBar` 模組） |
| enableVirtualization | boolean | false | 虛擬捲動（大資料量優化） |
| height | string \| number | 'auto' | 元件高度 |
| displayOption | 'Table' \| 'Chart' \| 'Both' | 'Table' | 顯示模式 |

> **注意**：`showRowSubTotals`、`showColumnSubTotals`、`showGrandTotals` 是 `dataSourceSettings` 的子屬性，非頂層 prop。模組需 `provide('pivotview', [FieldList, CalculatedField, GroupingBar])` 注入。

事件：`@dataBound` / `@drillThrough({ columnHeaders, rowHeaders, value })`

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §Surface / §On-* / §Outline / §Space / §Typography
- 值權威：`prototyper/assets/app.css`（canonical CSS）
- 同層：`DataGrid.md`（固定報表替代）；`Spreadsheet.md`（公式計算場景）
- Code：`@syncfusion/ej2-vue-pivotview`
