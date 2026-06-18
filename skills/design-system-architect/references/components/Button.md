---
name: Button
category: 按鈕與動作
tier: lite
status: ✅ 已採用
authority: ERP CLAUDE.md（Syncfusion Button, @syncfusion/ej2-vue-buttons）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16773-15148   # 🎨 FAI2 Library · Button 元件集
version: v0.2
last-synced: 2026-06-18   # 🎨 get_variable_defs + get_screenshot；紀錄見文末「Figma 補入紀錄」
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺值一律 token-reference；對不上既有 token 時停下回報，勿臆造。
> §3–§5 已用 Figma `get_variable_defs`（節點 16773-15148）校準；**未解 / 衝突項見文末「Figma 補入紀錄」**（focus-ring 無對應 token、按鈕圓角 4px vs design.md 8px、ghost 變體無 Figma 佐證）。

## 1. 概述　📋

頁面操作 CTA。每個畫面語意上的主動作至多一個（用 `primary`）；次要動作用 `secondary` / `ghost`；破壞性動作（刪除、作廢）用 `danger`，**不**用 primary 做 cancel。

## 2. Anatomy　🎨

`[leading icon?]` + `label` + `[trailing icon?]`；icon-only 時 label 走 `sr-only`，並需 `aria-label`。

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# md（預設）尺寸；padding 並列雙密度（Default 緊湊 = 資料/列表場景；Comfortable = 表單/觸控）
height:  36px                                   # 🎨 量測值，待 get_design_context 對齊 token
padding:
  default:     "{ds-space-padding-medium} {ds-space-padding-large}"        # 🎨 量測待補（變數未含 padding；需 get_design_context 單一 variant）
  comfortable: "{ds-space-padding-large} {ds-space-padding-extra-large}"   # 🎨 量測待補
radius:  "{ds-radius-small}"                     # 🎨🔗 Figma Radius/RadiusSm = 4px ⚠️ 與 athena-design.md「按鈕 8px」衝突，待 DS owner 確認
radius_pill: "{ds-radius-10extra-large}"         # 🎨🔗 Figma Radius/Radius10Xl = 1000px（pill 變體，見截圖）
font:    "{font-size-sf-text-md} / {font-weight-sf-medium}"   # 🔗 14px / 500（Figma「Label/14pt/Medium」line-height 1.3、letter-spacing 0.1px）
icon:    20px                                    # 🎨
gap:     "{ds-space-padding-small}"              # 🔗 icon↔label 4px（量測待 get_design_context 確認）
```

## 4. Variants　🎨🔗

> Figma 元件集（節點 16773-15148）含 4 個 variant：primary / secondary / success / danger（變數命名 `Primary_btn/$*` `Sec_btn/$*` `Success_btn/$*` `Danger_btn/$*`）。

```yaml
primary:                              # 🎨 Figma Primary_btn
  bg: "{color-sf-primary}"            # 🔗 ColorSf/primary #2877ee = rgb(40 119 238)
  fg: "{color-sf-on-primary}"         # 🔗 ColorSf/on-primary white
secondary:                            # 🎨 Figma Sec_btn（白底外框）
  bg: "{color-sf-secondary-btn-bg}"   # 🔗 ColorSf/secondary-btn-bg white
  fg: "{color-sf-on-surface}"         # 🔗 ColorSf/on-surface #0f172a
  border: "{ds-borderwidth-small} {color-sf-outline}"   # 🔗 1px ColorSf/outline #7f8996
success:                              # 🎨 Figma Success_btn
  bg: "{color-sf-success}"            # 🔗 ColorSf/success #12b76a
  fg: "{color-sf-success-text}"       # 🔗 ColorSf/success-text white
danger:                               # 🎨 Figma Danger_btn
  bg: "{color-sf-danger}"             # 🔗 ColorSf/danger #f4493e
  fg: "{color-sf-danger-text}"        # 🔗 ColorSf/danger-text white
# ghost: ⚠️ 本 Figma 元件集未見 ghost variant；先前為推論。待確認是否為 secondary 無框態或屬他元件，暫不列為權威
```

## 5. States　🎨🔗

> 互動態走「base 色 + 白色 state-layer 疊層」（Figma 變數值如 `#2877EE,#FFFFFF` 表示 base + overlay 兩段；落地時疊一層白色 alpha，非換色）。**已用 Figma 校準，修正先前 darken 假設**。

```yaml
hover:    { overlay: "{color-sf-primary-btn-hover-8}" }     # 🎨🔗 ColorSf/primary-btn-hover-8 = white 8%（非 darken）
active:   { overlay: "{color-sf-primary-btn-pressed-12}" }  # 🎨🔗 ColorSf/primary-btn-pressed-12 = white 12%（pressed）
focus:    { ring: "$shadow-focus-ring1" }                   # ⚠️ Figma 用 drop-shadow 效果（黑 spread 3 + 白 spread 1）；
                                                            #    athena-tokens.md 無 shadow token → 無法 token-reference，待 DS owner 補（見文末紀錄）
disabled: { fg: "{color-sf-on-surface-opacity38}", border: "{color-sf-outline}" }  # 🎨🔗 text 38% / 邊框 outline；bg 另以 opacity 疊層處理
loading:  { spinner: "{color-sf-on-primary}", label: dim }   # 📋 Figma 未含 loading 態，沿用慣例
```

## 9. a11y　📋

- Focus ring：Figma 為 `$shadow-focus-ring1` 雙層 drop-shadow（黑 spread 3 + 白 spread 1）；**athena-tokens.md 尚無對應 shadow token**，待 DS owner 補定義（見文末紀錄）。
- 鍵盤：Enter·Space 觸發。
- icon-only button **必填** `aria-label`。
- Mobile min touch target 44×44。

## 11. Content　📋

動詞 CTA（「新增」「提交」「核准」「作廢」），不用名詞堆疊；破壞性動作文案具體（「作廢」而非泛泛「確定」）。一般 microcopy 規範詳 `ux-writer`。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| variant | primary / secondary / ghost / danger | primary | — |
| size | sm / md / lg | md | — |
| icon | ReactNode / slot | — | 可選 icon |
| iconPosition | leading / trailing | leading | — |
| loading | boolean | false | 載入中（禁重複點、保留尺寸） |
| disabled | boolean | false | 停用 |
| fullWidth | boolean | false | 撐滿 |

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §Status — Danger / §Space / §Radius / §Typography
- 語意對照：`../athena-design.md` §主色與動作 / §狀態色
- Prototype 落地：`prototyper`（按鈕原子，散見各 template 的 `.btn`）
- Code：`@syncfusion/ej2-vue-buttons`

---

## Figma 補入紀錄（2026-06-18）

> 本元件作為「Figma 補入 SOP」首個演練對象。來源：`get_variable_defs` + `get_screenshot`（節點 `16773-15148`，FAI2 Library）。`get_metadata` 輸出過大（84k），未取結構樹；padding/height 量測待後續 `get_design_context` 於單一 variant 補。

**✅ 已確認對映（Figma 變數 → `athena-tokens.md`）**：`ColorSf/primary` `on-primary` `secondary-btn-bg` `on-surface` `outline` `danger` `danger-text` `success` `success-text` `on-surface-opacity38/12`、`FontSizeSf/TextMd` `FontWeightSf/Medium` `FontFamilySf/Fontfamily`、`primary-btn-hover-8` `primary-btn-pressed-12`。色彩 / 字體層對映無缺口。

**🔧 已據 Figma 修正先前假設**：
1. hover/pressed 疊層 = **白色 8% / 12%**（`primary-btn-hover-8` / `-pressed-12`），非 darken-opacity。
2. variant 集 = primary / secondary / **success** / danger（補上 success；移除無佐證的 ghost）。

**⚠️ 待 DS owner 裁示（schema「對不上停下回報」觸發）**：
1. **focus-ring 無對應 token**：Figma 用 `$shadow-focus-ring1`（drop-shadow 效果），但 `athena-tokens.md` 無 shadow / elevation token 章節。→ 建議在 tokens 補 shadow 群組（含 focus-ring），否則 focus 態無法 token-reference。
2. **按鈕圓角衝突**：Figma 矩形鈕 = `Radius/RadiusSm` **4px**；`athena-design.md` 散文寫「按鈕 8px」。另有 pill 變體 = `Radius/Radius10Xl` 1000px。→ 需確認真值並統一兩處。
3. **disabled bg 表示法**：Figma 以 two-stop（如 `#2877EE,#FFFFFF`）表示 base + 疊層，非單一色；落地需以 state-layer 疊加實作，padding/height 待 `get_design_context` 補量測。
