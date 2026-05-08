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

## App Shell 規範

- **erp-header**: home button + breadcrumb（模組分類 / 功能名 / 單號）+ favorite + 通知 / 設定 / avatar
- **nav-rail**: 5 項固定順序 — 我的最愛、財務、進銷存、人事、設定檔
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

- [ ] Summary card 含關鍵指標 + stepper；`form.status === 'voided'` 改顯示 pill（不顯示 stepper）
- [ ] Section 用 `bar + title + form-grid--4`；響應式靠斷點，**禁**手動隱藏欄位
- [ ] 必填欄位 label 加 `<span class="required">*</span>`
- [ ] Smart Bar: `form.relations.length > 0` 才渲染，無關聯時整段不顯示
- [ ] Tab block: 表頭右側固定 add 按鈕；行內編輯模式有 save / cancel
- [ ] Footer 三段: 上下筆 / 動作群 / 「更多操作」下拉
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
- [ ] Smart Bar 在無關聯時整段不渲染
- [ ] chat handoff 五項齊全（對應 Notion 路徑、Odoo model、版本差異、對齊方向、注意項）
- [ ] 開啟欲實作的 `.html` 後再 export（會標為 primary）

---

## 關聯規範（ERP repo 內專屬）

- 對應 ERP 內部規範: `.claude/rules/prototype-design/PRODUCE.md`、`.claude/rules/prototype-design/CLAUDE.md`
- token 與元件對照: `.claude/rules/figma-design-system/references/tokens.md`、`components.md`
- Syncfusion playground（production 升級時對照）: `/Users/athena/working/ddd/syncfusion-playground/app/pages/playground/`
