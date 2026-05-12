---
name: prototyper
description: Turn a module spec (PM doc, sibling module, or chat description) into a single-file HTML prototype that reviewers can click through. Use when the user says "做一個 XXX 的 prototype", pastes a spec doc path, or asks to convert a Figma frame into a clickable screen. Output is `prototype/project/<模組中文名>.html` (or profile-defined path) plus a chat handoff. Project-specific rules live in `profiles/<project>.md` (Athena ERP → `profiles/erp.md`).
---

# Prototyper（通用核心）

> 把規格 / 同類舊模組 / chat 描述，**一步到位**轉成可給 reviewer 試玩的單檔 HTML prototype。
> 不是 Figma 動效設計，不是 production code。**單檔 HTML + Vue 3 production CDN**。
>
> - 此檔為**所有專案共用核心**；專案專屬規則見 `profiles/<project>.md`
> - **Athena ERP** → `profiles/erp.md`（必讀，含 App Shell / state machine / chat handoff 5 項）
> - **反覆出現的審查問題** → `pitfalls.md`（每次製作前掃一眼，避免重蹈覆轍）
> - **詳細展開**（token / 元件對照 / `app.js` 起手式）→ `REFERENCE.md`

## 1. 觸發即先確認（缺一不開工）

開工前若使用者沒提供，**主動詢問**：

1. **模組中文名**（決定檔名與 `<title>`）
2. **專案 profile**（如 ERP，決定載入哪份 `profiles/*.md`；若 cwd 已能推斷則略）
3. **來源**（PM 文件路徑 / 同類舊模組 / 純 chat 描述？）
4. **輸出路徑**（預設 `prototype/project/<模組中文名>.html`，profile 可覆寫）

> profile 額外要問的項目（如 ERP 的 Odoo model、模組分類）由 profile 內定義，本檔不重複。

## 2. 五階段工作流

### 階段 0｜跨專案複用（先問再做）

- 有同類舊模組？→ 從舊 `.html` 抽出介面規格（欄位 / 狀態 / 關聯），新模組直接引用，**不重畫**
- 沒有？→ 進階段 1

### 階段 1｜規格抽取

依 profile 指定的「規格抽取表」抽欄位、狀態、關聯、預設搜尋條件等。
缺漏即在 chat 詢問。

### 階段 2｜製作 .html（核心）

1. 複製 profile 指定的 starter template → 目標路徑
2. 替換 App Shell（依 profile 規範:breadcrumb / nav / footer / programID / version 等）
3. 建構 List View（自檢項目見 profile）
4. 建構 Form View（自檢項目見 profile）
5. Modal / Toast / Empty State 範例（modal 兩款:`confirm` + `deeplink`，**`pick` 已淘汰**）

### 階段 3｜本機審查（自檢）

兩件事要做:

1. 跑 profile 的 Handoff Checklist，**逐項打勾**才算完成；任一 fail 回對應步驟修正
2. **每次都要掃一眼 `pitfalls.md`**——這份累積了反覆出現的審查問題，目的是不要再犯

### 階段 4｜chat handoff

依 profile 規範交付（如 ERP 要求 5 項）。沒有 profile 時最低限度提供:

1. 對應規格來源（路徑 / 連結 / 「依 chat 需求」）
2. 相比上版差異（首版寫「初版」）
3. 對齊方向（feature 編號 / 文件 / ticket）
4. 特別注意項（已知 trade-off、待 PM 確認的點）

## 3. 通用硬性限制（每次輸出前自檢，違反即重做）

- **IMPORTANT:** 預設 `<html lang="zh-Hant-TW">`（多語環境由 profile 指定）
- **IMPORTANT:** CSS 載入順序:design tokens CSS → Material Symbols → `app.css`
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

## 5. 上下游銜接

- **上游**:PM 文件（Github / Notion）、設計稿、同類舊模組 prototype
- **下游**:
  - reviewer（PM / 主管 / 系統管理員）試玩驗證
  - 工程師依 prototype 串接 API、升級為 production 元件
  - a11y / accessibility 審查

## 6. 常用觸發語

- 「幫我做一個 XXX 模組的 prototype」
- 「這份 PM 文件 [path] 轉成 prototype」
- 「參考 [既有模組].html 做一個類似的 [新模組]」
- 「讀取此份 PRD 並產出互動功能及前端頁面」

## 7. 檔案結構

```
skills/prototyper/
├── SKILL.md              # 本檔（通用核心，所有專案共用）
├── REFERENCE.md          # 詳細展開（token / 元件對照 / app.js 起手式）
├── pitfalls.md           # 反覆出現的審查問題（每次製作前必掃）
├── profiles/
│   └── erp.md            # Athena ERP 專案專屬規則
└── templates/
    └── module-page.html  # ERP starter（其他 profile 可指向自己的 template）
```

## 8. 輸出前 Checklist（通用最低限度）

profile 通常會擴充更嚴格的清單；本檔僅列共通底線:

- [ ] List View 與 Form View 兩種視圖都可切換
- [ ] 必填欄位有紅色 `*`
- [ ] 空狀態 / 刪除確認 / 儲存 toast 均能觸發
- [ ] 無 `@apply`、無 inline hex、無 TypeScript、無 `<style>` / `<script>` 內嵌
- [ ] 已掃過 `pitfalls.md`，沒有踩到既知地雷
- [ ] profile 的 Handoff Checklist 已逐項打勾
