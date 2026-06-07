# 執行模式完整細節（Execution Modes Reference）

主 SKILL.md 的「執行模式」段只負責**選對模式**；本檔是三種模式各自的完整操作細節。確定模式後，只讀對應的那一節即可。

模式偵測階梯與選用 rubric 見主 SKILL.md — 本檔假設你已經選好模式。

---

## Mode 1 — Subagent 編排（有 Agent tool）

每個角色以獨立 subagent 執行。好處：上下文隔離（角色不互相污染）、調研可真平行、每個角色都讀滿自己的完整定義。適合**單向接力**的流程 — 上一棒產出就是下一棒輸入，中途不需要回頭討論。

### Subagent prompt 模板

```
你是 Athena 產品設計團隊的 <role>。
1. 先完整讀取 <此 skill 的絕對路徑>/skills/<role>/SKILL.md，
   依其角色定位、工作模式、輸出格式與自檢清單執行。
2. 任務：<具體任務描述>
3. 上游輸入：<上游角色產出的檔案路徑，或內嵌內容>
4. 將產出寫入：<工作目錄>/<序號>-<role>.md
5. 輸出語言：繁體中文為主，技術術語保留英文。
```

### 注意事項

- **Subagents 之間不共享對話上下文** — 上游產出必須以檔案路徑或內嵌內容明確傳遞，不能假設下游「看得到」前面的對話。
- 工作目錄慣例：`design-run/<feature-slug>/`，每個角色產出一個編號檔案（如 `01-requirement-analyst.md`、`02a-ux-researcher.md`），讓任何角色都能往回追溯。
- 三路調研（`ux-researcher` + `market-insight-analyst` + `data-analyst`）**在同一回合 spawn 三個 subagents 平行執行**，全部完成後再餵給 `product-strategist`。
- 順序型角色（策略 → 互動 → 視覺 → 文案）依序 spawn，把前一棒的產出路徑傳給下一棒。

---

## Mode 2 — 單一上下文角色切換（無任何 spawn 能力）

在同一對話中依序切換角色。規則：

- 切換角色前**先讀取該角色的 SKILL.md 全文**，再開始扮演。
- 每段產出開頭標明目前角色（如 `## 🔎 requirement-analyst`），讓使用者知道現在是誰在說話。
- 「平行調研」改為依序執行，但每個角色仍須完整走完自己的輸出模板，不可因為前面已有內容而省略。
- 一次只扮演一個角色，產出完成才換下一個 — 混合視角會讓輸出失去角色該有的深度。

---

## Mode 3 — Agent Team 編排（有 team 協作工具）

Teammates 各自擁有獨立完整上下文、可**互相通訊**（SendMessage）、共享 task list、自行認領任務。適合**角色之間需要來回討論、互相挑戰、迭代協作**的長流程 — 這是它與 Mode 1「承包商單向回報」的本質差異。

> Token 成本顯著高於 Mode 1，且為實驗性功能（task 狀態可能延遲、resume 不還原 teammates）。只有當「角色間來回討論」本身會提升品質時才值得。

### Team lead 機制（重要）

**Team lead 永遠是主對話本身** — 建立 team 的 session 固定是 lead，不可轉移，所以不能把 `design-lead` spawn 成 teammate 來當 lead。正確做法：

1. 主對話開 team 之前，先讀取 `skills/design-lead/SKILL.md`
2. 以 design-lead 的 Mode A（Task Routing）產出 routing 計畫
3. 依計畫建 task、spawn teammates
4. 全程以 design-lead 的品質標準（Quality Bar checklist）守門

`design-lead` 角色檔在此模式下是「team lead 的操作手冊」，不是一個 teammate。

### Teammate spawn prompt 模板

Teammate 不繼承主對話歷史，所以模板必須明確指示讀角色檔與輸入來源：

```
你是 Athena 產品設計團隊的 <role>，team 內名字是 <teammate-name>。
1. 先完整讀取 <此 skill 的絕對路徑>/skills/<role>/SKILL.md，
   依其角色定位、工作模式、輸出格式與自檢清單執行。
2. 任務：<具體任務描述>
3. 上游輸入：design-run/<feature-slug>/ 下的 <編號檔案>（請先讀）。
4. 正式產出寫入：design-run/<feature-slug>/<序號>-<role>.md，
   完成後把對應 task 標為 completed。
5. 需要其他角色的定義或決策時（例如某個 state 的觸發條件），
   用 SendMessage 直接問該角色的 teammate，不要自己臆測。
6. 輸出語言：繁體中文為主，技術術語保留英文。
```

> 取捨說明：本 skill 的角色檔是 Agent Skill（`skills/<role>/SKILL.md`），**不是** Claude Code 的 subagent definition。不要試圖把角色包裝成 subagent type 來 spawn teammate — teammate 不套用 subagent frontmatter 的 `skills` 欄位，「通用 teammate + prompt 指定讀角色檔」才是穩定路線，且與 Mode 1 模板一致。

### Task list ↔ 設計流程映射

- **誰建 task**：team lead（主對話）依 routing 計畫一次建立該輪全部 task。
- **粒度**：一個角色一個 deliverable = 一個 task，task 描述附上對應的 `design-run/` 檔案路徑。
- **Dependency**：把標準流程的箭頭直接映射成 task dependency。以「新功能完整設計」為例：
  - `requirement-analyst`（無依賴）
  - 三路調研各一個 task，皆依賴 requirement-analyst → 三者互不依賴，**並行 self-claim**
  - `product-strategist` 依賴三路調研
  - `interaction-designer → ui-designer → ux-writer` 依序依賴
  - `accessibility-reviewer` 依賴**所有 user-facing 產出** — 「a11y 是一等公民」原則在此機械化為 dependency gate，handoff 的 task 過不了 a11y 就不會解鎖
- **認領**：並行段讓 teammates self-claim（task 有 file-lock 防搶）；順序段由 lead assign 給對應角色。

### 通訊慣例（避免 mailbox 變雜訊）

- **問定義 / 拿決策才發訊息**：例如 `ux-writer` 需要某 error state 的觸發條件 → SendMessage 問 `interaction-designer`，而非自行假設。
- **正式產出走檔案，不走訊息**：訊息只放結論與指路；完整 deliverable 一律寫進 `design-run/<feature-slug>/<序號>-<role>.md`。理由：task 狀態可能延遲、resume 不還原 teammates — 檔案才是持久的真相來源，且三模式產出結構因此完全一致。
- **挑戰式協作**（critique、多假設對撞）才需要角色間來回辯論；接力式產出不必互發訊息。
- 完成即標 task completed，讓下游 dependency 自動解鎖。

### 卡住處理

teammate 卡住或 task 久滯 in-progress 時，lead 依序：
1. SendMessage nudge 該 teammate
2. 無效 → spawn 替補 teammate 接手該 task（讀同一角色檔 + 同樣輸入）
3. 仍無效 → 該段降級為 lead 自行以 Mode 1 subagent 補完

---

## 回退規則（三模式通用）

- 偵測不到 team 協作工具 → **靜默退回 Mode 1**，不要嘗試呼叫不存在的工具、不要中斷任務。可附一句：「目前未偵測到 Agent Teams（需 `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`），本次以 subagent 編排執行，產出與品質不受影響。」
- 偵測不到 Agent tool → 退回 Mode 2。
- 無論哪個模式，產出一律遵循 `design-run/<feature-slug>/` 編號檔案慣例 — 模式只改變「怎麼協作」，不改變「交付什麼」。
