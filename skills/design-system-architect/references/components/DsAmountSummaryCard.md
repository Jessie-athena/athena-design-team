---
name: DsAmountSummaryCard（金額摘要卡）
category: 財務模組元件
tier: full           # 4 states × expand/collapse behavior × embedded Stepper × show flags → Full
status: ✅ 已產出（ERP 財務模組客製複合元件；Form 頂部摘要卡；4 state：Default/close/Open/Scrolling；顯示收款/沖抵主指標 + 3 步 Stepper + 可展開 9 項子金額）
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: myT6eqCVioMvVMf4PXnSlS/1058:56610
version: v0.1
last-synced: 2026-06-30
---

> 依 `../component-doc-schema.md`（Full 層）產出。視覺字面值（高度 / padding / 顏色實值）權威＝`prototyper/assets/app.css`；本檔＝契約（what/why/token-reference/state/a11y）。
> 來源 Figma 檔：ERP 財務作業模組（`myT6eqCVioMvVMf4PXnSlS`），非 FAI2 library。

## 1. 概述　📋

ERP 財務作業模組（出納、應付款等）**Form View 頂部**的**金額摘要卡**：整合主要金額指標、文件狀態 Stepper，及可展開的子金額明細列。

- **何時用**：財務單據（出納收款單、應付款項等）/進銷存/人事模組 Form View 頂部，作為 SummaryCard 的特化版本。
- **何時不用**：List View 列表行（用 `chips`）；無需金額摘要的設定檔頁面。
- **四種 state（prop 控制）**：
  - `Default`：僅 Block1 主摘要列（完整圓角），無展開面板——最精簡型。
  - `close`：Block1（頂部圓角）+ Block2 收合（顯示「查看明細」link）。
  - `Open`：Block1 + Block2 展開（9 項子金額橫列 + 「收合明細」link）。
  - `Scrolling`：單行緊湊橫列（無 Stepper），供頁面捲動時 sticky 頂部顯示。

---

## 2. Anatomy　🎨

```
DsAmountSummaryCard
│
├─ [Default / close / Open]  Block1（主摘要列，全寬，px-20 py-16）
│   ├─ .account（左區）
│   │   └─ .balance（主指標對）
│   │       ├─ .metric（收款總額）
│   │       │   ├─ .metric__title（label + [info-icon]，gap=4px）
│   │       │   └─ .metric__value（NT$ 0，18px/700）
│   │       ├─ .divider-v（46px 高垂直線，1px，rotate-90）
│   │       └─ .metric（沖抵總額，同結構）
│   └─ .stepper（右區，3 步 Stepper）
│       ├─ step-1-atom（bubble 32px + label，gap=2px）
│       ├─ connector-line（40px）
│       ├─ step-2-atom
│       ├─ connector-line
│       └─ step-3-atom
│
├─ [close only]  Block2（白底，3 邊 4px 邊框，底部圓角）
│   └─ .link-btn（「查看明細」14px/500/info + arrow-down 14px，gap=6px，h=18px）
│
├─ [Open only]   Block2（展開，gap=16px）
│   ├─ .sub-amounts（9 欄 flex-row，gap=24px）
│   │   └─ .sub-metric（flex-col，gap=4px）×9
│   │       ├─ label（14px/400/on-surface-variant）
│   │       └─ value（16px/700/on-surface）
│   └─ .link-btn（「收合明細」+ arrow-up，同 close 型）
│
└─ [Scrolling]   .compact-bar（全寬單行，替代 Block1+Block2；無 Stepper）
    ├─ .compact-item（flex-1，gap=8px，label + value 同行）×9
    └─ .divider-v（24px 高，欄間隔）×8
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

# ── 主指標（收款總額 / 沖抵總額）──
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

divider-v（兩主指標間）:
  height:         "46px"                                              # 🎨（= metric 行高）
  style:          "1px solid line，rotate-90"                         # 📋（rendered as rotate-90 0-width div）

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

# ── Stepper（Block1 右側，三步 3-bubble 型）──
stepper-wrapper:
  layout:         "flex-wrap，items-center"                           # 📋
  gap:            "{ds-space-margin-extralarge}"                      # 🔗 16px（column gap；row gap=0）

stepper-atom（bubble + label 垂直堆疊）:
  inner-gap:      "2px"                                               # 🎨（bubble wrapper gap-below）
  bubble-wrapper-pb: "{ds-space-margin-small}"                       # 🔗 4px（bubble 底部留空）

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

stepper-bubble（pending-tinted，用於已結轉 step-3）:
  bg:             "linear-gradient({color-sf-primary} at 8% over {color-sf-surface})"  # 🎨 淡藍底
  border:         "{ds-borderwidth-small} solid {color-sf-outline-variant}"  # 🔗 同 pending-future
  number-color:   "{color-sf-on-surface}"                             # 🔗 #0F172A

stepper-bubble-number:
  font:           "{font-size-sf-text-md}"                            # 🔗 14px
  weight:         "{font-weight-sf-normal}"                           # 🔗 400
  line-height:    "1.3"                                               # 🔗

stepper-label（active）:
  font / weight:  "{font-size-sf-text-md} / {font-weight-sf-normal}"  # 🔗 14px / 400
  line-height:    "1.3"                                               # 🔗
  color:          "{color-sf-on-surface}"                             # 🔗 #0F172A

stepper-label（inactive）:
  color:          "{color-sf-outline}"                                # 🔗 #7F8996

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
# ── Figma Variants（財務模組 node 1058:56610） ──
state=Default:
  structure:  Block1 only（完整圓角）
  use:        無需展開子明細的精簡版面，或作為嵌入其他卡片的摘要

state=close（Form View 預設態）:
  structure:  Block1（頂部圓角）+ Block2 收合
  use:        Form View 頂部預設顯示；使用者可展開查看子明細

state=Open:
  structure:  Block1 + Block2 展開（9 子金額欄 + 收合 link）
  use:        使用者點擊「查看明細」後

state=Scrolling:
  structure:  單行緊湊橫列（無 Stepper、無 Block2）
  use:        頁面捲動後 sticky 固定頂部；父層 scroll listener 切換

# ── Show flags ──
showInfoLeft:   收款總額 label 右側顯示 Circle info icon（state=Default/close/Open 有效）
showInfoRight:  沖抵總額 label 右側顯示 Circle info icon（全 state 有效）

# ── 9 項子金額固定欄位（Open / Scrolling 顯示）──
# 現金合計 / 匯款合計 / 信用卡合計 / 支票合計 / 其他合計 /
# 匯款手續費 / 匯款郵電費 / 信用卡手續費 / 信用卡郵電費
```

---

## 5. States　🎨🔗

| state prop | Block1 | Block2 | Stepper | Scrolling bar |
|---|---|---|---|---|
| Default | ✅（完整圓角） | ✗ | ✅ | ✗ |
| close | ✅（頂部圓角） | ✅（收合，link「查看明細」） | ✅ | ✗ |
| Open | ✅（頂部圓角，account 改 flex-col） | ✅（展開，9 欄 + link「收合明細」） | ✅ | ✗ |
| Scrolling | ✗ | ✗ | ✗ | ✅ |

*元件無 hover / active / focus 互動態；唯一互動為 link-btn 觸發 state 切換（close ↔ Open）*

---

## 6. Behavior　📋

| 互動 | 行為 |
|---|---|
| 點擊「查看明細」（state=close） | 父層將 state 切換為 `Open`；Block2 展開顯示 9 欄子金額；link 改「收合明細」+ arrow-up |
| 點擊「收合明細」（state=Open） | 父層將 state 切換為 `close`；Block2 收合；link 改「查看明細」+ arrow-down |
| 頁面捲動至 Stepper 超出 viewport | 父層 scroll listener 設 state=`Scrolling`；元件改緊湊橫列 |
| 頁面捲回頂部 | 父層恢復 state=`close` 或 `Open`（依前一態） |

📋 **Scrolling 切換完全由父層控制**：本元件只依 `state` prop 被動渲染，不自行偵測捲動。父層實作 `scroll listener → ref.state = 'Scrolling'`。

---

## 7. RWD / 斷點　📋

| 斷點 | 呈現 |
|---|---|
| ≥ 1280px | 完整顯示；9 欄子金額橫排 |
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

- 收款/沖抵總額：後端計算傳入，格式 `NT$ {千分位}`；幣別前綴 `NT$` 固定（不支援多幣別切換）。
- 9 項子金額固定（不可動態增減）；數值為 0 時仍顯示 `NT$ 0`。
- Stepper 文字：草稿 / 已核准 / 已結轉（固定 copy，不由 prop 傳入）。
- link-btn 文字：「查看明細」/ 「收合明細」（固定 copy）。

---

## 12. API / Props　📋

> production 為 ERP 財務模組客製元件；本表為設計指引層。

| Prop | Type | Default | 說明 |
|---|---|---|---|
| state | `'Default'` \| `'close'` \| `'Open'` \| `'Scrolling'` | `'close'` | 顯示配置（由父層控制，元件不自行管理） |
| showInfoLeft | boolean | false | 收款總額 label 右側顯示 info icon |
| showInfoRight | boolean | false | 沖抵總額 label 右側顯示 info icon |
| totalReceived | number | 0 | 收款總額（NT$） |
| totalOffset | number | 0 | 沖抵總額（NT$） |
| subAmounts | SubAmountMap | `{}` | 9 項子金額 key→value map |
| stepperStatus | `'draft'` \| `'approved'` \| `'transferred'` | `'draft'` | 決定 Stepper active step（1=草稿 / 2=已核准 / 3=已結轉） |

事件：`@toggle-detail`（link-btn 點擊，通知父層切換 state）

---

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §On-* / §Surface / §Outline / §Space / §Typography
- 值權威：`prototyper/assets/app.css`（canonical CSS）
- 同層：
  - `SummaryCard.md`（通用型，本元件為財務領域特化版；共用 sticky 無 shadow 設計語言）
  - `Stepper.md`（Block1 右側嵌入 3-bubble Stepper；bubble/connector 視覺值見 `Stepper.md`）
  - `DsSectionHeader.md`（DsAmountSummaryCard 在 Form 頂部；DsSectionHeader 在其下各 section）
- Figma 來源：ERP 財務作業模組（`myT6eqCVioMvVMf4PXnSlS`），非 FAI2 library
- Code：ERP 財務模組客製元件
