# Prototyper Skill

> 把規格 / 同類舊模組 / chat 描述，**一步到位**轉成 reviewer 可直接試玩的單檔 HTML prototype。

本檔是 prototyper skill 的入口文件。深入細節請依本檔指引導向 `SKILL.md` 與各 supporting file。

---

## 何時使用

### 自動觸發

使用者輸入以下任一語句時，Claude 會自動載入本 skill：

- 「幫我做一個 XXX 模組的 prototype」
- 「這份 PM 文件 [path] 轉成 prototype」
- 「參考 [既有模組].html 做一個類似的 [新模組]」
- 「讀取此份 PRD 並產出互動功能及前端頁面」
- 使用者貼上 Figma frame 並要求轉成可互動畫面

### 手動觸發

直接輸入 `/prototyper` 指令。

### 何時「不要」用

- 要產出 production code（Vue SFC / Odoo Python）→ 用其他 skill
- 要做 Figma 動效或互動原型 → 用 `figma-use` / `figma-generate-design`
- 只要解析 PRD、找出需求缺口 → 用 `requirement-analyst`

---

## 產出物

- **單檔 `.html`**：Vue 3 production CDN，無 build step，雙擊即可在瀏覽器開啟
- **List View + Form View 雙視圖**：toolbar / search / grid / pager + summary card / sections / tabs / footer
- **DS-correct 預設樣式**：色彩 / 間距 / 圓角 / 字級全部用 design tokens，無 inline hex、無 `@apply`
- **Modal / Toast / Empty State**：confirm / deeplink modal + success / warning / error toast 已內建

---

## 快速開始

### 場景 1 — 從 PM 文件

```
這份 PM 文件 docs/notion/銷貨/銷售訂單.md 轉成 prototype
```

Skill 會：
1. 載入對應專案 profile（cwd 推斷為 ERP）
2. 依 profile 規格抽取表從 PM 抽欄位 / 狀態 / 關聯 / 預設搜尋條件
3. 判斷類型（作業檔 vs 設定檔）→ 複製對應 starter template
4. 產出 `prototype/project/銷售訂單.html`

### 場景 2 — 從同類舊模組複製

```
參考 prototype/project/地點設定檔.html 做一個區域設定檔
```

Skill 會：
1. 從舊 `.html` 抽出介面規格（欄位 / 狀態 / 關聯）
2. 判斷為設定檔 → 複製 `templates/setup-page.html`
3. 套用設定檔特化規則 + 資料狀態矩陣
4. 產出新模組 `.html`

### 場景 3 — 純 chat 描述

```
幫我做一個請假申請的 prototype
```

Skill 會**先反問**：模組中文名、模組分類、Odoo model、單據類型（作業檔 / 設定檔）等。確認後才開工——**禁止**自動補 PRD 沒列的欄位。

---

## 目錄結構

```
skills/prototyper/
├── README.md           ← 本檔
├── SKILL.md            Skill 核心：四層權重 / 五階段工作流總覽 / 通用硬性限制（slim per official Skill spec）
├── REFERENCE.md        詳細展開：權重邊界、Pass 0-1-2 細節、Examples、token 對照、元件對照、app.js 起手式
├── pitfalls.md         反覆出現的審查問題（dated entries，第 2 次以上才寫入）
├── profiles/
│   ├── Shared.md       跨專案頁面框架（Header 56px / Nav-rail 72px / Info Bar 28px）
│   └── erp.md          ERP 專屬：State Machine / Summary Bar / Stepper / Smart Bar / DataGrid / Form Footer / 設定檔特化
└── templates/
    ├── module-page.html 作業檔 starter（含狀態流程）
    └── setup-page.html  設定檔 starter（master data，僅 active）
```

---

## 載入順序（Progressive disclosure）

| 載入時機 | 檔案 | 用途 |
|---|---|---|
| **永遠** | `SKILL.md` | 核心規範與工作流總覽，每次呼叫必載 |
| **永遠** | `profiles/Shared.md` | 頁面框架基底，所有專案前置必讀 |
| **依專案** | `profiles/<project>.md` | 專案專屬覆寫（ERP 即 `erp.md`） |
| **按需** | `pitfalls.md` | 每次製作前掃一眼，避免重蹈覆轍 |
| **按需** | `REFERENCE.md` | 需要工作流明細 / token / 元件對照 / `app.js` 範本時查 |
| **按需** | `templates/*.html` | 階段 2「複製 starter」時讀取 |

---

↑ 使用者導向　／　↓ 維護者導向

---

## 擴充新專案 profile

未來要服務 ERP 以外的專案時：

1. **複製 `profiles/erp.md` 為起點**，重新命名為 `profiles/<新專案>.md`
2. **補完共用 profile §「專案 profile 應補完」6 項**：
   - Nav-rail 項目清單（key / icon / label / 順序、產品縮寫）
   - Breadcrumb 層級結構
   - ProgramID 格式與範例
   - 版號格式
   - Class prefix 覆寫（如不沿用 `.erp-*`）
   - 任何結構覆寫的理由
3. **在 `SKILL.md` §支援檔案 加引用**，列出新 profile 與觸發條件
4.（可選）**為該專案在 `pitfalls.md` 開新區段**（如 `## <新專案> profile`），累積該專案反覆出現的審查問題

---

## 維護指引

### Pitfalls 累積規則

`pitfalls.md` 用 dated entries 累積反覆出現的審查問題。寫入規則：

- **一次性誤解** → 對話內修正即可，**不寫入**
- **第 2 次以上犯同樣錯** → 必須寫入；含三欄位「症狀 / 正確做法 / 為什麼會反覆犯」
- **過時** → 直接刪除，**不保留歷史記錄**（這不是 changelog）
- 寫入前先檢查是否有同類條目可合併

### Templates 三處同步

`templates/module-page.html` 是 starter 骨架，**同一份內容在三個位置維護**——改一處務必同步另兩處：

1. `Athena-designteam/skills/prototyper/templates/module-page.html`（GitHub canonical）
2. ERP repo `.claude/rules/prototype-design/templates/module-page.html`
3. ERP repo `.claude/rules/prototype-design/PRODUCE.md` 附錄 A

`~/.claude/skills/prototyper/templates/module-page.html` 是 user-level 部署副本，**從 GitHub bundle 同步，不是獨立維護目標**。

### GitHub 同步流程

User-level 副本（`~/.claude/skills/prototyper/`）是部署副本，**真實 source of truth 在 GitHub bundle**：
`https://github.com/Jessie-athena/athena-design-team/tree/main/skills/prototyper`

修改後同步流程：

```bash
# 1. 取得（或更新）本地 clone
git clone https://github.com/Jessie-athena/athena-design-team.git
# 或：cd athena-design-team && git pull

# 2. 把 user-level 改動複製到 clone
cp -r ~/.claude/skills/prototyper/. athena-design-team/skills/prototyper/

# 3. Commit + push
cd athena-design-team
git add skills/prototyper
git commit -m "skills(prototyper): <一句話描述>"
git push
```

**反向同步**（GitHub bundle 有更新、要拉回 user-level）：

```bash
cd athena-design-team && git pull
cp -r athena-design-team/skills/prototyper/. ~/.claude/skills/prototyper/
```

> 多人協作時，每次修改前先 `git pull`，避免覆蓋他人未同步的改動。

---

## 相關資源

- [athena-design-team 根 README](https://github.com/Jessie-athena/athena-design-team) — 整個設計團隊定位與 14 角色
- ERP repo `.claude/rules/prototype-design/CLAUDE.md` — Prototype 審查規範（與本 skill 互為對照）
- ERP repo `.claude/rules/prototype-design/PRODUCE.md` — Prototype 製作 SOP（含 starter template 附錄）
- Athena Design System bundle — 視覺 token 與元件樣式來源（URL 與 README 路徑詳見 `profiles/erp.md §視覺系統來源`）
