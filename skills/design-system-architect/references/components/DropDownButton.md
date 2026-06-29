---
name: DropDownButton
category: 按鈕與動作
tier: full
status: ⬜ 待分類（ERP 使用中，尚未正式採用為 Ds 元件）
authority: ERP CLAUDE.md（Syncfusion DropDownButton, @syncfusion/ej2-vue-splitbuttons）
figma-node: JhcpyIEEzSChcEXMRJUiIm/12736-47804
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs + get_screenshot + get_metadata + get_design_context（節點 12736:47854）；紀錄見文末「Figma 補入紀錄」
---

> 依 `../component-doc-schema.md`（Full 層）產出。視覺值一律 token-reference；對不上既有 token 時停下回報，勿臆造。
> §3–§5 已用 Figma `get_variable_defs`（節點 12736-47804）+ `get_design_context`（節點 12736:47854，Size=Small, Type=Primary, Shape=Rounded, Mode=Filled, State=Enabled）+ `get_screenshot`（同節點集）校準；未解 / 衝突項見文末「Figma 補入紀錄」。

---

## 1. 概述　📋

收納多個次要操作於單一按鈕的下拉選單。點擊 trigger 展開 overlay menu，使用者選取後選單收合並觸發對應動作。

**何時使用**：
- 同一操作入口有 3 個以上次要動作（如 List toolbar「更多操作」：匯出、列印、複製為新單…）。
- Form footer 的「更多操作」下拉（作廢、列印、複製為新單）。
- Toolbar 批次動作超過 2 個時，收納優先級低的動作。

**何時不用**：
- 主要動作唯一 → 用 `Button`（不要把主 CTA 藏進下拉）。
- 兩個平行動作 → 並排 `Button`（不必收納）。
- 一組互斥切換 → `ButtonGroup`。
- 既要點擊主動作、又要展開子選單 → `SplitButton`（本元件無獨立主動作區）。
- 從資料集選值 → `DropDownList`（語意不同，屬輸入控制）。

---

## 2. Anatomy　🎨

```
┌────────────────────────────────────────────────────────────┐
│  trigger-btn                                               │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ [left-icon?]  label  [caret-icon ▾]                  │  │
│  └──────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────┘
          ↓ 點擊後展開（portal，z-index > dialog）
┌─────────────────────────────┐
│  overlay-menu               │  ← shadow-md, radius-sm
│  ┌─────────────────────────┐│
│  │ menu-item               ││  ← default / hover / disabled
│  ├─────────────────────────┤│
│  │ menu-item               ││
│  ├─────────────────────────┤│
│  │ ── divider ──           ││  ← 分隔線（可選）
│  ├─────────────────────────┤│
│  │ menu-item (disabled)    ││
│  └─────────────────────────┘│
└─────────────────────────────┘
```

子元素：
- **trigger-btn**：整個按鈕觸發區，含 left-icon（可選）、label、caret-icon（必有）。
- **left-icon**（可選）：leading icon slot，16×16px 容器（`chooseIcon` prop / `iconCss`）。
- **label**：按鈕文字，`content` slot（Syncfusion）或 default slot。
- **caret-icon**：trailing，固定 16×16px 下三角 SVG（`▾`），foreground 色同 label，不可移除。
- **overlay-menu**：portal 層，瀏覽器視窗層級（脫離 stacking context）；帶 `{ds-shadow-md}` 與 `{ds-radius-small}`。
- **menu-item**：選單行，含可選 item icon + item text；支援 `disabled` 與 `separator` 行。
- **divider**：選單內分隔線（`separator: true`），視覺為細橫線。

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ——— Trigger Button（Size=Small 量測，節點 12736:47854）———
# 雙密度：Figma 無密度軸；密度由 athena-tokens.md CSS 層 Athena_Comfortable 覆蓋控制
height:
  small:  32px    # 🎨 量測值
  medium: 36px    # 🎨 量測值（同 Button Medium）
  large:  40px    # 🎨 量測值

padding:
  small:
    default:     "7px {ds-space-padding-large}"          # 🔗 py-7px / px-12px（Figma 量測）
    comfortable: "{ds-space-padding-medium} {ds-space-padding-extra-large}"  # 🔗 py-8px / px-16px（token 推算）
  medium:
    default:     "{ds-space-padding-medium} {ds-space-padding-extra-large}"  # 🔗 py-8px / px-16px
    comfortable: "{ds-space-padding-large} {ds-space-padding-2extra-large}"  # 🔗 py-12px / px-24px
  # 📋 Large padding 待 get_design_context 補量

radius:
  rounded:      "{ds-radius-small}"           # 🔗 Figma Radius/RadiusSm = 4px
  full_rounded: "{ds-radius-10extra-large}"   # 🔗 Figma Radius/Radius10Xl = 1000px（pill 變體）

gap:            "{ds-space-padding-small}"    # 🔗 icon↔label / label↔caret 間距 4px（Figma gap-4）

font:           "{font-size-sf-text-md} / {font-weight-sf-medium}"   # 🔗 14px / 500
line_height:    1.3                           # 🎨 Figma Label/14pt/Medium lineHeight 1.3（待對齊 token）
letter_spacing: "0.1px"                       # 🎨 Figma letterSpacing 0.1px（待對齊 token）

icon_left_size: 16px                          # 🎨 Left Icon 容器 16×16（小於 Button.md 的 18px）
caret_size:     16px                          # 🎨 Right Icon（caret）容器 16×16

# ——— Overlay Menu ———
menu_shadow:    "{ds-shadow-md}"              # 🔗 dropdown/menu 用途（athena-tokens.md §Shadow）
menu_radius:    "{ds-radius-small}"           # 🔗 4px（與 trigger-btn 同等）
menu_bg:        "{color-sf-surface}"          # 🔗 white（BG/$content-bg-color）
menu_z_index:   "高於 dialog（見 §6 層疊脈絡）"  # 📋
```

> **py-7px 無對應 token 的注意**：Small 的 padding-y 為 7px，最接近 `{ds-space-padding-medium}` 8px 差 1px，無精確對應 token，已標 🎨；值的唯一來源是 `prototyper/assets/app.css`（canonical CSS）。

---

## 4. Variants　🎨🔗

> Figma 元件集（節點 12736-47804）共 **5 個 variant 軸**：
> - **Size**：Small / Medium / Large
> - **Type**（色彩）：Primary / Secondary / Success / Danger
> - **Shape**：Rounded / Full Rounded（pill）
> - **Mode**：Filled / Flat / Outlined
> - **State**：Enabled / Hover / Active / Focus / Disabled（另見 §5）

```yaml
# ——— Type 軸（色彩）——— 承接 Button.md 同族 state-layer 邏輯
primary:
  bg:     "{color-sf-primary}"             # 🔗 #2877ee
  fg:     "{color-sf-on-primary}"          # 🔗 white
  border: "{color-sf-primary}"             # 🔗 同 bg

secondary:
  bg:     "{color-sf-secondary-btn-bg}"    # 🔗 white
  fg:     "{color-sf-on-surface}"          # 🔗 #0f172a
  border: "{color-sf-outline}"             # 🔗 #7f8996

success:
  bg:     "{color-sf-success}"             # 🔗 #12b76a
  fg:     "{color-sf-success-text}"        # 🔗 white
  border: "{color-sf-success}"             # 🔗 同 bg

danger:
  bg:     "{color-sf-danger}"              # 🔗 #f4493e
  fg:     "{color-sf-danger-text}"         # 🔗 white
  border: "{color-sf-danger}"              # 🔗 同 bg

# ——— Mode 軸（填色模式）——— Syncfusion cssClass 對應
filled:   { 同 Type 軸各 bg/fg/border }    # 🎨 預設；cssClass e-primary / e-success 等
flat:     { bg: transparent, fg: 同 Type fg, border: transparent }  # 📋 cssClass e-flat
outlined: { bg: transparent, fg: 同 Type bg, border: 同 Type bg }   # 📋 cssClass e-outline

# ——— Shape 軸 ———
rounded:      { radius: "{ds-radius-small}" }           # 🔗 4px
full_rounded: { radius: "{ds-radius-10extra-large}" }   # 🔗 1000px（pill）

# ——— Left Icon（可選）———
with_icon:    { icon_container: 16×16px, gap: "{ds-space-padding-small}" }  # 🎨🔗
without_icon: { icon: none }

# ——— Size 軸（高度）———
small:  { height: 32px }    # 🎨
medium: { height: 36px }    # 🎨
large:  { height: 40px }    # 🎨
```

---

## 5. States　🎨🔗

> Trigger button 的互動態走「base 色 + state-layer 疊層」，與 Button.md §5 完全同族邏輯。
> Menu-item 另有自己的 hover/disabled 態。

```yaml
# ——— Trigger Button States（以 Primary / Filled 為例）———
enabled:
  bg:      "{color-sf-primary}"              # 🔗
  fg:      "{color-sf-on-primary}"           # 🔗

hover:
  overlay: "{color-sf-primary-btn-hover-8}"  # 🔗 white 8%（疊於 bg 上）

active:
  overlay: "{color-sf-primary-btn-pressed-12}"  # 🔗 white 12%（pressed）

focus:
  ring:    "{ds-shadow-focus-ring1}"         # 🔗 白 1px 內環 + 黑 3px 外環

disabled:
  bg:      "{color-sf-primary-opacity-12}"   # 🔗 primary 12% tint
  fg:      "{color-sf-on-surface-opacity38}" # 🔗 on-surface 38% opacity
  border:  "{color-sf-outline}"              # 🔗
  cursor:  not-allowed                       # 📋

# ——— Overlay Menu States ———
menu_default:
  bg:      "{color-sf-surface}"              # 🔗 white
  shadow:  "{ds-shadow-md}"                  # 🔗

# ——— Menu-item States ———
item_default:
  bg:      transparent
  fg:      "{color-sf-on-surface}"           # 🔗 #0f172a

item_hover:
  bg:      "{color-sf-on-surface-opacity8}"  # 🔗 on-surface 8%（Sec_btn hover 同族）
  fg:      "{color-sf-on-surface}"           # 🔗

item_disabled:
  bg:      transparent
  fg:      "{color-sf-on-surface-opacity38}" # 🔗 38% opacity
  cursor:  not-allowed                       # 📋

# ——— Divider ———
divider:
  color:   "{color-sf-outline-variant}"      # 🔗 #d7dae0
  height:  "{ds-borderwidth-small}"          # 🔗 1px
```

---

## 6. Behavior 行為　📋

| 情境 | 行為 |
|------|------|
| 點擊 trigger-btn | 展開 overlay-menu；再次點擊 = 收合 |
| 點擊 menu-item（非 disabled） | 觸發 `select` 事件（`args.item`）並收合選單 |
| 點擊 menu-item（disabled） | 無反應，視覺淡化 |
| 點擊 outside（`click outside`） | 收合選單（Syncfusion 預設） |
| `Esc` | 收合選單，focus 回 trigger-btn |
| 選單開啟時捲動頁面 | 選單跟隨 trigger 定位（Syncfusion popup 自動 reposition） |
| **層疊脈絡** | overlay-menu 為 portal，z-index 高於一般內容；若頁面有 Dialog，overlay 仍需高於 Dialog；實作時確認 z-index 層級 |
| `popupPosition` | 預設 bottom-left；可改 bottom-center / top-left 等 |
| **選單寬度** | 預設不小於 trigger-btn 寬；若項目文字較長則擴展 |

---

## 8. Keyboard　📋

| 按鍵 | 動作 |
|------|------|
| `Tab` | focus 進入 trigger-btn |
| `Enter` / `Space` | 開啟 / 收合選單 |
| `↓` Arrow Down | 選單已開：focus 下一個 enabled menu-item |
| `↑` Arrow Up | 選單已開：focus 上一個 enabled menu-item |
| `Home` | 跳至第一個 enabled menu-item |
| `End` | 跳至最後一個 enabled menu-item |
| `Enter` 於 menu-item | 觸發選取並收合選單 |
| `Esc` | 收合選單，focus 回 trigger-btn |
| `Tab`（選單開啟中） | 收合選單，focus 移至下一個頁面元素 |

---

## 9. a11y 無障礙　📋

- **Trigger button**：
  - `aria-haspopup="menu"` — 告知螢幕閱讀器此按鈕帶下拉選單。
  - `aria-expanded="true|false"` — 隨選單開闔動態切換。
  - `aria-label` 或可見文字（label）必填；icon-only 模式需明確 `aria-label`（如「更多操作」）。
- **Overlay menu**：
  - 容器加 `role="menu"`。
  - 每個可選項加 `role="menuitem"`。
  - disabled 項加 `aria-disabled="true"`（不用 `disabled` 屬性，保留 Tab 停留能力讓 SR 可讀）。
  - Divider 加 `role="separator"`。
- **Focus ring**：`{ds-shadow-focus-ring1}`（白 1px 內環 + 黑 3px 外環），鍵盤導航時必須可見。
- **對比**：
  - Primary bg `{color-sf-primary}` 搭配 `{color-sf-on-primary}` white：確認符合 WCAG AA（4.5:1）。
  - disabled fg `{color-sf-on-surface-opacity38}`（38% = ~rgb(99 108 122)）在白底上約 2.7:1，不達 AA — 符合 disabled 態豁免規則（WCAG 1.4.3 exception），無需修正。
  - 若 Primary 色不達 AA，改用 `athena-tokens.md` 中的 `*-strong` 別名。
- **觸碰目標**：min 44×44px；Size=Small（32px）需確認觸碰區域補足（padding 或 touch-target wrapper）。

---

## 12. API / Props　📋

> 以下為 Syncfusion `ejs-dropdownbutton` 的關鍵 props；完整文件見 [Syncfusion DropDownButton API](https://ej2.syncfusion.com/vue/documentation/api/drop-down-button/)。
> Import：`DropDownButtonComponent as EjsDropdownbutton` from `@syncfusion/ej2-vue-splitbuttons`（**非** `ej2-vue-buttons`）。

| Prop | Type | Default | 說明 |
|------|------|---------|------|
| `items` | `ItemModel[]` | — | 選單項目陣列；每項含 `text`、`iconCss`（可選）、`disabled`（可選）、`separator`（可選） |
| `iconCss` | string | — | Trigger button 的 leading icon CSS class（如 `e-icons e-edit`） |
| `iconPosition` | `Left \| Right \| Top \| Bottom` | `Left` | Leading icon 位置（通常 Left） |
| `content` | string | — | Trigger button 文字；也可用 default slot |
| `cssClass` | string | — | 色彩 / 模式控制：`e-primary` / `e-success` / `e-danger` / `e-outline` / `e-flat` / `e-round`（pill） |
| `disabled` | boolean | false | 停用整個 DropDownButton（trigger + 不可展開） |
| `popupPosition` | string | `BottomLeft` | 選單展開方向：`BottomLeft` / `BottomCenter` / `BottomRight` / `TopLeft` 等 |
| `@select` | event | — | 使用者選取 menu-item 時觸發；`args.item` 為選中項目 |
| `@open` | event | — | 選單展開時觸發 |
| `@close` | event | — | 選單收合時觸發 |
| `@beforeOpen` | event | — | 展開前觸發（可取消） |

**ItemModel 結構**：

```js
{
  text: string,          // 顯示文字（必填）
  iconCss: string,       // 行內 icon CSS class（可選）
  disabled: boolean,     // 停用此項（可選）
  separator: boolean,    // 顯示分隔線（可選，取代 text）
}
```

**ERP 使用模式（List Toolbar 更多操作）**：

```vue
<script setup>
import { DropDownButtonComponent as EjsDropdownbutton } from '@syncfusion/ej2-vue-splitbuttons'

const moreItems = [
  { text: '匯出 Excel', iconCss: 'e-icons e-export-excel' },
  { text: '列印', iconCss: 'e-icons e-print' },
  { separator: true },
  { text: '批次作廢', iconCss: 'e-icons e-trash', disabled: !canVoid },
]
const onMoreSelect = (args) => {
  if (args.item.text === '批次作廢') onBatchVoid()
}
</script>

<template>
  <ejs-dropdownbutton
    :items="moreItems"
    css-class="e-primary"
    @select="onMoreSelect"
  >
    更多操作
  </ejs-dropdownbutton>
</template>
```

**ERP 使用模式（Form Footer 更多操作）**：

```vue
<!-- 對應 prototype app.js 中的 moreOpen dropdown 模式，可改為 ejs-dropdownbutton 原生實作 -->
<ejs-dropdownbutton
  :items="[
    { text: '作廢' },
    { text: '列印' },
    { text: '複製為新單' },
  ]"
  @select="onFormMoreSelect"
>
  更多操作
</ejs-dropdownbutton>
```

---

## 13. 關聯　🔗

- **Tokens**：`../athena-tokens.md` §Primary / §Secondary / §Status / §Space / §Radius / §Shadow / §Typography
- **語意對照**：`../athena-design.md` §主色與動作 / §狀態色
- **同族元件**：
  - `Button.md`（同 state-layer 邏輯；無子選單時優先用 Button）
  - `FAB.md`（同 state-layer 邏輯；固定定位主操作）
  - `SplitButton`（待補文件；主動作 + 下拉並存時用）
- **Prototype 落地**：`prototyper`（`app.js` 中 `moreOpen` dropdown；可逐步改接 `ejs-dropdownbutton`）
- **Code**：`@syncfusion/ej2-vue-splitbuttons`（playground 參考：`dropdownbutton.vue`）
- **Figma 元件集**：`https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/?node-id=12736-47804`

---

## Figma 補入紀錄（2026-06-29）

> 來源：`get_variable_defs`（節點 12736-47804）+ `get_screenshot`（同節點，2048px）+ `get_metadata`（同節點，結構樹）+ `get_design_context`（節點 12736:47854，Size=Small, Type=Primary, Shape=Rounded, Mode=Filled, State=Enabled）。

### 層級判定

截圖確認：所有 variant 均為「trigger button + caret icon（▾）」結構，帶 overlay menu（有 `{ds-shadow-md}` 與 z-index overlay 行為）→ **Full**。

### Variant 軸（5 軸，完整）

| 軸 | 值 | 來源 |
|----|----|----|
| Size | Small / Medium / Large | `get_metadata` 結構樹 |
| Type | Primary / Secondary / Success / Danger | `get_variable_defs` |
| Shape | Rounded / Full Rounded | `get_metadata` 結構樹 |
| Mode | Filled / Flat / Outlined | `get_metadata` 結構樹 |
| State | Enabled / Hover / Active / Focus / Disabled | `get_metadata` 結構樹 |

### ✅ 已確認 Token 對映（Figma 變數 → `athena-tokens.md`）

| Figma 變數 | Token |
|-----------|-------|
| `ColorSf/primary` | `{color-sf-primary}` rgb(40 119 238) |
| `ColorSf/on-primary` | `{color-sf-on-primary}` white |
| `ColorSf/secondary-btn-bg` | `{color-sf-secondary-btn-bg}` white |
| `ColorSf/on-surface` | `{color-sf-on-surface}` #0f172a |
| `ColorSf/outline` | `{color-sf-outline}` #7f8996 |
| `ColorSf/outline-variant` | `{color-sf-outline-variant}` #d7dae0 |
| `ColorSf/success` / `success-text` | `{color-sf-success}` / `{color-sf-success-text}` |
| `ColorSf/danger` / `danger-text` | `{color-sf-danger}` / `{color-sf-danger-text}` |
| `ColorSf/primary-btn-hover-8` | `{color-sf-primary-btn-hover-8}` white 8% |
| `ColorSf/primary-btn-pressed-12` | `{color-sf-primary-btn-pressed-12}` white 12% |
| `ColorSf/primary-opacity-12` | `{color-sf-primary-opacity-12}` |
| `ColorSf/on-surface-opacity38` | `{color-sf-on-surface-opacity38}` |
| `ColorSf/on-surface-opacity8` | `{color-sf-on-surface-opacity8}` |
| `ColorSf/on-surface-opacity12` | `{color-sf-on-surface-opacity12}` |
| `ColorSf/surface` | `{color-sf-surface}` white |
| `Radius/RadiusSm = 4` | `{ds-radius-small}` 4px |
| `Radius/Radius10Xl = 1000` | `{ds-radius-10extra-large}` 1000px |
| `FontSizeSf/TextMd = 14` | `{font-size-sf-text-md}` |
| `FontWeightSf/Medium = 500` | `{font-weight-sf-medium}` |
| `$shadow-md`（menu） | `{ds-shadow-md}` |
| `$shadow-focus-ring1` | `{ds-shadow-focus-ring1}` |

### 🎨 量測值（非 token，指向 app.css）

- Small height：32px（`get_design_context` 節點 12736:47854）
- Small padding：px-12px / py-7px（py-7px 無精確 token，差 1px 於 `{ds-space-padding-medium}`）
- gap：4px（= `{ds-space-padding-small}`）
- Left icon container：16×16px（比 Button.md 的 18px 小）
- Caret icon container：16×16px
- Caret icon：下三角 SVG，fill 繼承 fg 色
- Medium height：36px（結構樹 bounding box）
- Large height：40px（結構樹 bounding box）

### ⚠️ 待確認

1. **Small padding-y 7px**：無精確 token 對應（`{ds-space-padding-medium}` = 8px 差 1px）。待 DS owner 確認是固定量測值或捨入誤差；目前已標 🎨 指向 app.css。
2. **Medium / Large 的 padding**：僅 Small 已量測（get_design_context 節點 12736:47854）；Medium/Large 的 padding-y 待補查各自單一 variant 節點（推算 Medium = 8px，Large = 12px，待確認）。
3. **Overlay menu 的 menu-item padding / min-width**：需對 menu popup 節點取 get_design_context 量測；目前 §3 menu 區段只有 shadow/radius/bg，其他指向 app.css。
4. **Flat / Outlined mode 的 hover state-layer**：Flat 底色 transparent，hover overlay 顏色待確認（`$secondary-bg-color-hover` 疊層邏輯可能不同）。目前 §5 Flat/Outlined 狀態未展開。
5. **Success / Danger disabled state-layer**：`Danger_btn/$danger-bg-color-disabled: #0F172A` 表示 disabled 態 base 色換為 `on-surface`（與 Primary 不同），`border-color-disabled` 也為 `#0F172A`。此行為與 Button.md 的 `{color-sf-primary-opacity-12}` 不同，需核實是否為設計意圖。
