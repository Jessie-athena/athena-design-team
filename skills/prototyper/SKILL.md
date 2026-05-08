---
name: prototyper
description: Turn an Athena ERP module spec (Notion doc, sibling module, or chat description) into a single-file HTML prototype that reviewers can click through. Use when the user says "做一個 XXX 模組的 prototype", pastes a docs/notion path, or asks to convert a Figma frame into an ERP screen. Output is `prototype/project/<模組中文名>.html` plus a chat handoff message with five required fields.
---

# Prototyper（Athena ERP）

> 本 Skill 取代舊版 Figma-based prototyper（保留於 git history）。
> 對應 ERP 內部規範：`.claude/rules/prototype-design/PRODUCE.md`、`CLAUDE.md`。
> 詳細審查條款 / token 表 / 元件對照見 `REFERENCE.md`。

## 角色定位

把 PM 規格、同類舊模組或 chat 描述，**一步到位**轉成可給 reviewer 試玩的 `prototype/project/<模組中文名>.html`。
不是 Figma 動效設計，不是 production code。**單檔 HTML + Vue 3 production CDN**。

## 觸發即先確認（缺一不開工）

開工前若使用者沒提供，**主動詢問**這四項，得到答案才動手：

1. **模組中文名**（決定檔名與 `<title>`）
2. **對應 Odoo model**（如 `psi.sale_order`，衍生 `programID = PSI-SO`）
3. **模組分類**（財務 / 進銷存 / 人事 / 設定檔，決定 breadcrumb 與 nav-rail 高亮）
4. **來源**（PM 文件路徑 / 同類舊模組 / 純 chat 描述？）

## 五階段工作流

### 階段 0｜跨專案複用（先問再做）

- 有同類舊模組？→ 從舊 `.html` 抽出「介面規格文件」（欄位表 / 狀態流程 / 關聯單據），新模組直接引用，**不重畫**
- 沒有？→ 進階段 1

### 階段 1｜PM 規格抽取

從 `docs/notion/*.md` 依序抽 7 項；缺漏即在 chat 詢問：

| 抽取項 | 用於 |
|---|---|
| 模組中文名 | 檔名、`<title>`、breadcrumb |
| 模組分類 | breadcrumb 第一段、nav-rail 高亮 |
| Odoo model | programID 衍生、chat handoff 必附 |
| 欄位表（label / 型別 / 必填） | Form View form-grid cells |
| 狀態流程圖 / 動作清單 | stepper、footer 動作按鈕、tweaks `docState` |
| 關聯單據 | Smart Bar（無關聯則整段移除） |
| List 預設搜尋條件 | search bar `<option>` |

### 階段 2｜製作 .html（核心）

1. 複製 `templates/module-page.html` → `prototype/project/<模組中文名>.html`
2. 替換 App Shell：`<title>` / breadcrumb 三段 / `programId` / `version` / `activeNav`
3. 建構 List View（依 REFERENCE.md §5 的 7 項自檢）
4. 建構 Form View（依 §6 的 7 項自檢）
5. 建構 Tweaks Panel（**必備** 三組 radio：使用者角色 / 單據狀態 / 關鍵 flag，狀態值寫入 `localStorage`）
6. Modal / Toast / Empty State 範例（modal 兩款：`confirm` + `deeplink`，**`pick` 已淘汰**）

### 階段 3｜本機審查（自檢）

跑 REFERENCE.md §12 Handoff Checklist，**逐項打勾**才算完成；任何一項 fail 回對應步驟修正。

### 階段 4｜chat handoff（必附五項）

每次交付都要在 chat 附上：

1. 對應 `docs/notion/...` 路徑（沒有則寫「依 chat 需求」）
2. 目標 Odoo model
3. 相比上版差異（首版寫「初版」）
4. 對齊方向（feature 編號 / Notion page / Linear ticket）
5. 特別注意項（已知 trade-off、待 PM 確認的點）

## 硬性限制（每次輸出前自檢，違反即重做）

- **IMPORTANT:** `<html lang="zh-Hant-TW">`
- **IMPORTANT:** CSS 載入順序：`ds/colors_and_type.css` → Material Symbols → `app.css`
- **IMPORTANT:** Vue 3 production CDN，**禁**引入其他 UI library
- **IMPORTANT:** 樣式寫到 `app.css`、互動寫到 `app.js`，**禁**在 `.html` 內嵌 `<style>` / `<script>`（CDN 與引用 `app.js` 的 `<script src>` 例外）
- **IMPORTANT:** Icon 一律 Material Symbols Outlined（`<span class="material-symbols-outlined">`）
- **IMPORTANT:** 色彩 / 間距 / 圓角 / 陰影 / 字級必須使用 `ds/colors_and_type.css` 的 token；**禁** inline hex、**禁** `@apply`
- **IMPORTANT:** 不做 mobile（< 768px）；唯一斷點 `@media (max-width: 1024px)` 將 4 欄 grid 降為 2 欄
- **IMPORTANT:** State machine 用 `draft / submitted / approved / voided`；action 用 `action_submit / action_approve / action_unapprove / action_void`，偏離須在 chat 註明理由
- **IMPORTANT:** 「已產生傳票」是 chip（由 `move_id` 是否存在判斷），**不是**第 5 個狀態

## 常見決策題

| 情境 | 決策 |
|---|---|
| List 第一欄一定要 checkbox？ | 是。即使目前無批次操作也保留 |
| 沒有金額欄位怎麼放合計列？ | 移除 `tfoot` 整段，不要保留空合計列 |
| 必填判斷只在前端？ | Prototype 階段視覺上有 `*` 即可；validation 邏輯由 production code 處理 |
| 狀態欄要 pill 還是 stepper？ | List 用 pill；Form summary card 用 stepper（voided 改 pill） |
| Smart Bar 沒有關聯單據？ | 整段 `<nav>` 不渲染，**不留空 bar** |
| 響應式欄位太多被截斷？ | 橫向 scroll；**禁**隱藏關鍵欄位 |

## 上下游銜接

- **上游**：`product-strategist` / `requirement-analyst` 的 PM 文件、`ui-designer` 的視覺稿、同類舊模組 prototype
- **下游**：
  - → reviewer（PM / 主管 / 系統管理員）試玩驗證
  - → 前端 / 後端工程師（依 prototype 串接 Odoo API、升級為 Nuxt + Syncfusion EJ2）
  - → `accessibility-reviewer`（a11y 檢查）

## 常用觸發語

- 「幫我做一個 XXX 模組的 prototype」
- 「這份 PM 文件 [docs/notion/...md] 轉成 prototype」
- 「參考 [既有模組].html 做一個類似的 [新模組]」
- 「補一個 tweaks panel 讓 reviewer 切換狀態」

## 輸出前 Checklist

詳細條款見 `REFERENCE.md §12`，最低限度確認：

- [ ] 三件套 shell（header / nav-rail / footer）齊全、breadcrumb 層級正確
- [ ] List View 與 Form View 兩種視圖都可切換
- [ ] 所有 4 種狀態（含 voided）能透過 Tweaks 切換驗證
- [ ] 必填欄位有紅色 `*`
- [ ] 空狀態 / 刪除確認 / 儲存 toast 均能觸發
- [ ] 無 `@apply`、無 inline hex、無 TypeScript
- [ ] chat handoff 五項齊全
