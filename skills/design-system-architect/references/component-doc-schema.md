# Component 元件設計文件 — 說明架構（Schema）

> 本檔定義 Athena 每個元件的**設計文件格式**：章節結構、來源標記制、token-reference 規則、Figma 補入 SOP。
> 由 `design-system-architect` 擁有；`ui-designer` 消費（挑用元件時取此規格）、`prototyper` 引用（profile 標上游）。
> 逐元件設計文件落點：`references/components/<Component>.md`。Token 真值解析對象：同目錄 `athena-tokens.md`；語意對照：`athena-design.md`；元件目錄與採用狀態：`athena-components.md`。

---

## 1. 為什麼是這個格式（參考基準）

Athena 是 **B2B Web/App 產品**（資料密集、單據交易、跨平台、Syncfusion/Material 底）。本架構的結構基準取自六個真正的 **B2B 產品（in-app）design system**，而非品牌行銷官網：

| 借鑑點 | 來源 | 落地章節 |
|---|---|---|
| 章節 canon：Overview→Anatomy→Variants→**States**→Behavior→Style+Tokens→Responsive→**a11y+Keyboard**→Content→API | 六者收斂 | 本檔 §4 章節順序 |
| **密度一等公民**（Cozy / Compact） | SAP Fiori | §3 並列 Default / Comfortable |
| **States 多層**（cell/row/column × hover/focus/selected） | Salesforce Lightning | §5 互動列多層 |
| **empty / loading / error 一等公民**（skeleton 非 spinner） | IBM Carbon | §5 資料展示型必含 |
| **跨平台 = adaptive 非單純 responsive**（App≠縮小 Web） | SAP Fiori | §10 逐斷點 adaptive |
| **每元件 Content 文案指引**（empty 文案 / error / bulk verb+noun） | Shopify Polaris | §11 輕量 + 連 `ux-writer` |
| **API/Props 與設計指引分離** | Ant Design | §12 獨立區塊 |
| **token-reference by name** | Carbon / SAP token-first | §2 token-reference 規則 |

> 不採用品牌**行銷官網**型 `DESIGN.md`（`pricing-card` / `hero` / `testimonial`…）的結構——那些沒有 data grid / table row / form-field，與 ERP 主體相反。唯一沿用的點子是 token-reference 語法，且已被 Carbon/SAP 獨立佐證。

---

## 2. 通用規則

### 2.1 來源標記制（每個區塊標來源）

讓「Figma 補入」可機器化、讓「需人工判斷」不被誤填：

| 標記 | 意義 | 由誰 / 什麼填 |
|---|---|---|
| 🎨 | Figma 可讀填 | `get_variable_defs`（token 值）/ `get_design_context`（結構、量測）/ `get_screenshot`（狀態驗證） |
| 🔗 | Token 引用 | 指向 `athena-tokens.md` 的 token 名（值由該檔解析，不寫死） |
| 📋 | 人工 / PRD | 語意、互動、a11y、跨平台、文案、治理——Figma 讀不出 |

### 2.2 Token-reference 規則（硬性）

- 視覺值一律寫 `{token-name}`，**禁** raw hex / px（與三 skill 既有「禁 inline hex」一致）。
- `{}` 內為 `athena-tokens.md` 的 token **去前綴簡名**：`{color-sf-primary}` / `{ds-radius-large}` / `{font-size-sf-text-md}`（落地 CSS 時還原為 `var(--color-sf-primary)` 等）。
- 非 token 的固定量測值（如 Figma 量到的 `height: 36px`）標 🎨 註明「量測值，待對齊 token」；其**字面值的單一來源是 `prototyper/assets/app.css`（canonical CSS，值權威）**，設計文件與 profile 一律**指向 app.css 不重印**（避免兩處漂移）。
- **對不上既有 token 時停下回報，禁臆造新 token**（與 `figma-design-system/SKILL.md` Step 3、`athena-components.md` 治理原則一致）。

### 2.3 結構化 vs 散文

- §3 / §4 / §5（視覺規格 / variants / states）用 **YAML 風 fenced block**——機器可讀、Figma 可直接寫入。
- 其餘（行為 / a11y / 跨平台 / 文案 / usage）用**散文與表格**——承載判斷。

---

## 3. 元件分層（Lite / Full）

不是每個元件都背全部章節。先判層級，再套對應章節集（SAP Fiori「Table Types 分頁、簡單控制元件從簡」的精神）：

| 層級 | 適用 | 章節集 |
|---|---|---|
| **Lite** | 原子控制元件（Button / TextBox / NumericTextBox / Checkbox / DropDownList / Chip…） | §0 §1 §2 §3 §4 §5 §9 §11* §12 §13（*Content 多半省略或一行） |
| **Full** | 資料密集・複合元件（DataGrid / Stepper / SummaryCard / Dialog / Tab / ListSearch…） | 全部 §0–§13；§4 可分多型、§5 含 empty/loading/error 與多層、§3 並列雙密度、§6 §7 §8 §10 為核心 |

> 判層心法：元件本身**承載資料列 / 多狀態生命週期 / 跨斷點佈局改變** → Full；單一輸入或單一動作的原子 → Lite。拿不準時用 Full（多寫的章節標 N/A 比漏寫安全）。

---

## 4. 章節結構（固定順序）

```
0  Frontmatter      🎨🔗📋  name / category / tier(lite|full) / status(✅⬜🚫) / authority / figma-node / version / last-synced
1  概述 Overview      📋     用途、何時用 / 何時不用（2–3 行）
2  Anatomy           🎨     子元素 / 槽位（可附 get_design_context 樹）
3  視覺規格 Tokens    🎨🔗   結構化 block；data-dense 並列 Default / Comfortable
4  Variants / Types  🎨🔗   variant 軸 → token override；複合元件可分多型（如 DataGrid 顯示型 / 行內編輯型）
5  States            🎨🔗   default/hover/focus/active/disabled/loading；
                            [Full 資料展示型] 必含 empty/loading/error；互動列含 zebra/selected/row-hover（多層）
6  Behavior 行為      📋     [Full] 互動規則表（DataGrid 式）
7  RWD 響應式         📋     [Full] 斷點 + 欄位優先級 P0–P3
8  Keyboard          📋     [Full] Tab/Arrow/Enter/Esc、focus 持留（SLDS 式）
9  a11y 無障礙        📋     ARIA、contrast、touch target、screen reader
10 跨平台 Adaptive    📋     逐斷點：App ≠ 縮小 Web；pop-in / 替代佈局（SAP Fiori 式）
11 Content 指引       📋     [輕量] 該元件特有文案（empty / error / bulk verb+noun）；一般 microcopy → 連 ux-writer
12 API / Props        📋     prop / type / default（與設計指引分離，Ant 式）
13 關聯 Links         🔗     上游 token / prototyper profile / code 元件 / figma file
```

---

## 5. Figma 讀取 → 章節對應（補入 SOP）

| Figma MCP 動作 | 回填章節 | 備註 |
|---|---|---|
| `get_variable_defs` | §3 / §4 / §5 的 `{token}` 與值 | 對映回 `athena-tokens.md` token 名；對不上**停下回報**，勿臆造 |
| `get_design_context` | §2 Anatomy、§3 量測（padding/height）、§4 variant 軸、§5 state 結構 | variant / state 若以 Figma component property 設計可直抽 |
| `get_screenshot` | §5 各 state 視覺驗證 | 多狀態（hover/disabled/error/focus）才需 |
| 人工 / PRD | §1 §6 §7 §8 §9 §10 §11 §12 | Figma 讀不出語意 / 互動 / a11y / 跨平台 / 文案 / 治理 |

**補入流程**：先 `get_variable_defs` 解析 token → 填 §3–5 的 `{token}`；`get_design_context` 補結構與量測；多狀態元件再 `get_screenshot` 驗證；最後人工補 §6–12。每次補入後更新 §0 的 `last-synced` 與 `figma-node`。

**已知 SOP 缺口（演練實證，2026-06-18 Button）**：
- **Effects（shadow / focus-ring）**：演練當下 `athena-tokens.md` 無 shadow 群組，Figma 的 `$shadow-*` 對不上 → 依 §2.2 **停下回報**、未臆造；事後由 Figma 值**backfill** 為 `--ds-shadow-*`（見 tokens §Shadow）。**通則**：遇 token 缺口先停下回報，確認後再從 Figma 真值補入 tokens，**勿在元件文件內就地造值**。
- **`get_variable_defs` 不含 padding/height**：尺寸量測需 `get_design_context` 於**單一 variant** 取（整個元件集 metadata 常 >80k 過大）。
- **Figma 變數命名**：元件級為 `Component_btn/$state`（如 `Primary_btn/$primary-bg-color-hover`），值若為 two-stop（`#2877EE,#FFFFFF`）代表「base + state-layer 疊層」，非單一色；落地以疊層實作。
- **變數即權威**：variant 集 / 互動疊層以 Figma 變數為準，覆蓋散文假設（演練修正了 Button 的 hover 疊層與 variant 集）。

---

## 6. 範例 A — Button（Lite 層）

> 真實 Athena token；對照 `SKILL.md` 現行用 `color.blue.500` 的跨專案佔位版，這是「升級後」長相。

````markdown
---
name: Button
category: 按鈕與動作
tier: lite
status: ✅ 已採用
authority: ERP CLAUDE.md（Syncfusion Button, @syncfusion/ej2-vue-buttons）
figma-node: —          # 🎨 Figma 補入時填 <FILE_KEY>/<NODE_ID>
version: v0.1
last-synced: —
---

## 1. 概述　📋
頁面操作 CTA。每畫面語意上的主動作至多一個（primary）；次要用 secondary / ghost；破壞性用 danger，不用 primary 做取消。

## 2. Anatomy　🎨
[leading icon?] + label + [trailing icon?]；icon-only 時 label 走 sr-only。

## 3. 視覺規格 Tokens　🎨🔗
```yaml
# md（預設）；padding 並列雙密度（Default 緊湊 / Comfortable 舒適）
height:  36px                                   # 🎨 量測值，待對齊 token
padding: { default: "{ds-space-padding-medium} {ds-space-padding-large}",      # 🔗 8/12px
           comfortable: "{ds-space-padding-large} {ds-space-padding-extra-large}" }  # 🔗 12/16px·16/20px
radius:  "{ds-radius-large}"                     # 🔗 8px
font:    "{font-size-sf-text-md} / {font-weight-sf-medium}"  # 🔗 14px / 500
icon:    20px                                    # 🎨
gap:     "{ds-space-padding-small}"              # 🔗 icon↔label 4px
```

## 4. Variants　🎨🔗
```yaml
primary:   { bg: "{color-sf-primary}",         fg: "{color-sf-on-primary}" }      # 🔗 rgb(40 119 238) / white
secondary: { bg: "{color-sf-secondary-btn-bg}", fg: "{color-sf-on-surface}",
             border: "{ds-borderwidth-small} {color-sf-outline}" }                # 🔗 white / rgb(15 23 42) / 1px rgb(127 137 150)
ghost:     { bg: transparent,                   fg: "{color-sf-primary}" }
danger:    { bg: "{color-sf-danger}",           fg: "{color-sf-danger-text}" }     # 🔗 rgb(244 73 62) / white
```

## 5. States　🎨🔗
```yaml
# 疊層走 Material state layer，不自己調色
hover:    { primary_bg: "{color-sf-primary-darken-opacity-5}" }     # 🔗 rgb(31 87 209)
focus:    { ring: "{ds-borderwidth-medium} {color-sf-primary}", offset: "2px" }  # 🔗 2px
active:   { primary_bg: "{color-sf-primary-darken-opacity-10}" }    # 🔗 rgb(26 76 183)
disabled: { bg: "{color-sf-on-surface-opacity12}", fg: "{color-sf-on-surface-opacity38}" }  # 🔗 Material 12% / 38%
loading:  { spinner: "{color-sf-on-primary}", label: dim }
```

## 9. a11y　📋
focus ring `{ds-borderwidth-medium}` / `{color-sf-primary}` / offset 2px；Enter·Space 觸發；icon-only 必填 `aria-label`；mobile min tap 44×44。

## 11. Content　📋
動詞 CTA（「新增」「提交」「作廢」），不用名詞堆疊；破壞性動作文案明確（「作廢」非「確定」）。詳 `ux-writer`。

## 12. API / Props　📋
| Prop | Type | Default |
|---|---|---|
| variant | primary / secondary / ghost / danger | primary |
| size | sm / md / lg | md |
| icon / iconPosition | — / leading·trailing | leading |
| loading / disabled / fullWidth | boolean | false |

## 13. 關聯　🔗
- Tokens：`athena-tokens.md` §Primary / §Space / §Radius
- Prototype 落地：`prototyper`（按鈕原子，散見各 template）
- Code：`@syncfusion/ej2-vue-buttons`
````

---

## 7. 範例 B — 狀態 Chip `st-chip`（Full 取向，variant-heavy）

> 示範「§4 比 §3 厚」的 variant 密集元件，直接對映 `prototyper/profiles/erp-components/DataGrid.md §狀態 Chip`。Chip 雖小，但因承載單據狀態生命週期、有多態與設定檔/作業檔兩套，視為 Full 取向處理 §4。

````markdown
---
name: st-chip（狀態徽章）
category: 回饋與狀態
tier: full        # variant 生命週期密集；視覺雖簡單但狀態語意重
status: ✅ 已採用（含客製 DsStatusBadge）
authority: prototyper DataGrid.md / Stepper.md（狀態色推導，待 DS 正式定義各態）
figma-node: —
version: v0.1
last-synced: —
---

## 3. 視覺規格 Tokens　🎨🔗
```yaml
# 表格內緊湊版（28px）；獨立版（36px）見 Stepper.md voided-banner
height:  28px                          # 🎨
radius:  "{ds-radius-10extra-large}"   # 🔗 全圓 1000px
font:    "{font-size-sf-text-sm} / {font-weight-sf-medium}"  # 🔗 12px / 500
pattern: "底=狀態色 12% tint・邊框+字=狀態色實色"   # 三件套，每態套用
```

## 4. Variants（狀態軸）　🎨🔗
```yaml
# 每態三件套；色 = athena-design.md 狀態語意推導
draft:     { tone: neutral, bg: "{color-sf-surface-variant}", border: "{color-sf-outline}",        text: "{color-sf-on-surface-variant}" }
submitted: { tone: info,    bg: "{color-sf-primary-opacity-12}", border: "{color-sf-primary}",     text: "{color-sf-primary}" }
approved:  { tone: success, bg: "{color-sf-success-opacity12}",  border: "{color-sf-success}",     text: "{color-sf-success}" }
partial:   { tone: info,    base: submitted }     # 進銷存擴充
received:  { tone: info,    base: submitted }
settled:   { tone: info,    base: submitted }
done:      { tone: neutral, base: draft }
voided:    { tone: danger,  bg: "{color-sf-danger-opacity12}",   border: "{color-sf-danger}",      text: "{color-sf-danger}" }
cancelled: { tone: danger,  base: voided }
# 設定檔兩態
active:    { tone: success, base: approved }
inactive:  { tone: danger,  base: voided }
```

## 5. States　🎨🔗
Chip 本身唯讀無互動態；其「狀態值」即 §4 variant。表格列承載 chip 時的 hover/selected 屬 DataGrid，見上游 profile。

## 6. Behavior　📋
唯讀；不可點。狀態推進由 Form Footer 動作或下游回寫驅動（見 Stepper.md 狀態流轉）。

## 11. Content　📋
標籤用既定詞彙（草稿/已提交/已核准/已作廢…），不自創；空值欄位顯示 `—` 而非 chip。

## 13. 關聯　🔗
- 語意色：`athena-design.md` §狀態色
- Prototype 落地（權威 class / 尺寸 / 色）：`prototyper/profiles/erp-components/DataGrid.md §狀態 Chip`、`Stepper.md §voided-banner`
- Code：客製 `DsStatusBadge`（`components/ds/`）
````

> 注意 §4 引入 `base: <variant>` 簡記繼承（如 `partial` 繼承 `submitted` 的三件套），避免重複；Figma 補入時若各態獨立設計則展開為完整 token。

---

## 8. 自檢清單（產出每份設計文件後）

- [ ] §0 Frontmatter 含 tier（lite/full）與 status；Figma 未補的 figma-node / last-synced 標 `—`
- [ ] 視覺值全為 `{token}`，無 raw hex / px（量測值已標 🎨 待對齊）
- [ ] 每個 `{token}` 在 `athena-tokens.md` 找得到對應名
- [ ] Lite 不硬塞 §6–§10；Full 的資料展示型 §5 含 empty/loading/error
- [ ] 來源標記（🎨/🔗/📋）逐區塊標齊
- [ ] §13 三向連結可達（tokens / prototyper profile / code）
- [ ] 已收編而非另定：與 prototyper profile 重疊處用引用，不重寫 token 決策
- [ ] **無重複字面值**：凡 `athena-tokens.md` 已有 token，一律 `{token}` reference；**無對應 token 的具體 CSS 值（疊白實色、元件量測 px、非標準 alpha）不在設計文件、也不在 profile 重印**，改指**值權威 `prototyper/assets/app.css`**（canonical CSS）；狀態機邏輯（stepCur / 矩陣 / JS helper）非 CSS 值，單一來源在 profile / `erp-transaction.md`。要新增 token 須先在 `athena-tokens.md`（上游 CSS / Figma 真值）補定——`athena-tokens.md` 是上游匯出的忠實鏡像，**禁在設計文件就地造 token、或把同一字面值寫死兩份**（避免兩處漂移）。

---

## 9. 背景與落地紀錄

> 此格式於首批元件落地時定版；原獨立決策報告已收編於此。格式規範本身是上方 §1–§8，本節只補「為何要做、怎麼落地、還沒做什麼」。

### 9.1 確立此格式前的現況問題

三套「元件」表述彼此不互引、詳盡度與 token 真偽不一致：

- `design-system-architect` 的 Component Spec（SKILL.md §Step 2）用佔位 token（`color.primary`），真值是 `--color-sf-primary`；
- `prototyper/profiles/erp-components/*.md`（`DataGrid.md` / `Stepper.md`…）是最深的真實規格，卻沒被當「元件設計文件」收編；
- `athena-components.md` 是孤立目錄，✅ 元件沒連到任何設計文件。

→ 解法：確立「逐元件設計文件」為**單一權威格式**（即本檔 §1–§8），三 skill 各就各位指向它。

### 9.2 三 skill 落地分工與狀態

**`design-system-architect`（架構擁有者）**
- ✅ §Step 2 改為「依本 schema 產出設計文件（先判 Lite/Full）」，並補 Figma 補入流程；範例標為跨專案佔位、指向真版。
- ✅ 新增本檔（架構定義）＋ `references/components/Button.md`（首發 Lite 範例，真實 token）。
- ✅ `athena-components.md` 新增「設計文件索引」段，✅ 元件連到 `components/<name>.md`（未產出標待補）。
- ✅ 輸出品質清單補「依 schema 產出」「跨平台採 adaptive」兩項。

**`ui-designer`（消費者）**
- ✅ 「Component 應用」職責補：挑用前到設計文件取 variants/states/usage，不臆測；選 ⬜ 待分類 元件須 flag 治理。
- ✅ Step 2 UI Spec 範例 token 標為跨專案佔位，指向 `athena-design.md` / `athena-tokens.md` 真值。

**`prototyper`（引用者）**
- ✅ `DataGrid.md` / `Stepper.md` / `SummaryCard.md` 開頭改為**三權威分工**區塊（見下方 §9.4）：**值權威＝`prototyper/assets/app.css`**、**用法權威＝profile**（class / 行為 / app.js 邏輯）、**契約權威＝設計文件**。
- profile 已**瘦身**：不再複印 CSS 字面值（px / hex / rgba），改指 app.css；保留 class 名、行為、markup、JS helper。
- 不動 `allowed-tools` 與單檔交付定位。

### 9.3 後續（非本批）

- `references/components/` 已收編：`Button` / `DataGrid` / `Stepper` / `SummaryCard` / `st-chip`（5 件）。其餘 ✅ 已採用元件設計文件待逐步補。
- 首個真實元件做一次「Figma 補入」演練（`get_variable_defs` 填 §3–5），驗證對不上 token 會停下回報。
- 其餘 prototyper 複合 profile（ListSearch / FormGroup / FormFooter）是否各自對應設計文件，待 DS owner 決定（多為佈局 pattern 而非單一 Syncfusion 元件）。
- **已知 class-name 漂移待對齊**（瘦身 profile 時發現，非本批）：`prototyper/profiles/erp-components/*.md` 部分 class 名與 `assets/app.css` 不一致——`.btn-icon.is-primary` vs `.btn-icon-sq--primary`、`.form-field`/`.help`/`h2` vs `.field`/`.field__helper`/`h3`、`.form-footer__actions`/`.btn-more__menu` vs `.form-footer__right`/`.popover`。屬「用法權威」內部一致性，待另案以 app.css 為準收斂。

### 9.4 值權威下沉至 canonical CSS 資產（2026-06-23）

> 背景：以 Figma 校正後，`prototyper/assets/app.css`（+ `assets/ds/`）成為 prototype 產出時**逐字複製不重寫**的 canonical 元件樣式。實測「依 profile 文字規格重寫 app.css」selector 覆蓋率僅約標準一半、容器級數值大量漂移——故樣式準確對齊只能靠複製 canonical CSS。

此後三層元件表述的權威切分由「依元件切」改為「依關注點切」，每層只答一個問題、彼此引用不複印：

| 層 | 角色 | 答的問題 |
|---|---|---|
| `athena-tokens.md` | 值的原子 | token 真值（唯一寫 raw px/hex） |
| **`prototyper/assets/app.css` + `ds/`** | **★值權威** | 編譯後 canonical 實作（無 token 對應的尺寸 / 疊白實色 / alpha 階） |
| `design-system-architect/references/components/*.md` | ★契約權威 | 語意 / variants / states / a11y / 跨平台（token-reference，**不寫 raw 值**） |
| `prototyper/profiles/erp-components/*.md` | ★用法權威 | 套哪個 class / 互動行為 / markup / app.js 邏輯（**不複印 CSS 值**） |
| `ui-designer` | 消費者 | 挑用前查契約；契約未產出時退查 profile + app.css（§9.2） |

落地動作（本批）：① 解開 architect↔profile 互指權威死結（架構 frontmatter `authority` 與 profile 開頭改三權威分工，移除「待收編」語）；② profile md 瘦身刪 CSS 值副本改指 app.css；③ ui-designer 補設計文件未產出時的 fallback；④ 本節同步。
