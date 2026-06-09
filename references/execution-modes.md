# 執行模式完整細節（Execution Modes Reference）

主 SKILL.md 的「執行模式」段只負責**選對模式**；本檔是三種模式各自的完整操作細節。確定模式後，只讀對應的那一節即可。

模式偵測階梯與選用 rubric 見主 SKILL.md — 本檔假設你已經選好模式。

## 三模式速覽

模式依**自己可用的工具**偵測（不嗅探環境變數），與「選了哪個角色」正交 — 任何角色在 Mode 1 都能 spawn 成 subagent。下表只供確認該讀哪一節；完整偵測階梯見主 SKILL.md §執行模式，回退規則見本檔末「回退規則」。

| 模式 | 典型載體 | 怎麼協作 | 適用 |
|------|---------|---------|------|
| **Mode 1** Subagent 編排 | 有 Agent/Task 工具（如 Claude Code） | 每角色獨立 subagent：上下文隔離、三路調研真平行、各讀滿自己的角色檔 | **單向接力**：handoff 鏈、只要結果不要過程對話、1–3 棒短任務 |
| **Mode 2** 單一上下文角色切換 | 無 spawn 能力（如 Claude.ai） | 同一對話依序切換；切換前讀完角色檔全文、每段標明角色；平行調研改依序但不省模板 | 無 spawn 能力時的保底路線 |
| **Mode 3** Agent Team 編排 | 有 team 協作工具（Claude Code + 實驗性 Agent Teams） | 主對話當 team lead（承擔 design-lead 職能）；teammates 可互通訊、共享 task list、dependency 映射含 a11y gate | **角色間需來回討論、互相挑戰**的長流程 |

> 「典型載體」是常見對應，非定義 — 真正判準是工具清單（Claude Code 若未掛 subagent 工具也會落到 Mode 2）。
> **Mode 3 的偵測訊號是 `TeamCreate` 工具**，不是 `SendMessage`：同一個 `SendMessage` 也用於「續跑自己 spawn 的 background subagent（by agentId）」，本身有歧義；`TeamCreate`（建立 team 與其共享 task 板）才是 Agent Team 能力的明確訊號。Agent Teams 工具可能是依情境動態浮現的 deferred tool，第一次沒看到不代表不存在。
> 偵測到 `TeamCreate` 只代表**可用**，非**該用**：升級與否照主 SKILL.md §執行模式的**具體升級清單**判斷（命中清單才升、token 成本高、實驗性），不要用模糊的「不確定」停在 Mode 1。

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

**Team lead 永遠是主對話本身** — `TeamCreate` 的 session 固定是 lead，不可轉移，所以不能把 `design-lead` spawn 成 teammate 來當 lead。正確做法：

1. 主對話開 team 之前，先讀取 `skills/design-lead/SKILL.md`
2. 以 design-lead 的 Mode A（Task Routing）產出 routing 計畫
3. 依計畫建 team、建 task、spawn teammates（實際 API 序列見下節）
4. 全程以 design-lead 的品質標準（Quality Bar checklist）守門

`design-lead` 角色檔在此模式下是「team lead 的操作手冊」，不是一個 teammate。

### 實際操作流程（Agent Teams API）

Mode 3 不是「直接 spawn 幾個 subagent」，而是走 team API。固定序列：

1. **`TeamCreate`** — `team_name` 用 feature-slug（如 `so-allowance`）、`agent_type` 填 `design-lead`。建立後 team 與一個共享 task 板 1:1 綁定，主對話即 `team-lead`。
2. **建 task（`TaskCreate`）** — 依 routing 計畫一次建好該輪全部 task（自動掛到 team 板）。一個角色一個 deliverable = 一個 task，描述附上對應的 `design-run/<feature-slug>/<序號>-<role>.md` 路徑。
3. **建 dependency（`TaskUpdate` 的 `addBlockedBy`）** — 把標準流程箭頭映射成依賴；a11y gate 在此機械化（見下節）。
4. **spawn teammate** — 用 **`Agent` 工具帶 `team_name` + `name` 參數**（不是另一個 spawn 工具）。`name` 用角色名（如 `requirement-analyst`）、`subagent_type` 依該角色需要的工具選（純調研角色可用唯讀 agent，需產檔的用 general-purpose）。prompt 用下方模板指定讀角色檔。**要角色間自主來回（peer SendMessage 互答）時，spawn 須設 `run_in_background: true`**（理由見下方「存活性模型」）。
5. **指派 task（`TaskUpdate` 的 `owner`）** — 把 task 指給對應 teammate 名字；並行段可讓 teammates self-claim。
6. **協作期間** — teammates 完成各自 turn 後會 idle 並自動回報（訊息自動送達，**不需主動查信箱**）；需要跨角色問定義時用 peer **`SendMessage`（`to` 填 teammate 名字）**。
7. **收尾** — 全部完成後，對每個 teammate 發 `SendMessage` `{type: "shutdown_request"}` 優雅關閉，待全員關閉後 **`TeamDelete`** 清掉 team 與 task 板。

> teammate idle 是正常狀態（每個 turn 結束都會 idle），**不是錯誤、也不代表它做完或不可用**。不要因為它 idle 就急著評論或補位。

### 存活性模型（實測，關鍵）

teammate 跑完一個 turn 後的存活狀態，取決於 spawn 時有沒有 `run_in_background: true`：

- **`run_in_background: true`（live 背景成員）** → teammate 持續存活，收到 peer `SendMessage` 會**自動喚醒**跑下一輪、完成後自動通知 lead。**角色間需自主來回的情景（critique 辯論、下游回問上游）必須用這個**，否則 peer 互答不會自己接通。
- **同步 spawn（未設 background）** → teammate 跑完第一輪即「no active task」進入 inactive。**peer 訊息只會進它的信箱，不會自動觸發它跑下一輪** → 整串往返會**停滯**。此時 lead 必須用 **`SendMessage` 帶 `to: <agentId>`（spawn 結果回傳的 id）顯式 resume**，它才會在背景續跑。實測：下游發出 peer 提問後雙方都 inactive、deliverable 卡住，lead resume 後才完成。

> 經驗法則：**接力型**（上游做完才換下游、不需中途互問）同步 spawn 即可，lead 依序推進；**討論型**（需 peer 即時互答）一律 `run_in_background: true`。lead 卡住處理的第一步「nudge」對 inactive teammate 要用 **agentId resume**，光對 name 發訊息只會進信箱。

### 工具斷線的韌性（實測）

teammate 的 team 協調工具（`TaskUpdate` / `SendMessage`）可能在任務中途斷線。因此：
- **deliverable 一律先落盤**（檔案是持久真相來源）；teammate 寫完 `design-run/<feature-slug>/<序號>-<role>.md` 後再做標 task / 發訊息的程序步驟。
- teammate 若在落盤後失去工具、無法自標 completed 或通知 lead → **lead 負責補做**（`TaskUpdate` 標 completed、必要時代為周知）。實測 ux-writer 寫完 02 後工具斷線，由 lead 補標 task completed。

### Teammate spawn prompt 模板

這是上節步驟 4「spawn teammate」時，傳給 `Agent` 工具的 prompt 內容（搭配 `team_name` + `name` 參數）。teammate 不繼承主對話歷史，所以模板必須明確指示讀角色檔與輸入來源：

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

> 取捨說明：本 skill 的角色檔是 Agent Skill（`skills/<role>/SKILL.md`），**不是** Claude Code 的 subagent definition。**`subagent_type` 與「角色身份」是兩件正交的事**：
> - `subagent_type`（如 `general-purpose` / `Explore`）只決定 teammate 拿到哪些**工具**（要產檔的用 general-purpose，純調研可用唯讀 agent）。
> - **角色身份**（requirement-analyst、ux-writer…）來自 prompt 指定讀 `skills/<role>/SKILL.md`，**不靠** subagent definition。
>
> 所以不要試圖把 14 個角色各做成 subagent definition 來當 teammate type — teammate 不套用 subagent frontmatter 的 `skills` 欄位，「通用 teammate（依工具需求選 `subagent_type`）+ prompt 指定讀角色檔」才是穩定路線，且與 Mode 1 模板一致。

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
1. nudge 該 teammate — **live 背景成員**對 name 發 `SendMessage` 即可喚醒；**inactive（同步 spawn）成員**要用 `SendMessage` 帶 `to: <agentId>` resume，光對 name 發訊息只進信箱不會跑。需要時 lead 直接把上游答覆 relay 給下游，讓它定案。
2. 無效 → spawn 替補 teammate 接手該 task（讀同一角色檔 + 同樣輸入）
3. 仍無效 → 該段降級為 lead 自行以 Mode 1 subagent 補完

---

## 回退規則（三模式通用）

- 偵測不到 `TeamCreate` → **靜默退回 Mode 1**，不要嘗試呼叫不存在的工具、不要中斷任務。可附一句：「目前未偵測到 Agent Teams（需 `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`），本次以 subagent 編排執行，產出與品質不受影響。」
  - 但注意：Agent Teams 工具可能是**依情境動態浮現**的 deferred tool。若任務確實命中升級清單、值得用 team，可先嘗試載入 `TeamCreate` 再判定，而非因第一眼沒看到就放棄。
- 偵測不到 Agent tool → 退回 Mode 2。
- 無論哪個模式，產出一律遵循 `design-run/<feature-slug>/` 編號檔案慣例 — 模式只改變「怎麼協作」，不改變「交付什麼」。
