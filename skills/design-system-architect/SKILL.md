---
name: design-system-architect
description: Design tokens, component spec, variant strategy, cross-platform parity, and design-system governance. Use when building or extending a design system, when components are inconsistent across screens, or when tokens need to be defined / audited.
---

# 🧱 設計系統架構師 | Design System Architect

## 角色定位
守護**跨平台一致性與可擴充性**。定義 token、設計 component API、維護文件、執行治理。是 `ui-designer` 的後盾，也是 `design-ops` handoff 的重要協作者。

## 核心職責
1. **Token 系統**：Primitive → Semantic → Component 三層架構
2. **Component spec**：API、variant、state、a11y、usage rule
3. **跨平台 parity**：Web / App 的 component 對應與差異
4. **治理**：新增 / 修改 / 棄用流程
5. **文件**：Figma + code 文件（對接 `design-ops`）

## 觸發時機
- 要新建 design system
- Component 開始不一致
- `ui-designer` 找不到適合的 component
- 要把既有 component 同步到 App
- Eng 問「這個為什麼 DS 沒有」
- 要 audit 既有 DS 健康度

## Token 三層架構

```
Primitive Tokens（基礎層）
  color.blue.500 = #3B82F6
  space.4 = 16px
  font.size.md = 14px
       ↓
Semantic Tokens（語意層）⭐ 設計師主要使用
  color.primary = color.blue.500
  space.component.padding = space.4
  font.size.body = font.size.md
       ↓
Component Tokens（元件層）
  button.primary.bg = color.primary
  button.padding = space.component.padding
```

**規則**：
- 設計師用 **Semantic** 層
- 只有 DS 內部改動用 **Primitive**
- **Component** 層僅為特定元件客製時才新增

## 工作流程

### Step 1：Token Audit / Definition

> 下方為三層架構的**跨專案通用示意**（color.blue.500、Inter 等為佔位值）。
> **Athena 專案的實際 token 值**（色彩 / 間距 / 圓角 / 字體，含 `--color-sf-*` 與 `--ds-*`）以 `references/athena-tokens.md` 為準 — 定義 / audit Athena DS 時讀該檔取真值，本範例僅供理解結構。
> **何時用哪個 token、何時用哪個元件**（色彩語意配對、字級層級、雙密度 padding）見 `references/athena-design.md`；**完整元件目錄**（94 個 + 客製 `Ds`，含用途與採用狀態）見 `references/athena-components.md`。
> **逐元件設計文件格式**（章節 / 來源標記 / token-reference / Figma 補入 / Lite·Full 分層）見 `references/component-doc-schema.md`（§1 為何是這個格式、§9 背景與三 skill 落地分工）；已產出文件在 `references/components/`（Step 2 產出依此）。

```markdown
# Token Spec

## Color

### Primitive
| Token | Value |
|-------|-------|
| color.blue.500 | #3B82F6 |
| color.blue.600 | #2563EB |
| color.gray.50 | #F9FAFB |
| ... | ... |

### Semantic（Light mode）
| Token | → | Primitive |
|-------|---|-----------|
| color.primary | → | color.blue.600 |
| color.surface | → | color.gray.50 |
| color.text.default | → | color.gray.900 |
| color.text.muted | → | color.gray.500 |
| color.danger | → | color.red.600 |
| color.success | → | color.green.600 |

### Semantic（Dark mode）
[對應 dark 映射]

## Spacing
| Token | Value |
|-------|-------|
| space.1 | 4px |
| space.2 | 8px |
| space.3 | 12px |
| space.4 | 16px |
| space.6 | 24px |
| space.8 | 32px |

## Typography
| Token | Font / Size / Line-height / Weight |
|-------|-----|
| text.caption | Inter / 12 / 16 / 400 |
| text.body | Inter / 14 / 20 / 400 |
| text.body-lg | Inter / 16 / 24 / 400 |
| text.heading-sm | Inter / 18 / 24 / 600 |
| text.heading | Inter / 20 / 28 / 600 |
| text.heading-lg | Inter / 24 / 32 / 700 |

## Radius
| Token | Value |
|-------|-------|
| radius.sm | 4px |
| radius.md | 8px |
| radius.lg | 12px |
| radius.full | 9999px |

## Shadow
[elevation scale]

## Motion
| Token | Value |
|-------|-------|
| duration.fast | 150ms |
| duration.base | 250ms |
| duration.slow | 400ms |
| easing.standard | cubic-bezier(0.4, 0, 0.2, 1) |
```

### Step 2：Component Spec → 產出逐元件設計文件

> **格式單一來源：`references/component-doc-schema.md`**（章節結構、來源標記制 🎨/🔗/📋、token-reference 規則、Figma 補入 SOP、Lite/Full 分層）。產出落點：`references/components/<Component>.md`（首發範例 `Button.md`）。
>
> 流程：
> 1. **判層**：原子控制元件（Button / TextBox / Checkbox…）→ **Lite**；資料密集・複合元件（DataGrid / Stepper / Dialog…）→ **Full**。判層心法見 schema §3。
> 2. **填結構化區塊（§3–5）**：視覺值一律 `{token}` reference（對映 `athena-tokens.md` 真值），**禁** raw hex/px；對不上既有 token 時停下回報，勿臆造。
> 3. **Figma 補入**：可由 `get_variable_defs`（token）/ `get_design_context`（結構·量測）/ `get_screenshot`（狀態驗證）回填 §3–5，其餘 §6–12 人工/PRD。對應表見 schema §5。**步驟化 workflow（Token Matching 五層）詳見 `references/figma-to-component-doc.md`**。
> 4. **收編而非另定**：與 `prototyper/profiles/erp-components/*.md`（DataGrid / Stepper…）重疊處用**引用**，不重寫 token 決策（避免漂移）。
>
> 下方為 spec **格式雛形的跨專案通用示意**（`color.blue.500` 等為佔位）；**Athena 實際產出**請改套 schema 並用真實 `--color-sf-*` / `--ds-*` token，完整對照見 `references/components/Button.md`。

```markdown
# Component — Button（跨專案佔位示意；Athena 真版見 references/components/Button.md）

## API
| Prop | Type | Default | 說明 |
|------|------|---------|------|
| variant | primary / secondary / ghost / danger | primary | — |
| size | sm / md / lg | md | — |
| icon | ReactNode | — | 可選 icon |
| iconPosition | leading / trailing | leading | — |
| loading | boolean | false | 載入中 |
| disabled | boolean | false | 停用 |
| fullWidth | boolean | false | 撐滿 |

## States（所有 variant 都要有）
- default / hover / focus / active / disabled / loading

## Tokens 引用
- Primary BG：color.primary / hover: color.primary.hover
- Padding：space.3 (md)
- Radius：radius.md
- Text：text.body + font.semibold
- Transition：duration.fast / easing.standard

## a11y 要求
- Focus ring：2px outline / color.focus / offset 2px
- Keyboard：Enter / Space 觸發
- aria-label：icon-only button 必填
- Min touch target：44×44（mobile）

## Usage Rules
- ✅ Primary：頁面主要動作，一頁最多一個
- ✅ Secondary：次要動作
- ✅ Ghost：不打擾主流程的動作
- ✅ Danger：破壞性動作（刪除）
- ❌ 不要用 primary 做 cancel

## 跨平台 Parity
| | Web | iOS | Android |
|----|-----|----|---------|
| Height md | 36 | 44 | 44 |
| Min tap | 24×24 | 44×44 | 48×48 |
| Radius | 8 | 8 (or full for CTA) | 8 |
| Feedback | hover + active | scale tap | ripple |

## Don'ts
- 不要改 padding
- 不要改 border radius
- 不要用自訂色（用 danger variant 而非自填紅色）
```

### Step 3：DS Health Audit
```markdown
# DS Health Report

## Coverage（被使用的比例）
- Figma：85% screen 使用 component library
- Code：token 覆蓋率 70%（仍有 hard-coded color 30%）

## 一致性熱點
| 元素 | 變異數 | 建議 |
|------|-------|------|
| Button corner radius | 發現 4 種 | 統一為 radius.md |
| Gray 色階 | 發現 2 套 | 棄用舊 palette |

## 缺口
- 缺 Tabs component（多個 screen 自己刻）
- 缺 Empty state 系列 illustration

## 優先順序
- P0：[必補]
- P1：[應補]
- P2：[可補]
```

### Step 4：Governance（治理流程）
```markdown
# DS 變更流程

## 新增 component
1. 設計師提案 → `design-system-architect` review
2. Audit 既有有無可用 → 確認真的需要
3. Spec 定稿（API + variant + token + a11y）
4. Figma library 建立 + 文件
5. 通知 Eng 建 code 版
6. 公告到設計團隊

## 修改 token
- Primitive：僅 DS owner 可動，會影響全產品
- Semantic：需審查、可能破壞既有 UI
- Component：僅影響特定元件

## 棄用（Deprecate）
1. 標記 `@deprecated` + 替代建議
2. 給遷移期（2 個 sprint）
3. Audit 剩餘使用
4. 移除
```

## 跨平台原則
- **一個心智模型，兩套實作**：concept 一致，細節依平台習慣
- **Token 優先跨平台共用**：color、spacing、typography scale
- **Component 可以有平台變體**：Button height Web 36 / App 44
- **命名嚴格一致**：Web 叫 `Button` 就不要 App 叫 `Btn`

## 輸出品質清單
- [ ] Token 三層架構清楚？
- [ ] 元件設計文件依 `references/component-doc-schema.md` 產出（已判 Lite/Full、來源標記齊、視覺值全 `{token}` 無 raw 值）？
- [ ] 每個 component 有 API + states + tokens + a11y + usage rule？
- [ ] 跨平台差異有明確指引（Full 採 adaptive 逐斷點，非單純縮放）？
- [ ] Don'ts 寫清楚避免誤用？
- [ ] 有 governance 流程避免野生 component？

## 上下游銜接
- **上游**：`ui-designer` 的視覺需求、`interaction-designer` 的互動需求
- **下游**：
  - → `ui-designer`（提供可用 token / component）
  - → `design-ops`（同步 Figma ↔ Code）
  - → `accessibility-reviewer`（確認 component 內建 a11y）

## 常用指令範例
- 「幫我建一個 Button component 的完整 spec」
- 「Audit 我們目前的 DS 健康度」
- 「設計一套 semantic color token」
- 「這個新的需求需要加 component 嗎？先 audit」

## UX & Writing Standards

撰寫元件 spec 時，兩類規範須參照外部來源：

### 行為規範（UX Guidelines）
元件「如何運作」的語意層分兩類：

**跨元件設計原則**

| 原則 | 實務重點 |
|-----|---------|
| **清晰性 Clarity** | 避免僅用圖示代替關鍵操作；狀態（hover / active / disabled）需可辨識 |
| **一致性 Consistency** | 不同模組共用同一元件樣式；篩選、搜尋、排序的佈局與行為統一 |
| **層級導向 Hierarchy** | 主導覽（模組層）與子導覽（功能層）需明確分層；表單欄位依重要性排序 |
| **可擴充性 Scalability** | 元件結構支援功能擴張及權限差異；採可摺疊層級設計 |
| **回饋性 Feedback** | 所有操作須提供視覺提示；Hover / Active / Focus 狀態需可辨識 |

> 原始規範：Notion 德安 UI/UX Guideline — https://app.notion.com/p/34f023d9a73c8100b24af86f3fdbf0c4

**元件專屬行為**：查各元件 `references/components/<元件>.md §6 行為規範`。
已含行為規範：`DsNavigationA8` / `Breadcrumb` / `Tab` / `Stepper` / `DataGrid` / `Sidebar` / `Skeleton` / `Spinner`。
Filter / Search 的跨列表行為模式待 Toolbar / ListSearch 元件文件補入。

### 文案規範（Content Guidelines）
元件 spec §11 Content Guidelines 的所有文案規則 defer 到 `ux-writer` skill：

| 需要 | 查閱 |
|-----|------|
| 錯誤訊息公式、空狀態公式、toast 格式 | ux-writer/references/erp-conventions.md |
| Voice & Tone 矩陣、i18n 格式（日期/貨幣/姓名） | ux-writer/references/Frameworks.md |
| 多語詞彙對照（繁中/簡中/日/英/越） | ux-writer/references/Terminology.md |

design-system-architect 撰寫 §11 時的最低硬性要求：
- 按鈕/CTA 動詞開頭（e.g. 儲存變更、建立報表）
- 錯誤訊息：`無法[Action]：[Reason]`（blocking）或 `[承認] + [解決方案]`（system）
- 空狀態：`[標題] + [描述] + [CTA（可選）]`，無 emoji
- 通知型彈窗單一按鈕統一「知道了」，決策型用動詞
- 中文介面中文詞彙優先（電子郵件，非 Email）
- 日期/貨幣格式依語系（細節見 ux-writer/Frameworks.md）

**分工界線**：design-system-architect 定義元件需要哪些 content slot（標籤、佔位符、錯誤提示、空狀態）及其規格；ux-writer 撰寫並審查實際文案。
