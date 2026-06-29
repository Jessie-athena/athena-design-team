---
name: MaskedTextBox
category: 輸入 — 文字
tier: lite
status: ✅ 已採用
authority: ERP CLAUDE.md（Syncfusion MaskedTextBox, @syncfusion/ej2-vue-inputs）
figma-node: JhcpyIEEzSChcEXMRJUiIm/22828-503206   # 🎨 FAI2 Library · Input Mask 元件集
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs（node 22828-503206，42 vars）+ get_design_context（node 22828-504068，Filled/Small/Default/Date）+ get_screenshot（元件集 22828-503206）
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺值一律 token-reference；raw px 量測值指向值權威 `prototyper/assets/app.css`，不在此重印。
> §3–§5 已用 Figma `get_variable_defs` + `get_design_context`（節點 22828-504068，Filled / Small / Default / Date）+ `get_screenshot`（元件集）校準；token gap 見文末「Figma 補入紀錄」。
> 容器底層與 TextBox 高度共用；相同 token 以 **「同 TextBox」** 標注，差異處完整展開。

## 1. 概述　📋

格式遮罩文字輸入框。採 Material 3 **Filled** 樣式（surface-variant 底色 + 底線），與 TextBox 容器結構完全一致。遮罩由 `mask` prop 定義固定格式字串，使用者每個字元位置依序填入，不允許跳位或違反格式的輸入。

**何時使用**：需要輸入具有固定格式規律的資料欄位——統一編號、電話號碼、信用卡號、日期格式（如 `DD/MM/YYYY`）等。未填入的位置以遮罩佔位符顯示，提示使用者預期格式。  
**何時不用**：自由文字 → `TextBox`；純數值 / 金額 / 步進 → `NumericTextBox`；需從選項選取日期 → `DatePicker`；有限選項 → `DropDownList`。

---

## 2. Anatomy　🎨

> `get_design_context`（節點 22828-504068，Size=Small / State=Default / Shape=Filled / Template=Date）結構樹

```
InputMask（vertical stack）
├── label-row?（horizontal, gap = 2px = {ds-space-margin-extra-small}）
│   ├── paragraph-container（label-text + required-asterisk? 同行）
│   └── label-icon?（info-circle, 11×11）
├── input-container（h = 40px，Filled 底線式；同 TextBox 容器）
│   ├── left-content（horizontal, gap = 6px）
│   │   ├── left-icon?（16×16, lock 預設，opt）
│   │   └── text-stack（vertical）
│   │       ├── label-row?（12px 標籤，可選顯示）
│   │       └── mask-value（date 預設 "--/--/----"，placeholder 色）
│   └── right-content
│       └── clear-icon-btn?（32×32, opt）
└── helper-row?（h = 20px，帶 28px right spacer）
    ├── helper-text（pl = {ds-space-padding-large}）
    └── alert-icon?（circle-check, 10×10, opt）
```

> **與 TextBox anatomy 的差異**：
> - 右側控制區：無 right-icon-btn（TextBox 有）；只保留 clear-icon-btn（可選）
> - 無 spinner / arrow（不同於 NumericTextBox）
> - mask-value 顯示遮罩格式字串（未填入時為 `--/--/----` / `----/----/----/----` 等），使用 `{ds-color-placeholder}` 字色
> - `template` 決定遮罩格式與對應佔位字串（Date / Card Number / Phone Number）

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── 尺寸（Size: Small）──────────────────────────────────────────────────────
height_small:         40px                                 # 🎨 量測值；同 TextBox（Medium = 48px，Large = 56px，Extra Small = 32px）
padding_left:         10px                                 # 🎨 量測值，無 {ds-space-*} token；值見 app.css；同 TextBox
inner_gap:             6px                                 # 🎨 left-icon ↔ text-stack 量測值，無 token；值見 app.css；同 TextBox
icon_size:            16px                                 # 🎨 left-icon 量測值；同 TextBox
clear_btn_size:       32px                                 # 🎨 clear-icon 按鈕框；同 TextBox icon_btn_size
clear_btn_radius:     "{ds-radius-2extra-large}"           # 🔗 16px（ripple 圓形）；同 TextBox
helper_row_height:    20px                                 # 🎨 量測值；同 NumericTextBox
helper_padding_left:  "{ds-space-padding-large}"           # 🔗 12px（Default）；同 NumericTextBox
helper_padding_top:   "{ds-space-padding-extra-small}"     # 🔗 4px（Default）；同 NumericTextBox
label_input_gap:      "{ds-space-margin-small}"            # 🔗 4px（Default）/ 8px（Comfortable）；同 TextBox

# ── 容器外形（Shape: Filled）── 同 TextBox ───────────────────────────────────
container_bg:         "{color-sf-surface-variant}"         # 🔗 rgb(237 240 247)；同 TextBox
radius_top:           "{ds-radius-small}"                  # 🔗 4px（top-left + top-right）；同 TextBox
radius_bottom:        0                                    # 🎨 底無圓角（Filled 底線式）；同 TextBox
border_side:          bottom only                          # 🎨 Filled 底線式；同 TextBox
border_width_default: "{ds-borderwidth-small}"             # 🔗 1px（default / rest 態）；同 TextBox
border_width_active:  "{ds-borderwidth-medium}"            # 🔗 2px（focus / validation 態）；同 TextBox
border_color_default: "{color-sf-outline}"                 # 🔗 rgb(127 137 150)；同 TextBox

# ── 標籤文字 ── ⚠️ 12px（同 NumericTextBox，不同於 TextBox 14px）────────────
label_color:          "{color-sf-on-surface-variant}"      # 🔗 rgb(60 74 91)；同 TextBox
label_font_size:      "{font-size-sf-text-sm}"             # 🔗 12px（Label/12pt/Regular）← TextBox 為 14px
label_font_weight:    "{font-weight-sf-normal}"            # 🔗 400
label_line_height:     1.3                                 # 🎨 Label/12pt/Regular
label_letter_spacing:  0.1px                               # 🎨（同 NumericTextBox）
required_color:       "{color-sf-danger}"                  # 🔗 rgb(244 73 62)；同 TextBox

# ── 遮罩值 / 佔位顯示 ── MaskedTextBox 核心差異 ──────────────────────────────
mask_placeholder_color: "{ds-color-placeholder}"           # 🔗 rgb(103 113 126)（未輸入時的遮罩格式字串色；等同 placeholder）
mask_content_color:     "{color-sf-on-surface}"            # 🔗 rgb(15 23 42)（使用者輸入後的字元色）
content_font_size:    "{font-size-sf-text-md}"             # 🔗 14px（Body Content/14pt/Regular）；同 TextBox
content_font_weight:  "{font-weight-sf-normal}"            # 🔗 400；同 TextBox
content_line_height:   1.5                                 # 🎨；同 TextBox
content_letter_spacing: 0.24px                             # 🎨；同 TextBox

# ── 輔助文字 ── 同 TextBox ────────────────────────────────────────────────
helper_color:         "{color-sf-on-surface-variant}"      # 🔗；同 TextBox
helper_font_size:     "{font-size-sf-text-sm}"             # 🔗 12px；同 TextBox
helper_font_weight:   "{font-weight-sf-normal}"            # 🔗 400；同 TextBox
helper_line_height:    1.3                                 # 🎨；同 TextBox
helper_letter_spacing: 0.1px                               # 🎨；同 TextBox

font_family:          "{font-family-sf-fontfamily}"        # 🔗 Roboto（EN）/ Noto Sans TC（ZH）；同 TextBox
```

---

## 4. Variants　🎨🔗

> Figma 元件集（節點 22828-503206）暴露的 variant props（`get_metadata` 結構樹抽取）：

```yaml
shape:
  filled:   { bg: "{color-sf-surface-variant}", border_side: bottom only }   # 🔗 ERP 標準（同 TextBox）
  outlined: { bg: "{color-sf-surface}", border_side: all 4 sides }            # 🎨 Full border，非 ERP 標準
  line:     { bg: "{color-sf-transparent}", border_side: bottom only }        # 🎨 無背景純底線，非 ERP 標準

# ERP 使用 Shape=Filled；outlined / line 為 Syncfusion 庫提供但非 Athena ERP 規範

size:
  extra_small: { height: 24px }    # 🎨 Line=24px；Outlined/Filled=32px
  small:       { height: 32px }    # 🎨 Line=32px；Outlined/Filled=40px（ERP 標準）
  medium:      { height: 36px }    # 🎨 Line=36px；Outlined/Filled=48px
  large:       { height: 40px }    # 🎨 Line=40px；Outlined/Filled=56px

template:
  date:        { mask_format: "--/--/----",           hint: "DD/MM/YYYY 等格式視 mask prop 而定" }   # 🎨 Figma 預設
  card_number: { mask_format: "----/----/----/----",  hint: "16 碼信用卡號" }                         # 🎨
  phone_number:{ mask_format: 依 mask prop 設定,       hint: "本地或國際電話格式" }                   # 📋
```

> **注意**：`template` 為 Figma 設計層的示範 variant，對應 Syncfusion 的 `mask` prop 格式字串（如 `"00/00/0000"` 或 `"9999-9999-9999-9999"`）；實作時依需求傳入正確的 `mask` 字串，不受限於三種範本。

---

## 5. States　🎨🔗

> 容器底線寬度：default → `{ds-borderwidth-small}`（1px）；focus / validation → `{ds-borderwidth-medium}`（2px）。
> MaskedTextBox Figma 變數集包含 `$warning`（`#F79009`），與 TextBox 同，**可支援 warning 狀態**（此點優於 NumericTextBox）。

```yaml
default:                                               # 🎨 node 22828-504068 量測
  container_bg: "{color-sf-surface-variant}"           # 🔗
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}"   # 🔗 1px
  mask_display: "{ds-color-placeholder}"               # 🔗 遮罩格式字串以 placeholder 色顯示
  text: "{color-sf-on-surface}"                        # 🔗 使用者輸入字元色
  label: "{color-sf-on-surface-variant}"               # 🔗

hover:                                                 # 📋（Figma 含 hover variant；視覺邏輯同 TextBox）
  container_bg: "{color-sf-surface-variant}"           # 🔗 底色不變
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}"   # 🔗（hover 不加粗，同 TextBox）

focus:                                                 # 🔗（Figma $primary 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-primary}"  # 🔗 2px rgb(40 119 238)；同 TextBox
  label: "{color-sf-primary}"                          # 📋 Material 3 focus label 啟動色

info:                                                  # 🎨（Figma $border-info / $info 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-info}"     # 🔗 2px rgb(46 144 250)

success:                                               # 🎨（Figma $border-success 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-success}"  # 🔗 2px rgb(18 183 106)；同 TextBox

warning:                                               # 🎨（Figma $border-warning / $warning 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-warning}"  # 🔗 2px rgb(247 144 9)；同 TextBox（NumericTextBox 無此態）

error:                                                 # 🎨（Figma $border-error / $danger 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-error}"    # 🔗 2px rgb(244 73 62)；同 TextBox
  label: "{color-sf-error}"                            # 📋
  helper: "{color-sf-error}"                           # 📋

filled:                                                # 📋 Figma State=Filled（使用者已輸入值的靜止態）
  # 視覺同 default，但 mask_display 字元色改為 {color-sf-on-surface}（非 placeholder 色）

read_only:                                             # 📋（Figma State=Read Only）
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}"   # 🔗 1px，保留底線但不可編輯
  # 代碼使用 `readonly` 屬性（非 `disabled`）

disabled:                                              # 📋（Figma State=Disabled）
  text: "{color-sf-on-surface-opacity38}"              # 🔗 rgb(15 23 42 / 0.38)
  mask_display: "{color-sf-on-surface-opacity38}"      # 🔗（遮罩格式字串同步淡化）
  container_bg: "{color-sf-surface-variant}"           # 🔗（底色保留）
```

---

## 9. a11y　📋

- `<label>` 透過 `for` / `id` 與 input 關聯；`required` 欄位加 `aria-required="true"`。
- `pattern` 屬性：Syncfusion `<ejs-maskedtextbox>` 的 `mask` prop 對應 HTML `pattern`，瀏覽器驗證反饋；submit 時補 `aria-invalid="true"` + `aria-describedby` 指向 helper text。
- **輸入格式說明需可見**：遮罩本身（如 `--/--/----`）提供格式提示，但仍建議搭配 label 或 helper text 說明預期格式（如「日期格式 DD/MM/YYYY」），避免僅靠佔位符傳達。
- `aria-label`：若 label 不可見（純 icon 場景），必填 `aria-label` 明確說明「統一編號輸入」/ 「電話號碼輸入」等預期格式。
- 使用者輸入後每個字元立即就位（字元逐位插入），異常格式需透過 helper text 或 toast 提示，不可僅依賴色彩狀態。
- Mobile：input 容器 40px（Small）滿足 min touch target；clear button 32×32 可加 padding 補滿 44px 點擊區。

---

## 12. API / Props　📋

> 以下 props 反映 Figma 設計層（TypeScript interface 從 `get_design_context` 抽取）。Syncfusion 實際 API 詳 playground `maskedtextbox.vue`。

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `mask` | string | `"00/00/0000"` 等 | **核心 prop**：遮罩格式字串。`0`=數字、`9`=選填數字、`L`=字母、`A`=字母/數字、`&`=任意；詳 Syncfusion MaskedTextBox API |
| `template` | "Date" / "Card Number" / "Phone Number" | "Date" | Figma 示範 variant；實際依 `mask` 控制，template 僅影響 Figma 設計展示 |
| `label` | boolean | false | 是否顯示 label（內嵌於 input 上方） |
| `label1` | string | "Label" | label 文字 |
| `labelIcon` | boolean | false | 顯示 label 右側 info icon |
| `required` | boolean | false | 顯示必填 `*` |
| `date` | string | "--/--/----" | 遮罩格式字串預設顯示（未輸入時的佔位符外觀）；於 Figma 用作示範值 |
| `leftIcon` | boolean | false | 顯示左側 icon（預設 lock） |
| `leftIcon1` | slot | — | 自訂左側 icon |
| `clearIcon` | boolean | false | 顯示清除（×）按鈕 |
| `alertMessage` | boolean | false | 顯示 helper / alert text |
| `alertMessage1` | string | "Helping text" | helper 文字內容 |
| `alertIcon` | boolean | false | 顯示 helper 區域 alert icon |
| `shape` | "Filled" / "Outlined" / "Line" | "Filled" | 輸入框樣式；ERP 固定 Filled |
| `size` | "Extra Small" / "Small" / "Medium" / "Large" | "Small" | 尺寸 |
| `state` | "Default" / "Hover" / "Focus" / "Active" / "Filled" / "Read Only" / "Disabled" / "Info" / "Success" / "Warning" / "Error" | "Default" | 互動與驗證狀態 |

---

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface §On-* §Outline §Status §Border Width §Space §Radius §Typography §`ds-color-placeholder`
- 容器 / 底線 / 佔位文字 token 對齊：見 `components/TextBox.md`（共用 Filled 底線容器）；label 12px 對齊：見 `components/NumericTextBox.md`
- Prototype 落地（值權威 / class 命名）：`prototyper/assets/app.css`
- Code：`@syncfusion/ej2-vue-inputs`（`<ejs-maskedtextbox>`）；playground 參考 `syncfusion-playground/app/pages/playground/maskedtextbox.vue`
- Figma：`https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/` 節點 `22828-503206`（元件集）/ `22828-504068`（Filled Small Default Date 量測節點）

---

## Figma 補入紀錄（2026-06-29）

> Layer 1（`get_variable_defs`）+ Layer 4（`get_screenshot` + `get_metadata`）+ `get_design_context`（節點 22828-504068）依 Token Matching 五層工作流程執行。

**✅ 已確認對映（Figma 變數 → `athena-tokens.md`）**（42 變數）：
- 色彩：`DsColor/placeholder` → `{ds-color-placeholder}`；`ColorSf/surface-variant` → `{color-sf-surface-variant}`；`ColorSf/on-surface` → `{color-sf-on-surface}`；`ColorSf/on-surface-variant` → Figma 命名 `#3C4A5B` 即 `{color-sf-on-surface-variant}`；`ColorSf/outline` → `{color-sf-outline}`；`ColorSf/primary` / `$primary` → `{color-sf-primary}`；`ColorSf/info` / `$border-info` → `{color-sf-info}`；`ColorSf/success` / `$border-success` → `{color-sf-success}`；`ColorSf/warning` / `$border-warning` → `{color-sf-warning}`；`ColorSf/error` / `$border-error` → `{color-sf-error}`；`ColorSf/danger` → `{color-sf-danger}`；`ColorSf/surface` / `BG/$content-bg-color` → `{color-sf-surface}`；`ColorSf/transparent` / `BG/$transparent` → `{color-sf-transparent}`
- 字型：`FontFamilySf/Fontfamily` → `{font-family-sf-fontfamily}`；`FontWeightSf/Normal` → `{font-weight-sf-normal}`；`FontSizeSf/TextSm` → `{font-size-sf-text-sm}`；`FontSizeSf/TextMd` → `{font-size-sf-text-md}`；`FontSizeSf/TextLg` → `{font-size-sf-text-lg}`；font style tokens（Label/12pt、14pt、16pt；Body Content/12pt、14pt、16pt）全數命中
- 圓角：`Radius/RadiusSm` → `{ds-radius-small}`；`Radius/RadiusNone` → CSS 預設 0
- 框線：`Border/$border` → `{color-sf-outline}`（值相同）
- **42 個變數全數找到對應，無漂移。**

**✅ 與 TextBox / NumericTextBox 的主要差異確認**：
1. **Template / mask format**：MaskedTextBox 特有四維 variant 軸（Shape × Size × State × Template）；Template=Date/Card Number/Phone Number 僅為 Figma 展示，實際由 Syncfusion `mask` prop 控制
2. **遮罩佔位符色 = `{ds-color-placeholder}`**：與一般 placeholder 完全相同 token，無差異；視覺上以格式字串（`--/--/----`）區別，而非特殊色
3. **Label 12px（同 NumericTextBox，非 TextBox 的 14px）**：`Label/12pt/Regular` 確認
4. **Warning 態存在（同 TextBox）**：Figma 變數集有 `$warning` / `$border-warning`（不同於 NumericTextBox 缺 warning）
5. **無 right-icon-btn**：TextBox 有獨立 right-icon slot；MaskedTextBox 右側只有 clear-icon（可選）；無 NumericTextBox 的 spinner
6. **Info 態存在**：Figma 有 State=Info + `$border-info` / `$info` 變數；TextBox 及 NumericTextBox 設計文件未記錄此態（回報：可能所有輸入元件均有，待統一補入）

**⚠️ token gap（非阻塞，已標 🎨 量測值）**：
1. **`padding_left: 10px`**：`athena-tokens.md` 無 10px 間距 token（scale 跳 8→12px）→ 值見 `prototyper/assets/app.css`；同 TextBox / NumericTextBox
2. **`inner_gap: 6px`**：同上，scale 無 6px → 值見 app.css；同 TextBox

**⬜ 待補（次要）**：
- Size = Extra Small / Medium / Large 的 Filled 高度量測（本次量測 Small = 40px；metadata 顯示 Medium Filled = 48px、Large Filled = 56px、Extra Small Filled = 32px，已列於 §4，待 `get_design_context` 個別確認）
- Hover 態底線色是否變化（截圖可見 hover variant 存在，但容器色未明確區分，推論同 TextBox 不加粗）
- Focus label 色為 `{color-sf-primary}` 為 Material 3 推論，待截圖驗證
- Info 態跨元件一致性：建議 TextBox.md / NumericTextBox.md 補入 info 態記錄
