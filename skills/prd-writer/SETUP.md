# ⚙️ 安裝與設定指南 — prd-writer

## 1. 前置需求

- [Claude.ai](https://claude.ai) 帳號 **或** Claude Code CLI
- （選配）Notion workspace — 若要把 PRD 直接寫進 Notion

---

## 2. 安裝方式

### 方式 A：Claude Code

1. Clone 這個 repo 到 Claude Code 的 skills 路徑（保持目錄結構，`SKILL.md` 以相對路徑
   `references/<檔名>.md` 引用慣例檔）：
   ```bash
   git clone <this-repo> ~/.claude/skills/prd-writer      # 全域
   # 或專案層級：<project>/.claude/skills/prd-writer
   ```
2. 重啟 Claude Code
3. 開新對話，輸入「幫我起草 XXX 模組的 PRD」即會自動觸發

### 方式 B：Claude.ai 自訂 Skill

1. Clone 這個 repo
2. 進入 Claude.ai → Settings → Custom Skills
3. 將整個 `prd-writer/` 資料夾（含 `SKILL.md` 與 `references/`）上傳為一個 Skill

> **沒有對接任何 MCP 也能使用** — 純文字輸出模式下完整運作。

---

## 3. （選配）與 athena-design-team 並存

若你也安裝了 `athena-design-team`，兩者可並存：prd-writer 起草 PRD，athena 的
`requirement-analyst` 接手 gap 驗核。**安裝順序不限**，兩個 skill 各自獨立觸發；
prd-writer 不會被 athena 的 subagent 編排以路徑方式 spawn（它是獨立 skill，自行觸發）。

---

## 4. 驗證安裝

開新對話輸入：

```
幫我起草一個「銷貨退回原因設定檔」模組的完整 PRD
```

預期：skill 觸發後產出 11 章節 PRD（設定檔模式：無 Summary Card / Stepper / Smart Bar）。
也可測模式 B：「補完這份 PRD 的 §6 介面與流程」。

---

## 5. 評測

`evals/evals.json` 內含 3 個案例（全新 PRD / 補 §6 / 補 §9-§11）。
跑評測時 fixture 路徑為相對路徑 `evals/fixtures/`，從 repo 根目錄執行即可解析。

> ⚠️ 觸發率以**真實 `.claude/skills/` 安裝**驗證為準，不要只信 harness 模擬分數。

---

## 6. 疑難排解

| 問題 | 解法 |
|------|------|
| Claude 不認得 skill | 確認 `SKILL.md` 的 frontmatter `name:` 為 `prd-writer`，且與資料夾名一致 |
| 觸發但沒讀慣例檔 | 確認 `references/general-patterns.md`、`references/psi-patterns.md` 與 `SKILL.md` 同層的 `references/` 內 |
| 想指定模式 | 對話中明說：「用模式 B 只補 §6，不要重寫全文」 |
| 輸出中英混雜過重 | 補：「輸出語言：繁中為主，僅保留 spec / 元件名的英文」 |
