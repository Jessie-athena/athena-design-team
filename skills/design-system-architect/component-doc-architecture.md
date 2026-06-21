# Component 元件設計文件架構 — 定版報告

> 狀態：**定版（已落地首批）**。本報告記錄分析與決策；**架構格式的單一權威**是
> `references/component-doc-schema.md`（本檔不重複其內容，避免漂移）。
>
> 任務：(1) 整理 `ui-designer` / `design-system-architect` / `prototyper` 三 skill 的優化項目；
> (2) 規劃 **Component 元件設計文件說明架構**，供後續由 Figma 讀取元件後逐欄補入。
> 歸屬：`design-system-architect` 擁有；`ui-designer` 消費、`prototyper` 引用。格式：結構化 token-reference ＋ 用法散文 混合。

---

## 0. 參考基準的修正（為何不用行銷官網範本）

初版誤用 `awesome-design-md`（Stitch `DESIGN.md`）當結構範本。盤讀後發現：**該庫記錄的是品牌「行銷官網」**——元件清一色 `pricing-card` / `hero` / `testimonial` / `customer-logo-tile`，**沒有** data grid / table row / sidebar / form-field 等「應用內」元件，與 ERP 主體相反（連 Linear / Notion / Intercom 等 SaaS 品牌的 `DESIGN.md` 也是其官網，非產品 App）。

改以六個真正的 **B2B 產品（in-app）design system** 為結構基準：

| DS | 對 Athena 最有價值的借鑑 |
|---|---|
| **IBM Carbon** | Usage/Style/Code/Accessibility 分頁；**empty/loading/error 一等公民**（skeleton 非 spinner）；token-first |
| **SAP Fiori** | ERP 同домен；**密度 Cozy/Compact 一等公民**；**跨平台 adaptive**（tablet≠縮小 desktop，per-column pop-in）；Table Types 分型 |
| **Salesforce Lightning** | **States 多層**（cell/row/column × hover/focus/selected）；鍵盤導覽 Tab→cell/Arrow/Enter、focus 持留；ARIA 嚴謹 |
| **Ant Design** | **API/Props 與設計指引分離**；資料表功能（選取/排序/篩選/固定欄/inline edit）齊全 |
| **Shopify Polaris** | **每元件 Content 指引**（empty 文案、error、bulk verb+noun）；a11y 四段（Structure/Labeling/Keyboard/Focus） |
| **Atlassian** | 語意 token 命名（Foundation.Property.Modifier）；彈性章節 |

`awesome-design-md` 僅保留**一個**獨立可用點子：**token-reference 語法**（`{token}`）——且被 Carbon/SAP 的 token-first 做法獨立佐證，故續用。

---

## 1. 現況核心問題

三套「元件」表述彼此不互引、詳盡度與 token 真偽不一致：

- `design-system-architect` 的 Component Spec（SKILL.md §Step 2）用佔位 token（`color.primary`），真值是 `--color-sf-primary`；
- `prototyper/profiles/erp-components/*.md`（`DataGrid.md` / `Stepper.md`…）是最深的真實規格，卻沒被當「元件設計文件」收編；
- `athena-components.md` 是孤立目錄，✅ 元件沒連到任何設計文件。

→ 解法：確立「逐元件設計文件」為**單一權威格式**，三 skill 各就各位指向它。

---

## 2. 架構決策（已實作於 schema 檔）

- **兩層 Lite / Full**：原子控制元件（Button/TextBox…）走精簡章節；資料密集・複合元件（DataGrid/Stepper/Dialog…）走全章節（含密度雙軸、多層 states + empty/loading/error、行為、RWD 優先級、鍵盤、跨平台 adaptive、Content）。
- **來源標記制**：🎨 Figma 可讀填 / 🔗 token 引用 / 📋 人工·PRD——讓 Figma 補入可機器化、判斷類不被誤填。
- **Token-reference 強制**：視覺值一律 `{token}`（解析對象 `athena-tokens.md`），禁 raw hex/px；對不上既有 token 停下回報，禁臆造。
- **跨平台 adaptive**：App 不是縮小的 Web，逐斷點描述替代佈局 / pop-in / 欄位優先級 P0–P3。
- **Content 輕量章節**：只放該元件特有文案，一般 microcopy 連 `ux-writer`。
- **收編不漂移**：與 `prototyper` profile 重疊處用引用，不重寫 token 決策。

> 完整章節結構（§0–§13）、Figma 讀取→章節對應 SOP、自檢清單、Lite(Button)/Full(st-chip) 範例 → 見 `references/component-doc-schema.md`。

---

## 3. 三 skill 優化項目（對照實作狀態）

### `design-system-architect`（架構擁有者）
- ✅ §Step 2 改為「依 `component-doc-schema.md` 產出設計文件（先判 Lite/Full）」，並補 Figma 補入流程；範例標為跨專案佔位、指向真版。
- ✅ 新增 `references/component-doc-schema.md`（架構定義）＋ `references/components/Button.md`（首發 Lite 範例，真實 token）。
- ✅ `athena-components.md` 新增「設計文件索引」段，✅ 元件連到 `components/<name>.md`（未產出標待補）。
- ✅ 輸出品質清單補「依 schema 產出」「跨平台採 adaptive」兩項。

### `ui-designer`（消費者）
- ✅ 「Component 應用」職責補：挑用前到設計文件取 variants/states/usage，不臆測；選 ⬜ 待分類 元件須 flag 治理。
- ✅ Step 2 UI Spec 範例 token 標為跨專案佔位，指向 `athena-design.md` / `athena-tokens.md` 真值。

### `prototyper`（引用者）
- ✅ `DataGrid.md` / `Stepper.md` / `SummaryCard.md` 開頭加「上游設計文件」指標與分工說明（設計文件＝權威；profile＝單檔 HTML/CSS 落地層）。
- 不動 `allowed-tools` 與單檔交付定位。

---

## 4. 後續（非本批）

- 逐步補 `references/components/` 其餘 ✅ 已採用元件設計文件：優先 `st-chip` / `DataGrid` / `Stepper`（深度規格已在 prototyper，待收編引用）。
- 首個真實元件做一次「Figma 補入」演練（`get_variable_defs` 填 §3–5），驗證對不上 token 會停下回報。
- 其餘 prototyper 複合 profile（ListSearch / FormGroup / FormFooter）是否各自對應設計文件，待 DS owner 決定（多為佈局 pattern 而非單一 Syncfusion 元件）。
