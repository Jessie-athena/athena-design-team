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

> 結構基底（Header / Nav-rail / Info Bar 尺寸、layout、icon variant、hover/active 樣式）見 `Shared.md §頁面框架`。本節只列 ERP 專屬覆寫。

### Breadcrumb 層級

三層固定順序：**模組分類** > **功能名稱** > **單號**（分隔符 `>`，**不**用 `/`）

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

## 頁面捲動範圍
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

## Summary Bar 結構
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

## Stepper 三狀態結構
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

## Smart Bar `card-btn` 結構
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

`v-if="visibleRelations.length > 0"`。`visibleRelations` 邏輯：「**count > 0 的條目**」**或**「**count == null 的常駐條目**」任一存在即渲染；全部 `count === 0` 時整段不渲染。

> JS computed 實作見 `REFERENCE.md §14`（`app.js` 起手式）的 `visibleRelations` helper。

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

## 輸入欄樣式

- **預設 Shape：Filled**（class `.input.filled`） — Material 3 風格：surface-variant 底色 + 底部 underline + 上方圓角；`.outlined` 只在特定需要 borders-on-all-sides 的情境用
- **作業檔與設定檔皆預設 Filled**
- **Read Only ≠ Disabled**：
  - read-only：用 `readonly` 屬性 — 背景透明、底線 `1px solid var(--border-default)`、文字 `var(--text-primary)`、`cursor: default`、**不觸發** focus 樣式
  - disabled：用 `disabled` 屬性 — 同 readonly + select caret 隱藏 + `cursor: not-allowed` + `pointer-events: none`
  - **禁**用 `disabled` 屬性表達 read-only（會誤導使用者以為欄位不可用）
- DynamicForm（動態表單）：外層**無 border、padding 0**；外距由父層 `.form-section` 控制
- `DsSectionHeader`：下方 padding 16px（與下方 form-grid 之間）
- **詳細狀態視覺**（Default / Hover / Focus / Filled / Error / Readonly / Disabled）見 `profiles/erp/FormGroup.md → .input.filled 各狀態`

### Filled 詳細視覺規格（Figma source of truth）

> 以下為 Figma DS 中 `.input.filled` 的實際 CSS 值，是 token 在 `prototype/ds/colors_and_type.css` 中應對應的具象值。**禁** AI 反射用 outlined 風格（白底 + 1px solid `#D5D8DC` 是錯的）。

**外層 wrapper（一個 form-field 整體）**

| 屬性 | 值 |
|---|---|
| display | `flex; flex-direction: column` |
| gap（label → input → help） | `4px` |
| min-width | `300px` |
| 高度（含 label） | 自動（label 18 + gap 4 + input 40 = 62px；help 在 input 下方再 +4 + 16） |

**Label（label 元素）**

| 屬性 | 值 |
|---|---|
| font-family | `Roboto` |
| font-weight | `400`（Regular） |
| font-size | `14px` |
| line-height | `130%`（18px） |
| color | `#3C4A5B`（content-text-color-alt1） |

**必填星號（`<span class="required">*</span>`）**

| 屬性 | 值 |
|---|---|
| font-size | `12px` Roboto Regular |
| line-height | `130%`（16px） |
| letter-spacing | `0.1px` |
| color | `#F4493E`（danger） |
| 寬高 | `6×16px` |

**Input container（filled 的填色矩形本體）**

| 屬性 | 值 |
|---|---|
| height | `40px`（form 主表單） |
| background | `#EDF0F7`（Material 3 surface-variant） |
| border-bottom | `1px solid #7F8996`（border 色） |
| border-radius | `4px 4px 0 0`（**僅上方圓角**；Material Filled 簽名特徵） |
| padding | `0 0 0 10px`（左內距 10px；右側若有 action icon 則不留 padding） |
| display | `flex; align-items: center; justify-content: space-between` |
| 內容 gap | `6px`（文字與右側 icon 之間） |

**輸入文字（input value / placeholder）**

| 屬性 | 值 |
|---|---|
| font-family | `Roboto` |
| font-weight | `400`（Regular） |
| font-size | `14px` |
| line-height | `150%`（21px） |
| letter-spacing | `0.24px` |
| placeholder color | `#67717E`（placeholder-text-color） |
| content color | Material 3 primary text color（深於 placeholder） |

**右側 action icons（如 clear / search / 自訂）**

- 每個 icon 容器 `32×32`
- 無 action 時整段 `display: none`（**不**留空白槽）

**輔助／錯誤訊息（`.help` / `.help.is-error`）**

- 與 input 之間 `gap: 4px`
- `.help { min-height: 16px }`（永遠保留佔位，避免錯誤訊息出現時欄位位移）
- 錯誤訊息顏色同 error border（`#F4493E` / `--color-sf-error`）

> **狀態完整視覺（Default / Hover / Focus / Filled / Error / Readonly / Disabled）** 見 `profiles/erp/FormGroup.md → .input.filled 各狀態`。Focus 時 underline 加粗為 `2px solid` primary 色，**不**加 outline ring；Error 時 underline 變 `2px solid` danger 色。

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

對應 `REFERENCE.md §5 階段 1` 的三段式流程：Pass 0 找權威來源、Pass 1 抽 schema、Pass 2 對表轉實作。本節是 Pass 0 找不到 / 模組無設計文件時的 fallback 對照。

### 權威來源優先順序

衝突時上層贏（呼應 `SKILL.md §1 資料來源權重`）：

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
| TextBox / 文字 | 單行文字、單號、名稱 | `<input type="text">` + `.input.filled` | `<TextInput>` | `<ejs-textbox>` |
| NumericTextBox / 數值 / Monetary | 金額、數量、含千分位 | `<input>` + `.text-right` | `<NumericInput>` | `<ejs-numerictextbox>` |
| TextArea / Text Area / textarea / 多行 | 備註、長說明 | `<textarea>` + `.input.filled` | `<TextareaInput>` | `<ejs-textarea>` |
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

## 模組元件規格索引

下列元件規格已獨立成檔，避免單檔過長造成漏讀。**載入 erp profile 時這些子檔不會自動載入**；實作或審查特定元件時才依需求讀取。

| 元件 | 規格檔 |
|---|---|
| List 搜尋區（toolbar / search bar / RWD） | `profiles/erp/ListSearch.md` |
| DataGrid（含行內編輯互動） | `profiles/erp/DataGrid.md` |
| Form Group / form-grid RWD | `profiles/erp/FormGroup.md` |
| Form Footer（記錄分頁器 / 主 CTA / 更多操作） | `profiles/erp/FormFooter.md` |

> 撰寫 chat handoff 時若 prototype 命中其中任一元件，請在「對齊方向」段附上對應子檔路徑，方便 reviewer 直接跳到規格。

---

## List View 七項自檢

- [ ] Toolbar: `selectedRows.length === 0` 時顯示主操作；> 0 時切換為批次操作
- [ ] Search 第一個 `<option>` 為 `value=""` 標籤「全部」（**詳見 `profiles/erp/ListSearch.md`**）
- [ ] Grid 欄位順序: `checkbox(sticky-left) → PK(sticky-left) → 一般欄 → 金額(text-right) → 狀態 → actions(sticky-right)`（**詳見 `profiles/erp/DataGrid.md`**）
- [ ] 金額欄 class 加 `text-right`
- [ ] 空狀態套用 `inbox` icon（**詳見 `profiles/erp/DataGrid.md` → 空狀態**）
- [ ] `tfoot` 合計列只在 `rows.length > 0` 時顯示
- [ ] Pager 三段: page size（20 / 50 / 100）/ 範圍與總數 / 上下頁

---

## Form View 七項自檢

- [ ] Summary bar 為 `sticky` + 上下兩塊（標題/stepper / 指標）+ 無 shadow + padding 24px（詳 §Summary Bar 結構）；`form.status === 'voided'` 改顯示 pill（不顯示 stepper）
- [ ] Stepper 三狀態結構正確（`--active` / `--done` / pending + `.stepper__bar`），詳 §Stepper 三狀態結構
- [ ] Section 用 `bar + title + form-grid--4`；DynamicForm 外層無 border、padding 0（詳 §輸入欄樣式 + `profiles/erp/FormGroup.md`）
- [ ] 必填欄位 label 加 `<span class="required">*</span>`；read-only 用 `readonly` 屬性，**禁**用 `disabled`
- [ ] Smart Bar 用 `card-btn` 結構（無 link icon、count + 單位 + 標題 + `arrow_outward`），詳 §Smart Bar `card-btn` 結構；無關聯時整段不渲染
- [ ] Tab block: 表頭右側固定 add 按鈕（按鈕**無 add icon**，詳 §按鈕 icon 政策）；行內編輯模式有 save / cancel
- [ ] Footer 三段: 上下筆 / 動作群（按鈕**分層級配色 + 無 icon**） / 「更多操作」下拉（**詳 `profiles/erp/FormFooter.md`**）
- [ ] `form.moveId` 存在時顯示「已產生傳票」chip（**禁**當成獨立狀態加進 stepper）

---

## 設定檔（Master Data）特化規則

> 「設定檔」(master data) 的 UI 慣例與「作業檔」(transaction documents) 不同。判斷類型後，**以下章節 override 前述作業檔慣例**：
> - State Machine、Summary Bar (stepper)、Smart Bar、Form Footer 動作群 — **全部不適用**
> - List View 批次操作、Form View 章節結構、Footer — **依設定檔規範重寫**
> - Modal / Toast / Empty State、輸入欄樣式、按鈕 icon 政策 — **沿用本檔前述章節**
> - App Shell — 沿用 `Shared.md §頁面框架` + 本檔 §App Shell 規範

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

### → 詳細設定檔規範

判定為設定檔後，載入 **`${CLAUDE_SKILL_DIR}/profiles/erp-setup.md`** 取得完整規範：

- 設定檔側欄
- List View 七項自檢（設定檔版）
- Form View 七項自檢（設定檔版）
- 設定檔資料狀態矩陣（`.is-archived-view` / `.is-readonly-view` / `.is-keep-editable`）
- Form Footer（設定檔版）
- 設定檔 Modal / Toast 特有場景
- 設定檔刪除機制（「紅框白底紅字」家族視覺規格）
- 設定檔 Handoff Checklist

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

## ERP Handoff Checklist（疊加 `SKILL.md §5 輸出前 Checklist`）

通用清單通過後再逐項打勾:

- [ ] App Shell 結構符合 `Shared.md §頁面框架`（56px header / 72px nav-rail / 28px info bar）；class 沿用 `.erp-*`
- [ ] breadcrumb 三層正確（模組分類 > 功能名 > 單號；分隔符 `>`）
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
- [ ] **List 搜尋區** 欄位 > 5 或容器窄時能正確換行；操作按鈕固定右上不被擠下；收合狀態下隱藏 label 並依容器寬即時計算可容納欄位數（詳 `profiles/erp/ListSearch.md`）
- [ ] **DataGrid** 列本身不可點；唯讀模式操作欄切 chevron 而**非隱藏入口**；sticky 欄明確指定背景；斑馬紋偶列用 `var(--bg-surface-variant)`（詳 `profiles/erp/DataGrid.md`）
- [ ] **Form Footer** 在 `< 768px` 縱向堆疊、CTA 全寬；`Ctrl/Cmd + S` 在表單聚焦時可儲存；dirty 切筆 / sidebar / `beforeunload` 都會攔截確認（詳 `profiles/erp/FormFooter.md`）
- [ ] **Form Group** 跨欄 `--span-2 / --span-3 / --full` 在 1024 斷點下正確降欄；`.field-phone` 在父 grid 縮到 1 欄時仍維持並排（詳 `profiles/erp/FormGroup.md`）

---

## 關聯規範（ERP repo 內專屬）

- 對應 ERP 內部規範: `.claude/rules/prototype-design/PRODUCE.md`、`.claude/rules/prototype-design/CLAUDE.md`
- token 與元件對照: `.claude/rules/figma-design-system/references/tokens.md`、`components.md`
- Syncfusion playground（production 升級時對照）: `/Users/athena/working/ddd/syncfusion-playground/app/pages/playground/`
