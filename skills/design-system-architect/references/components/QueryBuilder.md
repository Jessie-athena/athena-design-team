---
name: QueryBuilder（查詢條件建構器）
category: 資料展示與表格
tier: full           # 複合 UI：巢狀條件群組 + 多層 rule 行 + 動態 add/remove 生命週期
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: JhcpyIEEzSChcEXMRJUiIm/13438:26816
version: v0.3
last-synced: 2026-06-30
---

> 依 `../component-doc-schema.md`（Full 層）產出。視覺字面值（尺寸 / 色）權威＝`prototyper/assets/app.css`；本檔＝契約（what/why/token-reference/state/a11y）。

## 1. 概述　📋

讓使用者以 **no-code 方式建構複雜篩選條件**的複合元件，輸出結構化 query（JSON rule）供後端 / ORM 執行。適用場景：進階搜尋、報表條件設定、資料匯出篩選。

何時**不用**：簡單單欄篩選（搜尋列 + 下拉即可）；固定條件無需動態組合時改 ListSearch（見 `ListSearch.md`）。

## 2. Anatomy　🎨

```
.qb                             ← QueryBuilder 根容器
  └─ .qb__group                 ← 條件群組（可巢狀）
       ├─ .qb__group-header     ← AND/OR ButtonGroup + 操作按鈕
       │   ├─ .qb__connector    ← ButtonGroup：[AND] [OR]（非 <select>；選中態 = primary 填色）
       │   ├─ .qb__btn-add-rule    ← + IconButton（新增條件列）
       │   └─ .qb__btn-remove      ← × IconButton（移除群組；只有巢狀群組才有）
       └─ .qb__rules             ← 條件列表（左側有樹狀連接線）
            ├─ .qb__rule         ← 單一條件列（Horizontal container）
            │   ├─ .qb__field       ← 欄位 DropDownList（Label + input）
            │   ├─ .qb__operator    ← 運算子 DropDownList（= / ≠ / > / < / Start with…）
            │   └─ .qb__value       ← 值 DropDownList 或 TextBox（隨欄位型別切換）
            └─ .qb__group (nested) ← 遞迴巢狀群組（含自己的 group-header）
```

> 🎨 個別條件列**無獨立移除按鈕**（Figma 稿無此控件）；群組移除走 `.qb__btn-remove`（× icon）。

## 3. 視覺規格 Tokens　🎨🔗

```yaml
group:
  bg:         "rgba({color-sf-primary}, 0.05)"          # 🎨 primary 5% tint over white（非 surface-variant）
  border:     "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗 1px；#D7DAE0
  gap:        "見 app.css"                               # 🎨 列間距（$border-light 分隔）

group-header:
  height（Small）: "32px"                               # 🎨
  height（Large）: "40px"                               # 🎨

connector（AND/OR ButtonGroup）:
  font:           "{font-size-sf-text-md} / {font-weight-sf-medium}"  # 🔗 14px / 500
  active-bg:      "{color-sf-primary}"                  # 🔗 #2877EE
  active-fg:      "{color-sf-on-primary}"               # 🔗 white
  inactive-bg:    "white"                               # 🎨
  inactive-fg:    "{color-sf-on-surface}"               # 🔗 #0F172A
  inactive-shadow: "$shadow-sm"                         # 🎨 drop-shadow 0px 1px 1.5px + 1px 1px

rule:
  height（Small）: "32px"                               # 🎨
  height（Large）: "40px"                               # 🎨
  gap:            "{ds-space-padding-large}"            # 🔗 12px（欄位間距）
  field-w（Small）: "168px"                             # 🎨
  field-w（Large）: "179–180px"                         # 🎨
  label-font:     "{font-size-sf-text-md}"             # 🔗 14px（兩尺寸相同）
  label-fg:       "{color-sf-on-surface-variant}"      # 🔗 #3C4A5B
  body-font（Small）: "{font-size-sf-text-md}"          # 🔗 14px
  body-font（Large）: "{font-size-sf-text-lg}"          # 🔗 16px
  body-fg:        "{color-sf-on-surface}"              # 🔗 #0F172A
  placeholder-fg: "{ds-color-placeholder}"             # 🔗 #67717E（Enter Value）
  input-border-b: "{ds-borderwidth-small} {color-sf-outline}"  # 🔗 #7F8996 底線

remove-group-btn:
  icon:           "16px（Small）/ 20px（Large）"         # 🎨 close icon（× ）
  fg:             "{color-sf-on-surface-variant}"       # 🔗
```

## 4. Variants / Types　🎨🔗

```yaml
# ── 視覺軸（Figma 兩軸交叉，共 4 variant） ──
Size=Small:
  rule-height:  "32px"
  field-w:      "168px"
  body-font:    "{font-size-sf-text-md}"    # 14px
  btn-size:     "32px"
  icon-size:    "16px"

Size=Large:
  rule-height:  "40px"
  field-w:      "179px"
  body-font:    "{font-size-sf-text-lg}"    # 16px
  btn-size:     "40px"
  icon-size:    "20px"

Condition=AND:
  desc: "AND ButtonGroup 按鈕為 primary 填色；OR 為白底＋shadow"

Condition=OR:
  desc: "OR ButtonGroup 按鈕為 primary 填色；AND 為白底＋shadow"

# ── 行為軸（非 Figma variant，由 Props 控制） ──
standard:
  max-depth:         2      # 預設最多 2 層巢狀群組
  value-type-switch: "欄位切換時 value input 動態換型（text→date→number→select）"

display-only:
  interactive: false         # 唯讀展示；隱藏 add-rule / remove-group 按鈕
```

## 5. States　🎨🔗

| 狀態 | 呈現 |
|---|---|
| empty（無條件） | 根群組保留 connector + add-rule；提示「尚未設定任何條件」 |
| loading | Skeleton 條件列（保留 field/operator/value 欄位寬） |
| error（value 驗證失敗） | 值輸入框底線改 `{color-sf-danger}` + helper text 錯誤訊息，採 `@blur` 觸發 |
| dragging（rule 重排） | 被拖列 opacity 0.5；目標位置插入 `{color-sf-primary}` 1px 佔位線 |

欄位輸入控制元件的 hover / focus / disabled 狀態繼承 TextBox / DropDownList 各自規格，見對應設計文件。

## 6. Behavior　📋

| 互動 | 行為 |
|---|---|
| AND/OR 切換 | 切換整個群組的連結邏輯；切換時即時更新預覽（若有） |
| 新增條件 | 在群組底部插入空 rule（欄位預選第一個）；自動 focus field select |
| 新增巢狀群組 | 在群組底部插入子 `.qb__group`；根群組之下，子群組另色底區分 |
| 移除條件 | 立即移除，無確認；若群組僅剩一條且非根，提示是否移除整個群組 |
| 移除群組 | 移除整個群組（含所有 rules 與子群組）；根群組無移除按鈕 |
| 欄位切換 | operator 重置至該型預設值；value 清空並切換 input 型態 |
| 條件重排 | 拖曳排序（同群組內）；跨群組移動需明確拖至目標群組 |
| 送出 / 重置 | 送出前驗證所有 value 非空；「重置」清空至初始狀態（1 個空 rule） |

## 7. RWD / 斷點　📋

| 斷點 | 呈現 |
|---|---|
| ≥ 1280px | field/operator/value 三欄橫排；rule 單行 |
| 1024–1279px | 同上；欄寬縮減 |
| ≤ 1024px | field/operator 單行；value 折到第二行；remove 置 row 右側 |
| < 768px | **不做**（QueryBuilder 為桌面功能，行動端以 ListSearch 簡易篩選替代） |

## 8. Keyboard　📋

- `Tab` / `Shift+Tab`：在 field → operator → value → remove 之間移動；移至 remove 按 `Space` / `Enter` 刪除。
- AND/OR 按鈕：`Space` / `Enter` 切換。
- 新增條件按鈕：`Enter` 後 focus 新增 rule 的 field select。
- `Esc`：關閉下拉選單；若下拉已收回，焦點還原至觸發元素。

## 9. a11y　📋

- 根容器 `role="group"` + `aria-label="查詢條件建構器"`；每個子 group 加 `aria-label="條件群組"`.
- AND/OR 切換視為 `role="radiogroup"` + `role="radio"`（或 toggle button group）。
- value input 需 `<label>`（sr-only），內容為 `"[欄位名] 的值"`。
- remove 按鈕 `aria-label="移除 [欄位名] 條件"`；禁無標籤的圖示按鈕。
- 動態新增 / 移除條件後 `aria-live="polite"` 播報（「已新增條件」/「已移除條件」）。

## 10. 跨平台 Adaptive　📋

- **桌面**：完整三欄 rule 橫排 + 巢狀群組；最多 2 層深度（超過禁止繼續巢狀）。
- **平板（M）**：field/operator 縮短，value 保留；移除巢狀新增群組按鈕（改只允許 AND/OR）。
- **手機（S）**：**不做**，以 ListSearch 簡易篩選替代。

## 11. Content　📋

- AND/OR 標籤：「且（AND）」/「或（OR）」（繁中環境中英並列）。
- 新增按鈕：「新增條件」、「新增群組」（動詞 + 對象）。
- empty state：「尚未設定任何條件，點『新增條件』開始」。
- 驗證錯誤：「請填入 [欄位名] 的值」。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| dataSource | Object[] | — | 用於推斷欄位型別的資料來源（可選） |
| columns | ColumnDirective[] | [] | 欄位定義，透過 `<e-columns>/<e-column field label type operators? values?>` 指令設定 |
| rule | RuleGroup | `{condition:'and',rules:[]}` | 初始規則（注意：Syncfusion 使用 `rule`（單數），非 `rules`） |
| allowDragAndDrop | boolean | false | 允許拖曳排序條件列 |
| maxGroupDepth | number | 2 | 最大巢狀群組層數（設計規範） |
| showGroupAdd | boolean | true | 是否顯示「新增群組」按鈕（設計規範） |
| readonly | boolean | false | 唯讀展示模式 |

事件：`@ruleChange(RuleGroup)` — 任意條件改變時觸發

方法（實例方法）：`getRules()` 取得 JSON rules；`getSqlFromRules()` 轉 SQL WHERE；`setRulesFromSql()` 從 SQL 載入

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §Surface / §On-* / §Outline / §Space / §Radius / §Typography
- 值權威：`prototyper/assets/app.css`（canonical CSS）
- 同層：`DataGrid.md`（QueryBuilder 輸出條件套用至 Grid 篩選）；`ListSearch.md`（簡易場景替代）；`TextBox.md`；`DropDownList.md`
- Code：`@syncfusion/ej2-vue-querybuilder`
