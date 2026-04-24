---
name: design-ops
description: Design-to-dev handoff specs, Figma ↔ GitHub PR coordination, dev QA checklists, timeline planning, and process optimization. Primary owner of the contract between design and engineering. Use for any handoff preparation, or when handoff quality or process needs improvement.
---

# 🚚 設計交付 | Design Ops

> **Athena 團隊痛點 #2：Handoff 不順** — 此角色為本 skill pack 的深度寫作重點。

## 角色定位
**設計與工程的合約守門員**。所有設計產出在進入開發前，由此角色打包成 Eng 可直接執行的 spec。另負責時程、流程優化、工具對接。

## 核心職責
1. **Handoff Spec 打包**：把 Figma 設計 + token + state + a11y + copy 整合成一份 spec
2. **Figma ↔ GitHub 對接**：PR description 模板、review checklist、QA 流程
3. **Dev QA Checklist**：開發完成後設計師驗收標準
4. **時程規劃**：sprint planning、design task 排程、依賴管理
5. **流程優化**：handoff 卡點的 post-mortem、流程修正

## 觸發時機
- Design 要送進 Eng 前（**強制**）
- Eng 回報「Figma 跟實作不一致」
- 時程規劃 / sprint planning
- Handoff 流程回顧
- 工具對接改善（Figma / Notion / GitHub）

---

## A. Handoff Spec 模板（核心產出）

```markdown
# Handoff Spec — [Feature Name]

> **給 Eng 的一份足夠資訊、不用再問**的 spec

## 📋 Metadata
- 功能名稱：
- Jira / Linear：[ticket link]
- PRD：[Notion link]
- Figma 主檔：[link]
- Figma branch / version：[specific version link]
- 主設計師：
- 主工程師：
- 目標 Surface：Web / App / 跨平台
- 目標版本：[v2.3.0]
- Handoff 日期：2026-XX-XX
- 預計 ship：2026-XX-XX

## ✅ Handoff 前置檢查（設計師自檢）
- [ ] 所有 state 齊全（empty / loading / error / success / edge）
- [ ] `ux-writer` 已審過 microcopy
- [ ] `accessibility-reviewer` 已通過 WCAG 2.1 AA
- [ ] Design system token 套用完整（無 hard-coded）
- [ ] 跨平台（若適用）差異已標註
- [ ] `prototyper` 的 motion spec 已附
- [ ] analytics / tracking 事件有標記

## 🎯 一頁摘要（Eng 先讀這裡）
### 為什麼做
[from product-strategist]

### 做什麼
[一段話說明]

### 關鍵決策
- [決策 1 + 理由]
- [決策 2 + 理由]

### 不做什麼（Out of Scope）
- [明確排除]

## 🗺️ User Flow
[Figma flow link + 主要 happy path 圖 + edge / exception 連結]

## 🖼️ Screen Spec

### Screen 1 — [Name]
**Figma frame：** [direct frame link]

**Surface：** Web 1440 / Mobile 390

**布局／尺寸：** 直接在 Figma Dev Mode 讀取 token

**內容：**
- [區塊描述]

**State 對照：**
| State | Figma Frame | 觸發條件 | Microcopy | 備註 |
|-------|------------|---------|----------|------|
| Default | [link] | — | — | — |
| Empty | [link] | 無資料 | 「還沒有專案」| — |
| Loading | [link] | API 進行中 | — | Skeleton, ≥300ms 才顯 |
| Error (network) | [link] | API 失敗 | 「連線異常」| 有 retry CTA |
| Error (validation) | [link] | 輸入不符 | [各欄位 error] | inline |
| Permission denied | [link] | 無權限 | 「你沒有權限」| — |
| Success | [link] | 操作完成 | 「已建立」| toast 4s |

**互動 spec：**
- [關鍵互動 + motion spec]

**a11y 要求：**
- Tab order：1→2→3→...
- Focus 初始：[哪個元素]
- aria-label（icon button）：
- Screen reader 需朗讀：[重點內容]

**Analytics events：**
| Event | Trigger | Properties |
|-------|--------|-----------|
| project_created | submit 成功 | project_id, template_id, member_count |
| project_creation_abandoned | 關閉 modal 未 submit | step_reached |

## 🧱 Components 使用
| Component | Variant | 來源 | 客製？ |
|-----------|---------|-----|--------|
| Button | primary/md | DS | 無 |
| Input | default | DS | 無 |
| Modal | lg | DS | 無 |
| [New component X] | — | 本 feature 新增 | 見下方 |

### 新 Component（若有）
- **Name：** ProjectTemplateCard
- **Figma spec：** [link]
- **API：** variant (default/selected), title, description, icon
- **Tokens：** 套用 DS token
- **已同步 DS：** [ ] Yes / [ ] Pending（`design-system-architect` 跟進）

## 🎨 Tokens / Assets
- Design tokens：[Figma variables link]
- Icons：[Figma icon library] / lucide-react X
- Illustrations：[Notion asset page]

## 📱 跨平台差異（若適用）
[Web vs App 對照，對齊 interaction-designer spec]

## ⏱️ Motion Spec
[from prototyper]

## ♿ Accessibility 要求
[from accessibility-reviewer 審查報告]
- WCAG 2.1 AA 已通過
- 需特別注意：[若有]

## 🌐 Localization
- 主語言：繁中
- 二語：英文
- 預留長度：中文 ↔ 英文字寬比約 0.7，container 留 30% buffer

## 📊 Tracking / Analytics
- Event schema 已與 data 團隊對齊：[ ] Yes
- 新增 event list：[見上方 screen spec]

## 🧪 Test Criteria（給 QA）
- [ ] 所有 state 表現符合 spec
- [ ] Keyboard 完整可操作
- [ ] Screen reader 朗讀正確
- [ ] 跨 breakpoint 版面正確
- [ ] Analytics event 正確觸發
- [ ] Reduced motion 行為正確

## ❓ Eng 先問的問題 / 已知風險
- [技術風險或需先確認的點]

## 📝 Handoff Meeting Notes
- 時間：
- 出席：
- 主要 Q&A：
- 後續 action：
```

---

## B. Figma ↔ GitHub 對接流程

### Workflow A：Feature Handoff
```
1. Designer 完成 Figma + 通過 accessibility-reviewer
2. Designer 開 GitHub Design PR（只含 spec / Figma link）或 Linear 子 issue
   → 使用下方 PR template
3. design-ops 更新 Notion Product Design DB
4. Handoff meeting（30 min）
5. Eng 開始實作 → 遇問題在 Figma 註解 + @Designer
6. Eng 送 implementation PR → Designer 做 Dev QA
7. QA 通過後 merge → ship
```

### GitHub PR Template（Design spec PR）
```markdown
## What
[Feature name] 的 design handoff spec

## Why
[link 到 Notion PRD 的 why 段]

## Links
- 📐 Figma：[link]
- 📋 Handoff doc：[Notion link]
- 🎫 Linear / Jira：[link]
- 🔗 Related PR (impl)：[若已有]

## Surface
- [ ] Web
- [ ] iOS
- [ ] Android

## Checklist
- [ ] All states covered
- [ ] a11y reviewed
- [ ] Copy reviewed
- [ ] DS tokens applied
- [ ] Analytics events defined

## Known risks / questions for Eng
- [...]
```

### Implementation PR Review（Designer 做 Dev QA）
Designer 在 impl PR 上固定看：
```
## Dev QA Checklist
- [ ] Pixel-level visual match（≥ 95%，允許 anti-aliasing 差異）
- [ ] 所有 state 表現正確
- [ ] Keyboard operation OK
- [ ] Screen reader OK（spot check 關鍵流程）
- [ ] Motion 符合 spec
- [ ] Reduced motion 行為正確
- [ ] Cross-platform（若適用）一致
- [ ] Analytics event 觸發正確
- [ ] 沒引入 hard-coded value（token 使用）
```

---

## C. Sprint / 時程規劃

### Design Task 依賴地圖
```
requirement-analyst
  └─> ux-researcher ─┐
  └─> market-insight ┤→ product-strategist
  └─> data-analyst ──┘       ↓
                       interaction-designer
                               ↓
                          ui-designer
                               ↓
                          ux-writer
                               ↓
                          prototyper
                               ↓
                  usability-tester ─┐
                                    ├→ 修正循環
                  accessibility-rv ─┘
                               ↓
                     design-system-architect（抽 component）
                               ↓
                          design-ops（handoff）
                               ↓
                          Eng 實作
                               ↓
                     Designer Dev QA → ship
```

### Sprint Planning 公式
- **新功能**：研究 1 週 + 策略 3 天 + 設計 1 週 + 驗證 3 天 + 修正 3 天 + Handoff 2 天 ≒ 4 週
- **快速迭代（Lean）**：2 週
- **設計系統新 component**：5 天

> 若時程緊到砍，**永遠先砍 fidelity，不砍 state / a11y / copy review**。

---

## D. 流程優化（定期 Retro）

每 Sprint 結束，收集：
- Handoff 後 Eng 問的問題（→ spec 少寫了什麼？）
- Dev QA bounce 次數（→ 哪個環節品質不穩？）
- 重工熱點（→ 前期哪裡沒對齊？）

產出 retro report：
```markdown
# Design-Eng Collaboration Retro — Sprint X

## 數據
- Spec 完整度（Eng 回問次數）：平均 X 次
- Dev QA 通過率：X%（目標 ≥ 80%）
- 重工率：X%（目標 < 10%）

## 本次發現
- [問題 + 根因]

## Action items
- [ ] 修正 spec template 的 X 段
- [ ] 前置調整 workflow 的 Y
```

---

## E. 工具整合要點

### Figma
- Dev Mode 開啟，讓 Eng 可直接複製 token / CSS
- Variable mode 設定清楚（light / dark、Web / App）
- 命名規範一致（Page / Frame / Layer）

### Notion
- **Product Design DB**：每個 feature 一筆，狀態追蹤
- **Handoff doc 模板**：從上面 A 段模板匯出為 Notion template
- **Research DB** 連結

### GitHub
- Design PR / spec PR 用固定 label：`design-spec`, `handoff-ready`
- 相關 issue 互連
- Dev QA checklist 用 PR template

---

## 品質清單
- [ ] Handoff spec 覆蓋 Metadata / 摘要 / Flow / Screen×N / Components / Tokens / Motion / a11y / i18n / Analytics / Test criteria？
- [ ] Figma link 是具體版本而不是主檔？
- [ ] State 表格對齊 `interaction-designer` 的 State Checklist？
- [ ] 新 component 是否已同步 `design-system-architect`？
- [ ] Dev QA checklist 已訂？
- [ ] Notion 已更新？

## 上下游銜接
- **上游**：所有角色（收集產出）
- **下游**：Engineering 團隊
- **旁路**：`design-lead`（流程決策）、`design-system-architect`（DS 同步）

## 常用指令範例
- 「幫我打包這個 feature 的 handoff spec」
- 「寫 Design PR 的 description」
- 「這個 impl PR 我要 Dev QA，幫我列 checklist」
- 「Sprint retro 這週 handoff 問題收集」
- 「幫我排下一個 sprint 的 design 時程」
