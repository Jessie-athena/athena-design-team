---
name: prototyper
description: Turn an ERP module spec into a clickable single-file HTML prototype (Vue 3 production CDN, no build step) for reviewers. Use this skill whenever the user types「做 prototype」「PM 文件轉 prototype」「參考 [既有].html 做 [新模組]」「PRD 產出前端頁面」「把這份規格做成可以點的頁面」, pastes a Figma frame asking for a clickable HTML conversion, references a PM doc path under `docs/notion/`, asks to convert a PRD or Notion page into an interactive prototype page, or explicitly runs `/prototyper`. Skip for Figma motion / interaction design (use `figma-use` / `figma-generate-design`), production Vue SFC / Odoo Python code, or pure requirement parsing without prototype output (use `requirement-analyst`).
allowed-tools: Read Write Edit Glob Grep
---

# Prototyper（ERP）

> 把規格 / 同類舊模組 / chat 描述，**一步到位**轉成可給 reviewer 試玩的單檔 HTML prototype。
> 不是 Figma 動效設計，不是 production code。**單檔 HTML + Vue 3 production CDN**。

## 支援檔案（按需載入）

- **跨專案共用 profile**：`${CLAUDE_SKILL_DIR}/profiles/Shared.md`（**所有專案前置必讀**；含頁面框架等共通骨架）
- **專案專屬 profile**：`${CLAUDE_SKILL_DIR}/profiles/<project>.md`（在共用 profile 之上**附加 / 覆寫**）
  - Athena ERP → `${CLAUDE_SKILL_DIR}/profiles/erp-transaction.md`（必讀，含 App Shell 覆寫 / state machine / chat handoff 5 項 / Form & List 樣式互動規則）
  - 若單據類型為**設定檔** (master data，依 `erp-transaction.md §類型判斷準則` 判定) → 另載入 `${CLAUDE_SKILL_DIR}/profiles/erp-setup.md`（含設定檔側欄、List/Form 自檢、資料狀態矩陣、刪除機制等）
  - **ERP 專案載入時，無論作業檔 / 設定檔，自動載入下列 4 份元件規格檔**（List View 與 Form View 幾乎都會用到，避免漏讀）：
    - `${CLAUDE_SKILL_DIR}/profiles/erp-components/ListSearch.md`（toolbar / search bar / RWD 收合）
    - `${CLAUDE_SKILL_DIR}/profiles/erp-components/DataGrid.md`（欄寬鎖 / sticky cell / 互動狀態）
    - `${CLAUDE_SKILL_DIR}/profiles/erp-components/FormGroup.md`（form-grid 4 欄 / 跨欄 modifier / RWD）
    - `${CLAUDE_SKILL_DIR}/profiles/erp-components/FormFooter.md`（記錄分頁器 / 主 CTA / 更多操作 / dirty-guard）
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
- **R2a — DS 不覆寫 PRD 視覺**：PRD 明確指定的視覺 > DS 預設；判斷不確定時，**先信 PRD，再回查 DS**
- **R2b — AI 反射不覆寫 DS 預設**：訓練資料中 Bootstrap / Tailwind UI Kit / generic web app 範例量遠多於企業內部 DS，沒被提醒就走反射預設（outlined input、所有按鈕都帶 icon、操作欄 hover 才浮出、卡片帶 shadow、`<input readonly>` 直接套 disabled 樣式…）。**禁**讓 AI 直覺壓過 DS；每次製作前**先讀 `pitfalls.md §通用 [2026-05-11] AI 預設樣式 ≠ Design System` 的反射對照表**，逐項對照

> **IMPORTANT:** 遇到衝突邊界情境（如「PRD 沒提但舊模組有」、「profile IMPORTANT 與 PRD 衝突」），**必讀 `REFERENCE.md §4 資料來源權重明細`**——一行版規則處理不了的，邊界表會給明確判定。

### 2. 觸發即先確認（缺一不開工）

開工前若使用者沒提供，**主動詢問**：

1. **模組中文名**（決定檔名與 `<title>`）
2. **專案 profile**（如 ERP；cwd 可推斷則略）
3. **來源**（PM 文件路徑 / 同類舊模組 / 純 chat 描述）
4. **輸出路徑**（預設 `prototype/project/<模組中文名>.html`，profile 可覆寫）

> profile 額外要問的項目（如 ERP 的 Odoo model、模組分類、作業檔/設定檔類型）由 profile 內定義。

### 3. 五階段工作流

| 階段 | 動作 |
|---|---|
| 0 | **跨專案複用**：有同類舊模組就抽介面規格直接引用，不重畫 |
| 1 | **規格抽取三段式**（Pass 0 / 1 / 2，詳見下方） |
| 2 | **製作 .html**：複製 profile 指定的 starter template → 替換 App Shell → 建 List View → 建 Form View → 補 Modal / Toast / Empty State |
| 3 | **本機審查**：跑 profile Handoff Checklist + 掃 `pitfalls.md` |
| 4 | **chat handoff**：依 profile 規範交付（ERP 5 項；通用底線 5 項見 REFERENCE） |

> 邊界判定（PRD 沒提但舊模組有 / profile 與 PRD 衝突等）與完整 Examples 見 `REFERENCE.md §5 五階段工作流明細`。

#### 階段 1 三段式：Pass 0 → Pass 1 → Pass 2（核心，**禁略過**）

**Pass 0｜找元件權威來源**

依 profile 規定的優先順序查找：
1. 該模組是否有對應的設計文件元件清單章節？（章節命名因模組不同，profile 會列清單）
   - 有 → 把該章節列出的元件當作**本模組的元件命名單一來源**
   - 無 → 落到 profile 內建的「PRD 元件詞彙 → 實作對照」最小集（如 `erp-transaction.md §PRD 元件對照 Table A`）
2. 記錄本次採用的權威來源（檔名 + 章節），handoff 時要附上

**Pass 1｜純抽取，不選元件 — 輸出五欄 schema 表給使用者確認**

把 PRD 拆成五欄表，**先給使用者校對**再進 Pass 2：

| 欄位中文 | 區塊 | 元件（PRD 標示） | 必填 | 約束 / 關聯 |

規則：
- 「元件」欄若 PRD 有列就照填（如 `TextBox` / `DropDownList`），**禁**自己編
- PRD 沒列就填 `?`，等 Pass 2 推論
- PRD 沒列的欄位 / List 欄 / action **禁止**自動補（R1 PRD 完整性原則）；缺漏即停下來問

> **IMPORTANT:** 略過「輸出 schema 表 → 使用者確認」這一步，等於跳過 PRD 抽取校對，後續若偏差就難救。

**Pass 2｜schema 查表轉實作**

對 Pass 1 確認過的 schema 每行：
- prototype HTML markup：依 profile 對照表查「Prototype HTML」欄
- handoff 對應的 production 元件：填 Pass 0 權威來源裡的元件名（如 `<TextInput>` / `<DataGrid>`）
- 若 Pass 1 元件欄為 `?`，依 profile 的「Form section 元件推論規則」推論並回填，**標記為推論**讓使用者確認

### 4. 通用硬性限制（每次輸出前自檢，違反即重做）

- **IMPORTANT:** 預設 `<html lang="zh-Hant-TW">`（多語環境由 profile 指定）。**Why**：lang 屬性決定瀏覽器字型回退、斷字規則、螢幕閱讀器發音；錯設 `en` 時中文常被誤套西文字型，行高與標點間距整批跑掉
- **IMPORTANT:** CSS 載入順序：design tokens CSS → Material Symbols → `app.css`。**Why**：tokens 必須先載入才能被後續 stylesheet 引用；`app.css` 寫應用層覆寫必須最後，否則 token 變數抓不到值、自訂樣式被 DS 預設蓋掉
- **IMPORTANT:** Vue 3 production CDN，**禁**引入其他 UI library。**Why**：prototype 用途是「reviewer 點一下開檔即試玩」，多加 library 增加下載 / 環境配置成本；視覺由 DS 已涵蓋，多餘 library 反成視覺噪音與整合阻力
- **IMPORTANT:** 樣式寫到 `app.css`、互動寫到 `app.js`，**禁**在 `.html` 內嵌 `<style>` / `<script>`（CDN 與引用 `app.js` 的 `<script src>` 例外）。**Why**：prototype 後續會 port 到 production Vue SFC；三檔分離可直接貼進 `<template>` / `<script>` / `<style scoped>`，內嵌會逼下一手先 untangle
- **IMPORTANT:** Icon variant 依 **Design System** 規定（Athena DS `components.css` / DS README §icon），prototype 不另立規則。**Why**：DS 是 icon 視覺的唯一來源；SKILL / profile / pitfalls 各自下規則會 drift。實作時直接看 DS：font family 用 `material-symbols-outlined` class（DS 指定的字型載入），實心 / 線條由 `font-variation-settings: 'FILL' 0/1` 決定，glyph 名禁用 `_outline` / `_outlined` 字尾（那是舊 Material Icons 慣例，新版 Material Symbols 沒有）
- **IMPORTANT:** 色彩 / 間距 / 圓角 / 陰影 / 字級必須使用 design tokens；**禁** inline hex、**禁** `@apply`。**Why**：prototype → production 重用同一份 token CSS，數值一次對齊；inline hex 失去與 DS 連動，DS 更新時 prototype 顯示舊色；`@apply` 把 token 烘進 component-scoped CSS，反而切斷 token 引用鏈
- **IMPORTANT:** 寬度雙標（語義不同，勿混用）：**1440px** = Figma 設計畫布基準（design ↔ dev 規格對齊用，所有設計稿尺寸以此計）／**1280px** = prototype 預覽 viewport（template `<meta name="viewport" content="width=1280">` 已釘住，reviewer 開瀏覽器即以此寬看）。**不支援** `< 768px`（mobile）。RWD 4 斷點 XL / L / M / S 規格詳見 `REFERENCE.md §8 Responsive`；主要 grid 降欄關鍵斷點為 `@media (max-width: 1024px)` 強制 2 欄

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
2. 載入 `${CLAUDE_SKILL_DIR}/profiles/Shared.md` + `${CLAUDE_SKILL_DIR}/profiles/erp-transaction.md` + `pitfalls.md`；判定類型 = 設定檔則另載 `${CLAUDE_SKILL_DIR}/profiles/erp-setup.md`
3. 依 erp-transaction.md「規格抽取表」從 PM 抽欄位、狀態、關聯、預設搜尋
4. 判斷為作業檔（有狀態流程）→ 複製 `templates/module-page.html`
5. 完成 List/Form View；跑 ERP 作業檔 Handoff Checklist
6. chat handoff 含 ERP 5 項

**關鍵守則**：依 R1（PRD 完整性），PRD 沒列的欄位 / 動作**不自動補**；缺漏就停下來問。

> 另外 2 種輸入情境的完整範例（A：從同類舊模組複製、B：純 chat 描述無正式文件）→ `REFERENCE.md §7 Examples 擴充`
