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

### 啟用前提

Mode 3 是 Claude Code 的實驗性功能，要同時滿足兩件事 team 工具才會出現：

1. **旗標**：`CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`。建議設在 user-level `~/.claude/settings.json`（本機所有專案生效）；只想單一專案啟用則設在該專案 `.claude/settings.local.json`（gitignored）：
   ```json
   { "env": { "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1" } }
   ```
2. **版本**：teammate 要能掛入 team，`Agent` 工具 schema 須有 `team_name`/`name` 參數 — **已知 Claude Code 2.1.170 可用**（2026-06-10 實測 teammate 掛入 + peer 互答 PASS）；更舊版本即使旗標生效也只能 spawn 孤立 subagent（見下方「環境相依」）。偵測失敗時先 `claude --version` 確認並更新。

> ⚠️ **環境相依（實測 2026-06-10，同日兩種環境結果相反，spawn 前先偵測）**：teammate 端能力取決於 **`Agent` 工具 schema 是否有 `team_name` / `name` 參數**（已知 2.1.170 起有；偵測失敗優先更新 Claude Code）。
> - **有（新版環境，peer 互答實測 PASS）**：帶 `team_name` + `name` spawn 的 teammate 會真正掛入 team（config `members` 可見、agentId 形如 `<name>@<team>`）；peer `SendMessage` 雙向直達、會喚醒 idle 的對方，全程不經 lead；lead 可從 idle 通知的 summary 看到 peer DM 摘要。**唯一前提：teammate 的 team 工具（`SendMessage` / `TaskList` / `TaskUpdate`）非原生，spawn prompt 必須指示它用 ToolSearch（`select:SendMessage,TaskList,TaskUpdate`）載入**。
> - **沒有（舊版環境）**：spawn 出的只是孤立 subagent — 掛不進 team（`members` 只剩 `team-lead`）、ToolSearch 也查無 team 工具 → peer 互答不可行，**所有跨角色協作一律 lead-mediated**（lead 派工、lead relay 上下游問答、靠上游落盤檔案當管道），Mode 3 實質 ≈「Mode 1 + 只有 lead 能寫的 task 板」。
> - **驗收教訓**：判斷 peer 訊息是否真的送達，**只信工具回傳原文**（`success` / `routing.target`）與雙側獨立落盤的交叉證據，**不信 agent 自述**「我已用 SendMessage 確認」——曾有 teammate 在根本沒有該工具的環境寫出這句話。

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
4. **spawn teammate** — 用 **`Agent` 工具帶 `team_name` + `name` 參數**（⚠️先確認 schema 有此二參數，見本節開頭「環境相依」；沒有就退 lead-mediated 形態）。`name` 用角色名（如 `requirement-analyst`）、`subagent_type` 依該角色需要的工具選（需產檔用 general-purpose、純調研可用唯讀 agent）。prompt 用下方模板指定讀角色檔，**並指示用 ToolSearch 載入 team 工具**（非原生，不載就發不了訊息）。需要角色間自主來回時 spawn 設 `run_in_background: true`（實測 background teammate idle 後可被 peer 訊息喚醒）。
5. **指派 task（`TaskUpdate` 的 `owner`）** — lead 指派給對應 teammate 名字；teammate 載入 `TaskUpdate` 後亦可 self-claim（工具實測可載入可呼叫；載入失敗就由 lead 代為）。
6. **協作期間** — teammates 完成各自 turn 後會 idle 並自動回報（訊息自動送達，**不需主動查信箱**）；跨角色問定義用 peer **`SendMessage`（`to` 填 teammate 名字）**，直達且會喚醒對方；peer 管道不可用（環境相依）時退 lead relay ＋上游落盤檔案。
7. **收尾** — 全部完成後，對每個 teammate 發 `SendMessage` `{type: "shutdown_request"}` 優雅關閉，待全員關閉後 **`TeamDelete`** 清掉 team 與 task 板。

> teammate idle 是正常狀態（每個 turn 結束都會 idle），**不是錯誤、也不代表它做完或不可用**。不要因為它 idle 就急著評論或補位。

### 存活性與 peer 互答（實測 2026-06-10）

- **新版環境（`Agent` 有 `team_name`/`name`）＋ `run_in_background: true`，實測 PASS**：teammate 每個 turn 結束會 idle；**peer `SendMessage` 自動送達並喚醒 idle 的對方**接續跑下一輪 — 實測 peer-b 發問 → 喚醒 peer-a → peer-a 直接回覆 → 喚醒 peer-b，全程零 lead 中轉。**需要角色間自主來回的情景一律 `run_in_background: true` spawn**。
- **同步 spawn（未設 background）在新版環境的存活行為未重測** — 別賭：要 peer 互答就用 background。對 inactive 成員，lead 可用 `SendMessage` 帶 `to: <agentId>`（spawn 回傳的 id）顯式 resume。
- **舊版環境（`Agent` 無此二參數）**：teammate 沒有任何 team 工具，`run_in_background` 只決定孤立 subagent 同步/非同步回傳，與 peer 互答無關。跨角色問答只能 lead-mediated：teammate 把問題寫進 deliverable／final report → **lead 用上游落盤檔案或自己的 `SendMessage` relay 給下游**（或讓下游直接讀上游已落盤的 `<序號>-<role>.md`）。
- **檔案式 handoff 無論哪個環境都是可靠底線**：實測 teammate / 孤立 subagent 都能完整落盤、靠 final report 回報；下游缺上游決策時**正確拒絕臆測、保留占位**等補。

> 經驗法則：設計依賴 teammate 互發訊息的流程前，**先過「環境相依」偵測**；過不了就把跨角色依賴拆成「上游先落盤 → 下游讀檔／lead relay」的順序接力，「多方即時辯論」改由 lead 逐一 spawn、收齊產出後自己綜整仲裁。

### 落盤先行（實測，兩種環境皆成立）

teammate 的 team 協調工具**不保證可用**（舊版環境完全沒有；新版也要 ToolSearch 載入成功才有），所以：
- **deliverable 一律先落盤**（檔案是持久真相來源，也是唯一保證送達 lead 的管道）；teammate 把成果寫進 `design-run/<feature-slug>/<序號>-<role>.md` 後，再做標 task / 發訊息的程序步驟。
- teammate 若無法自標 completed 或通知 lead → **lead 負責補做**（`TaskUpdate` 標 completed、必要時把上下游問答 relay）。實測多輪：teammate 落盤後由 lead 補標 task 均可行。

### Teammate spawn prompt 模板

這是上節步驟 4「spawn teammate」時，傳給 `Agent` 工具的 prompt 內容（搭配 `team_name` + `name` 參數）。teammate 不繼承主對話歷史，所以模板必須明確指示讀角色檔、輸入來源，**以及 team 工具的載入方法**（非原生，不指示就不會載）：

```
你是 Athena 產品設計團隊的 <role>，team 內名字是 <teammate-name>。
0. 先用 ToolSearch（query: "select:SendMessage,TaskList,TaskUpdate"）載入 team 工具。
   載入失敗不要中斷——peer 訊息與標 task 改走第 5 點 fallback，由 lead 代做。
1. 完整讀取 <此 skill 的絕對路徑>/skills/<role>/SKILL.md，
   依其角色定位、工作模式、輸出格式與自檢清單執行。
2. 任務：<具體任務描述>
3. 上游輸入：design-run/<feature-slug>/ 下的 <編號檔案>（請先讀）。
4. 正式產出寫入：design-run/<feature-slug>/<序號>-<role>.md（落盤才算交付），
   然後把對應 task 標 completed（TaskUpdate 不可用就略過，lead 會代標）。
5. 需要其他角色的定義或決策時（例如某個 state 的觸發條件），**不要臆測**：
   a. 上游的 <序號>-<role>.md 已落盤 → 直接讀檔取得答案；
   b. 還沒有 → 用 SendMessage 直接問該角色 teammate（to 填其名字）；
   c. peer 訊息不可用 → 把依賴點寫進產出與 final report
      （標「待 <role> 定案」並列出選項），由 lead relay 上游答覆後再補齊。
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
- **認領**：並行段讓 teammates self-claim（前提：已用 ToolSearch 載入 `TaskUpdate`；task 有 file-lock 防搶）；順序段由 lead assign 給對應角色。teammate 工具載入失敗或舊版環境 → 認領與標記由 lead 代為。

### 通訊慣例（避免 mailbox 變雜訊）

- **問定義 / 拿決策才發 peer 訊息**：例如 `ux-writer` 需要某 error state 的觸發條件 → 先讀 `interaction-designer` 已落盤的檔案；沒有才 `SendMessage` 直接問該 teammate，而非自行假設。peer 管道不可用（環境相依）時改走 lead relay。
- **正式產出走檔案，不走訊息**：訊息只放結論與指路；完整 deliverable 一律寫進 `design-run/<feature-slug>/<序號>-<role>.md`。理由：task 狀態可能延遲、resume 不還原 teammates — 檔案才是持久真相來源，且三模式產出結構因此完全一致。
- **挑戰式協作**（critique、多假設對撞）才需要角色間來回辯論；接力式產出不必互發訊息。peer 不可用的環境，辯論改由 **lead 逐一 spawn 各方、收齊產出後自己綜整／仲裁**。
- 完成即標 task completed（`TaskUpdate` 不可用時 lead 代標），讓下游 dependency 自動解鎖。

### 卡住處理

teammate 卡住或 task 久滯 in-progress 時，lead 依序：
1. nudge 該 teammate — idle 成員對 name 發 `SendMessage` 即可喚醒；inactive／孤立成員用 `SendMessage` 帶 `to: <agentId>` resume（光對 name 發只進信箱不會跑）。需要時 lead 直接把上游答覆或缺的決策 relay 給下游（或請它讀上游已落盤的檔案）讓它定案。
2. 無效 → spawn 替補 teammate 接手該 task（讀同一角色檔 + 同樣輸入）。
3. 仍無效 → 該段由 lead 自行以 Mode 1 subagent 補完。

---

## 回退規則（三模式通用）

- 偵測不到 `TeamCreate` → 先用 `ToolSearch`（`select:TeamCreate`）嘗試載入 — Agent Teams 工具可能是**依情境動態浮現**的 deferred tool，第一眼沒看到不代表不存在。仍載不到時分兩種：
  - **任務命中 Mode 3 升級清單** → **明示告知一次再退**，例如：「Mode 3 不可用：缺 `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` 或 Claude Code 版本過舊（已知 2.1.170 可用），啟用方法見 execution-modes.md §啟用前提。本次以 subagent 編排執行，產出與品質不受影響。」**告知不阻塞** — 說完即退 Mode 1 繼續任務。
  - **不命中升級清單** → 靜默退 Mode 1（本來就不需要 team，不必打擾使用者）。
  - 兩種情況都不要嘗試呼叫不存在的工具、不要中斷任務。
- 偵測不到 Agent tool → 退回 Mode 2。
- 無論哪個模式，產出一律遵循 `design-run/<feature-slug>/` 編號檔案慣例 — 模式只改變「怎麼協作」，不改變「交付什麼」。
