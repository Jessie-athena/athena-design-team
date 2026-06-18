---
name: Button
category: 按鈕與動作
tier: lite
status: ✅ 已採用
authority: ERP CLAUDE.md（Syncfusion Button, @syncfusion/ej2-vue-buttons）
figma-node: —          # 🎨 Figma 補入時填 <FILE_KEY>/<NODE_ID>
version: v0.1
last-synced: —         # 🎨 每次 Figma 同步後更新日期
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺值一律 token-reference；對不上既有 token 時停下回報，勿臆造。

## 1. 概述　📋

頁面操作 CTA。每個畫面語意上的主動作至多一個（用 `primary`）；次要動作用 `secondary` / `ghost`；破壞性動作（刪除、作廢）用 `danger`，**不**用 primary 做 cancel。

## 2. Anatomy　🎨

`[leading icon?]` + `label` + `[trailing icon?]`；icon-only 時 label 走 `sr-only`，並需 `aria-label`。

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# md（預設）尺寸；padding 並列雙密度（Default 緊湊 = 資料/列表場景；Comfortable = 表單/觸控）
height:  36px                                   # 🎨 量測值，待對齊 token
padding:
  default:     "{ds-space-padding-medium} {ds-space-padding-large}"        # 🔗 8px / 12px
  comfortable: "{ds-space-padding-large} {ds-space-padding-extra-large}"   # 🔗 12px / 16px → 16px / 20px
radius:  "{ds-radius-large}"                     # 🔗 8px
font:    "{font-size-sf-text-md} / {font-weight-sf-medium}"   # 🔗 14px / 500
icon:    20px                                    # 🎨
gap:     "{ds-space-padding-small}"              # 🔗 icon↔label 4px
```

## 4. Variants　🎨🔗

```yaml
primary:
  bg: "{color-sf-primary}"            # 🔗 rgb(40 119 238)
  fg: "{color-sf-on-primary}"         # 🔗 white
secondary:
  bg: "{color-sf-secondary-btn-bg}"   # 🔗 white
  fg: "{color-sf-on-surface}"         # 🔗 rgb(15 23 42)
  border: "{ds-borderwidth-small} {color-sf-outline}"   # 🔗 1px rgb(127 137 150)
ghost:
  bg: transparent
  fg: "{color-sf-primary}"
danger:
  bg: "{color-sf-danger}"             # 🔗 rgb(244 73 62)
  fg: "{color-sf-danger-text}"        # 🔗 white
```

## 5. States　🎨🔗

```yaml
# 互動疊層走 Material state layer，不自己調色
hover:    { primary_bg: "{color-sf-primary-darken-opacity-5}" }    # 🔗 rgb(31 87 209)
focus:    { ring: "{ds-borderwidth-medium} {color-sf-primary}", offset: "2px" }  # 🔗 2px
active:   { primary_bg: "{color-sf-primary-darken-opacity-10}" }   # 🔗 rgb(26 76 183)
disabled: { bg: "{color-sf-on-surface-opacity12}", fg: "{color-sf-on-surface-opacity38}" }  # 🔗 Material 12% / 38%
loading:  { spinner: "{color-sf-on-primary}", label: dim }
```

## 9. a11y　📋

- Focus ring：`{ds-borderwidth-medium}` / `{color-sf-primary}` / offset 2px。
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
