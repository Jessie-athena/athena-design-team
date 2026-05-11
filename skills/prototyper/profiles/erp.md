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

## 規格抽取表（PM 文件 → prototype）

從 `docs/notion/*.md` 依序抽 7 項；缺漏即在 chat 詢問:

| 抽取項 | 用於 |
|---|---|
| 模組中文名 | 檔名、`<title>`、breadcrumb |
| 模組分類 | breadcrumb 第一段、nav-rail 高亮 |
| Odoo model | programID 衍生、chat handoff 必附 |
| 欄位表（label / 型別 / 必填） | Form View form-grid cells |
| 狀態流程圖 / 動作清單 | stepper、footer 動作按鈕、tweaks `docState` |
| 關聯單據 | Smart Bar（無關聯則整段移除） |
| List 預設搜尋條件 | search bar `<option>` |

## 檔案路徑

- **starter template**: `templates/module-page.html`（已 ERP-shaped）
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
- padding：上下各 `24px`（`--spacing-6`），左右隨 main panel
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
| Pending | （無 modifier） | bg `rgba(var(--color-sf-primary), .08)` on `var(--surface-default)`、border `1px solid var(--border-default)`、數字 `var(--text-primary)` 14px/400 | `var(--text-secondary)` 14px/400 |
| Active | `--active` | bg `rgb(var(--color-sf-primary))`、border `1px solid rgb(var(--color-sf-primary))`、`box-shadow: inset 0 0 0 1px #fff`（內白環）、數字 `#fff` | `var(--text-primary)` |
| Done | `--done` | bg `rgb(var(--color-sf-success))`、border `1px solid rgb(var(--color-sf-success))`、無 shadow、改顯示 check icon（Material Symbols, 20px, wght 600, `#fff`） | `var(--text-disabled)` |

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
| `.smart-bar` | `display: inline-flex; gap: 12px; padding: 8px 4px 8px 16px; background: var(--surface-default); border: 1px solid rgb(var(--color-sf-primary)); border-radius: 8px; width: fit-content;` |
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

## Tweaks Panel（必備）

右下浮動 FAB；展開後**至少**含三組 radio:

| fieldset | 必含選項 |
|---|---|
| 使用者角色 | 一般使用者 / 主管 / 系統管理員 |
| 單據狀態 | 草稿 / 已提交 / 已核准 / 已作廢 |
| 關鍵 flag | 模組相關，如「是否已產生傳票」、「跨公司」、「外幣」 |

`tweaks.*` 變動須持久化到 `localStorage`（在 `app.js` 用 `watch` 寫入 / 啟動時讀取）。

用途: reviewer 不寫程式即可驗證所有狀態分支與權限差異。

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
- [ ] State machine 4 種狀態（含 voided）能透過 Tweaks 切換驗證
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
