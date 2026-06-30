---
name: MultiColumnComboBox
category: 輸入 — 選擇
tier: full
status: ✅ 已產出
authority: ERP CLAUDE.md（Syncfusion MultiColumnComboBox, @syncfusion/ej2-vue-dropdowns）
figma-node: JhcpyIEEzSChcEXMRJUiIm/22855-801537
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs（node 22855-801537）+ get_design_context（node 22855-802457）+ get_screenshot（node 22855-801537）
---

> 依 `../component-doc-schema.md`（Full 層）產出。三權威分工：**視覺值（尺寸 / 互動色 / overlay 實色）權威＝`prototyper/assets/app.css`（canonical CSS，產出時複製不重寫）**；**class 套用 / 行為細節＝`prototyper/profiles/erp-components/DataGrid.md`（用法權威，overlay grid 結構）**；**本檔＝契約**（what/why/token-reference/state/a11y）。
> §3–§5 已用 Figma `get_variable_defs`（元件集 22855-801537）+ `get_design_context`（Default 量測節點 22855-802457）+ `get_screenshot`（元件集）校準；overlay header / column grid 部分依 Syncfusion 文件 + DataGrid.md 推導（見文末 Figma 補入紀錄）。

---

## 1. 概述　📋

帶多欄 overlay 的組合框（ComboBox + 多欄 popup grid）。使用者在觸發器輸入文字即時過濾，展開的 overlay 以**多欄表格**呈現候選項，讓使用者同時看到多個維度（如：編號 / 品名 / 規格 / 庫存量）後再確認選取。選取後，trigger 顯示其中一個指定欄（`text` 欄）的值。

**何時使用**

- 候選項本身資訊量大，單欄名稱不足以區分（物料代號相似、需同時確認規格或庫存）。
- ERP 典型情境：商品 / 物料選擇（需同時顯示 **商品代號 + 品名 + 規格/規格說明 + 可用庫存**）。
- 資料量較大，需要使用者先輸入關鍵字縮小範圍後再用視覺確認。

**何時不用**

| 替代方案 | 適用情境 |
|---|---|
| `DropDownList` | 選項少（< 20）、單欄名稱足以區分 → 無需多欄輔助 |
| `ComboBox` | 同上，但允許自由輸入不存在的值 |
| `AutoComplete` | 只需搜尋過濾文字、不選取物件 ID |
| `MultiSelect` | 需要多選（≥2 項）時 |

---

## 2. Anatomy　🎨

> 節點 22855-802457（`get_design_context`）結構樹；overlay grid 部分依 Syncfusion runtime 推導（見文末 Figma 補入紀錄 §Overlay gap）。

```
MultiColumnComboBox（vertical stack, gap = {ds-space-margin-small}）
├── label-row?（horizontal, gap = 2px = {ds-space-margin-extra-small}）
│   ├── label-text
│   ├── required-asterisk?（*）
│   └── label-icon?（circle-info, 11×11）
├── trigger-container（h = 40px，Filled 底線式）
│   ├── left-icon?（16×16, opt）
│   ├── text-area（placeholder / selected-value；overflow-clip）
│   ├── clear-icon-btn?（32×32，可選，`clearIcon` prop 控制）
│   │   └── close-icon（16×16）
│   └── arrow-down-btn（32×32，固定，不可省略）
│       └── arrow-down-icon（16×16）
└── helper-row?（horizontal, gap = 4px = {ds-space-margin-small}）
    ├── helper-text
    └── alert-icon?（circle-check, 10×10, opt）

── 展開 overlay ─────────────────────────────────────────────────────
overlay-panel（position: absolute, z-index: 1000, shadow）
  ├── overlay-header（固定表頭，欄名 row）
  │   └── th × N（每欄欄名）
  └── overlay-body（可捲動 grid rows）
      └── tr × M（每列候選項，多欄 cell）
```

> **與 DropDownList 的 Anatomy 差異**：
> 1. `clearIcon` prop 存在（32×32 btn，`clearIcon = false` 預設隱藏）——DropDownList Figma 層無此槽位。
> 2. overlay 為多欄 grid（帶表頭），非單欄選項列表。
> 3. trigger placeholder 預設為「Select any product」反映 ERP 商品選擇語境。

---

## 3. 視覺規格 Tokens　🎨🔗

### Trigger（觸發器）

```yaml
# ── 尺寸 ─────────────────────────────────────────────────────────────────────
height:               40px                                # 🎨 量測值（size: Small）；值見 app.css
default_width:        240px                               # 🎨 量測值（Figma default）；實際由父層決定
padding_left:         10px                                # 🎨 量測值，無 {ds-space-*} 對應；值見 app.css
text_area_px_padding: 4px                                 # 🎨 text-area 左右各 4px；值見 app.css
icon_gap:             6px                                 # 🎨 left-icon ↔ text-area 量測值；值見 app.css
label_input_gap:      "{ds-space-margin-small}"           # 🔗 4px（Default）/ 8px（Comfortable）
helper_gap:           "{ds-space-margin-small}"           # 🔗 同上
icon_size:            16px                                # 🎨 left-icon / arrow / close icon 量測值
clear_btn_size:       32px                                # 🎨 close-icon 按鈕框
arrow_btn_size:       32px                                # 🎨 arrow-down 按鈕框（固定）
btn_radius:           "{ds-radius-2extra-large}"          # 🔗 16px（ripple 圓形）

# ── 容器外形（shape: Filled）────────────────────────────────────────────────
container_bg:         "{color-sf-surface-variant}"        # 🔗 rgb(237 240 247)
radius_top:           "{ds-radius-small}"                 # 🔗 4px（top-left + top-right）
radius_bottom:        0                                   # 🎨 底線式無圓角（無 token）
border_side:          bottom only                         # 🎨 Filled 特徵：僅底邊框
border_width_default: "{ds-borderwidth-small}"            # 🔗 1px
border_width_active:  "{ds-borderwidth-medium}"           # 🔗 2px（focus / validation 態）
border_color_default: "{color-sf-outline}"                # 🔗 rgb(127 137 150)

# ── 標籤 ──────────────────────────────────────────────────────────────────────
label_color:          "{color-sf-on-surface-variant}"     # 🔗 rgb(60 74 91)
label_font:           "{font-size-sf-text-md} / {font-weight-sf-normal}"   # 🔗 14px / 400
label_line_height:    1.3                                 # 🎨 Label/14pt/Regular
required_color:       "{color-sf-danger}"                 # 🔗 rgb(244 73 62)
required_font_size:   "{font-size-sf-text-sm}"            # 🔗 12px

# ── 輸入文字 ───────────────────────────────────────────────────────────────────
placeholder_color:    "{ds-color-placeholder}"            # 🔗 rgb(103 113 126)
selected_value_color: "{color-sf-on-surface}"             # 🔗 rgb(15 23 42)
content_font:         "{font-size-sf-text-md} / {font-weight-sf-normal}"   # 🔗 14px / 400
content_line_height:  1.5                                 # 🎨 Body Content/14pt/Regular
content_tracking:     0.24px                              # 🎨 Body Content/14pt/Regular

# ── 輔助文字 ───────────────────────────────────────────────────────────────────
helper_color:         "{color-sf-on-surface-variant}"     # 🔗 rgb(60 74 91)
helper_font:          "{font-size-sf-text-sm} / {font-weight-sf-normal}"   # 🔗 12px / 400
helper_line_height:   1.3                                 # 🎨 Label/12pt/Regular
helper_tracking:      0.1px                               # 🎨

# ── 圖示與字型 ─────────────────────────────────────────────────────────────────
icon_color:           "{color-sf-on-surface-variant}"     # 🔗 rgb(60 74 91)
arrow_icon:           "{color-sf-on-surface-variant}"     # 🔗 同上
font_family:          "{font-family-sf-fontfamily}"       # 🔗 Roboto（EN）/ Noto Sans TC（ZH）
```

### Overlay Panel（多欄 dropdown）

```yaml
# overlay panel 整體
overlay_bg:           "{color-sf-surface}"                # 🔗 rgb(255 255 255)
overlay_shadow:       "{ds-shadow-md}"                    # 🔗 dropdown / menu 標準提升階
overlay_radius:       "{ds-radius-small}"                 # 🔗 4px（依 DropDownList overlay 慣例）
overlay_border:       "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗 1px（依推導）
overlay_min_width:    "trigger 寬度（自動對齊）"            # 📋 Syncfusion 預設

# ── overlay header（⚠️ Figma overlay 未補入，以下依 Syncfusion + DataGrid 推導） ──────
overlay_header_bg:    "見 app.css"                        # 📋 無單一 token；與 DataGrid 表頭同族
                                                          #    primary 5% 疊白後實色，值見 app.css
overlay_header_font:  "{font-size-sf-text-md} / {font-weight-sf-medium}"   # 🔗 14px / 500（表頭加粗）
overlay_header_color: "{color-sf-on-surface-variant}"     # 🔗 rgb(60 74 91)
overlay_header_border_bottom: "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗 1px 分隔

# ── overlay rows ────────────────────────────────────────────────────────────
overlay_row_height:   "見 app.css"                        # 🎨 量測值待補入（Figma overlay 未呈現）
overlay_row_font:     "{font-size-sf-text-md} / {font-weight-sf-normal}"   # 🔗 14px / 400
overlay_row_fg:       "{color-sf-on-surface}"             # 🔗 rgb(15 23 42)
overlay_row_padding:  "{ds-space-padding-extra-large}"    # 🔗 水平 16px（對齊 DataGrid cell-pad）
overlay_row_border:   "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗 列間分隔

# ── overlay row 互動態（無單一 token，以下為半透明語意描述）─────────────────────
overlay_row_hover:    "primary 6% 疊層（rgba primary 0.06）"   # 📋 與 DataGrid 一致；字面值見 app.css
overlay_row_selected: "{color-sf-primary-opacity-14}"          # 🔗 rgb(40 119 238 / 0.14)
overlay_row_focused:  "primary 8% halo"                        # 📋 字面值見 app.css
```

---

## 4. Variants　🎨🔗

> Figma 元件集（節點 22855-801537）暴露的 variant props（`get_design_context` 抽取）。

```yaml
shape:
  filled:                                                 # 🎨 唯一 shape（本節點）
    bg: "{color-sf-surface-variant}"                      # 🔗 底色
    border_side: bottom only                              # 🎨 Filled 底線式

size:
  small: { height: 40px }                                 # 🎨 量測確認

# ── 右側 icon 行為 ─────────────────────────────────────────────────────────────
arrow_down:
  static: always visible                                  # 📋 固定顯示，不可省略，非 prop 控制
  icon: arrow-down（material-symbols:arrow-drop-down）    # 📋 Iconify 對應
  btn_bg: "{color-sf-transparent}"                        # 🔗 靜態態透明

clear_icon:
  default: hidden（clearIcon = false）                    # 🎨 Figma prop 確認
  visible: close icon 32×32 btn（clearIcon = true）       # 🎨 Figma prop 確認
  note: "與 DropDownList 差異——DropDownList Figma 層無此 prop"  # 📋

# ── overlay columns（非 Figma variant，為 runtime 設定）─────────────────────
columns:
  type: Column[]（見 §12 API）                            # 📋
  width: "各欄可設 width / minWidth；無則自適應"            # 📋
  header: "可設 headerText；Figma overlay 未補入，外觀依 DataGrid 表頭 token"  # 📋
```

---

## 5. States　🎨🔗

> Trigger 底線寬度：default → `{ds-borderwidth-small}`（1px）；focus/validation → `{ds-borderwidth-medium}`（2px）。
> 所有 state 變數均來自 `get_variable_defs`（節點 22855-801537），**$border-warning 已出現**（與 DropDownList 差異）。

### Trigger 狀態

```yaml
default:                                                  # 🎨 node 22855-802457 量測
  container_bg: "{color-sf-surface-variant}"              # 🔗
  border_bottom: "{ds-borderwidth-small} {color-sf-outline}"    # 🔗 1px rgb(127 137 150)
  placeholder: "{ds-color-placeholder}"                   # 🔗
  label: "{color-sf-on-surface-variant}"                  # 🔗
  arrow_icon: "{color-sf-on-surface-variant}"             # 🔗

focus:                                                    # 🔗（Figma $primary 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-primary}"   # 🔗 2px rgb(40 119 238)
  label: "{color-sf-primary}"                             # 📋 Material 3 focus label 啟動色

success:                                                  # 🎨（Figma $border-success 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-success}"   # 🔗 2px rgb(18 183 106)

warning:                                                  # 🎨（Figma $border-warning 確認，本節點存在）
  border_bottom: "{ds-borderwidth-medium} {color-sf-warning}"   # 🔗 2px rgb(247 144 9)

error:                                                    # 🎨（Figma $border-error / $danger 確認）
  border_bottom: "{ds-borderwidth-medium} {color-sf-error}"     # 🔗 2px rgb(244 73 62)
  label: "{color-sf-error}"                               # 📋
  helper: "{color-sf-error}"                              # 📋

disabled:                                                 # 📋（Material 3 慣例 + Icon/$icon-color-disabled 推導）
  text: "{color-sf-on-surface-opacity38}"                 # 🔗 38% opacity（Material 3）
  placeholder: "{color-sf-on-surface-opacity38}"          # 🔗
  container_bg: "{color-sf-surface-variant}"              # 🔗（底色保留，透明度由文字體現）
  arrow_icon: "{color-sf-on-surface-opacity38}"           # 🔗
```

### Overlay 資料生命週期（Full 層必含）

| 狀態 | 呈現 | 規則 |
|---|---|---|
| empty | 「無符合資料」文字（overlay 仍顯示，body 空白 + 中置訊息） | 不關閉 overlay |
| loading | Skeleton 欄列佔位（Syncfusion 內建 spinner；對齊 Carbon skeleton 原則） | 保留欄結構避免跳版 |
| error | 載入失敗訊息 + 重試入口 | 不靜默空白 |

---

## 6. Behavior 行為　📋

| 互動 | 行為 |
|---|---|
| 輸入文字 | 即時過濾 overlay rows（`filterType` 控制：`StartsWith` 預設；可改 `Contains`） |
| 輸入欄為空 | 顯示全部候選項（Syncfusion 預設） |
| 展開觸發 | 點 arrow-down btn、input click、`Alt+↓` |
| 收合觸發 | 選取確認（Enter / 點列）、點 overlay 外、Esc |
| 選取後 trigger 顯示 | 顯示 `fields.text` 欄的值（非整列 JSON），見 §12 |
| `clearIcon` 可見條件 | `clearIcon = true` 且有已選值時（Syncfusion 運行時邏輯） |
| 清除（×）按下 | 清空選取值，trigger 回 placeholder；不展開 overlay |
| 欄寬 | 各欄預設 `auto`；可透過 `columns[n].width` 指定 `px` 值（不建議 %，overlay 寬動態） |
| 搜尋欄位設定 | `filterType` + `fields.value` 決定過濾的欄；可指定多欄搜尋時配合 `query` 自訂 |
| 最大高度 | `popupHeight` 預設約 300px；超過 `overflow-y: auto` |
| trigger 寬 vs overlay 寬 | overlay 最小寬 = trigger 寬；columns 總寬超出時 overlay 自動更寬 |

---

## 9. a11y 無障礙　📋

- `<label>` 透過 `for` / `id` 與 input 關聯；`required` 欄位加 `aria-required="true"`。
- Error 態：`aria-invalid="true"` + `aria-describedby` 指向 helper text。
- Overlay 展開：trigger `aria-expanded="true"` + `aria-haspopup="listbox"`；overlay 根元素 `role="listbox"`。
- Overlay 表頭（欄名 row）：`role="row"` + 每格 `role="columnheader"`；各選項列 `role="option"`（讀屏器以欄名+值唸出）。
- 鍵盤（見 §8 整合版）：`Tab` 進入 → `Enter` / 點擊展開 → `↑↓` 移動候選列 → `Enter` 確認 → `Esc` 關閉。
- Focus ring：Syncfusion Material 3 主題 `{ds-shadow-focus-ring1}`（黑 3px + 白 1px 雙環）。
- Mobile min touch target：44×44（arrow/clear btn 32×32 需外補 padding）。
- 避免僅靠顏色傳遞狀態（error 態同時需 `aria-invalid` + 文字訊息）。

---

## 12. API / Props　📋

### Figma 設計層 props（`get_design_context` 抽取）

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `label` | boolean | true | 是否顯示 label |
| `label1` | string | "Label" | label 文字 |
| `labelIcon` | boolean | false | 顯示 label 右側 info icon |
| `required` | boolean | false | 顯示必填 `*` |
| `text` | string | "Select any product" | placeholder 文字 |
| `leftIcon` | boolean | false | 顯示左側 icon |
| `leftIcon1` | slot | — | 自訂左側 icon 內容 |
| `clearIcon` | boolean | false | 顯示清除（×）按鈕 |
| `alertMessage` | boolean | false | 顯示 helper / alert text |
| `alertMessage1` | string | "Helping text" | helper 文字內容 |
| `alertIcon` | boolean | true | 顯示 helper 區域的 alert icon |
| `shape` | "Filled" | "Filled" | 輸入框樣式（目前僅 Filled） |
| `size` | "Small" | "Small" | 尺寸 |
| `state` | "Default" | "Default" | 驗證狀態 |

> **與 DropDownList Figma props 差異**：`clearIcon` prop **存在**（DropDownList 無此 Figma prop）；`text` 預設值為 `"Select any product"` 而非 `"Select a vegetable"`。

### Syncfusion Runtime props（`<ejs-multicolumncombobox>`，`@syncfusion/ej2-vue-dropdowns`）

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `dataSource` | Array / Object | — | 資料來源 |
| `fields` | `{ text, value }` | — | 顯示欄與值欄的 key 映射 |
| `columns` | `Column[]` | — | **核心**：overlay 欄定義陣列（見下方「columns 設定」） |
| `value` | string / number | — | 目前選取值（v-model） |
| `placeholder` | string | — | 未選取時的 placeholder |
| `filterType` | "StartsWith" / "Contains" / "EndsWith" | "StartsWith" | 即時搜尋的比對方式 |
| `showClearButton` | boolean | false | 顯示清除（×）按鈕 |
| `popupHeight` | string | "300px" | dropdown 最大高度 |
| `popupWidth` | string | — | dropdown 寬度（不設則跟 trigger） |
| `enabled` | boolean | true | 啟用 / 停用 |
| `readonly` | boolean | false | 唯讀 |
| `noRecordsTemplate` | string | "No records found" | 無結果時顯示的訊息（空態文案） |

#### columns 設定（重點 prop）

`columns` 陣列每筆格式：

```js
{
  field: 'fieldName',      // 對應 dataSource 的 key
  header: '欄名',          // overlay 表頭顯示文字
  width: '120px',          // 欄寬（可選；不設則 auto）
}
```

**ERP 商品選擇範例（完整四欄）：**

```vue
<ejs-multicolumncombobox
  :dataSource="products"
  :fields="{ text: 'name', value: 'id' }"
  :columns="[
    { field: 'code',  header: '商品代號', width: '120px' },
    { field: 'name',  header: '品名',     width: '180px' },
    { field: 'spec',  header: '規格',     width: '120px' },
    { field: 'stock', header: '庫存量',   width: '80px'  },
  ]"
  placeholder="輸入商品代號或品名搜尋"
  filterType="Contains"
  :showClearButton="true"
/>
```

> `fields.text = 'name'`：選取後 trigger 顯示品名（非代號）。若需顯示代號，改 `text: 'code'`。

---

## 13. 關聯 Links　🔗

- Tokens：`../athena-tokens.md` §Surface §On-* §Outline §Primary §Status §Border Width §Space §Radius §Shadow §Typography §`--ds-*`
- Prototype 落地（值權威 / class 命名）：`prototyper/assets/app.css`（Syncfusion Material 3 theme override）
- Overlay grid 結構（表頭 / 列互動 / 欄寬 / 凍結）：`prototyper/profiles/erp-components/DataGrid.md`（用法權威；overlay grid 為 DataGrid 的視覺近親）
- Code：`@syncfusion/ej2-vue-dropdowns`（`<ejs-multicolumncombobox>`）
- Figma：`https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/` 節點 `22855-801537`（元件集）/ `22855-802457`（Filled Small Default 量測節點）
- 同族元件：`DropDownList.md`（trigger 外觀與 token 高度共用）；`DataGrid.md`（overlay header / row 視覺語言）

---

## Figma 補入紀錄（2026-06-29）

> Layer 1–4 依 Token Matching 五層工作流程執行。來源：`get_variable_defs`（節點 22855-801537，41 變數）+ `get_design_context`（節點 22855-802457）+ `get_screenshot`（元件集 22855-801537）。

### ✅ 已確認對映（Figma 變數 → `athena-tokens.md`）

`ColorSf/*`（surface-variant / surface / outline / on-surface-variant / on-surface / primary / success / warning / danger / error / transparent / on-surface-opacity38）、`DsColor/placeholder`、字型三件（FontSizeSf/TextMd|Sm|Lg、FontWeightSf/Normal、FontFamilySf/Fontfamily）、`Label/14pt/Regular`、`Body Content/14pt/Regular`、`Label/12pt/Regular`、`Body Content/12pt/Regular`、`Label/16pt/Regular`、`Body Content/16pt/Regular`、`DsSpace/Margin/Small`、`Radius/RadiusSm`、`Radius/RadiusNone`、`$primary`、`$success`、`$warning`、`$border-success`、`$border-warning`（✅ 本節點存在，解除 DropDownList gap）、`$border-error`、`$danger`。

**41 個變數全數找到對應，無漂移。**

### ✅ 與 DropDownList 差異確認

| 項目 | DropDownList | MultiColumnComboBox |
|---|---|---|
| `clearIcon` Figma prop | ❌ 無 | ✅ 有（default: false） |
| `$border-warning` in var set | ⚠️ 未出現 | ✅ 出現 |
| placeholder 預設 | "Select a vegetable" | "Select any product" |
| overlay | 單欄 listbox | **多欄 grid（含表頭）** |
| trigger 外觀 | 完全相同 | 完全相同（共用 Filled 族群 token） |
| Import 套件 | `@syncfusion/ej2-vue-dropdowns` | `@syncfusion/ej2-vue-dropdowns` |
| 元件名稱 | `<ejs-dropdownlist>` | `<ejs-multicolumncombobox>` |

### ⚠️ Token gap（非阻塞，已標 🎨 量測值）

1. **`padding_left: 10px`**：`athena-tokens.md` 無 10px 間距 token（scale 跳 8→12px）→ 與 DropDownList 同 gap；值見 `app.css`。
2. **`inner_icon_gap: 6px`**：同上，scale 無 6px；值見 `app.css`。
3. **`text_area_px_padding: 4px`**：text-area 左右各 4px（同 DropDownList）；值見 `app.css`。
4. **overlay header 背景色**：DataGrid 表頭使用「primary 5% 疊白後實色」——無單一 `athena-tokens.md` token，依 schema §2.2 不臆造；字面值來源 `prototyper/assets/app.css`。

### ⬜ Overlay gap（Figma 未補入，需另次補）

本次 `get_design_context` 取得節點為 trigger-only（22855-802457），**overlay panel（多欄 dropdown 展開態）的 Figma 節點 ID 未取得**。以下項目待補入：

- overlay panel 的 Figma 節點 ID
- overlay header 確實高度（量測值）
- overlay row 高度
- overlay 邊框 radius 實際值
- 欄間垂直分隔線（是否與 DataGrid `{color-sf-outline-variant}` 一致？）
- 各互動態（hover / selected / focused）的視覺截圖驗證

**暫行決策**：overlay 樣式依 Syncfusion Material 3 主題 + `DataGrid.md` 推導（§3 overlay section 已標 📋），待 Figma 補入後更新 §3 overlay 區段並更新 `last-synced`。

### ⬜ 待補（次要）

- size: Medium / Large 的 trigger height 量測（本節點僅確認 Small = 40px）
- overlay grid 的 Keyboard §8（Tab 在 overlay header cell 的 focus 行為、Enter 確認後 focus 還原）
- 跨平台 §10（App 窄螢幕的 overlay 替代佈局——全屏抽屜 vs popup）

### Variant 軸摘要（回答任務要求）

| 軸 | 值 |
|---|---|
| shape | Filled（唯一） |
| size | Small（唯一，Medium/Large 待補） |
| state | Default / Focus / Success / Warning / Error / Disabled |
| clearIcon | false（預設隱藏）/ true（顯示 × btn） |
| columns | runtime 設定（非 Figma variant，N 欄由 `columns[]` prop 決定） |
