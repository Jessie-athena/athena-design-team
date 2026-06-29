# Athena 元件清單（完整目錄）

> 本檔是 Athena 可用元件的**完整共用目錄**：有哪些元件、各用在哪、目前採用狀態。
> 來源：Syncfusion Vue（Material 3 主題）元件庫，對應 `syncfusion-playground/app/pages/playground/*.vue`（94 個，轉設計/實作前先查對應 `.vue` 確認最新 API）；客製元件前綴 `Ds`，路徑 `components/ds/`。
> 用色 / 字級 / 間距語意 → 見同目錄 `athena-design.md`；token 值 → `athena-tokens.md`。

## 採用狀態圖例

| 標記 | 意義 |
|------|------|
| ✅ 已採用 | ERP `CLAUDE.md` 已定版採用、有明確 import 對照 |
| ⬜ 待分類 | 元件存在於庫中、尚未由 DS owner 明訂可用 / 不可用 |
| 🚫 不可用 | DS owner 明訂不採用（**待你圈選** — 目前無權威來源，未擅自標記） |

> ⚠️ **「可用 / 不可用」是治理決策**。本檔僅能據 ERP `CLAUDE.md` 標出「✅ 已採用」者；其餘一律「⬜ 待分類」，不臆造為不可用。請於審閱時把要禁用的改標 🚫，並補禁用理由 / 替代元件。

---

## 設計文件索引（✅ 已採用元件）

> 每個 ✅ 已採用元件的**逐元件設計文件**落點。格式見 `component-doc-schema.md`；產出由 `SKILL.md §Step 2` 負責。
> 為避免每張表格塞入大量空欄（多數元件仍 ⬜ 待分類無設計文件），設計文件連結集中於此單一索引，不分散到下方各分類表。

| 元件 | 設計文件 | 狀態 |
|------|---------|------|
| Button | [`components/Button.md`](components/Button.md) | ✅ 已產出（Lite；v0.3 所有 ⚠️ 項已解，含 disabled bg token、Comfortable 密度 padding） |
| st-chip（狀態徽章 / `DsStatusBadge`） | [`components/st-chip.md`](components/st-chip.md) | ✅ 已產出（Full；色 / class 落地引用 prototyper DataGrid.md / Stepper.md） |
| Grid（DataGrid） | [`components/DataGrid.md`](components/DataGrid.md) | ✅ 已產出（Full；class / 尺寸 / 互動色 / 凍結欄 / 行內編輯落地引用 prototyper DataGrid.md） |
| Stepper | [`components/Stepper.md`](components/Stepper.md) | ✅ 已產出（Full；步序狀態 / 語意色 / 判定邏輯 / voided-banner 落地引用 prototyper Stepper.md） |
| SummaryCard | [`components/SummaryCard.md`](components/SummaryCard.md) | ✅ 已產出（Full；Layout A/B、sticky 無 shadow、4 步動態 stepper 落地引用 prototyper SummaryCard.md） |
| TextBox | [`components/TextBox.md`](components/TextBox.md) | ✅ 已產出（Lite；Filled Small Default；Layer 1–4 Figma 校準，含 token gap 標注） |
| NumericTextBox | [`components/NumericTextBox.md`](components/NumericTextBox.md) | ✅ 已產出（Lite；Filled Small Default；label=12px、無 warning 態、spinner 控制區） |
| DropDownList | [`components/DropDownList.md`](components/DropDownList.md) | ✅ 已產出（Lite；固定 arrow-down btn；clearIcon 為 Syncfusion runtime prop） |
| DatePicker | [`components/DatePicker.md`](components/DatePicker.md) | ✅ 已產出（Lite；DsDatePicker 客製包裝；固定 calendar-icon-btn） |
| Tab | [`components/Tab.md`](components/Tab.md) | ✅ 已產出（Full；Size×Alignment×Count×scroll 四軸；indicator 3px 無 token） |
| Dialog | [`components/Dialog.md`](components/Dialog.md) | ✅ 已產出（Full；Size Small/Large；confirm/deeplink 為 ERP 應用層規範） |
| FAB | [`components/FAB.md`](components/FAB.md) | ✅ 已產出（Lite；4 尺寸 × 6 色 × Icon/Extended；圓角 16px 非全圓；shadow-lg） |
| Toast | [`components/Toast.md`](components/Toast.md) | ✅ 已產出（Full；5 kind：default/success/info/warning/error；3 秒自動消失；ERP 目前用 success/warning/error 三種） |
| Skeleton | [`components/Skeleton.md`](components/Skeleton.md) | ✅ 已產出（Lite；6 個複合佈局 preset；shimmer 靜態疊層，動效由 Syncfusion 實作） |

> 收編原則：`prototyper` 既有的深度元件規格（DataGrid / Stepper / SummaryCard…）為「prototype 單檔 HTML/CSS 落地層」；設計文件是「what/why/token/state 的權威」。補設計文件時**引用** profile，不重寫 token 決策（見 `component-doc-schema.md §8`）。

---

## 1. 佈局與導覽（Layout & Navigation）

> Import 多來自 `@syncfusion/ej2-vue-navigations` / `-layouts`。

| 元件 | 用途（用在哪） | 採用狀態 |
|------|--------------|---------|
| Tab | 同一畫面切換多組內容（單據明細分頁） | ✅ 已採用 |
| Accordion | 可摺疊區塊、FAQ、設定分組 | ⬜ 待分類 |
| AppBar | 頂部應用列 / header chrome | ⬜ 待分類 |
| Toolbar | 工具列、批次操作列 | ⬜ 待分類 |
| Menu | 主選單、多層導覽 | ⬜ 待分類 |
| ContextMenu | 右鍵 / 長按情境選單 | ⬜ 待分類 |
| Breadcrumb | 階層路徑導覽 | ⬜ 待分類 |
| Sidebar | 側邊抽屜 / 導覽面板 | ⬜ 待分類 |
| Splitter | 可調整比例的分割版面 | ⬜ 待分類 |
| Card | 卡片容器 | ⬜ 待分類（另有客製 `DsCard`） |
| Dashboard Layout | 可拖拉的儀表板網格 | ⬜ 待分類 |
| Ribbon | Office 風格功能區 | ⬜ 待分類 |
| TreeView | 樹狀導覽 / 階層清單 | ⬜ 待分類 |

## 2. 按鈕與動作（Buttons & Actions）

> Import：`@syncfusion/ej2-vue-buttons`。

| 元件 | 用途（用在哪） | 採用狀態 |
|------|--------------|---------|
| Button | 各類操作 CTA | ✅ 已採用 |
| FAB / SpeedDial | 浮動主操作 | ✅ 已採用 |
| ButtonGroup | 一組相關切換 / 分段按鈕 | ⬜ 待分類 |
| DropDownButton | 帶下拉選單的按鈕 | ⬜ 待分類 |
| SplitButton | 主動作 + 下拉次動作 | ⬜ 待分類 |
| ProgressButton | 帶進度狀態的按鈕（提交中） | ⬜ 待分類 |

## 3. 輸入 — 文字（Text Inputs）

> Import 多來自 `@syncfusion/ej2-vue-inputs`。

| 元件 | 用途（用在哪） | 採用狀態 |
|------|--------------|---------|
| TextBox | 文字輸入 | ✅ 已採用 |
| NumericTextBox | 數值 / 金額輸入 | ✅ 已採用 |
| TextArea | 多行文字 | ⬜ 待分類 |
| MaskedTextBox | 格式遮罩輸入（電話 / 統編） | ⬜ 待分類 |
| OTP Input | 一次性驗證碼輸入 | ⬜ 待分類 |
| In-place Editor | 行內就地編輯 | ⬜ 待分類 |
| Signature | 手寫簽名 | ⬜ 待分類 |
| Mention | @ 提及輸入 | ⬜ 待分類 |
| Smart Paste | AI 智慧貼上解析 | ⬜ 待分類 |
| Smart TextArea | AI 輔助文字輸入 | ⬜ 待分類 |
| Speech to Text | 語音轉文字輸入 | ⬜ 待分類 |

## 4. 輸入 — 選擇（Selection Inputs）

> Import 多來自 `@syncfusion/ej2-vue-dropdowns` / `-buttons` / `-inputs`。

| 元件 | 用途（用在哪） | 採用狀態 |
|------|--------------|---------|
| DropDownList | 單選下拉 | ✅ 已採用 |
| AutoComplete | 輸入即時建議 | ⬜ 待分類 |
| ComboBox | 可輸入可選的下拉 | ⬜ 待分類 |
| MultiColumn ComboBox | 多欄下拉（顯示多欄資訊） | ⬜ 待分類 |
| MultiSelect | 多選下拉 / 標籤選擇 | ⬜ 待分類 |
| DropDownTree | 樹狀下拉選擇 | ⬜ 待分類 |
| ListBox | 清單選擇 / 雙清單搬移 | ⬜ 待分類 |
| Checkbox | 多選框 | ⬜ 待分類 |
| RadioButton | 單選鈕 | ⬜ 待分類 |
| Switch | 開關切換 | ⬜ 待分類 |
| Slider | 數值滑桿 | ⬜ 待分類 |
| Rating | 星等評分 | ⬜ 待分類 |
| ColorPicker | 色彩選擇 | ⬜ 待分類 |

## 5. 日期與時間（Date & Time）

> Import：`@syncfusion/ej2-vue-calendars`。

| 元件 | 用途（用在哪） | 採用狀態 |
|------|--------------|---------|
| DatePicker | 日期選擇 | ✅ 已採用（另有客製 `DsDatePicker` 含多語系格式） |
| Calendar | 月曆面板 | ⬜ 待分類 |
| DateRangePicker | 日期區間選擇 | ⬜ 待分類 |
| DateTimePicker | 日期＋時間選擇 | ⬜ 待分類 |
| TimePicker | 時間選擇 | ⬜ 待分類 |

## 6. 資料展示與表格（Data Display）

> Import 多來自 `@syncfusion/ej2-vue-grids` / `-navigations` / `-layouts`。

| 元件 | 用途（用在哪） | 採用狀態 |
|------|--------------|---------|
| Grid | 資料列表、報表、可排序/篩選表格 | ✅ 已採用 |
| TreeGrid | 階層式表格 | ⬜ 待分類 |
| Pivot View | 樞紐分析表 | ⬜ 待分類 |
| Spreadsheet | 試算表 | ⬜ 待分類 |
| ListView | 清單檢視 | ⬜ 待分類 |
| Kanban | 看板（拖拉卡片） | ⬜ 待分類 |
| Gantt | 甘特圖 / 專案排程 | ⬜ 待分類 |
| Schedule | 行事曆 / 排程檢視 | ⬜ 待分類 |
| QueryBuilder | 視覺化條件查詢建構 | ⬜ 待分類 |
| Pager | 分頁器 | ⬜ 待分類 |

## 7. 圖表與視覺化（Charts & Visualization）

> Import 多來自 `@syncfusion/ej2-vue-charts` / `-gauges` / `-maps` / `-diagrams` / `-barcode-generator`。

| 元件 | 用途（用在哪） | 採用狀態 |
|------|--------------|---------|
| Chart | 2D 直角座標圖（折線 / 長條 / 區域…） | ⬜ 待分類 |
| Chart3D | 3D 圖表 | ⬜ 待分類 |
| Accumulation Chart | 圓餅 / 環圈圖 | ⬜ 待分類 |
| Bullet Chart | 子彈圖（KPI 對標） | ⬜ 待分類 |
| Stock Chart | 股價 / 財務 K 線圖 | ⬜ 待分類 |
| Smith Chart | 史密斯圓圖（工程用） | ⬜ 待分類 |
| Sparkline | 行內微型趨勢圖 | ⬜ 待分類 |
| Range Navigator | 圖表時間區間導覽 | ⬜ 待分類 |
| Circular Gauge | 圓形儀表 | ⬜ 待分類 |
| Linear Gauge | 線性儀表 | ⬜ 待分類 |
| Heatmap | 熱力圖 | ⬜ 待分類 |
| TreeMap | 矩形樹狀圖 | ⬜ 待分類 |
| Maps | 地圖 | ⬜ 待分類 |
| Diagram | 流程圖 / 節點圖 | ⬜ 待分類 |
| Barcode | 條碼 / QR 產生 | ⬜ 待分類 |

## 8. 回饋與狀態（Feedback & Status）

> Import 多來自 `@syncfusion/ej2-vue-notifications` / `-popups`。

| 元件 | 用途（用在哪） | 採用狀態 |
|------|--------------|---------|
| Toast | 操作結果提示（success / warning / error） | ✅ 已採用 |
| Skeleton | 載入佔位 | ✅ 已採用 |
| Dialog | 確認操作、deeplink 跳轉、表單 modal | ✅ 已採用 |
| Predefined Dialogs | Alert / Confirm / Prompt 預設對話框 | ⬜ 待分類 |
| Message | 行內訊息條 | ⬜ 待分類 |
| Tooltip | 提示氣泡 | ⬜ 待分類 |
| Spinner | 載入轉圈 | ⬜ 待分類 |
| ProgressBar | 進度條 | ⬜ 待分類 |
| Badge | 數字 / 狀態角標 | ⬜ 待分類（狀態徽章另有客製 `DsStatusBadge`） |
| Chips | 標籤 / 篩選 chip | ⬜ 待分類 |
| Avatar | 使用者頭像 | ⬜ 待分類 |

## 9. 編輯器與富內容（Editors & Rich Content）

> Import 多來自 `@syncfusion/ej2-vue-richtexteditor` / `-documenteditor` / `-image-editor` / `-pdfviewer` / `-filemanager` / `-inputs`。

| 元件 | 用途（用在哪） | 採用狀態 |
|------|--------------|---------|
| RichTextEditor | 富文本編輯 | ⬜ 待分類 |
| Document Editor | Word 文件編輯 | ⬜ 待分類 |
| Image Editor | 圖片編輯 | ⬜ 待分類 |
| PDF Viewer | PDF 檢視 | ⬜ 待分類 |
| File Manager | 檔案總管 | ⬜ 待分類 |
| File Upload | 檔案上傳 | ⬜ 待分類 |

## 10. 流程與其他（Flow & Misc）

| 元件 | 用途（用在哪） | 採用狀態 |
|------|--------------|---------|
| Stepper | 單據狀態流程、多步驟精靈 | ✅ 已採用 |
| Carousel | 輪播 | ⬜ 待分類 |
| AI AssistView | AI 對話輔助面板 | ⬜ 待分類 |
| Chat UI | 聊天介面 | ⬜ 待分類 |

---

## 客製 `Ds` 元件（前綴 `Ds`，路徑 `components/ds/`）

| 元件 | 用途（用在哪） | 採用狀態 |
|------|--------------|---------|
| `DsDatePicker` | 含多語系格式的日期選擇 | ✅ 已採用 |
| `DsStatusBadge` | 單據狀態徽章 | ✅ 已採用 |
| `DsCard` | 卡片容器 | ✅ 已採用 |
| `DsSideNavMenu` | 側邊導覽選單 | ✅ 已採用 |

> 優先順序：Syncfusion 既有元件 → 客製 `Ds` 元件 → 行內組合。需新增客製元件前，先 audit 上表是否已有可用者（見 SKILL.md §Governance）。
