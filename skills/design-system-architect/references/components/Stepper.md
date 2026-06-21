---
name: Stepper（狀態流程指示器）
category: 流程與其他
tier: full           # 承載單據狀態生命週期、多 pattern（基本/驗收/結轉）、多步序狀態與終止分支 → Full
status: ✅ 已採用（Syncfusion Stepper；ERP 落地以 .stepper class）
authority: prototyper Stepper.md（步序狀態 / 語意色 / 判定邏輯 / voided-banner 落地權威）；狀態機 erp-transaction.md
figma-node: —        # 🎨 Figma 補入時填 <FILE_KEY>/<NODE_ID>
version: v0.1
last-synced: —
---

> 依 `../component-doc-schema.md`（Full 層）產出。**步序狀態 / 語意色 / 判定邏輯 / voided-banner / JS helper 的落地權威是 `prototyper/profiles/erp-components/Stepper.md`**（見 §13）；本檔做 what/why/token/state/a11y 整理與 token-reference，重疊處引用、不重寫決策（schema §8）。狀態機（含七值驗收 / 6 值結轉模型）權威在 `erp-transaction.md`。

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

子元素：bubble（圓形步點）、label（步驟名）、line（連接線）。第 ④ 步為動態插槽（驗收 / 結轉模型專屬，見 §4）。

## 3. 視覺規格 Tokens　🎨🔗

```yaml
bubble:
  size:    32px                              # 🎨 量測值
  radius:  "{ds-radius-10extra-large}"       # 🔗 圓形 999/1000px
  border:  "{ds-borderwidth-small} {color-sf-outline-variant}"   # 🔗 1px 預設灰框
  font:    "{font-size-sf-text-md} / {font-weight-sf-normal}"    # 🔗 14px / 400（內字或數字）
  check:   18px                              # 🎨 material-symbols:check（筆畫型無 -outline）
label:
  font:    "13px / {font-weight-sf-normal}"  # 🎨🔗 13px（無對應 size token，介於 sm12/md14）；當前·終態 {font-weight-sf-medium} 500
  fg:      "{color-sf-on-surface}"           # 🔗 主要文字；pending/placeholder 用 {color-sf-on-surface-variant}
line:
  height:  2px                               # 🎨
  flex:    "1 1 28px（min 28 / max 56px）"
  align:   "margin-top: 16px 對齊圓心"
container-gap: 16px                          # 🎨（= {ds-space-padding-extra-large} Default 概念對齊）
motion:  "background / color 200ms ease-out；尊重 prefers-reduced-motion → 0ms"
```

> label `13px` 與 line `28/56px`、bubble `32px` 為量測值，`athena-tokens.md` 無精確對應；落地以 profile 為準，待 DS 對齊 size token。

## 4. Patterns / Types　🎨🔗

步數與狀態集**依各模組 PRD 狀態機決定**（PRD 沒列的狀態禁自動補）。三種已驗證 pattern 共用結構命名、判定邏輯與樣式 token，差異只在**第 ④ 步動態插槽**；**不可混用**於同一模組：

```yaml
basic:            # 基本型 3 步
  states:  "canonical 4 值（draft / submitted / approved + voided 分支）"
  stepCur: "{ draft:1, submitted:2, approved:3 }"
  step4:   none   # 無第 ④ 步與 step4 helper
acceptance:       # 驗收模型 4 步（動態第 ④ 步）
  states:  "7 值（+ partial / received / settled），由下游驗收單回寫；首見採購單"
  stepCur: "{ draft:1, submitted:2, approved:3, partial:4, received:5, settled:4, voided:3 }"
  step4:   "partial 藍『部分驗收』/ received 藍『已驗收』(終態) / settled 藍『已結清』(終態)"
convert:          # 結轉模型 4 步（動態第 ④ 步）
  states:  "6 值（+ partial / done / cancelled），由本單結轉進度驅動；首見請購單"
  stepCur: "{ draft:1, submitted:2, approved:3, partial:4, done:5, cancelled:3 }"
  step4:   "partial 靛『部分採購』/ done 灰『已結案』(終態) / placeholder"
```

> 第 ④ 步是例外，永遠以 `step4Class` / `step4Label` 計算，**不套**一般 `--current` 判定（見 §5）。狀態 → 步序完整映射與 JS helper（`stepCur` / `stepState` / `stepClass` / `lineClass` / `step4Class` / `step4Label`）以 profile 為權威。

## 5. States　🎨🔗

### 步驟狀態（每步 `n` 相對 `stepCur`）

```yaml
# 判定：n < 當前 → done；n = 當前 → current；n > 當前 → pending（無 modifier）
done:        { bubble: "{color-sf-success}", icon: check, label: "{color-sf-on-surface}" }       # 🔗 綠 #12B76A
current:     { bubble: "{color-sf-primary} + 內白環(inset 1px #fff)", label: "{color-sf-on-surface} / 500" }  # 🔗 藍 #2877EE
pending:     { bubble: "primary 8% 疊白 + {color-sf-outline-variant} 框", label: "{color-sf-on-surface-variant}" }  # 🎨🔗 底⚠疊白實色
placeholder: { bubble: "同 pending（第 ④ 步未確定）", label: "{color-sf-on-surface-variant}" }    # 第 ④ 步專屬
# ── 第 ④ 步終態（不套 --current）──
partial:     { acceptance: "{color-sf-primary} 藍 + 內白環", convert: "靛色填充（見 SummaryCard.md）" }   # 進行中
final:       { acceptance: "{color-sf-primary} 藍 + 內白環", convert: "灰色填充（已結案，刻意不用 done 綠避混淆）" }  # received / done 終態
settled:     { bubble: "{color-sf-primary} 藍 + 內白環（僅驗收模型）" }                              # 已結清終態
```

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

> **語意色僅五種**：綠（完成）、藍（當前 / 進行中 / 驗收終態）、靛（結轉 partial）、紅（作廢 / 取消）、中性灰（未達 / 結轉已結案）。**禁**為個別狀態另造新色。
> ⚠️ pending/placeholder bubble 底「primary 8% 疊白實色」無單一 token；落地見 profile。靛色（結轉 partial）詳 `SummaryCard.md`。

## 6. Behavior　📋

- **唯讀、不可點**；狀態推進由 Form Footer 動作（或下游回寫 / 結轉）驅動。
- 驗收模型的「部分驗收 / 已驗收」**非按鈕觸發**，由驗收單的驗收量決定（尚有未到量=部分、全到齊=已驗收）。
- 再次核准警示：單據曾被「取消核准」（`was_unapproved`）回到已提交時，表單須顯示警示橫幅「此單曾被取消核准，請重新確認明細後再次提交核准。」
- 狀態流轉圖（主線 / 驗收 / 回退 / 終止）與結轉模型流轉（`action_convert_po` / `action_cancel`）以 profile §狀態流轉 + `erp-transaction.md` 為權威。

## 7. RWD / 響應式　📋

- 桌面 / 平板：水平排列，line `flex:1 1 28px`（min 28 / max 56px）自適應寬度。
- 窄螢幕：step 最小寬 64px（`flex:0 0 auto`）；步數多時容器可橫向捲動，**不**壓縮 bubble 尺寸。
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
- **落地權威（步序狀態 / 語意色 / 判定邏輯 / voided-banner / JS helper，勿在此重寫）**：`prototyper/profiles/erp-components/Stepper.md`
- 狀態機權威：`prototyper/profiles/erp-transaction.md §State Machine` / §進銷存擴充狀態機
- 佈局載體：`prototyper/profiles/erp-components/SummaryCard.md`（Layout A/B 插槽、靛色 partial）
- Code：Syncfusion Stepper；helper 見 profile

---

## 待 DS 正式定義（缺口彙整）

> 依 schema §2.2，以下對不上既有 token，**未臆造**；列此供 DS owner 補定後回填。

1. **label 字級 13px**：`athena-tokens.md` 有 `text-sm` 12px / `text-md` 14px，無 13px；待對齊。
2. **bubble 32px / line 28–56px / container gap 16px**：量測值，待對齊 size / space token。
3. **pending·placeholder bubble 底「primary 8% 疊白實色」**：sticky 不需，但與 DataGrid 同屬「疊白實色」缺口家族，待 DS 一併定義。
4. **結轉模型靛色（partial）**：語意色第「靛」色未在 `athena-tokens.md` 立 token（現由 `SummaryCard.md` 落地）；五語意色中唯一無 token 者，待 DS 補定。
