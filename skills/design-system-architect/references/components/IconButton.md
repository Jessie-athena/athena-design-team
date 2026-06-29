---
name: IconButton
category: 按鈕與動作
tier: lite
status: ✅ 已採用
authority: ERP prototype app.css（`.btn--icon` / `.btn-icon-sq`）；Code 端 `@syncfusion/ej2-vue-buttons`（`e-icon-btn` / `e-round`）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16782-533652
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs + get_screenshot + get_metadata + get_design_context（節點 16782:533697 Rounded、16782:533688 Full Rounded）；紀錄見文末「Figma 補入紀錄」
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺值一律 token-reference；對不上既有 token 時停下回報，勿臆造。
> §3–§5 已用 Figma 多工具校準（`get_variable_defs` / `get_metadata` / `get_design_context` × 2 / `get_screenshot`，節點 `16782-533652`）；紀錄見文末。

---

## 1. 概述　📋

空間受限場景下承載單一操作的 icon-only 按鈕。icon 意義必須明確且有 `aria-label` 輔助才可使用。

**何時使用**：
- 表格 actions 欄（查看 👁 / 編輯 ✏️ / 刪除 🗑）：icon 意義高共識，空間有限。
- 工具列次要操作（欄位設定 ⚙、匯出 ⬇）：緊鄰文字 CTA，不需重複文字標籤。
- 表單 icon 觸發（嵌入式行內，非頁面浮動）：區分於 FAB 的固定浮動定位。

**何時不用**：
- icon 語意不明確 → 改用帶文字的 `Button`（讓使用者不需猜測動作）。
- 頁面語意上的主要唯一動作（如「新增」）→ 用 Toolbar 的 `Button--primary`；長清單頁面可考慮 `FAB`。
- 需展開子選單 → `SplitButton` 或 `DropDownButton`。

**與 Button / FAB 的區別**：

| 元件 | 定位 | 尺寸 | Label |
|---|---|---|---|
| `Button` | 嵌入式，帶文字 CTA | sm/md/lg（24–40px 高） | 可見文字（必填） |
| **`IconButton`** | 嵌入式，icon-only | xs/sm/md/lg（24–36px 正方形）| `sr-only` + `aria-label` |
| `FAB` | 固定浮動（頁角） | 32–56px 圓角矩形 | icon-only 或 icon+text |

---

## 2. Anatomy　🎨

```
┌─────────────────┐
│   [container]   │   ← square (Rounded) 或 circle (Full Rounded)
│  ┌───────────┐  │
│  │  icon 16–18px │  │   ← slot: chooseIcon（預設為 Plus）
│  └───────────┘  │
└─────────────────┘
```

- **container**：正方形容器（width = height），承載 icon；background / border / shadow 由 Type × Mode 決定。
- **icon slot**（`chooseIcon`）：16px（Medium Rounded）/ 18px（Medium Full Rounded）；使用 Syncfusion icon font（`e-icons`）或 Iconify `material-symbols:` 集。
- **label（隱藏）**：不渲染可見文字；螢幕閱讀器透過 `aria-label` 辨識（見 §9）。

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# 🎨 get_design_context 節點 16782:533697（Size=Medium, Shape=Rounded, Mode=Filled, Type=Primary, State=Enabled）量測

# ——— 尺寸（正方形，width = height）———
extra_small:  24px × 24px   # 🎨 get_metadata 量測
small:        28px × 28px   # 🎨 get_metadata 量測
medium:       36px × 36px   # 🎨 get_design_context 渲染值（size-[36px]）
large:        40px × 40px   # 🎨 get_metadata 量測

# ——— Medium 詳細量測 ———
padding:  "{ds-space-padding-extra-large} {ds-space-padding-medium}"  # 🔗 px-16px / py-8px（get_design_context 渲染值；與 Button Medium 水平 padding 同值，垂直較短因無文字）
# 📋 注意：padding 不決定最終尺寸，size-[36px] 為固定尺寸（寬=高）

# ——— Shape 對應圓角 ———
radius_rounded:      "{ds-radius-small}"          # 🔗 4px（Figma Rounded，方形按鈕）
radius_full_rounded: "{ds-radius-10extra-large}"  # 🔗 1000px（Figma Full Rounded，全圓；rendered `rounded-[24px]` = 半徑=高/2，等效全圓 pill）

# ——— 邊框 ———
border_width: "{ds-borderwidth-small}"            # 🔗 1px（Outlined / Filled 皆有 border；Flat 無）

# ——— Icon 尺寸 ———
icon_medium_rounded:       16px   # 🎨 get_design_context 節點 16782:533697（size-[16px]）
icon_medium_full_rounded:  18px   # 🎨 get_design_context 節點 16782:533688（size-[18px]）
# 📋 icon 尺寸隨 Shape 略有差異；與 Button.md §3 icon 18px 相比，Rounded 縮為 16px（正方形容器更緊湊）

# ——— 字型 ———
# IconButton 不含可見文字，無字型規格
```

---

## 4. Variants　🎨🔗

> Figma 元件集（節點 16782-533652）五個 variant 軸（`get_metadata` 確認）：
> - **Size**：Extra Small / Small / Medium / Large
> - **Type**（色彩）：Primary / Secondary / Success / Danger
> - **Shape**：Rounded（方形 4px）/ Full Rounded（全圓）
> - **Mode**（外觀）：Filled / Outlined / Flat
> - **State**：Enabled / Hover / Active / Focus / Disabled

```yaml
# ——— Type 軸（色彩，以 Mode=Filled 為基準）———
# 與 Button.md §4 同族，變數命名 Primary_btn/Sec_btn/Success_btn/Danger_btn

primary:
  bg:     "{color-sf-primary}"           # 🔗 rgb(40 119 238)
  fg:     "{color-sf-on-primary}"        # 🔗 white
  border: "{color-sf-primary}"           # 🔗 同 bg（Figma Primary_btn/$primary-border-color）

secondary:
  bg:     "{color-sf-secondary-btn-bg}"  # 🔗 white
  fg:     "{color-sf-on-surface}"        # 🔗 rgb(15 23 42)
  border: "{color-sf-outline}"           # 🔗 rgb(127 137 150)
  # 📋 secondary icon 色（非文字）：{color-sf-on-surface-variant}（#3c4a5b，見 get_variable_defs Icon/$icon-color）

success:
  bg:     "{color-sf-success}"           # 🔗 rgb(18 183 106)
  fg:     "{color-sf-success-text}"      # 🔗 white
  border: "{color-sf-success}"           # 🔗 同 bg

danger:
  bg:     "{color-sf-danger}"            # 🔗 rgb(244 73 62)
  fg:     "{color-sf-danger-text}"       # 🔗 white
  border: "{color-sf-danger}"            # 🔗 同 bg

# ——— Mode 軸（外觀，以 Primary Type 為例）———
filled:
  bg:     "{color-sf-primary}"           # 🔗 實色填充
  border: "{color-sf-primary}"           # 🔗 同 bg
  fg:     "{color-sf-on-primary}"        # 🔗 white

outlined:
  bg:     "{color-sf-transparent}"       # 🔗 透明背景
  border: "{color-sf-primary}"           # 🔗 彩色邊框（width: {ds-borderwidth-small}）
  fg:     "{color-sf-primary}"           # 🔗 icon 同邊框色

flat:
  bg:     "{color-sf-transparent}"       # 🔗 透明背景
  border: none                           # 📋 無邊框
  fg:     "{color-sf-primary}"           # 🔗 icon 同 Type 色

# ——— Shape 軸（幾何）———
rounded:
  radius: "{ds-radius-small}"            # 🔗 4px 方形（ERP 表格操作主流）

full_rounded:
  radius: "{ds-radius-10extra-large}"    # 🔗 1000px 全圓（視覺更柔和，適合單獨呈現）
```

---

## 5. States　🎨🔗

> 互動態走「base 色 + white / on-surface state-layer 疊層」，與 `Button.md §5` 同族邏輯。Figma 變數 `#2877EE,#FFFFFF` 表示 base + overlay 兩段。

```yaml
# 以 Primary Filled 為例（其他 Type 以各自 bg 為 base，疊層比例相同）

enabled:
  bg:     "{color-sf-primary}"                      # 🔗

hover:
  overlay: "{color-sf-primary-btn-hover-8}"         # 🔗 white 8%（Figma Primary_btn/$primary-bg-color-hover）

active:
  overlay: "{color-sf-primary-btn-pressed-12}"      # 🔗 white 12%（pressed）

focus:
  ring:   "{ds-shadow-focus-ring1}"                 # 🔗 白 1px 內環 + 黑 3px 外環

disabled:
  bg:     "{color-sf-primary-opacity-12}"           # 🔗 primary 12% tint（同 Button.md 推導）
  fg:     "{color-sf-on-surface-opacity38}"         # 🔗 on-surface 38%（Figma Icon/$icon-color-disabled = #0F172A；opacity 層由 Primary_btn/$primary-bg-color-disabled 推算）
  border: "{color-sf-outline-variant}"              # 🔗 rgb(215 218 224)（Border/$border-disabled）
  cursor: not-allowed                               # 📋

# Secondary hover（on-surface 疊層，非 white）
secondary_hover:
  overlay: "{color-sf-on-surface-opacity8}"         # 🔗 Figma Sec_btn/$secondary-bg-color-hover = #FFFFFF,#0F172A → on-surface 8%
secondary_active:
  overlay: "{color-sf-on-surface-opacity12}"        # 🔗 on-surface 12%（pressed）
```

---

## 9. a11y　📋

- **`aria-label` 為必填，無例外**：IconButton 無可見文字，螢幕閱讀器完全依賴 `aria-label` 辨識操作意圖。
  - 正確：`aria-label="查看採購單 PO-2024-001"`、`aria-label="刪除此列"`。
  - 錯誤：省略 `aria-label`，或只寫 `aria-label="icon"`。
  - 上下文足夠明確時可簡化：`aria-label="查看"`（Grid 行內每列都相同動作時可接受）。
- **Tooltip 建議一同提供**：hover 時顯示文字提示（如 `title` attribute 或自製 tooltip），補充視力使用者的快速掃描體驗；尤其 Grid actions 欄中 3–4 個 IconButton 並排時。
- **Focus ring**：`{ds-shadow-focus-ring1}`（白 1px 內環 + 黑 3px 外環）；鍵盤 Tab 到達時必須清晰可見。
- **鍵盤觸發**：Enter · Space。
- **Minimum touch target 44×44px**：
  - Extra Small（24px）/ Small（28px）/ Medium（36px）實際點擊區域不足 44px。
  - 落地須以 padding 或透明 touch-target wrapper 補足至 44×44px（`min-width: 44px; min-height: 44px`），視覺大小不變。
  - Large（40px）仍略不足，建議補 padding 至 44px 或確認 Grid 行高已提供足夠點擊面積。
- **對比（Filled mode）**：Primary bg `{color-sf-primary}` + fg `{color-sf-on-primary}` white；請確認符合 WCAG AA（4.5:1 圖示比例需計算 icon 像素尺寸）。若不達標參見 `athena-tokens.md` 待確認的 `*-strong` 別名。
- **DOM 位置**：Grid actions 欄的 IconButton 應在對應 `<tr>` 的 DOM 末尾，確保 tab order 符合視覺順序。

---

## 12. API / Props　📋

> 以 Syncfusion `ejs-button` 為基礎（`@syncfusion/ej2-vue-buttons`）。IconButton 使用 `e-icon-btn`（方形）或 `e-round`（全圓）`cssClass` 實現，不另包裝元件。

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `iconCss` | string | — | Syncfusion icon font class（如 `e-icons e-eye`）；必填 |
| `cssClass` | string | `e-icon-btn` | 形狀：`e-icon-btn`（Rounded 方形）/ `e-round`（Full Rounded 全圓）；疊加 type：`e-primary`、`e-success`、`e-danger`；疊加 mode：`e-outline`（Outlined）/ `e-flat`（Flat）；疊加 size：`e-small`（Extra Small / Small）|
| `disabled` | boolean | false | 停用 |
| `aria-label` | string | — | **必填**，無例外；說明按鈕動作（見 §9） |
| `title` | string | — | 建議提供，顯示 hover tooltip |
| `onClick` / `@click` | EventHandler | — | 點擊回呼 |

**ERP 常用模式（Vue 3，`<script setup>`）**：

```vue
<!-- Grid actions 欄 — 查看（Rounded 方形，ghost-like secondary flat） -->
<ejs-button
  css-class="e-icon-btn e-flat"
  icon-css="e-icons e-eye"
  aria-label="查看"
  title="查看"
  @click="openForm(row.id)"
/>

<!-- Grid actions 欄 — 刪除（danger flat） -->
<ejs-button
  css-class="e-icon-btn e-flat e-danger"
  icon-css="e-icons e-delete"
  aria-label="刪除"
  title="刪除"
  @click="onDeleteRow(row.id)"
/>

<!-- 工具列設定按鈕（secondary outlined） -->
<ejs-button
  css-class="e-icon-btn e-outline"
  icon-css="e-icons e-settings"
  aria-label="欄位設定"
  title="欄位設定"
  @click="openColumnSettings"
/>

<!-- 全圓形 primary（表單嵌入式觸發） -->
<ejs-button
  css-class="e-round e-primary"
  icon-css="e-icons e-plus"
  aria-label="新增明細"
  title="新增明細"
  @click="onAddLine"
/>
```

> **Prototype（Iconify）對應**：prototype 中使用 `<iconify-icon>` 包在 `<button class="btn btn--icon">` 中（見 `prototyper/assets/app.css` `.btn--icon` class）。
> prototype 的 `e-icon-btn` / `e-round` 對應關係：
> - `e-icon-btn` → `.btn--icon`（方形）
> - `e-round` + 無 label → `.btn--icon.btn--icon-circle`（全圓，若 app.css 有定義）

---

## 13. 關聯　🔗

- **Tokens**：`../athena-tokens.md` §Primary / §Status / §Space / §Radius / §Shadow / §Border Width
- **語意對照**：`../athena-design.md` §主色與動作 / §狀態色
- **同族元件**：
  - `Button.md`：同套件、同 state-layer 邏輯；IconButton 可視為 Button 的 icon-only 特例（`e-icon-btn` / `e-round` cssClass）
  - `FAB.md`：icon-only 圓角按鈕，但固定浮動定位、尺寸更大（48–56px）、預設帶 `{ds-shadow-lg}`
- **Prototype 落地**：`prototyper`（`.btn--icon` 行內樣式，散見 Grid actions 欄、工具列）
- **Code**：`@syncfusion/ej2-vue-buttons`；playground 參考：`button.vue`（`e-icon-btn`、`e-round` 段）
- **Figma 元件集**：`https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/?node-id=16782-533652`

---

## Figma 補入紀錄（2026-06-29）

> 來源：`get_variable_defs`（節點 16782-533652）+ `get_screenshot`（同節點，1672×5268px 完整 variant grid）+ `get_metadata`（同節點，78k，提取 XML 結構）+ `get_design_context` × 2（節點 16782:533697 Size=Medium Shape=Rounded Filled Primary Enabled；節點 16782:533688 Size=Medium Shape=Full Rounded Filled Primary Enabled）。

**層級判定**：所有 variant 均為單顆 icon-only 按鈕，無展開子選單行為 → **Lite**。

**Variant 軸確認（`get_metadata` 解析）**：
- **Size**（4 值）：Extra Small 24px / Small 28px / Medium 36px / Large 40px（width = height，正方形）
- **Type**（4 值）：Primary / Secondary / Success / Danger
- **Shape**（2 值）：Rounded（圓角 4px）/ Full Rounded（全圓，`rounded-[24px]` ≈ radius = height/2）
- **Mode**（3 值）：Filled / Outlined / Flat
- **State**（5 值）：Enabled / Hover / Active / Focus / Disabled

**✅ 已確認對映（Figma 變數 → `athena-tokens.md`）**：
- `ColorSf/primary` → `{color-sf-primary}`
- `ColorSf/on-primary`（`$primary-text-color: #FFFFFF`）→ `{color-sf-on-primary}`
- `Primary_btn/$primary-bg-color` → `{color-sf-primary}`
- `Primary_btn/$primary-bg-color-hover` (#2877EE,#FFFFFF) → base `{color-sf-primary}` + overlay `{color-sf-primary-btn-hover-8}`
- `Primary_btn/$primary-bg-color-pressed` → overlay `{color-sf-primary-btn-pressed-12}`
- `Primary_btn/$primary-bg-color-disabled` (#2877EE) → base only；final = `{color-sf-primary-opacity-12}`（同 Button.md disabled 推導）
- `$shadow-focus-ring1` → `{ds-shadow-focus-ring1}`
- `Sec_btn/$secondary-bg-color-hover` (#FFFFFF,#0F172A) → overlay `{color-sf-on-surface-opacity8}`
- `Sec_btn/$secondary-bg-color-pressed` → overlay `{color-sf-on-surface-opacity12}`
- `Sec_btn/$secondary-bg-color-disabled` (#0F172A) → `{color-sf-on-surface-opacity12}`
- `Border/$border-disabled: #D7DAE0` → `{color-sf-outline-variant}`
- `Icon/$icon-color: #3C4A5B` → `{color-sf-on-surface-variant}`（secondary / flat 的 icon 色）
- `Icon/$icon-color-disabled: #0F172A` → base；最終 fg = `{color-sf-on-surface-opacity38}`
- `Success_btn/$*` / `Danger_btn/$*` → 同 Button.md §4 對映
- `$shadow-sm` → `{ds-shadow-sm}`（Flat mode hover 可能使用，待確認）

**🎨 量測值（非 token，指向 app.css）**：
- Medium Rounded Filled Enabled：36×36px，padding 16px/8px，icon 16px，radius 4px
- Medium Full Rounded Filled Enabled：36×36px，padding 16px/8px，icon 18px，radius 24px（≈全圓）
- Extra Small：24×24px / Small：28×28px / Large：40×40px（`get_metadata` 量測值）

**Shape 裁示**：
- **Rounded = square button**：`rounded-[4px]` → `{ds-radius-small}`（與 Button.md 同）
- **Full Rounded = circular button**：`rounded-[24px]` for 36px = radius = height/2，等效全圓 → `{ds-radius-10extra-large}`（1000px token，等效全圓）

**⚠️ 待確認**：
1. **Small 尺寸**：`get_metadata` 量測為 28px；Button.md Small 為 32px，不同元件尺寸，待 DS owner 確認是否為刻意區隔（IconButton XS/S/M/L = 24/28/36/40 vs Button XS/S/M/L = 24/32/36/40）。
2. **Flat mode disabled border**：Flat 無邊框，disabled 態是否也無 border，或改顯示 `{color-sf-outline-variant}` 邊框作為停用提示，待截圖單一 Flat Disabled variant 確認。
3. **icon 尺寸隨 Size 縮放**：目前只量測 Medium（16px Rounded / 18px Full Rounded）；XS / Small / Large 的 icon 尺寸待 `get_design_context` 各尺寸節點確認。
