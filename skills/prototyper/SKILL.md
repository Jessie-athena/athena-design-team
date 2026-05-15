---
name: prototyper
description: Turn a module spec into a clickable single-file HTML prototype for reviewers. Use when the user asks to build, generate, or convert anything into a prototype — from a PM doc path, an existing prototype to clone, a Figma frame, or a chat-described spec.
when_to_use: |
  Activate when user types「做 prototype」「PM 文件轉 prototype」「參考 [既有].html 做 [新模組]」「PRD 產出前端頁面」, or pastes a Figma frame asking for clickable conversion.
allowed-tools: Read Write Edit Glob Grep
---

# Prototyper（通用核心）

> 把規格 / 同類舊模組 / chat 描述，**一步到位**轉成可給 reviewer 試玩的單檔 HTML prototype。
> 不是 Figma 動效設計，不是 production code。**單檔 HTML + Vue 3 production CDN**。

## 支援檔案（按需載入）

- **專案專屬規則**：`${CLAUDE_SKILL_DIR}/profiles/<project>.md`
  - Athena ERP → `${CLAUDE_SKILL_DIR}/profiles/erp.md`（必讀，含 App Shell / state machine / chat handoff 5 項 / Form & List 樣式互動規則）
- **反覆審查問題**：`${CLAUDE_SKILL_DIR}/pitfalls.md`（每次製作前掃一眼）
- **詳細展開（工作流明細 / 權重規則明細 / 決策題 / 完整 Examples / token / 元件對照 / `app.js` 起手式）**：`${CLAUDE_SKILL_DIR}/REFERENCE.md`
- **Starter templates**：
  - 作業檔（含狀態流程）：`${CLAUDE_SKILL_DIR}/templates/module-page.html`
  - 設定檔（master data，僅 active true/false）：`${CLAUDE_SKILL_DIR}/templates/setup-page.html`

## Instructions

### 1. 資料來源權重（衝突時的判定法）

四層權重，由上往下：

1. **SKILL.md + profile**（標 IMPORTANT 的硬限） — 永遠最高
2. **PRD**（規格 / 設計稿） — 欄位、狀態、業務邏輯
3. **Design System** — PRD 未指定的視覺
4. **其他**（舊模組、Figma frame、chat） — 結構靈感，不得當規格

派生規則（每次製作前自檢）：

- **R1 — PRD 完整性**：**禁**自動補 PRD 沒列的欄位 / List 欄 / action / status；缺漏停下來問
- **R2 — DS 不覆寫 PRD 視覺**：PRD 明確指定的視覺 > DS 預設；判斷不確定時，**先信 PRD，再回查 DS**

> **IMPORTANT:** 遇到衝突邊界情境（如「PRD 沒提但舊模組有」、「profile IMPORTANT 與 PRD 衝突」），**必讀 `REFERENCE.md §4 資料來源權重明細`**——一行版規則處理不了的，邊界表會給明確判定。

### 2. 觸發即先確認（缺一不開工）

開工前若使用者沒提供，**主動詢問**：

1. **模組中文名**（決定檔名與 `<title>`）
2. **專案 profile**（如 ERP；cwd 可推斷則略）
3. **來源**（PM 文件路徑 / 同類舊模組 / 純 chat 描述）
4. **輸出路徑**（預設 `prototype/project/<模組中文名>.html`，profile 可覆寫）

> profile 額外要問的項目（如 ERP 的 Odoo model、模組分類、作業檔/設定檔類型）由 profile 內定義。

### 3. 五階段工作流（總覽）

| 階段 | 動作摘要 | 完整明細 |
|---|---|---|
| 0 | 跨專案複用：有同類舊模組就抽介面規格直接引用 | `REFERENCE.md §5 階段 0` |
| 1 | 規格抽取三段式：Pass 0 找元件權威來源 → Pass 1 抽 schema → Pass 2 查表轉實作 | `REFERENCE.md §5 階段 1` |
| 2 | 製作 .html：複製 starter template → 替換 Shell → List → Form → Modal/Toast | `REFERENCE.md §5 階段 2` |
| 3 | 本機審查：跑 profile Handoff Checklist + 掃 `pitfalls.md` | `REFERENCE.md §5 階段 3` |
| 4 | chat handoff：依 profile 規範交付（ERP 5 項；通用底線 5 項見 REFERENCE） | `REFERENCE.md §5 階段 4` |

> **IMPORTANT:** **進階段 1 前必讀 `REFERENCE.md §5 五階段工作流明細`**——特別是 Pass 1 的「輸出 5 欄 schema 表給使用者確認」這一步，總覽表不會傳達。略過此步等於跳過使用者校對 PRD 抽取結果，後續若偏差就難救。

### 4. 通用硬性限制（每次輸出前自檢，違反即重做）

- **IMPORTANT:** 預設 `<html lang="zh-Hant-TW">`（多語環境由 profile 指定）
- **IMPORTANT:** CSS 載入順序：design tokens CSS → Material Symbols → `app.css`
- **IMPORTANT:** Vue 3 production CDN，**禁**引入其他 UI library
- **IMPORTANT:** 樣式寫到 `app.css`、互動寫到 `app.js`，**禁**在 `.html` 內嵌 `<style>` / `<script>`（CDN 與引用 `app.js` 的 `<script src>` 例外）
- **IMPORTANT:** Icon 一律 Material Symbols Outlined（`<span class="material-symbols-outlined">`）
- **IMPORTANT:** 色彩 / 間距 / 圓角 / 陰影 / 字級必須使用 design tokens；**禁** inline hex、**禁** `@apply`
- **IMPORTANT:** 不做 mobile（< 768px）；唯一斷點 `@media (max-width: 1024px)` 將 4 欄 grid 降為 2 欄

> profile 可**附加更嚴格**規則（如 ERP 規定 state machine 命名），但**不可放寬**通用限制。
> 通用決策題（List 第一欄是否要 checkbox / 合計列處理 / 必填判斷邊界 / 響應式截斷 / 設定檔有無狀態流程等）→ `REFERENCE.md §6 通用決策題`

### 5. 輸出前 Checklist（通用最低限度）

profile 通常會擴充更嚴格的清單；本檔僅列共通底線：

- [ ] List View 與 Form View 兩種視圖都可切換
- [ ] 必填欄位有紅色 `*`
- [ ] 空狀態 / 刪除確認 / 儲存 toast 均能觸發
- [ ] 無 `@apply`、無 inline hex、無 TypeScript、無 `<style>` / `<script>` 內嵌
- [ ] 已掃過 `${CLAUDE_SKILL_DIR}/pitfalls.md`，沒有踩到既知地雷
- [ ] profile 的 Handoff Checklist 已逐項打勾
- [ ] R1 + R2 已自檢：未自動補 PRD 沒列的欄位；DS 預設未覆寫 PRD 明確指定的視覺

## Example

**輸入**：「這份 PM 文件 `docs/notion/出納模組/付款作業.md` 轉成 prototype」

**預期流程**：

1. §2 四項確認：模組中文名（付款作業）、profile（cwd 推斷為 ERP）、來源（已給 PM 路徑）、輸出（`prototype/project/付款作業.html`）
2. 載入 `${CLAUDE_SKILL_DIR}/profiles/erp.md` + `pitfalls.md`
3. 依 erp.md「規格抽取表」從 PM 抽欄位、狀態、關聯、預設搜尋
4. 判斷為作業檔（有狀態流程）→ 複製 `templates/module-page.html`
5. 完成 List/Form View；跑 ERP 作業檔 Handoff Checklist
6. chat handoff 含 ERP 5 項

**關鍵守則**：依 R1（PRD 完整性），PRD 沒列的欄位 / 動作**不自動補**；缺漏就停下來問。

> 另外 2 種輸入情境的完整範例（A：從同類舊模組複製、B：純 chat 描述無正式文件）→ `REFERENCE.md §7 Examples 擴充`
