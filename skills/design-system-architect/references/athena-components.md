# Athena 元件清單（完整目錄）

> 本檔是 Athena 可用元件的**完整共用目錄**：有哪些元件、設計文件建立情況。
> 來源：Syncfusion Vue（Material 3 主題）元件庫，對應 `syncfusion-playground/app/pages/playground/*.vue`（94 個，轉設計/實作前先查對應 `.vue` 確認最新 API）；客製元件前綴 `Ds`，路徑 `components/ds/`。
> 用色 / 字級 / 間距語意 → 見同目錄 `athena-design.md`；token 值 → `athena-tokens.md`。

## 文件狀態圖例

| 標記 | 意義 |
|------|------|
| ✅ 已產出 | 設計文件（`.md`）已建立，見下方設計文件索引 |
| ⬜ 待建立 | 尚未建立設計文件 |
| 🚫 不可用 | DS owner 明訂不採用（目前無，未擅自標記） |

---

## 設計文件索引

> 已建立設計文件的元件快速索引，格式見 `component-doc-schema.md`；產出由 `SKILL.md §Step 2` 負責。
> 設計文件連結集中於此索引，不分散到下方各分類表。

| 元件 | 設計文件 | 狀態 |
|------|---------|------|
| Button | [`components/Button.md`](components/Button.md) | ✅ 已產出（Lite；v0.3 所有 ⚠️ 項已解，含 disabled bg token、Comfortable 密度 padding） |
| Grid（DataGrid） | [`components/DataGrid.md`](components/DataGrid.md) | ✅ 已產出（Full；v0.3 FAI2 對齊；row-h 36px(S)/48px(L)；凍結欄 / 行內編輯 / 斑馬 / 多選） |
| TreeGrid | [`components/TreeGrid.md`](components/TreeGrid.md) | ✅ 已產出（Full；v0.3 FAI2 對齊；indent 32px（非 20px）、expander 16px(S)/18px(L)；繼承 DataGrid；checkable / lazy-load） |
| PivotView | [`components/PivotView.md`](components/PivotView.md) | ✅ 已產出（Full；v0.3 FAI2 對齊；header primary-5%、border-l+border-t outline-variant、header-fg on-surface-variant；欄位拖放 / drill-down；⚠️ DS AA token 待補） |
| Spreadsheet | [`components/Spreadsheet.md`](components/Spreadsheet.md) | ✅ 已產出（Full；v0.3 FAI2 對齊；Ribbon 160px primary-8%、cell border 0.5px、sheet-tabs 52px/14px/500；cellLock；ERP 建議限定可編輯範圍） |
| ListView | [`components/ListView.md`](components/ListView.md) | ✅ 已產出（Lite；v0.3 FAI2 對齊；item min-h 40px（非 48px）、py 4px、primary-text weight=400（非 500）；default / avatar / checkbox / dense） |
| Kanban | [`components/Kanban.md`](components/Kanban.md) | ✅ 已產出（Full；v0.3 FAI2 對齊；card padding 16px、header primary-5% rounded-tl/tr-[4px]；S=1224px L=1624px；WIP limit / swimlane） |
| Gantt | [`components/Gantt.md`](components/Gantt.md) | ✅ 已產出（Full；v0.3 FAI2 對齊；row-h 36px(Small)；雙面板 TreeGrid+Timeline；critical-path / resource-view / 依賴連線） |
| Schedule | [`components/Schedule.md`](components/Schedule.md) | ✅ 已產出（Full；v0.3 FAI2 對齊；toolbar 48px+primary-8%、active-view-bg on-surface-8%（非 primary）、slot 62px/0.5px；5 視圖） |
| QueryBuilder | [`components/QueryBuilder.md`](components/QueryBuilder.md) | ✅ 已產出（Full；v0.3 FAI2 對齊；巢狀群組 max-depth 2；field type 動態切換 value input；keyboard 替代拖曳） |
| Pager | [`components/Pager.md`](components/Pager.md) | ✅ 已產出（Lite；v0.3 FAI2 對齊；h 48px(S)/56px(L)、nav-btn 32×32(S)/40×40(L)；20/50/100；禁單獨存在） |
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
| ButtonGroup | [`components/ButtonGroup.md`](components/ButtonGroup.md) | ✅ 已產出（Lite；4 尺寸 × 4 色；純 CSS 組合；首尾項各自圓角，中間項 0） |
| DropDownButton | [`components/DropDownButton.md`](components/DropDownButton.md) | ✅ 已產出（Full；5 軸 variant；overlay menu shadow-md；caret icon 16px） |
| SplitButton | [`components/SplitButton.md`](components/SplitButton.md) | ✅ 已產出（Full；左段 4px/0/0/4px × 右段 0/4px/4px/0；divider 用 border-right） |
| IconButton | [`components/IconButton.md`](components/IconButton.md) | ✅ 已產出（Lite；5 軸 variant；Rounded 4px / Full Rounded 1000px；無 token gap） |
| TextArea | [`components/TextArea.md`](components/TextArea.md) | ✅ 已產出（Lite；resizer + character-counter；padding_left 12px；line-height 1.3 vs TextBox 1.5） |
| MaskedTextBox | [`components/MaskedTextBox.md`](components/MaskedTextBox.md) | ✅ 已產出（Lite；template 軸 Date/Card/Phone；label=12px；含 Info + Warning state） |
| OTP Input | [`components/OTPInput.md`](components/OTPInput.md) | ✅ 已產出（Full；3 shape × 4 size × 4 count = 48 variant；Filled/Outlined/Line 三種單格樣式） |
| PasswordTextBox | [`components/PasswordTextBox.md`](components/PasswordTextBox.md) | ✅ 已產出（Lite；獨立元件；eye-toggle-btn 32×32；含 Info state；token gap 同 TextBox） |
| AutoComplete | [`components/AutoComplete.md`](components/AutoComplete.md) | ✅ 已產出（Lite；48 variant；overlay embedded-input + Add 按鈕；arrow-down 可隱） |
| ComboBox | [`components/ComboBox.md`](components/ComboBox.md) | ✅ 已產出（Lite；132 variant；overlay embedded-search TextBox；含 clearIcon） |
| MultiColumnComboBox | [`components/MultiColumnComboBox.md`](components/MultiColumnComboBox.md) | ✅ 已產出（Full；overlay multi-column grid with header；overlay panel 節點待補） |
| MultiSelect | [`components/MultiSelect.md`](components/MultiSelect.md) | ✅ 已產出（Full；chip flex-wrap 撐高 trigger；39 var 零漂移） |
| DropDownTree | [`components/DropDownTree.md`](components/DropDownTree.md) | ✅ 已產出（Full；tree indent 38px token gap；inline search；50 var 零漂移） |
| ListBox | [`components/ListBox.md`](components/ListBox.md) | ✅ 已產出（Full；Dual ListBox = toolbarSettings 組合；3 item padding token gap） |
| Checkbox | [`components/Checkbox.md`](components/Checkbox.md) | ✅ 已產出（Lite；indeterminate = Type=Intermediate；disabled opacity-38；1 label_gap token gap） |
| RadioButton | [`components/RadioButton.md`](components/RadioButton.md) | ✅ 已產出（Lite；1.5px border token gap；inner-dot selected style） |
| Switch | [`components/Switch.md`](components/Switch.md) | ✅ 已產出（Lite；4 軸 ~160 variant；track/thumb token；2 token gap） |
| Rating | [`components/Rating.md`](components/Rating.md) | ✅ 已產出（Lite；3 軸 88 variant；Star / Heart shape；2 token gap） |
| ColorPicker | [`components/ColorPicker.md`](components/ColorPicker.md) | ✅ 已產出（Full；Size × Template；Color Range / Swatches；3 token gap） |
| DsColorPicker | [`components/DsColorPicker.md`](components/DsColorPicker.md) | ✅ 已產出（Lite；客製固定色盤；light/dark 2 variant；18 var 零漂移） |
| Calendar | [`components/Calendar.md`](components/Calendar.md) | ✅ 已產出（Full；24 variant；今日格 outline ring；32 var 零漂移） |
| DateRangePicker | [`components/DateRangePicker.md`](components/DateRangePicker.md) | ✅ 已產出（Full；88 variant；overlay 雙月曆 252px 並排；43 var 零漂移） |
| DateTimePicker | [`components/DateTimePicker.md`](components/DateTimePicker.md) | ✅ 已產出（Lite；132 variant；雙 icon action-container；50 var 零漂移） |
| TimePicker | [`components/TimePicker.md`](components/TimePicker.md) | ✅ 已產出（Lite；3 軸；clock icon；dropdown list 型；3 token gap） |
| Accordion | [`components/Accordion.md`](components/Accordion.md) | ✅ 已產出（Lite；Single/Multiple expandMode；展開 chevron 動畫；keyboard Tab/Enter/Space） |
| PredefinedDialog | [`components/PredefinedDialog.md`](components/PredefinedDialog.md) | ✅ 已產出（Full；Alert/Confirm/Prompt/Image type × Small(360px)/Large(380px)；shadow-lg；3 按鈕色各異） |
| Message | [`components/Message.md`](components/Message.md) | ✅ 已產出（Full；Mode=Subtle/Ascent/Outline × Type=5色 × Shadow × Button Position；行內固定 520px） |
| Tooltip | [`components/Tooltip.md`](components/Tooltip.md) | ✅ 已產出（Lite；dark bg #303034；Small 260px/Large 320px；9 pointer 位置；shadow-md） |
| Spinner | [`components/Spinner.md`](components/Spinner.md) | ✅ 已產出（Lite；固定 36px；8 色型 Primary/Secondary/Info/Success/Warning/Danger/Light/Dark） |
| ProgressBar | [`components/ProgressBar.md`](components/ProgressBar.md) | ✅ 已產出（Lite；Circle(80px Small)/Linear × Status=Default/Error/Success；10% 步進） |
| Badge | [`components/Badge.md`](components/Badge.md) | ✅ 已產出（Lite；Small(16px)/Large(20px)；Ascent/Subtle × 8 類型 × Word/Letter/Dot） |
| Chips | [`components/Chips.md`](components/Chips.md) | ✅ 已產出（Lite；Extra Small 20px；Full Rounded/Rounded × Filled/Outlined/Flat；5 狀態） |
| Avatar | [`components/Avatar.md`](components/Avatar.md) | ✅ 已產出（Lite；5 尺寸 24-56px；Letter bg=#D5E4FF；Status + Notification badge；6 Mode） |
| DsHeader | [`components/DsHeader.md`](components/DsHeader.md) | ✅ 已產出（Full；ERP 客製 App Shell 頂部列；三版本 A7/A8_V1/A8_V2 × 三狀態 Breadcrumb/Title/BookTable；含 DsHeaderHome / DsUtilityNavigation） |
| DsNavigationA8 | [`components/DsNavigationA8.md`](components/DsNavigationA8.md) | ✅ 已產出（Full；Code 名 DsSideNavMenu；close=72px rail / open=72+260px rail+sidebar；L1/L2/L3 三層導覽） |
| DsDialogHeader | [`components/DsDialogHeader.md`](components/DsDialogHeader.md) | ✅ 已產出（Lite；Dialog 頂部列；三配置 A7 title / A7_CRS 含來源編號 / A8 title+縮放；h=46px） |
| DsSectionHeader | [`components/DsSectionHeader.md`](components/DsSectionHeader.md) | ✅ 已產出（Lite；左側 5px accent 藍條 + 14px/700/primary 標題文字；用於 Form / Card 區段分組） |
| DsAmountSummaryCard | [`components/DsAmountSummaryCard.md`](components/DsAmountSummaryCard.md) | ✅ SummaryCard 設計契約（Full；財務模組 Form 頂部摘要區；4 state Default/close/Open/Scrolling；指標區與 Stepper 步數依模組配置；single/dual/formula 三種指標模式；6 Figma 節點）|
| DsSmartBtn | [`components/DsSmartBtn.md`](components/DsSmartBtn.md) | ✅ 已產出（Lite；Form View 關聯單據導覽列 Smart Bar；1–8 個 card-btn；count/unit/label/arrow；⚠️ unit 11px 與 #4287f0 無對應 token）|
| AppBar | — | ⬜ 待建立（Syncfusion AppBar；ERP 以 DsHeader 取代） |
| Toolbar | — | ⬜ 待建立（FAI2 無對應元件） |
| Menu | — | ⬜ 待建立（Syncfusion Menu；ERP 導覽以 DsNavigationA8 取代） |
| ContextMenu | — | ⬜ 待建立（FAI2 無對應元件） |
| Breadcrumb | [`components/Breadcrumb.md`](components/Breadcrumb.md) | ✅ 已產出（Lite；3 層固定結構 模組>功能>單號；erp-breadcrumb 對應；aria-current="page"） |
| Sidebar | [`components/Sidebar.md`](components/Sidebar.md) | ✅ 已產出（Full；Push/Over/Auto 三模式；nav-rail 72px 固定對應；focus trap Over 模式；App Bottom Nav 替代） |
| Splitter | — | ⬜ 待建立（FAI2 無對應元件） |
| Card | — | ⬜ 待建立（FAI2 無對應元件） |
| DashboardLayout | — | ⬜ 待建立（FAI2 無對應元件） |
| Ribbon | — | ⬜ 待建立（FAI2 無對應元件） |
| TreeView | — | ⬜ 待建立（FAI2 無對應元件） |

> 收編原則：`prototyper` 既有的深度元件規格（DataGrid / Stepper / SummaryCard…）為「prototype 單檔 HTML/CSS 落地層」；設計文件是「what/why/token/state 的權威」。補設計文件時**引用** profile，不重寫 token 決策（見 `component-doc-schema.md §8`）。

---

## 1. 佈局與導覽（Layout & Navigation）

> Import 多來自 `@syncfusion/ej2-vue-navigations` / `-layouts`。

| 元件 | 用途 | 文件狀態 |
|------|------|---------|
| Tab | 同一畫面切換多組內容 | ✅ 已產出 |
| Accordion | 可摺疊區塊、FAQ、設定分組 | ✅ 已產出 |
| DsSectionHeader | Form / Card 區段分組標題（左側藍條 + 粗體標題） | ✅ 已產出 |
| AppBar | 頂部應用列 / header chrome | ⬜ 待建立（Syncfusion AppBar；ERP 以 DsHeader 取代） |
| Toolbar | 工具列、批次操作列 | ⬜ 待建立 |
| Menu | 主選單、多層導覽 | ⬜ 待建立（Syncfusion Menu；ERP 導覽以 DsNavigationA8 取代） |
| ContextMenu | 右鍵 / 長按情境選單 | ⬜ 待建立 |
| Breadcrumb | 階層路徑導覽 | ✅ 已產出 |
| Sidebar | 側邊抽屜 / 導覽面板 | ✅ 已產出 |
| Splitter | 可調整比例的分割版面 | ⬜ 待建立 |
| Card | 卡片容器 | ⬜ 待建立 |
| Dashboard Layout | 可拖拉的儀表板網格 | ⬜ 待建立 |
| Ribbon | Office 風格功能區 | ⬜ 待建立 |
| TreeView | 樹狀導覽 / 階層清單 | ⬜ 待建立 |

## 2. 按鈕與動作（Buttons & Actions）

> Import：`@syncfusion/ej2-vue-buttons`。

| 元件 | 用途 | 文件狀態 |
|------|------|---------|
| Button | 各類操作 CTA | ✅ 已產出 |
| FAB / SpeedDial | 浮動主操作 | ✅ 已產出 |
| ButtonGroup | 一組互斥切換（List/Form 模式切換、週期選擇） | ✅ 已產出 |
| DropDownButton | 收納多個次要操作（更多操作選單） | ✅ 已產出 |
| SplitButton | 主動作 + 下拉次動作（核准 + 核准並產傳票） | ✅ 已產出 |
| IconButton | icon-only 嵌入式操作點（Grid actions 欄、工具列） | ✅ 已產出 |
| ProgressButton | 帶進度狀態的按鈕 | ⬜ 待建立 |

## 3. 輸入 — 文字（Text Inputs）

> Import 多來自 `@syncfusion/ej2-vue-inputs`。

| 元件 | 用途 | 文件狀態 |
|------|------|---------|
| TextBox | 文字輸入 | ✅ 已產出 |
| NumericTextBox | 數值 / 金額輸入 | ✅ 已產出 |
| TextArea | 多行文字（備註、說明欄） | ✅ 已產出 |
| MaskedTextBox | 格式遮罩輸入（電話 / 統編 / 日期） | ✅ 已產出 |
| OTP Input | 一次性驗證碼輸入 | ✅ 已產出 |
| PasswordTextBox | 密碼輸入（含 eye toggle） | ✅ 已產出 |
| In-place Editor | 行內就地編輯 | ⬜ 待建立 |
| Signature | 手寫簽名 | ⬜ 待建立 |

## 4. 輸入 — 選擇（Selection Inputs）

> Import 多來自 `@syncfusion/ej2-vue-dropdowns` / `-buttons` / `-inputs`。

| 元件 | 用途 | 文件狀態 |
|------|------|---------|
| DropDownList | 單選下拉 | ✅ 已產出 |
| AutoComplete | 輸入即時建議 | ✅ 已產出 |
| ComboBox | 可輸入可選的下拉 | ✅ 已產出 |
| MultiColumn ComboBox | 多欄下拉（顯示多欄資訊） | ✅ 已產出 |
| MultiSelect | 多選下拉 / 標籤選擇 | ✅ 已產出 |
| DropDownTree | 樹狀下拉選擇 | ✅ 已產出 |
| ListBox | 清單選擇 / 雙清單搬移 | ✅ 已產出 |
| Checkbox | 多選框 | ✅ 已產出 |
| RadioButton | 單選鈕 | ✅ 已產出 |
| Switch | 開關切換 | ✅ 已產出 |
| Slider | 數值滑桿 | ⬜ 待建立 |
| Rating | 星等評分 | ✅ 已產出 |
| ColorPicker | 色彩選擇 | ✅ 已產出 |
| DsColorPicker | 客製固定色盤選擇器（Ds 元件） | ✅ 已產出 |

## 5. 日期與時間（Date & Time）

> Import：`@syncfusion/ej2-vue-calendars`。

| 元件 | 用途 | 文件狀態 |
|------|------|---------|
| DatePicker | 日期選擇（另有客製 `DsDatePicker` 含多語系格式） | ✅ 已產出 |
| Calendar | 月曆面板 | ✅ 已產出 |
| DateRangePicker | 日期區間選擇 | ✅ 已產出 |
| DateTimePicker | 日期＋時間選擇 | ✅ 已產出 |
| TimePicker | 時間選擇 | ✅ 已產出 |

## 6. 資料展示與表格（Data Display）

> Import 多來自 `@syncfusion/ej2-vue-grids` / `-navigations` / `-layouts`。

| 元件 | 用途 | 文件狀態 |
|------|------|---------|
| Grid | 資料列表、報表、可排序/篩選表格 | ✅ 已產出（v0.3 FAI2 對齊） |
| TreeGrid | 階層式表格 | ✅ 已產出（v0.3 FAI2 對齊；indent 32px、expander 16/18px） |
| Pivot View | 樞紐分析表 | ✅ 已產出（v0.3 FAI2 對齊；header primary-5%+border；⚠️ DS AA 待補） |
| Spreadsheet | 試算表 | ✅ 已產出（v0.3 FAI2 對齊；Ribbon primary-8%、cell 0.5px border；⚠️ ERP 建議限定可編輯範圍） |
| ListView | 清單檢視 | ✅ 已產出（v0.3 FAI2 對齊；item min-h 40px、text weight=400） |
| Kanban | 看板（拖拉卡片） | ✅ 已產出（v0.3 FAI2 對齊；card p-[16px]、header primary-5%） |
| Gantt | 甘特圖 / 專案排程 | ✅ 已產出（v0.3 FAI2 對齊；row-h 36px(Small)；雙面板） |
| Schedule | 行事曆 / 排程檢視 | ✅ 已產出（v0.3 FAI2 對齊；active-view-bg on-surface-8%、slot 62px） |
| QueryBuilder | 視覺化條件查詢建構 | ✅ 已產出（v0.3 FAI2 對齊；巢狀群組 max-depth 2） |
| Pager | 分頁器 | ✅ 已產出（v0.3 FAI2 對齊；h 48px(S)/56px(L)、btn 32/40px） |

## 7. 圖表與視覺化（Charts & Visualization）

> Import 多來自 `@syncfusion/ej2-vue-charts` / `-gauges` / `-maps` / `-diagrams` / `-barcode-generator`。

| 元件 | 用途 | 文件狀態 |
|------|------|---------|
| Chart | 2D 直角座標圖（折線 / 長條 / 區域…） | ⬜ 待建立 |
| Chart3D | 3D 圖表 | ⬜ 待建立 |
| Accumulation Chart | 圓餅 / 環圈圖 | ⬜ 待建立 |
| Bullet Chart | 子彈圖（KPI 對標） | ⬜ 待建立 |
| Stock Chart | 股價 / 財務 K 線圖 | ⬜ 待建立 |
| Smith Chart | 史密斯圓圖（工程用） | ⬜ 待建立 |
| Sparkline | 行內微型趨勢圖 | ⬜ 待建立 |
| Range Navigator | 圖表時間區間導覽 | ⬜ 待建立 |
| Circular Gauge | 圓形儀表 | ⬜ 待建立 |
| Linear Gauge | 線性儀表 | ⬜ 待建立 |
| Heatmap | 熱力圖 | ⬜ 待建立 |
| TreeMap | 矩形樹狀圖 | ⬜ 待建立 |
| Maps | 地圖 | ⬜ 待建立 |
| Diagram | 流程圖 / 節點圖 | ⬜ 待建立 |
| Barcode | 條碼 / QR 產生 | ⬜ 待建立 |

## 8. 回饋與狀態（Feedback & Status）

> Import 多來自 `@syncfusion/ej2-vue-notifications` / `-popups`。

| 元件 | 用途 | 文件狀態 |
|------|------|---------|
| Toast | 操作結果提示（success / warning / error） | ✅ 已產出 |
| Skeleton | 載入佔位 | ✅ 已產出 |
| Dialog | 確認操作、deeplink 跳轉、表單 modal | ✅ 已產出 |
| Predefined Dialogs | Alert / Confirm / Prompt 預設對話框 | ✅ 已產出（Full） |
| Message | 行內訊息條 | ✅ 已產出（Full） |
| Tooltip | 提示氣泡 | ✅ 已產出（Lite） |
| Spinner | 載入轉圈 | ✅ 已產出（Lite） |
| ProgressBar | 進度條 | ✅ 已產出（Lite） |
| Badge | 數字 / 狀態角標 | ✅ 已產出（Lite） |
| Chips | 標籤 / 篩選 chip | ✅ 已產出（Lite） |
| Avatar | 使用者頭像 | ✅ 已產出（Lite） |
| DsDialogHeader | Dialog 頂部列（三配置：title / 含來源編號 / title+縮放） | ✅ 已產出（Lite） |

## 9. 編輯器與富內容（Editors & Rich Content）

> Import 多來自 `@syncfusion/ej2-vue-richtexteditor` / `-documenteditor` / `-image-editor` / `-pdfviewer` / `-filemanager` / `-inputs`。

| 元件 | 用途 | 文件狀態 |
|------|------|---------|
| RichTextEditor | 富文本編輯 | ⬜ 待建立 |
| Document Editor | Word 文件編輯 | ⬜ 待建立 |
| Image Editor | 圖片編輯 | ⬜ 待建立 |
| PDF Viewer | PDF 檢視 | ⬜ 待建立 |
| File Manager | 檔案總管 | ⬜ 待建立 |
| File Upload | 檔案上傳 | ⬜ 待建立 |

## 10. 流程與其他（Flow & Misc）

| 元件 | 用途 | 文件狀態 |
|------|------|---------|
| Stepper | 單據狀態流程、多步驟精靈 | ✅ 已產出 |
| Carousel | 輪播 | ⬜ 待建立 |
| AI AssistView | AI 對話輔助面板 | ⬜ 待建立 |
| Chat UI | 聊天介面 | ⬜ 待建立 |

---

## 客製 `Ds` 元件（前綴 `Ds`，路徑 `components/ds/`）

| 元件 | 用途 | 文件狀態 |
|------|------|---------|
| `DsDatePicker` | 含多語系格式的日期選擇（Syncfusion DatePicker 客製包裝） | ✅ 已產出 |
| `DsColorPicker` | 固定色盤選擇器 | ✅ 已產出 |
| `DsHeader` | App Shell 頂部列（三版本 A7/A8_V1/A8_V2 × 三狀態 Breadcrumb/Title/BookTable） | ✅ 已產出 |
| `DsNavigationA8` | 側邊導覽選單（Code 名 DsSideNavMenu；close/open 兩態；L1/L2/L3 三層導覽） | ✅ 已產出 |
| `DsDialogHeader` | Dialog 頂部列（三配置 A7/A7_CRS/A8；h=46px；含來源單號 / 人員操作 / 縮放歷程 btn） | ✅ 已產出 |
| `DsSectionHeader` | 區段標題（左側 5px 藍條 + 14px/700/primary 標題；Form / Card 分組用） | ✅ 已產出 |
| `DsAmountSummaryCard` | 財務模組 SummaryCard（Form 頂部摘要卡；指標區與 Stepper 步數依模組配置；single/dual/formula 三種指標模式） | ✅ 已產出 |
| `DsSmartBtn` | Form View 關聯單據導覽列（Smart Bar；card-btn 含 count/unit/label/arrow-outward） | ✅ 已產出 |

> 優先順序：Syncfusion 既有元件 → 客製 `Ds` 元件 → 行內組合。需新增客製元件前，先 audit 上表是否已有可用者（見 SKILL.md §Governance）。
