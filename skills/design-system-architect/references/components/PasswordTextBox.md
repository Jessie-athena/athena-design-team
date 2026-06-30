---
name: PasswordTextBox
category: 輸入 — 密碼
tier: lite
status: ✅ 已產出
authority: ERP CLAUDE.md（Syncfusion TextBox type="password", @syncfusion/ej2-vue-inputs）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16812-17033
version: v0.1
last-synced: 2026-06-29
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺值一律 token-reference；raw px 量測值指向值權威 `prototyper/assets/app.css`，不在此重印。
> §3–§5 已用 Figma `get_variable_defs` + `get_design_context`（節點 16812-17333，Filled / Small / Default）+ `get_screenshot`（元件集 16812-17033）校準；token gap 見文末「Figma 補入紀錄」。
> **本元件為獨立元件集**（非 TextBox 帶 rightIcon 的 variant），Figma frame「Password TextBox」（16812-17033）與「TextBox」（16791-26050）並列，共用大量 token，差異見 §2 Anatomy。

## 1. 概述　📋

密碼輸入框。與 TextBox 容器結構一致（Material 3 Filled 底線式），核心差異為：
- 右側固定 **eye icon-btn**（show/hide password toggle），預設顯示
- 輸入值初始以 `**********` 遮罩字元顯示（placeholder 色），toggle 後切換為明文

**何時使用**：登入頁密碼欄位、設定頁修改密碼、任何需遮罩輸入的場景。  
**何時不用**：一般文字輸入 → `TextBox`；數值/金額 → `NumericTextBox`；需自訂右側 icon 但不做 show/hide → `TextBox` 加 `rightIcon`。

## 2. Anatomy　🎨

> 結構樹源自 `get_design_context`（節點 16812-17333）。相較 TextBox，差異僅在 eye-toggle-btn 固定於右側，以及 `text` 預設為 `**********`。

```
PasswordTextBox（vertical stack, gap = {ds-space-margin-small}）
├── input-container（h = 40px，Filled 底線式）
│   ├── [left-icon?]（16×16, opt — 與 TextBox 同）
│   ├── text-area-wrapper（flex-grow）
│   │   ├── label-row?（label-text + required-asterisk? + label-icon?）
│   │   └── masked-value（"**********" 預設；明文態顯示實際輸入）
│   └── right-btn-group
│       ├── clear-icon-btn?（32×32, opt — 與 TextBox 同）
│       └── eye-icon-btn（32×32, 固定存在）  ← 核心差異
└── helper-row?（helper-text + alert-icon?, opt）
```

**eye-icon-btn 子結構**（node 16812-17350）：
```
eye-icon-btn-container（32×32）
├── ripple-bg（32×32, bg = transparent, radius = {ds-radius-2extra-large} = 16px）
└── eye-icon（16×16）  ← Eye / Eye-off 兩種 glyph
```

**遮罩字元**：預設 `**********`，字色 = `{ds-color-placeholder}`（非輸入文字色 `{color-sf-on-surface}`）

## 3. 視覺規格 Tokens　🎨🔗

> 容器尺寸、間距、圓角、字型與 TextBox 完全一致，此處標明共用項並展開 eye icon-btn 差異部分。

```yaml
# ── 尺寸（size: Small）── 同 TextBox ────────────────────────────────────────
height_small:         40px                                 # 🎨 量測值；見 TextBox.md 對應值
padding_left:         10px                                 # 🎨 量測值，無 {ds-space-*} token；值見 app.css
inner_icon_gap:        6px                                 # 🎨 left-icon ↔ text 量測值，無 token；值見 app.css
label_input_gap:      "{ds-space-margin-small}"            # 🔗 同 TextBox
helper_gap:           "{ds-space-margin-small}"            # 🔗 同 TextBox
icon_size:            16px                                 # 🎨 left-icon 量測值，同 TextBox

# ── eye-icon-btn（PasswordTextBox 核心差異）─────────────────────────────────
eye_icon_btn_size:    32px                                 # 🎨 與 TextBox clear/right-icon-btn 同款
eye_icon_size:        16px                                 # 🎨 eye glyph 本身大小（同 left-icon）
eye_icon_btn_radius:  "{ds-radius-2extra-large}"           # 🔗 16px（ripple 圓形；same as TextBox icon-btn）
eye_icon_color:       "{color-sf-on-surface-variant}"      # 🔗 rgb(60 74 91)（Icon/$icon-color）
eye_icon_btn_bg:      transparent                          # 🎨 BG/$transparent；ripple 態見 §5

# ── 容器外形（shape: Filled）── 同 TextBox ───────────────────────────────────
container_bg:         "{color-sf-surface-variant}"         # 🔗 同 TextBox
radius_top:           "{ds-radius-small}"                  # 🔗 4px（top-left + top-right）
radius_bottom:        0                                    # 🎨 底線式，無底圓角
border_side:          bottom only                          # 🎨 Filled 特徵
border_width_default: "{ds-borderwidth-small}"             # 🔗 1px
border_width_active:  "{ds-borderwidth-medium}"            # 🔗 2px
border_color_default: "{color-sf-outline}"                 # 🔗 rgb(127 137 150)

# ── 遮罩字元（PasswordTextBox 差異）─────────────────────────────────────────
masked_char:          "**********"                         # 📋 預設值（Figma text prop default）
masked_text_color:    "{ds-color-placeholder}"             # 🔗 rgb(103 113 126)（與 placeholder 同色）
# 明文態（show-password）時：text_color → {color-sf-on-surface}（同 TextBox 輸入文字色）

# ── 標籤文字 ── 同 TextBox ────────────────────────────────────────────────
label_color:          "{color-sf-on-surface-variant}"      # 🔗 同 TextBox
label_font_size:      "{font-size-sf-text-md}"             # 🔗 14px
label_font_weight:    "{font-weight-sf-normal}"            # 🔗 400
label_line_height:     1.3                                 # 🎨
required_color:       "{color-sf-danger}"                  # 🔗 rgb(244 73 62)
required_font_size:   "{font-size-sf-text-sm}"             # 🔗 12px

# ── 輸入文字（明文態）── 同 TextBox ──────────────────────────────────────────
text_color:           "{color-sf-on-surface}"              # 🔗 rgb(15 23 42)
placeholder_color:    "{ds-color-placeholder}"             # 🔗 rgb(103 113 126)
content_font_size:    "{font-size-sf-text-md}"             # 🔗 14px
content_font_weight:  "{font-weight-sf-normal}"            # 🔗 400
content_line_height:   1.5                                 # 🎨
content_letter_spacing: 0.24px                             # 🎨

# ── 輔助文字 ── 同 TextBox ────────────────────────────────────────────────
helper_color:         "{color-sf-on-surface-variant}"      # 🔗
helper_font_size:     "{font-size-sf-text-sm}"             # 🔗 12px
helper_font_weight:   "{font-weight-sf-normal}"            # 🔗 400
helper_line_height:    1.3                                 # 🎨
helper_letter_spacing: 0.1px                               # 🎨

# ── 字型 ── 同 TextBox ───────────────────────────────────────────────────
font_family:          "{font-family-sf-fontfamily}"        # 🔗 Roboto / Noto Sans TC
```

## 4. Variants　🎨🔗

> Figma 元件集（16812-17033）暴露三個 variant 軸；與 TextBox 完全對應（Figma 來源 `get_metadata` 確認）。

```yaml
shape:
  filled:                                              # 🎨 Filled 底線式（ERP 主要用）
    bg: "{color-sf-surface-variant}"                   # 🔗
    border_side: bottom only                           # 🎨
  outlined:                                           # 🎨 full border（非 ERP 主流，備查）
  line:                                               # 🎨 無背景、只有底線（極簡）

size:                                                 # 🎨 量測自 Figma metadata
  extra_small: { height: 32px }                       # 🎨 Shape=Filled；Extra Small 高度
  small:       { height: 40px }                       # 🎨 Shape=Filled；ERP 主要使用
  medium:      { height: 48px }                       # 🎨 Shape=Filled
  large:       { height: 56px }                       # 🎨 Shape=Filled

state:                                               # 🎨 State prop（同 TextBox）
  default: ~
  hover: ~
  focus: ~
  active: ~
  filled: ~          # 有值輸入後的靜止態
  read_only: ~
  disabled: ~
  info: ~
  success: ~
  warning: ~
  error: ~
```

## 5. States　🎨🔗

> 所有底線、背景、文字 token 與 TextBox 完全一致，差異僅 eye-icon-btn 互動態。
> 底線寬度：default/read-only/hover → `{ds-borderwidth-small}`；focus/active/validation → `{ds-borderwidth-medium}`。

```yaml
default:                                               # 🎨 node 16812-17333 量測
  container_bg: "{color-sf-surface-variant}"           # 🔗
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}"   # 🔗 1px
  masked_text: "{ds-color-placeholder}"                # 🔗
  label: "{color-sf-on-surface-variant}"               # 🔗
  eye_icon: "{color-sf-on-surface-variant}"            # 🔗

focus:
  border_bottom: "{ds-borderwidth-medium} {color-sf-primary}"  # 🔗 2px rgb(40 119 238)
  label: "{color-sf-primary}"                          # 📋 Material 3 focus label 啟動色

show_password:                                         # 📋 eye-btn toggled → show明文
  masked_text: hidden                                  # 📋 遮罩移除
  input_type: "text"                                   # 📋 type="text"
  text: "{color-sf-on-surface}"                        # 🔗 明文字色

hide_password:                                         # 📋 eye-btn toggled → hide
  masked_text: "{ds-color-placeholder}"                # 🔗 回到遮罩色
  input_type: "password"                               # 📋 type="password"

success:
  border_bottom: "{ds-borderwidth-medium} {color-sf-success}"  # 🔗 2px rgb(18 183 106)

warning:
  border_bottom: "{ds-borderwidth-medium} {color-sf-warning}"  # 🔗 2px rgb(247 144 9)

info:
  border_bottom: "{ds-borderwidth-medium} {color-sf-info}"     # 🔗 2px rgb(46 144 250)

error:
  border_bottom: "{ds-borderwidth-medium} {color-sf-error}"    # 🔗 2px rgb(244 73 62)
  label: "{color-sf-error}"                            # 📋
  helper: "{color-sf-error}"                           # 📋

disabled:
  text: "{color-sf-on-surface-opacity38}"              # 🔗 38% opacity
  masked_text: "{color-sf-on-surface-opacity38}"       # 🔗（遮罩字元同樣降透明）
  container_bg: "{color-sf-surface-variant}"           # 🔗 底色保留

read_only:
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}"   # 🔗 同 default（無 active 底線）
  eye_icon: "{color-sf-on-surface-variant}"            # 🔗（show/hide 仍可互動）
```

## 9. a11y　📋

- **eye-toggle-btn** 必須有動態 `aria-label`：toggle 前 `aria-label="顯示密碼"`、toggle 後 `aria-label="隱藏密碼"`（隨 state 切換，非靜態）。
- `<input>` 的 `type` 屬性在 `"password"` / `"text"` 間切換（非換 icon）：`type="password"` 讓 screen reader 通知使用者這是密碼欄位。
- `autocomplete="current-password"` 建議加在登入密碼欄；設定新密碼改用 `autocomplete="new-password"`。
- `<label>` 透過 `for` / `id` 與 input 關聯；`required` 欄位加 `aria-required="true"`。
- Error 態需 `aria-invalid="true"` + `aria-describedby` 指向 helper text。
- eye icon-btn 為 `<button type="button">` 以防止觸發 form 提交；min touch target 44×44（eye btn 32×32 需加 `padding` 補滿點擊區）。
- 遮罩字元（`**********`）為 Figma prop 的預設 `text` 值，code 端應以 `type="password"` 讓瀏覽器自行處理遮罩，**不要** 手動顯示星號（避免 screen reader 逐字念出）。

## 12. API / Props　📋

> 以下 props 反映 Figma 設計層（`get_design_context` 抽取，TypeScript interface 轉換）。
> Syncfusion 實際 API 詳 playground `textbox.vue`（`type="password"` + 自訂 toggle 邏輯）。

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `label` | boolean | false | 是否顯示 label |
| `label1` | string | "Label" | label 文字 |
| `labelIcon` | boolean | false | 顯示 label 右側 info icon |
| `required` | boolean | false | 顯示必填 `*` |
| `text` | string | "**********" | 遮罩態顯示值（明文態由 input value 決定） |
| `leftIcon` | boolean | false | 顯示左側 icon（可選，如 lock icon） |
| `leftIcon1` | slot | — | 自訂左側 icon |
| `rightIcon` | boolean | **true** | 顯示 eye-toggle-btn（**PasswordTextBox 預設為 true**，與 TextBox 不同） |
| `rightIcon1` | slot | — | 自訂右側 icon（若覆寫則 eye icon 消失，請謹慎使用） |
| `clearIcon` | boolean | false | 顯示清除（×）按鈕（可與 eye-btn 並存，位於其左側） |
| `alertMessage` | boolean | false | 顯示 helper / alert text |
| `alertMessage1` | string | "Helping text" | helper 文字內容 |
| `alertIcon` | boolean | false | 顯示 helper 區域的 alert icon |
| `alertIcon1` | slot | — | 自訂 alert icon |
| `shape` | "Filled" | "Filled" | 輸入框樣式（ERP 一律 Filled） |
| `size` | "Small" | "Small" | 尺寸 |
| `state` | "Default" / "Error" / "Success" / "Warning" / "Info" | "Default" | 驗證狀態 |

**Code 端 toggle 狀態（非 Figma prop，需自行管理）：**

| Prop / State | Type | 說明 |
|---|---|---|
| `isVisible` | boolean（ref） | `true` = 明文顯示；`false` = 遮罩 |
| `inputType` | computed | `isVisible ? 'text' : 'password'` |
| `eyeAriaLabel` | computed | `isVisible ? '隱藏密碼' : '顯示密碼'` |

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface §On-* §Outline §Status §Border Width §Space §Radius §Typography
- 同族參考：`components/TextBox.md`（容器結構 authority；所有「同 TextBox」的 token 以此為準）
- Prototype 落地（值權威 / class 命名）：`prototyper/assets/app.css`（Syncfusion Material 3 theme override）
- Code：`@syncfusion/ej2-vue-inputs`；playground 參考 `syncfusion-playground/app/pages/playground/textbox.vue`（`type="password"` + toggle button）
- Figma：`https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/` 節點 `16812-17033`（元件集）/ `16812-17333`（Filled Small Default 量測節點）

---

## Figma 補入紀錄（2026-06-29）

> Layer 1–4 依 `figma-to-component-doc.md` Token Matching workflow 執行。
> 來源：`get_variable_defs`（節點 16812-17033，共 47 變數）+ `get_metadata`（元件集結構全覽）+ `get_design_context`（節點 16812-17333，Filled/Small/Default 量測）+ `get_screenshot`（元件集 16812-17033，截圖視覺確認）。

**元件獨立性確認**：Password TextBox 為 Figma 獨立 frame（16812-17033），與 TextBox（16791-26050）並列，非 variant 關係。兩者 token set 幾乎完全一致（47 個變數含 TextBox 的全部 37 個，另多 `$info` / `$border-info` / `ColorSf/info`），差異在結構（eye-btn 預設顯示 + 遮罩字元預設值）。

**✅ 已確認對映（Figma 變數 → `athena-tokens.md`）**：`ColorSf/*`（surface-variant / outline / on-surface / on-surface-variant / primary / success / warning / danger / error / info / surface / transparent / on-surface-opacity38）、`DsColor/placeholder`、字型（FontSizeSf/TextSm|Md|Lg、FontWeightSf/Normal、FontFamilySf/Fontfamily）、間距（DsSpace/Margin/Small）、圓角（DsRadius/Small）、框線寬（DsBorderwidth/Small|Medium）。**47 個變數全數找到對應，無漂移。**

**🆕 新增 token（相較 TextBox）**：`$info` / `$border-info` / `ColorSf/info` = `{color-sf-info}`（rgb(46 144 250)）— Info state 底線色，TextBox 文件中已隱含（state list 有 Info），此處明確列入 §5。無新 token gap。

**eye icon 尺寸確認**：eye-icon-btn 外框 32×32（與 TextBox clear/right-icon-btn 同款，`data-node-id="16812:17350"`），eye glyph 16×16（`left-[8px] top-[8px]` 定位），btn radius = 16px = `{ds-radius-2extra-large}`。

**遮罩字元色確認**：`get_design_context` 中 masked value（node 16812-17345）的字色綁定 `{ds-color-placeholder}`（#67717e），而非 `{color-sf-on-surface}`（輸入文字色）。此為意圖設計（遮罩態視覺上與 placeholder 相近）。

**⚠️ Token gap（非阻塞，與 TextBox 相同，已標 🎨）**：
1. **`padding_left: 10px`**：scale 無對應（8→12px 跳）→ 值權威見 `prototyper/assets/app.css`
2. **`inner_icon_gap: 6px`**：同上 → 值權威見 `app.css`

**⬜ 待補（次要）**：
- Extra Small / Medium / Large 三尺寸的完整量測（height 已從 metadata 確認；padding 待逐一 get_design_context）
- focus 態 eye icon 色是否改為 `{color-sf-primary}`（截圖未含 focus 態，依 Material 3 慣例推導為不變色，仍需驗證）
- disabled 態 eye icon 色（應為 `{color-sf-on-surface-opacity38}`，待 Figma 截圖確認）
