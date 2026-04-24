# Athena Product Design Team — AI Skill Pack

A multi-role AI product design team skill for B2B cross-platform (Web / App) product design workflows.

> 專為 Athena 產品設計團隊打造的 Claude AI 虛擬夥伴，14 個專業角色分工協作，涵蓋從需求解析、使用者研究、市場洞察、產品策略、互動與視覺設計、文案、原型、設計系統、可用性測試、無障礙審查到 Dev Handoff 的完整產品設計流程。

---

## 這是什麼？

這套 Skill Pack 把 Claude 變成一個完整的**產品設計團隊**。每個 Skill 是一個有專業角色定位、工作流程和輸出格式的 AI 成員。你可以根據任務需要呼叫對應的角色，或讓 `design-lead` 幫你分派。

設計理念：
- **前期調研寫得最深**（Athena 團隊痛點 #1：研究不足）
- **Dev Handoff 寫得最深**（Athena 團隊痛點 #2：Handoff 不順）
- **B2B Web / App 跨平台**為預設情境
- **Double Diamond + Lean UX** 混合方法論
- **繁中輸出**、技術術語保留英文

---

## 團隊成員（14 角色）

### Discovery Cluster（前期調研群）
| 角色 | Skill 名稱 | 核心職責 | 何時呼叫 |
|------|-----------|---------|---------|
| 需求分析師 | `requirement-analyst` | PRD / ticket 解析、gap 識別、風險提醒 | 收到 PM 需求或 Jira / Linear / GitHub issue 時 |
| UX 研究員 | `ux-researcher` | 使用者訪談、persona、JTBD、journey map | 需要質性洞察、使用者動機時 |
| 市場洞察分析師 | `market-insight-analyst` | 競品分析、產業 benchmark、趨勢 | 定位或差異化決策前 |
| 數據分析師 | `data-analyst` | Funnel、retention、行為量化 | 需要量化佐證設計決策時 |

### Strategy & Design Cluster（策略與設計群）
| 角色 | Skill 名稱 | 核心職責 | 何時呼叫 |
|------|-----------|---------|---------|
| 設計總監 | `design-lead` | 任務分派、critique、流程統籌 | 不知從哪開始、需要審核方向 |
| 產品策略師 | `product-strategist` | 問題框架、成功指標、MVP 範圍 | 研究後、設計前的策略決策 |
| 互動設計師 | `interaction-designer` | User flow、IA、wireframe、state | 策略確定後進入結構設計 |
| 視覺設計師 | `ui-designer` | 版面、typography、color、component 應用 | 互動確定後進入視覺設計 |
| UX 文案 | `ux-writer` | Microcopy、error、onboarding、voice & tone | 有 UI 骨架後的文案撰寫 |
| 原型設計師 | `prototyper` | Figma prototype、motion、互動驗證 | 需要可點擊原型或動效規範 |
| 設計系統架構師 | `design-system-architect` | Token、component spec、variant、governance | 建立或擴充 DS 時 |

### Validation & Delivery Cluster（驗證與交付群）
| 角色 | Skill 名稱 | 核心職責 | 何時呼叫 |
|------|-----------|---------|---------|
| 可用性測試師 | `usability-tester` | 測試計畫、腳本、發現合成、嚴重度評級 | 需要驗證設計時 |
| 無障礙審查員 | `accessibility-reviewer` | WCAG 2.1 AA 檢查、remediation 建議 | Handoff 前的 a11y 審查 |
| 設計交付 | `design-ops` | Handoff spec、Figma↔GitHub 對接、Dev QA | 交付給工程前 |

---

## 常見工作流程

### 新功能完整設計流程
```
① requirement-analyst       → 解析 PRD，識別 gap 與風險
② ux-researcher + market-insight-analyst + data-analyst (平行)
                            → 質性／市場／量化三路調研
③ product-strategist        → 問題框架、成功指標、MVP 範圍
④ interaction-designer      → User flow、IA、wireframe
⑤ ui-designer               → 視覺設計
⑥ ux-writer                 → Microcopy
⑦ prototyper                → 可點擊原型
⑧ usability-tester          → 測試驗證
⑨ accessibility-reviewer    → WCAG 檢查
⑩ design-system-architect   → 抽取 component、更新 token
⑪ design-ops                → Handoff spec + Dev QA checklist
```

### 快速迭代（Lean UX）
```
data-analyst → ux-researcher → interaction-designer → ui-designer → prototyper → usability-tester
```

### 設計系統建立
```
design-system-architect → ui-designer → ux-writer → design-ops
```

### Dev Handoff
```
design-system-architect → accessibility-reviewer → design-ops
```

### Usability 問題調查
```
data-analyst → ux-researcher → usability-tester → interaction-designer
```

---

## 工具鏈整合（後續將持續調整）

預設搭配的工具：

| 工具 | 用途 | 對應角色 |
|------|------|---------|
| **Figma** | 讀 design file、token、component、prototype | `ui-designer`, `interaction-designer`, `prototyper`, `design-system-architect`, `design-ops` |
| **Notion** | 需求文件、研究筆記、handoff doc | `requirement-analyst`, `ux-researcher`, `market-insight-analyst`, `design-ops` |
| **GitHub** | 讀 issue / PR、Handoff 對接 | `requirement-analyst`, `design-ops` |
| **Claude.ai（本身）** | 生成 UI mock、component 代碼示意、內容產出 | `ui-designer`, `prototyper`, `ux-writer` |

> **沒有對接 MCP 也能使用** — 所有 Skill 在純文字輸出模式下同樣能完整運作。

詳細安裝與 MCP 設定請見 **[SETUP.md](./SETUP.md)**。

---

## 設計原則

- **角色分工明確**：每個 Skill 只做自己負責的事，不越界
- **上下游銜接**：每個角色的輸出格式，都是下一個角色需要的輸入格式
- **可直接執行**：所有輸出都是「可以直接用」的格式（Figma 可貼的結構化表格、spec 可直接送 Dev、research 報告可直接貼 Notion）
- **B2B 跨平台優先**：每個產出明確標示目標 surface（Web / App / 跨平台）
- **a11y 是一等公民**：所有 user-facing 輸出前都要經過 `accessibility-reviewer`
- **繁中＋英文術語**：可讀性優先，技術精確性不打折

---

## 如何使用

快速入門：
1. Clone 這個 repo
2. 將 `skills/` 資料夾的內容上傳至你的 Claude.ai 自訂 Skill 設定（或直接放在 Claude Code 的 `.claude/skills/` 目錄）
3. 開啟新對話，Claude 會自動根據任務觸發對應角色
4. 若想指定角色，直接在對話中說明：「用 `ux-researcher` 幫我規劃 5 場訪談」

範例指令：
- 「幫我解析這份 PRD」→ 自動觸發 `requirement-analyst`
- 「這個功能該不該做？」→ 自動觸發 `product-strategist`
- 「幫我寫 error message」→ 自動觸發 `ux-writer`
- 「幫我做 Dev Handoff」→ 自動觸發 `design-ops`

---

## 客製化建議

- **產品類型**：預設是 B2B Web / App 跨平台。若轉 B2C 或單一平台，請調整 `product-strategist` 與 `interaction-designer` 的預設假設。
- **方法論偏好**：預設 Double Diamond + Lean UX 混用。可在 `product-strategist` 內指定特定方法論。
- **平台優先序**：預設 Web / App 並重。若有明確主力平台，於 `design-lead` 的預設偏好中指定。
