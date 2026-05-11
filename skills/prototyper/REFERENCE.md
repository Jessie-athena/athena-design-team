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

> 以下 token 列以 **Athena Design System** 作為 source of truth（`design-tokens-base.css` + `design-tokens-athena.css` + `colors_and_type.css`）。其他專案以對應 DS 的同名 token 為準；profile 內若用到此清單未列的 token 須在 profile 註明來源。

### 色彩 — 三層架構

DS 色彩採三層：

1. **Raw channels** (`--color-sf-*`)：comma-separated RGB（如 `--color-sf-primary: 40, 119, 238`）。**用於 `rgba()` wrapper**（半透明覆層、tint）。
2. **Semantic alias** (`--text-*` / `--bg-*` / `--border-*`)：已經 wrap 為 `rgb(...)` 的別名。**用於實心色**（文字、實心背景、邊框）。
3. **CSS variable consumer**（component 自己的 class）：盡量引用第 2 層；只有需要 alpha 時才用第 1 層。

#### 文字色

| Token | 用途 |
|---|---|
| `--text-primary` | 主要文字（near-black） |
| `--text-secondary` | 次要文字 / label（grey） |
| `--text-placeholder` | placeholder / 淡化文字 |
| `--text-inverse` | 反白文字（用於深色背景） |
| `--text-brand` | 品牌色文字（= primary blue） |
| `--text-success` / `--text-error` / `--text-warning` | 狀態色文字 |

#### 背景 / 表面

| Token | 用途 |
|---|---|
| `--bg-surface-default` | 頁面 / 卡片基本背景（白） |
| `--bg-surface-variant` | 微 tint 表面（如 input filled 背景、grid header 背景） |
| `--bg-surface-inverse` | 反色表面 |
| `--bg-primary` | 實心品牌色背景（primary 按鈕） |
| `--bg-primary-tint` | 品牌色 container tint |
| `--bg-app` | App-shell 底層背景 |

#### 邊框

| Token | 用途 |
|---|---|
| `--border-default` | 一般邊框（≈ `#D7DAE0`） |
| `--border-strong` | 強調邊框（≈ `#7F8996`，hover / focus 用） |
| `--border-focus` | Focus 邊框（= primary） |

#### Raw channels（rgba 用）

| Token | 值 | 範例 |
|---|---|---|
| `--color-sf-primary` | `40, 119, 238` | `rgba(var(--color-sf-primary), .08)` — primary 8% tint |
| `--color-sf-success` | `18, 183, 106` | `rgba(var(--color-sf-success), .12)` |
| `--color-sf-error` | `244, 73, 62` | `rgba(var(--color-sf-error), .12)` — 例如 voided pill 背景 |
| `--color-sf-warning` | `247, 144, 9` | `rgba(var(--color-sf-warning), .12)` |
| `--color-sf-primary-container` | `213, 228, 255` | container tint |

> 預先 wrap 的 alpha 變體也存在（如 `--color-sf-primary-opacity-8` = `40, 119, 238, 0.08`），可用 `rgba(var(--color-sf-primary-opacity-8))` 簡寫。

### 間距（t-shirt 命名）

| Token | 值 | 別名 |
|---|---|---|
| `--space-none` | 0 | — |
| `--space-xs` | 2px | — |
| `--space-sm` | 4px | — |
| `--space-md` | 8px | — |
| `--space-lg` | 12px | — |
| `--space-xl` | 16px | — |
| `--space-2xl` | 20px | — |
| `--space-3xl` | 24px | — |
| `--space-4xl` | 32px | — |
| `--space-5xl` | 40px | — |
| `--space-6xl` | 48px | — |
| `--space-7xl` | 56px | — |
| `--space-8xl` | 64px | — |

> `--ds-space-athena-padding-*` 為 padding 專用對應；通用情境用 `--space-*` 即可。

### 圓角

| Token | 值 |
|---|---|
| `--radius-none` | 0 |
| `--radius-xs` | 2px |
| `--radius-sm` | 4px |
| `--radius-md` | 6px |
| `--radius-lg` | 8px |
| `--radius-xl` | 12px |
| `--radius-2xl` | 16px |
| `--radius-3xl` | 18px |
| `--radius-4xl` | 20px |
| `--radius-5xl` | 24px |
| `--radius-full` | 1000px（pill / circle） |

### 陰影（Material 3 elevation）

| Token | 用途 |
|---|---|
| `--shadow-e1` | 靜止 card / dropdown 表面 |
| `--shadow-e2` | toast / snackbar |
| `--shadow-e3` | dialog |
| `--shadow-e4` | menu / popover（浮於 dialog 上方） |
| `--shadow-focus-ring` | 4px primary 16% focus ring |

> **DS 卡片預設無 shadow**（只用 1px outline-variant 邊框）。Shadow 只在 hover/active 或浮層出現。

### 字型

#### Family

- `--font-family-base`（中文）`Noto Sans TC`
- `--font-family-en` `Roboto`
- `--font-family-mono` `JetBrains Mono` / `Roboto Mono`

#### Size

| Token | 值 | 對應 Figma style 名 |
|---|---|---|
| `--font-size-xxs` | 10px | — |
| `--font-size-xs` | 11px | `body/sm` 變體 |
| `--font-size-sm` | 12px | `body/sm` |
| `--font-size-md` | 14px | `body/md` / `label/md` |
| `--font-size-lg` | 16px | `body/lg` / `heading/sm` |
| `--font-size-sf-h6` | 18px | — |
| `--font-size-sf-h5` | 20px | `heading/md` |
| `--font-size-sf-h4` | 22px | — |
| `--font-size-sf-h3` | 24px | `heading/lg` |
| `--font-size-sf-h2` | 28px | `display/md` |
| `--font-size-sf-h1` | 32px | `display/lg` |

#### Weight

| Token | 值 |
|---|---|
| `--font-weight-sf-normal` | 400 |
| `--font-weight-sf-medium` | 500 |
| `--font-weight-sf-bold` | 700 |

> Figma 的 `display/*` / `heading/*` / `body/*` / `label/*` 是**複合樣式**（size + weight + line-height + letter-spacing），對應到 CSS 時要從上表分別挑 token，或直接用 component-level utility class（如 DS components.css 內的 `.btn` 已預先綁好）。

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

    // ===== Stepper helpers（ERP profile §Stepper 三狀態結構） =====
    // form.status ∈ stateOrder 時：step 在 cur 前 = done、等於 cur = active、之後 = pending
    // bar 的狀態 = 左側 step 的狀態（見 profile 對應矩陣）
    const stateOrder = ['draft', 'submitted', 'approved']
    const stepState = (s) => {
      const cur = stateOrder.indexOf(form.status)
      const idx = stateOrder.indexOf(s)
      if (idx < cur) return 'done'
      if (idx === cur) return 'active'
      return 'pending'
    }
    const stepClass = (s, kind = 'step') => {
      const st = stepState(s)
      if (st === 'pending') return ''
      return kind === 'bar' ? `stepper__bar--${st}` : `stepper__step--${st}`
    }

    // ===== Smart Bar（ERP profile §Smart Bar `card-btn` 結構） =====
    // form.relations 形狀：[{ type, count, unit, title }, ...]
    //   count === null 的條目（如「會計傳票」）省略 count + 單位，永遠顯示
    //   count > 0 才顯示；count === 0 過濾掉
    const visibleRelations = computed(() =>
      (form.relations || []).filter(r => r.count == null || r.count > 0)
    )

    return {
      breadcrumb, programId, version, navItems, activeNav, view, tweaks, toasts,
      stepState, stepClass, visibleRelations,
    }
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
