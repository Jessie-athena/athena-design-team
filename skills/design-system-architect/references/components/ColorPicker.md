---
name: ColorPicker
category: 輸入與選擇
tier: full
status: ✅ 已採用
authority: ERP CLAUDE.md（Syncfusion ColorPicker, @syncfusion/ej2-vue-inputs）
figma-node: JhcpyIEEzSChcEXMRJUiIm/11932-30084
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs + get_metadata + get_design_context（節點 11932:30085 Color Range Area / 節點 11932:30124 Swatches）+ get_screenshot（節點 11932:30084，2048px）；紀錄見文末「Figma 補入紀錄」
---

> 依 `../component-doc-schema.md`（Full 層）產出。視覺值一律 token-reference；對不上既有 token 時停下回報，勿臆造。
> §3–§5 已用 Figma `get_variable_defs`（節點 11932-30084）+ `get_design_context`（節點 11932:30085 Color Range Area Small；節點 11932:30124 Swatches Small）+ `get_screenshot`（節點 11932:30084，full panel）校準；未解 / 衝突項見文末「Figma 補入紀錄」。

---

## 1. 概述　📋

色彩選取器。讓使用者從色彩範圍區（漸層拖拉）或預設色板（Swatches）中挑選顏色，並可精確輸入 Hex / R / G / B / A 值。

**何時使用**：
- 使用者需要自由選色（如圖表系列色、資料標籤底色、個人化顏色設定）。
- 提供預設色板供快速選取業務常用色（Swatches 模式）。
- 需要同時支援透明度（Alpha 滑桿）的場景。

**何時不用**：
- 選項為固定語意色（如狀態色 success / danger）→ 改用 `DropDownList` 搭配色塊預覽（使用者不應自由選色破壞語意）。
- 僅顯示顏色而不允許編輯 → 只顯示色塊，不放 ColorPicker。
- 移動裝置主流程中直接嵌入 → 考慮 inline 模式或另開 Dialog（觸控面積需確認）。

---

## 2. Anatomy　🎨

```
┌──────────────────────────────────────────────────────────┐
│  panel（overlay / inline）                               │
│  ┌────────────────────────────────────────────────────┐  │
│  │  [template=Color Range Area]                       │  │
│  │  color-canvas（漸層拖拉區，canvas）                 │  │
│  │  ┌──────────────────────────────────────────────┐  │  │
│  │  │  drag-handle（圓形手柄）                      │  │  │
│  │  └──────────────────────────────────────────────┘  │  │
│  │  hue-slider ─────────────────── ●                  │  │
│  │  opacity-slider ──────────────── ●                 │  │
│  │                           ┌──────────┐             │  │
│  │  [hex-input] [r] [g] [b] [a]  >      │ preview-swatch│
│  │                           └──────────┘             │  │
│  │  [🌈 system-palette?]                              │  │
│  │  ───────────────────────────────────────────────   │  │
│  │  [Cancel]                               [Apply]    │  │
│  └────────────────────────────────────────────────────┘  │
│                                                          │
│  ── OR ──                                                │
│                                                          │
│  ┌────────────────────────────────────────────────────┐  │
│  │  [template=Swatches]                               │  │
│  │  swatch-grid（10 × 7 色塊，Small）                  │  │
│  │  hue-slider ─────────────────── ●                  │  │
│  │  opacity-slider ──────────────── ●                 │  │
│  │                           ┌──────────┐             │  │
│  │  [hex-input] [r] [g] [b] [a]  >      │ preview-swatch│
│  │                           └──────────┘             │  │
│  │  ───────────────────────────────────────────────   │  │
│  │  [Cancel]                               [Apply]    │  │
│  └────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────┘
```

子元素：
- **panel**：最外層容器，帶 `{ds-shadow-md}` 與 `{ds-radius-extra-large}`（12px）。
- **color-canvas**（Color Range Area 限定）：漸層拖拉區，以 Canvas 渲染；邊框 2px `Border/$border-light`（🎨）。
- **swatch-grid**（Swatches 限定）：預設色板格，10 欄 × 7 行（Small），以 `Border/$flyout-border`（🎨）2px 圍框。
- **drag-handle**：Color Range Area 的圓形拖拉手柄，落點表示選色位置。
- **hue-slider**：色相滑桿（canvas 渲染，固定彩虹漸層），`{ds-radius-10extra-large}`（pill）。
- **opacity-slider**：透明度滑桿（canvas 棋盤 + 色相橫向漸層），`{ds-radius-10extra-large}`（pill）。
- **preview-swatch**：當前選色預覽方塊，約 56×56px，`{ds-radius-extra-large}`（12px）。
- **hex-input**：Hex 色碼輸入框，`TextBox` 底線風格（`{color-sf-outline}` 底線，高 32px）。
- **rgba-inputs**：R / G / B / A 各自獨立輸入框，`TextBox` 底線風格，label 12px/400。
- **navigate-icon**（`>`）：切換 Hex ↔ RGB / HSL 模式按鈕（Syncfusion 內建）。
- **system-palette**（可選）：底部 4 格迷你色塊，顯示系統預設色（Color Range Area 範本限定）。
- **action-footer**：Cancel / Apply 兩個 ghost 按鈕，右對齊；左側可留 system-palette 空間。

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ——— Panel 容器 ———
panel_bg:         "{color-sf-surface}"            # 🔗 white（覆一層 {color-sf-primary-opacity-5} 底色）
panel_shadow:     "{ds-shadow-md}"                # 🔗 overlay/dropdown 提升（athena-tokens.md §Shadow）
panel_radius:     "{ds-radius-extra-large}"       # 🔗 12px（Radius/RadiusXl = 12）

# ——— 尺寸（量測值，見 §Figma 補入紀錄）———
# Size=Small
width_small:      350px                           # 🎨 量測值
height_small_color_range: 454px                  # 🎨 量測值
height_small_swatches:    501px                  # 🎨 量測值
# Size=Large
width_large:      390px                           # 🎨 量測值
height_large_color_range: 506px                  # 🎨 量測值
height_large_swatches:    532px                  # 🎨 量測值

# ——— color-canvas 邊框 ———
canvas_border:    "2px Border/$border-light"      # 🎨 #C4C7C5；無對應 token，指向 app.css

# ——— Swatch grid 邊框 ———
swatch_border:    "2px Border/$flyout-border"     # 🎨 #EDEBE9；無對應 token，指向 app.css

# ——— Slider（hue / opacity）———
slider_radius:    "{ds-radius-10extra-large}"     # 🔗 1000px（pill 形）

# ——— Preview swatch ———
preview_size:     56px                            # 🎨 量測值（Large 約 66px）
preview_radius:   "{ds-radius-extra-large}"       # 🔗 12px

# ——— Hex / RGBA 輸入框 ———
input_height:     32px                            # 🎨 量測值
input_border:     "1px bottom-only {color-sf-outline}"  # 🔗 #7f8996 底線風格
input_bg:         "{color-sf-transparent}"        # 🔗 透明底（Filled line 風格）
input_value_font: "{font-size-sf-text-md} / {font-weight-sf-normal}"   # 🔗 14px / 400
input_value_color: "{ds-color-placeholder}"       # 🔗 #67717e（placeholder 即值顯示色）
input_label_font: "{font-size-sf-text-sm} / {font-weight-sf-normal}"   # 🔗 12px / 400
input_label_color: "{color-sf-on-surface-variant}"  # 🔗 #3c4a5b
input_px:         10px                            # 🎨 量測值，px-10（待對齊 token）
input_letter_spacing: "0.24px"                    # 🎨（Body 14pt/Regular letterSpacing）

# ——— Action 按鈕（Cancel / Apply）———
action_btn_font:  "{font-size-sf-text-md} / {font-weight-sf-medium}"   # 🔗 14px / 500
action_btn_color: "{color-sf-primary}"            # 🔗 #2877ee（ghost 文字色）
action_btn_bg:    "{color-sf-transparent}"        # 🔗 無底色
action_btn_radius: "{ds-radius-small}"            # 🔗 4px（Radius/S = 4）
action_btn_px:    12px                            # 🎨 量測值
action_btn_py:    7px                             # 🎨 量測值（無精確 token，參見 DropDownButton.md §3 同款 py-7px 待確認）
action_btn_gap:   "{ds-space-padding-small}"      # 🔗 icon↔label 4px
action_letter_spacing: "0.1px"                    # 🎨（Label 14pt/Medium letterSpacing）

# ——— Icon（切換模式 / system-palette 圖示）———
icon_color:       "Icon/$icon-color #49454e"      # 🎨 無對應 token；最近似 {color-sf-on-surface-variant} #3c4a5b 但值不同，指向 app.css
icon_size:        16px                            # 🎨 量測值
```

---

## 4. Variants / Types　🎨🔗

> Figma 元件集（節點 11932-30084）共 **2 個 variant 軸**：
> - **Size**：Small / Large
> - **Template**（色彩選取介面模式）：Color Range Area / Swatches

```yaml
# ——— Size 軸 ———
small:
  width:  350px     # 🎨
  panel_bg_tint: "{color-sf-primary-opacity-5}"   # 🔗 primary 5% 底層（overlay tint）

large:
  width:  390px     # 🎨
  # 其餘量測值見 §3；tint 同 small

# ——— Template 軸 ———
color_range_area:
  has_canvas:   true       # 📋 漸層拖拉 canvas；非 swatch grid
  has_swatches: false
  has_system_palette: true  # 📋 底部 4 格迷你系統色板（Color Range Area 模式含此）
  canvas_border: "2px Border/$border-light"  # 🎨 見 §3

swatches:
  has_canvas:   false
  has_swatches: true        # 📋 10 × 7 色塊（Small）
  has_system_palette: false
  swatch_border: "2px Border/$flyout-border"  # 🎨 見 §3
  # swatch tile 為 hardcode 色值（Figma 設計稿各 Tile 獨立 bg），指向 app.css
```

> **兩個 Template 共用**：hue-slider / opacity-slider / preview-swatch / hex-input / rgba-inputs / action-footer。差異僅在上半段（canvas 或 swatch-grid）。

---

## 5. States　🎨🔗

```yaml
# ——— Panel ———
panel_default:
  shadow: "{ds-shadow-md}"       # 🔗
  bg:     "{color-sf-surface}"   # 🔗

# ——— Hex / RGBA 輸入框 ———
input_default:
  border_bottom: "{color-sf-outline}"            # 🔗 #7f8996
  bg:            "{color-sf-transparent}"        # 🔗

input_focus:
  ring:  "{ds-shadow-focus-ring1}"               # 🔗 白 1px 內環 + 黑 3px 外環（键盤導航）
  # 注意：Syncfusion 底線 input focus 態通常為 {color-sf-primary} 底線加厚（Filled Line 規則），
  # 但本元件 Figma 未展示 focus state；以 Syncfusion 原生行為為準，待 get_screenshot 補確認

# ——— Action 按鈕（Cancel / Apply）———
action_default:
  bg:   "{color-sf-transparent}"                 # 🔗
  fg:   "{color-sf-primary}"                     # 🔗

action_hover:
  overlay: "{color-sf-primary-opacity-5}"        # 🔗 primary 5%（ghost hover 疊層）

action_focus:
  ring: "{ds-shadow-focus-ring1}"                # 🔗

# ——— Swatch Tile ———
tile_default:
  bg:      "（各 tile hardcode 色，指向 app.css）"  # 🎨
  outline: "none"

tile_hover:
  # 📋 Syncfusion 預設：tile hover 顯示縮放或邊框高亮；值待 get_design_context 細查 hover variant 確認

tile_selected:
  # 📋 Syncfusion 預設：選中 tile 顯示 checkmark 或輪廓；值待確認

# ——— Empty state ———
# 📋 ColorPicker 無 empty state（面板始終顯示預設色）
# ——— Loading state ———
# 📋 ColorPicker 無 loading state（canvas 同步渲染）
# ——— Error state ———
input_error:
  # 📋 Hex / RGBA 輸入非法值時 Syncfusion 恢復上一個合法值；無獨立視覺 error state
```

---

## 6. Behavior 行為　📋

| 情境 | 行為 |
|------|------|
| 點擊 color-canvas | 拖拉手柄移至點擊位置，即時更新 Hex / RGBA 輸入框與 preview-swatch |
| 拖拉 drag-handle | 連續更新選色值（`select` 事件，`args.currentValue`） |
| 拖拉 hue-slider | 更新色相，canvas 漸層重繪，RGBA 輸入同步 |
| 拖拉 opacity-slider | 更新 Alpha 值，preview-swatch 透明度同步 |
| 點擊 swatch-tile | 立即選中該色，更新 Hex / RGBA、hue-slider 位置、preview-swatch |
| 輸入 Hex 欄位 | 若合法（`#RRGGBB` / `#RRGGBBAA`）立即同步 RGBA 與 canvas 位置；非法則還原 |
| 輸入 R / G / B / A 欄位 | 值域 0–255（A 亦然，Syncfusion 以 0–255 表示不透明度）；超出範圍自動截斷 |
| 點擊 `>` 按鈕 | 切換 Hex ↔ RGB / HSL 輸入模式（Syncfusion 內建循環） |
| 點擊 Apply | 觸發 `change` 事件（`args.currentValue`），關閉面板（若為 popup 模式） |
| 點擊 Cancel | 還原至最後一次 `change` 確認的值，關閉面板（若為 popup 模式） |
| Inline 模式 | 無 Cancel / Apply；每次互動即觸發 `select` 事件，不需確認 |
| **Popup 模式** | 透過 `<ejs-colorpicker>` 搭配 trigger button 展開；`Esc` 關閉 |

---

## 8. Keyboard　📋

| 按鍵 | 動作 |
|------|------|
| `Tab` | 在 hex-input → R → G → B → A → `>` → Cancel → Apply 間順序移動 |
| `↑` / `↓` | 在 RGBA 輸入框：數值 ±1；在 canvas drag-handle：垂直移動色彩明度 |
| `←` / `→` | 在 canvas drag-handle：水平移動色彩飽和度；在 hue/opacity slider：移動滑桿 |
| `Enter` | Apply |
| `Esc` | Cancel（等同 Cancel 按鈕行為） |
| `Space` | 於 swatch-tile：選中該色 |
| `Home` / `End` | 於 hue/opacity slider：跳至最小 / 最大值 |

---

## 9. a11y 無障礙　📋

- **Panel**：加 `role="dialog"` 或 `aria-label="色彩選取器"` 若為 popup。
- **color-canvas**：加 `role="slider"` + `aria-label="色彩選取區"` + `aria-valuetext`（說明當前選色）。
- **hue-slider**：`role="slider"` + `aria-label="色相"` + `aria-valuemin/max/now`。
- **opacity-slider**：`role="slider"` + `aria-label="不透明度"` + `aria-valuemin/max/now`。
- **swatch-tile**：每格 `role="option"` + `aria-label="色彩名稱或 hex"`，grid 容器 `role="listbox"` 或 `role="grid"`。
- **hex-input / rgba-inputs**：標準 `<input>` + `aria-label`（「Hex」/「R」/「G」/「B」/「A」）；label 已可見，另確認 `for` 屬性或 `aria-labelledby`。
- **preview-swatch**：`aria-live="polite"` 宣告當前選色（供螢幕閱讀器告知）。
- **Focus ring**：`{ds-shadow-focus-ring1}`（白 1px 內環 + 黑 3px 外環），鍵盤導航時必須可見。
- **對比**：
  - Action button 文字 `{color-sf-primary}` (#2877ee) 在白底上：符合 WCAG AA（約 4.5:1）。
  - Input label `{color-sf-on-surface-variant}` (#3c4a5b) 在白底上：需確認（約 7.7:1，達 AAA）。
  - Input value `{ds-color-placeholder}` (#67717e) 在白底上：約 4.7:1，符合 WCAG AA。
- **觸碰目標**：Apply / Cancel 按鈕需確認觸碰區域 ≥ 44×44px（量測 py-7px 高 32px，需 padding 補足）。

---

## 10. 跨平台 Adaptive　📋

- **Web（≥1280px）**：完整面板，Popup 或 Inline 均可。
- **Web（768–1023px）**：Popup 模式置中顯示；面板寬度可適配，最小 320px。
- **App（Mobile）**：強烈建議僅提供 Swatches 模式（避免 canvas 精準拖拉在觸控裝置體驗差）；可改為 bottom-sheet overlay。
- **App（Tablet）**：接近 Web，可使用 Color Range Area；確認拖拉手柄觸碰目標 ≥ 44×44px。
- **不做 < 768px Web**：依 prototype 規範（§9 RWD），< 768px 不支援。

---

## 11. Content 指引　📋

- **Apply 按鈕**：文字固定「Apply」（英文 UI）/ 「套用」（中文 UI）；勿改為「確定」避免歧義。
- **Cancel 按鈕**：文字固定「Cancel」/ 「取消」。
- **輸入框 label**：R / G / B / A / Hex — 保持縮寫，不需展開為 Red / Green… (B2B 使用者熟悉 RGB)。
- **空值 placeholder**：依 Syncfusion 行為，預設顯示初始值（如 `#FFFFFF`），不需另設 placeholder 文字。
- **錯誤回饋**：Syncfusion 自動還原非法值，無需另提供錯誤文字；若需強化可加 Toast（見 Toast 元件）。

---

## 12. API / Props　📋

> 以下為 Syncfusion `ejs-colorpicker` 的關鍵 props；完整文件見 [Syncfusion ColorPicker API](https://ej2.syncfusion.com/vue/documentation/api/color-picker/)。
> Import：`ColorPickerComponent as EjsColorpicker` from `@syncfusion/ej2-vue-inputs`。

| Prop | Type | Default | 說明 |
|------|------|---------|------|
| `value` | string | `#008000ff` | 初始色值（hex8 格式，含 alpha） |
| `mode` | `Picker \| Palette` | `Picker` | `Picker` = Color Range Area；`Palette` = Swatches |
| `inline` | boolean | false | `true` = 行內顯示（無 popup trigger）；`false` = popup 模式 |
| `showButtons` | boolean | true | 是否顯示 Apply / Cancel；`inline: true` 時建議 `false` |
| `modeSwitcher` | boolean | true | 是否顯示 Picker ↔ Palette 切換按鈕 |
| `columns` | number | 10 | Palette 模式每列色塊數 |
| `presets` | `PresetModel[]` | Syncfusion 預設 | 自訂色板分組（`label` + `colors[]`） |
| `noColor` | boolean | false | 是否顯示「無色」選項（透明） |
| `disabled` | boolean | false | 停用整個 ColorPicker |
| `enableOpacity` | boolean | true | 是否顯示 opacity（Alpha）滑桿 |
| `cssClass` | string | — | 客製 CSS class |
| `@change` | event | — | Apply 確認後觸發；`args.currentValue.hex8`（`#RRGGBBAA`）、`args.currentValue.rgba` |
| `@select` | event | — | 每次互動（inline 模式每次選色）觸發；`args.currentValue` 同上 |
| `@beforeClose` | event | — | popup 即將關閉時觸發（可取消） |
| `@open` | event | — | popup 展開後觸發 |

**ERP 使用模式（Popup 模式，帶初始值）**：

```vue
<script setup>
import { ColorPickerComponent as EjsColorpicker } from '@syncfusion/ej2-vue-inputs'

const selectedColor = ref('#2877eeff')

const onColorChange = (args) => {
  selectedColor.value = args.currentValue.hex8
}
</script>

<template>
  <ejs-colorpicker
    :value="selectedColor"
    mode="Picker"
    :show-buttons="true"
    @change="onColorChange"
  />
</template>
```

**ERP 使用模式（Swatches，Inline，自訂色板）**：

```vue
<script setup>
import { ColorPickerComponent as EjsColorpicker } from '@syncfusion/ej2-vue-inputs'

const chartColor = ref('#12b76aff')
const palettePresets = [
  {
    label: '業務色',
    colors: [
      '#2877ee', '#12b76a', '#f44940', '#f79009',
      '#05bb3d', '#0085ff', '#7107dc', '#d21020',
    ],
  },
]

const onSelect = (args) => {
  chartColor.value = args.currentValue.hex8
}
</script>

<template>
  <ejs-colorpicker
    :value="chartColor"
    mode="Palette"
    :inline="true"
    :show-buttons="false"
    :presets="palettePresets"
    :columns="8"
    @select="onSelect"
  />
</template>
```

---

## 13. 關聯　🔗

- **Tokens**：`../athena-tokens.md` §Surface/Background / §Primary / §Outline / §Space / §Radius / §Shadow / §Typography
- **語意對照**：`../athena-design.md` §主色與動作 / §表面層
- **同族元件**：
  - `TextBox.md`（hex-input / rgba-inputs 使用底線 Filled Line 風格同族）
  - `DropDownButton.md`（action-footer 按鈕 py-7px 同款待確認項）
  - `Dialog.md`（Popup 模式可搭配 Dialog 展示 ColorPicker）
- **Prototype 落地**：`prototyper`（ERP prototype 目前無 ColorPicker 使用案例；如需嵌入，引用 `ejs-colorpicker`）
- **Code**：`@syncfusion/ej2-vue-inputs`（playground 參考：無獨立 `colorpicker.vue`，參照官方 API 文件）
- **Figma 元件集**：`https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/?node-id=11932-30084`

---

## Figma 補入紀錄（2026-06-29）

> 來源：`get_variable_defs`（節點 11932-30084）+ `get_metadata`（同節點，結構樹）+ `get_design_context`（節點 11932:30085 Size=Small Template=Color Range Area；節點 11932:30124 Size=Small Template=Swatches）+ `get_screenshot`（節點 11932:30084，2048px）。

### 層級判定

截圖確認：兩個 Template 均為帶 overlay 面板（shadow-md、radius-xl）的複合元件，含 canvas / swatch-grid、雙滑桿、RGBA 輸入群組、preview-swatch、action-footer，視覺複雜度遠超原子元件 → **Full**。

### Variant 軸（2 軸，完整）

| 軸 | 值 | 來源 |
|----|----|----|
| Size | Small / Large | `get_metadata` 結構樹 |
| Template | Color Range Area / Swatches | `get_metadata` 結構樹 |

### ✅ 已確認 Token 對映（Figma 變數 → `athena-tokens.md`）

| Figma 變數 | Token |
|-----------|-------|
| `ColorSf/surface` | `{color-sf-surface}` rgb(255 255 255) |
| `ColorSf/primary` | `{color-sf-primary}` rgb(40 119 238) |
| `ColorSf/primary-opacity-5` | `{color-sf-primary-opacity-5}` rgb(40 119 238 / 0.05) |
| `ColorSf/on-surface-variant` | `{color-sf-on-surface-variant}` rgb(60 74 91) |
| `ColorSf/outline` | `{color-sf-outline}` rgb(127 137 150) |
| `ColorSf/white` | `{color-sf-white}` white |
| `ColorSf/transparent` | `{color-sf-transparent}` |
| `DsColor/placeholder` | `{ds-color-placeholder}` rgb(103 113 126) |
| `$shadow-md` | `{ds-shadow-md}` |
| `$shadow-sm` | `{ds-shadow-sm}` |
| `Radius/RadiusXl = 12` | `{ds-radius-extra-large}` 12px |
| `Radius/RadiusSm = 4` | `{ds-radius-small}` 4px |
| `Radius/RadiusXs = 2` | `{ds-radius-extra-small}` 2px |
| `Radius/S = 4` | `{ds-radius-small}` 4px（與 RadiusSm 相同值） |
| `FontSizeSf/TextSm = 12` | `{font-size-sf-text-sm}` |
| `FontSizeSf/TextMd = 14` | `{font-size-sf-text-md}` |
| `FontWeightSf/Normal = 400` | `{font-weight-sf-normal}` |
| `FontFamilySf/Fontfamily` | `{font-family-sf-fontfamily}` |

### 🎨 量測值（非 token，指向 app.css）

- Small Color Range Area：350×454px（`get_design_context` 節點 11932:30085）
- Small Swatches：350×501px（`get_design_context` 節點 11932:30124）
- Large Color Range Area：390×506px（`get_metadata` bounding box）
- Large Swatches：390×532px（`get_metadata` bounding box）
- preview-swatch：56×56px（Small，`get_design_context` 量測）
- Input height：32px（TextBox/Small/Default/Line 容器）
- Input px：10px（`overflow-clip px-[10px]`）
- Action btn：px-12px / py-7px（無精確 token；py-7px 與 DropDownButton.md §3 同款待確認）
- Swatch tile：約 26×26px（Small，10 cols × 7 rows in 325px 寬度範圍，待精確量測）
- Hue/opacity slider thumb：圓形，約 18–20px 徑（待 get_design_context 對 slider 子節點精確量測）

### 🎨 未對應 Token（回報，未臆造）

| Figma 變數 / 用途 | 值 | 說明 |
|---|---|---|
| `Border/$border-light` | `#C4C7C5` | canvas 邊框；athena-tokens.md 無對應 token，指向 app.css |
| `Border/$flyout-border` | `#EDEBE9` | swatch grid 邊框；athena-tokens.md 無對應 token，指向 app.css |
| `Icon/$icon-color` | `#49454E` | `>` 切換圖示色；與 `{color-sf-on-surface-variant}` (#3c4a5b) 值不同，未強行對映，指向 app.css |
| `BG/$content-bg-color-alt1` | `#FFFFFF,#2877EE`（two-stop） | 設計稿 background 為白底 + primary 5% 疊層，以 `{color-sf-surface}` + `{color-sf-primary-opacity-5}` 兩層實作 |

### ⚠️ 待確認

1. **Action btn py-7px**：與 DropDownButton.md §3 相同問題（無精確 token，最近 token `{ds-space-padding-medium}` = 8px 差 1px），待 DS owner 確認。
2. **Swatch tile hover / selected 態**：Figma 節點僅顯示 default 色塊，未見 hover / selected variant；待對 Swatches 子節點取 `get_design_context` 確認 Syncfusion 原生行為是否直接採用。
3. **Input focus 態**：Figma 設計稿未展示 TextBox focus state；以 Syncfusion Filled Line 規則（primary 底線加厚）為預設，待實作驗證。
4. **`Icon/$icon-color` #49454E**：與 `{color-sf-on-surface-variant}` (#3c4a5b) 和 `{color-sf-outline}` (#7f8996) 都不完全吻合，疑為 Material3 icon 色（接近 `on-surface-variant` Light）。待 DS owner 確認是否需補 token `--color-material3-icon` 或直接以 `on-surface-variant` 視為設計近似值。
5. **Large variant 量測**：`get_design_context` 僅取了 Small 的兩個 variant；Large padding / input 尺寸待各自補查節點 11932:30250（Color Range Area Large）與 11932:30285（Swatches Large）。
6. **Swatch tile 精確尺寸**：Small 10 cols / 7 rows 在約 325px 寬度中，計算約 26×26px；需對 Tile 節點 `get_design_context` 精確量測（避免 gap 計算誤差）。
