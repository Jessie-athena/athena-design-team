# Prototyper REFERENCE

> SKILL.md 的詳細展開：審查條款（§4-§12）、Token 對照、元件對照、Tweaks Panel 規格、`app.js` 起手式。
> 來源 single source of truth：ERP repo `.claude/rules/prototype-design/CLAUDE.md` + `PRODUCE.md`。

---

## §1 Source of Truth

- **Prototype = 真相來源**；`docs/notion/.../*.md` 與 production code 都對齊到 prototype
- 文件變更不回拋 prototype；prototype 修正後須重新 export

## §2 Tech Baseline（重申硬性限制）

- `<html lang="zh-Hant-TW">`
- 載入順序：`ds/colors_and_type.css` → Material Symbols → `app.css`
- Vue 3 production CDN，禁引入其他 UI library
- 樣式寫到 `app.css`、互動寫到 `app.js`，禁在 `.html` 內嵌
- Icon 一律 Material Symbols Outlined，禁其他 icon 套件

## §3 File Layout

- 每模組一個 `.html`，檔名 = 模組中文名（與 `docs/notion/.../*.md` 同名）
- 共用資源放 `ds/`，**勿改**；新增 token 須先回上游 design system

---

## §4 Required App Shell

- **erp-header**：home button + breadcrumb（模組分類 / 功能名 / 單號）+ favorite + 通知 / 設定 / avatar
- **nav-rail**：5 項固定順序 — 我的最愛、財務、進銷存、人事、設定檔
- **erp-footer**：左 programID（如 `PSI-SO`）、右版號（格式 `vX.Y.Z.A.B`）

---

## §5 List View 七項自檢

- [ ] Toolbar：`selectedRows.length === 0` 時顯示主操作；> 0 時切換為批次操作
- [ ] Search 第一個 `<option>` 為 `value=""` 標籤「全部」
- [ ] Grid 欄位順序：`checkbox(sticky-left) → PK(sticky-left) → 一般欄 → 金額(text-right) → 狀態 → actions(sticky-right)`
- [ ] 金額欄 class 加 `text-right`
- [ ] 空狀態套用 `inbox` icon
- [ ] `tfoot` 合計列只在 `rows.length > 0` 時顯示
- [ ] Pager 三段：page size（20 / 50 / 100）/ 範圍與總數 / 上下頁

---

## §6 Form View 七項自檢

- [ ] Summary card 含關鍵指標 + stepper；`form.status === 'voided'` 改顯示 pill（不顯示 stepper）
- [ ] Section 用 `bar + title + form-grid--4`；響應式靠 §10 斷點，**禁**手動隱藏欄位
- [ ] 必填欄位 label 加 `<span class="required">*</span>`
- [ ] Smart Bar：`form.relations.length > 0` 才渲染，無關聯時整段不顯示
- [ ] Tab block：表頭右側固定 add 按鈕；行內編輯模式有 save / cancel
- [ ] Footer 三段：上下筆 / 動作群 / 「更多操作」下拉
- [ ] `form.moveId` 存在時顯示「已產生傳票」chip（**禁**當成獨立狀態加進 stepper）

---

## §7 State Machine

| State | label | 流向 |
|---|---|---|
| `draft` | 草稿 | → `submitted` (via `action_submit`) |
| `submitted` | 已提交 | → `approved` (via `action_approve`) / 退回 `draft` |
| `approved` | 已核准 | → `submitted` (via `action_unapprove`) |
| `voided` | 已作廢 | 獨立分支 (via `action_void`) |

- 「已產生傳票」**不是獨立狀態**，由 `move_id` 是否存在判斷，顯示為 `chip--success`
- 偏離此命名（如多個審核層級、加了 `cancelled`）須在 chat handoff 註明理由

---

## §8 Tweaks Panel（必備）

右下浮動 FAB；展開後**至少**含三組 radio：

| fieldset | 必含選項 |
|---|---|
| 使用者角色 | 一般使用者 / 主管 / 系統管理員 |
| 單據狀態 | 草稿 / 已提交 / 已核准 / 已作廢 |
| 關鍵 flag | 模組相關，如「是否已產生傳票」、「跨公司」、「外幣」 |

`tweaks.*` 變動須持久化到 `localStorage`（在 `app.js` 用 `watch` 寫入 / 啟動時讀取）。

用途：reviewer 不寫程式即可驗證所有狀態分支與權限差異。

---

## §9 Modal / Toast / Empty State

- **Modal kinds**：
  - `confirm`（確認操作，如「確認作廢？」）
  - `deeplink`（跳轉提示，如「跳轉至已核准的傳票」）
  - **`pick` 已淘汰**：改用 domain 篩選自動帶入，不再做 picker 彈窗
- **Toast**：`success / warning / error` 三型，3 秒自動消失（`setTimeout` 實作）
- **Empty State**：統一 `inbox` icon + 主訊息

---

## §10 Responsive

- 預設桌面寬度（≥ 1280px）；**不做** mobile（< 768px）版本，PM 明確要求才例外
- 唯一斷點：`@media (max-width: 1024px)` → 4 欄 grid 降為 2 欄
- 小螢幕一律以橫向 scroll 處理，**禁**隱藏關鍵欄位

---

## §11 Tokens

色彩 / 間距 / 圓角 / 陰影 / 字級必須使用 `ds/colors_and_type.css` 的 token，禁 inline hex、禁 `@apply`、禁新增 token 而不更新上游。

### 色彩語義別名（最常用）

| Token | 用途 |
|---|---|
| `--surface-default` | 頁面背景 |
| `--surface-raised` | Card 背景 |
| `--surface-overlay` | Dialog 背景 |
| `--text-primary` / `--text-secondary` / `--text-disabled` | 文字 |
| `--border-default` / `--border-focus` | 邊框 |
| `--status-success` / `--status-warning` / `--status-error` | 狀態色 |

### 間距（4–48px）

`--spacing-1`(4) / `-2`(8) / `-3`(12) / `-4`(16) / `-5`(20) / `-6`(24) / `-8`(32) / `-12`(48)

### 圓角

`--radius-none` / `-sm`(2) / `-md`(4) / `-lg`(8) / `-xl`(12) / `-full`

### 陰影層級

`--shadow-none` / `-xs` / `-sm`(card hover) / `-md`(dropdown) / `-lg`(dialog) / `-xl`(modal) / `-2xl`(overlay) / `-inner` / `-focus`

### 字型樣式

| 用途 | 樣式 |
|---|---|
| Page title | `display/lg`（32px / 700 / 1.2） |
| Section heading | `heading/sm`（16px / 600 / 1.4） |
| Body | `body/md`（14px / 400 / 1.6） |
| Label | `label/md`（14px / 500 / 1.4） |

> 完整對照：ERP repo `.claude/rules/figma-design-system/references/tokens.md`

---

## §12 Handoff Checklist（必過才能交付）

匯出 handoff bundle 前**逐項打勾**：

- [ ] 三件套 shell 齊全、breadcrumb 層級正確
- [ ] List View 與 Form View 兩種視圖都可切換
- [ ] §7 所有狀態（含 voided）能透過 Tweaks 切換驗證
- [ ] 必填欄位有 `*`、唯讀 / 編輯切換正確
- [ ] 空狀態、刪除確認、儲存 toast 均能觸發
- [ ] 無 `@apply`、無 inline hex、無 TypeScript
- [ ] chat 內必含五項：對應 `docs/notion/` 路徑、目標 Odoo model、相比上版差異、對齊方向、特別注意項
- [ ] 開啟欲實作的 `.html` 後再 export（會標為 primary）

---

## §13 元件對照（HTML prototype 階段不引外部 lib）

Prototype 階段所有元件用 **原生 HTML + Tailwind class + Vue directive** 寫成（template 已在 `templates/module-page.html` 內示範）。
**production 升級**才把這些對應到 Syncfusion Vue 或 `Ds*` 客製元件。

| Prototype HTML | Production 對應 |
|---|---|
| `<input>` / `<select>` | `<ejs-textbox>` / `<ejs-dropdownlist>` |
| 自製 `<table class="grid">` | `<ejs-grid>` |
| `<dialog>` 自製 | `<ejs-dialog>` |
| `<button class="btn btn--primary">` | `<ejs-button isPrimary>` |
| `<span class="status-pill">` | `<DsStatusBadge>` |
| `<aside class="nav-rail">` | `<DsSideNavMenu>` |

---

## §14 `app.js` 起手式（reactive state + handlers）

> 共用 `app.js`，每個模組各自 mount。完整版見 ERP repo `.claude/rules/prototype-design/PRODUCE.md` 附錄 B。

```js
const { createApp, ref, reactive, computed, watch, onMounted } = Vue

createApp({
  setup() {
    // ===== Shell =====
    const breadcrumb = reactive({ module: '財務', feature: '模組中文名', docNo: '' })
    const programId = 'PSI-XX'
    const version = 'v1.0.0.0.0'
    const navItems = [
      { key: 'fav',     icon: 'star',            label: '我的最愛' },
      { key: 'finance', icon: 'account_balance', label: '財務' },
      { key: 'psi',     icon: 'inventory',       label: '進銷存' },
      { key: 'hr',      icon: 'badge',           label: '人事' },
      { key: 'config',  icon: 'settings',        label: '設定檔' },
    ]
    const activeNav = ref('finance')

    // ===== View =====
    const view = ref('list')   // 'list' | 'form'

    // ===== Tweaks（持久化 localStorage） =====
    const tweaks = reactive(JSON.parse(localStorage.getItem('tweaks') || '{}'))
    if (!tweaks.role)     tweaks.role     = 'user'
    if (!tweaks.docState) tweaks.docState = 'draft'
    if (!tweaks.flag)     tweaks.flag     = 'no-move'
    watch(tweaks, (v) => localStorage.setItem('tweaks', JSON.stringify(v)), { deep: true })

    // ===== Toast =====
    const toasts = ref([])
    const showToast = (kind, message) => {
      const id = Date.now() + Math.random()
      toasts.value.push({ id, kind, message })
      setTimeout(() => { toasts.value = toasts.value.filter(t => t.id !== id) }, 3000)
    }

    // ===== State machine handlers =====
    const onSubmit    = () => { form.status = 'submitted'; showToast('success', '已提交') }
    const onApprove   = () => { form.status = 'approved';  showToast('success', '已核准') }
    const onUnapprove = () => { form.status = 'submitted'; showToast('warning', '已解核') }
    const onVoid      = () => { form.status = 'voided';    showToast('success', '已作廢') }

    return { breadcrumb, programId, version, navItems, activeNav, view, tweaks, toasts,
             onSubmit, onApprove, onUnapprove, onVoid }
  }
}).mount('#app')
```

---

## §15 `app.css` 結構建議（章節順序）

```
/* 1. Reset / base                   */
/* 2. App Shell                      */
/*    .erp-header / .nav-rail        */
/* 3. List View                      */
/*    .toolbar / .search-bar / .grid */
/*    .pager / .empty-state          */
/* 4. Form View                      */
/*    .summary-card / .stepper       */
/*    .smart-bar                     */
/*    .form-section / .form-grid     */
/*    .form-tabs / .form-footer      */
/* 5. Tweaks Panel                   */
/* 6. Overlay (Modal / Toast)        */
/* 7. Atoms (.btn / .chip / .pill)   */
/* 8. Responsive @1024px             */
```

所有色彩 / 間距 / 陰影 / 字級必須引用 `ds/colors_and_type.css` 已定義的 CSS variables，**禁** hex、**禁** `@apply`。
