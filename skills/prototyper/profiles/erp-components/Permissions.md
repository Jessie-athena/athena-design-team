# 角色權限與 perm-block（作業檔）

> 屬於 `profiles/erp-transaction.md` 的延伸元件規格。
> 載入時機：作業檔 Form / List View **依需求載入**（單據含角色分流 / 唯讀檢視 / 無權限遮罩時必載；由 `SKILL.md §支援檔案` 規定——該處為載入規則的**單一來源**）。
>
> 上層 profile：`profiles/erp-transaction.md`
> 同層元件：`ListSearch.md` / `DataGrid.md` / `FormGroup.md` / `FormFooter.md` / `SummaryCard.md` / `Stepper.md` / `Permissions.md` / `RelBanner.md` / `Skeleton.md`
>
> 對齊基準：`design-prototype/web-erp/庫存模組`（行為基準 = 領料單；唯讀基準 = 出庫單 / 入庫單）。本檔把該標準的角色分流與無權限遮罩落地為單檔 HTML/CSS。

---

進銷存作業檔常依**角色**分流可視 / 可編 / 可核能力。權限是**正交於狀態機**的另一條軸：狀態決定「這張單現在能做什麼」，角色決定「這個人能不能做」。兩者**不可混用**——例如「已核准且我是檢視者」要兩條件分別判斷。

prototype 以 `<div class="app-shell" :data-role="role">` 承載當前角色，所有可視性由 computed 衍生；**禁**用 `disabled` 把無權限欄位鎖死當作權限表達（語意錯誤，且使用者誤以為欄位故障）——無權限一律「不渲染入口」或「整頁 perm-block」。

## 角色階層（兩種典型）

依單據是否可編輯分兩套，**同一模組只取一套**：

### A. 唯讀系統產生單（如出庫單 / 入庫單）

由上游單據核准後系統產生，單身本體不可編；權限只控制「能不能看」。

| role | 名稱 | canView |
|---|---|---|
| `general` | 一般使用者 | ✗ |
| `buyer` | 採購人員 | ✗ |
| `lead` | 採購主管 | ✗ |
| `deanmgr` | 系統管理者 | ✓ |

- 非 `canView` 角色進入 List 或 Detail → 整頁顯示 `perm-block`（見下節）。
- 各模組的可視角色清單依 PRD；上表為標準樣板，缺漏時以此為預設。

### B. 可編輯作業單（如領料單）

| role | 名稱 | canView | canEdit | canApprove | canDeleteDraft | canSelect |
|---|---|---|---|---|---|---|
| `readonly` | 檢視者 | ✓ | ✗ | ✗ | ✗ | ✗ |
| `user` | 倉管 | ✓ | ✓ | ✓ | ✓ | ✓ |
| `admin` | 管理員 | ✓ | ✓ | ✓ | ✓ | ✓ |

## Computed 可視性（單一來源）

衍生規則集中在 computed，template 只讀 boolean，**禁**在 template 內重算角色：

```js
canView()        { return !!(ROLES[this.role] && ROLES[this.role].canView); }
canEdit()        { return ['user','admin'].includes(this.role); }      // 新增 / 編輯 / 提交 / 刪草稿
canApprove()     { return ['user','admin'].includes(this.role); }      // 核准 / 退回 / 作廢
canDeleteDraft() { return ['user','admin'].includes(this.role); }
canSelect()      { return this.canEdit; }                              // 是否顯示多選 / 批次
isReadOnly()     { return this.role === 'readonly'; }
isLocked()       { return !['draft','submitted'].includes(this.form.state); } // 狀態鎖（正交於角色）
blocked()        { return (this.view === 'list' || this.view === 'detail') && !this.canView; }
```

> `isLocked` 是**狀態軸**的鎖（已核准 / 已作廢的 header 欄位不可改），與角色軸的 `isReadOnly` 各自獨立；欄位「可改」需 `!isLocked && !isReadOnly` 同時成立。

## 可視性如何套用到各區（速查）

| 區塊 | 規則 |
|---|---|
| Toolbar 主操作（新增 / 匯入 / 批次） | `v-if="canEdit"`（再疊狀態條件，如批次結轉 `canEdit && batchConvertable`） |
| List 多選欄（`.col-check`）+ `chip--selected` | `v-if="canSelect"`（詳 `ListSearch.md §批次選取 chip`） |
| List 列操作鈕 | `canEdit` 且該列可編 → `.ico-btn.is-edit`（鉛筆）；否則 `.is-view`（chevron，title「檢視」）。**禁直接隱藏入口** |
| List 刪除鈕 | `v-if="canDeleteDraft"` 且該列為 `draft`（草稿物理刪除） |
| Form header 欄位 | `!isLocked && !isReadOnly` → `<select>/<input>`；否則切唯讀文字 `div.input.is-readonly`（詳 `erp-transaction.md §輸入欄樣式`） |
| Form 明細行內編輯 | `canEditLines = ['draft','submitted'].includes(state) && canEdit && !isReadOnly`（詳 `DataGrid.md §三`） |
| Form Footer 動作群 | 依 `canEdit / canApprove` 顯隱；唯讀角色只剩 `.readonly-tag`（詳 `FormFooter.md §狀態矩陣`） |
| 整頁無權限 | `blocked` → 用 `perm-block` 取代 List / Detail 主內容 |

---

## perm-block（無權限全頁遮罩）

`canView === false` 的角色進入 List 或 Detail 時，**main-panel 內容整段**改顯示 `perm-block`，引導返回首頁；**不**渲染空表頭、不渲染 toolbar。

### Anatomy

```html
<div v-if="blocked" class="perm-block">
  <div class="perm-state">
    <span class="perm-state__icon material-symbols-outlined-block">
      <iconify-icon icon="material-symbols:lock-outline"></iconify-icon>
    </span>
    <h2 class="perm-state__title">無檢視權限</h2>
    <p class="perm-state__msg">您目前的角色（<strong>{{ roleName }}</strong>）沒有檢視此單據的權限，請洽系統管理者。</p>
    <button class="btn btn--outline-primary" @click="goHome">返回首頁</button>
  </div>
</div>
```

> 標準原型用 160×160 SVG 插圖；prototype 階段以單顆大尺寸 Iconify（`material-symbols:lock-outline` / `block`）替代即可，**禁**另裝插圖套件或建佔位圖。

### 視覺規格

| 元素 | 規格 |
|---|---|
| `.perm-block` | `height: 100%; display: flex; align-items: center; justify-content: center; padding: var(--space-6xl)`（48px）`; background: transparent` |
| `.perm-state` | `display: flex; flex-direction: column; align-items: center; text-align: center; gap: var(--space-xl)`（16px）`; max-width: 460px` |
| `.perm-state__icon` | icon 字級 ~96px（或 SVG 160px）；色 `var(--border-strong)`（#7F8996，沉穩不搶眼） |
| `.perm-state__title` | Noto Sans TC 22px / 700 / line-height 1.3 / `var(--text-primary)`（#0F172A） |
| `.perm-state__msg` | 14px / line-height 1.6 / `var(--text-secondary)`（#67717E）；內 `strong { color: var(--text-primary); font-weight: 700; }` |
| 返回鈕 | `.btn.btn--outline-primary`（動詞 CTA 無 icon，詳 `erp-transaction.md §按鈕 icon 政策`） |

### 互動

- `blocked` 為 true 時，List 的 toolbar / search / grid / pager 與 Detail 的 summary / form **全部不渲染**（`v-if` 互斥），只留 perm-block。
- 切換 `:data-role` 即時反映：有權限角色立刻顯示正常內容，無權限角色立刻切回 perm-block——可作為 prototype demo 的權限驗證路徑（呼應 ERP Handoff Checklist「狀態 / 權限能透過路徑切換驗證」）。
- 返回鈕 `goHome` 導回首頁（prototype 內以 `view = 'list'` 或 toast 示意即可）。

> 與「空狀態」區分：`empty-state`（`inbox` icon）是「有權限但查無資料」；`perm-block`（lock icon）是「無權限存取」。兩者**不可混用** icon 與文案。
