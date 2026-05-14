# Athena ERP Profile

> Athena ERP 專案專屬的 prototype 製作規則。本檔在 `SKILL.md` 之上**附加** ERP 專屬限制；**不可放寬**通用限制。

## 觸發載入此 profile

使用者提到下列任一情境時載入:

- 提到「Athena ERP」、「ERP 模組」、「ERP prototype」
- 路徑出現 `prototype/project/`、`docs/notion/...md`、Odoo model（`psi.*` / `ac.*` / `cm.*`）
- cwd 在 ERP repo 內（`/Users/athena/working/ddd/ERP-System` 或同類路徑）
- 同類舊 prototype 在 ERP repo 內

## 開工前額外確認（疊加 SKILL.md §1）

`SKILL.md` 三項通用問題之外，另須確認:

1. **對應 Odoo model**（如 `psi.sale_order`，衍生 `programID = PSI-SO`）
2. **模組分類**（財務 / 進銷存 / 人事 / 設定檔，決定 breadcrumb 與 nav-rail 高亮）
3. **單據類型**（**作業檔 / 設定檔**，決定走哪份 starter template 與下游章節）— 判斷準則見 §設定檔（Master Data）特化規則

## 規格抽取表（PM 文件 → prototype）

從 `docs/notion/*.md` 依序抽 7 項；缺漏即在 chat 詢問:

| 抽取項 | 用於 |
|---|---|
| 模組中文名 | 檔名、`<title>`、breadcrumb |
| 模組分類 | breadcrumb 第一段、nav-rail 高亮 |
| Odoo model | programID 衍生、chat handoff 必附 |
| 欄位表（label / 型別 / 必填） | Form View form-grid cells |
| 狀態流程圖 / 動作清單 | stepper、footer 動作按鈕（**設定檔不需要**） |
| 關聯單據 | Smart Bar（無關聯則整段移除；**設定檔通常不渲染**） |
| List 預設搜尋條件 | search bar `<option>` |

> 設定檔的規格中不會出現「狀態流程」、「核准 / 提交 / 作廢」等欄位；缺漏不代表規格不完整，是類型本身就不需要。

## 檔案路徑

- **starter template**:
  - 作業檔 → `templates/module-page.html`（含 Summary Bar / Stepper / Smart Bar / 狀態動作群）
  - 設定檔 → `templates/setup-page.html`（含設定檔側欄 / 批次刪除 / active chip / 儲存變更 footer）
- **輸出**: `prototype/project/<模組中文名>.html`
- **配套資源**: `prototype/app.js`、`prototype/app.css`、`prototype/ds/colors_and_type.css`
- **規格來源**: `docs/notion/*.md`（檔名同模組中文名）

---

## 視覺系統來源（Visual System Reference）

ERP prototype 的視覺語言來自 **Claude Design 內部 design system bundle**。每次製作前都應假設使用者沒提到、但答案在這裡：

- Bundle URL：`https://api.anthropic.com/v1/design/h/3NlTfIVpv5CoYw5xmHzi5g`
- 主檔：`Components v3.html`
- 必讀：`athena-design-system/README.md`（品牌語氣、色彩、字型、icon、layout、states、elevation 全規範）
- 元件 CSS：`athena-design-system/project/components.css`（`.input.filled`、`.dg`、`.shell .rail-nav` 等所有 class 規格）

prototype 的 `prototype/ds/colors_and_type.css` 與 `prototype/app.css` 已預先把 DS 的 token / 元件樣式 wire 好。**AI 容易反射地走 Bootstrap / generic web app 樣式（outlined input、所有按鈕帶 icon、hover-only action 等），請每次製作前掃一次 `pitfalls.md` 的「DS 反射對照表」。**

---

## App Shell 規範

- **erp-header**:
  - 結構：home button + breadcrumb（模組分類 / 功能名 / 單號）+ favorite + 通知 / 設定 / avatar
  - icon variant：**filled**（`home` / `notifications` / `settings`，**禁** `*_outline`）
  - EIP / 產品 icon 區塊寬度須**對齊 nav-rail 寬度（72px）**；icon 尺寸 24px、非隨意縮小
- **nav-rail**:
  - 5 項固定順序 — 我的最愛、財務、進銷存、人事、設定檔
  - icon variant：**filled**
  - **必含產品縮寫**文字（如 `ERP`），位置依 template；不可遺失
- **erp-footer**: 左 programID（如 `PSI-SO`）、右版號（格式 `vX.Y.Z.A.B`）

---

## State Machine

| State | label | 流向 |
|---|---|---|
| `draft` | 草稿 | → `submitted` (via `action_submit`) |
| `submitted` | 已提交 | → `approved` (via `action_approve`) / 退回 `draft` |
| `approved` | 已核准 | → `submitted` (via `action_unapprove`) |
| `voided` | 已作廢 | 獨立分支 (via `action_void`) |

- **IMPORTANT:**「已產生傳票」**不是獨立狀態**，由 `move_id` 是否存在判斷，顯示為 `chip--success`
- 偏離此命名（如多個審核層級、加 `cancelled`）**須在 chat handoff 註明理由**

---

## Summary Bar 結構（必依）

Form View 頂部的 summary bar 在實作時最常被改錯。**必依**以下結構：

- 採 `position: sticky; top: 0`，滾動時固定在 main 區域頂部
- **無 shadow**（DS 預設卡片無陰影；shadow 只在 hover/dialog 用）
- 分**上下兩塊**：
  - 上區：標題（單號）+ Stepper 或 voided pill
  - 下區：關鍵指標（總金額 / 幣別 / 建立者 / 「已產生傳票」chip）
  - 兩塊之間用 `1px solid var(--border-default)` 分隔，**不**用 shadow
- padding：上下各 `var(--space-3xl)`（24px），左右隨 main panel
- 寬度撐滿 main panel；圓角延續 main panel 的 12px

> voided 狀態下，上區的 stepper 改顯示 `pill-voided`（紅色膠囊），其他結構不變。

---

## Stepper 三狀態結構（必依）

`<ol class="stepper">` 內每個 step 是 `<li class="stepper__step">`，step 之間插入 `<span class="stepper__bar">` 連接線。

### 結構

```html
<ol class="stepper">
  <li class="stepper__step stepper__step--done">
    <span class="stepper__num"><span class="material-symbols-outlined">check</span></span>
    <span>草稿</span>
  </li>
  <span class="stepper__bar stepper__bar--done"></span>
  <li class="stepper__step stepper__step--active">
    <span class="stepper__num">2</span>
    <span>已提交</span>
  </li>
  <span class="stepper__bar"></span>
  <li class="stepper__step">
    <span class="stepper__num">3</span>
    <span>已核准</span>
  </li>
</ol>
```

### 三種 step 狀態

| 狀態 | class modifier | 圓圈 `.stepper__num` (32×32, radius 100px) | 標籤文字 |
|---|---|---|---|
| Pending | （無 modifier） | bg `rgba(var(--color-sf-primary), .08)` on `var(--bg-surface-default)`、border `1px solid var(--border-default)`、數字 `var(--text-primary)` 14px/400 | `var(--text-secondary)` 14px/400 |
| Active | `--active` | bg `rgb(var(--color-sf-primary))`、border `1px solid rgb(var(--color-sf-primary))`、`box-shadow: inset 0 0 0 1px #fff`（內白環）、數字 `#fff` | `var(--text-primary)` |
| Done | `--done` | bg `rgb(var(--color-sf-success))`、border `1px solid rgb(var(--color-sf-success))`、無 shadow、改顯示 check icon（Material Symbols, 20px, wght 600, `#fff`） | `var(--text-placeholder)`（比 pending 的 `--text-secondary` 更淡，視覺上「已淡化但已完成」） |

### 連接線 `.stepper__bar`

- 預設：`width: 40px`、`border-top: 2px solid var(--border-default)`、`margin-top: 15px`（對齊圓圈中心）
- `--active`：`border-top-color: rgb(var(--color-sf-primary))`
- `--done`：`border-top-color: rgb(var(--color-sf-success))`

### 狀態 × step 對應矩陣

| `form.status` | Step 1 草稿 | Bar 1 | Step 2 已提交 | Bar 2 | Step 3 已核准 |
|---|---|---|---|---|---|
| `draft` | active | pending | pending | pending | pending |
| `submitted` | done ✓ | done | active | active | pending |
| `approved` | done ✓ | done | done ✓ | done | active |
| `voided` | ⛔ 整個 stepper 隱藏，改顯示 `.pill-voided`（bg `rgba(var(--color-sf-error), .12)`, border `1px solid rgb(var(--color-sf-error))`, color `rgb(var(--color-sf-error))`, 12px medium, 36px 高，文字「已作廢」） | — | — | — | — |

### 容器

```css
.stepper { display: flex; align-items: flex-start; gap: 16px; }
```

---

## Smart Bar `card-btn` 結構（必依）

ERP 的 Smart Bar **不是** 一排有 link icon 的文字連結。是一組以藍框統一包起來的 card-btn，每個 card-btn 顯示「N /單位 標題 ↗」。

### 資料模型

`form.relations` 是陣列，每個元素形狀 `{ type, count, unit, title }`：

| 欄位 | 型別 | 說明 |
|---|---|---|
| `type` | string | 關聯類型 key（如 `'pr'` / `'ec'` / `'voucher'`），用於 `:key` 與 `openRelated(rel)` 路由 |
| `count` | number \| null | 筆數；`null` 代表此關聯不計數（如「會計傳票」），永遠顯示 |
| `unit` | string \| null | 計數單位（如 `'/筆'`）；`count === null` 時可省略 |
| `title` | string | 顯示名稱（如「應付請款單」） |

### 出現條件

`v-if="visibleRelations.length > 0"`，其中 `visibleRelations` 為 computed：

```js
const visibleRelations = computed(() =>
  (form.relations || []).filter(r => r.count == null || r.count > 0)
)
```

也就是「**count > 0 的條目**」**或**「**count == null 的常駐條目**」任一存在即渲染；全部 `count === 0` 時整段不渲染。

### DOM 結構

```html
<nav v-if="visibleRelations.length > 0" class="smart-bar">
  <a v-for="rel in visibleRelations" :key="rel.type"
     class="card-btn" @click.prevent="openRelated(rel)">
    <div class="card-btn__main">
      <span v-if="rel.count != null" class="card-btn__count">{{ rel.count }}</span>
      <span class="card-btn__title">
        <span v-if="rel.unit" class="card-btn__unit">{{ rel.unit }}</span>{{ rel.title }}
      </span>
    </div>
    <span class="card-btn__arrow material-symbols-outlined">arrow_outward</span>
  </a>
</nav>
```

`count == null` 的條目（如「會計傳票」）省略 `.card-btn__count` 與 `.card-btn__unit`。

### 視覺規格

| Token | 值 |
|---|---|
| `.smart-bar` | `display: inline-flex; gap: 12px; padding: 8px 4px 8px 16px; background: var(--bg-surface-default); border: 1px solid rgb(var(--color-sf-primary)); border-radius: 8px; width: fit-content;` |
| `.card-btn` | `display: inline-flex; align-items: center; gap: 8px; padding: 0 12px 0 0; border-right: 1px solid rgb(var(--color-sf-primary))`；`:last-child { border-right: none; padding-right: 0; }` |
| Hover | `opacity: 0.75; transition: 200ms;` |
| `.card-btn__count` | `font-size: 18px; font-weight: 700; color: rgb(var(--color-sf-primary))` |
| `.card-btn__unit` | `font-size: 11px; margin: 0 8px;` |
| `.card-btn__title` | `font-size: 16px; font-weight: 400; color: rgb(var(--color-sf-primary))` |
| `.card-btn__arrow` | 18×18px、Material Symbols `arrow_outward`、`wght: 500; opsz: 20; color: rgb(var(--color-sf-primary))` |

### 互動

- 所有 link `@click.prevent`（SPA 行為），點擊呼叫 `openRelated(rel)` 跳轉到對應列表 / 單據（handler 內依 `rel.type` 路由）
- **禁加 link icon**（如 `<span class="material-symbols-outlined">link</span>`）
- 整段 Smart Bar 內所有顏色統一 `rgb(var(--color-sf-primary))`，視覺動線左到右串連一致

---

## 輸入欄樣式（必依）

- **預設 Shape：Filled**（`.input.filled` — background `surface-variant` + 底部 2px 底線）；`.outlined` 只在特定需要 borders-on-all-sides 的情境用
- **Read Only ≠ Disabled**：
  - read-only：用 `readonly` 屬性，套 DS 的 readonly 樣式（背景仍為 surface-variant、文字 secondary、cursor: default）
  - disabled：用 `disabled` 屬性，opacity 38%、cursor not-allowed、pointer-events: none
  - **禁**用 disabled 屬性表達 read-only（會誤導使用者以為欄位不可用）
- DynamicForm（動態表單）：外層**無 border、padding 0**；外距由父層 `.form-section` 控制
- `DsSectionHeader`：下方 padding 16px（與下方 form-grid 之間）

---

## 按鈕 icon 政策

| 按鈕類型 | icon 政策 | 例 |
|---|---|---|
| 主要 CTA（動詞標籤） | **禁加 icon** | `新增` / `提交` / `核准` / `儲存` / `查詢` |
| 次要 CTA（動詞標籤） | **禁加 icon** | `取消` / `清除` / `回列表` |
| 危險動作 | **禁加 icon** | `批次作廢` |
| icon-only 按鈕 | 必有 icon、必有 `aria-label` | settings / close / favorite |
| 導向類（前/後筆、上下頁） | 允許 chevron | `chevron_left` 上一筆、`chevron_right` 下一筆 |
| Dropdown trigger | 允許 `expand_more` | 「更多操作 expand_more」 |
| Footer 按鈕 | 依 DS 分**層級配色**（primary / outline / text / danger / ghost），無 icon | 提交 = primary、解核 = outline、作廢 = danger |

> 違反此政策的最常見錯誤：「新增」按鈕被加上 `<span class="material-symbols-outlined">add</span>新增`。動詞標籤本身已說明動作，icon 是噪音。

---

## PRD 元件對照（Pass 0 / Pass 2 用）

對應 SKILL.md `### 3. 五階段工作流` 下的 `#### 階段 1` 三段式流程：Pass 0 找權威來源、Pass 1 抽 schema、Pass 2 對表轉實作。本節是 Pass 0 找不到 / 模組無設計文件時的 fallback 對照。

### 權威來源優先順序

衝突時上層贏（呼應 SKILL.md §1 資料來源權重規則）：

1. **模組對應的設計文件元件清單章節** — 該模組元件命名的單一來源；章節命名因模組不同：
   - `docs/uiflowspec/出納模組/<功能>-設計文件.md §2.1.1 元件實作狀態`
   - `docs/uiflowspec/應付模組/<功能>-設計文件.md §3.1 元件庫對照表`
   - `docs/uiflowspec/應收模組/<功能>-互動規格.md §2 互動元素清單`
   - 找不到時：`find docs/uiflowspec/<模組> -type f` → `grep '元件實作狀態\|元件庫對照\|互動元素清單'`
2. **本節 Table A** — 設計文件不存在 / 章節未涵蓋時的最小集 fallback
3. **完整 Syncfusion 元件清單** — `/Users/athena/working/ddd/syncfusion-playground/app/pages/playground/`（95 個 `.vue`，進一步擴充查詢用）

### Table A — PRD 元件詞彙 → 實作對照（最小集）

| PRD 詞彙（含別名）| 適用情境（PRD 文字線索） | Prototype HTML（本 skill） | Production：shared-ui | 共享庫未封裝時：Syncfusion |
|---|---|---|---|---|
| TextBox / 文字 | 單行文字、單號、名稱 | `<input type="text">` + `.input--filled` | `<TextInput>` | `<ejs-textbox>` |
| NumericTextBox / 數值 / Monetary | 金額、數量、含千分位 | `<input>` + `.text-right` | `<NumericInput>` | `<ejs-numerictextbox>` |
| TextArea / Text Area / textarea / 多行 | 備註、長說明 | `<textarea>` + `.input--filled` | `<TextareaInput>` | `<ejs-textarea>` |
| DropDownList / Dropdown / 下拉 | 列舉 ≤ 30 項、單選 | `<select>` + 自製 caret | `<DropdownInput>` | `<ejs-dropdownlist>` |
| MultiSelect / 多選下拉 | 多選 chip 列 | `<select multiple>` | `<MultiSelectInput>` | `<ejs-multiselect>` |
| DropDownTree / 樹狀下拉 | 階層選擇 | (prototype 略，用 select 代) | `<DropdownTreeInput>` | `<ejs-dropdowntree>` |
| DropdownPair / 雙欄下拉 | 雙欄複合下拉 | `<select>` 兩個並列 | `<DropdownPairField>` | — |
| DropdownWithAction | 含側邊動作的下拉 | `<select>` + `<button>` | `<DropdownWithAction>` | — |
| PhoneField | 電話欄位（國碼 + 號碼） | `<select>` + `<input>` | `<PhoneField>` | — |
| DatePicker / Date Picker / 日期 | 單一日期 | `<input type="date">` | `<DatePickerInput>` | `<ejs-datepicker>` |
| DateRangePicker / 日期區間 | 起訖兩日期 | 兩個 `<input type="date">` | `<DateRangePickerInput>` | `<ejs-daterangepicker>` |
| Checkbox / 勾選框 | 布林、多選列項 | `<input type="checkbox">` | `<CheckboxInput>` | `<ejs-checkbox>` |
| Dialog / Modal | confirm / deeplink | `<div class="modal-overlay">` | `<AppDialog>` | `<ejs-dialog>` |
| Toast | 3 秒回饋 | `<div class="toast">` | `<AppToast>` | `<ejs-toast>` |
| Message / inline alert | 表單區段警示 | `<div class="alert">` | `<AppMessage>` | `<ejs-message>` |
| Grid / DataGrid / 多筆 | List View、明細 | `<table class="grid">` | `<DataGrid>` | `<ejs-grid>` |
| NestedGrid / TreeGrid | 巢狀 / 樹表 | `<table>` + 縮排列 | `<NestedGrid>` / `<TreeGrid>` | `<ejs-treegrid>` |
| Pager / 分頁器 | List 底部 | `<div class="pager">` | `<GridPager>` | `<ejs-pager>` |
| Header | App shell 頂部 | `<header class="erp-header">` | `<AppHeader>` | — |
| Sidebar / NavRail | nav-rail + 設定檔側欄 | `<aside class="nav-rail">` | `<NavigationRail>` / `<AppSidebar>` | `<ejs-sidebar>` |
| ContentSection / 區段容器 | Form section block | `<section class="form-section">` | `<ContentSection>` | — |
| SearchPanel | List 搜尋區 | `<div class="search-bar">` | `<SearchPanel>` | — |
| StatusBadge / Badge / 狀態 pill | List 狀態欄、Summary | `<span class="status-pill">` | `<StatusBadge>` | — (自製) |
| Tab / 分頁 | Form 內 Tab block | `<button class="tab">` | (B 類) | `<ejs-tab>` |
| Stepper | 作業檔狀態流程 | `<ol class="stepper">` | (B 類) | `<ejs-stepper>` |
| Button | CTA、操作 | `<button class="btn btn--primary">` | (B 類) | `<ejs-button>` |
| FAB | 浮動主動作 | `<button class="fab">` | (B 類) | `<ejs-fab>` |
| AutoComplete / Lookup | FK 到主檔（含搜尋）| `<input>` + suggest | (B 類) | `<ejs-autocomplete>` |
| Tooltip | hover 說明 | `[title]` attr | (B 類) | `<ejs-tooltip>` |
| Skeleton | loading | `<div class="skeleton">` | (B 類) | `<ejs-skeleton>` |
| ProgressBar | 進度條 | `<div class="progress">` | (B 類) | `<ejs-progressbar>` |
| Spinner | inline loading | `<span class="spinner">` | (B 類) | `<ejs-spinner>` |

> **Switch / boolean_toggle 刻意不列**：設定檔 `active` 強制用 `<DropdownInput>`「啟用 / 停用」（見 §設定檔特化規則）。
> 「(B 類)」= 共享庫 `@web-erp/shared-ui` 尚未封裝；production 化暫直用 Syncfusion。
> **C 類（提案待建）**：`AmountSummaryCard`、`DropdownGrid`（3 欄變體）等個別模組提案，需評估後才進共享庫；prototype 階段以 Tailwind 行內組合替代。

### Table B — Form section 元件推論規則（PRD 沒列 `元件` 欄時用）

| PRD 文字線索 | 推論元件（→ 套 Table A） |
|---|---|
| 「下拉」「選擇」「列表選一個」 | DropDownList |
| 「文字」「填入」「輸入」（單行）| TextBox |
| 「備註」「說明」（多行）| TextArea |
| 「日期」「YYYY-MM-DD」 | DatePicker |
| 「日期區間」「起訖」「起迄」 | DateRangePicker |
| 「金額」「數量」「含千分位」「右對齊」 | NumericTextBox |
| 「啟用」「停用」（設定檔 active） | DropDownList（**非** Switch；設定檔慣例）|
| 「唯讀」「系統帶入」 | TextBox readonly（**非** disabled）|
| 「FK 到」「關聯到」「來自主檔」「domain：」| AutoComplete / Lookup |
| 「明細」「逐列輸入」「可加列」 | Grid |
| 「樹狀」「階層」「組織架構」 | DropDownTree |
| 「多選」「複選」 | MultiSelect |

> 推論結果**必須在 Pass 1 回填 schema 時標記為「推論」**，等使用者確認。

### Section C — 條件式顯隱規則（非元件，但常被 AI 漏處理）

| PRD 文字線索 | 實作 |
|---|---|
| 「條件欄位」「依 X 顯隱」「X='internal' 才顯示」 | 整段 `v-if`，**禁**用 `disabled` / `hidden` 逐欄鎖死 |
| 「核准後唯讀」「狀態 = X 全部唯讀」 | view-level flag class（呼應 §設定檔資料狀態矩陣） |
| 「保留位置但隱藏」「不顯示但保留資料」 | 個別欄位 `v-show` 或外層保留 wrapper |

---

## List View 七項自檢

- [ ] Toolbar: `selectedRows.length === 0` 時顯示主操作；> 0 時切換為批次操作
- [ ] Search 第一個 `<option>` 為 `value=""` 標籤「全部」
- [ ] Grid 欄位順序: `checkbox(sticky-left) → PK(sticky-left) → 一般欄 → 金額(text-right) → 狀態 → actions(sticky-right)`
- [ ] 金額欄 class 加 `text-right`
- [ ] 空狀態套用 `inbox` icon
- [ ] `tfoot` 合計列只在 `rows.length > 0` 時顯示
- [ ] Pager 三段: page size（20 / 50 / 100）/ 範圍與總數 / 上下頁

---

## Form View 七項自檢

- [ ] Summary bar 為 `sticky` + 上下兩塊（標題/stepper / 指標）+ 無 shadow + padding 24px（詳 §Summary Bar 結構）；`form.status === 'voided'` 改顯示 pill（不顯示 stepper）
- [ ] Stepper 三狀態結構正確（`--active` / `--done` / pending + `.stepper__bar`），詳 §Stepper 三狀態結構
- [ ] Section 用 `bar + title + form-grid--4`；DynamicForm 外層無 border、padding 0（詳 §輸入欄樣式）
- [ ] 必填欄位 label 加 `<span class="required">*</span>`；read-only 用 `readonly` 屬性，**禁**用 `disabled`
- [ ] Smart Bar 用 `card-btn` 結構（無 link icon、count + 單位 + 標題 + `arrow_outward`），詳 §Smart Bar `card-btn` 結構；無關聯時整段不渲染
- [ ] Tab block: 表頭右側固定 add 按鈕（按鈕**無 add icon**，詳 §按鈕 icon 政策）；行內編輯模式有 save / cancel
- [ ] Footer 三段: 上下筆 / 動作群（按鈕**分層級配色 + 無 icon**） / 「更多操作」下拉
- [ ] `form.moveId` 存在時顯示「已產生傳票」chip（**禁**當成獨立狀態加進 stepper）

---

## 設定檔（Master Data）特化規則

> 「設定檔」(master data) 的 UI 慣例與「作業檔」(transaction documents) 不同。判斷類型後，**以下章節 override 前述作業檔慣例**：
> - State Machine、Summary Bar (stepper)、Smart Bar、Form Footer 動作群 — **全部不適用**
> - List View 批次操作、Form View 章節結構、Footer — **依本節重寫**
> - Modal / Toast / Empty State、輸入欄樣式、按鈕 icon 政策、App Shell — **沿用前述章節**

### 類型判斷準則（命中任一即為設定檔）

- Odoo model 屬於設定檔層（如 `stock.location` 擴展、`psi.sale_reason`、`psi.sales_category`、`cm.partner_grade`、`cm.branch`、`cm.area`）
- 規格文件路徑在 `docs/notion/<模組>設定檔/`、`docs/notion/共用設定檔/`、`docs/notion/財務設定檔/`、`docs/notion/進銷存設定檔/`
- 規格中**無**「狀態流程圖」、**無**「核准 / 提交 / 作廢」動作
- 模組分類為「設定檔」（nav-rail 第 5 項）

### 作業檔 vs 設定檔 差異速查

| 維度 | 作業檔 (default) | 設定檔 (override) |
|---|---|---|
| State machine | draft / submitted / approved / voided | 僅 `active`: true/false |
| Form Summary Bar | sticky, 上下兩塊, stepper + 指標 | **不使用**；以麵包屑 + Page Title 取代 |
| Stepper | 三狀態 (pending/active/done) + bar | **不使用** |
| Smart Bar (card-btn) | 關聯單據列 | **不使用**（設定檔通常無下游關聯） |
| 模組分類 nav-rail | 財務 / 進銷存 / 人事 | **設定檔** |
| List 工具列批次操作 | 批次提交 / 批次作廢 / 批次匯出 | **僅批次刪除**；icon-only danger 按鈕 + `[已選取 N 筆 ×]` chip |
| List 狀態欄 | status-pill (4 種狀態) | **st-chip**（啟用 / 停用）；display only, **不在列表 toggle** |
| List 操作欄 | view (👁) | **edit + delete**（兩個 icon button） |
| Form 章節結構 | 基本資料 + Smart Bar + Tabs（明細） | 基本資料 → 附加群組（依模組）→（可選）稽核軌跡 |
| Form `active` 欄位 | n/a | **Dropdown**「啟用 / 停用」；**非** `boolean_toggle` widget |
| Form 動作按鈕 | 提交 / 核准 / 解核 / 作廢 + 更多 | 刪除（danger outline）/ 更多操作（儲存後新增、複製）/ **儲存變更** (primary) |
| Form Footer 左群 | 上下筆 (prev/next doc) | 上下筆（`[‹] {n}/{total} [›]`）；新增（`route.id === 'new'`）時整組停用 |
| 稽核軌跡 | n/a（暫不在 prototype 表現） | **表單內 Group**（最近 5 筆 tracking inline）；**不使用 Odoo chatter** |
| 設定檔側欄 | 不使用 | **使用**（main panel 左側列出同組設定，方便跳轉） |

### 設定檔側欄（main panel 左側 sub-nav）

設定檔常以「主檔設定 → 進銷存設定 → 〔本設定〕」進入；**同組內**的同類設定（如「進銷存設定」下：地點設定、成本計算層級、倉庫設定、產品類別設定檔、盤存制依據）以**側欄**列出，方便 reviewer 在設定群組內快速跳轉。

- **位置**：nav-rail 右邊、main panel 左邊；寬度 220px（≤1024px 時整段收成 dropdown，**非**隱藏）
- **結構**：群組標題（如「共用設定」、「財務設定」、「進銷存設定」）+ 連結列表（icon + label）
- **行為**：點擊切換到對應設定的 List View；本檔高亮
- **資料模型**：`settingsSideNav` array，每個 group `{ title, items: [{ key, label, icon }] }`；`activeSettingKey` 決定高亮
- **顯示條件**：**僅在「模組分類 = 設定檔」**時渲染；作業檔不渲染

### List View 七項自檢（設定檔版）

- [ ] Toolbar:`selectedRows.length === 0` 時顯示 `[新增 XX]`（primary、**無 icon**）；> 0 時切換為 icon-only `[🗑 批次刪除]`（danger 框） + `[已選取 N 筆 ×]` chip
- [ ] 點 chip 內 `×` 取消選取；**不**提供獨立「取消選取」文字按鈕
- [ ] **無**批次提交 / 批次作廢 / 批次匯出 / 批次啟用 / 批次停用
- [ ] Search 第一個 `<option>` 為 `value=""` 標籤「全部」（規則同作業檔）
- [ ] 狀態 filter 只列 啟用 / 停用（搜尋區可省略此 filter，由前端切換管道處理）
- [ ] Grid 欄位順序:`checkbox(sticky-left) → 主欄（code/name 連結樣式, sticky-left）→ 一般欄 → 狀態(st-chip, display-only) → actions(sticky-right)`
- [ ] 「一般欄」的語意排序遵守 **識別 → 分類 → 歸屬 → 業務屬性 → 狀態**（如：完整路徑 → 類型 → 所屬倉庫/館別/區域 → 部門 → 公司別 → active）；違反此序視同欄位排序錯誤
- [ ] 操作欄: `[編輯]` + `[刪除]`（兩個 icon button；**非** `[檢視]`）

### Form View 七項自檢（設定檔版）

- [ ] **無** Summary Bar、**無** Stepper、**無** Smart Bar
- [ ] 麵包屑帶 record name（如「進銷存設定檔 / 地點設定 / 一樓門市」）或「新增 XX」
- [ ] 章節分群以「基本資料 → 附加群組 →（可選）稽核軌跡」為骨幹；DynamicForm 外層無 border、padding 0
- [ ] 必填欄位 label 加 `<span class="required">*</span>`；read-only 用 `readonly` 屬性，**禁**用 `disabled`
- [ ] `active` 欄位用 Dropdown「啟用 / 停用」，**非** `boolean_toggle` widget
- [ ] 稽核軌跡群組：只在 `tracking.length > 0` 時顯示；最近 5 筆 inline 顯示「{訊息} ─ {時間戳 monospace}」；背景 `--bg-surface-variant`、12px、`--text-secondary`
- [ ] 隱藏欄位（如本檔的 `scrap_location` / `return_location` / `replenish_location` 等 boolean flag）**不渲染** UI，但保留資料
- [ ] 整段條件式 section（如「庫存與盤點」`v-if="draft.usage === 'internal'"`）以 `v-if` 整段顯隱，**禁**用 disabled / hidden 把欄位逐一鎖死
- [ ] State banner（可選）：若有多重資料狀態（新增 / 編輯啟用 / 編輯停用 / 唯讀檢視），在頁面標題與第一個 section 之間加 `.state-banner` 色條提示；無多狀態可省略
- [ ] 「狀態」欄位 `.form-field.is-keep-editable`，切「啟用 ↔ 停用」即時驅動 `.is-archived-view`（見 §設定檔資料狀態矩陣）

### 設定檔資料狀態矩陣（Form View readonly 行為）

設定檔 form 視角依「資料狀態」決定 readonly 行為。在 `.form-view--setup` 上套兩個 flag class，由 reactive 狀態驅動：

| 資料狀態 | 觸發條件 | `.form-view--setup` flag | 整體 form | 例外可編輯 | Footer 行為 |
|---|---|---|---|---|---|
| 新增中 | `route.id === 'new'` | 無 flag | 全欄位可編輯 | — | `儲存變更` 顯示 |
| 編輯（啟用） | 既有記錄且 `form.active === true` | 無 flag | 可編輯 | 建立後不可變欄位（如 `company_id`）個別加 `readonly` 屬性 | `儲存變更` 顯示 |
| 編輯（停用） | 既有記錄且 `form.active === false` 且 `role !== 'readonly'` | `.is-archived-view` | 整張 form 唯讀（透明背景 + 1px 灰底線、無下拉箭頭） | `.form-field.is-keep-editable` 標記的「狀態」欄保留可編輯（白底） | `儲存變更` 仍顯示（讓 user 切回啟用並儲存） |
| 唯讀檢視 | `role === 'readonly'` | `.is-readonly-view` | 全 readonly（含「狀態」欄） | — | `儲存變更` 改顯示 `👁 唯讀檢視` 標籤 |

**互動規則**

- 「狀態」欄切「啟用 ↔ 停用」**立即**驅動 `.is-archived-view` 切換，不需等儲存（reactive computed 直接綁 `form.active`）
- `.is-keep-editable` 放在最內層 `.form-field`（**非**外層 section），且只用於「狀態」欄這一格；用 CSS 覆寫繼承自 `.is-archived-view` 的 readonly 樣式
- `role === 'readonly'` **優先於** active 狀態 — readonly 視角下「狀態」欄也鎖定
- 「公司別」等建立後不可變欄位：個別 input 加 `readonly` 屬性，**不**靠 view-level flag

**class 命名**（建議延用，避免每模組各自取名）

- `.form-view--setup.is-archived-view` — 整張 form 進入停用唯讀
- `.form-view--setup.is-readonly-view` — readonly 角色視角
- `.form-field.is-keep-editable` — section 內個別欄位的「白名單例外」標記

**State banner 對照**（若有採用 `.state-banner`）

| 資料狀態 | banner variant |
|---|---|
| 新增中 | `.state-banner--new`（藍） |
| 編輯（啟用） | 不顯示 banner |
| 編輯（停用） | `.state-banner--inactive`（紅淡） |
| 唯讀檢視 | `.state-banner--readonly`（灰） |

### Form Footer（設定檔版，必依）

| 區塊 | 內容 | 說明 |
|---|---|---|
| 左群（rec-nav） | `[‹] {recordIndex}/{recordTotal} [›]` | 上下筆切換；`route.id === 'new'` 時整組停用 |
| 右群 | `[刪除]`（danger outline） | 受 `canDelete` 控制（manager 角色才可用）；點擊 → 阻擋（如 `quant_count > 0`）走 error toast；無阻擋 → confirm modal（danger）→ success toast + 返回列表 |
|  | `[更多操作 ▾]`（primary outline） | 下拉：`儲存後新增`、`複製` |
|  | `[儲存變更]`（primary fill） | 受 `canSave` 控制；唯讀視角改顯示 `👁 唯讀檢視` 標籤 |

> **無**「提交 / 核准 / 解核 / 作廢」；**無**「回列表」按鈕（離開靠麵包屑或 `isDirty` 攔截 modal）。

### 設定檔 Modal / Toast 特有場景

| 場景 | 元件 | 樣式 | 文案範例 |
|---|---|---|---|
| 離開未儲存表單 | confirm | warning | 您有尚未儲存的變更。是否放棄並返回列表？ |
| 刪除受阻（單筆，如有依賴庫存） | toast | error | 「{name}」仍有 N 筆庫存，無法刪除 |
| 刪除受阻（批次） | toast | error | 其中 N 筆仍有依賴，無法刪除（**直接阻擋，不彈 confirm**） |
| 批次刪除 | confirm | danger | 將刪除 N 筆 {模組}，是否繼續？／hint：此動作無法復原。 |
| 切換 related 來源（如 `warehouse_id` 連動 `branch_id`） | confirm | info | 此變更將連動更新「館別」與「區域」欄位。 |
| 複製成功 | toast | success | 已複製，請編輯副本 |
| 重置篩選 | toast | info | 已重置篩選 |

### 設定檔 Handoff Checklist（疊加通用 + ERP 清單）

通用 + ERP 清單通過後再逐項打勾：

- [ ] 模組分類 = 設定檔，nav-rail 第 5 項高亮；麵包屑首段為「{設定群組}設定檔」
- [ ] 設定檔側欄渲染，本檔高亮，群組分類正確
- [ ] 無 Summary Bar / Stepper / Smart Bar（不要從作業檔 template 殘留下來）
- [ ] List View 批次模式只有「批次刪除」+「已選取 N 筆 ×」chip
- [ ] List 狀態欄為 st-chip，且**非** toggle（不在列表直接切換 active）
- [ ] List 操作欄為「編輯 + 刪除」icon button
- [ ] Form `active` 用 Dropdown（非 `boolean_toggle`），文案「啟用 / 停用」（**禁**用「已封存」/「已停用」變體）
- [ ] Form Footer 為「上下筆 / 刪除 / 更多操作 / 儲存變更」四段，**無**狀態動作按鈕
- [ ] List 一般欄語意排序遵守「識別 → 分類 → 歸屬 → 業務屬性 → 狀態」
- [ ] Form 資料狀態矩陣已套用：`.is-archived-view` / `.is-readonly-view` / `.is-keep-editable` 三個 class 對應正確；切「狀態」欄即時生效
- [ ] 「離開未儲存表單」攔截 modal 能在 isDirty 時觸發
- [ ] 刪除受阻場景（單筆與批次）能跑出 error toast 並阻擋

---

## Modal / Toast / Empty State

- **Modal kinds**:
  - `confirm`（確認操作，如「確認作廢？」）
  - `deeplink`（跳轉提示，如「跳轉至已核准的傳票」）
  - **`pick` 已淘汰**: 改用 domain 篩選自動帶入，不再做 picker 彈窗
- **Toast**: success / warning / error 三型，3 秒自動消失（`setTimeout` 實作）
- **Empty State**: 統一 `inbox` icon + 主訊息

---

## 常見決策題（ERP 專屬）

| 情境 | 決策 |
|---|---|
| 「已產生傳票」要不要當第 5 個狀態？ | **不要**。它是 chip，由 `move_id` 是否存在判斷，與 4 個狀態正交 |
| Smart Bar 沒有關聯單據？ | 整段 `<nav>` 不渲染，**不留空 bar** |
| 狀態欄要 pill 還是 stepper？ | List View 用 pill；Form View summary card 用 stepper（voided 改 pill） |

> 通用決策題見 `SKILL.md §4`。

---

## chat Handoff 五項（必附）

每次交付都要在 chat 附上:

1. 對應 `docs/notion/...` 路徑（沒有則寫「依 chat 需求」）
2. 目標 Odoo model
3. 相比上版差異（首版寫「初版」）
4. 對齊方向（feature 編號 / Notion page / Linear ticket）
5. 特別注意項（已知 trade-off、待 PM 確認的點）

---

## ERP Handoff Checklist（疊加 SKILL.md §8 通用清單）

通用清單通過後再逐項打勾:

- [ ] 三件套 shell（erp-header / nav-rail / erp-footer）齊全
- [ ] breadcrumb 三層正確（模組分類 / 功能名 / 單號）
- [ ] nav-rail 高亮對應模組分類
- [ ] programID 與版號格式正確（`vX.Y.Z.A.B`）
- [ ] State machine 4 種狀態（含 voided）能在 Form View 正確呈現
- [ ] 「已產生傳票」用 chip，**不在** stepper 內
- [ ] Stepper 三狀態結構（`--active` / `--done` / pending + `.stepper__bar`）正確、矩陣對應 4 種 form.status
- [ ] Smart Bar 在無關聯時整段不渲染；有關聯時用 `card-btn` 結構（無 link icon、count + 單位 + 標題 + `arrow_outward`）
- [ ] Summary bar `sticky` + 上下兩塊 + 無 shadow
- [ ] 所有 input 預設 Filled、read-only 用 `readonly` 屬性（**非** disabled）
- [ ] 動詞 CTA 按鈕無 icon（新增 / 提交 / 核准 / 儲存 / 查詢 / 取消 / 清除）
- [ ] chat handoff 五項齊全（對應 Notion 路徑、Odoo model、版本差異、對齊方向、注意項）
- [ ] 開啟欲實作的 `.html` 後再 export（會標為 primary）

---

## 關聯規範（ERP repo 內專屬）

- 對應 ERP 內部規範: `.claude/rules/prototype-design/PRODUCE.md`、`.claude/rules/prototype-design/CLAUDE.md`
- token 與元件對照: `.claude/rules/figma-design-system/references/tokens.md`、`components.md`
- Syncfusion playground（production 升級時對照）: `/Users/athena/working/ddd/syncfusion-playground/app/pages/playground/`
