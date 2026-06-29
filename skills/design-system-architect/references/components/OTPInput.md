---
name: OTPInput
category: 輸入 — 驗證碼
tier: full
status: ✅ 已採用
authority: ERP CLAUDE.md（Syncfusion OtpInput, @syncfusion/ej2-vue-inputs）
figma-node: JhcpyIEEzSChcEXMRJUiIm/15568-220
version: v0.1
last-synced: 2026-06-29
---

> 依 `../component-doc-schema.md`（Full 層）產出。視覺值一律 token-reference；raw px 量測值指向值權威 `prototyper/assets/app.css`，不在此重印。
> §3–§5 已用 Figma `get_variable_defs`（節點 15568-220，20 個變數）+ `get_design_context`（節點 16827:40126 Filled/Small/4格、15568:423 Outlined/Small/4格、15568:865 Line/Small/4格）+ `get_screenshot`（節點 15568-220 整組）校準；token gap 見文末「Figma 補入紀錄」。

## 1. 概述　📋

多格驗證碼輸入元件。用於登入流程的**兩步驟驗證**（2FA）、手機驗證碼確認等一次性密碼（OTP）輸入情境。每格接受單一字元，輸入後自動跳至下一格，全部填入後觸發 `valueChanged` 事件。

**何時使用**：需要使用者輸入固定位數驗證碼（通常 4–6 位）且各位元獨立展示時。

**何時不用**：
- 一般文字 / 數字輸入 → `TextBox` / `NumericTextBox`
- 不需要位元分隔視覺的單欄驗證碼輸入 → `TextBox` with `type="number"`

## 2. Anatomy　🎨

> 依 `get_design_context`（節點 16827:40126 Filled/Small/4格）+ `get_metadata`（節點 15568-220）確認結構。

```
OTPInput（horizontal flex，gap = {ds-space-padding-large}，items-center）
├── AtomOtp（單格容器，×N）          # N = length prop（3 / 4 / 5 / 6）
│   ├── $border（底層背景 + 外框，absolute fill）
│   └── Slider container（絕對置中，水平 flex）
│       └── Slider（overflow clip）
│           └── text（值 / placeholder）
├── separator（字元，格間顯示，optional）
├── AtomOtp（×2）
├── separator（optional）
└── AtomOtp（×3 or more…）
```

**補充說明**：
- separator 為 Figma 元件的一部分（不可選關閉，由 `separator` prop 設值；傳空字串可視覺隱藏）
- 單格為正方形（Filled / Outlined shape），但 Line shape 時寬高不同（w=40，h 依 size 縮短）
- 無整組 error state 的 Figma 節點（元件集僅展示 Default state）；error 視覺需搭配外層容器處理

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── 整組間距 ─────────────────────────────────────────────────────────────────
group_gap:            "{ds-space-padding-large}"      # 🔗 12px（Default）/ 16px（Comfortable）
group_align:          center                          # 🎨 items-center（含 separator 垂直對齊）

# ── 單格尺寸（size 四階，Filled / Outlined 為正方形；Line 同寬不等高） ─────────
# Filled / Outlined（正方形）：
size_extra_small:     32px × 32px                    # 🎨 quantity 量測（ExtraSmall Filled）
size_small:           40px × 40px                    # 🎨 quantity 量測（Small Filled，設計建議預設）
size_medium:          48px × 48px                    # 🎨 quantity 量測（Medium Filled）
size_large:           56px × 56px                    # 🎨 quantity 量測（Large Filled）

# Line（底線式，寬固定 40px，高略矮）：
line_h_extra_small:   24px                           # 🎨（ExtraSmall Line）
line_h_small:         32px                           # 🎨（Small Line）
line_h_medium:        36px                           # 🎨（Medium Line）
line_h_large:         40px                           # 🎨（Large Line）
line_w:               40px                           # 🎨 固定同 Filled 同 size 寬

# ── 格內文字 ─────────────────────────────────────────────────────────────────
text_color:           "{color-sf-on-surface}"         # 🔗 rgb(15 23 42)（填入值）
placeholder_color:    "{ds-color-placeholder}"        # 🔗 rgb(103 113 126)
text_font_size:       "{font-size-sf-text-md}"        # 🔗 14px
text_font_weight:     "{font-weight-sf-normal}"       # 🔗 400
text_line_height:     1.3                             # 🎨
font_family:          "{font-family-sf-fontfamily}"   # 🔗 Roboto（EN）/ Noto Sans TC（ZH）

# ── Separator ────────────────────────────────────────────────────────────────
separator_color:      "{color-sf-on-surface}"         # 🔗 rgb(15 23 42)
separator_font_size:  "{font-size-sf-text-lg}"        # 🔗 16px（Label/16pt/Regular）
separator_font_weight: "{font-weight-sf-normal}"      # 🔗 400
separator_line_height: 1.3                            # 🎨
```

## 4. Variants　🎨🔗

> Figma 元件集（節點 15568-220）暴露三個 variant 軸：`Size`、`Count`、`Shape`。

```yaml
# ── Shape 軸（對應 Syncfusion stylingMode）────────────────────────────────────
filled:
  # ★ Athena 預設樣式（與 TextBox 保持一致）
  cell_bg:      "{color-sf-surface-variant}"      # 🔗 rgb(237 240 247)
  border_style: bottom_only                       # 🎨 Filled 特徵：僅底邊線
  border_width: "{ds-borderwidth-small}"          # 🔗 1px（default 態）
  border_color: "{color-sf-outline}"              # 🔗 rgb(127 137 150)
  radius_top:   "{ds-radius-small}"               # 🔗 4px（top-left + top-right）
  radius_bottom: 0                                # 🎨 Filled 底線式，底部無圓角

outlined:
  cell_bg:      "{color-sf-transparent}"          # 🔗 rgba(255,255,255,0)
  border_style: all_sides                         # 🎨 Outlined 特徵：全框線
  border_width: "{ds-borderwidth-small}"          # 🔗 1px
  border_color: "{color-sf-outline}"              # 🔗 rgb(127 137 150)
  radius:       "{ds-radius-small}"               # 🔗 4px（全四角）

line:
  # 對應 Syncfusion stylingMode="underlined"
  cell_bg:      "{color-sf-transparent}"          # 🔗 rgba(255,255,255,0)
  border_style: bottom_only                       # 🎨 Line 特徵：僅底邊線
  border_width: "{ds-borderwidth-small}"          # 🔗 1px
  border_color: "{color-sf-outline}"              # 🔗 rgb(127 137 150)
  radius:       0                                 # 🎨 無圓角（Radius/RadiusNone）

# ── Count 軸 ──────────────────────────────────────────────────────────────────
count:
  supported: [3, 4, 5, 6]                        # 🎨 Figma 元件集全部格數
  default: 4                                      # 📋 驗證碼常見 4 位（6 位時用 :length="6"）

# ── Size 軸 ───────────────────────────────────────────────────────────────────
size:
  extra_small: { cell: "32px（F/O）/ 24×40px（L）" }   # 🎨
  small:       { cell: "40×40px（F/O）/ 32×40px（L）" } # 🎨 預設建議尺寸
  medium:      { cell: "48×48px（F/O）/ 36×40px（L）" } # 🎨
  large:       { cell: "56×56px（F/O）/ 40×40px（L）" } # 🎨
```

**命名對照（Figma → Syncfusion `stylingMode`）**：

| Figma Shape | Syncfusion stylingMode |
|---|---|
| `Filled` | `"filled"` |
| `Outlined` | `"outlined"` |
| `Line` | `"underlined"` |

## 5. States　🎨🔗

> Figma 元件集僅展示 `Default` state；interactive states（focus / error / disabled）依 Syncfusion Material 3 行為及 TextBox 類比推導，標 📋。

```yaml
# ── 整組層級 ──────────────────────────────────────────────────────────────────
group_default:
  description: "空格（placeholder 顯示）或部分填入"

group_complete:
  description: "全部格填入 → 觸發 valueChanged 事件"

group_error:
  description: "驗證失敗（由外層邏輯控制，ERP 通常顯示 helper text / toast）"
  note: "📋 Figma 無整組 error state 節點；error 視覺依外層容器 / `cssClass` 客製"

group_disabled:
  description: "整組 disabled，所有格不可互動"

# ── 單格層級 ──────────────────────────────────────────────────────────────────
cell_empty:                                              # 🎨
  text: "{ds-color-placeholder}"                        # 🔗（顯示 placeholder 字元）
  bg:   "{color-sf-surface-variant}"                    # 🔗（Filled；其他 shape 見 §4）

cell_filled:                                             # 📋（值已輸入）
  text: "{color-sf-on-surface}"                         # 🔗
  bg:   "{color-sf-surface-variant}"                    # 🔗（Filled）

cell_focused:                                            # 📋 類比 TextBox focus 態
  border_width: "{ds-borderwidth-medium}"               # 🔗 2px
  border_color: "{color-sf-primary}"                    # 🔗 rgb(40 119 238)

cell_disabled:                                           # 📋 類比 TextBox disabled 態
  text: "{color-sf-on-surface-variant-opacity38}"       # 🔗 Material 3 38% 慣例
  bg:   "{color-sf-surface-variant}"                    # 🔗（Filled；不用 disabled bg 調淡）
```

## 6. Behavior 行為　📋

| 動作 | 行為 |
|---|---|
| 輸入單一字元 | 值填入當前格，自動 focus 移至下一格（auto-advance） |
| 輸入後已是末格 | 停在末格；若 `valueChanged` 未綁定則靜默完成 |
| `Backspace`（空格） | 清除當前格後，focus 退回前一格 |
| `Backspace`（有值格） | 清除當前格值，停留原格（不退格） |
| `ArrowLeft / ArrowRight` | 在相鄰格間移動 focus |
| 貼上整串（paste） | 逐格分配字元（Syncfusion 自動處理 `length` 範圍內的貼上） |
| 全部填入 | 觸發 `valueChanged` event，回傳完整字串 |
| 超出 `length` 字元 | 超出部分忽略 |

## 7. RWD 響應式　📋

OTP Input 為行內元件，隨父容器寬度排列，無複雜 RWD 行為。

| 斷點 | 建議 |
|---|---|
| ≥ 1280px（桌面） | Small 或 Medium size；4–6 格 |
| < 1024px（Tablet） | Medium 或 Large size（觸控 target 更大） |
| < 768px（手機，若 ERP 需支援） | Large size；`length="4"` 優先（避免橫向溢出） |

## 8. Keyboard　📋

| 按鍵 | 行為 |
|---|---|
| `Tab` | 進入整組（focus 落在第一格）；再次 `Tab` 跳出整組 |
| 任意字元 | 輸入並 auto-advance |
| `Backspace` | 退格（見 §6 Behavior） |
| `ArrowLeft` / `ArrowRight` | 格間移動 focus |
| `Delete` | 清除當前格值，停留原格 |
| `Escape` | 不清除；退回上層 focus 由父容器處理 |

> Focus 持留：Tab 離開後再 Tab 回，focus 落第一格（Syncfusion 預設行為）。

## 9. a11y　📋

- 整組需用 `role="group"` 容器包裹，搭配 `aria-label="驗證碼輸入"` 或 `aria-labelledby` 連結可見標題。
- 每格需 `aria-label="第 N 位驗證碼"`（N 從 1 計）；Syncfusion 實作請查 `otp-input.vue` playground 確認是否原生提供。
- Error 態：外層 `aria-invalid="true"` + `aria-describedby` 指向錯誤訊息元素。
- Disabled 整組：`aria-disabled="true"` 於 group 容器。
- 色彩對比：`{color-sf-outline}` 邊框色（rgb 127,137,150）對白底對比比 ≈ 3.0:1，低於 WCAG 2.1 AA UI component 要求（3:1 邊界值；**建議確認是否達標或補充 focus ring**）。
- Focus ring：Syncfusion Material 3 主題提供；確認 `{ds-shadow-focus-ring1}` 是否套用（黑 3px 外環 + 白 1px 內環）。
- Mobile min touch target：Small size = 40×40px，符合 WCAG 2.5.5（44×44）邊界建議；Large = 56×56px 更佳。

## 10. 跨平台 Adaptive　📋

| | Web（桌面） | App（手機） |
|---|---|---|
| 預設 size | Small（40px） | Large（56px），符合 iOS / Android 最小 touch target |
| shape 建議 | Filled（與 TextBox 一致） | Filled 或 Outlined（底線式在觸控較難識別） |
| 鍵盤喚起 | `type="number"` 顯示數字鍵盤 | 同；iOS 會自動顯示數字鍵盤 |
| Auto-advance | JS 事件控制 | 原生 input 行為相同 |
| 貼上 | 桌面 Ctrl+V | 行動端長按貼上（Syncfusion 均支援） |

## 11. Content 指引　📋

- Placeholder 字元建議用 `"0"` 或 `"-"`（數字型）、`"X"` 或 `"_"`（文字型）；勿用空字串（避免格子看起來空白、無引導）。
- 驗證碼輸入頁面需附說明文字（如「請輸入 6 位驗證碼，有效期 5 分鐘」），不由 OTPInput 元件本身承載。
- Error 訊息：「驗證碼錯誤，請重新輸入」；剩餘次數需由外層 Toast / helper text 顯示。
- 詳細文案規範 → `ux-writer`。

## 12. API / Props　📋

> 以下 props 反映 Figma 設計層（Shape / Count / Size）與 Syncfusion 實際 API（`otp-input.vue` playground 確認）。

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `length` | number | 4 | 驗證碼位數（格數） |
| `value` | string | — | 預填值 |
| `type` | `'number'` \| `'text'` \| `'password'` | `'number'` | 輸入類型；`password` 會遮蔽字元 |
| `placeholder` | string | — | 所有格的 placeholder 字元（單字元或與 length 等長字串） |
| `separator` | string | — | 格間分隔字元（如 `"-"`、`"/"` |
| `styling-mode` | `'outlined'` \| `'filled'` \| `'underlined'` | `'outlined'` | 視覺樣式（對應 Figma Shape：Outlined / Filled / Line） |
| `disabled` | boolean | false | 停用整組 |
| `valueChanged` | event | — | 全部格填入後觸發，回傳 `{ value: string }` |
| `input` | event | — | 每格輸入時觸發 |
| `focus` | event | — | focus 進入時觸發 |
| `blur` | event | — | focus 離開時觸發 |

**Vue 3 使用範例（純 JS，無 TypeScript）**：

```vue
<script setup>
import { OtpInputComponent as EjsOtpinput } from '@syncfusion/ej2-vue-inputs'

const onComplete = (args) => {
  console.log('OTP complete:', args.value)
}
</script>

<template>
  <!-- Filled 樣式，6 格，破折號分隔 -->
  <ejs-otpinput
    :length="6"
    styling-mode="filled"
    separator="-"
    placeholder="0"
    :valueChanged="onComplete"
  />
</template>
```

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface §On-* §Outline §Border Width §Space §Radius §Typography
- 設計參考元件：`TextBox.md`（單格容器結構、Filled shape、border token 完全相同）
- Prototype 落地（值權威 / class 命名）：`prototyper/assets/app.css`（Syncfusion Material 3 theme override）
- Code：`@syncfusion/ej2-vue-inputs`（`OtpInputComponent as EjsOtpinput`）；playground 參考 `syncfusion-playground/app/pages/playground/otp-input.vue`
- Figma：`https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/` 節點 `15568-220`（元件集）

---

## Figma 補入紀錄（2026-06-29）

> Layer 1–5 依 `figma-to-component-doc.md` Token Matching workflow 執行。
> 來源：`get_variable_defs`（節點 15568-220，20 個變數）+ `get_design_context`（節點 16827:40126 Filled/Small/4格、15568:423 Outlined/Small/4格、15568:865 Line/Small/4格）+ `get_screenshot`（節點 15568-220）+ `get_metadata`（節點 15568-220，48 個 variant）。

**✅ 已確認對映（Figma 變數 → `athena-tokens.md`）**：`ColorSf/*`（transparent / outline / surface-variant / on-surface）、`DsColor/placeholder`、字型三件（FontSizeSf/TextSm|Md|Lg、FontWeightSf/Normal、FontFamilySf/Fontfamily）、圓角（Radius/RadiusSm）、`Radius/RadiusNone`（值 0，CSS 預設不需 token）。**全數 20 個變數找到對應，無漂移。**

**📐 結構性發現（Layer 4）**：
1. **三種 shape 各自獨立**：Filled = surface-variant 底色 + 底線 + 頂圓角；Outlined = transparent 底色 + 全框線 + 全圓角；Line（Underline）= transparent 底色 + 底線 + 無圓角。三套各有不同圓角策略。
2. **Line shape 非正方形**：寬固定 40px，高依 size 縮短（Small Line = 32px h × 40px w），與 Filled / Outlined 的正方形不同。
3. **separator 內嵌於元件**：Figma 每個 variant 都帶 separator，透過 `separator` prop 設值（傳空字串可隱藏）。
4. **variant 軸三維**：Size（4）× Count（4：3/4/5/6 格）× Shape（3）= 48 個 variant，全部在元件集節點 15568-220。

**⚠️ Token gap（非阻塞）**：
1. **格間 gap = 12px** → `{ds-space-padding-large}`（Default）✅ 有對應 token。
2. **Radius/RadiusNone = 0** → 無 `--ds-radius-none` token；0 為 CSS 預設值，無需補 token。
3. **整組 error state** → Figma 元件集無 error variant；error 視覺需外層客製（`cssClass` 或 helper text）。

**⬜ 待補（次要）**：
- Extra Small / Medium / Large 三個 size 的單格尺寸已從 `get_metadata` 的寬高維度推算；若需精確 padding 量測可再對各 size variant 執行 `get_design_context`。
- disabled / focus / error 單格態無對應 Figma 節點；已依 TextBox 類比與 Material 3 慣例推導，標 📋。
- Syncfusion `ejs-otpinput` 是否自動加每格 `aria-label`，待查 Syncfusion 原生 a11y 文件確認。

**層級判定根據**：OTPInput 包含整組複合互動邏輯（自動跳格、貼上解析、整組 `valueChanged` 事件、多 variant 軸×48）→ **Full 層**。
