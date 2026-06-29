---
name: Checkbox
category: 表單與輸入
tier: lite
status: ✅ 已採用
authority: ERP CLAUDE.md（Syncfusion Checkbox, @syncfusion/ej2-vue-buttons）
figma-node: JhcpyIEEzSChcEXMRJUiIm/12775-28409
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs + get_screenshot + get_metadata + get_design_context(×5 variants)；紀錄見文末「Figma 補入紀錄」
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺值一律 token-reference；對不上既有 token 時停下回報，勿臆造。
> §3–§5 已用 Figma 多工具校準（`get_variable_defs` / `get_metadata` / `get_design_context` × 5 節點 / `get_screenshot`，節點集 `12775:28409`）。

## 1. 概述　📋

多選輸入控制元件，允許使用者從一組選項中勾選零個或多個項目。支援三態（未選 / 已選 / 半選），半選（indeterminate）態用於「全選」行為中的部分選取。

**何時使用**：
- Grid / 表格列選取（批次操作前置）
- 篩選條件多選（多個可同時成立的 filter）
- 批次操作選取集合（資料列 checkbox）
- 表單內多選項目（非互斥選項群）

**何時不用**：
- 互斥單選 → `RadioButton`（`ejs-radiobutton`）
- 開關（單一布林值，立即生效）→ `Switch`（`ejs-switch`）
- 單一必填同意 → 可用 Checkbox，但注意語意（提交前 validate）

---

## 2. Anatomy　🎨

```
[ripple-touch-area]
  └─ box (16×16px)          ← 視覺框，unchecked 時透明底 + 2px border；checked/intermediate 時 primary 填底
       └─ check-mark / dash  ← checked = ✓ icon（白色）；intermediate = — icon（白色）；unchecked = 空
[gap-8px]
[label?]                     ← 可選文字，right 或 left 排列
  └─ text
  └─ info-icon?              ← 可選 circle-info 圖示
```

- **ripple-touch-area**：hover 狀態下出現圓形 ripple 圈（Material state layer，以 image asset 實作於 Figma；Syncfusion 元件以 CSS ripple 效果對應）
- **box**：實際可見框，含圓角 `{ds-radius-extra-small}`（2px）
- **check-mark**：checked 態，白色勾形 icon，佔 box 內 14×14px 區域（Medium）
- **dash（indeterminate mark）**：intermediate 態，白色橫槓 icon，佔 12×12px 區域（Medium）
- **label**：可選；省略時為 icon-only checkbox；Text Position = Right（預設）或 Left

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# 🎨 get_design_context 量測，Size=Medium（最常用尺寸）
box_size:
  xs:     14px × 14px   # 🎨 Extra Small（含容器 14×16px，box 14×14px）
  small:  14px × 14px   # 🎨 Small（含容器 14×16px，box 14×14px）
  medium: 16px × 16px   # 🎨 Medium（含容器 16×18px，box 16×16px）
  large:  18px × 18px   # 🎨 Large（含容器 18×18px，box 18×18px）

radius:  "{ds-radius-extra-small}"     # 🎨🔗 Figma Radius/RadiusXs = 2px

# box border（unchecked 態）
border_width:  "{ds-borderwidth-medium}"   # 🎨🔗 Figma border-2 = 2px
border_color:  "{color-sf-on-surface-variant}"  # 🎨🔗 #3c4a5b

# label
label_font:    "{font-size-sf-text-md} / {font-weight-sf-normal}"  # 🔗 14px / 400
label_color:   "{color-sf-on-surface}"                              # 🎨🔗 #0f172a
label_gap:     8px                                                  # 🎨 量測值，待對齊 token（box ↔ label 間距）

# check / dash mark
mark_color:    "{color-sf-on-primary}"     # 🔗 white；mark 為 SVG icon，以 on-primary 色填色
```

---

## 4. Variants　🎨🔗

> Figma 元件集（節點 `12775:28409`）含 4 個 variant 軸：
> `Size`（Extra Small / Small / Medium / Large）× `Type`（Unchecked / Checked / Intermediate）× `State`（Default / Hover / Focus / Active / Disabled）× `Text Position`（Right / Left）

```yaml
unchecked:                               # 🎨 Figma Type=Unchecked
  box_bg:     "{color-sf-transparent}"   # 🎨🔗 透明底（rgba(255,255,255,0)）
  box_border: "{color-sf-on-surface-variant}"  # 🎨🔗 #3c4a5b，2px solid
  mark:       none

checked:                                 # 🎨 Figma Type=Checked
  box_bg:     "{color-sf-primary}"       # 🎨🔗 #2877ee
  box_border: none                       # 🎨 border-0（填底時不顯示邊框）
  mark:       check_icon                 # 🎨 白色勾形，{color-sf-on-primary}

intermediate:                            # 🎨 Figma Type=Intermediate（半選）
  box_bg:     "{color-sf-primary}"       # 🎨🔗 同 checked
  box_border: none                       # 🎨 border-0
  mark:       dash_icon                  # 🎨 白色橫槓，{color-sf-on-primary}

# label_position（獨立軸，不影響色彩 token）
text_position:
  right:  label 在 box 右側（預設）
  left:   label 在 box 左側
  none:   無 label（icon-only）
```

---

## 5. States　🎨🔗

> 狀態矩陣：各 Type（unchecked / checked / intermediate）× State（default / hover / focus / active / disabled）。
> hover / focus / active 三態對 box 本身視覺相同（色彩不變），差異在 ripple 圈與 focus ring 的有無。

```yaml
# ===== Unchecked 各態 =====
unchecked_default:
  box_bg:     "{color-sf-transparent}"
  box_border: "{color-sf-on-surface-variant}"
  label_color: "{color-sf-on-surface}"

unchecked_hover:
  box_bg:     "{color-sf-transparent}"
  box_border: "{color-sf-on-surface-variant}"
  ripple:     圓形 state-layer 出現（Material ripple，半徑 ≈ box 的 150%）  # 🎨 Figma 以 image 表示
  label_color: "{color-sf-on-surface}"

unchecked_focus:
  box_bg:     "{color-sf-transparent}"
  box_border: "{color-sf-on-surface-variant}"
  focus_ring: "{ds-shadow-focus-ring1}"    # 🔗 白 1px 內環 + 黑 3px 外環（同 Button §5 focus）
  label_color: "{color-sf-on-surface}"

unchecked_active:
  # 🎨 Figma 節點顯示同 unchecked_default；active 僅短暫 pressed 態，視覺等同 default
  base: unchecked_default

unchecked_disabled:
  box_bg:     "{color-sf-transparent}"
  box_border: "{color-sf-on-surface-variant}"
  opacity:    38%   # 🎨 整體 box 套 opacity-38（參照 checked disabled 同一邏輯）
  label_color: "{color-sf-on-surface-opacity38}"  # 🎨🔗 rgba(15,23,42,0.38)

# ===== Checked 各態 =====
checked_default:
  box_bg:     "{color-sf-primary}"
  box_border: none
  mark:       check_icon（{color-sf-on-primary}）

checked_hover:
  base: checked_default
  ripple: 圓形 state-layer（{color-sf-primary} 上疊白色 8%，同 Button hover 邏輯）  # 📋 Syncfusion ripple

checked_focus:
  base: checked_default
  focus_ring: "{ds-shadow-focus-ring1}"   # 🔗 同 Button focus ring

checked_active:
  base: checked_default
  # 📋 Syncfusion pressed：白色 12% overlay（同 Button active）

checked_disabled:
  box_bg:     "{color-sf-on-surface}"     # 🎨🔗 #0f172a（base 色）
  box_border: none
  opacity:    38%                         # 🎨 Figma: opacity-38 套在整個 box container
  mark:       check_icon（{color-sf-on-primary}，同白）
  label_color: "{color-sf-on-surface-opacity38}"   # 🎨🔗 rgba(15,23,42,0.38)

# ===== Intermediate 各態 =====
intermediate_default:
  box_bg:     "{color-sf-primary}"
  box_border: none
  mark:       dash_icon（{color-sf-on-primary}）

intermediate_hover:
  base: intermediate_default
  ripple: 同 checked_hover

intermediate_focus:
  base: intermediate_default
  focus_ring: "{ds-shadow-focus-ring1}"

intermediate_active:
  base: intermediate_default

intermediate_disabled:
  # 🎨 Figma 同 checked_disabled 結構，以 on-surface base + opacity-38 表達
  box_bg:     "{color-sf-on-surface}"
  opacity:    38%
  mark:       dash_icon（{color-sf-on-primary}）
  label_color: "{color-sf-on-surface-opacity38}"
```

---

## 9. a11y　📋

- `aria-checked`：`"false"`（unchecked）/ `"true"`（checked）/ `"mixed"`（intermediate）
- **indeterminate 全選**：當清單為部分選取時，設 `indeterminate` prop（`<ejs-checkbox :indeterminate="true">`）渲染 intermediate 視覺；`aria-checked="mixed"` 由 Syncfusion 自動注入
- 鍵盤：`Space` 切換選取；`Tab` / `Shift+Tab` 在 checkbox 群間移動
- Focus ring：`{ds-shadow-focus-ring1}`（白 1px 內環 + 黑 3px 外環），與 Button 一致
- 禁用態：`disabled` prop（不用 `readonly`——checkbox disabled 表示不可互動）；`aria-disabled="true"`
- Mobile min touch target：44×44px（box 本身小，需確保 wrapper 有足夠點擊面積）
- Label 文字對比：`{color-sf-on-surface}`（#0f172a on white = > 16:1，WCAG AAA）
- Disabled label：`{color-sf-on-surface-opacity38}`（38% on white ≈ 3.0:1，低於 AA 4.5:1 屬 WCAG intentional disabled 豁免）

---

## 12. API / Props　📋

> Syncfusion `<ejs-checkbox>`（`@syncfusion/ej2-vue-buttons`）

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `label` | string | `''` | 顯示文字；不傳則無 label（icon-only） |
| `checked` | boolean | `false` | 是否勾選 |
| `indeterminate` | boolean | `false` | 半選態（部分選取，全選 checkbox 用） |
| `disabled` | boolean | `false` | 停用 |
| `name` | string | — | 表單欄位名稱 |
| `value` | string | — | 表單提交值 |
| `labelPosition` | `'Before'` / `'After'` | `'After'` | label 在 box 前（Left）或後（Right） |
| `change` | `(args: ChangeEventArgs) => void` | — | 值變更事件 |

```vue
<!-- 基本用法 -->
<ejs-checkbox label="全選" v-model:checked="allSelected" />

<!-- Indeterminate（部分選取）— Grid 全選 checkbox 慣用 -->
<ejs-checkbox
  label="全選"
  :checked="allSelected"
  :indeterminate="isPartialSelected"
  @change="onToggleAll"
/>

<!-- Disabled -->
<ejs-checkbox label="不可選" :checked="true" :disabled="true" />
```

---

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §On-* 前景文字色 / §Outline / §Border Width / §Radius / §Typography
- 語意對照：`../athena-design.md` §主色與動作
- Focus ring 來源：`../components/Button.md §5 States focus`（`{ds-shadow-focus-ring1}` 同根）
- Prototype 落地：ERP List View `<input type="checkbox">` 於 Grid 列頭（全選）與各列（單選）
- Code：`@syncfusion/ej2-vue-buttons`（`CheckBoxComponent`，import as `<ejs-checkbox>`）
- Figma 元件集：[FAI2 Library · CheckBox](https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/?node-id=12775-28409)

---

## Figma 補入紀錄（2026-06-29）

> 來源：`get_variable_defs`（節點 `12775:28409`）+ `get_screenshot`（同節點，396×1300px 全集預覽）+ `get_metadata`（同節點，完整 variant 結構樹）+ `get_design_context`（× 5 節點）：
> - `12775:28680`（Medium / Checked / Default / Right）→ checked 態色彩、check mark、label
> - `12775:28674`（Medium / Unchecked / Default / Right）→ unchecked 態色彩、border
> - `12775:28688`（Medium / Intermediate / Default / Right）→ intermediate 態、dash mark
> - `12775:28768`（Medium / Checked / Disabled / Right）→ disabled 態色彩、opacity-38
> - `12775:28724`（Medium / Checked / Focus / Right）→ focus 態視覺（無額外 CSS，ring 由 shadow 控制）
> - `12775:28696`（Medium / Unchecked / Hover / Right）→ hover ripple 以 image 表示

**✅ 已確認 token 對映（Figma 變數 → `athena-tokens.md`）**：
- `ColorSf/primary` → `{color-sf-primary}`（#2877ee）
- `ColorSf/on-surface-variant` → `{color-sf-on-surface-variant}`（#3c4a5b，unchecked border）
- `ColorSf/transparent` → `{color-sf-transparent}`（unchecked bg）
- `ColorSf/on-surface` → `{color-sf-on-surface}`（label 文字 / disabled box base）
- `ColorSf/on-surface-opacity38` → `{color-sf-on-surface-opacity38}`（disabled label + disabled box overlay）
- `ColorSf/on-primary` → `{color-sf-on-primary}`（check mark / dash mark 白色）
- `Radius/RadiusXs` → `{ds-radius-extra-small}`（2px，border-radius）
- `FontSizeSf/TextMd` → `{font-size-sf-text-md}`（14px）
- `FontWeightSf/Normal` → `{font-weight-sf-normal}`（400）

**✅ Variant 結構確認（get_metadata）**：
4 軸：Size（XS / Small / Medium / Large）× Type（Unchecked / Checked / Intermediate）× State（Default / Hover / Focus / Active / Disabled）× Text Position（Right / Left）。
Box 尺寸按 Size：14px（XS/Small）/ 16px（Medium）/ 18px（Large）；容器高度一律比 box 多 2px（ripple 預留空間）。

**✅ Indeterminate 確認**：
Figma Type=`Intermediate`，Syncfusion prop 為 `indeterminate`；`aria-checked="mixed"` 由 Syncfusion 注入。視覺上 box bg 同 checked（`{color-sf-primary}`），mark 為橫槓（dash icon）。

**⚠️ Token 缺口（1 項）**：
- **label_gap（8px）**：box 與 label 之間 gap 量測為 8px，對應 `--ds-space-margin-medium`（Athena_Default = 8px）或 `--ds-space-padding-small`（Athena_Default = 4px）— 前者語意較近。但 `athena-tokens.md` margin / padding token 的語意區分不明確於此情境，暫標 🎨 量測值（8px），待 DS owner 確認是否對齊 `{ds-space-margin-medium}`。現行 Syncfusion 以 `gap-[8px]` 控制。

**✅ hover ripple 說明**：
Figma hover 態以 image asset（圓形 ripple 圖）表示 Material state layer。Syncfusion `ejs-checkbox` 有內建 ripple 效果（`enableRtl` 無關），落地時不需另加樣式；設計文件標記為 Syncfusion 原生行為。

**✅ focus 態補充**：
Figma focus 態 checked / intermediate 節點無額外 CSS（box 同 default），focus ring 由 `{ds-shadow-focus-ring1}` 套在 wrapper 上控制（與 Button focus 機制一致，見 `Button.md §Figma 補入紀錄 2026-06-18`）。

**本次無需新增 token**：所有確認值均能在現有 `athena-tokens.md` 找到對應，label_gap 缺口已標 🎨 待確認，未在設計文件就地造值。
