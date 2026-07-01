# PRD 撰寫師 — AI Skill（prd-writer）

從一句話需求、口頭描述或不完整的 brief，起草**完整的產品功能 PRD（11 章節格式）**；
也能補全現有 PRD 的缺漏章節，或從已有設計稿 / prototype 反向補寫 PRD。

> 適用於任何產品領域（B2B SaaS、Mobile App、ERP 模組、消費品平台等），對 Athena ERP 的
> PSI / AC 業務單據另有專屬慣例（見 `references/psi-patterns.md`）。

---

## 這是什麼？

一個獨立的 Claude AI Skill，職責單純：**把 brief 變成可執行的 11 章節 PRD 草稿**。
它原本是 `athena-design-team` skill pack 的一個角色，現獨立成單一 skill repo，
可單獨安裝使用，也可與 `athena-design-team` 並存（見下方「與 athena-design-team 的關係」）。

- **繁中輸出**、技術術語保留英文
- PRD 全文以中文業務名稱表述功能 / 欄位 / 狀態 / 動作 / 錯誤情境，**不出現任何程式代號**
  （程式 ID、資料模型 / 欄位名、method 名、錯誤碼 key、CSS class、設定參數鍵）

---

## 三種工作模式

| 模式 | 觸發情境 | 產出 |
|------|---------|------|
| **A — 全新 PRD** | 只有一句話描述 / brief，尚無 PRD | 完整 11 章節 PRD |
| **B — 補全章節** | 現有 PRD 缺某幾章（常見 §6 / §9 / §5） | 只補缺章，不重寫全文 |
| **C — 反向補寫** | 已有 prototype / mockup / 設計稿，PRD 未完整 | 從設計稿回推規格 |

11 章節：§1 文件目的、§2 背景、§3 目標、§4 詞彙定義、§5 使用者故事與驗收條件、
§6 介面與流程、§7 限制條件與範圍、§8 權限與稽核、§9 資料欄位規則、§10 錯誤訊息文案、§11 版本紀錄。

---

## 觸發時機

只要任務是「**產生 PRD 文字**」就會觸發此 skill，即使使用者沒說「PRD」這個詞。範例指令：

- 「幫我寫 XXX 模組的 PRD」
- 「幫我把這個功能寫成規格」
- 「補完這份文件的介面章節（§6）」
- 「我有一個新功能需求想落成文件」
- 「從這個 prototype 反向補一份 PRD」

---

## 安裝

詳見 **[SETUP.md](./SETUP.md)**。快速版：

```bash
# Claude Code（專案層級或全域）
git clone <this-repo> ~/.claude/skills/prd-writer
# 重啟 Claude Code 後即會依任務自動觸發
```

`SKILL.md` 的 frontmatter `name: prd-writer` 必須與資料夾名一致（已是）。
`references/` 內的兩份慣例檔以相對路徑被 `SKILL.md` 引用，**整個資料夾一起安裝即可**。

---

## 倉庫結構

```
prd-writer/
├── SKILL.md                       ← 角色定義（角色定位、三模式、11 章節模板、自檢清單）
├── references/
│   ├── general-patterns.md        ← 通用 PRD 慣例（整合自 daydayding 文件規則 + masonailab AI PM 實踐）
│   └── psi-patterns.md            ← Athena ERP PSI / AC 業務單據專屬慣例
├── evals/
│   ├── evals.json                 ← 3 個觸發 + 品質評測（全新 PRD / 補 §6 / 補 §9-§11）
│   └── fixtures/
│       └── prd-purchase-deduction-partial.md
├── README.md                      ← 本檔
├── SETUP.md                       ← 安裝與設定
└── .gitignore
```

---

## 與 athena-design-team 的關係（並存可選）

prd-writer 是「**生產者**」，起草 PRD 草稿；`athena-design-team` 的 `requirement-analyst` 是
「**消費者**」，驗核 gap 與風險。兩者可並存安裝：

```
（外部 prd-writer skill 起草 PRD、落盤）
  → athena requirement-analyst（gap 驗核）
  → interaction-designer / prototyper → …
```

prd-writer 不依賴 athena 也能單獨運作；athena 端的 `requirement-analyst` 仍把
prd-writer 起草的 PRD 列為上游輸入之一。

---

## 評測

```bash
# 用 skill-creator / run_loop 等工具跑 evals/evals.json
```

⚠️ 觸發率驗證請以**真實 `.claude/skills/` 安裝**為準（harness 模擬常系統性低估觸發率）。

---

## 創建者與維護者

| 角色 | 姓名 |
|------|------|
| 創建者 & 維護者 | Jessie Chen |
