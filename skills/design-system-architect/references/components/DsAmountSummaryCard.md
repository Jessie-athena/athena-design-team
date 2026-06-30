---
name: DsAmountSummaryCard（金額摘要卡）
category: 財務模組元件
tier: full           # 4 states × expand/collapse behavior × embedded Stepper × show flags → Full
status: ✅ SummaryCard 設計契約（財務模組）；指標區與 Stepper 步數依模組配置；4 state Default/close/Open/Scrolling
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: |
  myT6eqCVioMvVMf4PXnSlS/1058:56610（出納）
  myT6eqCVioMvVMf4PXnSlS/2950:93569
  myT6eqCVioMvVMf4PXnSlS/3074:12908
  myT6eqCVioMvVMf4PXnSlS/1967:11975（應付請款）
  myT6eqCVioMvVMf4PXnSlS/2369:122094
  myT6eqCVioMvVMf4PXnSlS/1068:107382
version: v0.2
last-synced: 2026-06-30
---

> 依 `../component-doc-schema.md`（Full 層）產出。視覺字面值（高度 / padding / 顏色實值）權威＝`prototyper/assets/app.css`；本檔＝契約（what/why/token-reference/state/a11y）。
> 來源 Figma 檔：ERP 財務作業模組（`myT6eqCVioMvVMf4PXnSlS`），非 FAI2 library。

## 1. 概述　📋

**本文件為 ERP 財務模組 SummaryCard 的設計契約。** 不同財務模組（出納、應付請款、沖銷、內部調撥等）使用相同的結構骨架，以不同的**指標配置**（指標欄數、運算符、info icon）與 **Stepper 步數**呈現各自的業務語意。

- **何時用**：所有財務模組 Form View 頂部摘要區，顯示主要金額指標 + 文件狀態 Stepper，以及可展開的子金額明細列（視模組而定）。
- **何時不用**：List View 列表行（用 `st-chip`）；Dialog 頂部（用 `DsDialogHeader`）；設定檔頁面無金額摘要需求。
- **四種 state（prop 控制）**：
  - `Default`：僅 Block1 主摘要列（完整圓角），無展開面板——最精簡型。
  - `close`：Block1（頂部圓角）+ Block2 收合（顯示「查看明細」link）。
  - `Open`：Block1 + Block2 展開（子金額橫列 + 「收合明細」link）。
  - `Scrolling`：單行緊湊橫列（無 Stepper），供頁面捲動時 sticky 頂部顯示。

---

## 2. Anatomy　🎨

```
DsAmountSummaryCard
│
├─ [Default / close / Open]  Block1（主摘要列，全寬，px-20 py-16）
│   ├─ .account（左區：指標欄，模式由模組決定）
│   │   └─ .balance（flex-row，gap=16px，items-center）
│   │       │
│   │       │  ── 指標模式 single ──
│   │       └─ .metric（單一欄）
│   │           ├─ .metric__title（label [+ ⓘ icon]，gap=4px）
│   │           └─ .metric__value（NT$ 0，18px/700）
│   │
│   │       │  ── 指標模式 dual ──
│   │       ├─ .metric（左欄）
│   │       ├─ .divider-v（46px 高垂直線，rotate-90）
│   │       └─ .metric（右欄）
│   │
│   │       │  ── 指標模式 formula ──
│   │       ├─ .metric（結果欄）
│   │       ├─ .operator（18px/500 全形字元，如 ＝ ＋ −）
│   │       ├─ .metric（運算元 A）
│   │       ├─ .operator（另一符號，可有多個）
│   │       ├─ .metric（運算元 B）
│   │       └─ [.divider-v + .metric]（公式右側可再加分隔線接另一欄）
│   │
│   └─ .stepper（右區，N 步 Stepper，步數由模組決定）
│       ├─ step-atom（bubble 32px + label，gap=2px）
│       ├─ connector-line（40px）
│       ├─ ... （重複 N-1 次）
│       └─ step-atom（終末步）
│
├─ [close only]  Block2（白底，3 邊 4px 邊框，底部圓角）
│   └─ .link-btn（「查看明細」14px/500/info + arrow-down 14px，gap=6px，h=18px）
│
├─ [Open only]   Block2（展開，gap=16px）
│   ├─ .sub-amounts（N 欄 flex-row，gap=24px）
│   │   └─ .sub-metric（flex-col，gap=4px）× N
│   │       ├─ label（14px/400/on-surface-variant）
│   │       └─ value（16px/700/on-surface）
│   └─ .link-btn（「收合明細」+ arrow-up，同 close 型）
│
└─ [Scrolling]   .compact-bar（全寬單行，替代 Block1+Block2；無 Stepper）
    ├─ .compact-item（flex-1，gap=8px，label + value 同行）× N
    └─ .divider-v（24px 高，欄間隔）× (N-1)
```

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── Block1（主摘要列，三態共用）──
block1:
  bg:             "linear-gradient({color-sf-primary} at 5% over {color-sf-surface})"  # 🎨 rgba(40,119,238,0.05)+white
  padding-x:      "{ds-space-padding-2extralarge}"                    # 🔗 20px
  padding-y:      "{ds-space-padding-extralarge}"                     # 🔗 16px

block1（Default）:
  radius:         "{ds-radius-large}（四角）"                          # 🔗 8px

block1（close / Open）:
  radius:         "top-left {ds-radius-large} / top-right {ds-radius-large}"  # 🔗 8px 頂部兩角

# ── 主指標欄 ──
metric-title:
  font:           "{font-size-sf-text-md}"                            # 🔗 14px
  weight:         "{font-weight-sf-normal}"                           # 🔗 400
  line-height:    "1.5"                                               # 🔗
  color:          "{color-sf-on-surface-variant}"                     # 🔗 #3C4A5B
  tracking:       "0.24px"                                            # 🎨
  align:          "text-right"                                        # 📋

metric-value（主，h6）:
  font:           "{font-size-sf-h6}"                                 # 🔗 18px
  weight:         "{font-weight-sf-bold}"                             # 🔗 700
  line-height:    "1.5"                                               # 🔗
  color:          "{color-sf-on-surface}"                             # 🔗 #0F172A
  align:          "text-right"                                        # 📋

metric-inner-gap（title + value 堆疊）:
  gap:            "{ds-space-margin-small}"                           # 🔗 4px

info-icon:
  size:           "16×16px"                                           # 🎨（Circle info）
  margin-left:    "{ds-space-margin-small}"                           # 🔗 4px（gap from label）

balance-group（close / Default 横排）:
  layout:         "flex-row，items-center"                            # 📋
  gap:            "{ds-space-margin-3extralarge}"                     # 🔗 24px（兩 metric 間）

balance-group（Open，帳戶區改直排）:
  layout:         "flex-col，items-start，justify-center"             # 📋
  gap:            "{ds-space-margin-medium}"                          # 🔗 8px

# ── 指標分隔符 / 運算符 ──
divider-v（dual 型，兩主指標間）:
  height:         "46px"                                              # 🎨（= metric 行高）
  style:          "1px solid line，rotate-90"                         # 📋（rendered as rotate-90 0-width div）

operator（formula 型）:
  font:           "{font-size-sf-h6}"                                 # 🔗 18px（全形字元：＝ ＋ −）
  weight:         "{font-weight-sf-medium}"                           # 🔗 500
  color:          "{color-sf-on-surface}"                             # 🔗 #0F172A
  align:          "text-right"                                        # 📋

# ── Block2（展開面板）──
block2:
  bg:             "{color-sf-surface}"                                # 🔗 white
  border-sides:   "left, right, bottom（無 top）"                     # 📋
  border-width:   "{ds-borderwidth-large}"                            # 🔗 4px
  border-color:   "#f4f8fe"                                           # 🎨（≈ primary-4%；無 token，由 app.css 管理）
  padding:        "{ds-space-padding-extralarge}"                     # 🔗 16px all sides
  radius:         "bottom-left {ds-radius-large} / bottom-right {ds-radius-large}"  # 🔗 8px

block2-open-gap（sub-amounts + link-btn 間）:
  gap:            "{ds-space-padding-extralarge}"                     # 🔗 16px

# ── 子金額欄（Open 展開 / Scrolling 各欄）──
sub-metric-label:                                                     # 同 metric-title
  font:           "{font-size-sf-text-md}"                            # 🔗 14px
  weight:         "{font-weight-sf-normal}"                           # 🔗 400
  color:          "{color-sf-on-surface-variant}"                     # 🔗 #3C4A5B

sub-metric-value（16px）:
  font:           "{font-size-sf-text-lg}"                            # 🔗 16px
  weight:         "{font-weight-sf-bold}"                             # 🔗 700
  color:          "{color-sf-on-surface}"                             # 🔗 #0F172A

sub-amounts-row（Open，各欄 flex-col）:
  gap（欄間）:    "{ds-space-margin-3extralarge}"                     # 🔗 24px
  each-col:       "flex-1，flex-col，gap={ds-space-margin-small}"     # 🔗 4px

sub-amounts-row（Scrolling，inline label+value）:
  gap（欄間）:    "{ds-space-margin-medium}"                          # 🔗 8px（含 divider slot）
  each-col:       "flex-1，flex-row，items-center，gap={ds-space-margin-medium}"  # 🔗 8px

divider-v（Scrolling 欄分隔）:
  height:         "24px"                                              # 🎨
  style:          "1px solid line，rotate-90"                         # 📋

# ── Link Button（查看 / 收合明細）──
link-btn:
  height:         "18px"                                              # 🎨
  gap（text+icon）: "6px"                                             # 🎨
  text-font:      "{font-size-sf-text-md}"                            # 🔗 14px
  text-weight:    "{font-weight-sf-medium}"                           # 🔗 500
  text-color:     "{color-sf-info}"                                   # 🔗 #2E90FA（info 藍）
  text-tracking:  "0.1px"                                             # 🎨
  text-line-height: "1.3"                                             # 🔗
  icon-size:      "14×14px"                                           # 🎨（keyboard-arrow-down / up）

# ── Stepper（Block1 右側）──
stepper-wrapper:
  layout:         "flex-wrap，items-center"                           # 📋
  gap:            "{ds-space-margin-extralarge}"                      # 🔗 16px（column gap；row gap=0）

stepper-atom（bubble + label 垂直堆疊）:
  inner-gap:      "2px"                                               # 🎨（bubble wrapper gap-below）
  bubble-wrapper-pb: "{ds-space-margin-small}"                        # 🔗 4px（bubble 底部留空）

stepper-bubble:
  size:           "32×32px"                                           # 🎨
  radius:         "{ds-radius-full}"                                  # 🔗 圓形（rounded-[100px]）

stepper-bubble（active-current）:
  bg:             "{color-sf-primary}"                                # 🔗 #2877EE（filled）
  border:         "{ds-borderwidth-small} solid {color-sf-primary}"   # 🔗 1px
  number-color:   "{color-sf-surface}"                                # 🔗 white

stepper-bubble（pending-future）:
  bg:             "transparent"                                       # 📋
  border:         "{ds-borderwidth-small} solid {color-sf-outline-variant}"  # 🔗 1px #D7DAE0
  number-color:   "{color-sf-on-surface}"                             # 🔗 #0F172A

stepper-bubble（pending-tinted）:
  bg:             "linear-gradient({color-sf-primary} at 8% over {color-sf-surface})"  # 🎨 淡藍底
  border:         "{ds-borderwidth-small} solid {color-sf-outline-variant}"  # 🔗 同 pending-future
  number-color:   "{color-sf-on-surface}"                             # 🔗 #0F172A
  # 📋 出現於出納模組（3步 treasury 型）的步驟 2、步驟 3

stepper-bubble-number:
  font:           "{font-size-sf-text-md}"                            # 🔗 14px
  weight:         "{font-weight-sf-normal}"                           # 🔗 400
  line-height:    "1.3"                                               # 🔗

stepper-label（active）:
  font / weight:  "{font-size-sf-text-md} / {font-weight-sf-normal}"  # 🔗 14px / 400
  line-height:    "1.3"                                               # 🔗
  color:          "{color-sf-on-surface}"                             # 🔗 #0F172A

stepper-label（inactive）:
  color:          "{color-sf-outline}"                                # 🔗 #7F8996（或 placeholder #67717E，依版本）

stepper-connector-line:
  width:          "40px"                                              # 🎨
  style:          "1px solid（color 對應 Stepper.md）"                # 🔗 見 Stepper.md §line

# ── Scrolling（緊湊單行，替代 Block1+Block2）──
scrolling:
  bg:             "同 Block1（{color-sf-primary} at 5% over {color-sf-surface}）"  # 🔗
  padding-x:      "{ds-space-padding-2extralarge}"                    # 🔗 20px
  padding-y:      "{ds-space-padding-extralarge}"                     # 🔗 16px
  radius:         "{ds-radius-large}（四角）"                          # 🔗 8px
```

---

## 4. Variants　🎨🔗

```yaml
# ── State Variants（跨模組通用）──
state=Default:
  structure:  Block1 only（完整圓角）
  use:        無需展開子明細的精簡版面；無 Block2 的模組通用此態

state=close（Form View 預設態）:
  structure:  Block1（頂部圓角）+ Block2 收合
  use:        Form View 頂部預設；使用者可展開查看子明細

state=Open:
  structure:  Block1 + Block2 展開（子金額欄 + 收合 link）
  use:        使用者點擊「查看明細」後

state=Scrolling:
  structure:  單行緊湊橫列（無 Stepper、無 Block2）
  use:        頁面捲動後 sticky 固定頂部；父層 scroll listener 切換

# ── Show flags ──
showInfoLeft:   左側主指標 label 右側顯示 Circle info icon（ⓘ 16px）
showInfoRight:  右側主指標 label 右側顯示 Circle info icon（ⓘ 16px）

# ── Content Configurations by Module ──
# 結構骨架與視覺 tokens 跨模組一致；以下僅記指標配置與 Stepper 步數差異

# 指標模式：single（單一欄，無分隔符）
single:
  結構: 一個 .metric，無 divider-v / operator
  Block2: 通常不存在（Default 態）
  例:   node 2369:122094 — 沖銷金額合計

# 指標模式：dual（雙欄 + 垂直分隔線）
dual:
  結構: .metric + .divider-v（46px）+ .metric
  例 A: node 1058:56610 — 收款總額 / 沖抵總額（出納；Block2 ✅；3步 treasury 型）
  例 B: node 1967:11975 — 應付總額 ⓘ / 未付總額 ⓘ（應付請款；showInfo=true；Block2 ✅ close 態；6步）

# 指標模式：formula（多欄 + 運算符文字）
formula:
  結構: .metric + .operator + .metric [+ .operator + .metric ...] [+ .divider-v + .metric]
  operator 字元: 18px/500 全形（＝ ＋ −），等同普通文字節點
  例 A: node 3074:12908 — 差額 ＝ 應付扣抵合計 − 沖抵金額合計（3步 canonical）
  例 B: node 2950:93569 — 請款總額 ＝ 應付總額 ＋ 其它款總額 | 已付金額（4步；分隔線接 已付金額）
  例 C: node 1068:107382 — 移出合計 ＝ 移入合計（3步 canonical）

# ── Stepper 步數已知配置 ──
stepper-3step-treasury:
  步驟: 草稿 → 已核准 → 已結轉
  出現: 出納（node 1058:56610）
  特點: 省略「已提交」；step-2/step-3 bubble 用 pending-tinted 樣式（淡藍底）

stepper-3step-canonical:
  步驟: 草稿 → 已提交 → 已核准
  出現: node 3074:12908 / 2369:122094 / 1068:107382

stepper-4step:
  步驟: 草稿 → 已提交 → 已核准 → 部分付款
  出現: node 2950:93569

stepper-6step:
  步驟: 草稿 → 已提交 → 已核准 → 發票到 → 部分請款 → 部分付款
  出現: node 1967:11975（應付請款）

# ── Block2 存在規則 ──
block2-exists:   指標區有子明細可展開時（如 收款子分類、請款明細）→ state 有 close / Open 意義
block2-absent:   指標為彙總數字、無需展開的模組 → 只有 Default 與 Scrolling 兩態有意義
```

---

## 5. States　🎨🔗

| state prop | Block1 | Block2 | Stepper | Scrolling bar |
|---|---|---|---|---|
| Default | ✅（完整圓角） | ✗ | ✅ | ✗ |
| close | ✅（頂部圓角） | ✅（收合，link「查看明細」） | ✅ | ✗ |
| Open | ✅（頂部圓角，account 改 flex-col） | ✅（展開，子欄 + link「收合明細」） | ✅ | ✗ |
| Scrolling | ✗ | ✗ | ✗ | ✅ |

*元件無 hover / active / focus 互動態；唯一互動為 link-btn 觸發 state 切換（close ↔ Open）*

---

## 6. Behavior　📋

| 互動 | 行為 |
|---|---|
| 點擊「查看明細」（state=close） | 父層將 state 切換為 `Open`；Block2 展開顯示子金額；link 改「收合明細」+ arrow-up |
| 點擊「收合明細」（state=Open） | 父層將 state 切換為 `close`；Block2 收合；link 改「查看明細」+ arrow-down |
| 頁面捲動至 Stepper 超出 viewport | 父層 scroll listener 設 state=`Scrolling`；元件改緊湊橫列 |
| 頁面捲回頂部 | 父層恢復 state=`close` 或 `Open`（依前一態） |

📋 **Scrolling 切換完全由父層控制**：本元件只依 `state` prop 被動渲染，不自行偵測捲動。

---

## 7. RWD / 斷點　📋

| 斷點 | 呈現 |
|---|---|
| ≥ 1280px | 完整顯示；子金額欄橫排 |
| ≤ 1024px | 元件全寬縮減；子金額欄數不減，內容可能截斷——由父層決定是否加 `overflow-x: auto` |

---

## 8. Keyboard　📋

- link-btn：`Tab` 可達；`Enter` / `Space` 觸發 toggle（close ↔ Open）。
- Stepper：純顯示，無鍵盤互動（`tab-index="-1"` 或 `aria-hidden="true"`）。
- info-icon：若帶 tooltip，需 `Tab` 可達 + `aria-describedby` 連結。

---

## 9. a11y　📋

- link-btn：`<button type="button">` + `aria-expanded="true/false"` + `aria-controls="[block2-id]"`。
- Block2 panel：`id="[block2-id]"` + `aria-hidden="true/false"` 配合 link-btn。
- info-icon：提供 `aria-label` 說明文字；若為裝飾性可 `aria-hidden="true"`。
- Stepper：整段 `aria-hidden="true"`；文件狀態另以 `<span class="sr-only">` 補充（如「目前狀態：草稿」）。
- 色彩對比（Block1 bg ≈ #F4F8FE on-surface）：
  - label on-surface-variant (#3C4A5B) on primary-5% ≈ 7.0:1 ✅
  - value on-surface (#0F172A) on primary-5% ≈ 14:1 ✅
  - link-btn info (#2E90FA) on white ≈ 2.9:1 ⚠️（14px/500 — 低於 AA 4.5:1；Figma 既有值；建議 DS review 或使用 `{color-sf-info-strong}` 加深）
  - stepper active bubble white on primary (#2877EE) ≈ 3.2:1 ⚠️（14px/400 — 低於 AA normal text；已知限制，Figma 既有設計）

---

## 11. Content　📋

- **指標欄位**：由各模組配置決定（label 固定文字；value 由後端傳入，格式 `NT$ {千分位}`）。
- **Stepper 步驟**：步數與步驟名稱由模組決定（見 §4 Content Configurations）；非通用固定值。
- **子金額欄（Open / Scrolling）**：各模組自行定義欄位數與欄位名稱；出納模組為 9 欄固定。
- **link-btn 文字**：「查看明細」/ 「收合明細」（固定 copy，不由 prop 傳入）。

---

## 12. API / Props　📋

> production 為 ERP 財務模組客製元件；本表為設計指引層。指標欄位與子金額 props 由各模組自行擴充。

| Prop | Type | Default | 說明 |
|---|---|---|---|
| state | `'Default'` \| `'close'` \| `'Open'` \| `'Scrolling'` | `'close'` | 顯示配置（由父層控制） |
| showInfoLeft | boolean | false | 左側主指標 label 右側顯示 info icon |
| showInfoRight | boolean | false | 右側主指標 label 右側顯示 info icon |
| stepperStatus | string | `'draft'` | 決定 Stepper active step；可能值由模組 Stepper 步數決定 |

*各模組另有自身的金額 props（如 totalReceived / totalOffset）與子明細 props，非跨模組通用，不列於此。*

事件：`@toggle-detail`（link-btn 點擊，通知父層切換 state）

---

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §On-* / §Surface / §Outline / §Space / §Typography
- 值權威：`prototyper/assets/app.css`（canonical CSS）
- 同層：
  - `SummaryCard.md`（prototyper usage 規範；本文件為 SummaryCard 設計契約，兩者互補：本檔 = 設計契約（what/why/token）；SummaryCard.md = 用法規範（when/how to prototype））
    → 路徑：`prototyper/profiles/erp-components/SummaryCard.md`
  - `Stepper.md`（Block1 右側嵌入 Stepper；bubble/connector 視覺值見 `Stepper.md`）
  - `DsSectionHeader.md`（DsAmountSummaryCard 在 Form 頂部；DsSectionHeader 在其下各 section）
- Figma 來源：ERP 財務作業模組（`myT6eqCVioMvVMf4PXnSlS`），非 FAI2 library
- Code：ERP 財務模組客製元件
