# Prototyper REFERENCE（通用技術細節）

> `SKILL.md` 的詳細展開:技術 baseline、token 對照、prototype → production 元件對照、`app.js` / `app.css` 起手式。
>
> **此檔僅含跨專案通用內容**。專案專屬的 App Shell / state machine / list view / form view / handoff 規則見 `profiles/<project>.md`（如 `profiles/erp.md`）。

---

## §1 Source of Truth

- **Prototype = 真相來源**；規格文件與 production code 都對齊到 prototype
- 文件變更不回拋 prototype；prototype 修正後須重新 export

## §2 Tech Baseline（重申硬性限制）

- `<html lang="zh-Hant-TW">`（多語環境由 profile 指定）
- 載入順序:design tokens CSS → Material Symbols → `app.css`
- Vue 3 production CDN，禁引入其他 UI library
- 樣式寫到 `app.css`、互動寫到 `app.js`，禁在 `.html` 內嵌
- Icon 一律 Material Symbols Outlined，禁其他 icon 套件

## §3 File Layout

- 每模組一個 `.html`，檔名 = 模組中文名（與規格文件同名）
- 共用資源放 `ds/` 或 design system 統一目錄，**勿改**；新增 token 須先回上游 design system

---

## §10 Responsive

- 預設桌面寬度（≥ 1280px）；**不做** mobile（< 768px）版本，PM 明確要求才例外
- 唯一斷點:`@media (max-width: 1024px)` → 4 欄 grid 降為 2 欄
- 小螢幕一律以橫向 scroll 處理，**禁**隱藏關鍵欄位

---

## §11 Tokens

色彩 / 間距 / 圓角 / 陰影 / 字級必須使用 design tokens CSS 的 token，禁 inline hex、禁 `@apply`、禁新增 token 而不更新上游。

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

> ERP 完整對照:`.claude/rules/figma-design-system/references/tokens.md`

---

## §13 元件對照（HTML prototype 階段不引外部 lib）

Prototype 階段所有元件用 **原生 HTML + Tailwind class + Vue directive** 寫成（template 已在 `templates/module-page.html` 內示範）。
**production 升級**才把這些對應到對應專案的元件庫（如 ERP 用 Syncfusion Vue 或 `Ds*` 客製元件）。

| Prototype HTML | Production 對應（依 profile） |
|---|---|
| `<input>` / `<select>` | ERP:`<ejs-textbox>` / `<ejs-dropdownlist>` |
| 自製 `<table class="grid">` | ERP:`<ejs-grid>` |
| `<dialog>` 自製 | ERP:`<ejs-dialog>` |
| `<button class="btn btn--primary">` | ERP:`<ejs-button isPrimary>` |
| `<span class="status-pill">` | ERP:`<DsStatusBadge>` |
| `<aside class="nav-rail">` | ERP:`<DsSideNavMenu>` |

> 其他專案的對應由各自 profile 補完。

---

## §14 `app.js` 起手式（reactive state + handlers）

> 共用 `app.js`，每個模組各自 mount。下例為通用骨架；專案專屬欄位（nav-rail 五項、state machine 命名等）請依 profile 調整。

```js
const { createApp, ref, reactive, computed, watch, onMounted } = Vue

createApp({
  setup() {
    // ===== Shell（依 profile 填值） =====
    const breadcrumb = reactive({ module: '', feature: '', docNo: '' })
    const programId = ''
    const version = 'v1.0.0.0.0'
    const navItems = [/* profile 提供 */]
    const activeNav = ref('')

    // ===== View =====
    const view = ref('list')   // 'list' | 'form'

    // ===== Tweaks（持久化 localStorage） =====
    const tweaks = reactive(JSON.parse(localStorage.getItem('tweaks') || '{}'))
    // 預設值由 profile 補
    watch(tweaks, (v) => localStorage.setItem('tweaks', JSON.stringify(v)), { deep: true })

    // ===== Toast =====
    const toasts = ref([])
    const showToast = (kind, message) => {
      const id = Date.now() + Math.random()
      toasts.value.push({ id, kind, message })
      setTimeout(() => { toasts.value = toasts.value.filter(t => t.id !== id) }, 3000)
    }

    // ===== State machine handlers（依 profile 命名） =====
    // 例:ERP profile 用 onSubmit / onApprove / onUnapprove / onVoid

    return { breadcrumb, programId, version, navItems, activeNav, view, tweaks, toasts }
  }
}).mount('#app')
```

> ERP 完整版（含 navItems / state machine handlers）見 ERP repo `.claude/rules/prototype-design/PRODUCE.md` 附錄 B。

---

## §15 `app.css` 結構建議（章節順序）

```
/* 1. Reset / base                       */
/* 2. App Shell                          */
/*    .app-header / .nav-rail / footer   */
/* 3. List View                          */
/*    .toolbar / .search-bar / .grid     */
/*    .pager / .empty-state              */
/* 4. Form View                          */
/*    .summary-card / .stepper           */
/*    .smart-bar                         */
/*    .form-section / .form-grid         */
/*    .form-tabs / .form-footer          */
/* 5. Tweaks Panel                       */
/* 6. Overlay (Modal / Toast)            */
/* 7. Atoms (.btn / .chip / .pill)       */
/* 8. Responsive @1024px                 */
```

所有色彩 / 間距 / 陰影 / 字級必須引用 design tokens CSS 已定義的 CSS variables，**禁** hex、**禁** `@apply`。

---

## 章節編號注意

§4–§9 與 §12（App Shell / List View / Form View / State Machine / Tweaks Panel / Modal-Toast-Empty / Handoff Checklist）為**專案專屬內容**，已移至 `profiles/<project>.md`。本檔保留 §1–§3、§10–§11、§13–§15 跨專案通用條款；編號保留以利對照舊文件。
