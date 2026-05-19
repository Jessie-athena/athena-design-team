# Athena ERP Profile

> Athena ERP 專案專屬的 prototype 製作規則。本檔在 `SKILL.md` 之上**附加** ERP 專屬限制；**不可放寬**通用限制。

## 觸發載入此 profile

> 載入本檔前**必先**載入 `profiles/Shared.md`（頁面框架基底）。本檔在共用 profile 之上附加 / 覆寫 ERP 專屬行為。

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

> 結構基底（Header / Nav-rail / Info Bar 尺寸、layout、icon variant、hover/active 樣式）見 [`Shared.md` §頁面框架](Shared.md)。本節只列 ERP 專屬覆寫。

### Breadcrumb 層級

三層固定順序：**模組分類** / **功能名稱** / **單號**

- 例：「進銷存 / 銷售訂單 / SO_2026_000123」
- 新單時單號顯示 `—`（dash）

### Nav-rail 項目（5 項固定順序）

| key | icon | label |
|---|---|---|
| `fav` | `star` | 我的最愛 |
| `finance` | `account_balance` | 財務 |
| `psi` | `inventory` | 進銷存 |
| `hr` | `badge` | 人事 |
| `config` | `settings` | 設定檔 |

- 產品縮寫文字 = `ERP`（位於 `.nav-rail__top`，**不可遺失**）
- 模組分類 → 對應 nav-rail 高亮項：財務 → `finance` / 進銷存 → `psi` / 人事 → `hr` / 設定檔 → `config`

### Info Bar 格式

- **左 programID**：格式 `<MODULE>-<CODE>`（如 `PSI-SO`、`AC-AP`、`HR-EMP`）；由對應 Odoo model 衍生
- **右版號**：格式 `vX.Y.Z.A.B`（如 `v1.0.0.0.0`）

### Class prefix

沿用 `.erp-*` 前綴（legacy），與既有 template 一致；不改為 `.app-*`。

### Home icon 額外規範

(EIP) home icon 區塊寬度須**對齊 nav-rail 寬度（72px）**；icon 尺寸 32px、非隨意縮小。

---

## 頁面捲動範圍（必依）

prototype 頁面層級**不可**出現第二條捲軸。reviewer 反覆遇到的問題：「整頁有一條捲軸、grid 內又有一條」、「sticky footer 被頁面捲走」、「nav-rail 跟著頁面一起滾」——根因都是頁面層級沒鎖高。

### 鎖高規則

| 元素 | 規則 |
|---|---|
| `html` / `body` | `height: 100vh; overflow: hidden`（兩者都要設，缺一不可） |
| `#app` | `height: 100vh; overflow: hidden` |
| `.shell`（erp-header + body + erp-footer 的最外層） | `height: 100vh; overflow: hidden; display: flex; flex-direction: column` |
| `.erp-body`（nav-rail + main 的容器） | `flex: 1 1 auto; min-height: 0; overflow: hidden`（`min-height: 0` 是 flex child 允許縮小的必備） |
| `.erp-main`（list-view / form-view 的容器） | `flex: 1 1 auto; min-height: 0; overflow: hidden; display: flex; flex-direction: column` |
| `.list-view` / `.form-view` | `flex: 1 1 auto; min-height: 0; display: flex; flex-direction: column`；**禁**直接加 `overflow-y: auto` |

### 唯一允許的捲軸來源

| 場景 | 載體 | 規則 |
|---|---|---|
| List View 表身縱向滾動 | `.grid-wrap` | `overflow: auto`；`thead th` 用 `position: sticky; top: 0` 凍結表頭 |
| List View 寬欄位橫向滾動 | `.grid-wrap` | 同上載體（`overflow: auto` 同時負責 X/Y） |
| Form View body 內容捲動 | `.form-view__body`（包住 summary bar 下方所有 section） | `flex: 1 1 auto; min-height: 0; overflow-y: auto`；summary bar 與 form-footer 不在此容器內 |
| Modal overlay 內超長內容 | `.modal` 內部容器 | 允許 modal 自帶捲軸（不影響頁面層） |

### 反例（禁止）

- 在 `<main>` / `.form-view` / `.list-view` 上加 `overflow-y: auto` 或 `min-height: 100vh` 造成頁面整體出現第二條捲軸
- 用 `height: auto` + `overflow: visible` 讓 grid 撐高 → 表頭跟著頁面捲走，sticky 失效
- nav-rail 與 main 共用同一個 `overflow: auto` 父層 → 滑鼠滾輪同時動到 nav-rail

### 驗證方法

開發時把瀏覽器視窗縮到 1280×800 / 1440×900 / 1920×1080 三個尺寸，**頁面右側都不應出現捲軸**；只有 grid 在資料超出時內部出現捲軸。

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

## 資料顯示與過濾慣例

### 會計科目顯示格式

科目欄位（含 dropdown 選項、grid 顯示、autoComplete 結果、smart-bar、列印單）一律使用 `[NNNN] 會計科目名稱` 格式（科目代號方括號 + 半形空白 + 名稱）。

- 來源：`account.code` + `account.name`
- 應用位置：所有出現 `ac.account` FK 的欄位
- **禁**單獨顯示「會計科目名稱」或「會計科目代號」，必須組合

### 公司別（公司過濾）Dropdown 預設值

App Shell 與多公司情境下的「公司別」過濾 dropdown **預設為空白**（無 pre-selection），由使用者主動選擇。

- **禁**顯示「全域」或「全部」當作預設值（會誤導使用者以為已套用全公司視角）
- DOM：`<select v-model="filters.companyId">` 不放 `value=""` 的「全域」option；首 option 即為空白
- 與 List 搜尋區的「狀態」filter（第一 option 為 `value=""` 標「全部」）規則**不同**——搜尋 filter 是「條件選空 = 不過濾」；公司別 filter 是「身分選擇，無預設」

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

## List 搜尋區結構與互動（必依）

工具列下方獨立一列、白底卡片。左欄位、右操作的二段式版面，**操作區永不被擠壓、永不換行**。

### DOM Anatomy

```
.search-bar                       (外層 flex row · 永不換行)
├─ .search-bar__fields            (左：欄位區，flex-wrap 視狀態切換)
│  └─ .field × N                  (每個欄位 = label + input/select)
└─ .search-bar__actions           (右：操作區，flex-shrink: 0 永不被擠壓)
   ├─ .btn-icon.is-primary        (搜尋 🔍 — 主色填底)
   ├─ .btn-icon.is-outline        (清除 🗑 — 主色描邊)
   └─ .btn-icon.is-outline        (收合 / 展開 ▲▼ — 條件顯示)
```

### Token 與尺寸

| 項目 | 值 |
|---|---|
| 卡片背景 | `var(--bg-surface-default)` |
| 卡片邊框 | `1px solid var(--border-default)` |
| 卡片圓角 | `var(--radius-lg)` |
| 卡片內距（展開） | `16px` 四邊 |
| 卡片內距（收合） | `12px 16px` |
| 欄位區與操作區水平 gap | `16px` |
| 欄位之間 gap（展開） | `12px 16px`（垂直 12 / 水平 16） |
| 欄位之間 gap（收合） | `8px` |
| 欄位 min-width | **150px** |
| 欄位高度（展開，含 label） | `62px`（label 18 + gap 4 + input 40） |
| 欄位高度（收合，僅 input） | `40px` |
| input / select 高度 | `40px` |
| 操作按鈕尺寸 | `40 × 40px` |
| 操作按鈕之間 gap | `8px` |

### 展開狀態（預設）

- `.search-bar` → `display: flex; flex-wrap: nowrap; align-items: flex-start`
- `.search-bar__fields` → `display: flex; flex-wrap: wrap; align-items: flex-end; flex: 1 1 auto; min-width: 0`
- `.search-bar__actions` → `flex-shrink: 0; align-self: stretch; align-items: flex-end`
- 每個 `.field` → `flex: 1 1 150px; min-width: 150px`
- 寬度足夠 → 欄位等寬平均分配，並排同一行
- 寬度不足 → 欄位自動換行（操作區固定在第一行右上不被擠下）

### RWD 4 斷點對照

> 對應 `REFERENCE.md §10` 的 XL / L / M / S 四斷點。Search Bar 採「Flex Wrap → 自動收合」兩階段策略，避免水平捲動。

| 斷點 | 欄位排列 | 欄位 flex-basis | 收合行為 |
|---|---|---|---|
| **XL ≥ 1440** | 單列 5 欄 | `flex: 1 1 180px` | 不收合 |
| **L 1280–1439** | 單列 5 欄 | `flex: 1 1 160px` | 不收合 |
| **M 1024–1279** | 2 列換行（3+2 或 4+1） | `flex: 1 1 200px` | 偵測到換行 → 顯示「收合」按鈕 |
| **S 768–1023** | **預設收合**，依寬度動態顯示 N 個欄位（至少 1 個） | 固定 `150px` | 預設 `.is-collapsed = true`；點「展開」可顯示全部 |

> S 斷點下進入頁面時 **預設 `searchCollapsed = true`**，避免擠壓主要表格區域。

### RWD 換行觸發條件

換行於以下任一條件成立時發生：

1. **欄位數量 > 5**（預設 5 個 filter；新增第 6 個會直接掉到下一行）
2. **外層容器寬度不足**：依當前斷點的 flex-basis 計算容納寬度；容器寬度低於該臨界值 → 最右側欄位優先換行
3. **S 斷點預設收合**：進入頁面時直接套 `.is-collapsed`，跳過「先展開再收合」的中間狀態

### 換行偵測（JS）

- `ResizeObserver` 監聽 `.search-bar` 與 `.search-bar__fields` 尺寸變化
- 比對所有 `.field` 的 `offsetTop`：只要任一欄位的 `top > 第 1 個欄位的 top` → `searchWrapped = true`
- 並於 `document.fonts.ready` 之後再量測一次，避免字體載入前判斷錯誤

### 操作區按鈕顯示規則

| 狀態 | 按鈕數量 | 內容 |
|---|---|---|
| 未換行（內容符合容器寬度） | **2 個** | 搜尋 🔍、清除 🗑 |
| 已換行（內容超出 或 欄位 > 5） | **3 個** | 搜尋 🔍、清除 🗑、收合 ▲ |
| 收合狀態 | **3 個** | 搜尋 🔍、清除 🗑、展開 ▼ |

收合按鈕條件渲染：`v-if="searchWrapped || searchCollapsed"`

### 收合狀態（`.is-collapsed`）

點擊 ▲ → `searchCollapsed = true`，整張卡片切換為**單行模式**：

- `.search-bar` 加上 `.is-collapsed`；卡片內距改 `12px 16px`
- `.search-bar__fields` → `flex-wrap: nowrap; overflow: hidden`（強制單行）
- 欄位 → `flex: 0 0 150px; height: 40px`；**label 隱藏**（`display: none`），只顯示 input
- 容納邏輯（即時計算）：

  ```
  available = barInnerWidth - actionsWidth - fieldsPaddingRight
  visibleCount = floor((available + gap) / (fieldWidth + gap))
  ```

- 第 N 個欄位 `v-show="!searchCollapsed || collapsedVisibleCount > N-1"`
- **容納不下的欄位完全隱藏**（`display: none`，**不切半、不模糊邊緣**）
- 容器寬度變化時 `ResizeObserver` 即時重算
- 操作區 → `align-self: center`（垂直置中）

### 收合按鈕 icon / title / aria-label 切換

| 屬性 | `searchCollapsed = false`（可收合） | `searchCollapsed = true`（已收合） |
|---|---|---|
| icon | `keyboard_arrow_up` ▲ | `keyboard_arrow_down` ▼ |
| title | 「收合搜尋」 | 「展開搜尋」 |
| aria-label | `collapse search` | `expand search` |

### 三種狀態速覽

```
[A] 寬螢幕 / 欄位 ≤ 5 / 一行容納得下
┌──────────────────────────────────────────────────────────────────┐
│ [館別代號 ] [館別名稱 ] [所屬區域 ] [公司別 ] [狀態 ]    🔍 🗑   │
└──────────────────────────────────────────────────────────────────┘

[B] 窄螢幕 / 欄位 > 5 → 欄位換行；操作區停留在第一行右上
┌──────────────────────────────────────────────────────────────────┐
│ [館別代號 ] [館別名稱 ] [所屬區域 ]                              │
│ [公司別 ]   [狀態 ]                                  🔍 🗑 ▲    │
└──────────────────────────────────────────────────────────────────┘

[C] 收合 → 單行；左側保留容納得下的欄位（無 label），右側 3 顆按鈕
┌──────────────────────────────────────────────────────────────────┐
│ [TP01]   [輸入名稱關鍵字]  [全部 ▾]              🔍 🗑 ▼        │
└──────────────────────────────────────────────────────────────────┘
```

### 互動行為

| 互動 | 行為 |
|---|---|
| 任一輸入框按 `Enter` | 觸發搜尋（`onSearch`） |
| 點 🔍 搜尋 | 套用所有 filter，頁碼重設為 1 |
| 點 🗑 清除 | 清空所有 filter 並重新查詢 |
| 點 ▲ 收合 | `searchCollapsed = true`，卡片變單行 |
| 點 ▼ 展開 | `searchCollapsed = false`，恢復多行展開 |
| 視窗 resize | ResizeObserver 自動重算 `searchWrapped` 與 `collapsedVisibleCount` |

### 設計原則（給 reviewer 引用）

1. **左欄位、右操作** 的二段式版面，操作區永不被擠壓、永不換行
2. **欄位最小寬 150px**，是 RWD 換行的唯一觸發條件
3. **操作按鈕數量隨狀態自動調整**（2 ↔ 3），收合按鈕僅在需要時出現
4. **收合不是「完全隱藏」**，而是把可容納的欄位保留在原處（單行、無 label），常用 filter 仍可立即使用
5. **不顯示半個欄位** — 容納不下就完全隱藏，避免視覺破碎
6. 所有換行 / 容納計算均**即時動態，依容器寬度**，不依視窗寬度（適用側邊欄收合、嵌入面板等場景）

---

## DataGrid 結構與互動（必依）

List View 的表格本體；唯一的橫向 / 縱向捲軸來源。

### DOM Anatomy

```
.data-grid-block          ← 上邊框；包住 grid + pager 為一組
  └─ .grid-wrap           ← overflow: auto；唯一的捲軸來源
      └─ table.grid       ← width: max-content; min-width: 100%
  └─ .pager               ← 與 grid 相連，無上下額外邊框
```

### RWD 與寬度規則

- `table.grid` → `width: max-content; min-width: 100%`
  - 容器寬時：表格 = 容器 100%，欄位依 `min-width` 平均分配剩餘空間
  - 容器窄時：表格寬度 = 各欄 `min-width` 總和，`.grid-wrap` 顯示水平捲軸
- **不使用** `table-layout: fixed`（讓欄位可依內容自然撐開）
- 一般欄位 HTML **不要寫死** `style="width:..."`，由 CSS 統一控制

### 欄位 min-width

| 欄位類型 | min-width | width |
|---|---|---|
| 一般資料欄（`<th>` / `<td>`） | **200px** | auto |
| Checkbox 欄 `.col-check` | **50px** | 50px |
| 操作欄 `.col-actions`（1 顆按鈕） | **56px** | 56px |
| 操作欄 `.col-actions`（2 顆按鈕） | 96px | 96px |

### 欄位優先級與 RWD 顯示規則（必依）

> 採「優先級欄位 + 黏性欄位 + 橫向捲動」三層策略。重要欄位（勾選、代號、操作）永遠可見；次要欄位於窄視窗下隱藏；對應 §RWD 4 斷點 XL / L / M / S。

#### 優先級分類

| 優先級 | 規則 | 典型欄位 |
|---|---|---|
| **P0** 必要 | 任何斷點皆顯示 | 勾選欄（sticky-left）、主鍵 / 代號（sticky-left）、名稱、狀態、操作欄（sticky-right） |
| **P1** 重要 | M 橫向捲動可見；S 隱藏 | 主要業務欄位（如「應收科目」「對應科目」等） |
| **P2** 次要 | M 橫向捲動可見；S 隱藏 | 補充業務欄位（如「銷貨價格表」「預設區域」等） |
| **P3** 輔助 | L 橫向捲動可見；M / S 隱藏 | 多公司 / 跨組織欄位（如「公司別」） |

#### 實作要點

1. 低優先級欄位以 `data-prio="p1|p2|p3"` 標記，透過 `@media` 設定 `display: none`
2. 黏性欄位（勾選 / 代號 / 操作）採 `position: sticky`，背景色**必須不透明**（見 §資料列 → 斑馬紋），避免捲動時穿透
3. 欄位最小寬度遵守上表，避免文字折行；超出時以 `text-overflow: ellipsis` 截斷並提供 tooltip
4. 「設定」按鈕（toolbar 右側 `tune` icon）提供「自訂顯示欄位」開關，可覆寫斷點預設值並持久化至 `localStorage`

#### 圖例

| 符號 | 意義 |
|---|---|
| ● | 顯示 |
| ↔ | 該斷點下顯示，但隨容器寬度進入橫向捲動 |
| — | 隱藏（`display: none`） |

> 各模組的具體欄位 → 優先級對照表，由該模組 prototype 製作時依本表分類，並在 chat handoff 附上「P0–P3 欄位分配」清單供 reviewer 對照。

### Sticky 凍結欄

- **左凍結**：`.sticky-left` → `position: sticky; left: <offset>`
  - 第 1 欄（checkbox）：`left: 0`
  - 第 2 欄（主鍵，如館別代號）：`left: 50px`（緊接 checkbox）
- **右凍結**：`.sticky-right` → `position: sticky; right: 0`（操作欄）
- z-index 階層：`thead th.sticky-*` = `3`；`tbody td.sticky-*` = `1`；一般 `thead th` = `2`
- **禁加 `box-shadow`** 強調凍結邊（會視覺破碎）

### 垂直捲軸 / 表頭凍結

- `.grid-wrap` 同時負責 X/Y 捲軸；`thead th` 用 `position: sticky; top: 0`
- 上下邊框由 `.data-grid-block` 的 `border-top` + `.pager` 一起處理；內部 `.grid-wrap` 自身 `border: 0`

### 表頭 `thead th` 樣式

- 高度 `45px`，padding `0 16px`
- 背景：5% Primary 疊白底 → `background: rgba(var(--color-sf-primary), .05)` on `var(--bg-surface-default)`
- 字級 14px / Medium / `color: var(--text-primary)`
- **無下邊框**
- 每欄右側用 `::after` 畫 `1px × 26px` 灰線分隔（`background: var(--border-strong)`）；**最後一欄不畫**
- 文字 `text-align: left`（**含「狀態」欄**）

### 資料列 `tbody td` 樣式

- 高度 `50px`，padding `0 16px`
- 字級 14px / `color: var(--text-primary)`
- 列間 `border-bottom: 1px solid var(--border-default)`；最後一列無
- **斑馬紋**：
  - **奇數列**：純白 `#fff`（= `var(--bg-surface-default)`）
  - **偶數列・一般欄（非 sticky）**：`rgba(15, 23, 42, .04)` — 淺岩石色 4% 疊白（semi-transparent，視覺層次靠透明度疊出）
  - **偶數列・Sticky 左欄（勾選 / 主鍵）/ Sticky 右欄（操作）**：`rgb(245, 246, 248)`（= `#F5F6F8` 實色）— **必須不透明**，否則捲動時會穿透看到下層內容
  - 表頭背景同表頭值（5% Primary 疊白，見 §表頭 `thead th` 樣式）

  > 一般欄用半透明、sticky 欄用實色：兩者視覺接近（疊白後皆為 ≈ `#F5F6F8`），但 sticky 必須是 solid 才能正確遮蔽下層。app.css 已配置；token 對應由 DS 內部命名（`--bg-surface-variant` ≈ `#F5F6F8`）。

### 特殊資料型態

- **主鍵 / 代碼欄** `.code-cell`：`font-family: var(--font-family-mono); font-feature-settings: 'tnum'; font-weight: 500`
- **可點擊連結** `.link`：`color: rgb(var(--color-sf-primary)); font-weight: 500`；hover 加底線
- **次要資訊** `.text-secondary`（如電話）：`color: var(--text-secondary)` + `font-family: var(--font-family-mono)`
- **空值顯示**：一律 `—`（em dash），**禁**用「無」「N/A」
- **關聯欄位顯示**：只顯示**名稱**，**不顯示代碼前綴**（如「北區」而非「N · 北區」）

### 狀態 Chip `.st-chip`

- 高度 `28px`、`border-radius: var(--radius-full)`、padding `0 8px 0 6px`、`min-width: 49px`
- 字 12px / Medium / line-height 1.3 / letter-spacing 0.1px
- `.st-chip--active`：背景 `rgba(var(--color-sf-success), .12)`、邊框 + 文字 `rgb(var(--color-sf-success))`
- `.st-chip--inactive`：背景 `rgba(var(--color-sf-error), .12)`、邊框 + 文字 `rgb(var(--color-sf-error))`
- chip 在 cell 中**靠左**（隨欄位 `text-align: left`），不置中

### 操作按鈕 `.ico-btn`

- `40 × 40px`、icon `20px`、`border-radius: var(--radius-sm)`
- 預設背景透明
- Hover：`background: rgba(var(--color-sf-primary), .08)`
- `.is-edit` / `.is-view`（唯讀模式切 chevron）共用同一規格

### 互動規則

| 互動 | 行為 |
|---|---|
| 表頭 checkbox | 部分選取 → `indeterminate`；全選 / 全不選切換 |
| 列 checkbox | 勾選後該列加 `.is-selected`，背景 `rgba(var(--color-sf-primary), .10)` |
| 列 hover | 整列背景 `rgba(var(--color-sf-primary), .06)`；Sticky 欄 hover 同色（**禁**改用不同 tint 讓 sticky 欄看起來是獨立區塊） |
| `.is-selected` × hover | `.is-selected` 優先級高於 hover |
| 列點擊 | **禁**整列可點（避免誤觸）。進詳細只有兩個入口：① 主鍵欄 `.link`、② 操作欄按鈕 |
| 唯讀模式 | 操作欄按鈕從鉛筆（`.is-edit`）切換成右箭頭 chevron（`.is-view`），title 改「檢視」；**禁直接隱藏按鈕**，保留入口 |
| 橫向捲動 | 左 / 右凍結欄保持可見；Sticky 欄一定要明確指定 `background` |
| 進入「批次模式」 | 由父層判斷 `selectedIds.length > 0` 切換 toolbar，與表格本身解耦 |

### 空狀態

無資料時隱藏整個 `.data-grid-block`，改顯示 `.empty-state`（48px `inbox` icon + 標題 + 說明）；**禁**保留空表頭。

---

## List View 七項自檢

- [ ] Toolbar: `selectedRows.length === 0` 時顯示主操作；> 0 時切換為批次操作
- [ ] Search 第一個 `<option>` 為 `value=""` 標籤「全部」（**詳見 §List 搜尋區結構與互動**）
- [ ] Grid 欄位順序: `checkbox(sticky-left) → PK(sticky-left) → 一般欄 → 金額(text-right) → 狀態 → actions(sticky-right)`（**詳見 §DataGrid 結構與互動**）
- [ ] 金額欄 class 加 `text-right`
- [ ] 空狀態套用 `inbox` icon（**詳見 §DataGrid 結構與互動 → 空狀態**）
- [ ] `tfoot` 合計列只在 `rows.length > 0` 時顯示
- [ ] Pager 三段: page size（20 / 50 / 100）/ 範圍與總數 / 上下頁

---

## Form Group 分群與 form-grid RWD（必依）

Form View 主體由多個 Group（`.form-section`）組成，每個 Group 內以 `.form-grid` 排版欄位。本節規範分群結構、grid RWD、輸入欄狀態與 group header 視覺；輸入欄樣式的「Filled vs Outlined」、「readonly vs disabled」原則見 §輸入欄樣式，本節不重述。

### Anatomy

```
.form-section
├─ .form-section__bar           ← 群組標題列（5×21 藍直條 + 14px Bold Primary 標題）
└─ .form-section__body
    └─ .form-grid               ← Grid 容器（決定 RWD 行為）
        └─ .form-field          ← 單一欄位（label + input + help）
            ├─ label            ← 13px Medium，必填 * 紅色
            ├─ input.filled     ← 40px 高、padding 0 10px
            └─ .help（.is-error）  ← min-height 16px（永遠佔位，避免錯誤訊息出現時欄位位移）
```

### Sizing & Spacing

| 元素 | 規格 |
|---|---|
| Section 之間垂直 gap | `24px` |
| 群組標題到內容 padding | `padding-bottom: 16px`（標題列） |
| Grid row / column gap | `gap: 16px` |
| Field 內 label → input | `gap: 6px` |
| Field 內 input → help | `.help { min-height: 16px }`（永遠保留佔位） |
| Input 高度 | `40px` |
| Input 內距 | 左右 `padding: 0 10px`；select 右側 `padding-right: 36px`（讓出 caret） |
| Textarea 預設高 | `min-height: 100px`，`resize: vertical` |

### form-grid 變體與 RWD

**核心原則：用 `minmax(300px, 1fr)` + `auto-fit` 自動換行；≤ 1024px 強制 2 欄。最小欄位寬度 300px。**

| Grid 變體 | template-columns | 適用 |
|---|---|---|
| `.form-grid`（預設） | `repeat(auto-fit, minmax(300px, 1fr))` | 一般 3~4 欄並列（基本資料） |
| `.form-grid--2` | `repeat(auto-fit, minmax(300px, 1fr))` | 地址 / 聯絡（配合 `--full` 跨欄） |
| `.form-grid--1` | `minmax(300px, 1fr)` | 單欄區塊（備註 / textarea） |

### 跨欄 modifier

- `.form-field--span-2` / `.form-field--span-3` — 桌機寬度下跨 2 / 3 欄
- `.form-field--full` — `grid-column: 1 / -1`，**永遠**整列

### 斷點對照表（對應 §REFERENCE.md §10 四斷點）

| 代號 | 範圍 | 自動欄數 | padding | 跨欄 modifier 表現 |
|---|---|---|---|---|
| **XL** | ≥ 1440px | 4 欄（餘額空間平分） | `32px` 左右內距 | `--span-3` 維持 3 欄 |
| **L** | 1280–1439px | 4 欄（緊縮） | `24~32px` | `--span-3` 維持 3 欄 |
| **M** | 1024–1279px | 3 欄（auto-fit 自然收斂） | `24px` | `--span-3` → 降為 2 欄 |
| **S** | 768–1023px | **強制 2 欄** | **`20px`** | 所有 `--span` 皆變 2 欄 |
| — | < 768px | 不支援 | — | — |

#### 為何 ≤ 1024 強制 2 欄而非繼續 auto-fit？

在 ~1000px 時 auto-fit 仍可能給 3 欄但每欄擠到 300px 邊界，標籤易折行；強制 2 欄能維持較寬欄位與更易讀的標籤。

```css
@media (max-width: 1024px) {
  .form-grid       { grid-template-columns: repeat(2, minmax(0, 1fr)); }
  .form-view__body { padding: 20px; }
}
```

### 複合欄位 `.field-phone`（含國碼 + 號碼）

- 容器 `.field-phone` → `display: flex; gap: 8px; min-width: 300px`
- 國碼 select → `width: 110px; flex: 0 0 110px`
- 號碼 input → `flex: 1 1 auto; min-width: 0`（避免 flex item 撐爆）
- 當父 grid 縮到 1 欄時，**複合欄位內部仍維持並排**，不再分行

### `.input.filled` 各狀態（必依）

| 狀態 | 視覺 |
|---|---|
| **Default** | 底色 `var(--bg-surface-variant)`、底線 `1px solid var(--border-strong)`、頂部 `border-radius: var(--radius-sm)` 圓角 |
| **Hover** | 底線顏色不變，僅 `cursor: text` |
| **Focus** | **底線 `2px solid rgb(var(--color-sf-primary))`**，**禁加 outline ring**（焦點靠底線變色表達） |
| **Filled**（有值） | 同 default |
| **Error**（`.is-error`） | 底線 `2px solid rgb(var(--color-sf-error))`；下方 `.help.is-error` 同色顯示訊息 |
| **Readonly** | 背景透明、底線 `1px solid var(--border-default)`、文字 `var(--text-primary)`；`cursor: default`；**不觸發** focus 樣式 |
| **Disabled** | 同 readonly；select 額外隱藏 caret 圖示 |

### `.input.filled.is-select`

- 用原生 `<select>` + `appearance: none` + 自繪 caret（右側 8px 處 `16×16` SVG triangle）
- 右側 `padding-right: 36px` 預留 caret 空間
- Hover → `cursor: pointer`
- Focus 行為與 text input 一致（2px Primary 底線）
- Disabled / Readonly → caret 隱藏、`padding-right` 收回 `10px`、`cursor: default`

### Textarea

- `min-height: 100px`、`resize: vertical`
- padding 改為 `10px` 四邊（承載多行）
- Readonly：背景透明，與 input 一致

### Group Header 樣式

```
.form-section__bar              高度自動，padding 0 0 16px 0
├─ ::before                     5×21 直條，rgb(var(--color-sf-primary))，margin-right 10px
└─ h2.form-section__title       font-size: 14px / font-weight: 700 / color: rgb(var(--color-sf-primary))
```

**規則**：

- 群組標題**不**加底線、**不**加背景
- **不**放任何輔助文字 / icon 在標題列右側（保持極簡）
- Group 之間以 `gap: 24px` 區隔，**不**用分隔線

### 欄位排列原則（撰寫表單時）

1. **同類欄位放同一 Section**，每個 Section 標題用 4~6 字描述（如「基本資料」「聯絡資訊」「補充說明」）
2. **必填欄位優先**放在 Section 前段
3. **代號 / 狀態類短欄位**放第一行右側，**地址 / 備註類長欄位**用 `--full` 整列
4. **每行不超過 4 欄**，視覺上一掃即看完一列
5. 若一列只剩 1~2 欄，補上空白 `<div class="form-field"></div>` 維持 grid 對齊
6. Help text 高度**永遠保留 `min-height: 16px`**，避免錯誤訊息出現時欄位位移

### Readonly / Archived 全域樣式

整張表單為唯讀（`.is-readonly-view`）或停用後檢視（`.is-archived-view`）時，所有 `.input` 變透明背景 + `1px solid var(--border-default)` 底線 + `var(--text-secondary)` 文字色 + `pointer-events: none`；select caret 隱藏。例外規則與切換時機**詳見 §設定檔資料狀態矩陣**。

### A11y

- `<label>` 必須與 input 關聯（同 `.form-field` 內，建議用 `for` + `id` 或包覆方式）
- 必填用 `<span class="required">*</span>` 視覺標記 **且** input 加 `required` 屬性
- Error 訊息與欄位用 `aria-describedby` 連結（reviewer 用 keyboard reader 時可讀到錯誤）
- Tab 順序依 DOM 順序（grid 不改變 tab order）
- Select 支援鍵盤輸入首字快速跳選（原生行為，**不要 JS 攔截**）

---

## Form View 七項自檢

- [ ] Summary bar 為 `sticky` + 上下兩塊（標題/stepper / 指標）+ 無 shadow + padding 24px（詳 §Summary Bar 結構）；`form.status === 'voided'` 改顯示 pill（不顯示 stepper）
- [ ] Stepper 三狀態結構正確（`--active` / `--done` / pending + `.stepper__bar`），詳 §Stepper 三狀態結構
- [ ] Section 用 `bar + title + form-grid--4`；DynamicForm 外層無 border、padding 0（詳 §輸入欄樣式 + §Form Group 分群與 form-grid RWD）
- [ ] 必填欄位 label 加 `<span class="required">*</span>`；read-only 用 `readonly` 屬性，**禁**用 `disabled`
- [ ] Smart Bar 用 `card-btn` 結構（無 link icon、count + 單位 + 標題 + `arrow_outward`），詳 §Smart Bar `card-btn` 結構；無關聯時整段不渲染
- [ ] Tab block: 表頭右側固定 add 按鈕（按鈕**無 add icon**，詳 §按鈕 icon 政策）；行內編輯模式有 save / cancel
- [ ] Footer 三段: 上下筆 / 動作群（按鈕**分層級配色 + 無 icon**） / 「更多操作」下拉（**詳 §Form Footer 結構與互動（共通）**）
- [ ] `form.moveId` 存在時顯示「已產生傳票」chip（**禁**當成獨立狀態加進 stepper）

---

## Form Footer 結構與互動（共通，必依）

作業檔與設定檔的 Form Footer **共用本節結構、RWD、互動、A11y 規則**；右側按鈕內容差異（作業檔的狀態動作群 vs 設定檔的儲存變更 / 啟用按鈕）見對應章節：

- 作業檔右側按鈕內容 → §Form View 七項自檢第 7 項
- 設定檔右側按鈕內容 → §Form Footer（設定檔版，必依）

### Anatomy

```html
<footer class="form-footer">                    <!-- Container（sticky / flex-shrink:0）-->
  <div class="form-footer__nav">                <!-- 左：記錄分頁器 -->
    <button class="pg-btn">‹</button>
    <span  class="form-footer__pos">3 / 27</span>
    <button class="pg-btn">›</button>
  </div>

  <div class="form-footer__actions">            <!-- 右：操作群 -->
    <!-- 內容依「作業檔 / 設定檔」差異，順序：條件按鈕 → 更多操作 ▾ → 主 CTA -->
    <button class="btn btn-outline">啟用</button>
    <div class="btn-more">
      <button class="btn btn-outline btn-more__btn">更多操作 ▾</button>
      <ul class="btn-more__menu">…</ul>
    </div>
    <button class="btn btn-primary">儲存變更</button>
    <!-- 唯讀狀態改顯示： -->
    <span class="readonly-tag">👁 唯讀檢視</span>
  </div>
</footer>
```

**結構規則**

- Footer **必為 `<footer>`** 元素，且為 `.form-view` 的**最後子節點**（不是 `.form-view__body` 內部）
- 左區（`__nav`）與右區（`__actions`）以 `justify-content: space-between` 分置兩端，**中間不得插入任何內容**
- 右區順序固定：**條件按鈕 → 下拉群組 → 主 CTA**（由左至右視覺權重遞增）

### Container Token

| 屬性 | 值 |
|---|---|
| `position` | `sticky`（隨 `.form-view` flex column 釘底） |
| `flex-shrink` | `0`（永遠不被壓縮） |
| `display` / 排版 | `flex` / `align-items: center` / `justify-content: space-between` |
| `padding` | **桌機 `12px 24px`** ／ 平板 `12px 16px` ／ 手機 `10px 12px` |
| `background` | `var(--bg-surface-default)` |
| `border-top` | `1px solid var(--border-default)` |
| `box-shadow` | 內容可捲動時加 footer top shadow；內容未滿一頁時移除。**DS 目前無對應 token**（`--shadow-e1` 是 card / dropdown 用、方向錯），請先在 `REFERENCE.md §11` 新增 `--shadow-footer-top`（建議值 `0 -2px 8px rgba(var(--color-sf-neutral), .04)` 或對應 DS 規格）再引用；prototype 階段未補 token 前可暫時省略 shadow |
| `z-index` | `5`（高於表單內容，低於 modal / toast） |
| `min-height` | `64px`（保留 40px CTA + 12×2 padding） |

### 左：`__nav`（記錄分頁器）

| 屬性 | 值 |
|---|---|
| 元件 | `pg-btn 32×32` + `.form-footer__pos` |
| 顯示格式 | `{當前筆數} / {總筆數}`；**新增模式 `1 / {總筆數 + 1}`** |
| 字級 | 13px / `color: var(--text-secondary)` / `font-variant-numeric: tabular-nums` |
| 間距 | `gap: 4px`，pos 左右 `padding: 0 4px` |
| Hover | `background: rgba(var(--color-sf-primary), .08)`；顏色升至 `var(--text-primary)` |
| Disabled | `opacity: .38; cursor: not-allowed` |

### 右：`__actions`

| 屬性 | 值 |
|---|---|
| 排列 | `display: flex; align-items: center; gap: 8px` |
| 按鈕高度 | 統一 `40px`（CTA / Outline / Dropdown 同高） |
| 主 CTA | `.btn.btn-primary`，寬度自適應、永遠靠右 |
| Outline | `.btn-outline`：`border: 1px solid rgb(var(--color-sf-primary))`、`border-radius: var(--radius-sm)`、`padding: 10px 16px` |
| 下拉 menu | `position: absolute; left: 0; bottom: calc(100% + 4px)`（向上彈、左對齊按鈕） |

### RWD 斷點

| 斷點 | 行為 |
|---|---|
| **≥ 1024px（桌機）** | 預設樣式；左右兩段水平並列 |
| **768–1023px（平板）** | padding 改 `12px 16px`；`__actions` `gap: 6px`；下拉 menu `min-width: 180px` |
| **< 768px（手機）** | ① Footer 改 `flex-direction: column-reverse; align-items: stretch; gap: 8px`（CTA 在上、分頁器在下）<br>② 右區按鈕 `flex: 1`，主 CTA `min-width: 50%`；條件按鈕與「更多操作」各佔等寬<br>③ 「儲存變更」標籤可縮為「儲存」<br>④ 分頁器 `justify-content: center`<br>⑤ Footer 高度允許自動長至約 `120px` |
| **< 360px** | 下拉 menu 改 `position: fixed; left: 12px; right: 12px; bottom: 64px`（鋪滿避免被裁切） |

### 鍵盤 / 觸控

- 按鈕 hit-target **不小於 `40×40px`**（pg-btn `32×32` 在桌機 ok，手機自動放大至 `40×40`）
- Focus ring：使用 DS 預設 `outline: 2px solid rgb(var(--color-sf-primary)); outline-offset: 2px`，**不可移除**

### 互動規則

#### 1. 記錄分頁器

| 觸發 | 行為 |
|---|---|
| 點 `‹` / `›` | 切換到上 / 下一筆。**若表單為 dirty**，先彈確認「尚未儲存的變更會遺失，是否繼續？」 |
| 新增模式（`isNew = true`） | `‹` `›` 皆 disabled；`pos` 顯示 `1 / {recordTotal + 1}` |
| 首筆 / 末筆 | 對應方向 disabled |
| 鍵盤 | `Alt + ←` / `Alt + →` 對應上下筆 |

#### 2. 主 CTA「儲存變更」

| 狀態 | 條件 | 表現 |
|---|---|---|
| 顯示 | `canSave = true`（有編輯權限且表單有效） | `.btn-primary` |
| 隱藏 | 唯讀（無權限 / 已停用 / 角色限制） | 改顯示 `.readonly-tag「👁 唯讀檢視」` |
| Disabled | 表單未通過 validation 或正在送出 | `opacity: .38; pointer-events: none`；hover 顯示 tooltip 列出缺漏欄位 |
| Loading | 送出中 | 文字換成 spinner + 「儲存中…」；其餘 footer 按鈕 disabled |
| 鍵盤 | `Ctrl/Cmd + S` 觸發 | 同點擊 |
| 成功 | toast「已儲存」並把按鈕狀態回 idle | — |
| 失敗 | 維持資料、彈錯誤 modal、CTA 回 idle | — |

#### 3.「啟用」按鈕（設定檔條件出現）

- 出現條件：`!isNew && !formData.active && !isReadOnly`
- 點擊：開啟 confirm modal「確定啟用此 XX？」，確認後呼叫 API；成功後此按鈕消失（`formData.active` 變 `true`）

#### 4.「更多操作 ▾」下拉

| 行為 | 規則 |
|---|---|
| 開合 | 點按鈕 toggle；再次點按鈕關閉 |
| 外部關閉 | 點 menu 以外區域、按 `Esc`、滾動表單、視窗 resize 皆關閉 |
| 彈出位置 | 預設向上彈（`bottom: calc(100% + 4px)`），左對齊按鈕 left |
| 邊界翻轉 | 若上方空間 < `200px`，改向下彈；若右側超出 viewport，自動 right-align |
| 鍵盤 | `Enter / Space` 開合；開啟後 `↑/↓` 切換項目、`Enter` 觸發、`Esc` 關閉 |
| 項目 disabled | `.is-disabled` → `cursor: not-allowed`；hover 不變色；點擊無反應 |
| 內建項目 | 「儲存並新增」、「複製」、（可選）「刪除」、「停用」。新增模式下「複製」disabled |

#### 5. 唯讀模式

- `.form-footer__actions` 只保留 `.readonly-tag`，**移除**條件按鈕 / 更多操作 / 主 CTA
- `__nav` 仍可切換筆數（瀏覽用途）

### Dirty-guard 對照表

| 動作 | 是否需 dirty-guard |
|---|---|
| 切換筆（上下筆） | ✅ |
| 點 Sidebar / 換路由 | ✅ |
| 關閉視窗 / Tab（`beforeunload`） | ✅ |
| 點「儲存變更」 / 「儲存並新增」 | ❌（直接走儲存） |
| 點「複製」 | ✅（複製前先保存當前） |

### 狀態矩陣（速查）

| 角色 / 狀態 | 啟用 | 更多操作 | 儲存變更 | 唯讀 tag | 分頁器 |
|---|---|---|---|---|---|
| Admin・既有・active | — | ✅ | ✅ | — | ✅ |
| Admin・既有・inactive | ✅ | ✅ | ✅ | — | ✅ |
| Admin・新增 | — | ✅（複製 disabled） | ✅ | — | disabled |
| AreaManager・既有 | — | ✅（限部分） | ✅ | — | ✅ |
| Viewer / 唯讀 | — | — | — | ✅ | ✅ |
| 送出中 | disabled | disabled | spinner | — | disabled |

### A11y

- Container `<footer>` 使用語意標籤；**不需** `role="contentinfo"`（已是 footer 元素）
- 分頁按鈕加 `aria-label="上一筆 / 下一筆"`
- `.form-footer__pos` 加 `aria-live="polite"`，筆數變動可被朗讀
- 下拉按鈕：`aria-haspopup="menu" aria-expanded="true|false" aria-controls="more-menu-id"`
- 下拉 menu：`<ul role="menu">`、每個 `<li role="menuitem">`；disabled 項加 `aria-disabled="true"`
- 主 CTA disabled 時用 `aria-disabled="true"` **而非** `disabled` 屬性（避免 disabled 阻擋 tooltip 顯示缺漏訊息）
- 整個 Footer Tab 順序：`prev → pos → next → 條件按鈕 → 更多操作 → 主 CTA`

### 驗收清單（QA Checklist）

- [ ] Footer 永遠貼在 `.form-view` 底部，滾動表單不會推走它
- [ ] 新增模式下 `pos` 顯示 `1 / {total+1}` 且分頁器 disabled
- [ ] 「更多操作」彈出位置：向上、左對齊按鈕；空間不足時自動翻轉
- [ ] 唯讀模式右側只剩 `.readonly-tag`
- [ ] `Ctrl/Cmd + S` 在表單聚焦時可儲存
- [ ] 切換筆數時若 dirty 必跳確認；確認後才切換
- [ ] 手機（< 768px）時 footer 縱向堆疊，CTA 全寬
- [ ] 所有按鈕 focus ring 可見，鍵盤可完整操作
- [ ] 送出中所有 footer 按鈕 disabled，CTA 顯示 spinner
- [ ] Dropdown 在 `Esc` / 外部點擊 / scroll / resize 後關閉

---

## 設定檔（Master Data）特化規則

> 「設定檔」(master data) 的 UI 慣例與「作業檔」(transaction documents) 不同。判斷類型後，**以下章節 override 前述作業檔慣例**：
> - State Machine、Summary Bar (stepper)、Smart Bar、Form Footer 動作群 — **全部不適用**
> - List View 批次操作、Form View 章節結構、Footer — **依本節重寫**
> - Modal / Toast / Empty State、輸入欄樣式、按鈕 icon 政策 — **沿用前述章節**
> - App Shell — 沿用 `Shared.md §頁面框架` + 本檔 §App Shell 規範（breadcrumb 三層 / nav-rail 5 項 / Info Bar 格式）

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

> 通用結構規則沿用 §List 搜尋區結構與互動 + §DataGrid 結構與互動；本清單只列**設定檔覆寫項**。

- [ ] Toolbar:`selectedRows.length === 0` 時顯示 `[新增 XX]`（primary、**無 icon**）；> 0 時切換為 icon-only `[🗑 批次刪除]`（danger 框） + `[已選取 N 筆 ×]` chip
- [ ] 點 chip 內 `×` 取消選取；**不**提供獨立「取消選取」文字按鈕
- [ ] **無**批次提交 / 批次作廢 / 批次匯出 / 批次啟用 / 批次停用
- [ ] Search 第一個 `<option>` 為 `value=""` 標籤「全部」（規則同作業檔）
- [ ] 狀態 filter 只列 啟用 / 停用（搜尋區可省略此 filter，由前端切換管道處理）
- [ ] Grid 欄位順序:`checkbox(sticky-left) → 主欄（code/name 連結樣式, sticky-left）→ 一般欄 → 狀態(st-chip, display-only) → actions(sticky-right)`（**詳見 §DataGrid 結構與互動**）
- [ ] 「一般欄」的語意排序遵守 **識別 → 分類 → 歸屬 → 業務屬性 → 狀態**（如：完整路徑 → 類型 → 所屬倉庫/館別/區域 → 部門 → 公司別 → active）；違反此序視同欄位排序錯誤
- [ ] 操作欄: `[編輯]` + `[刪除]`（兩個 icon button；**非** `[檢視]`）；唯讀模式切 chevron `[檢視]`（**詳見 §DataGrid → 唯讀模式**）
- [ ] `canDelete === false` 時 `[刪除]` **不渲染**（**禁**用 disabled），操作欄套 `.col-actions--single` 收窄寬度至 56px（從預設 96px）
- [ ] 列尾 `[刪除]`（`.ico-btn.is-delete`）與批次列 `[🗑]`（`.btn-icon--danger-square`）符合 §設定檔刪除機制（必依）

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

> 通用結構 / RWD / A11y / dirty-guard / 鍵盤快捷鍵見 §Form Footer 結構與互動（共通）。本表只列**設定檔右側按鈕內容覆寫項**。

| 區塊 | 內容 | 說明 |
|---|---|---|
| 左群（rec-nav） | `[‹] {recordIndex}/{recordTotal} [›]` | 上下筆切換；`route.id === 'new'` 時整組停用（行為同共通章節 §1 記錄分頁器） |
| 右群 | `[刪除]`（danger outline） | 受 `canDelete` 控制（manager 角色才可用）；點擊 → 阻擋（如 `quant_count > 0`）走 error toast；無阻擋 → confirm modal（danger）→ success toast + 返回列表 |
|  | `[更多操作 ▾]`（primary outline） | 下拉：`儲存後新增`、`複製`（互動同共通章節 §4 更多操作 ▾） |
|  | `[儲存變更]`（primary fill） | 受 `canSave` 控制；唯讀視角改顯示 `👁 唯讀檢視` 標籤（行為同共通章節 §2 主 CTA） |

> **無**「提交 / 核准 / 解核 / 作廢」；**無**「回列表」按鈕（離開靠麵包屑或 `isDirty` 攔截 modal）。

### 設定檔 Modal / Toast 特有場景

| 場景 | 元件 | 樣式 | 文案範例 |
|---|---|---|---|
| 離開未儲存表單 | confirm | warning | 您有尚未儲存的變更。是否放棄並返回列表？ |
| 單筆刪除（可刪） | confirm | danger | 確定刪除「{name}」？此操作無法復原。 |
| 批次刪除（全部可刪） | confirm | danger | 確定刪除已選取的 {n} 筆資料？ |
| 單筆改停用（已被引用） | confirm | warning | 此筆已被 {n} 筆資料引用，無法刪除。是否改為停用？ |
| 混合批次（部分需停用） | confirm | warning | 已選取 {n} 筆，其中 {a} 筆可刪除、{b} 筆需改為停用，是否繼續？ |
| 切換 related 來源（如 `warehouse_id` 連動 `branch_id`） | confirm | info | 此變更將連動更新「館別」與「區域」欄位。 |
| 複製成功 | toast | success | 已複製，請編輯副本 |
| 重置篩選 | toast | info | 已重置篩選 |
| 刪除 / 停用失敗 | toast | error | 刪除失敗：{reason}（不關 modal，讓使用者重試） |
| 批次部分失敗 | toast | warning | 成功 {n} 筆，失敗 {m} 筆（失敗者保持選取狀態） |

> 完整刪除機制（按鈕位置、判斷邏輯、視覺、鍵盤）見下方 §設定檔刪除機制（必依）。**禁**直接用 error toast 阻擋刪除——已被引用的項目走 warning modal 提供「改為停用」選項。

---

### 設定檔刪除機制（必依）

設定檔混合「實體刪除 vs 軟刪除（停用）」行為，且入口分散在 List 批次列 / List 列尾 / Form 底部三處。本節是設定檔必依結構，沿用 §設定檔資料狀態矩陣 的 `canDelete` 旗標。

#### 刪除按鈕的三個出現位置

| 位置 | 觸發條件 | 元件樣式 | 行為 |
|---|---|---|---|
| List · 批次列 | `selectedRows.length > 0 && canDelete` | `.btn-icon--danger-square`（40×40、error 邊框 + icon、背景 surface-default） | 對選取的多筆執行刪除 / 停用判斷 |
| List · 列尾 | 該列 `canDelete` | `.ico-btn.is-delete`（40×40、純 icon、error 色） | 對該單筆執行刪除 / 停用判斷 |
| Form · 底部 | `!isNew && canDelete && form.active` | `.btn--outline-danger`（40px、紅色外框文字按鈕、背景透明） | 對當前單筆執行刪除 / 停用判斷 |

**不出現的情境**：新增中（`isNew`）、無刪除權限（`!canDelete`）、本筆已停用（`!form.active`，停用 = 軟刪除目的已達成）。

> `canDelete === false` 時整顆按鈕**不渲染**（**禁**用 disabled 表達無權限）；同時影響 List 操作欄寬度——搭配 `.col-actions--single` 收窄至 56px（見 §List View 七項自檢（設定檔版））。

#### 刪除 vs 停用的判斷邏輯（最重要）

```
if (row.usage_count > 0)  →  禁止實體刪除，改走「停用」流程
else                       →  允許實體刪除
```

- **同一顆刪除按鈕**承擔兩種行為，由系統依資料引用狀態切換，使用者只看到一個入口
- 按鈕 `aria-label` / `title` 必須對應實際行為：
  - 可刪除：`aria-label="delete"`、`title="刪除"`
  - 須停用：`aria-label="deactivate"`、`title="已被 N 筆引用，須改為停用"`
- 視覺**不改變**（仍紅色 icon），改變的是 hover tooltip 與後續 modal 內容

#### 確認 Modal 規則

所有刪除操作 **MUST** 經 modal 二次確認，**不可直接動作**。完整情境與文案見上方 §設定檔 Modal / Toast 特有場景；額外行為規則：

- 取消按鈕一律 `btn--outline`「取消」，靠右側次位
- **主按鈕焦點預設在「取消」上**（避免 Enter 誤觸刪除）
- Modal icon 對應 `kind`：danger → `delete`、warning → `warning`

#### 視覺樣式規則

```css
/* 列尾刪除 icon */
.ico-btn.is-delete             { color: rgb(var(--color-sf-error)); }
.ico-btn.is-delete:hover       { background: rgba(var(--color-sf-error), .08); }

/* 批次刪除（icon-square） */
.btn-icon--danger-square       { background: var(--bg-surface-default); color: rgb(var(--color-sf-error)); border: 1px solid rgb(var(--color-sf-error)); border-radius: var(--radius-sm); }
.btn-icon--danger-square:hover { background: rgba(var(--color-sf-error), .08); }

/* Form 底部刪除文字按鈕 */
.btn--outline-danger           { background: transparent; color: rgb(var(--color-sf-error)); border: 1px solid rgb(var(--color-sf-error)); }
.btn--outline-danger:hover     { background: rgba(var(--color-sf-error), .08); }
.btn--outline-danger:active    { background: rgba(var(--color-sf-error), .12); }
```

**統一原則（合稱「紅框白底紅字」家族）**：

- 預設背景透明 / 白底、僅 error 色描邊或 icon、文字 error 色（= **紅框白底紅字**）
- Hover 統一加 `error @ 8%` 底色，**不**改 icon / 文字顏色
- Active 加深至 `error @ 12%`
- **MUST NOT** 使用實心 `.btn--danger`（紅底白字）於 List / Form；實心紅色僅出現於 Modal 主按鈕

#### 結果反饋

| 結果 | 反饋 | 後續動作 |
|---|---|---|
| 刪除成功 | `toast--success`「已刪除」 | List：列消失 / Form：返回 List |
| 停用成功 | `toast--success`「已改為停用」 | 該列 `st-chip` 切為 `st-chip--inactive` |
| 失敗 | `toast--error`「刪除失敗：{reason}」 | **不關 modal**，讓使用者重試 / 取消 |
| 批次部分失敗 | `toast--warning`「成功 {n} 筆，失敗 {m} 筆」 | 失敗者保持選取狀態 |

#### 鍵盤與快捷鍵

- **Delete / Backspace** 鍵：**MUST NOT** 綁定為刪除快捷鍵（避免誤刪）
- Modal 開啟時：`Esc` = 取消、`Enter` = **取消**（非確認，因主按鈕焦點在取消上）
- Tab order：取消按鈕在主按鈕之前

#### 權限與審計

- `canDelete` 為布林權限旗標，影響**所有**刪除按鈕的可見性（**不是 disabled，是不渲染**）
- 已作廢 / 已核准的資料 **MUST NOT** 顯示刪除按鈕（用作廢 / 反核准取代）
- 每次刪除 / 停用 **MUST** 寫入 audit log（誰、何時、哪筆、行為）

---

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
- [ ] 刪除機制符合 §設定檔刪除機制（必依）：三個按鈕位置與樣式皆為「紅框白底紅字」家族（`.ico-btn.is-delete` / `.btn-icon--danger-square` / `.btn--outline-danger`）、刪除 vs 停用 by `usage_count`、modal `kind` 對應（可刪 = danger / 已被引用 = warning「改為停用」/ 混合批次 = warning）、實心紅底 `.btn--danger`**不**出現於 List/Form、modal 主按鈕焦點預設在「取消」、`canDelete === false` 時 `.col-actions--single` 收窄

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

- [ ] App Shell 結構符合 `Shared.md §頁面框架`（56px header / 72px nav-rail / 28px info bar）；class 沿用 `.erp-*`
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
- [ ] **頁面層級無第二條捲軸**（`html` / `body` / `#app` / `.shell` 全部 `100vh + overflow hidden`；捲軸只在 `.grid-wrap` 與 `.form-view__body` 內，詳 §頁面捲動範圍）
- [ ] **List 搜尋區** 欄位 > 5 或容器窄時能正確換行；操作按鈕固定右上不被擠下；收合狀態下隱藏 label 並依容器寬即時計算可容納欄位數（詳 §List 搜尋區結構與互動）
- [ ] **DataGrid** 列本身不可點；唯讀模式操作欄切 chevron 而**非隱藏入口**；sticky 欄明確指定背景；斑馬紋偶列用 `var(--bg-surface-variant)`（詳 §DataGrid 結構與互動）
- [ ] **Form Footer** 在 `< 768px` 縱向堆疊、CTA 全寬；`Ctrl/Cmd + S` 在表單聚焦時可儲存；dirty 切筆 / sidebar / `beforeunload` 都會攔截確認（詳 §Form Footer 結構與互動）
- [ ] **Form Group** 跨欄 `--span-2 / --span-3 / --full` 在 1024 斷點下正確降欄；`.field-phone` 在父 grid 縮到 1 欄時仍維持並排（詳 §Form Group 分群與 form-grid RWD）

---

## 關聯規範（ERP repo 內專屬）

- 對應 ERP 內部規範: `.claude/rules/prototype-design/PRODUCE.md`、`.claude/rules/prototype-design/CLAUDE.md`
- token 與元件對照: `.claude/rules/figma-design-system/references/tokens.md`、`components.md`
- Syncfusion playground（production 升級時對照）: `/Users/athena/working/ddd/syncfusion-playground/app/pages/playground/`
