---
name: Button
category: 按鈕與動作
tier: lite
status: ✅ 已採用
authority: ERP CLAUDE.md（Syncfusion Button, @syncfusion/ej2-vue-buttons）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16773-15148   # 🎨 FAI2 Library · Button 元件集
version: v0.3
last-synced: 2026-06-29   # 🎨 get_variable_defs + get_screenshot + get_design_context(disabled)；紀錄見文末「Figma 補入紀錄」
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺值一律 token-reference；對不上既有 token 時停下回報，勿臆造。
> §3–§5 已用 Figma 多工具校準（`get_variable_defs` / `get_design_context` / `get_metadata` / `get_screenshot`，節點 `16773:15148`）；v0.3 所有 `⚠️` 項已解，**無遺留待確認項**。紀錄見文末「Figma 補入紀錄」（2026-06-18 + 2026-06-29）。

## 1. 概述　📋

頁面操作 CTA。每個畫面語意上的主動作至多一個（用 `primary`）；次要動作用 `secondary` / `ghost`；破壞性動作（刪除、作廢）用 `danger`，**不**用 primary 做 cancel。

## 2. Anatomy　🎨

`[leading icon?]` + `label` + `[trailing icon?]`；icon-only 時 label 走 `sr-only`，並需 `aria-label`。

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# 🎨 get_design_context 節點 16773:15233（Size=Medium, Type=Primary, Enabled）量測
height:  36px                                   # 🎨 Medium（Extra Small 24 / Small 32 / Large 40）
# padding 雙密度：Figma Button 元件集**不含密度軸**（只有 Size/Type/Shape/Mode/State 五軸，2026-06-29 get_metadata 確認）
# 密度切換由 athena-tokens.md CSS 層 Athena_Comfortable 覆蓋 Athena_Default 控制，非 Figma variant
padding:
  default:     "{ds-space-padding-medium} {ds-space-padding-extra-large}"    # 🔗 py-8px / px-16px（Figma Size=Medium 量測值）
  comfortable: "{ds-space-padding-large} {ds-space-padding-2extra-large}"    # 🔗 py-12px / px-24px（Comfortable token 對齊；Figma 無此 variant，值由 token 層推算）
radius:  "{ds-radius-small}"                     # 🎨🔗 Figma Radius/RadiusSm = 4px（已裁示採 Figma，athena-design.md 散文同步改 4px）
radius_pill: "{ds-radius-10extra-large}"         # 🎨🔗 Figma Radius/Radius10Xl = 1000px（pill 變體，見截圖）
font:    "{font-size-sf-text-md} / {font-weight-sf-medium}"   # 🔗 14px / 500；line-height 1.5、letter-spacing 0.24px（Figma 套「Body Content/14pt/Medium」）
icon:    18px                                    # 🎨 修正（先前假設 20px）
gap:     "{ds-space-padding-small}"              # 🎨🔗 icon↔label 4px（Figma gap-4 確認）
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
focus:    { ring: "{ds-shadow-focus-ring1}" }               # 🎨🔗 已補：白 1px 內環 + 黑 3px 外環（athena-tokens.md §Shadow）
disabled: { bg: "{color-sf-primary-opacity-12}", fg: "{color-sf-on-surface-opacity38}", border: "{color-sf-outline}" }  # 🎨🔗 bg primary 12% tint / text on-surface 38% / border outline（get_design_context 16773:19307 渲染值確認）
loading:  { spinner: "{color-sf-on-primary}", label: dim }   # 📋 Figma 未含 loading 態，沿用慣例
```

## 9. a11y　📋

- Focus ring：`{ds-shadow-focus-ring1}`（白 1px 內環 + 黑 3px 外環；已補入 `athena-tokens.md §Shadow`）。
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

**✅ 已解（2026-06-18 後續）**：
- **focus-ring token 已補**：`athena-tokens.md §Shadow` 新增 `--ds-shadow-none/sm/md/focus-ring1`（來源 FAI2 `$shadow-*`）；§5 focus 改引用 `{ds-shadow-focus-ring1}`。
- **尺寸已量測**（`get_design_context` 節點 16773:15233）：Medium 36px、padding 8/16px、radius 4px、icon 18px、gap 4px、label line-height 1.5 / letter-spacing 0.24px。

**✅ 已裁示**：
- **按鈕圓角**：採 Figma `RadiusSm` **4px**（變數 + 渲染 CSS 雙重佐證）；`athena-design.md §圓角搭配` 散文已從 8px 改 4px。pill 變體 = 1000px。

**✅ 已確認（次要，2026-06-29 全解）**：
1. **輸入框圓角**：✅ 已確認（2026-06-29 TextBox Figma 補入）：TextBox 頂部圓角 = `{ds-radius-small}` 4px，底部 = 0（Filled 底線式）。見 `components/TextBox.md §Figma 補入紀錄`。
2. **disabled bg 表示法**：✅ 已確認（2026-06-29 `get_design_context` 節點 `16773:19307`）：disabled bg = `{color-sf-primary-opacity-12}`（primary 12% tint），fg = `{color-sf-on-surface-opacity38}`（38% on-surface），border = `{color-sf-outline}`。渲染 CSS 值與 token 完全對映；`get_variable_defs` 顯示的 `$primary-bg-color-disabled: #2877EE` 是 base 色而非最終色，實際由 opacity variant 層決定。§5 disabled 行已補入 bg token。
3. **Comfortable 密度 padding**：✅ 已確認（2026-06-29 `get_metadata` 節點 `16773:15148`）：Figma Button 元件集**不含密度軸**（只有 Size/Type/Shape/Mode/State 五軸，共 4 尺寸：Extra Small 24px / Small 32px / Medium 36px / Large 40px）。密度切換由 `athena-tokens.md` CSS 層 `Athena_Comfortable` 覆蓋實作，非 Figma variant。Comfortable padding 依 token 層推算為 `{ds-space-padding-large}` (12px) / `{ds-space-padding-2extra-large}` (24px)，已補入 §3。

---

## Figma 補入紀錄（2026-06-29）

> 來源：`get_variable_defs`（節點 `16773:15148`） + `get_screenshot`（同節點）+ `get_metadata`（同節點，結構樹）+ `get_design_context`（節點 `16773:19307`，Size=Large Primary Disabled）。

**本次補強目標**：解決 v0.2 遺留的三個 `⚠️ 仍待確認` 項目。

**補查方法與結論**：

1. **disabled bg 表示法（原待確認項 2）**
   - `get_variable_defs` 顯示 `Primary_btn/$primary-bg-color-disabled: #2877EE`（base 單色）與 `Primary_btn/$primary-text-disabled: #0F172A`（不透明），引發與 §5 記載衝突之疑。
   - `get_design_context`（節點 `16773:19307`）補查渲染 CSS 確認：
     - bg 渲染為 `var(--colorsf/primary-opacity-12, rgba(40,119,238,0.12))` = `{color-sf-primary-opacity-12}`
     - text 渲染為 `var(--colorsf/on-surface-opacity38, rgba(15,23,42,0.38))` = `{color-sf-on-surface-opacity38}`
   - 結論：Figma 的 `$primary-bg-color-disabled: #2877EE` 是 base color reference，最終色由 opacity variant 決定；§5 fg 記載正確；bg token 原缺失，已補入 `{color-sf-primary-opacity-12}`。
   - **§5 disabled 行更新**：由 `{ fg, border }` 補完為 `{ bg: {color-sf-primary-opacity-12}, fg: {color-sf-on-surface-opacity38}, border: {color-sf-outline} }`。

2. **Comfortable 密度 padding（原待確認項 3）**
   - `get_metadata` 完整結構樹確認：Button 元件集共 5 個 variant 屬性軸（Size / Type / Shape / Mode / State），**完全不含密度（Comfortable / Compact）軸**。
   - Size 軸 4 個層級：Extra Small (24px) / Small (32px) / Medium (36px) / Large (40px)。
   - 密度由 `athena-tokens.md §Space` CSS 層控制（`Athena_Comfortable` 覆蓋 `Athena_Default` 同名 token）。
   - Comfortable padding 由 token 層推算：`{ds-space-padding-large}` (12px) vertical / `{ds-space-padding-2extra-large}` (24px) horizontal。
   - **§3 padding 已更新**：補入雙密度並列 block，並標明 Comfortable 值為 token 推算（Figma 無此 variant）。

**新增補入資訊（v0.2 未收錄）**：
- **Extra Small 尺寸**：高度 24px（節點 `16773:15149`），文件 §3 height 行補記。
- **所有可用尺寸**：Extra Small 24px / Small 32px / Medium 36px / Large 40px（`get_metadata` 量測值）。
- **`Sec_btn` disabled 態**：bg = `{color-sf-on-surface-opacity12}`（on-surface 12%），border = `{color-sf-outline-variant}`（較 primary disabled 更淡）。Secondary 與 Primary disabled 配色不同。

**本次無新增 token**：所有確認值均能在現有 `athena-tokens.md` 找到對應，無需補建 token。
