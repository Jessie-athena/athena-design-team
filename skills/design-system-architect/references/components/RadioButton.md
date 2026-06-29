---
name: RadioButton
category: 表單輸入 — 選擇控制元件
tier: lite
status: ✅ 已採用
authority: ERP CLAUDE.md（Syncfusion RadioButton, @syncfusion/ej2-vue-buttons）
figma-node: JhcpyIEEzSChcEXMRJUiIm/13185-104862
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs + get_screenshot + get_metadata + get_design_context（多 variant）；紀錄見文末「Figma 補入紀錄」
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺值一律 token-reference；對不上既有 token 時停下回報，勿臆造。
> §3–§5 已用 Figma 多工具校準（`get_variable_defs` / `get_metadata` / `get_design_context` × 6 variant / `get_screenshot`，節點集 `13185:104862`）。遺留待確認項見文末紀錄。

## 1. 概述　📋

單選選項控制元件。在一組互斥選項中選取恰好一個。

**何時使用**：
- 一組選項須**互斥單選**（付款方式、交貨方式、稅別類型、列印格式等）。
- 選項數量少（2–5 個），空間允許並排顯示時；選項 ≥ 6 或空間受限 → `DropDownList`。

**何時不用**：
- 可複選 → `Checkbox`。
- 選項多 / 空間不足 / 需搜尋 → `DropDownList`。
- 開關型（啟用 / 停用）→ `Checkbox`（單獨使用）。

---

## 2. Anatomy　🎨

```
[ripple-layer?]  [outer-ring]  [inner-dot?]   [label]   [helper-icon?]
      ↑               ↑              ↑            ↑
  36×36px        16–18px圓        8px圓       14px / 400
  hover/focus     透明bg        selected 才有
  state-layer
```

| 子元素 | 說明 |
|---|---|
| **outer-ring** | 圓形邊框，始終可見；unselected = 邊框色；selected = primary 邊框 + primary 填滿 inner-dot |
| **inner-dot** | 位於 outer-ring 中央；僅 `Checked=ON` 時渲染，佔 outer-ring 一半尺寸（8/16px = 50%） |
| **ripple-layer** | 36×36px 絕對定位圓層，hover / focus / active 狀態出現，中心對齊 outer-ring；傳遞 state-layer 視覺效果 |
| **label** | 可選；預設 Text Position=Right；亦支援 Left（label 在 circle 左側） |
| **helper-icon** | 可選 info icon（14px），緊跟 label 右側；提供額外說明 tooltip |
| **helper-text** | 可選次行文字，12px，`{color-sf-on-surface-variant}` |

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# 🎨 Medium 尺寸（預設）；get_design_context 節點 13185:105007 / 13185:105013 量測

# --- 圓形控制元件（outer-ring） ---
outer-ring:
  size_xs: 14px                              # 🎨 Extra Small（量測值，待對齊 token）
  size_sm: 14px                              # 🎨 Small（量測值，待對齊 token）
  size_md: 16px                              # 🎨 Medium（量測值，待對齊 token）
  size_lg: 18px                              # 🎨 Large（量測值，待對齊 token）
  radius: "{ds-radius-10extra-large}"        # 🔗 1000px（全圓）
  border_width_unselected: "{ds-borderwidth-medium}"   # 🔗 2px（Figma border-2 = 2px = ds-borderwidth-medium）
  border_width_selected: 1.5px              # 🎨 ⚠️ token gap：Figma 實測 1.5px；athena-tokens.md 無 1.5px token（只有 1px / 2px）；待 DS owner 補 token 或裁示用 {ds-borderwidth-small}(1px) 代替
  border_color_unselected: "{color-sf-on-surface-variant}"   # 🔗 rgb(60 74 91)
  border_color_selected: "{color-sf-primary}"                # 🔗 rgb(40 119 238)
  bg: "{color-sf-transparent}"              # 🔗 rgba(255,255,255,0)（始終透明）

# --- 內點（inner-dot，僅 Checked=ON） ---
inner-dot:
  size_md: 8px                              # 🎨 Medium = outer-ring 的 50%（量測值，待對齊 token）
  offset_md: 4px                            # 🎨 from outer-ring 邊緣（量測值，待對齊 token）
  radius: "{ds-radius-10extra-large}"       # 🔗 1000px（全圓）
  bg_selected: "{color-sf-primary}"         # 🔗 rgb(40 119 238)

# --- Ripple / State-layer 圓 ---
ripple:
  size: 36px                                # 🎨 量測值（hover/focus/active 時，36×36px 絕對定位；待對齊 token）
  offset_x: -10px                           # 🎨 相對 outer-ring 左緣（量測值）
  offset_y: -9px                            # 🎨 相對 outer-ring 上緣（量測值）
  # 顏色由 state-layer 決定，見 §5

# --- 元件列高（outer-ring + 垂直溢出） ---
container_height:
  xs: 16px                                  # 🎨
  sm: 16px–18px                             # 🎨（variant 間略有差異）
  md: 18px                                  # 🎨
  lg: 18px                                  # 🎨

# --- 間距 ---
gap_ring_to_label: "{ds-space-margin-medium}"   # 🔗 8px（Figma gap-8；unselected state）
# selected state gap 為 10px → 同 {ds-space-margin-medium} 在 Comfortable 密度或差異在 inner-dot 推擠？
# ⚠️ token gap：unselected gap=8px=medium, selected gap=10px，無對應單一 token；待 DS owner 裁示

# --- 標籤文字 ---
label:
  font_size: "{font-size-sf-text-md}"            # 🔗 14px
  font_weight: "{font-weight-sf-normal}"         # 🔗 400
  line_height: 1.3                               # 🎨
  color_default: "{color-sf-on-surface}"         # 🔗 rgb(15 23 42)
  color_disabled: "{color-sf-on-surface-opacity38}"  # 🔗 rgba(15,23,42,0.38)

# --- 輔助文字（helper text，第二行） ---
helper_text:
  font_size: "{font-size-sf-text-sm}"            # 🔗 12px
  font_weight: "{font-weight-sf-normal}"         # 🔗 400
  letter_spacing: 0.1px                          # 🎨
  color: "{color-sf-on-surface-variant}"         # 🔗 rgb(60 74 91)
  color_disabled: "{color-sf-on-surface-opacity38}"  # 🔗 rgba(15,23,42,0.38)
```

---

## 4. Variants　🎨🔗

Figma 元件集（節點 `13185:104862`）含以下 variant 軸：

| 軸 | 值 |
|---|---|
| **Size** | Extra Small / Small / Medium / Large |
| **Checked** | OFF / ON |
| **State** | Default / Hover / Focus / Active / Disabled |
| **Text Position** | Right / Left |

```yaml
# --- Size 軸（outer-ring 尺寸） ---
extra-small: { outer_ring: 14px, inner_dot: 7px }   # 🎨
small:        { outer_ring: 14px, inner_dot: 7px }   # 🎨（與 XS 同）
medium:       { outer_ring: 16px, inner_dot: 8px }   # 🎨（預設）
large:        { outer_ring: 18px, inner_dot: 9px }   # 🎨

# --- Text Position 軸 ---
right: # label 在 circle 右側（預設，ERP 慣用）
left:  # label 在 circle 左側（特殊佈局）
```

---

## 5. States　🎨🔗

> Hover / Focus / Active 的視覺差異由 ripple-layer（36×36px 圓，絕對定位）表達。
> Figma 將 hover/focus 的 ripple 以圖片資產儲存（state-layer overlay），未直接暴露 token；以下依 Figma 渲染值對映。

```yaml
# ==== Checked=OFF ====

unselected_default:
  outer_ring_border: "{color-sf-on-surface-variant}"    # 🔗 rgb(60 74 91)
  outer_ring_border_width: "{ds-borderwidth-medium}"    # 🔗 2px
  outer_ring_bg: "{color-sf-transparent}"               # 🔗 透明
  ripple: none

unselected_hover:
  outer_ring_border: "{color-sf-on-surface-variant}"    # 🔗（不變）
  ripple: visible                                        # 🎨 36×36px state-layer圓；顏色待 DS 補 token
  # ⚠️ token gap：ripple 顏色 Figma 以圖片資產呈現，未直接顯示 token；推測為 {color-sf-on-surface-opacity8}（同族 Button 規律），待補確認

unselected_focus:
  outer_ring_border: "{color-sf-on-surface-variant}"    # 🔗（不變）
  focus_ring: "{ds-shadow-focus-ring1}"                  # 🔗 白 1px 內環 + 黑 3px 外環（同 Button §5 focus）
  ripple: visible                                        # 🎨 同 hover 尺寸

unselected_active:
  outer_ring_border: "{color-sf-on-surface-variant}"    # 🔗（不變）
  ripple: visible                                        # 🎨 active 態（稍深 alpha），待補

unselected_disabled:
  outer_ring_border: "{color-sf-on-surface-opacity38}"  # 🔗 rgba(15,23,42,0.38)
  outer_ring_border_width: "{ds-borderwidth-medium}"    # 🔗 2px
  outer_ring_bg: "{color-sf-transparent}"               # 🔗 透明
  label_color: "{color-sf-on-surface-opacity38}"        # 🔗 rgba(15,23,42,0.38)
  ripple: none

# ==== Checked=ON ====

selected_default:
  outer_ring_border: "{color-sf-primary}"               # 🔗 rgb(40 119 238)
  outer_ring_border_width: 1.5px                        # 🎨 ⚠️ token gap（見 §3）
  inner_dot_bg: "{color-sf-primary}"                    # 🔗 rgb(40 119 238)
  ripple: none

selected_hover:
  outer_ring_border: "{color-sf-primary}"               # 🔗（不變）
  inner_dot_bg: "{color-sf-primary}"                    # 🔗（不變）
  ripple: visible                                        # 🎨 primary 色 state-layer；推測 {color-sf-primary-opacity-8}（同 Button hover），待補確認

selected_focus:
  outer_ring_border: "{color-sf-primary}"               # 🔗（不變）
  inner_dot_bg: "{color-sf-primary}"                    # 🔗（不變）
  focus_ring: "{ds-shadow-focus-ring1}"                  # 🔗 白 1px 內環 + 黑 3px 外環（同 Button §5 focus）
  ripple: visible

selected_active:
  outer_ring_border: "{color-sf-primary}"               # 🔗（不變）
  inner_dot_bg: "{color-sf-primary}"                    # 🔗（不變）
  ripple: visible                                        # 🎨 active（稍深）

selected_disabled:
  outer_ring_border: "{color-sf-on-surface-opacity38}"  # 🔗 rgba(15,23,42,0.38)（同 unselected disabled；primary 色褪去）
  outer_ring_border_width: 1.5px                        # 🎨 ⚠️ token gap（同 §3）
  inner_dot_bg: "{color-sf-on-surface-opacity38}"       # 🔗 rgba(15,23,42,0.38)
  label_color: "{color-sf-on-surface-opacity38}"        # 🔗 rgba(15,23,42,0.38)
  ripple: none
```

---

## 9. a11y　📋

- **ARIA**：每個 radio 元素需 `role="radio"` + `aria-checked="true|false"`；整個選項群組需 `role="radiogroup"` + `aria-labelledby`（指向群組標題）。
- **同群組 `name` 相同**：原生 `<input type="radio">` 或 `ejs-radiobutton` 須設定相同 `name` 屬性，確保互斥語意與鍵盤導覽正確。
- **鍵盤導覽**：
  - `Tab` 進入群組（聚焦到選中項，或第一個）。
  - `←` / `→`（或 `↑` / `↓`）在同群組內切換，並自動選取。
  - `Tab` 離開群組（移至下一個 focusable element）。
  - **不需 Space 鍵選取**（與 Checkbox 不同；方向鍵即選取）。
- **Focus ring**：`{ds-shadow-focus-ring1}`（白 1px 內環 + 黑 3px 外環），與 Button 相同。
- **Contrast**：unselected ring `{color-sf-on-surface-variant}` (#3c4a5b) 對 white 約 7.7:1（AA+）；selected + disabled `{color-sf-on-surface-opacity38}` 對 white ≈ 2.5:1（低於 AA，但 Figma 設計如此，為 disabled 慣例）。
- **Touch target**：min 44×44px（含 ripple 區域；ripple 36px 加上觸控 margin 補足）。
- **Label 關聯**：`ejs-radiobutton` 的 `label` prop 自動處理 `<label>` 關聯；自定義排版需手動 `<label for="...">` 或 `aria-label`。

---

## 12. API / Props　📋

`@syncfusion/ej2-vue-buttons` → `<ejs-radiobutton>`

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `label` | string | — | 標籤文字（顯示於 circle 右側或左側） |
| `name` | string | — | **必填**；同群組所有 radio 須設定相同 name |
| `value` | string | — | 綁定值（v-model 配對用） |
| `checked` | boolean | false | 是否選取 |
| `disabled` | boolean | false | 停用 |
| `labelPosition` | Before / After | After | Before = label 在左（Text Position=Left）；After = label 在右（預設） |
| `cssClass` | string | — | 自定義 class |
| `enableRtl` | boolean | false | RTL 方向（國際化） |
| `change` | EventEmitter | — | 選取變更事件，payload `{ value, event }` |

**Vue 3 使用範例（ERP 情境：付款方式）**：

```vue
<script setup>
const paymentMethod = ref('transfer')
</script>

<template>
  <div role="radiogroup" aria-labelledby="payment-label">
    <span id="payment-label">付款方式</span>
    <ejs-radiobutton
      label="銀行轉帳"
      name="paymentMethod"
      value="transfer"
      v-model:checked="paymentMethod"
    />
    <ejs-radiobutton
      label="現金"
      name="paymentMethod"
      value="cash"
      v-model:checked="paymentMethod"
    />
    <ejs-radiobutton
      label="支票"
      name="paymentMethod"
      value="check"
      v-model:checked="paymentMethod"
    />
  </div>
</template>
```

---

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §On-* 前景文字色 / §Outline / §Border Width / §Radius / §Shadow
- 語意對照：`../athena-design.md`（若存在）§主色與動作 / §互動態疊層
- 同族對照：`components/Button.md`（focus ring / disabled 配色規律一致）
- Checkbox 設計文件：尚未產出（`components/Checkbox.md` 不存在）；本文件已盡量標注與 Checkbox 預期共享的模式
- Prototype 落地：`prototyper/profiles/erp-components/`（待補）
- Figma 元件集：`https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/?node-id=13185-104862`
- Code：`@syncfusion/ej2-vue-buttons`（`<ejs-radiobutton>`）

---

## Figma 補入紀錄（2026-06-29）

> 來源：`get_variable_defs`（節點 `13185:104862`）+ `get_screenshot`（同節點，310×1065px）+ `get_metadata`（同節點，完整 variant 結構樹）+ `get_design_context`（6 個 variant 節點：Medium OFF/ON × Default / Hover / Focus / Disabled）。

**元件集結構（get_metadata 確認）**：
- 4 個 variant 軸：Size（XS / Sm / Md / Lg）× Checked（OFF / ON）× State（Default / Hover / Focus / Active / Disabled）× Text Position（Right / Left）
- 總計：4 × 2 × 5 × 2 = 80 個 symbol

**Figma 變數對映（get_variable_defs 回傳）**：
- `ColorSf/primary` → `{color-sf-primary}` rgb(40 119 238)
- `ColorSf/on-surface-opacity38` → `{color-sf-on-surface-opacity38}` rgba(15,23,42,0.38)
- `ColorSf/on-surface-variant` → `{color-sf-on-surface-variant}` rgb(60 74 91)
- `ColorSf/on-surface` → `{color-sf-on-surface}` rgb(15 23 42)
- `ColorSf/transparent` → `{color-sf-transparent}` rgba(255,255,255,0)
- `Radius/Radius10Xl` → `{ds-radius-10extra-large}` 1000px
- `FontSizeSf/TextMd` → `{font-size-sf-text-md}` 14px
- `FontSizeSf/TextSm` → `{font-size-sf-text-sm}` 12px
- `FontWeightSf/Normal` → `{font-weight-sf-normal}` 400

**✅ 已確認對映**：primary / on-surface-variant / on-surface / on-surface-opacity38 / transparent / Radius10Xl / FontSize / FontWeight — 全部可在 `athena-tokens.md` 找到對應。

**⚠️ Token gap（2 項，需 DS owner 裁示）**：

1. **Selected border-width 1.5px**：`get_design_context`（節點 `13185:105013` Checked=ON Default，及 `13185:105061` Checked=ON Disabled）均顯示 `border-[1.5px]`。`athena-tokens.md §Border Width` 僅有 `{ds-borderwidth-small}` 1px 和 `{ds-borderwidth-medium}` 2px，無 1.5px。設計文件暫標 `1.5px 🎨 ⚠️ token gap`，建議 DS owner 補 `--ds-borderwidth-1.5px` 或裁示用既有 token 代替。與 Checkbox 相同問題預期存在（Checkbox 設計文件尚未產出，待比對）。

2. **Ripple state-layer 顏色**：hover / focus / active 的 ripple（36×36px 圓）在 Figma 以圖片資產呈現（`$transparent` layer），未直接暴露顏色 token。Unselected ripple 推測為 `{color-sf-on-surface-opacity8}`（on-surface 8%，符合 Material state layer 規律）；Selected ripple 推測為 `{color-sf-primary-opacity-8}`（primary 8%，與 Button hover 疊層同族）。兩者均為推測，需 DS owner 以 Figma 變數層確認。本文件 §5 標 `待補確認`，不寫死推測值。

**與 Checkbox 預期差異（待 Checkbox 設計文件產出後補全比對）**：
- Radio 外框為**圓形**（`{ds-radius-10extra-large}` 1000px）；Checkbox 外框預期為**方形圓角**（推測較小 radius）。
- Radio inner-dot（ON 時）= 小圓實心；Checkbox checkmark = icon/SVG。
- Radio selected border = 1.5px（token gap）；Checkbox selected border 預期亦為 1.5px（同源 DS）。
- Unselected border 兩者均為 2px `{ds-borderwidth-medium}`（依本文件推論，待 Checkbox 文件驗證）。
- Disabled 配色兩者均使用 `{color-sf-on-surface-opacity38}`，規律一致。

**本次無新增 token**：已知 gap 項均標為待裁示，未在設計文件就地造 token。
