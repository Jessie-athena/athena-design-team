---
name: TreeGrid（樹狀資料表格）
category: 資料展示與表格
tier: full           # DataGrid 超集：帶 parent-child 展開 / 收合 + 層級縮排生命週期
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）；行為層上游＝DataGrid.md
figma-node: —        # 🎨 Figma 補入時填 <FILE_KEY>/<NODE_ID>（FAI2 componentKey: 3e633b7a5170a16f42dd7f0e8036aaae2f77b1b0）
version: v0.2
last-synced: 2026-06-30
---

> 依 `../component-doc-schema.md`（Full 層）產出。**繼承 `DataGrid.md` 全部規格**，本檔只補 tree-specific 差異。視覺字面值（尺寸 / 層級縮排 / 展開鈕色）權威＝`prototyper/assets/app.css`；本檔＝契約。

## 1. 概述　📋

在 DataGrid 的基礎上承載**父子層級資料**（BOM 清單、組織架構、科目樹、倉庫位置樹），每列可展開/收合以顯示子列。繼承 DataGrid 所有互動（選取、凍結欄、行內編輯、斑馬）；**差異僅在首欄呈現 tree controls**。

何時改用 DataGrid：資料無層級關係（平面列表）→ DataGrid。需查看整棵樹結構（展開所有節點後仍可篩選）→ TreeGrid。

## 2. Anatomy　🎨

繼承 `DataGrid.md §2`，差異如下：

```
.tg（extends .dg）
  └─ table
       └─ tr.tg__node
            └─ td.tg__primary-col    ← 主欄（固定第一欄）
                 ├─ .tg__indent       ← width = depth × indentSize（見 §3）
                 ├─ .tg__expander     ← expand/collapse 切換鈕（葉節點隱藏）
                 │   ├─ [expanded]    ← chevron-down icon
                 │   └─ [collapsed]   ← chevron-right icon
                 └─ .tg__cell-content ← 欄位文字（貼 expander 右側）
```

葉節點（`hasChildren=false`）：`.tg__expander` 以 `.tg__leaf-indent`（同寬空白 div）取代，維持縮排對齊。

## 3. 視覺規格 Tokens　🎨🔗

繼承 DataGrid.md §3 全部 token。TreeGrid-specific 差異：

```yaml
indent:
  size:      "20px per depth level"            # 🎨 量測值，單一來源在 app.css
  max-depth: 5                                 # 超過 5 層顯示截斷（UX 建議）

expander:
  size:      "20px"                            # 🎨 icon 尺寸
  fg:        "{color-sf-on-surface-variant}"   # 🔗 灰色 chevron
  hover-fg:  "{color-sf-primary}"              # 🔗 hover 主色

parent-row:
  font-weight: "{font-weight-sf-medium}"       # 🔗 500（子列繼承 regular 400）
  bg:          "{color-sf-surface}"            # 🔗 無額外底色（靠縮排區分層級）

loading-child:
  "子節點展開時 lazy load 期間顯示 Skeleton 列（縮排同深度）"
```

## 4. Variants / Types　🎨🔗

```yaml
flat-tree:      "父子同欄；expander 在首欄（預設型）"
grouped-header: "可選：群組列 .group-row 替代葉前一層（科目群組模式）；參見 DataGrid.md §5 群組列"
checkable:      "每節點帶 checkbox；父節點 indeterminate（部分子選取）/ checked（全子選取）"
```

## 5. States　🎨🔗

繼承 DataGrid.md §5 全部列互動多層（斑馬 / hover / selected）與資料生命週期（empty / loading / error）。TreeGrid-specific 差異：

| 狀態 | 呈現 |
|---|---|
| collapsed（預設） | 子列 `display:none`；expander=chevron-right |
| expanded | 子列顯示；expander=chevron-down |
| loading-children | 展開觸發 lazy load → Skeleton 子列（縮排同深度）；載入完成後替換 |
| leaf-node | `.tg__expander` 改為空白間隔 `.tg__leaf-indent`；不可交互 |
| parent-selected | 僅選父列本身（不自動聯選子列，除非 `checkable` 模式） |
| all-collapsed | 僅根節點可見；適合搜尋結果高亮時臨時展開特定路徑 |

## 6. Behavior　📋

繼承 DataGrid.md §6 全部互動規則。TreeGrid-specific 新增：

| 互動 | 行為 |
|---|---|
| 點擊 expander | 展開/收合直接子列；**不影響**選取狀態 |
| 雙擊父列 row | 同點擊 expander（展開/收合），不進入 Form View |
| 鍵盤 ← → | 收合/展開目前節點（Arrow Left=收合 / Arrow Right=展開） |
| 篩選 / 搜尋 | 符合條件的節點高亮並**自動展開路徑**至符合節點；不符合的父節點仍保留顯示（路徑可達） |
| 展開全部 / 收合全部 | Toolbar 額外按鈕（`unfold-more` / `unfold-less` icon）；依 TreeGrid 最大深度控制 |
| 行內編輯（若啟用） | 同 DataGrid `.dg-lines`；父子列可各自進入編輯，不互鎖 |
| Checkbox 父節點 | 勾選父 → 全勾所有子；部分子勾 → 父 indeterminate；取消父 → 全取消子 |

## 7. RWD / 欄位優先級　📋

與 DataGrid.md §7 完全相同（P0–P3 + 橫向 scroll）。TreeGrid 主欄（含縮排 + expander）列為 **P0 必要**，不得隱藏。

## 8. Keyboard　📋

繼承 DataGrid.md §8，額外：

- `→`（右鍵）：展開已收合節點；若已展開，焦點移至第一子列。
- `←`（左鍵）：收合已展開節點；若已收合或為根，焦點移至父列。
- `Home` / `End`：移至可見列最頂 / 最底。

## 9. a11y　📋

- `role="treegrid"`；每列 `role="row"`；主欄格 `role="gridcell"` + `aria-level="<depth>"` + `aria-expanded`（父節點）/ `aria-expanded` 不設（葉節點）。
- Expander 按鈕 `aria-label="展開 [節點名稱]"` / `"收合 [節點名稱]"`。
- Checkbox 父節點 `aria-checked="mixed"` 對應 indeterminate。
- 繼承 DataGrid.md §9 對比 / focus ring / 動畫規則。

## 10. 跨平台 Adaptive　📋

- **桌面（XL/L）**：完整 TreeGrid + expander + 凍結主欄 + 橫捲；P0–P3 欄位依斷點顯隱。
- **平板（M）**：保留 tree structure；收斂至 P0–P1 欄位；expander 觸控目標 ≥ 44px。
- **手機（S）**：TreeGrid 不做，改以**折疊列表（accordion）**替代——父節點 = accordion header，子節點 = 展開 panel。

## 11. Content　📋

- 空值同 DataGrid：`—`（em dash）。
- empty state（無樹狀資料）：「尚無分類資料，請先建立父層項目」。
- loading-children（lazy load 中）：「載入子項目中…」`aria-live="polite"` 播報。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| dataSource | Object[] | [] | 巢狀樹狀資料（含 children 陣列）或扁平陣列（含 parentId） |
| childMapping | string | — | 子資料欄位名稱（巢狀模式，如 `'subtasks'`） |
| idMapping | string | 'taskID' | 主鍵欄位名（扁平 parentID 模式） |
| parentIdMapping | string | 'parentID' | 父鍵欄位名（扁平 parentID 模式） |
| treeColumnIndex | number | 0 | 顯示展開 / 收合 icon 的欄索引（0-based） |
| hasChildMapping | string | 'isParent' | 是否有子節點的判斷欄（lazy load 用） |
| enableLazyLoading | boolean | false | 展開時 demand load 子節點 |
| autoCheckHierarchy | boolean | false | 啟用 checkbox 父子聯動選取（父→全選子；部分子→父 indeterminate） |
| 繼承 DataGrid props | — | — | `columns` / `allowSorting` / `allowFiltering` / `allowPaging` / `pageSettings` / `editSettings` / `toolbar` 等 |

## 13. 關聯　🔗

- 上游契約：**`DataGrid.md`**（本檔差異繼承，重疊處引用）
- Tokens：`../athena-tokens.md` §Primary / §Surface / §On-* / §Space / §Typography
- 值權威：`prototyper/assets/app.css`（canonical CSS）
- 同層：`DataGrid.md`（平面列表改用）；`Pager.md`（頁尾分頁）
- Code：`@syncfusion/ej2-vue-treegrid`
