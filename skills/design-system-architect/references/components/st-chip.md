---
name: st-chip（狀態徽章）
category: 回饋與狀態
tier: full           # 視覺簡單，但承載單據狀態生命週期、多態、作業檔/設定檔兩套 → 走 Full §4
status: ✅ 已採用（客製 DsStatusBadge；表格內為 .st-chip class）
authority: 契約＝本檔；色相 / 尺寸值落地＝prototyper/assets/app.css（值權威）；class 套用導引＝prototyper DataGrid.md §狀態 Chip / Stepper.md §voided-banner（各態色相為語意推導，DS 尚未逐態正式定義）
figma-node: —        # 🎨 Figma 補入時填 <FILE_KEY>/<NODE_ID>
version: v0.1
last-synced: —
---

> 依 `../component-doc-schema.md` 產出。三權威分工：**色相 / 尺寸值權威＝`prototyper/assets/app.css`（canonical CSS，複製不重寫）**；**class 套用導引＝prototyper profile**（見 §13）；**本檔＝契約**（語意整理 + token-reference），重疊處引用、不重寫決策（schema §8）。DS 正式逐態定義後以 DS 為準。

## 1. 概述　📋

單據 / 資料列的**狀態徽章**，唯讀顯示生命週期狀態。表格內用緊湊版（28px）；獨立使用（如 Form voided-banner）用 36px 版。空值欄位顯示 `—`，不放 chip。

## 2. Anatomy　🎨

單一 `label`（DS Chips 元件的 Avatar / Left Icon / Right Icon 槽位皆 `display:none`，僅留 Label）。

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# 兩種尺寸：表格緊湊版（DataGrid 內）與獨立版（Stepper voided-banner）
table:                                      # 緊湊版
  height:  28px                             # 🎨
  radius:  "{ds-radius-10extra-large}"      # 🔗 全圓 1000px
  padding: "0 8px 0 6px"                    # 🎨 量測值，待對齊 token
  min-width: 49px                           # 🎨
  font:    "{font-size-sf-text-sm} / {font-weight-sf-medium}"  # 🔗 12px / 500
standalone:                                 # 獨立版（DS Chips danger 本體基準）
  height:  36px                             # 🎨
  radius:  "{ds-radius-9extra-large}"       # 🔗 48px
  padding: "{ds-space-padding-medium} {ds-space-padding-extra-large}"  # 🔗 8px / 16px
  font:    "{font-size-sf-text-sm} / {font-weight-sf-medium}"  # 🔗 12px / 500
pattern: "三件套：底=狀態色 12% tint・邊框=狀態色實色・字=狀態色實色"
align:   "表格內靠左（隨欄位 text-align: left），不置中"
```

## 4. Variants（狀態軸）　🎨🔗

> 三件套 = `{ bg, border, text }`。`base:` 為簡記繼承（同三件套）；Figma 各態獨立設計時展開為完整 token。色相對映 `athena-design.md §狀態色` 語意：綠=完成、藍=進行中/驗收終態、紅=作廢/取消、灰=未取號/已結案。

```yaml
# ── 作業檔 canonical 4 值 ──
draft:     { tone: neutral, bg: "{color-sf-surface-variant}",  border: "{color-sf-outline}", text: "{color-sf-on-surface-variant}" }  # 🔗 灰
submitted: { tone: primary, bg: "{color-sf-primary-opacity-12}", border: "{color-sf-primary}", text: "{color-sf-primary}" }            # 🔗 藍
approved:  { tone: success, bg: "{color-sf-success-opacity12}",  border: "{color-sf-success}", text: "{color-sf-success}" }            # 🔗 綠
voided:    { tone: danger,  bg: "{color-sf-danger-opacity12}",   border: "{color-sf-danger}",  text: "{color-sf-danger}" }             # 🔗 紅（終態，不進 Stepper）

# ── 進銷存擴充（驗收 / 結轉模型）──
partial:   { base: submitted }   # 部分驗收 / 部分採購
received:  { base: submitted }   # 已驗收（終態）
settled:   { base: submitted }   # 已結清（終態）
done:      { base: draft }       # 已結案（終態，中性灰）
cancelled: { base: voided }      # 已取消（終態）

# ── 設定檔兩態 ──
active:    { base: approved }    # 啟用（綠）
inactive:  { base: voided }      # 停用（紅）
```

> 各模組依其狀態機（PRD）取用對應態，不自動補 PRD 未列的狀態（R1 PRD 完整性）。標籤詞彙固定：草稿/已提交/已核准/部分驗收/已驗收/已結清/已結案/已作廢/已取消/啟用/停用。

## 5. States　🎨🔗

Chip 本身**唯讀、無互動態**；其「狀態值」即 §4 的 variant。當 chip 位於 DataGrid 列中，列的 hover / selected / zebra 屬 DataGrid 的多層 states（見上游 DataGrid.md），非 chip 自身。

## 6. Behavior　📋

- 唯讀、不可點。
- 狀態推進由 Form Footer 動作按鈕、下游單據回寫或結轉結果驅動（見 `Stepper.md §狀態流轉`）。
- 終止態（voided / cancelled）在 Form Summary 不進 Stepper，整段改 `.voided-banner`（內含本元件 standalone 版徽章）。

## 9. a11y　📋

- 狀態語意不可僅靠顏色傳達——label 文字本身即為狀態名（草稿/已核准…），達成色盲可辨。
- 對比：12% tint 底 + 狀態色實色字，需滿足文字對比 ≥ 4.5:1；飽和原色當文字若不足，改用加深別名（見 `athena-design.md` / DS `*-strong` token 慣例）。
- 作為 status 指示時可加 `role="status"` 或視情境以 `aria-label` 補「狀態：<label>」。

## 11. Content　📋

固定狀態詞彙，不自創同義詞（「已作廢」非「廢除」）；空值用 `—` 不放 chip。一般 microcopy 規範詳 `ux-writer`。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| status | draft/submitted/approved/partial/received/settled/done/voided/cancelled/active/inactive | — | 對映 §4 variant |
| size | table / standalone | table | 28px 緊湊 / 36px 獨立 |

> 客製元件 `DsStatusBadge`（`components/ds/`）為其 production 載體。

## 13. 關聯　🔗

- 語意色：`../athena-design.md` §狀態色（草稿=中性/已提交=info/已核准=success/已作廢=danger）
- Tokens：`../athena-tokens.md` §Primary / §Status（Success/Danger）/ §Radius / §Typography
- **值權威（尺寸 / 色，勿在此重寫）**：`prototyper/assets/app.css`（canonical CSS）
- **用法權威（class 套用導引）**：
  - 表格緊湊版 → `prototyper/profiles/erp-components/DataGrid.md §狀態 Chip`
  - 獨立 36px 版 → `prototyper/profiles/erp-components/Stepper.md §voided-banner`
- Code：客製 `DsStatusBadge`
