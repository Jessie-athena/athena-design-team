---
name: SplitButton
category: 按鈕與動作
tier: full
status: ⬜ 待分類（Syncfusion ej2-vue-buttons SplitButton；ERP 尚未大量使用）
authority: ERP CLAUDE.md（Syncfusion SplitButton, @syncfusion/ej2-vue-buttons）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16783-674766
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs + get_metadata + get_screenshot + get_design_context（節點 16783:674784，Size=Medium Primary Filled）
---

> 依 `../component-doc-schema.md`（Full 層）產出。視覺值一律 token-reference；對不上既有 token 時停下回報，勿臆造。
> §3–§5 已用 Figma 多工具校準（`get_variable_defs` / `get_metadata` / `get_screenshot` / `get_design_context`，節點 `16783:674766` + 單一 variant `16783:674784`）；紀錄見文末「Figma 補入紀錄」。

---

## 1. 概述　📋

一個視覺整合的複合按鈕，左側為**主動作區**（直接觸發），右側為**下拉觸發區**（展開選單，列出次要動作）。

**何時使用**：
- 有一個明確的主動作，同時存在兩個以上的次要動作（如「核准」為主，「核准並通知」「核准並產傳票」為次）
- 主動作在 90% 以上情境下使用，次要動作是補充路徑
- Form footer 最主要的前進動作 + 衍生操作（進銷存 / 財務單據核准類場景）

**何時不用**：
- 次要動作只有一個 → 改用兩個獨立 Button（SplitButton 不划算）
- 無明確主動作，全部動作地位相等 → 改用 DropDownButton
- 動作語意為破壞性（作廢、刪除）且需雙重確認 → 單獨 danger Button + confirm modal，不進 SplitButton

---

## 2. Anatomy　🎨

```
[ primary-action-btn ] | [ dropdown-trigger-btn ]
                              ↓（展開時）
              ┌───────────────────────┐
              │ [search input]        │  ← 可選搜尋列
              │ ── Header ────────    │  ← 可選群組標頭
              │   menu-item           │
              │   menu-item (hover)   │
              │ ── Header ────────    │
              │   menu-item           │
              │ ─────────────────     │
              │ ＋ Button             │  ← 可選底部快速新增
              └───────────────────────┘
```

子元素：
| 部位 | 說明 |
|---|---|
| `primary-action-btn`（Atom / Split First） | 左段，承載主動作 label（＋可選 leading icon）；點擊直接觸發動作 |
| `divider` | 兩段之間的分隔線；以 `primary-action-btn` 的 `border-right` 實作，顏色為主色加深態 |
| `dropdown-trigger-btn`（Atom / Split Last） | 右段，僅顯示 caret-down icon，寬度固定；點擊展開選單 |
| `dropdown-menu`（Context menu） | 展開覆層；可含搜尋列、群組標頭、menu-item、底部快速新增 |
| `menu-item` | 選單條目；有 default / hover 兩態 |
| `menu-header` | 分組標頭（label-only，不可點），前置分隔線 |
| `menu-add-btn` | 選單底部快速新增按鈕（ghost 樣式，前置分隔線） |

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── 整體尺寸（依 Size variant，get_metadata 量測）──────────────────
sizes:
  extra-small: 24px    # 🎨 width 67px total（Medium 基準縮比）
  small:       32px    # 🎨 width 77px total
  medium:      36px    # 🎨 width 89px total（主要基準）
  large:       40px    # 🎨 width 90px total

# ── primary-action-btn（左段）──────────────────────────────────────
primary_action:
  height:         medium: 36px              # 🎨 同 Button md
  padding:
    default:      "{ds-space-padding-medium} {ds-space-padding-large}"
    # 🔗 py-8px / px-12px（Figma Medium 量測；Button 是 px-16px，左段收窄 4px 讓整體視覺更緊湊）
    comfortable:  "{ds-space-padding-large} {ds-space-padding-extra-large}"
    # 🔗 py-12px / px-16px（token 層推算；Figma 無密度 variant）
  radius:
    # 左段：左兩角圓、右兩角直
    top-left:     "{ds-radius-small}"    # 🔗 4px
    bottom-left:  "{ds-radius-small}"    # 🔗 4px
    top-right:    0                      # 🎨 量測值；接 divider
    bottom-right: 0                      # 🎨 量測值
  font:           "{font-size-sf-text-md} / {font-weight-sf-medium}"   # 🔗 14px / 500
  line-height:    1.5
  letter-spacing: 0.24px                 # 🎨 Figma TextMd 量測值

# ── divider（以 primary-action-btn border-right 實作）──────────────
divider:
  width:          "{ds-borderwidth-small}"   # 🔗 1px
  # 顏色隨 Type variant 呈現各自加深態；見 §4 各型 divider-color

# ── dropdown-trigger-btn（右段）────────────────────────────────────
dropdown_trigger:
  height:    medium: 36px              # 🎨 同左段
  width:     medium: 32px              # 🎨 固定寬（Figma 量測 16783:674784 右段 w=32px）
  padding:   "{ds-space-padding-extra-large} {ds-space-padding-medium}"   # 🔗 px-16px py-8px（Figma 量測）
  radius:
    # 右段：右兩角圓、左兩角直
    top-right:    "{ds-radius-small}"    # 🔗 4px
    bottom-right: "{ds-radius-small}"    # 🔗 4px
    top-left:     0                      # 🎨
    bottom-left:  0                      # 🎨
  icon:      18px                       # 🎨 caret-down icon 18×18（同 Button icon 尺寸）

# ── dropdown-menu（展開覆層）───────────────────────────────────────
dropdown_menu:
  width:     234px                      # 🎨 Figma get_design_context 量測（Context menu w=234px）
  radius:    "{ds-radius-small}"        # 🔗 4px（Figma rounded-4）
  shadow:    "{ds-shadow-sm}"           # 🔗 $shadow-sm
  bg:        "{color-sf-surface}"       # 🔗 white

# ── menu-item ──────────────────────────────────────────────────────
menu_item:
  height:        min: 34px              # 🎨 py-5px = 5+5+24=34px（Figma get_design_context 量測）
  padding:       "{ds-space-padding-small} {ds-space-padding-large}"   # 🔗 py-4px px-12px（近似；Figma py=5px → 待對齊 token）
  font:          "{font-size-sf-text-md} / {font-weight-sf-normal}"    # 🔗 14px / 400
  text-color:    "{color-sf-on-surface}"                               # 🔗 #0f172a

# ── menu-header ────────────────────────────────────────────────────
menu_header:
  min-height:    36px                   # 🎨 Figma get_design_context（min-h-36px）
  padding:       "{ds-space-padding-small} {ds-space-padding-medium}"  # 🔗 py-4px
  font:          "{font-size-sf-text-md} / {font-weight-sf-medium}"    # 🔗 14px / 500（header 用 Medium weight 區分）
  text-color:    "{color-sf-on-surface}"                               # 🔗 #0f172a
  divider-color: "{color-sf-outline-variant}"                          # 🔗 #d7dae0（header 前置分隔線）
```

---

## 4. Variants　🎨🔗

> Figma 元件集（節點 16783-674766）variant 三軸：**Size**（4 級）× **Type**（4 型）× **Mode**（Filled / Outlined，Secondary 獨有）。
> `get_variable_defs` 確認各型獨立變數組（`Primary_btn/$*`、`Sec_btn/$*`、`Success_btn/$*`、`Danger_btn/$*`）。

```yaml
# ── Type 軸 ────────────────────────────────────────────────────────

primary:                                         # 🎨 Figma Primary_btn
  action_bg:     "{color-sf-primary}"            # 🔗 #2877ee
  action_fg:     "{color-sf-on-primary}"         # 🔗 white
  trigger_bg:    "{color-sf-primary}"            # 🔗 同左段（視覺一體）
  trigger_fg:    "{color-sf-on-primary}"         # 🔗 white（caret icon）
  divider_color: "{color-sf-primary-darken-opacity-5}"  # 🔗 #1f57d1（Figma border-right 量測）

secondary_filled:                                # 🎨 Figma Sec_btn / Mode=Filled
  action_bg:     "{color-sf-secondary-btn-bg}"  # 🔗 white
  action_fg:     "{color-sf-on-surface}"         # 🔗 #0f172a
  trigger_bg:    "{color-sf-secondary-btn-bg}"  # 🔗 white
  trigger_fg:    "{color-sf-on-surface}"         # 🔗 #0f172a（caret icon）
  border:        "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗 1px #d7dae0（Figma $border-light）
  divider_color: "{color-sf-outline-variant}"   # 🔗 #d7dae0（整體 border 同色）
  # 🎨 get_variable_defs: Sec_btn/$secondary-border-color = "#FFFFFF,#FFFFFF"（base+overlay，Filled 態 border 不顯）
  # → Filled 模式 border 由 $border-light (#D7DAE0) 控制，非 Sec_btn 變數；值見 Border/$border-light token

secondary_outlined:                              # 🎨 Figma Sec_btn / Mode=Outlined（截圖可見含外框線版）
  action_bg:     "{color-sf-secondary-bg-color}"  # 🔗 white（同 Filled）
  action_fg:     "{color-sf-on-surface}"           # 🔗 #0f172a
  trigger_bg:    "{color-sf-secondary-bg-color}"  # 🔗 white
  trigger_fg:    "{color-sf-on-surface}"           # 🔗 #0f172a
  border:        "{ds-borderwidth-small} {color-sf-outline}"    # 🔗 1px #7f8996（Outlined 用深邊框）
  divider_color: "{color-sf-outline}"             # 🔗 #7f8996

success:                                         # 🎨 Figma Success_btn
  action_bg:     "{color-sf-success}"            # 🔗 #12b76a
  action_fg:     "{color-sf-success-text}"       # 🔗 white
  trigger_bg:    "{color-sf-success}"            # 🔗 同左段
  trigger_fg:    "{color-sf-success-text}"       # 🔗 white
  divider_color: "{color-sf-success-darken-opacity-5}"  # 🔗 #29961f（$border-success-darken，Figma 量測）

danger:                                          # 🎨 Figma Danger_btn
  action_bg:     "{color-sf-danger}"             # 🔗 #f4493e
  action_fg:     "{color-sf-danger-text}"        # 🔗 white
  trigger_bg:    "{color-sf-danger}"             # 🔗 同左段
  trigger_fg:    "{color-sf-danger-text}"        # 🔗 white
  divider_color: "{color-sf-danger-darken-opacity-5}"   # 🔗 #c52118（$border-danger-darken，Figma 量測）

# ── Size 軸（高度；各 Size 左段 padding / 右段寬另隨比例縮）────────
sizes:
  extra-small: { height: 24px }   # 🎨 xs 節點高度（16783:674764）
  small:       { height: 32px }   # 🎨 sm 節點高度（16783:674767）
  medium:      { height: 36px }   # 🎨 md 節點高度（16783:674784）—— 預設
  large:       { height: 40px }   # 🎨 lg 節點高度（16783:674801）
```

---

## 5. States　🎨🔗

> SplitButton 的左右兩段各自有獨立 hover / active / focus 態；整組 disabled 時兩段同步停用。
> 互動態疊層模式沿用 Button.md — base 色 + 白色 state-layer（`primary-btn-hover-8` / `primary-btn-pressed-12`）。

```yaml
# ── primary-action-btn（左段）互動態 ─────────────────────────────
primary_action.hover:    { overlay: "{color-sf-primary-btn-hover-8}" }     # 🔗 white 8%
primary_action.active:   { overlay: "{color-sf-primary-btn-pressed-12}" }  # 🔗 white 12%
primary_action.focus:    { ring: "{ds-shadow-focus-ring1}" }               # 🔗 白 1px 內環 + 黑 3px 外環

# ── dropdown-trigger-btn（右段）互動態 ───────────────────────────
dropdown_trigger.hover:  { overlay: "{color-sf-primary-btn-hover-8}" }     # 🔗 同左段疊層邏輯
dropdown_trigger.active: { overlay: "{color-sf-primary-btn-pressed-12}" }  # 🔗
dropdown_trigger.focus:  { ring: "{ds-shadow-focus-ring1}" }               # 🔗 右段聚焦時獨立 focus ring

# ── menu-item 互動態 ─────────────────────────────────────────────
menu_item.default: { bg: transparent }
menu_item.hover:   { bg: "{color-sf-on-surface-opacity5}" }                # 🔗 rgba(15,23,42,0.05)（Figma get_design_context 量測：Copy 條目 bg）

# ── 整組 disabled ────────────────────────────────────────────────
disabled:
  action_bg:     "{color-sf-primary-opacity-12}"      # 🔗 primary 12% tint（同 Button disabled bg）
  action_fg:     "{color-sf-on-surface-opacity38}"    # 🔗 on-surface 38%
  trigger_bg:    "{color-sf-primary-opacity-12}"      # 🔗
  trigger_fg:    "{color-sf-on-surface-opacity38}"    # 🔗
  divider_color: "{color-sf-outline}"                 # 🔗（同 Button disabled border）
  # 📋 整組 disabled = `disabled` prop 傳入；左右兩段同步無法互動
  # 📋 Figma 元件集未見 disabled variant（get_metadata 僅含 Enabled 態）；值依 Button.md §5 推論

# ── loading 態（左段主動作觸發中）────────────────────────────────
loading:
  spinner: "{color-sf-on-primary}"    # 📋 沿用 Button loading 慣例；Figma 無 loading variant
  right_trigger: disabled             # 📋 觸發中時右段也應停用，防止重複開選單
```

---

## 6. Behavior 行為　📋

### 觸發邏輯

| 部位 | 點擊 | 鍵盤 |
|---|---|---|
| **primary-action-btn（左段）** | 直接觸發主動作（不展開選單） | `Enter` / `Space` = 觸發主動作 |
| **dropdown-trigger-btn（右段）** | 展開 dropdown-menu | `Enter` / `Space` = 展開選單；`↓` = 聚焦第一個 menu-item |
| **menu-item** | 執行對應次要動作，關閉選單 | `Enter` = 執行；`Esc` = 關閉選單 |

### 選單開關

- 右段點擊後展開選單，再點右段或選單外任意處 → 關閉選單
- 選單展開時右段保持 active 態（`aria-expanded="true"`）
- 選單預設向下展開，空間不足時向上展開（由 Syncfusion SplitButton 自動處理）

### 選單項目執行後

- 選單自動關閉
- 主動作 label 不隨選單選項改變（**不**是 select-and-invoke 模式，左段 label 固定為主動作）

### 整組 disabled

- `disabled` prop → 左右兩段均不可互動，選單無法展開

---

## 8. Keyboard　📋

| 鍵 | 焦點在左段 | 焦點在右段 | 焦點在選單 |
|---|---|---|---|
| `Tab` | 移至右段 | 移至選單外下一 focusable | — |
| `Shift+Tab` | 移至前一 focusable | 移至左段 | 移至右段 |
| `Enter` / `Space` | 觸發主動作 | 展開 / 關閉選單 | 執行 menu-item |
| `↓` | — | 開選單並聚焦第一項 | 下一 menu-item |
| `↑` | — | — | 上一 menu-item |
| `Esc` | — | 關閉選單（焦點留右段） | 關閉選單（焦點回右段） |
| `Home` / `End` | — | — | 跳至第一 / 最後 menu-item |

---

## 9. a11y 無障礙　📋

```html
<!-- 整組包裝 -->
<div role="group" aria-label="核准">

  <!-- 左段：主動作 -->
  <button aria-label="核准">
    核准
  </button>

  <!-- 右段：展開觸發 -->
  <button
    aria-label="更多核准選項"
    aria-haspopup="menu"
    aria-expanded="false | true"
  >
    <!-- caret-down icon -->
  </button>

  <!-- 選單（展開時渲染）-->
  <ul role="menu" aria-label="更多核准選項">
    <li role="menuitem">核准並通知</li>
    <li role="menuitem">核准並產傳票</li>
  </ul>

</div>
```

- **左段 `aria-label`**：與可見 label 相同（「核准」），不需額外加工
- **右段 `aria-label`**：建議格式「更多 {主動作} 選項」（如「更多核准選項」），讓 screen reader 能辨別功能
- **右段 `aria-expanded`**：選單展開時為 `true`，關閉時為 `false`
- **選單標頭 `menu-header`**：設 `role="presentation"` 或 `aria-hidden="true"`（不可選），不需為 `menuitem`
- **contrast**：Primary / Success / Danger 型均為淺色文字於飽和色背景，需確認 AA（4.5:1）；如未達標改用各型 `*-strong` token（見 `athena-tokens.md` 相關說明）
- **touch target**：左右兩段各自需達 44×44 最小點擊目標；Medium 36px 高時建議外加 padding 補足

---

## 12. API / Props　📋

> 以下為設計端 Props 對照，Syncfusion SplitButton 實際 API 以 `@syncfusion/ej2-vue-buttons` 文件為準。

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `primaryLabel` | string | — | 左段主動作文字（必填） |
| `type` | primary / secondary / success / danger | primary | 整組視覺型別 |
| `mode` | filled / outlined | filled | Secondary 型可切 Outlined |
| `size` | xs / sm / md / lg | md | — |
| `items` | MenuItem[] | — | 選單條目陣列（必填；空陣列時退化為普通 Button） |
| `disabled` | boolean | false | 整組停用 |
| `loading` | boolean | false | 主動作觸發中（左段 spinner，右段同步停用） |
| `primaryIcon` | slot / string | — | 左段 leading icon（可選） |
| `onPrimaryClick` | function | — | 左段點擊事件 |
| `onItemSelect` | function | — | 選單項目選取事件 |

### MenuItem 結構

| 欄位 | Type | 說明 |
|---|---|---|
| `id` | string | 唯一識別 |
| `label` | string | 顯示文字 |
| `disabled` | boolean | 條目個別停用 |
| `separator` | boolean | 前置分隔線（作為 menu-header 前的視覺分組） |
| `header` | boolean | 此項為群組標頭（不可點） |

---

## 13. 關聯 Links　🔗

- **上游 Tokens**：`../athena-tokens.md`
  - §Primary（`color-sf-primary` / `on-primary` / `primary-darken-opacity-5` / `primary-btn-hover-8` / `primary-btn-pressed-12` / `primary-opacity-12`）
  - §Status（`color-sf-success` / `color-sf-danger` 及各加深 / opacity 變體）
  - §Secondary（`color-sf-secondary-btn-bg` / `color-sf-outline` / `color-sf-outline-variant`）
  - §Shadow（`ds-shadow-sm` / `ds-shadow-focus-ring1`）
  - §Radius（`ds-radius-small`）
  - §Space（`ds-space-padding-medium` / `ds-space-padding-large` / `ds-space-padding-extra-large`）
  - §Typography（`font-size-sf-text-md` / `font-weight-sf-medium` / `font-weight-sf-normal`）
- **同族元件**：
  - `Button.md`（左段視覺規格繼承；hover / disabled 疊層完全相同）
  - DropDownButton（無 primary-action-btn，所有點擊均展開選單；SplitButton 分左右兩用）
- **Prototype 落地（值權威）**：`prototyper/assets/app.css`（canonical CSS；無對應 SplitButton class 時以 Button + dropdown-menu pattern 組合）
- **Code**：`@syncfusion/ej2-vue-buttons`（`EjsSplitbutton` / `SplitButtonPlugin`）

---

## Figma 補入紀錄（2026-06-29）

> 來源：`get_variable_defs`（節點 `16783:674766`）+ `get_metadata`（同節點，variant 結構樹）+ `get_screenshot`（同節點，全覽圖）+ `get_design_context`（節點 `16783:674784`，Size=Medium Primary Filled）。

### variant 結構（get_metadata 確認）

三個 variant 軸：
- **Size**：Extra Small (24px) / Small (32px) / Medium (36px) / Large (40px)
- **Type**：Primary / Secondary / Success / Danger
- **Mode**：Filled（所有 Type）/ Outlined（Secondary 專屬）

共 20 個 variant（4 Size × 4 Type，Secondary 多 4 個 Outlined = 20 個，截圖可驗證）。

### 已確認對映（Figma 變數 → athena-tokens.md）

| Figma 變數 | 對映 token |
|---|---|
| `ColorSf/primary` → `#2877EE` | `{color-sf-primary}` |
| `ColorSf/on-primary` → `#FFFFFF` | `{color-sf-on-primary}` |
| `ColorSf/primary-darken-opacity-5` → `#1F57D1` | `{color-sf-primary-darken-opacity-5}` |
| `ColorSf/secondary-btn-bg` → `#FFFFFF` | `{color-sf-secondary-btn-bg}` |
| `ColorSf/on-surface` → `#0F172A` | `{color-sf-on-surface}` |
| `ColorSf/outline` → `#7F8996` | `{color-sf-outline}` |
| `ColorSf/outline-variant` → `#D7DAE0` | `{color-sf-outline-variant}` |
| `Border/$border-light` → `#D7DAE0` | `{color-sf-outline-variant}` |
| `ColorSf/success` → `#12B76A` | `{color-sf-success}` |
| `ColorSf/success-text` → `#FFFFFF` | `{color-sf-success-text}` |
| `ColorSf/success-darken-opacity-5` → `#29961F` | `{color-sf-success-darken-opacity-5}` |
| `ColorSf/danger` → `#F4493E` | `{color-sf-danger}` |
| `ColorSf/danger-text` → `#FFFFFF` | `{color-sf-danger-text}` |
| `ColorSf/danger-darken-opacity-5` → `#C52118` | `{color-sf-danger-darken-opacity-5}` |
| `$shadow-sm` | `{ds-shadow-sm}` |
| `Radius/RadiusSm` → `4px` | `{ds-radius-small}` |
| `Radius/RadiusNone` → `0px` | 量測值（左段 right 角、右段 left 角均為 0）|
| `FontSizeSf/TextMd` → `14px` | `{font-size-sf-text-md}` |
| `FontWeightSf/Medium` → `500` | `{font-weight-sf-medium}` |
| `FontFamilySf/Fontfamily` | `{font-family-sf-fontfamily}` |
| `ColorSf/on-surface-opacity5` → `rgba(15,23,42,0.05)` | `{color-sf-on-surface-opacity5}` |

### 關鍵量測（get_design_context，節點 16783:674784 Medium Primary Filled）

- **整體高度**：36px（Medium）
- **左段（primary-action-btn）**：`px-12px py-8px`；左角 4px，右角 0；border-right 1px `{color-sf-primary-darken-opacity-5}`
- **右段（dropdown-trigger-btn）**：`w-32px`；`px-16px py-8px`；右角 4px，左角 0
- **caret icon**：18×18px
- **dropdown-menu 寬**：234px；`rounded-4px`；shadow = `$shadow-sm`
- **menu-item 高**：min 34px（`py-5px`，待對齊 token 時使用 `{ds-space-padding-small}` 近似）
- **menu-item hover bg**：`rgba(15,23,42,0.05)` = `{color-sf-on-surface-opacity5}`（Figma「Copy」條目確認）
- **menu-header 高**：min 36px，font-weight Medium（區分於 menu-item 的 Normal）

### Token gap（對不上既有 token，需後續確認）

1. **`Radius/RadiusNone` (0px)** — 有對應 token `{ds-borderwidth-none}` 但非 radius token，`{ds-radius-extra-small}` = 2px 不符；此處取字面值 `0`，標 🎨；後續可在 `athena-tokens.md §Radius` 補 `--ds-radius-none: 0px` 若有系統性需求。
2. **menu-item padding py-5px** — 最近 token `{ds-space-padding-extra-small}` = 2px 或 4px（Comfortable），均不符 5px；標 🎨 待對齊。落地以 `prototyper/assets/app.css` 為值權威。
3. **menu 寬 234px** — Figma 示意寬，實際應由選單內容自動撐寬；標 🎨 參考值，不固定 hard-code。
4. **disabled 態 Figma 未含** — `get_metadata` 元件集無 disabled variant（只有 Enabled 態）；disabled token 依 Button.md §5 推論補入，標 📋。
