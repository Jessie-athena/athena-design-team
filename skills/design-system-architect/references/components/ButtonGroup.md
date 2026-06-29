---
name: ButtonGroup
category: 按鈕與動作
tier: lite
status: ⬜ 待採用（Syncfusion CSS-only，ERP 有限使用）
authority: ERP CLAUDE.md（Syncfusion ButtonGroup CSS-only, @syncfusion/ej2-vue-buttons）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16569-3817
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs + get_screenshot + get_metadata + get_design_context(Medium Primary + Medium Secondary)；紀錄見文末「Figma 補入紀錄」
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺值一律 token-reference；對不上既有 token 時停下回報，勿臆造。
> §3–§5 已用 Figma 多工具校準；v0.1 所有量測完整。紀錄見文末「Figma 補入紀錄」（2026-06-29）。

## 1. 概述　📋

一組橫向（或縱向）排列的按鈕，共用視覺邊框、統一圓角，呈現為單一控制單元。

**何時使用**：
- **互斥切換**（單選）：List / Form 模式切換、週期選擇（週 / 月 / 年）、檢視類型切換。
- **多選切換**（複選）：文字格式（粗 / 斜 / 底線）、篩選標籤多選。

**何時不用**：
- 非互斥動作 → 用個別 `Button`（各自獨立觸發，語意不同）。
- 單一動作 → 用 `Button`（不需要「組」的框架）。
- 帶下拉子項目 → 用 `SplitButton` / `DropDownButton`。
- 選項超過 5 個以上 → 改 `DropDownList`，避免橫向溢出。

## 2. Anatomy　🎨

```
[group-container]
  ├── [button-item--first]   ← 左側圓角（tl / bl）
  ├── [button-item--center]  ← 無圓角（中間項，可有多個）
  └── [button-item--last]    ← 右側圓角（tr / br）
```

- **group-container**：純 flex row（無自身 border、bg、radius）
- **button-item**：每個 `<button>` 或 `<input>+<label>` 對；共享邊框（相鄰邊重疊，無額外 divider 元素）
- **selected-item**：當前選中的 button-item，疊加白色 state-layer 於底色之上
- 選擇模式 A（互斥）：`<input type="radio"> + <label class="e-btn">`，由 CSS `:checked` 驅動選中態
- 選擇模式 B（多選）：`<input type="checkbox"> + <label class="e-btn">`
- 選擇模式 C（無狀態）：純 `<button class="e-btn">`，狀態由父層控制

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# Size=Medium（預設）；量測來源：get_design_context 節點 16569:3870 / 16569:4046
height:
  extra-small: 24px    # 🎨 XS
  small:        32px    # 🎨
  medium:       36px    # 🎨 預設
  large:        40px    # 🎨

padding:
  default:     "{ds-space-padding-medium} {ds-space-padding-extra-large}"   # 🔗 py-8px / px-16px（Medium 量測）
  comfortable: "{ds-space-padding-large} {ds-space-padding-2extra-large}"   # 🔗 py-12px / px-24px（Comfortable token 層推算）

# 圓角：只有 first / last item 有圓角，center item 為 0
radius-first:  "{ds-radius-small}"           # 🎨🔗 tl + bl = 4px（Figma Radius/RadiusSm）
radius-last:   "{ds-radius-small}"           # 🎨🔗 tr + br = 4px
radius-center: "{ds-borderwidth-none}"       # 🔗 0px（`Radius/RadiusNone`）

font:    "{font-size-sf-text-md} / {font-weight-sf-medium}"   # 🔗 14px / 500
# line-height 1.5 / letter-spacing 0.24px（Body Content/14pt/Medium，與 Button 相同）

gap-label-icon: "{ds-space-padding-small}"   # 🔗 icon↔label 4px（與 Button 一致）

# 相鄰邊界：無顯式 divider 元素；各 button-item 的 1px border 相鄰重疊，視覺無縫
divider: none
```

## 4. Variants　🎨🔗

> Figma 元件集（節點 16569-3817）variant 軸：**Size**（XS / Small / Medium / Large）× **Type**（Primary / Secondary / Success / Danger）。共 16 個預設變體。

```yaml
# ── Type 軸（按鈕底色 / 文字色 / 邊框色）──
primary:
  bg:     "{color-sf-primary}"           # 🔗 rgb(40 119 238)
  fg:     "{color-sf-on-primary}"        # 🔗 white
  border: "{color-sf-primary}"           # 🎨🔗 Primary_btn/$primary-border-color = #2877EE

secondary:
  bg:     "{color-sf-secondary-btn-bg}"  # 🔗 white
  fg:     "{color-sf-on-surface}"        # 🔗 rgb(15 23 42)
  border: "{color-sf-surface}"           # 🎨🔗 Sec_btn/$secondary-border-color = #FFFFFF（邊框與底色同色，分隔靠 shadow）
  shadow: "{ds-shadow-sm}"              # 🎨🔗 $shadow-sm 套用於每個 button-item

success:
  bg:     "{color-sf-success}"           # 🔗 rgb(18 183 106)
  fg:     "{color-sf-success-text}"      # 🔗 white
  border: "{color-sf-success}"           # 🎨🔗 Success_btn/$success-border-color = #12B76A

danger:
  bg:     "{color-sf-danger}"            # 🔗 rgb(244 73 62)
  fg:     "{color-sf-danger-text}"       # 🔗 white
  border: "{color-sf-danger}"            # 🎨🔗 Danger_btn/$danger-border-color = #F4493E

# ── Size 軸（高度，padding 見 §3）──
# XS=24px / Small=32px / Medium=36px / Large=40px
# Syncfusion CSS 對應：e-small（Small/XS）/ 無額外 class（Medium）/ e-bigger（Large）
```

## 5. States　🎨🔗

> 選中態（selected）為 ButtonGroup 最重要的 state，其餘繼承 Button 邏輯。
> 選中態機制：`input:checked + label.e-btn`（CSS `:checked` selector）觸發 Syncfusion 主題樣式；Figma 以 state-layer 表達。

```yaml
# ── 各 button-item 的 state ──
default:
  primary: { bg: "{color-sf-primary}", fg: "{color-sf-on-primary}", border: "{color-sf-primary}" }

selected:            # 🎨 Figma Atom/Button Last 截圖可見選中態疊層
  primary:
    overlay: "{color-sf-primary-btn-pressed-12}"   # 🔗 white 12%（等同 Button pressed 疊層）
    # 渲染值：linear-gradient(rgba(255,255,255,0.12) 100%), linear-gradient(#2877ee 100%)
    # bg 結果 ≈ rgb(40 119 238) + 白 12% = 視覺上略淡（亮）的 primary
  secondary:
    # 🎨 Figma Sec_btn/$secondary-border-color 為 #FFFFFF（同底色）
    # ⚠️ token gap：secondary 選中態在 Figma 無明確視覺差異（border=white、bg=white）；
    #    Syncfusion 實作依賴 e-active class 的主題 CSS；確切 token 值待補入 athena-tokens.md

hover:
  overlay: "{color-sf-primary-btn-hover-8}"    # 🔗 white 8%（與 Button 一致）

active / pressed:
  overlay: "{color-sf-primary-btn-pressed-12}" # 🔗 white 12%（與 Button 一致）

focus:
  ring: "{ds-shadow-focus-ring1}"              # 🔗 黑 3px 外環 + 白 1px 內環

disabled:
  # 各 button-item 獨立 disabled（不需整組一起停用）
  bg:     "{color-sf-primary-opacity-12}"      # 🔗 primary 12% tint（與 Button disabled 一致）
  fg:     "{color-sf-on-surface-opacity38}"    # 🔗 on-surface 38%
  border: "{color-sf-outline}"                 # 🔗 rgb(127 137 150)
```

## 9. a11y　📋

- **`role="group"`**：`div.e-btn-group` 應加 `role="group"` + `aria-label`（如 `aria-label="檢視模式"`）。
- **選中狀態**：
  - 互斥（radio）：選中的 `<input type="radio">` 為 `checked`；screen reader 朗讀「已選取」。
  - 多選（checkbox）：`<input type="checkbox">` 的 `checked` 狀態傳遞語意。
  - 純按鈕（無 input）：選中的 `<button>` 加 `aria-pressed="true"`。
- **鍵盤導航**：
  - 互斥模式：方向鍵（←/→ 或 ↑/↓）在 radio 選項間移動（瀏覽器原生行為）。
  - 多選 / 純按鈕：Tab 在 group 內各 item 間移動；Space / Enter 觸發。
- **Focus ring**：`{ds-shadow-focus-ring1}` 套用於當前 focus 的 button-item。
- **Mobile touch target**：各 button-item 高度至少 44px（ERP 桌面優先，僅供參考）。
- **Disabled item**：設 `disabled` attribute 並確保 color contrast 符合 WCAG AA（`{color-sf-outline}` + 38% fg 已吻合 3:1 最低要求）。

## 12. API / Props　📋

> Syncfusion ButtonGroup 為 **CSS-only 方案**，無獨立 Vue 元件；透過 CSS class 與原生 HTML 元素組合。

| 屬性 / Class | 型別 / 值 | 預設 | 說明 |
|---|---|---|---|
| `.e-btn-group` | CSS class | — | 容器必加；啟用 ButtonGroup 樣式 |
| `.e-vertical` | CSS class | — | 加在容器上，切換為垂直排列 |
| `.e-btn` | CSS class | — | 每個 button / label 必加 |
| `.e-primary` | CSS class | — | Primary 顏色（Type=Primary） |
| `.e-success` | CSS class | — | Success 顏色（Type=Success） |
| `.e-danger` | CSS class | — | Danger 顏色（Type=Danger） |
| `.e-small` | CSS class | — | 縮小尺寸（對應 Size=Small / XS） |
| `.e-icon-btn` | CSS class | — | icon-only 按鈕（搭配 `e-btn-icon`） |
| `type="radio"` | HTML attr | — | 互斥單選模式（`input` + `label` 對） |
| `type="checkbox"` | HTML attr | — | 多選模式（`input` + `label` 對） |
| `disabled` | HTML attr | — | 停用單一 item |
| `name` | HTML attr | — | radio 群組名稱（單選模式必填） |

**Vue 3 慣用模式（互斥切換）**：

```vue
<script setup>
const view = ref('list')
</script>

<template>
  <div class="e-btn-group" role="group" aria-label="檢視模式">
    <input id="btn-list" v-model="view" type="radio" name="view-mode" value="list">
    <label class="e-btn" for="btn-list">列表</label>
    <input id="btn-form" v-model="view" type="radio" name="view-mode" value="form">
    <label class="e-btn" for="btn-form">表單</label>
  </div>
</template>
```

**Vue 3 慣用模式（純按鈕，狀態由父層控制）**：

```vue
<script setup>
const period = ref('month')
</script>

<template>
  <div class="e-btn-group" role="group" aria-label="週期">
    <button
      v-for="opt in ['week','month','year']"
      :key="opt"
      class="e-btn"
      :aria-pressed="period === opt"
      @click="period = opt"
    >{{ { week:'週', month:'月', year:'年' }[opt] }}</button>
  </div>
</template>
```

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §Status / §Border Width / §Radius / §Shadow / §Space / §Typography
- 上游元件：`Button.md`（ButtonGroup 的每個 item 共用 Button token 體系）
- 語意對照：`../athena-design.md` §主色與動作 / §狀態色
- Prototype 落地：`prototyper`（ERP List View Toolbar 的模式切換，見 `prototype/project/` 各 List View `.toolbar__right .view-switch`）
- Code（playground）：`/Users/athena/working/ddd/syncfusion-playground/app/pages/playground/buttongroup.vue`
- Code（import）：`@syncfusion/ej2-vue-buttons`（CSS-only，無需 import 元件；僅引入樣式）

---

## Figma 補入紀錄（2026-06-29）

> 來源：`get_variable_defs`（節點 `16569:3817`）+ `get_screenshot`（同節點，1408×324）+ `get_metadata`（同節點，結構樹）+ `get_design_context`（節點 `16569:3870` Medium Primary；`16569:4046` Medium Secondary）。

**層級判定**：**Lite**。ButtonGroup 是純視覺 CSS 組合——多個 Button item 緊排、共用首尾 border-radius，無 JS 元件、無獨立狀態機、無可展開子項目。

**Figma variant 軸確認（get_metadata）**：
- `Size`：Extra Small（24px） / Small（32px） / Medium（36px） / Large（40px）
- `Type`：Primary / Secondary / Success / Danger
- 共 16 個 symbol（4×4）；無 Direction 軸（vertical 由 CSS class `.e-vertical` 控制，不在 Figma 元件集內）
- 元件名稱模式：`Atom / Button First` / `Atom / Button Center` / `Atom / Button Last`（首 / 中 / 尾三部位）

**Token 對映（get_variable_defs / get_design_context）**：

| Figma 變數 | 對映 token | 備註 |
|---|---|---|
| `Primary_btn/$primary-bg-color` | `{color-sf-primary}` | Primary bg |
| `Primary_btn/$primary-text` | `{color-sf-on-primary}` | Primary 文字 |
| `Primary_btn/$primary-border-color` | `{color-sf-primary}` | 邊框同底色 |
| `Primary_btn/$primary-bg-color-pressed` | base + `{color-sf-primary-btn-pressed-12}` | 選中態 / pressed 疊白 12% |
| `Sec_btn/$secondary-bg-color` | `{color-sf-secondary-btn-bg}` | Secondary bg white |
| `Sec_btn/$secondary-text-color` | `{color-sf-on-surface}` | Secondary 文字 |
| `Sec_btn/$secondary-border-color` | `{color-sf-surface}` | 邊框=白（靠 shadow 做視覺分隔） |
| `$shadow-sm` | `{ds-shadow-sm}` | Secondary 每 item 的 drop-shadow |
| `Success_btn/$success-bg-color` | `{color-sf-success}` | — |
| `Success_btn/$success-text` | `{color-sf-success-text}` | — |
| `Danger_btn/$danger-bg-color` | `{color-sf-danger}` | — |
| `Danger_btn/$danger-text` | `{color-sf-danger-text}` | — |
| `Radius/RadiusSm` | `{ds-radius-small}` | 4px，首尾圓角 |
| `Radius/RadiusNone` | `{ds-borderwidth-none}` | 0px，中間項圓角 |
| `FontSizeSf/TextMd` | `{font-size-sf-text-md}` | 14px |
| `FontWeightSf/Medium` | `{font-weight-sf-medium}` | 500 |

**視覺量測（get_design_context，Medium）**：
- `padding`：`py-8px px-16px`（= `{ds-space-padding-medium}` / `{ds-space-padding-extra-large}`）
- `label gap`：4px（`{ds-space-padding-small}`，label 內 icon↔text）
- Primary 選中態：`linear-gradient(rgba(255,255,255,0.12) 100%), linear-gradient(rgb(40,119,238) 100%)`→ white 12% = `{color-sf-primary-btn-pressed-12}` 疊層確認
- Secondary 每 item drop-shadow：`drop-shadow(0px 1px 1.5px rgba(0,0,0,0.15)) drop-shadow(0px 1px 1px rgba(0,0,0,0.3))` = `{ds-shadow-sm}` 確認

**Token gap（需後續補入 `athena-tokens.md`）**：
1. **Secondary 選中態（selected bg）**：Figma `Sec_btn/$secondary-bg-color` 選中狀態顯示 `#FFFFFF,#FFFFFF`（two-stop 同色），無明確視覺疊層；Syncfusion 主題 CSS 以 `.e-active` class 自行處理。確切 selected bg token 待由 Syncfusion 主題 CSS 反查補入。本文件 §5 secondary selected 暫標 `⚠️ token gap`。

**本次無新增 token**：除上述 gap 外，所有確認值均能在現有 `athena-tokens.md` 找到對應。
