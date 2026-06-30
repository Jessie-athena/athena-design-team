---
name: figma-to-component-doc
description: 將 Figma 元件系統性轉換為 Component 元件設計文件的可重複工作流程（Token Matching 五層驗證）。由 design-system-architect 執行；ui-designer 消費產出文件；prototyper 在 Pass 0 引用文件作為元件命名 authority。
---

# Figma 元件 → Component 設計文件（Token Matching 工作流程）

> **核心理念**
>
> Design System 的核心不是生一堆 Token，而是維護這條鏈：
>
> **Primitive → Semantic → Component Token → Figma Variant → Code Component**
>
> 每一層都要確認「它到底接的是哪個 Token」。日後只需綁定 Component 和 Primitive Token，中間層的對應交給 claude code + agent 自動驗證。

---

## 觸發時機

- 「Figma 元件轉 Component 設計文件」
- 「補充 / 同步元件設計文件」
- 「新元件收編到 DS」
- `design-system-architect` Step 2 執行 Figma 補入

---

## 前置確認（缺一不開工）

- [ ] **Figma node 已知**：元件集節點 ID（格式 `FILE_KEY/NODE_ID`）→ 查 `athena-components.md` 或詢問使用者
- [ ] **`athena-tokens.md` 已讀取**：Token 真值映射表（Primitive 與 Semantic 層）
- [ ] **`component-doc-schema.md` 已讀取**：§0–§13 章節格式、Lite/Full 分層規則
- [ ] **目標文件路徑確認**：`references/components/<ComponentName>.md`
- [ ] **層級判定**：原子控制元件（Button / TextBox / Checkbox…）→ **Lite**；資料密集・複合元件（DataGrid / Stepper / Dialog…）→ **Full**（判層心法見 `component-doc-schema.md §3`）

---

## 五層 Token Matching 工作流程

每層是一個驗證關卡。**任何層發現 token 缺口，立即停下回報，禁臆造新 token**（與 `component-doc-schema.md §2.2` 一致）。

---

### Layer 1：Primitive Token — 原始值映射

**目標**：確認 Figma 定義的每個 raw 值（色彩 hex / 尺寸 px）都有對應的 Primitive token。

**動作**：

```
get_variable_defs(node_id)
```

取得元件集的全部 Figma 變數定義（色彩 / 字體 / 間距 / 圓角等）。

**驗證**：

- 逐一對映至 `athena-tokens.md` 的 Primitive 層（`--color-sf-*` / `--ds-*`）
- ✅ 對映到 → 填入 §3 視覺規格 Tokens 的底層 `{token-name}`
- ❌ 對不上 → 停下回報：「Layer 1 缺口：`{Figma 變數名}` = `{原始值}`，`athena-tokens.md` 無對應」

**輸出**：§3 Tokens block 的 Primitive 層清單（raw 值已全數轉為 `{token}` reference）

---

### Layer 2：Semantic Token — 語意別名確認

**目標**：確認 Primitive 如何被語意化，設計決策走的是 Semantic alias 而非 Primitive 直接引用。

**動作**：

- 查閱 `athena-tokens.md` Semantic 層（`color.primary` → `color.blue.600` 等）
- 查閱 `athena-design.md` 語意對照
- 確認 Layer 1 的每個 token 是否已有 Semantic alias 可替換

**驗證**：

- 色彩：能用 Semantic（`{color-sf-primary}`）則不直接引 Primitive（`{color-blue-500}`）
- 設計師使用的層應是 **Semantic**；Primitive 只在 DS 內部修改時才直接動
- Semantic 層若缺失 → 記錄「待補 semantic alias」但不阻塞（此為 token 系統缺口，非元件文件缺口）

**輸出**：§3 / §4 / §5 的 `{token}` reference 全部升為 Semantic alias（若存在）

---

### Layer 3：Component Token — 部位分配填充

**目標**：針對每個 variant（primary / secondary / danger / success），明確每個 UI 部位用哪個 token。

**動作**：

從 Layer 1–2 的結果，按元件部位（border / container-hover / text / radius / disabled-bg）組裝 token 清單：

```yaml
# 範例：Button primary variant
primary:
  bg: "{color-sf-primary}"         # Layer 1 → Layer 2 升 semantic
  fg: "{color-sf-on-primary}"
hover:
  overlay: "{color-sf-primary-btn-hover-8}"   # two-stop 值 = 疊層
active:
  overlay: "{color-sf-primary-btn-pressed-12}"
disabled:
  fg: "{color-sf-on-surface-opacity38}"
  border: "{color-sf-outline}"
```

**驗證**：

- 每個 variant × 每個部位 × 每個 state 都有明確 `{token}` reference
- **two-stop Figma 值**（如 `#2877EE,#FFFFFF`）= base + state-layer 疊層，非換色 → 標為疊層實作
- Token 來源必須指向 Layer 1–2 已驗證的名稱，不得憑記憶填寫

**輸出**：§4 Variants 與 §5 States 完整填充（YAML fenced block，每行有來源標記 🎨/🔗）

---

### Layer 4：Figma Variant — 視覺驗證與量測

**目標**：確認 Figma 中每個 Variant 是否正確綁定 Token（無 hardcoded 值）；量測視覺尺寸補入文件。

**動作**：

```
# 取單一 variant 量測（整個元件集 metadata 常 >80k，挑單一 variant 即可）
get_design_context(single_variant_node_id)

# 多狀態元件才需（hover / disabled / error / focus 各有不同外觀）
get_screenshot(node_id)
```

**驗證**：

- 量測值（height / padding px）與 Layer 3 填入的 token 對齊 → 加標 `# 🎨 量測確認`
- variant 軸與 Figma component property 吻合（不多也不少）
- screenshot 中各狀態視覺與 §5 States 描述一致

**輸出**：

- §2 Anatomy 補入結構樹（子元素清單）
- §3 量測值加 `🎨` 標記
- §0 `figma-node` 填入 `FILE_KEY/NODE_ID`、`last-synced` 填本次日期
- §5 States 附視覺確認備註（`# 🎨 screenshot 確認`）

**已知 SOP 限制**（來自 Button 演練，詳 `component-doc-schema.md §5`）：

- `get_variable_defs` 不含 padding/height → 需 `get_design_context` 於單一 variant 量取
- Figma 變數命名為元件級 `Component_btn/$state` 格式，值如 `#2877EE,#FFFFFF` = two-stop
- `get_metadata` 整個元件集常 >80k → 先查 `athena-components.md §figma-node` 找精確 node ID

---

### Layer 5：Code Component — Code 側驗證

**目標**：確認 Code 使用 Token 而非 hard-coded color；填充 authority 與 API/Props。

**動作**：

- 查閱 `athena-components.md` 確認 authority（Syncfusion import 路徑 / 客製 `Ds*` 元件路徑）
- 查閱 shared UI 元件命名（`DataGrid` / `TextInput` 等；完整清單見 `component-doc-schema.md §1`）或 Syncfusion 原生名
- 若元件有既有的 `prototyper/profiles/erp-components/*.md` → 列入 §13 引用，**不重印** token 決策

**填充**：

- §12 API / Props（prop / type / default；設計指引與 API 分離，不混入視覺描述）
- §13 關聯 Links（tokens / prototyper profile / code 元件 / figma file）
- §0 Frontmatter 補完（`authority` / `status`）

**驗證**：

- §0 `status` 對應 `athena-components.md` 的文件狀態（✅ 已產出 / ⬜ 待建立）
- §13 三向連結可達（tokens / prototyper profile / code）
- 確認無 raw hex / px 散落文件中（全部 `{token}` reference 或 🎨 量測標記）

**輸出**：§0 §12 §13 填充完成

---

## 人工補充（§1 §6–§11 / 📋）

Layer 1–5 為「Figma 可讀」部分（🎨🔗）；以下章節需人工 / PRD 補充：

| 章節 | 內容 | Lite / Full |
|------|------|-------------|
| §1 概述 | 用途、何時用 / 不用（2–3 行）| Lite + Full |
| §6 Behavior | 互動規則表（DataGrid 式）| **Full 必填** |
| §7 RWD 響應式 | 斷點 + 欄位優先級 P0–P3 | **Full 必填** |
| §8 Keyboard | Tab/Arrow/Enter/Esc、focus 持留 | **Full 必填** |
| §9 a11y 無障礙 | ARIA、contrast、touch target、screen reader | Lite + Full |
| §10 跨平台 Adaptive | App ≠ 縮小 Web；pop-in / 替代佈局 | **Full 必填** |
| §11 Content 指引 | 元件特有文案（empty / error / bulk verb+noun）| 輕量 |

---

## 輸出物

完成五層 + 人工補充後：

1. **`references/components/<ComponentName>.md`** — 符合 `component-doc-schema.md §0–§13` 的完整設計文件
2. **`athena-components.md` 設計文件索引更新** — 在對應元件列補上 `[components/<Name>.md]` 連結與產出狀態
3. **若有新發現的 token 缺口** → 同步補入 `athena-tokens.md`（先確認 Figma 真值，再補入，禁臆造）

---

## 自檢清單（輸出前逐項確認）

依 `component-doc-schema.md §8`：

- [ ] §0 Frontmatter 含 tier（lite/full）與 status；figma-node 與 last-synced 已更新
- [ ] 視覺值全為 `{token}`，無 raw hex / px（量測值已標 🎨 待對齊）
- [ ] 每個 `{token}` 在 `athena-tokens.md` 找得到對應名
- [ ] Lite 不硬塞 §6–§10；Full 的資料展示型 §5 含 empty/loading/error
- [ ] 來源標記（🎨/🔗/📋）逐區塊標齊
- [ ] §13 三向連結可達（tokens / prototyper profile / code）
- [ ] 與 prototyper profile 重疊處用引用，不重寫 token 決策
- [ ] `athena-components.md` 設計文件索引已更新

---

## 三 Skill 分工（本工作流程在三角色中的位置）

| 角色 | 與本工作流程的關係 |
|------|-----------------|
| `design-system-architect` | **執行者**：按五層流程產出 `references/components/<Name>.md` |
| `ui-designer` | **消費者**：選用元件前查 §4 Variants / §5 States / §9 a11y；契約未產出時退查 `prototyper profile + app.css`（見 `component-doc-schema.md §9.2`）|
| `prototyper` | **引用者**：Pass 0 發現元件有對應 `.md` 設計文件時，其 §4 Variants / §12 API 為 prototype HTML 元件命名的更上游 authority（高於 prototyper profile 的 class 名）|
