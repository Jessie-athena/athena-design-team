# UX Guidelines — 德安 UI/UX 行為準則

> **來源**：Notion 德安 UI/UX Guideline（02.使用者體驗設計）
> **用途**：設計元件 spec（行為層）時的參照依據，定義元件「如何運作」
> **完整原始文件**：https://app.notion.com/p/34f023d9a73c8100b24af86f3fdbf0c4

---

## §1 設計原則（跨元件通用）

以下五大原則來自 02.01 Navigation 但適用於所有元件類別：

| 原則 | 目的 | 實務重點 |
|-----|------|---------|
| **清晰性 Clarity** | 使用者立即理解所在位置與用途 | 避免僅用圖示代表關鍵操作；狀態（hover / active / disabled）需可辨識 |
| **一致性 Consistency** | 降低學習成本，提供一致體驗 | 不同模組共用同一元件樣式；篩選、搜尋、排序的佈局與行為統一 |
| **層級導向 Hierarchy** | 協助使用者辨識結構 | 主導覽（模組層）與子導覽（功能層）需明確分層；表單欄位依重要性排序 |
| **可擴充性 Scalability** | 確保系統長期可維護 | 元件結構支援功能擴張及權限差異；採可摺疊層級設計 |
| **回饋性 Feedback** | 避免使用者迷失於系統 | 所有操作須提供視覺提示；Hover / Active / Focus 狀態需可辨識 |

---

## §2 導覽模式（Navigation）

> 來源：02.01.導覽（Navigation）

### 2.1 五大導覽類型

| 類型 | 定義 | 對應元件 |
|-----|------|---------|
| **全域導航 Global** | 系統核心結構，全產品層級導覽 | Navigation Rail、Navigation Drawer、Top Navigation |
| **返回類 Return** | 幫助使用者回到上一層 | Breadcrumb、Back Button |
| **頁內導航 In-page** | 同頁內容之間的切換 | Tabs（Basic / Card / Pill / Vertical）、Tree Control、Anchor、Back to Top |
| **下鑽類 Drill-down** | 從列表深入內容層 | List → Detail、Nested View |
| **聯想類 Associative** | 引導完成任務或探索相關 | Step Bar、Previous/Next |

### 2.2 頁面導覽規則

- **同層級跳轉**：透過全域導航完成模組間切換
- **頁面間跳轉**：使用 Breadcrumb 或 Back Button（兩者不建議同時使用）
- **任務中斷再回來**：須保留使用者先前的篩選條件、分頁位置與捲動狀態
- **階層切換提示**：Breadcrumb 或頁面標題需即時更新

### 2.3 元件設計重點

**Navigation Rail（ERP 主導覽）**
- 固定於畫面左側；3–7 個主要目的地
- 未收合時顯示文字標籤；收合時顯示 Icon + Tooltip
- 支援 Active / Hover / Focus / Disabled；不得將 CTA 混置其中
- 建議支援鍵盤導航

**Breadcrumb（麵包屑）**
- 層級節點以分隔符（`>`）呈現；最後一層為當前頁（不可點擊）
- 與頁面標題聯動更新；建議 ≤ 5 層
- 長路徑支援折疊（中段縮短為 `…`）

**Tabs（頁籤）**
- 分類標題控制在 2–6 個中文字
- 提供鍵盤導航（左右鍵切換）與 focus 狀態
- 超過 6 個頁籤改用下拉式 Tab 或滾動
- 切換若有延遲，需顯示 loading skeleton

**Step Bar（步驟導覽）**
- 明確展示步驟狀態：Completed / Current / Upcoming
- 完成步驟顯示打勾；必填步驟不允許跳過
- 長流程顯示百分比或「當前步驟 / 總步驟數」

---

## §3 資料顯示原則（Data Display）

> 來源：02.02.資料顯示（Data Display）

### 3.1 三大資料元件分類

| 類別 | 資料流向 | 使用者角色 | 範例元件 |
|-----|---------|---------|---------|
| **Data Entry（資料錄入）** | 使用者 → 系統 | 輸入者 | Input、Select、Date Picker、Form |
| **Data Display（數據顯示）** | 系統 → 使用者 | 閱讀者 | Table、Card、Tag、Chart |
| **Data Interaction（資料互動）** | 雙向（使用者 ↔ 系統） | 操作者 | Filter、Search、Sort、Pagination、Editable Table |

### 3.2 七大設計原則（B2B 後台系統）

| 原則 | 說明 | 實務重點 |
|-----|------|---------|
| **清晰性 Clarity** | 元件讓使用者一眼理解用途與狀態 | 避免圖示代替關鍵操作；狀態轉換清楚可辨；欄位標籤一對一對應 |
| **一致性 Consistency** | 相同類型元件外觀行為一致 | 不同模組共用同一表單/表格/按鈕樣式；錯誤提示統一樣式 |
| **以使用者為中心 User-Centric** | 基於實際任務與情境設計 | 常用欄位置前；依角色權限顯示可編輯內容；支援快速鍵與批次操作 |
| **效率與擴充性 Efficiency & Scalability** | 支援高頻操作與資料量成長 | 大型表格支援伺服器端分頁；可複用的表單模組 |
| **即時回饋 Interactive Feedback** | 動畫回饋強化可理解性 | 操作後立即顯示處理中/成功/錯誤；loading 與 empty state 清楚 |
| **包容性 Inclusivity & Accessibility** | 適用於所有使用者與環境 | 支援鍵盤操作；錯誤提示可被螢幕閱讀器讀取；色彩對比符合 WCAG |
| **語義明確 Meaningful Design** | 視覺、文案與互動傳達明確目的 | 動作按鈕文案反映實際結果（如「儲存設定」非「確認」） |

---

## §4 表單設計（Form Design）

> 來源：02.03.表單設計（Form Design）
> 完整子頁文件：

| 子頁 | Notion URL | 說明 |
|-----|-----------|------|
| 02.03.01 表單結構與通用規範 | https://app.notion.com/p/34f023d9a73c81c3b8dce10b43189089 | 欄位排列、分組、必填標示 |
| 02.03.02 漸進式披露與動態欄位 | https://app.notion.com/p/34f023d9a73c81ca8a5cc8a1858b07da | 依條件顯示/隱藏欄位 |
| 02.03.03 表單提交與錯誤處理 | https://app.notion.com/p/34f023d9a73c813cbfd2fcd8b084fbb4 | 送出流程、取消行為、錯誤提示 |
| 02.03.04 表單類型 | https://app.notion.com/p/34f023d9a73c816a8d12c1a75f5807c2 | 依情境選擇表單容器與呈現方式 |

**設計目標摘要**：
- 讓欄位排列與分組直覺易讀
- 降低認知負擔，避免高密度資訊干擾
- 提供一致的提交、錯誤回饋與系統狀態提示
- 根據使用情境選擇合適的表單類型

**與其他章節關聯**：
- 表單設計需結合 §3 Data Entry 的規範原則
- 錯誤訊息格式見 ux-writer/references/erp-conventions.md

---

## §5 系統狀態與回饋（System Status & Feedback）

> 來源：02.04.系統狀態與回饋（System Status & Feedback）
> 完整子頁文件：

| 子頁 | Notion URL | 說明 |
|-----|-----------|------|
| 02.04.01 系統回饋 | https://app.notion.com/p/34f023d9a73c813abfe1ddd2ed7c7094 | Toast、Alert、Dialog 等回饋元件規範 |
| 02.04.02 系統狀態 | https://app.notion.com/p/34f023d9a73c81028abdd26884d30ccf | Loading、Empty State、Error State 等狀態規範 |

**與文案規範的關聯**：
- Toast / Dialog / Empty State 文案格式 → ux-writer/references/erp-conventions.md
- Voice & Tone 選擇（系統回饋屬於哪個情境）→ ux-writer/references/Frameworks.md

---

## §6 交互與資料處理（Interaction & Data）

> 來源：02.05.交互與資料處理（Interaction & Data）

### 6.1 八大原則

| 原則 | 說明 | 實務重點（B2B 後台） |
|-----|------|-------------------|
| **清晰性 Clarity** | 篩選、搜尋、排序方向一目了然 | 篩選條件顯示於結果上方，提供「清除全部」；排序箭頭需 icon + label |
| **一致性 Consistency** | 不同模組的資料操作方式一致 | 搜尋框、篩選面板、排序控制的佈局與行為統一；同層頁面共用 Toolbar 規格 |
| **即時回饋 Responsiveness** | 每次操作給出可感知回饋 | Loading state 清晰；篩選條件變更後列表立即更新或提供「套用」按鈕 |
| **可控性 Control** | 使用者掌握操作影響範圍 | 複雜篩選提供「套用 / 重設」兩步驟；搜尋框支援 Enter 觸發與 debounce |
| **層級導向 Hierarchy** | 複雜條件有清楚分層 | 篩選條件可依欄位分類；進階搜尋收入可展開面板 |
| **可發現性 Discoverability** | 互動功能易於發現 | Hover 時出現排序提示；篩選 icon 在有條件時顯示 badge；搜尋 placeholder 說明範圍 |
| **可擴充性 Scalability** | 因應資料量成長 | Toolbar 組件化設計；支援伺服器端篩選/排序/搜尋 |
| **無障礙 Accessibility** | 可透過鍵盤與螢幕閱讀器操作 | 排序箭頭可用 Tab / Enter；篩選面板支援鍵盤導覽 |

### 6.2 核心互動模式

**Filter（篩選）**
- 多欄篩選支援「批次套用」避免頻繁請求
- 條件明確標示並可一鍵清除
- 篩選條可收合；提供「清除全部」與「條件記憶」

**Search（搜尋）**
- 關鍵字搜尋優先、複合條件次之
- 支援模糊搜尋與範圍搜尋（scope filter）
- 篩選與結果區需明確關聯（Tag 顯示已套用條件）

**Batch Operations（批次操作）**
- 多選後浮現操作列，行為與單筆一致
- 具風險操作（刪除、發送）需強制確認 Dialog

**Drawer（側開面板）**
- 可在列表中快速開啟細節，避免頻繁換頁
- Drawer 寬度固定，支援鍵盤 ESC 關閉

### 6.3 Loading 模式

| 模式 | 適用情境 | 說明 |
|-----|---------|------|
| **全域式 Loading** | 頁面主內容操作後（如刪除清單、列表篩選） | 限制使用者操作其他區域，Spinner 以程式庫的動畫效果為準 |
| **面板式 Loading** | Panel 面板的資料載入（如載入購物車資訊） | 僅侷限在該面板範圍，Spinner 以程式庫的動畫效果為準 |
