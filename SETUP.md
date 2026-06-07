# ⚙️ 安裝與設定指南

## 1. 前置需求

- [Claude.ai](https://claude.ai) 帳號（建議 付費 方案）**或** Claude Code CLI
- Figma 帳號（含 Dev Mode，供 design-system-architect 與 design-ops 使用）
- Notion workspace（研究筆記、PRD、Handoff doc）
- GitHub access（讀 issue / PR）

---

## 2. 安裝方式

### 方式 A：Claude.ai 自訂 Skill
1. Clone 這個 repo
2. 進入 Claude.ai → Settings → Custom Skills
3. 將 `skills/` 下每個子資料夾上傳為一個 Skill
4. 將頂層 `SKILL.md` 上傳為主調度 Skill

### 方式 B：Claude Code
1. Clone 這個 repo 到本機
2. 整包複製到你的 Claude Code skills 路徑（保持目錄結構，主 SKILL.md 會用相對路徑 `skills/<role>/SKILL.md` 指引 Claude 讀取角色檔）：
   ```bash
   cp -r <repo> ~/.claude/skills/athena-design-team
   ```
3. 重啟 Claude Code
4. 在 Claude Code 中，主 skill 觸發後會以 **subagent 編排模式**執行（角色上下文隔離、調研平行），詳見 README「執行架構」

---

## 3. MCP 整合（選配）

以下 MCP 讓對應 Skill 可直接操作外部工具。沒有 MCP 也完全可以使用，所有 Skill 在純文字輸出模式下同樣能運作。

### Figma MCP
**用途**：讀取 design file、token、component、prototype 連結
**對應角色**：`ui-designer`, `interaction-designer`, `prototyper`, `design-system-architect`, `design-ops`
**設定**：
- 在 Claude 裡連結 Figma MCP
- 確認可以存取你的 Figma team workspace
- 建議開啟 Dev Mode，讓 `design-ops` 可讀取 spec

### Notion MCP
**用途**：建立研究報告、PRD 分析、handoff doc
**對應角色**：`requirement-analyst`, `ux-researcher`, `market-insight-analyst`, `design-ops`
**設定**：
- 在 Claude 裡連結 Notion workspace
- 至少授予 **Product Design** 與 **Research** 資料庫的讀寫權限

### GitHub MCP
**用途**：讀取 issue / PR、建立 design-related issue
**對應角色**：`requirement-analyst`, `design-ops`
**設定**：
- 在 Claude 裡連結 GitHub，授予 repo 讀取權限
- 若要建 issue，須授予 write 權限

---

## 4. 建議 Notion 資料庫結構

供 `design-ops` 與 `requirement-analyst` 寫入時使用：

### Product Design DB
| 欄位 | 類型 | 說明 |
|------|------|------|
| Feature | Title | 功能名稱 |
| Status | Select | Discovery / Design / Handoff / Shipped |
| Surface | Multi-select | Web / App / 跨平台 |
| Figma Link | URL | 主設計檔 |
| PRD Link | URL | PRD 原檔 |
| GitHub Issue | URL | 對應 issue |
| Designer | Person | 主設計師 |
| a11y Reviewed | Checkbox | 是否通過 a11y 審查 |

### Research DB
| 欄位 | 類型 | 說明 |
|------|------|------|
| Title | Title | 研究主題 |
| Type | Select | Generative / Evaluative / Market / Data |
| Method | Select | Interview / Usability / Survey / Analytics |
| Participants | Number | 受訪者數 |
| Date | Date | 執行日期 |
| Findings | Relation | 連到洞察卡片 |

---

## 5. 驗證安裝

安裝完成後，開新對話輸入：

```
@design-lead 幫我確認所有 14 個角色都載入完成
```

`design-lead` 應該回應完整的 14 個角色清單，並詢問你目前的任務是什麼。

---

## 6. 疑難排解

| 問題 | 解法 |
|------|------|
| Claude 不認得角色名稱 | 確認 SKILL.md 的 frontmatter `name:` 欄位與資料夾名稱一致 |
| 角色觸發錯誤 | 在對話開頭明確指定：「用 `ux-researcher` 處理」 |
| MCP 存取失敗 | 到 Claude settings 重新連結對應工具 |
| 輸出中英混雜過重 | 在對話中補：「輸出語言：繁中為主，僅保留 spec / component / token 的英文」 |
