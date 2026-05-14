---
name: prototyper
description: Turn a module spec into a clickable single-file HTML prototype for reviewers. Use when the user asks to build, generate, or convert anything into a prototype — from a PM doc path, an existing prototype to clone, a Figma frame, or a chat-described spec.
when_to_use: |
  Activate when the user types phrases like:
  - 「幫我做一個 XXX 模組的 prototype」
  - 「這份 PM 文件 [path] 轉成 prototype」
  - 「參考 [既有模組].html 做一個類似的 [新模組]」
  - 「讀取此份 PRD 並產出互動功能及前端頁面」
  Also activate when the user pastes a Figma frame and asks to convert it into a clickable screen.
allowed-tools: Read Write Edit Glob Grep
---

# Prototyper（通用核心）

> 把規格 / 同類舊模組 / chat 描述，**一步到位**轉成可給 reviewer 試玩的單檔 HTML prototype。
> 不是 Figma 動效設計，不是 production code。**單檔 HTML + Vue 3 production CDN**。

## 支援檔案（按需載入）

- 專案專屬規則：`${CLAUDE_SKILL_DIR}/profiles/<project>.md`
  - Athena ERP → `${CLAUDE_SKILL_DIR}/profiles/erp.md`（必讀，含 App Shell / state machine / chat handoff 5 項）
- 反覆出現的審查問題：`${CLAUDE_SKILL_DIR}/pitfalls.md`（每次製作前掃一眼）
- 詳細展開（token / 元件對照 / `app.js` 起手式）：`${CLAUDE_SKILL_DIR}/reference.md`
- Starter templates：
  - 作業檔（含狀態流程）：`${CLAUDE_SKILL_DIR}/templates/module-page.html`
  - 設定檔（master data，僅 active true/false）：`${CLAUDE_SKILL_DIR}/templates/setup-page.html`

## 1. 觸發即先確認（缺一不開工）

開工前若使用者沒提供，**主動詢問**：

1. **模組中文名**（決定檔名與 `<title>`）
2. **專案 profile**（如 ERP，決定載入哪份 profile；若 cwd 已能推斷則略）
3. **來源**（PM 文件路徑 / 同類舊模組 / 純 chat 描述？）
4. **輸出路徑**（預設 `prototype/project/<模組中文名>.html`，profile 可覆寫）

> profile 額外要問的項目（如 ERP 的 Odoo model、模組分類）由 profile 內定義，本檔不重複。

## 2. 五階段工作流

### 階段 0｜跨專案複用（先問再做）

- 有同類舊模組？→ 從舊 `.html` 抽出介面規格（欄位 / 狀態 / 關聯），新模組直接引用，**不重畫**
- 沒有？→ 進階段 1

### 階段 1｜規格抽取

依 profile 指定的「規格抽取表」抽欄位、狀態、關聯、預設搜尋條件等。缺漏即在 chat 詢問。

### 階段 2｜製作 .html（核心）

1. 複製 profile 指定的 starter template → 目標路徑
   - 作業檔（transaction documents，含狀態流程）→ `${CLAUDE_SKILL_DIR}/templates/module-page.html`
   - 設定檔（master data，僅 active true/false）→ `${CLAUDE_SKILL_DIR}/templates/setup-page.html`
   - 類型判斷準則由 profile 規定（ERP 見 `${CLAUDE_SKILL_DIR}/profiles/erp.md §設定檔（Master Data）特化規則`）
2. 替換 App Shell（依 profile 規範：breadcrumb / nav / footer / programID / version 等）
3. 建構 List View（自檢項目見 profile，作業檔與設定檔有獨立清單）
4. 建構 Form View（自檢項目見 profile，作業檔與設定檔有獨立清單）
5. Modal / Toast / Empty State 範例（modal 兩款：`confirm` + `deeplink`，**`pick` 已淘汰**）

### 階段 3｜本機審查（自檢）

兩件事要做：

1. 跑 profile 的 Handoff Checklist，**逐項打勾**才算完成；任一 fail 回對應步驟修正
2. **每次都要掃一眼 `${CLAUDE_SKILL_DIR}/pitfalls.md`**——這份累積了反覆出現的審查問題，目的是不要再犯

### 階段 4｜chat handoff

依 profile 規範交付（如 ERP 要求 5 項）。沒有 profile 時最低限度提供：

1. 對應規格來源（路徑 / 連結 / 「依 chat 需求」）
2. 相比上版差異（首版寫「初版」）
3. 對齊方向（feature 編號 / 文件 / ticket）
4. 特別注意項（已知 trade-off、待 PM 確認的點）

## 3. 通用硬性限制（每次輸出前自檢，違反即重做）

- **IMPORTANT:** 預設 `<html lang="zh-Hant-TW">`（多語環境由 profile 指定）
- **IMPORTANT:** CSS 載入順序：design tokens CSS → Material Symbols → `app.css`
- **IMPORTANT:** Vue 3 production CDN，**禁**引入其他 UI library
- **IMPORTANT:** 樣式寫到 `app.css`、互動寫到 `app.js`，**禁**在 `.html` 內嵌 `<style>` / `<script>`（CDN 與引用 `app.js` 的 `<script src>` 例外）
- **IMPORTANT:** Icon 一律 Material Symbols Outlined（`<span class="material-symbols-outlined">`）
- **IMPORTANT:** 色彩 / 間距 / 圓角 / 陰影 / 字級必須使用 design tokens；**禁** inline hex、**禁** `@apply`
- **IMPORTANT:** 不做 mobile（< 768px）；唯一斷點 `@media (max-width: 1024px)` 將 4 欄 grid 降為 2 欄

> profile 可**附加更嚴格**規則（如 ERP 規定 state machine 命名），但**不可放寬**通用限制。

## 4. 通用決策題

| 情境 | 決策 |
|---|---|
| List 第一欄一定要 checkbox？ | 是。即使目前無批次操作也保留（未來容易加） |
| 沒有金額欄位怎麼放合計列？ | 移除 `tfoot` 整段，不要保留空合計列 |
| 必填判斷只在前端？ | Prototype 階段視覺上有 `*` 即可；validation 邏輯由 production code 處理 |
| 狀態欄要 pill 還是 stepper？ | List 用 pill；Form summary card 用 stepper |
| 響應式欄位太多被截斷？ | 橫向 scroll；**禁**隱藏關鍵欄位 |
| 規格沒提「狀態流程」是不是缺漏？ | 不一定。設定檔（master data）本來就沒有狀態機，僅 `active`；參照 profile 的設定檔特化規則 |

## 5. 輸出前 Checklist（通用最低限度）

profile 通常會擴充更嚴格的清單；本檔僅列共通底線：

- [ ] List View 與 Form View 兩種視圖都可切換
- [ ] 必填欄位有紅色 `*`
- [ ] 空狀態 / 刪除確認 / 儲存 toast 均能觸發
- [ ] 無 `@apply`、無 inline hex、無 TypeScript、無 `<style>` / `<script>` 內嵌
- [ ] 已掃過 `${CLAUDE_SKILL_DIR}/pitfalls.md`，沒有踩到既知地雷
- [ ] profile 的 Handoff Checklist 已逐項打勾
