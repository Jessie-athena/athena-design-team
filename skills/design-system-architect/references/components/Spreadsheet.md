---
name: Spreadsheet（試算表）
category: 資料展示與表格
tier: full           # 類 Excel 全功能：儲存格 / 公式 / 多 sheet / 格式設定 + 複雜互動生命週期
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: —        # 🎨 Figma 補入時填 <FILE_KEY>/<NODE_ID>（FAI2 componentKey: 9d61b6b28f3d0dba10e7c16534c3a8fc35207bb2）
version: v0.1
last-synced: —
---

> 依 `../component-doc-schema.md`（Full 層）產出。視覺字面值（ribbon 高度 / 儲存格尺寸 / 選取色）權威＝`prototyper/assets/app.css`；本檔＝契約（what/why/token-reference/state/a11y）。

## 1. 概述　📋

**嵌入式試算表元件**：在 Web/App 內提供類 Excel 的儲存格編輯、公式計算、格式設定與多 Sheet 管理。適用場景：財務模型輸入、進階報表自訂匯出、預算明細逐格編輯。

何時**不用**：資料為 ERP 結構化欄位（有 model 對應）→ DataGrid 行內編輯（避免「洗資料」語境的試算表思維流入 ERP 工作流）；需要樞紐分析 → PivotView；需要甘特排程 → Gantt。

> ⚠️ **使用警告**：Spreadsheet 賦予使用者高度自由度，可能導致未經驗證的資料進入系統。ERP 場景建議**限定可編輯儲存格範圍**（`cellLock`）並啟用 `allowEditing` 白名單，避免結構性欄位被覆寫。

## 2. Anatomy　🎨

```
.spreadsheet                      ← 根容器（flex column，height 固定或撐滿父容器）
  ├─ .ss__ribbon                  ← Ribbon 工具列（格式 / 數字格式 / 對齊 / 公式 / 插入刪除…）
  ├─ .ss__formula-bar             ← 儲存格地址 + 公式輸入列
  ├─ .ss__body                    ← 工作區（overflow:auto）
  │   ├─ .ss__col-header         ← A B C D… 欄標題列（凍結 top）
  │   ├─ .ss__row-header         ← 1 2 3 4… 列標題欄（凍結 left）
  │   └─ .ss__cell-grid          ← 儲存格矩陣
  │        └─ .ss__cell          ← 單一儲存格（可輸入 / 只讀 / 合併）
  └─ .ss__sheet-tabs              ← 工作表頁籤列（Sheet1 / Sheet2 / + 新增）
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
ribbon:
  height:        "見 app.css .ss__ribbon"               # 🎨 量測值（含多列按鈕組）
  bg:            "{color-sf-surface-variant}"            # 🔗 淡灰背景
  separator:     "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗

formula-bar:
  height:        "見 app.css .ss__formula-bar"          # 🎨
  address-width: "80px"                                 # 🎨 A1 地址寬
  bg:            "{color-sf-surface}"                   # 🔗
  font:          "{font-size-sf-text-md}"               # 🔗 14px（等寬字型搭配公式）
  font-family:   "font-mono"                            # 等寬（公式可讀性）

cell:
  default-width:  "80px"                               # 🎨 量測值
  default-height: "見 app.css .ss__cell"               # 🎨
  font:           "{font-size-sf-text-md}"              # 🔗 14px
  fg:             "{color-sf-on-surface}"               # 🔗
  bg:             "{color-sf-surface}"                  # 🔗 白底
  border:         "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗 1px 格線
  pad-h:          "{ds-space-padding-medium}"           # 🔗 8px

  selection-bg:   "見 app.css .ss__cell--selected"     # 🎨 primary 疊白，app.css 權威
  selection-border: "{ds-borderwidth-medium} {color-sf-primary}"  # 🔗 2px 藍框
  copy-border:    "1px dashed {color-sf-primary}"      # 🔗 複製態虛線框
  header-bg:      "見 app.css .ss__col-header / .ss__row-header"  # 🎨 primary 疊白實色

sheet-tabs:
  height:         "見 app.css .ss__sheet-tabs"          # 🎨
  active-fg:      "{color-sf-primary}"                  # 🔗
  active-border:  "bottom: 2px {color-sf-primary}"     # 🔗
  font:           "{font-size-sf-text-sm}"              # 🔗 12px
```

## 4. Variants / Types　🎨🔗

```yaml
editable:          "完整 Ribbon + 公式列；所有儲存格可編輯（預設）"
readonly:          "隱藏 Ribbon 編輯按鈕；儲存格 cursor=default；保留 scroll / copy / 公式展示"
limited-edit:      "cellLock 指定可編輯範圍；鎖定格 cursor=not-allowed + bg={color-sf-on-surface-opacity4}（🔗）"
embedded-lite:     "無 Ribbon；公式列可選；適合嵌入 Form View 作為「進階明細表」"
```

## 5. States　🎨🔗

| 狀態 | 呈現 |
|---|---|
| cell-default | 白底 + 1px 格線 |
| cell-hover | row / col header hover 底色略深（見 app.css）|
| cell-selected（單格） | `{color-sf-primary}` 2px 框 + primary 疊白底 |
| cell-selected（多格） | 範圍框 + `{color-sf-primary-opacity-12}` 範圍底色 |
| cell-editing | 文字游標 + formula-bar 即時同步；`Esc` 取消 |
| cell-locked | `not-allowed` cursor + `{color-sf-on-surface-opacity4}` 底色 |
| cell-formula-error | `#DIV/0!` / `#REF!` 等錯誤代碼 + `{color-sf-danger}` 文字；tooltip 說明 |
| cell-conditional-format | 依條件格式規則疊加底色 / 文字色（ERP 常見：逾期 = red tint）|
| selection-copy | 1px dashed primary 框（`Ctrl+C` 後）|
| loading | Skeleton 儲存格矩陣 |
| empty（空工作表） | 呈現空格線（Spreadsheet 不顯示 inbox empty-state，空格即初始態）|

## 6. Behavior　📋

| 互動 | 行為 |
|---|---|
| 儲存格選取 | 點選單格；`Shift+Click` 範圍；`Ctrl+Click` 不連續；Arrow 移動 |
| 儲存格編輯 | 雙擊 / `F2` / 直接輸入 → 進入 editing；`Enter` 確認下移；`Tab` 確認右移；`Esc` 取消 |
| 公式輸入 | `=` 開頭 → formula-bar 高亮；自動補全函式名稱；`Enter` 計算 |
| 欄 / 列 resize | 拖曳 header 邊界；雙擊 auto-fit |
| 凍結欄列 | Ribbon「凍結」→ 固定目前選取格左上角 |
| 工作表管理 | 雙擊 tab 重命名；右鍵 → 插入 / 刪除 / 移動 / 複製 |
| 複製貼上 | `Ctrl+C/V`；支援格式、公式、值三模式貼上 |
| 匯入 / 匯出 | Ribbon 支援匯入 xlsx / csv；匯出 xlsx / pdf |
| 撤銷 / 重做 | `Ctrl+Z` / `Ctrl+Y`；最多 100 步 |
| 尋找取代 | `Ctrl+F` 開 dialog；`Ctrl+H` 尋找取代 |

## 7. RWD / 斷點　📋

| 斷點 | 呈現 |
|---|---|
| ≥ 1440px | 完整 Ribbon（展開群組）；儲存格矩陣佔滿 |
| 1024–1439px | Ribbon 部分群組收合為 icon-only |
| ≤ 1024px | Ribbon 改為 overflow-scroll（橫向）；儲存格矩陣橫捲 |
| < 768px | **Spreadsheet 不建議行動端使用**（儲存格觸控精準度不足）；如需改 DataGrid 行內編輯 |

## 8. Keyboard　📋

- `Tab` / `Shift+Tab`：編輯態儲存格右移 / 左移。
- `Enter` / `Shift+Enter`：確認並下移 / 上移。
- `Arrow`：選取態儲存格移動；`Ctrl+Arrow`：跳至邊界。
- `Ctrl+Shift+End`：擴展選取至最後資料格。
- `Ctrl+;`：插入今日日期。
- 函式精靈：`Shift+F3`。

## 9. a11y　📋

- `role="grid"` 搭配 `aria-rowcount` / `aria-colcount`（動態）；每格 `role="gridcell"` + `aria-selected`。
- 編輯中格子：`aria-busy="true"` + `aria-label="編輯 [地址]：[當前值]"`。
- 公式錯誤：`aria-label="[地址] 公式錯誤：[錯誤碼]"`。
- 鍵盤操作完整（Arrow / Tab / Enter），screen reader 可完整操作。
- 對比：Locked 格的 muted 底色需文字對比 ≥ 3:1（大字）。

## 10. 跨平台 Adaptive　📋

- **桌面**：完整功能（Ribbon + 公式 + 多 Sheet + 凍結 + 拖曳填充）。
- **平板（M）**：Ribbon 收至 icon-only；觸控選取（長按開 context menu）；公式列縮小；禁拖曳填充。
- **手機（S）**：**不做** Spreadsheet；改以 DataGrid 行內編輯或 Form View 提供相似功能。

## 11. Content　📋

- 公式錯誤代碼一律顯示英文（`#DIV/0!`、`#REF!`、`#NAME?`）搭配 tooltip 中文說明。
- 儲存格空值顯示為空（不填 `—`，與 DataGrid 不同）。
- 工作表 tab 預設命名：「工作表 1」「工作表 2」。
- locked 格 tooltip：「此欄位不允許編輯」。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| sheets | SheetModel[] | [] | `{ name, rows: RowModel[], columns: ColumnModel[] }` |
| allowEditing | boolean | true | 全域可否編輯 |
| showRibbon | boolean | true | 是否顯示 Ribbon |
| showFormulaBar | boolean | true | 是否顯示公式列 |
| scrollSettings | ScrollSettings | — | `{ enableVirtualization }` 虛擬捲動（大資料） |
| cellStyle | CellStyleModel | — | 預設格式（字型 / 對齊 / 框線） |
| beforeCellUpdate | Function | — | 儲存前攔截驗證 |

事件：`@cellEdit` / `@cellSave` / `@cellChange` / `@beforeSave`

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §Surface / §On-* / §Outline / §Space / §Typography / §Danger
- 值權威：`prototyper/assets/app.css`（canonical CSS）
- 同層：`DataGrid.md`（結構化資料優先）；`PivotView.md`（樞紐分析）
- Code：`@syncfusion/ej2-vue-spreadsheet`
