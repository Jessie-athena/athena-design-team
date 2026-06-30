---
name: SummaryCard（作業檔摘要卡）
category: 佈局與導覽
tier: full           # 複合容器：兩種 layout、內含 stepper、sticky 行為、跨斷點重排 → Full
status: ✅ 已產出（ERP 客製複合元件；落地以 .summary-card / .summary-card-wrap class）
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（值權威）；layout class / 4 步動態 stepper 行為＝prototyper/profiles/erp-components/SummaryCard.md（用法權威）；Layout A 規格 erp-transaction.md §Summary Bar 結構
figma-node: —        # 🎨 Figma 補入時填 <FILE_KEY>/<NODE_ID>
version: v0.1
last-synced: —
---

> 依 `../component-doc-schema.md`（Full 層）產出。三權威分工：**視覺值（padding / sticky / 金額字級）權威＝`prototyper/assets/app.css`（canonical CSS，複製不重寫）**；**layout class 套用 / 4 步動態 stepper 行為權威＝`prototyper/profiles/erp-components/SummaryCard.md`**（Layout A 另見 `erp-transaction.md §Summary Bar 結構`）；**本檔＝契約**（what/why/token-reference/state/a11y）。內含的 Stepper 一切視覺 / 判定以 `Stepper.md` 為準。

## 1. 概述　📋

作業檔 Form View **頂部的摘要卡**：sticky 置頂，彙總本單據最關鍵資訊（金額指標）並嵌入狀態 stepper，供核准 / 檢視時一眼掌握「這是什麼單、現在到哪一步」。

- 何時用：作業檔（transaction）必載；**設定檔無 Summary Card**（`erp-setup.md` 明定）。
- 何時**不用**：純設定 / 清單頁無單據生命週期者；多步驟精靈的進度條用獨立 Stepper（不包進摘要卡）。
- 三大共用原則：**sticky 置頂、無 shadow（DS 卡片無陰影）、內含狀態 stepper**。

## 2. Anatomy　🎨

```
.summary-card-wrap            ← 外層：padding + 不透明背景 + sticky 置頂
└─ .summary-card              ← flex row（Layout B 左右兩區 / Layout A 上下兩塊）
   ├─ .summary-card__left     ← 單指標金額（Layout B）
   │   ├─ .summary-card__label    ← 指標名稱（如「預估總金額」）
   │   └─ .summary-card__amount   ← 金額：放大 / 加粗 / 主色
   └─ .summary-card__right    ← stepper 或 .voided-banner
       └─ .stepper / .voided-banner   ← 見 Stepper.md
```

Layout A（多指標）為「上區標題 + stepper／下區多指標 + 已產生傳票 chip」上下兩塊結構，完整 anatomy 見 `erp-transaction.md §Summary Bar 結構`，本檔不重列。

## 3. 視覺規格 Tokens　🎨🔗

```yaml
wrap:
  position: "sticky; top: 0"                 # 滾動時固定於 main panel 頂部
  bg:       "{color-sf-surface}"             # 🔗 不透明背景（sticky 須實色，避免下層穿透）
  shadow:   "{ds-shadow-none}"               # 🔗 DS 卡片無陰影
  padding:  "{ds-space-padding-3extra-large} ─"  # 🔗 上下 24px（Default）；左右隨 main panel
card:
  layout:   "flex row; justify-content: space-between"   # 左右兩區分置，不加分隔線
amount:
  font:     "放大 + {font-weight-sf-bold} + {color-sf-primary}"   # 🎨🔗 字級放大待量測（暫對 {font-size-sf-h5} 20px）；金額主色強調
  format:   "幣別符號 + 千分位；唯讀；tabular-nums"
label:
  font:     "{font-size-sf-text-sm} / {font-weight-sf-normal}"   # 🔗 12px；色 {color-sf-on-surface-variant}
```

> `.summary-card__amount` 的確切字級未在 profile 標明（僅「放大 / 加粗」），暫以 `h5` 20px 對映並標 🎨 待 Figma 量測確認。

## 4. Layout / Variants　🎨🔗

兩種 layout 共用 §3 wrap 原則，差異在指標數量與排列：

```yaml
layout_A:        # 多指標（上下兩塊）
  適用:   "財務作業檔（付款 / 請款；金額需拆稅額 / 互抵等多指標）"
  結構:   "上區：標題 + stepper；下區：多指標（總金額 / 幣別 / 建立者）+「已產生傳票」chip"
  stepper: "canonical 3 步（基本型，見 Stepper.md）"
  spec:   "完整規格 → erp-transaction.md §Summary Bar 結構（本檔不重複）"
layout_B:        # 單指標 + stepper（左右兩區）
  適用:   "進銷存作業檔（請購單；單一關鍵金額即足）"
  結構:   "左 .summary-card__left 單一強調金額；右 .summary-card__right stepper"
  stepper: "視狀態機而定；6 值結轉模型用 4 步動態 stepper（見 §5 + Stepper.md）"
  理由:   "階段尚未涉及稅額 / 互抵等複雜計算時，單一彙總金額供核准參考即足；需多指標改 Layout A"
```

> 同模組擇一 layout，不混用。Layout 選擇依「單據階段是否需多指標」而非外觀偏好。

## 5. States　🎨🔗

### 卡片資料狀態

| 狀態 | 呈現 |
|---|---|
| loaded | 正常顯示指標 + stepper |
| loading | 金額 / stepper 區 Skeleton 佔位（非 spinner），保留卡片高度避免跳版 |
| 金額為 0 / 未計算 | 顯示 `—` 或 `0`（依模組語意），不隱藏整卡 |

### 內含 Stepper 的動態終態插槽（結轉模型 6 值）

前段步固定（草稿 / 已提交 / 已核准…），**終態步三選一互斥**插槽（步數動態，最少 3、上不封頂）。bubble / line 的 pending·current·done 視覺沿用 `Stepper.md §樣式規則`；終態步三 modifier：

| 當前 `form.state` | 終態步顯示 | class | 配色 |
|---|---|---|---|
| `partial`（部分採購） | 部分採購 | `stepper__step--partial` | **primary 藍** + 內白環（進行中） |
| `done`（已結案） | 已結案 | `stepper__step--final` | **primary 藍** + 內白環（終態；2026-06-21 起與當前步同色，靠 label 區分，不再用灰；仍非 `--done` 綠） |
| `draft` / `submitted` / `approved` | 待結轉 / — | `stepper__step--placeholder` | 淺灰 placeholder |
| `cancelled`（已取消） | **整條 stepper 隱藏** → `.voided-banner` 內紅色 `st-chip--cancelled`「已取消」pill | — | 見 `Stepper.md §voided-banner` |

狀態 × step 完整對應矩陣、`is-current` 連接線索引規則（線索引 = 其右側 step 序號；藍線永遠連向當前步）以 profile §狀態×step 對應矩陣為權威。

> **再次核准**：`was_cancelled && state==='submitted'` 時 stepper active 落前段步、終態步仍 placeholder，另於卡片下方插入「再次核准警示」`form-banner.is-warning`（詳 `erp-transaction.md`）。
> **設計修正（2026-06-21）**：原「partial 靛 / done 灰」已取消——終態步**一律 primary 藍**（與當前步同視覺），無需新色 / 新 token。

## 6. Behavior　📋

- 卡片本身**唯讀**：指標金額、stepper 皆不可互動點擊；狀態推進由 Form Footer 動作驅動（見 `FormFooter.md`）。
- sticky：隨 Form 內容捲動固定於頂部；卡片背景須不透明，確保捲動內容不穿透。
- 金額即時連動：Layout B 的單指標金額同步自明細合計（DataGrid `tfoot` 的 Σ，見 `DataGrid.md §合計列`）；兩處數字必一致。
- JS helper（`stepState` / `stepClass` / `lineClass` / `step4Class` / `step4Label` / `isCancelled`）以 profile §JS helper 約定為權威。

## 7. RWD / 響應式　📋

- 桌面：Layout B 左右並排（`space-between`）；Layout A 上下兩塊。
- 窄螢幕：Layout B 左右兩區改**上下堆疊**（金額在上、stepper 在下）；stepper 步數多時依 `Stepper.md §RWD` 橫捲或當前步聚焦。
- sticky 在小螢幕仍保留，但需確保不過度佔用垂直空間（必要時收合次要指標）。

## 8. Keyboard　📋

唯讀容器，**不在 Tab 順序內**。內含 stepper 同為 read-only（見 `Stepper.md §Keyboard`）。狀態變更入口為 Form Footer 按鈕，鍵盤可達性歸該處。

## 9. a11y　📋

- 金額指標需有可讀 label（`.summary-card__label` 文字 + 金額），螢幕閱讀器讀「<指標名>：<金額>」。
- sticky 卡片不可遮擋鍵盤焦點目標；focus 進入被遮元素時需自動捲動讓其可見。
- 內含 stepper 的狀態語意不可僅靠顏色（見 `Stepper.md §a11y`）。
- 動畫尊重 `prefers-reduced-motion: reduce`。

## 10. 跨平台 Adaptive　📋

App ≠ 縮小 Web：

- 手機上摘要卡優先保留「**最關鍵金額 + 當前狀態**」，次要指標（建立者、幣別）可收進「更多」展開或下移；避免整塊 Layout A 多指標硬擠。
- sticky 在 App 視窗需與頂部 app bar 協調（不重疊），必要時 stepper 改當前步聚焦樣式（見 `Stepper.md §跨平台 Adaptive`）。

## 11. Content　📋

- 指標 label 用既定詞彙（「預估總金額」「總金額」「幣別」「建立者」），不自創。
- 「已產生傳票」chip（Layout A）文案固定；空 / 未產生時不顯示該 chip。
- stepper 步驟與 voided-banner 文案見 `Stepper.md §Content`。一般 microcopy 詳 `ux-writer`。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| layout | A / B | B | 對映 §4；同模組不混用 |
| metrics | Metric[] | — | Layout A 多指標；Layout B 取首個為強調金額 |
| status | <狀態值> | draft | 驅動內含 stepper（pattern 見 Stepper.md） |
| stepperPattern | basic / acceptance / convert | — | 透傳 Stepper |

> production 為 ERP 客製複合元件；本表為設計指引層，與實作 API 分離。

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface / §Shadow（`--ds-shadow-none`）/ §Space / §Primary / §Typography
- 語意對照：`../athena-design.md` §中性·背景 / §字級（金額用 h5、label 用 text-sm）/ §間距
- 子元件：`Stepper.md`（內含狀態 stepper，終態步一律 primary 藍）、`.st-chip`（voided-banner / 已產生傳票 chip，見 Chips.md）、`DataGrid.md`（合計列 Σ → 單指標金額來源）
- **值權威（padding / sticky / 金額字級，勿在此重寫）**：`prototyper/assets/app.css`（canonical CSS）
- **用法權威（layout class / 4 步動態 stepper 行為）**：`prototyper/profiles/erp-components/SummaryCard.md`
- Layout A 完整規格：`prototyper/profiles/erp-transaction.md §Summary Bar 結構`
- 狀態機權威：`erp-transaction.md §進銷存擴充狀態機`
- Code：ERP 客製複合元件（Form View header）

---

## 待 DS 正式定義（缺口彙整）

> 依 schema §2.2，以下對不上既有 token，**未臆造**；列此供 DS owner 補定後回填。

1. **`.summary-card__amount` 字級**：profile 僅標「放大 / 加粗」，未指定 size token；暫對 `h5` 20px，待 Figma 量測確認。
2. **wrap 左右 padding「隨 main panel」**：非固定值，依版面 context；落地見 profile。

> **已解（2026-06-21）**：原「結轉模型靛色」缺口經設計裁示**取消**——終態步一律 primary 藍，無需新色 / 新 token。
