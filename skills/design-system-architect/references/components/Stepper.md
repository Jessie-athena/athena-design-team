---
name: Stepper（狀態流程指示器）
category: 流程與其他
tier: full           # 承載單據狀態生命週期、多 pattern（基本/驗收/結轉）、多步序狀態與終止分支 → Full
status: ✅ 已產出（Syncfusion Stepper；ERP 落地以 .stepper class）
authority: 契約＝本檔；視覺值落地（語意色 / bubble·line 尺寸）＝prototyper/assets/app.css（值權威）；判定邏輯 / JS helper / voided-banner 行為＝prototyper/profiles/erp-components/Stepper.md（用法權威）；狀態機 erp-transaction.md
figma-node: —        # 🎨 Figma 補入時填 <FILE_KEY>/<NODE_ID>
version: v0.1
last-synced: —
---

> 依 `../component-doc-schema.md`（Full 層）產出。三權威分工：**視覺值（語意色實色 / bubble·line·label 尺寸）權威＝`prototyper/assets/app.css`（canonical CSS，複製不重寫）**；**判定邏輯 / JS helper（`stepCur` 等）/ voided-banner 行為權威＝`prototyper/profiles/erp-components/Stepper.md`**（見 §13；JS 邏輯非 CSS，落 `app.js`，由 profile 主導）；**本檔＝契約**（what/why/token-reference/state/a11y）。狀態機（含七值驗收 / 6 值結轉模型）權威在 `erp-transaction.md`。

## 1. 概述　📋

作業檔 Form View Summary Card 內的**流程指示器**，以固定步驟呈現單據生命週期（草稿→已提交→已核准→…）。

- **Read-only**：純反映單據狀態，**不接受點擊**；狀態推進一律由 Form Footer 動作按鈕（或下游單據回寫 / 結轉結果）驅動。
- 何時用：作業檔（transaction）必載；**設定檔無 Stepper**（`erp-setup.md` 明定）。
- 終止分支（`voided` 已作廢 / `cancelled` 已取消）**不進入 Stepper**，整段以 `.voided-banner` 取代（見 §5）。

## 2. Anatomy　🎨

```
.stepper（display:flex; align-items:flex-start; gap:16px）
  ├─ .stepper__step--<state>
  │    ├─ .stepper__bubble   ← 數字或 check 圖示
  │    └─ .stepper__label    ← 狀態名
  ├─ .stepper__line[.is-done|.is-current]   ← 步驟間連接線
  └─ …（重複）
voided / cancelled 時整段改 .voided-banner（內含一顆 danger st-chip）
```

子元素：bubble（圓形步點）、label（步驟名）、line（連接線）。終態步為動態插槽（驗收 / 結轉模型專屬，見 §4）。

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# token-ref 的值在此給；無 token 的量測 px（bubble/check/label/line 尺寸）不重印，單一來源見 profile §尺寸與字體
bubble:
  size:    "見 profile §尺寸與字體"           # 量測值無 token
  radius:  "{ds-radius-10extra-large}"       # 🔗 圓形 999/1000px
  border:  "{ds-borderwidth-small} {color-sf-outline-variant}"   # 🔗 1px 預設灰框（current/終態步改 {color-sf-primary}，見 §5）
  font:    "{font-size-sf-text-md} / {font-weight-sf-normal}"    # 🔗 14px / 400（內字或數字）
  check:   "見 profile"                      # material-symbols:check（筆畫型無 -outline）
label:
  font:    "size 見 profile / {font-weight-sf-normal}"  # label 字級無對應 size token（介於 sm12/md14）；當前·終態 {font-weight-sf-medium} 500
  fg:      "{color-sf-on-surface}"           # 🔗 主要文字；pending/placeholder 用 {color-sf-on-surface-variant}
line:
  height / flex / align: "見 profile §尺寸與字體"   # 量測值無 token（高度 / flex 28–56px / margin 對齊）
container-gap: "見 profile（≈ {ds-space-padding-extra-large} Default 概念對齊）"
motion:  "background / color 200ms ease-out；尊重 prefers-reduced-motion → 0ms"
```

> bubble / line / label 的量測 px `athena-tokens.md` 無精確對應；**單一來源在 `prototyper/assets/app.css`**（canonical CSS；profile `§尺寸與字體` 提供 class / 語意導引），本檔不重印（schema §8）。

## 4. Patterns / Types　🎨🔗

步數**動態**：依各模組 PRD 狀態機決定，**最少 3 步、上不封頂**（PRD 沒列的狀態禁自動補）。三種已驗證 pattern 共用結構命名、判定邏輯與樣式 token，差異只在**終態步（動態插槽）的狀態集與 label**；**不可混用**於同一模組：

| pattern | 狀態集 | 終態步呈現 |
|---|---|---|
| **basic** 基本型 | canonical 4 值（draft / submitted / approved + voided 分支） | 無動態插槽 |
| **acceptance** 驗收模型 | 7 值（+ partial / received / settled），由下游驗收單回寫；首見採購單 | partial / received / settled — **一律 primary 藍**，label 異 |
| **convert** 結轉模型 | 6 值（+ partial / done / cancelled），由本單結轉進度驅動；首見請購單 | partial / done — **一律 primary 藍**（2026-06-21 取消靛 / 灰），label 異；未達為 placeholder 灰 |

> 終態（動態插槽）步永遠以 `step4Class` / `step4Label` 計算，**不套**一般 `--current` 判定，但**配色與當前步相同**（皆 primary 藍 + 內白環，見 §5）。
> **stepCur 步序映射、判定邏輯、JS helper（`stepCur` / `stepState` / `stepClass` / `lineClass` / `step4Class` / `step4Label`）的權威在 prototyper `Stepper.md` profile**（schema §8「不重寫」）；本檔不重印映射物件，需查映射請見 profile / `erp-transaction.md` 狀態機。

## 5. States　🎨🔗

### 步驟狀態（每步 `n` 相對 `stepCur`）

```yaml
# 判定：n < 當前 → done；n = 當前 → current；n > 當前 → pending（無 modifier）
done:        { bubble: "{color-sf-success}", icon: check, label: "{color-sf-on-surface}" }       # 🔗 綠 #12B76A（已通過的中間步）
current:     { bubble: "{color-sf-primary} + 內白環 inset 1px {color-sf-surface}", label: "{color-sf-on-surface} / 500" }  # 🔗 藍 #2877EE
pending:     { bubble: "primary 8% 疊白 + {color-sf-outline-variant} 框", label: "{color-sf-on-surface-variant}" }  # 🔗 疊層用 {color-sf-primary-opacity-8}；落地實色見 profile
placeholder: { bubble: "同 pending（終態步未達）", label: "{color-sf-on-surface-variant}" }
# ── 終態（動態插槽）步：不套 --current 判定，但配色＝當前步（2026-06-21 統一）──
partial / final / settled:
  bubble: "一律 {color-sf-primary} 藍 + 內白環 inset 1px {color-sf-surface}（= current 視覺；驗收 / 結轉模型皆同）"
  label:  "{color-sf-on-surface} / 500（label 由 step4Label 區分：部分驗收·採購 / 已驗收·已結案 / 已結清）"
```

> 當前步 / 終態步 bubble 完整規格：`border-radius: 100px`（{ds-radius-10extra-large} 全圓）/ `border: {ds-borderwidth-small} {color-sf-primary}` / `fill: {color-sf-primary}` / `box-shadow: 0 0 0 1px {color-sf-surface} inset`（內白環）。

### 連接線 `.stepper__line`

```yaml
default:    "{color-sf-outline-variant}"   # 🔗 #D7DAE0 灰；區段在當前步之後（未達）
is-done:    "{color-sf-success}"           # 🔗 綠；區段在當前步之前（已過）
is-current: "{color-sf-primary}"           # 🔗 藍；區段正連向當前步
```

### 終止分支 → voided-banner

`voided`（驗收 / canonical）或 `cancelled`（結轉）時整個 Stepper 隱藏，原位改 `.voided-banner`，內含一顆 danger `st-chip`（standalone 36px 版）：

```yaml
banner-chip:    # = st-chip standalone（danger variant），見 st-chip.md §3 standalone
  height:  36px                              # 🎨
  radius:  "{ds-radius-9extra-large}"        # 🔗 48px
  padding: "{ds-space-padding-medium} {ds-space-padding-extra-large}"  # 🔗 8px / 16px
  bg:      "{color-sf-danger-opacity12}"     # 🔗 danger 12%
  border:  "{ds-borderwidth-small} {color-sf-danger}"  # 🔗 1px 實色
  text:    "{color-sf-danger} / {font-size-sf-text-sm} / {font-weight-sf-medium}"  # 🔗 紅 / 12px / 500
```

> **語意色僅四種**：綠（已完成的中間步）、藍（當前步 + 所有終態步，含 partial / received / settled / done）、紅（作廢 / 取消，走 voided-banner）、中性灰（未達 / placeholder）。**禁**為個別狀態另造新色（2026-06-21 取消「靛」與「結轉已結案灰」）。
> pending / placeholder bubble 底為 primary 8% 疊層；sticky 不需，落地實色細節以 profile 為準。

## 6. Behavior　📋

- **唯讀、不可點**；狀態推進由 Form Footer 動作（或下游回寫 / 結轉）驅動。
- 驗收模型的「部分驗收 / 已驗收」**非按鈕觸發**，由驗收單的驗收量決定（尚有未到量=部分、全到齊=已驗收）。
- 再次核准警示：單據曾被「取消核准」（`was_unapproved`）回到已提交時，表單須顯示警示橫幅「此單曾被取消核准，請重新確認明細後再次提交核准。」
- 狀態流轉圖（主線 / 驗收 / 回退 / 終止）與結轉模型流轉（`action_convert_po` / `action_cancel`）以 profile §狀態流轉 + `erp-transaction.md` 為權威。

## 7. RWD / 響應式　📋

- 桌面 / 平板：水平排列，連接線自適應伸縮（flex 量測值見 profile §尺寸與字體）。
- 窄螢幕：step 固定最小寬（見 profile）；步數多時容器可橫向捲動，**不**壓縮 bubble 尺寸。
- 與 Summary Card 佈局連動：Layout A 上區右側 / Layout B 右區（見 `SummaryCard.md`）。

## 8. Keyboard　📋

Read-only 元件，**不在 Tab 順序內**（無互動焦點）。狀態變更入口為 Form Footer 按鈕，鍵盤可達性歸屬該處（見 `FormFooter.md`）。螢幕閱讀器需能讀出「目前步驟 N / 共 M：<label>」。

## 9. a11y　📋

- 狀態不可僅靠顏色——每步有 label 文字、當前步以數字 + 樣式雙重標示、done 步以 check 圖示佐證（色盲可辨）。
- 建議容器 `role="list"`、各步 `role="listitem"`；當前步加 `aria-current="step"`。
- voided-banner 的 st-chip label 即狀態名（見 `st-chip.md §9`）。
- 動畫尊重 `prefers-reduced-motion: reduce` → 轉場改 `0ms`，無彈跳 / 視差。

## 10. 跨平台 Adaptive　📋

- App ≠ 縮小 Web：手機上若步數 > 4，採**當前步聚焦**樣式（顯示「第 N / M 步：<label>」+ 前後步縮圖），而非強塞整條 stepper 橫捲。
- 終止分支在任何平台皆以 voided-banner 取代，不保留灰階 stepper 殘影。

## 11. Content　📋

- 步驟 label 用既定狀態詞彙（草稿 / 已提交 / 已核准 / 部分驗收 / 已驗收 / 已結清 / 已結案），**不自創**同義詞，對齊 `st-chip.md` 詞彙表。
- voided-banner 文案「已作廢」/「已取消」固定。
- 再次核准警示文案見 §6。一般 microcopy 詳 `ux-writer`。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| pattern | basic / acceptance / convert | basic | 對映 §4 三型；不可同模組混用 |
| status | <狀態值> | draft | 當前單據狀態，驅動 `stepCur` |
| steps | Step[] | — | label + 第 ④ 步插槽設定 |

> production 以 Syncfusion Stepper + 模組狀態機落地；JS helper 簽名（純函式版 vs `app.js` 閉包版）見 profile §引用程式碼。本表為設計指引層，與實作 API 分離。

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §Status — Success·Danger / §Outline / §Radius / §Typography / §Space
- 語意對照：`../athena-design.md` §狀態色（綠=完成 / 藍=進行中 / 紅=作廢）
- 子元件：`st-chip.md`（voided-banner 內 danger 徽章 = standalone 36px 版）
- **值權威（語意色實色 / bubble·line·label 尺寸，勿在此重寫）**：`prototyper/assets/app.css`（canonical CSS）
- **用法權威（判定邏輯 / voided-banner 行為 / JS helper）**：`prototyper/profiles/erp-components/Stepper.md`
- 狀態機權威：`prototyper/profiles/erp-transaction.md §State Machine` / §進銷存擴充狀態機
- 佈局載體：`prototyper/profiles/erp-components/SummaryCard.md`（Layout A/B 動態插槽）
- Code：Syncfusion Stepper；helper 見 profile

---

## 量測值（落地權威在 profile，本檔不重印）

> 以下為元件專屬量測值，非可複用 design token——單一來源在 `prototyper/assets/app.css`（canonical CSS；profile `Stepper.md §尺寸與字體` 提供 class / 語意導引），本檔僅引用、不重寫（schema §8）：bubble 32px / line 2px·28–56px / container gap 16px / label 13px。需精確值請見 app.css。

> **已解（2026-06-21）**：原「結轉模型靛色」與「pending 疊白實色無 token」兩項缺口——靛色經設計裁示**取消**（終態步一律 primary 藍，無需新色 / 新 token）；pending 疊層用既有 `{color-sf-primary-opacity-8}`，sticky 實色非 Stepper 所需。Stepper 已無待 DS 補定的 token 缺口。
