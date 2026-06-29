---
name: FAB（Floating Action Button）
category: 按鈕與動作
tier: lite
status: ✅ 已採用
authority: ERP CLAUDE.md（Syncfusion FAB, @syncfusion/ej2-vue-buttons）
figma-node: JhcpyIEEzSChcEXMRJUiIm/12736-73932
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs + get_screenshot + get_metadata + get_design_context；紀錄見文末「Figma 補入紀錄」
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺值一律 token-reference；對不上既有 token 時停下回報，勿臆造。
> §3–§5 已用 Figma `get_variable_defs`（節點 12736-73932）+ `get_design_context`（節點 12736-73954）校準；**未解 / 衝突項見文末「Figma 補入紀錄」**。

## 1. 概述　📋

浮動操作按鈕，固定於畫面角落，承載頁面最重要的單一主操作（通常為「新增」）。

**何時用**：
- List View 主操作（`新增`）浮於右下角，讓使用者在長清單中不需捲動至 Toolbar 即可觸發。
- 每個畫面至多一個 FAB（主操作唯一性）。

**何時不用**：
- 頁面已有明確 Toolbar 且視線不深（< 3 屏）時，優先用 Toolbar 的 `.btn.btn--primary`，不疊 FAB。
- Form View 禁用 FAB（動作集中於 Footer）。
- SpeedDial（展開多個子操作）**不在本元件集範圍**；Syncfusion 另有 `ejs-speeddial`，如需展開菜單請改用之。

**FAB vs SpeedDial 區分**：
本 Figma 元件集（節點 12736-73932）僅含純 FAB（圓角矩形 icon-only / extended），**不含** SpeedDial 展開子選單。截圖確認：所有 variant 皆為單顆按鈕，無展開行為。→ 層級判定：**Lite**。

---

## 2. Anatomy　🎨

```
┌─────────────────────────────────────────┐
│  [container: rounded rect / shadow-lg]  │
│    ┌──────────────────┐                 │
│    │  Leading Icon    │  [label?]       │
│    └──────────────────┘                 │
└─────────────────────────────────────────┘
```

- **container**：圓角矩形容器，承載 icon（Icon Only 模式）或 icon + label（Icon with Text / Extended 模式）。
- **Leading Icon**：18px icon；slot 名 `chooseIcon`，預設 edit pencil。
- **label**（僅 Icon with Text 模式）：icon 右側文字標籤，字型同 Button Body Content。
- **shadow**：預設帶 `$shadow-lg`（大提升），浮起感。

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# 🎨 get_design_context 節點 12736:73954（Size=Medium, Type=Primary, Mode=Icon Only, State=Enabled）量測

# ——— 尺寸（Icon Only 模式）———
# Size 軸（從 get_metadata 尺寸推算）：
extra_small:  32px × 32px   # 🎨
small:        40px × 40px   # 🎨
medium:       50px × 48px   # 🎨（設計稿寬≠高；icon-only 非正圓，為圓角矩形）
large:        58px × 56px   # 🎨

# ——— 尺寸（Icon with Text / Extended 模式）———
extended_extra_small:  63px × 32px   # 🎨
extended_small:        73px × 40px   # 🎨
extended_medium:       87px × 48px   # 🎨
extended_large:        87px × 56px   # 🎨

# ——— Medium 詳細量測（get_design_context 節點 12736:73954）———
padding:      16px (x) / 15px (y)     # 🎨 待對齊 token
radius:       "{ds-radius-2extra-large}"   # 🔗 Figma Radius/Radius2Xl = 16px（非全圓）
icon_size:    18px                     # 🎨 与 Button 元件一致（見 Button.md §3 修正）

# ——— 陰影 ———
shadow:       "{ds-shadow-lg}"         # 🔗 Figma $shadow-lg；提升階梯：FAB > Dialog 前一層

# ——— 字型（Icon with Text 模式的 label）———
font:         "{font-size-sf-text-xs} / {font-weight-sf-medium}"   # 🔗 11px / 500（Figma Body Content/11pt/Medium，Extra Small）
# 📋 注意：Medium/Large 的 label 字型是否升級至 TextSm / TextMd 待 Figma 單一 variant 量測確認；
#         目前以 Extra Small get_variable_defs 得到的 Body Content/11pt/Medium 為基準記錄。
```

---

## 4. Variants　🎨🔗

> Figma 元件集（節點 12736-73932）三個 variant 軸：
> - **Size**：Extra Small / Small / Medium / Large（4 值）
> - **Type**（色彩）：Primary / Secondary / Info / Success / Warning / Danger（6 值）
> - **Mode**：Icon Only / Icon with Text（2 值）
> State 軸另見 §5。

```yaml
# 色彩（Type 軸）；走 Primary_btn 同族 state-layer 邏輯（see §5）
primary:
  bg:     "{color-sf-primary}"        # 🔗 #2877ee
  fg:     "{color-sf-on-primary}"     # 🔗 white
  border: "{color-sf-primary}"        # 🔗 同 bg

secondary:
  bg:     "{color-sf-secondary-btn-bg}"    # 🔗 white
  fg:     "{color-sf-on-surface}"          # 🔗 #0f172a
  border: "{color-sf-outline}"             # 🔗 #7f8996

info:
  bg:     "{color-sf-info}"           # 🔗 #2e90fa
  fg:     "{color-sf-info-text}"      # 🔗 white
  border: "{color-sf-info}"           # 🔗

success:
  bg:     "{color-sf-success}"        # 🔗 #12b76a
  fg:     "{color-sf-success-text}"   # 🔗 white
  border: "{color-sf-success}"        # 🔗

warning:
  bg:     "{color-sf-warning}"        # 🔗 #f79009
  fg:     "{color-sf-warning-text}"   # 🔗 white
  border: "{color-sf-warning}"        # 🔗

danger:
  bg:     "{color-sf-danger}"         # 🔗 #f4493e
  fg:     "{color-sf-danger-text}"    # 🔗 white
  border: "{color-sf-danger}"         # 🔗

# Mode 軸
icon_only:       { icon: 18px }                                 # 🎨
icon_with_text:  { icon: 18px, gap: "{ds-space-padding-small}", label: see §3 font }  # 🔗 gap 4px
```

---

## 5. States　🎨🔗

> 互動態走「base 色 + state-layer 疊層」，與 Button.md §5 同族邏輯。
> Figma 變數命名（Primary_btn 族）：`$primary-bg-color-hover` = `#2877EE,#FFFFFF`（base + white overlay）。

```yaml
# 以 Primary Type 為例；其他 Type 以各自 bg 為 base，疊層同比例
enabled:
  shadow:   "{ds-shadow-lg}"                               # 🔗 浮起
hover:
  overlay:  "{color-sf-primary-btn-hover-8}"               # 🔗 white 8%
  shadow:   "{ds-shadow-lg}"                               # 🔗 維持
active:
  overlay:  "{color-sf-primary-btn-pressed-12}"            # 🔗 white 12%（pressed）
  shadow:   "{ds-shadow-lg}"                               # 🔗 維持（或可降為 md，待確認）
focus:
  ring:     "{ds-shadow-focus-ring1}"                      # 🔗 白 1px 內環 + 黑 3px 外環
  shadow:   "{ds-shadow-lg}"                               # 🔗 維持
disabled:
  fg:       "{color-sf-on-surface-opacity38}"              # 🔗 38% opacity
  bg:       "{color-sf-on-surface-opacity12}"              # 🔗 12% opacity overlay（Figma $primary-bg-color-disabled = #2877EE → disabled 態顏色含 opacity 疊層實作）
  shadow:   "{ds-shadow-none}"                             # 🔗 停用時去除浮起感
  cursor:   not-allowed                                    # 📋
```

---

## 9. a11y　📋

- **`aria-label` 必填**（icon-only FAB 無可見文字，螢幕閱讀器依賴 `aria-label` 辨識用途）。
  - 正確範例：`aria-label="新增採購單"`、`aria-label="新增"`。
  - Icon with Text 模式：label 本身即語義，`aria-label` 仍建議補充上下文（如同頁有多個 FAB 時）。
- **Focus ring**：`{ds-shadow-focus-ring1}`（白 1px 內環 + 黑 3px 外環）；鍵盤 Tab 到達 FAB 時必須可見。
- **鍵盤觸發**：Enter·Space。
- **觸碰目標**：最小 44×44px；Extra Small（32px）需確認觸碰區域已補足（可用 padding 或 touch-target wrapper）。
- **對比**：primary bg `{color-sf-primary}` 搭配 `{color-sf-on-primary}` white，確認符合 WCAG AA（4.5:1）；若不達標參見 `athena-tokens.md` 的 `*-strong` 別名。
- **position: fixed / absolute**：FAB 通常脫離文件流，螢幕閱讀器可能跳過；建議於 DOM 中置於 `<main>` 結尾前，避免 tab order 混亂。

---

## 12. API / Props　📋

> 以下為 Syncfusion `ejs-fab` 的關鍵 props，完整文件見 [Syncfusion FAB API](https://ej2.syncfusion.com/vue/documentation/api/fab/)。

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `iconCss` | string | — | icon font class（Syncfusion icons 格式，如 `e-icons e-edit`） |
| `content` | string | — | Extended FAB 的 label 文字；省略 = Icon Only 模式 |
| `cssClass` | string | — | 注入客製 class（控制 Type 色彩與 Size） |
| `isPrimary` | boolean | false | 🚫 FAB 無此 prop；色彩由 `cssClass` 控制，**不同於 `ejs-button`** |
| `disabled` | boolean | false | 停用（見 §5 disabled） |
| `target` | string | — | CSS selector；FAB 相對哪個容器定位（預設 body） |
| `position` | BottomRight / BottomLeft / TopRight / TopLeft / … | BottomRight | 定位角落 |
| `onClick` | EventHandler | — | 點擊回呼 |

**ERP 使用模式**：

```vue
<!-- 列表頁浮動「新增」主操作 -->
<ejs-fab
  cssClass="e-fab--primary"
  iconCss="e-icons e-plus"
  content="新增"
  target="#list-container"
  position="BottomRight"
  aria-label="新增採購單"
  @click="onCreate"
/>
```

---

## 13. 關聯　🔗

- **Tokens**：`../athena-tokens.md` §Primary / §Status / §Radius / §Shadow / §Typography
- **語意對照**：`../athena-design.md` §主色與動作 / §狀態色
- **同族元件**：`Button.md`（同套件、同 state-layer 邏輯）
- **Prototype 落地**：`prototyper`（`app.js` 中 FAB 以 `.fab` / `ejs-fab` 掛於 `#app` 末，postion fixed right-bottom）
- **Code**：`@syncfusion/ej2-vue-buttons`（FAB 與 SpeedDial 同套件；playground 參考：`fab.vue`）
- **Figma 元件集**：`https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/?node-id=12736-73932`

---

## Figma 補入紀錄（2026-06-29）

> 來源：`get_variable_defs`（節點 12736-73932）+ `get_screenshot`（同節點）+ `get_metadata`（同節點）+ `get_design_context`（節點 12736-73954，Size=Medium, Type=Primary, Mode=Icon Only, State=Enabled）。

**層級判定**：截圖確認全部 variant 為單顆按鈕，無展開子選單 → **Lite**。

**✅ 已確認對映（Figma 變數 → `athena-tokens.md`）**：
- `ColorSf/primary` → `{color-sf-primary}` rgb(40 119 238)
- `ColorSf/on-primary` → `{color-sf-on-primary}` white
- `ColorSf/secondary-btn-bg` → `{color-sf-secondary-btn-bg}`
- `ColorSf/on-surface` → `{color-sf-on-surface}`
- `ColorSf/outline` → `{color-sf-outline}`
- `ColorSf/info` / `ColorSf/info-text` → `{color-sf-info}` / `{color-sf-info-text}`
- `ColorSf/success` / `ColorSf/success-text` → `{color-sf-success}` / `{color-sf-success-text}`
- `ColorSf/warning` / `ColorSf/warning-text` → `{color-sf-warning}` / `{color-sf-warning-text}`
- `ColorSf/danger` / `ColorSf/danger-text` → `{color-sf-danger}` / `{color-sf-danger-text}`
- `ColorSf/primary-btn-hover-8` → `{color-sf-primary-btn-hover-8}` white 8%
- `ColorSf/primary-btn-pressed-12` → `{color-sf-primary-btn-pressed-12}` white 12%
- `ColorSf/on-surface-opacity38` → `{color-sf-on-surface-opacity38}`
- `ColorSf/on-surface-opacity12` → `{color-sf-on-surface-opacity12}`
- `$shadow-lg` → `{ds-shadow-lg}`（`athena-tokens.md §Shadow` 已定義）
- `$shadow-none` → `{ds-shadow-none}`
- `$shadow-focus-ring1` → `{ds-shadow-focus-ring1}`
- `Radius/Radius2Xl = 16px` → `{ds-radius-2extra-large}`（非全圓）
- `FontSizeSf/TextXs` / `FontWeightSf/Medium` → `{font-size-sf-text-xs}` / `{font-weight-sf-medium}`

**🎨 量測值（非 token，指向 app.css）**：
- Medium Icon Only：width 50px × height 48px
- 尺寸列表（Extra Small 32 / Small 40 / Medium 48 / Large 56，高度軸）
- padding-x 16px / padding-y 15px（Medium，get_design_context 取得）
- icon size 18px（與 Button.md 一致）

**⚠️ 待確認**：
1. **padding 對應 token**：Medium 的 padding-x 16px = `{ds-space-padding-extra-large}`，padding-y 15px 無直接對應 token（最接近 `{ds-space-padding-extra-large}` 16px 差 1px）。待 DS owner 確認是否為 15px 固定值或捨入誤差。
2. **Extended label 字型（Medium / Large）**：目前 `get_variable_defs` 取到的是 `Body Content/11pt/Medium`（XS 尺寸）；Medium / Large 的 label 字型（可能升至 TextSm 12px 或 TextMd 14px）待補 `get_design_context` 於各尺寸 Icon with Text 節點確認。
3. **Secondary hover/active state-layer**：`Sec_btn/$secondary-bg-color-hover = #FFFFFF,#0F172A`（base white + on-surface overlay），對應 token 為 `{color-sf-on-surface-opacity8}` / `{color-sf-on-surface-opacity12}`；已記錄但未在 §5 展開（§5 以 Primary 為例，Secondary 同比例推論）。
4. **disabled shadow**：Figma 未明確定義 disabled 態陰影是否降為 none；此處依語意判斷填 `{ds-shadow-none}`，待截圖確認。
