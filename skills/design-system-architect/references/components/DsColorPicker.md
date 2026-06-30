---
name: DsColorPicker（客製色票選擇器）
category: 資料輸入 — 色彩選擇
tier: lite
status: ✅ 已產出
authority: ERP CLAUDE.md（客製 Ds 元件，components/ds/DsColorPicker.vue）
figma-node: JhcpyIEEzSChcEXMRJUiIm/27007-37364
version: v0.1
last-synced: 2026-06-29   # 🎨 get_metadata + get_variable_defs + get_design_context（light + dark variant）+ get_screenshot；紀錄見文末「Figma 補入紀錄」
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺值一律 token-reference；對不上既有 token 時標 🎨 並停下回報。
> §3–§5 已用 Figma 多工具校準（`get_metadata` / `get_variable_defs` / `get_design_context` × 2 variant / `get_screenshot`，節點集 `27007:37364`）。

---

## 1. 概述　📋

固定色盤的色票選擇器，以下拉浮層（flyout）形式呈現。使用者從 14 個預設顏色中點選一個，用於標記資料列、事件類別或標籤等需要視覺辨識的場景。

**何時使用**：
- 需要替記錄或標籤指定一個**品牌固定色**（非任意 hex 輸入），例如：行事曆事件顏色、銷售類別色、備注分類。
- 選色操作需要快速（一眼辨識，一鍵選取），不需 Hue/Saturation/Brightness 精細調整。

**何時不用**：
- 需任意輸入色值（hex / RGB / HSL）→ 改用全功能 color picker（目前 ERP 未導入）。
- 色彩非使用者可選配置（固定由系統決定）→ 直接用 `st-chip` 或對應語意色，不暴露選色器。
- 選項數量需要動態擴充 → 超出 14 色固定色盤範圍，需重新設計。

---

## 2. Anatomy　🎨

```
[flyout-container]
  └─ [color-item × 14]  （「下拉項目」）
       └─ [circle × 1]    （24×24px 色票圓）
            └─ [Vector]    （填色 SVG，含 2px inset）
```

| 子元素 | 說明 |
|---|---|
| **flyout-container** | 浮層容器；帶陰影、圓角、背景色；`flex-wrap` 排列，最大寬 344px |
| **color-item**（下拉項目） | 每個可點擊色票的互動包裝層；padding + radius 提供 hover/focus 回饋空間 |
| **circle** | 24×24px 色票視覺圓；內部 `Vector` 為填色形狀（8.33% inset = 約 2px 內縮） |

**色盤固定色（14 色，2 排）**：

| 排序 | 顏色名稱 | Token |
|---|---|---|
| 深 1 | Purple（紫） | `{ds-color-purple}` |
| 深 2 | Blue（藍） | `{ds-color-blue}` |
| 深 3 | Green（綠） | `{ds-color-green}` |
| 深 4 | Yellow（黃） | `{ds-color-yellow}` |
| 深 5 | Orange（橙） | `{ds-color-orange}` |
| 深 6 | Red（紅） | `{ds-color-red}` |
| 深 7 | Neutral Dark（深灰） | `{ds-color-neutral-dark}` |
| 淺 1 | Lavender（薰衣草） | `{ds-color-lavender}` |
| 淺 2 | Sky Blue（天藍） | `{ds-color-sky-blue}` |
| 淺 3 | Mint（薄荷） | `{ds-color-mint}` |
| 淺 4 | Cream（奶油） | `{ds-color-cream}` |
| 淺 5 | Peach（蜜桃） | `{ds-color-peach}` |
| 淺 6 | Pink（粉） | `{ds-color-pink}` |
| 淺 7 | Neutral Light（淺灰） | `{ds-color-neutral-light}` |

> 色盤顏色固定，不支援動態增減。若未來需自訂色盤，屬 breaking change，須更新本文件與元件 API。

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# 🎨 get_design_context 節點 27007:37146（light）/ 27007:37349（dark）量測

# --- flyout 容器 ---
container:
  width: 344px                                   # 🎨 量測值，待對齊 token
  padding: "{ds-space-padding-medium}"           # 🔗 8px（Athena_Default）
  gap: "{ds-space-margin-medium}"                # 🔗 8px（Athena_Default）
  radius: "{ds-radius-large}"                    # 🔗 8px
  shadow: "{ds-shadow-sm}"                       # 🔗 0px 1px 2px rgba(0,0,0,.3), 0px 1px 3px 1px rgba(0,0,0,.15)
  border: "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗 1px / #d7dae0（Border/$flyout-border 對映）
  # state=light:  bg = "{color-sf-white}"        # 🔗 #ffffff
  # state=dark:   bg = "{color-sf-inverse-surface}"  # 🔗 #303034

# --- color-item（下拉項目） ---
color-item:
  padding: "{ds-space-padding-medium}"           # 🔗 8px（Athena_Default）
  gap: "{ds-space-margin-medium}"                # 🔗 8px（Athena_Default）
  radius: "{ds-radius-small}"                    # 🔗 4px
  # hover/focus bg 見 §5

# --- circle（色票圓） ---
circle:
  size: 24px                                     # 🎨 量測值，待對齊 token
  inset: 8.33%                                   # 🎨 ≈ 2px（Vector 相對容器內縮，量測值）
  radius: "{ds-radius-10extra-large}"            # 🔗 1000px（全圓）
  # 填色由色盤固定色 token 決定，見 §2 色盤表
```

---

## 4. Variants　🎨🔗

| 軸 | 值 | 說明 |
|---|---|---|
| **state** | `light` / `dark` | 浮層背景色切換；深色模式下色票圓本體不變，僅容器背景改為 inverse surface |

```yaml
light:
  container_bg: "{color-sf-white}"               # 🔗 #ffffff
  # 用途：亮色主題頁面（ERP 預設）

dark:
  container_bg: "{color-sf-inverse-surface}"     # 🔗 #303034
  # 用途：深色背景疊加時（如深色 tooltip/popover 場景）
```

> ⚠️ 目前 Figma 僅設計兩個 variant（`state=light` / `state=dark`）。ERP 原型預設使用 `light`；`dark` 備用於日後深色模式或特殊浮層場景。

---

## 5. States　🎨🔗

> color-item 的互動 state 僅涉及 hover / focus / active / selected；色票圓本身不變色。
> Figma 當前版本（27007:37364）僅定義靜態外觀，未設計 state-layer variant；以下依 Material state layer 規律推論並標記待確認。

```yaml
color-item:
  default:
    bg: transparent
    # 無 border

  hover:
    bg: "{color-sf-on-surface-opacity8}"          # 🔗 rgba(15,23,42,0.08)（推測，同 Button hover；待 DS 補入 Figma 確認）
    # ⚠️ token gap：Figma 本節點未設計 hover state；推測值，待 DS owner 確認

  focus:
    bg: "{color-sf-on-surface-opacity8}"          # 🔗（同 hover）
    outline: "{ds-shadow-focus-ring1}"             # 🔗 黑 3px + 白 1px 同心環（同 Button focus ring）
    # ⚠️ 同上，待確認

  active:
    bg: "{color-sf-on-surface-opacity12}"         # 🔗 rgba(15,23,42,0.12)（推測，同 Button active；待確認）

  selected:
    # 選中的色票圓：外加選取指示（設計待補）
    # ⚠️ Figma 當前版本未定義 selected 態；建議以 checkmark overlay 或 ring 指示；待 DS 定義
    checkmark_color: "{color-sf-white}"            # 🔗（推測前景色，確保對比）
    # 注意：深色票（Purple / Blue / Green…）白色 checkmark；淺色票（Lavender / Sky Blue…）可能需要深色 checkmark
    # ⚠️ token gap：淺色票 checkmark 前景未有對應 token；待 DS owner 裁示

  disabled:
    opacity: "{color-sf-on-surface-opacity38}"    # 🔗 rgba(15,23,42,0.38)（如整個選色器停用時）
    cursor: not-allowed
```

---

## 9. a11y　📋

- **ARIA**：整個 flyout 建議 `role="listbox"` + `aria-label="選擇顏色"`；每個 color-item 為 `role="option"` + `aria-label="<顏色中文名>"` + `aria-selected="true|false"`。
- **鍵盤導覽**：
  - `Tab` 進入 flyout（聚焦到第一個 color-item 或已選取項）。
  - `←` / `→` / `↑` / `↓` 在色票間移動（色盤 7 欄兩排）。
  - `Enter` / `Space` 選取當前聚焦的顏色。
  - `Esc` 關閉 flyout 不選取。
- **Focus ring**：`{ds-shadow-focus-ring1}`（白 1px 內環 + 黑 3px 外環），配合 color-item 的 `{ds-radius-small}` 圓角顯示。
- **對比度**：色票圓本身為填色視覺，無文字對比要求；但 selected 態的 checkmark 需滿足 3:1（圖形對比，WCAG 1.4.11）。淺色票與 checkmark 的對比組合待 DS 定義（見 §5 ⚠️）。
- **Touch target**：color-item（含 padding）= 40×40px（24px circle + 8px padding × 2），符合 40px 最小觸控目標建議。

---

## 11. Content　📋

- 顏色名稱供 `aria-label` 用：Purple / Blue / Green / Yellow / Orange / Red / Neutral Dark / Lavender / Sky Blue / Mint / Cream / Peach / Pink / Neutral Light。
- 中文化建議：紫 / 藍 / 綠 / 黃 / 橙 / 紅 / 深灰 / 薰衣草 / 天藍 / 薄荷 / 奶油 / 蜜桃 / 粉 / 淺灰。
- 空選狀態顯示文字：「未選擇顏色」（觸發欄位 placeholder，非 flyout 內部文字）。

---

## 12. API / Props　📋

> 元件為客製 `DsColorPicker.vue`，以下為建議 Props 設計（待實作時確認）。

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `modelValue` | string \| null | `null` | 已選色 token 名（如 `'ds-color-blue'`）或 null 表示未選取 |
| `theme` | `'light'` \| `'dark'` | `'light'` | flyout 背景主題（對應 Figma state variant） |
| `disabled` | boolean | `false` | 停用整個選色器 |

**Vue 3 使用範例（ERP 情境：銷售類別標籤色）**：

```vue
<script setup>
import DsColorPicker from '@/components/ds/DsColorPicker.vue'

const categoryColor = ref(null)
</script>

<template>
  <!-- trigger button：顯示目前選色或預設狀態 -->
  <button @click="showPicker = !showPicker">
    <span
      class="inline-block w-4 h-4 rounded-full"
      :style="categoryColor ? { background: `var(--${categoryColor})` } : { background: 'var(--color-sf-outline-variant)' }"
    ></span>
    選擇顏色
  </button>

  <!-- flyout -->
  <DsColorPicker
    v-if="showPicker"
    v-model="categoryColor"
    theme="light"
    @update:modelValue="showPicker = false"
  />
</template>
```

---

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface / §Inverse / §Outline / §ds-* Athena 自有色盤 / §Shadow / §Radius / §Space
- 語意對照：`../athena-design.md`（若存在）§ds 色盤語意
- Prototype 落地：`prototyper/profiles/erp-components/`（待補）
- Figma 元件集：`https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/?node-id=27007-37364`
- Code：客製 `DsColorPicker.vue`（`components/ds/`）

---

## Figma 補入紀錄（2026-06-29）

> 來源：`get_metadata`（節點 `27007:37364`，確認元件名稱與 variant 結構）+ `get_variable_defs`（同節點，取設計變數）+ `get_design_context`（light 節點 `27007:37146` / dark 節點 `27007:37349`，量測尺寸與取得 token 引用）+ `get_screenshot`（同節點，384×269px 含雙 variant 合圖）。

**元件識別（get_metadata 確認）**：
- 節點 `27007:37364` 的 Figma frame 名稱為 `Color Picker 客製功能`。
- 含兩個 symbol：`27007:37146`（state=light）、`27007:37349`（state=dark）。
- 兩 symbol 尺寸均為 344×104px。

**Figma 變數對映（get_variable_defs 回傳）**：
| Figma 變數 | 對映 Token | 確認 |
|---|---|---|
| `ColorSf/Purple` | `{ds-color-purple}` | ✅ |
| `ColorSf/Blue` | `{ds-color-blue}` | ✅ |
| `ColorSf/Green` | `{ds-color-green}` | ✅ |
| `ColorSf/Yellow` | `{ds-color-yellow}` | ✅ |
| `ColorSf/Orange` | `{ds-color-orange}` | ✅ |
| `ColorSf/Red` | `{ds-color-red}` | ✅ |
| `ColorSf/Neutral Dark` | `{ds-color-neutral-dark}` | ✅ |
| `ColorSf/Lavender` | `{ds-color-lavender}` | ✅ |
| `ColorSf/Sky Blue` | `{ds-color-sky-blue}` | ✅ |
| `ColorSf/Mint` | `{ds-color-mint}` | ✅ |
| `ColorSf/Cream` | `{ds-color-cream}` | ✅ |
| `ColorSf/Peach` | `{ds-color-peach}` | ✅ |
| `ColorSf/Pink` | `{ds-color-pink}` | ✅ |
| `ColorSf/Neutral Light` | `{ds-color-neutral-light}` | ✅ |
| `ColorSf/white` / `BG/$white` | `{color-sf-white}` | ✅ |
| `ColorSf/inverse-surface` / `BG/$content-bg-color-inverse` | `{color-sf-inverse-surface}` | ✅ |
| `ColorSf/outline-variant` / `Border/$flyout-border` | `{color-sf-outline-variant}` | ✅ |
| `$shadow-sm` | `{ds-shadow-sm}` | ✅ |

**✅ 已確認對映**：全部 18 個 Figma 變數均可在 `athena-tokens.md` 找到對應 token，無新增 token 需求。

**⚠️ Token gap（3 項，需 DS owner 裁示）**：

1. **color-item hover/focus/active state-layer**：Figma 節點 `27007:37364` 未設計互動 state variant。依 Material state layer 規律推測 hover = `{color-sf-on-surface-opacity8}`、active = `{color-sf-on-surface-opacity12}`，與 Button 同族。需 DS owner 補充 Figma hover state 或書面確認推測值。

2. **selected 態視覺指示**：Figma 未定義選中色票的 selected 態（如 checkmark overlay 或邊框 ring）。建議 DS owner 補設計後回填 §5 selected 段落。

3. **淺色票 checkmark 前景色**：淺色排（Lavender / Sky Blue / Mint / Cream / Peach / Pink / Neutral Light）若需 checkmark 指示，白色前景對比不足（部分淺色背景與白色對比 < 3:1）；深色前景需用 `{color-sf-on-surface}` 或特定 token，待 DS owner 裁示。

**本次無新增 token**：所有 gap 項均標為待裁示，未在設計文件就地造 token。
