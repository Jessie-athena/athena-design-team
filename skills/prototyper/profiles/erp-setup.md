# Athena ERP Profile — 設定檔（Master Data）

> 本檔承接 `erp-transaction.md §設定檔（Master Data）類型判斷`（判型準則在該檔），提供設定檔模組的**差異速查與完整製作規範**。
>
> **載入條件**：於 `erp-transaction.md` 完成類型判斷後，若判定為**設定檔**才載入本檔；作業檔（transaction documents）不需要。
>
> **覆寫的章節**：State Machine、Summary Bar、Stepper、Smart Bar、Form Footer 動作群、List 工具列批次操作、Form View 章節結構。
>
> **沿用的章節**：
> - `erp-transaction.md`：App Shell、輸入欄樣式、按鈕 icon 政策、Modal/Toast/Empty State、PRD 元件對照
> - `profiles/erp-components/ListSearch.md`、`profiles/erp-components/DataGrid.md`、`profiles/erp-components/FormGroup.md`、`profiles/erp-components/FormFooter.md`（4 個元件子檔，已從 `erp-transaction.md` 獨立出來；設定檔無 Stepper / Summary Card，`Stepper.md` / `SummaryCard.md` 不載）

---

## 作業檔 vs 設定檔 差異速查

| 維度 | 作業檔 (default) | 設定檔 (override) |
|---|---|---|
| State machine | canonical 4 值（draft / submitted / approved / voided）；進銷存可擴充（6 值結轉 / 七值驗收，詳 `erp-transaction.md §進銷存擴充狀態機`） | 僅 `active`: true/false |
| Form Summary Card | sticky；Layout A 多指標 / Layout B 單指標 + stepper（詳 `SummaryCard.md`） | **不使用**；以麵包屑 + Page Title 取代 |
| Stepper | 步序判定（pending/current/done）+ `stepper__line`；動態第 ④ 步（詳 `Stepper.md`） | **不使用** |
| Smart Bar (card-btn) | 關聯單據列 | **不使用**（設定檔通常無下游關聯） |
| 模組分類 nav-rail | 財務 / 進銷存 / 人事 | **設定檔** |
| List 工具列批次操作 | 批次提交 / 批次作廢 / 批次匯出 | **僅批次刪除**；icon-only danger 按鈕 + `[已選取 N 筆 ×]` chip |
| List 狀態欄 | st-chip（依模組狀態機 4–7 種狀態） | **st-chip**（啟用 / 停用）；display only, **不在列表 toggle** |
| List 操作欄 | view (👁) | **edit + delete**（兩個 icon button） |
| Form 章節結構 | 基本資料 + Smart Bar + Tabs（明細） | 基本資料 → 附加群組（依模組）→（可選）稽核軌跡 |
| Form `active` 欄位 | n/a | **Dropdown**「啟用 / 停用」；**非** `boolean_toggle` widget |
| Form 動作按鈕 | 提交 / 核准 / 解核 / 作廢 + 更多（依狀態機變體增減） | 刪除（danger outline）/ 更多操作（儲存後新增、複製）/ **儲存變更** (primary) |
| Form Footer 左群 | 上下筆 (prev/next doc) | 上下筆（`[‹] {n}/{total} [›]`）；新增（`route.id === 'new'`）時整組停用 |
| 稽核軌跡 | n/a（暫不在 prototype 表現） | **表單內 Group**（最近 5 筆 tracking inline）；**不使用 Odoo chatter** |
| 設定檔側欄 | 不使用 | **使用**（main panel 左側列出同組設定，方便跳轉） |

---

## 模組元件規格索引

下列元件規格已獨立成檔，**載入 ERP profile 時這 4 份會一併自動載入**（由 `SKILL.md §支援檔案` 規定）。本表為「快速跳轉」索引，並列出設定檔的覆寫項，避免直接套作業檔規則。

| 元件 | 規格檔 | 設定檔覆寫項 |
|---|---|---|
| List 搜尋區 | `profiles/erp-components/ListSearch.md` | 全沿用，無覆寫 |
| DataGrid | `profiles/erp-components/DataGrid.md` | 操作欄為 `[編輯]+[刪除]`（非 `[檢視]`）、狀態欄 `st-chip` display-only（**不**在列表 toggle active）、`canDelete === false` 時套 `.col-actions--single` 收窄；詳 §List View 七項自檢（設定檔版） |
| Form Group | `profiles/erp-components/FormGroup.md` | `active` 欄位用 Dropdown「啟用 / 停用」、隱藏 boolean flag 欄位不渲染但保留資料、條件式 section 整段 `v-if`；詳 §Form View 七項自檢（設定檔版） + §設定檔資料狀態矩陣 |
| Form Footer | `profiles/erp-components/FormFooter.md` | 右群替換為「`[刪除]` + `[更多操作 ▾]` + `[儲存變更]`」；**無**提交 / 核准 / 解核 / 作廢；詳 §Form Footer（設定檔版） + §設定檔刪除機制 |

> 撰寫 chat handoff 時若 prototype 命中其中任一元件，請在「對齊方向」段附上對應子檔路徑與本檔的覆寫章節編號。

---

## 設定檔側欄（main panel 左側 sub-nav）

設定檔常以「主檔設定 → 進銷存設定 → 〔本設定〕」進入；**同組內**的同類設定（如「進銷存設定」下：地點設定、成本計算層級、倉庫設定、產品類別設定檔、盤存制依據）以**側欄**列出，方便 reviewer 在設定群組內快速跳轉。

- **位置**：nav-rail 右邊、main panel 左邊；寬度 220px（≤ 1024px 時整段收成 dropdown，**非**隱藏）
- **結構**：群組標題（如「共用設定」、「財務設定」、「進銷存設定」）+ 連結列表（icon + label）
- **行為**：點擊切換到對應設定的 List View；本檔高亮
- **資料模型**：`settingsSideNav` array，每個 group `{ title, items: [{ key, label, icon }] }`；`activeSettingKey` 決定高亮
- **顯示條件**：**僅在「模組分類 = 設定檔」**時渲染；作業檔不渲染

---

## List View 七項自檢（設定檔版）

> 通用結構規則沿用 `profiles/erp-components/ListSearch.md` + `profiles/erp-components/DataGrid.md`；本清單只列**設定檔覆寫項**。

- [ ] Toolbar：`selectedRows.length === 0` 時顯示 `[新增 XX]`（primary、**無 icon**）；> 0 時切換為 icon-only `[🗑 批次刪除]`（danger 框） + `[已選取 N 筆 ×]` chip
- [ ] 點 chip 內 `×` 取消選取；**不**提供獨立「取消選取」文字按鈕
- [ ] **無**批次提交 / 批次作廢 / 批次匯出 / 批次啟用 / 批次停用
- [ ] Search 第一個 `<option>` 為 `value=""` 標籤「全部」（規則同作業檔）
- [ ] 狀態 filter 只列 啟用 / 停用（搜尋區可省略此 filter，由前端切換管道處理）
- [ ] Grid 欄位順序：`checkbox(sticky-left) → 主欄（code/name 連結樣式, sticky-left）→ 一般欄 → 狀態(st-chip, display-only) → actions(sticky-right)`（詳見 `profiles/erp-components/DataGrid.md`）
- [ ] 「一般欄」的語意排序遵守 **識別 → 分類 → 歸屬 → 業務屬性 → 狀態**（如：完整路徑 → 類型 → 所屬倉庫/館別/區域 → 部門 → 公司別 → active）；違反此序視同欄位排序錯誤
- [ ] 操作欄：`[編輯]` + `[刪除]`（兩個 icon button；**非** `[檢視]`）；唯讀模式切 chevron `[檢視]`（詳見 `profiles/erp-components/DataGrid.md → 唯讀模式`）
- [ ] `canDelete === false` 時 `[刪除]` **不渲染**（**禁**用 disabled），操作欄套 `.col-actions--single` 收窄寬度至 56px（從預設 96px）
- [ ] 列尾 `[刪除]`（`.ico-btn.is-delete`）與批次列 `[🗑]`（`.btn-icon--danger-square`）符合 §設定檔刪除機制

---

## Form View 七項自檢（設定檔版）

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

---

## 設定檔資料狀態矩陣（Form View readonly 行為）

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

---

## Form Footer（設定檔版）

> 通用結構 / RWD / A11y / dirty-guard / 鍵盤快捷鍵見 `profiles/erp-components/FormFooter.md`。本表只列**設定檔右側按鈕內容覆寫項**。

| 區塊 | 內容 | 說明 |
|---|---|---|
| 左群（rec-nav） | `[‹] {recordIndex}/{recordTotal} [›]` | 上下筆切換；`route.id === 'new'` 時整組停用（行為同共通章節 §1 記錄分頁器） |
| 右群 | `[刪除]`（danger outline） | 受 `canDelete` 控制（manager 角色才可用）；點擊 → 阻擋（如 `quant_count > 0`）走 error toast；無阻擋 → confirm modal（danger）→ success toast + 返回列表 |
|  | `[更多操作 ▾]`（primary outline） | 下拉：`儲存後新增`、`複製`（互動同共通章節 §4 更多操作 ▾） |
|  | `[儲存變更]`（primary fill） | 受 `canSave` 控制；唯讀視角改顯示 `👁 唯讀檢視` 標籤（行為同共通章節 §2 主 CTA） |

> **無**「提交 / 核准 / 解核 / 作廢」；**無**「回列表」按鈕（離開靠麵包屑或 `isDirty` 攔截 modal）。

---

## 設定檔 Modal / Toast 特有場景

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

> 完整刪除機制（按鈕位置、判斷邏輯、視覺、鍵盤）見下方 §設定檔刪除機制。**禁**直接用 error toast 阻擋刪除——已被引用的項目走 warning modal 提供「改為停用」選項。

---

## 設定檔刪除機制

設定檔混合「實體刪除 vs 軟刪除（停用）」行為，且入口分散在 List 批次列 / List 列尾 / Form 底部三處。本節是設定檔結構規範，沿用 §設定檔資料狀態矩陣 的 `canDelete` 旗標。

### 刪除按鈕的三個出現位置

| 位置 | 觸發條件 | 元件樣式 | 行為 |
|---|---|---|---|
| List · 批次列 | `selectedRows.length > 0 && canDelete` | `.btn-icon--danger-square`（40×40、error 邊框 + icon、背景 surface-default） | 對選取的多筆執行刪除 / 停用判斷 |
| List · 列尾 | 該列 `canDelete` | `.ico-btn.is-delete`（40×40、純 icon、error 色） | 對該單筆執行刪除 / 停用判斷 |
| Form · 底部 | `!isNew && canDelete && form.active` | `.btn--outline-danger`（40px、紅色外框文字按鈕、背景透明） | 對當前單筆執行刪除 / 停用判斷 |

**不出現的情境**：新增中（`isNew`）、無刪除權限（`!canDelete`）、本筆已停用（`!form.active`，停用 = 軟刪除目的已達成）。

> `canDelete === false` 時整顆按鈕**不渲染**（**禁**用 disabled 表達無權限）；同時影響 List 操作欄寬度——搭配 `.col-actions--single` 收窄至 56px（見 §List View 七項自檢（設定檔版））。

### 刪除 vs 停用的判斷邏輯（最重要）

```
if (row.usage_count > 0)  →  禁止實體刪除，改走「停用」流程
else                       →  允許實體刪除
```

- **同一顆刪除按鈕**承擔兩種行為，由系統依資料引用狀態切換，使用者只看到一個入口
- 按鈕 `aria-label` / `title` 必須對應實際行為：
  - 可刪除：`aria-label="delete"`、`title="刪除"`
  - 須停用：`aria-label="deactivate"`、`title="已被 N 筆引用，須改為停用"`
- 視覺**不改變**（仍紅色 icon），改變的是 hover tooltip 與後續 modal 內容

### 確認 Modal 規則

所有刪除操作 **MUST** 經 modal 二次確認，**不可直接動作**。完整情境與文案見上方 §設定檔 Modal / Toast 特有場景；額外行為規則：

- 取消按鈕一律 `btn--outline`「取消」，靠右側次位
- **主按鈕焦點預設在「取消」上**（避免 Enter 誤觸刪除）
- Modal icon 對應 `kind`：danger → `delete`、warning → `warning`

### 視覺樣式規格

> 完整 CSS 由實作端 `app.css` 對齊；本表為「紅框白底紅字」家族的視覺規格描述。

| Class | 場景 | bg | color | border | Hover bg | Active bg |
|---|---|---|---|---|---|---|
| `.ico-btn.is-delete` | List · 列尾 | 透明 | `rgb(var(--color-sf-error))` | — | `rgba(var(--color-sf-error), .08)` | — |
| `.btn-icon--danger-square` | List · 批次列 | `var(--bg-surface-default)` | `rgb(var(--color-sf-error))` | `1px solid rgb(var(--color-sf-error))` + `radius-sm` | `rgba(var(--color-sf-error), .08)` | — |
| `.btn--outline-danger` | Form · 底部 | transparent | `rgb(var(--color-sf-error))` | `1px solid rgb(var(--color-sf-error))` | `rgba(var(--color-sf-error), .08)` | `rgba(var(--color-sf-error), .12)` |

**統一原則（合稱「紅框白底紅字」家族）**：

- 預設背景透明 / 白底、僅 error 色描邊或 icon、文字 error 色（= **紅框白底紅字**）
- Hover 統一加 `error @ 8%` 底色，**不**改 icon / 文字顏色
- Active 加深至 `error @ 12%`
- **MUST NOT** 使用實心 `.btn--danger`（紅底白字）於 List / Form；實心紅色僅出現於 Modal 主按鈕

### 結果反饋

| 結果 | 反饋 | 後續動作 |
|---|---|---|
| 刪除成功 | `toast--success`「已刪除」 | List：列消失 / Form：返回 List |
| 停用成功 | `toast--success`「已改為停用」 | 該列 `st-chip` 切為 `st-chip--inactive` |
| 失敗 | `toast--error`「刪除失敗：{reason}」 | **不關 modal**，讓使用者重試 / 取消 |
| 批次部分失敗 | `toast--warning`「成功 {n} 筆，失敗 {m} 筆」 | 失敗者保持選取狀態 |

### 鍵盤與快捷鍵

- **Delete / Backspace** 鍵：**MUST NOT** 綁定為刪除快捷鍵（避免誤刪）
- Modal 開啟時：`Esc` = 取消、`Enter` = **取消**（非確認，因主按鈕焦點在取消上）
- Tab order：取消按鈕在主按鈕之前

### 權限與審計

- `canDelete` 為布林權限旗標，影響**所有**刪除按鈕的可見性（**不是 disabled，是不渲染**）
- 已作廢 / 已核准的資料 **MUST NOT** 顯示刪除按鈕（用作廢 / 反核准取代）
- 每次刪除 / 停用 **MUST** 寫入 audit log（誰、何時、哪筆、行為）

---

## 設定檔 Handoff Checklist（疊加通用 + ERP 清單）

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
- [ ] 刪除機制符合 §設定檔刪除機制：三個按鈕位置與樣式皆為「紅框白底紅字」家族（`.ico-btn.is-delete` / `.btn-icon--danger-square` / `.btn--outline-danger`）、刪除 vs 停用 by `usage_count`、modal `kind` 對應（可刪 = danger / 已被引用 = warning「改為停用」/ 混合批次 = warning）、實心紅底 `.btn--danger`**不**出現於 List/Form、modal 主按鈕焦點預設在「取消」、`canDelete === false` 時 `.col-actions--single` 收窄
- [ ] **List 搜尋區** 規則完全沿用（詳 `profiles/erp-components/ListSearch.md`），設定檔無覆寫項
- [ ] **DataGrid** 操作欄為 `[編輯]+[刪除]`（**非** `[檢視]`）、狀態欄 `st-chip` 不可在列表 toggle、`canDelete === false` 時套 `.col-actions--single` 收窄；通用結構詳 `profiles/erp-components/DataGrid.md`
- [ ] **Form Group** `active` 欄位用 Dropdown「啟用 / 停用」（**非** `boolean_toggle`）、隱藏 boolean flag 欄位不渲染 UI 但保留資料、條件式 section 整段 `v-if` 顯隱；通用結構詳 `profiles/erp-components/FormGroup.md`
- [ ] **Form Footer** 右群為「刪除 / 更多操作 / 儲存變更」三段，**無**提交 / 核准 / 作廢；`canDelete === false` 時刪除按鈕**不渲染**（**禁**用 disabled）；通用結構詳 `profiles/erp-components/FormFooter.md`
