---
name: MultiSelect
category: 輸入 — 選擇
tier: full
status: ✅ 已採用
authority: ERP CLAUDE.md（Syncfusion MultiSelect, @syncfusion/ej2-vue-dropdowns）
figma-node: JhcpyIEEzSChcEXMRJUiIm/22831-939764
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs（39 vars, node 22831-939764）+ get_screenshot + get_design_context（nodes 22831-940580 / 22831-940856）
---

> 依 `../component-doc-schema.md`（Full 層）產出。視覺值一律 token-reference；raw px 量測值指向值權威 `prototyper/assets/app.css`，不在此重印。
> §3–§5 已用 Figma `get_variable_defs` + `get_design_context`（節點 22831-940580 Small/Filled/Default 與 22831-940856 Small/Filled/Filled）校準；variant 軸確認見文末「Figma 補入紀錄」。

---

## 1. 概述　📋

多選下拉框。允許使用者從預定選項中選取**零至多個**項目；已選項以 **chip** 形式排列於 trigger 容器內，選取後 trigger 仍可繼續鍵入進行 type-ahead 篩選。

**何時使用**：需要多選的表單欄位（多部門選擇、多標籤篩選、多項目條件設定…）。  
**何時不用**：單選 → `DropDownList`；帶搜尋單選 → `ComboBox` / `AutoComplete`；選項數極少且同時可見 → radio group 或 checkbox list。

---

## 2. Anatomy　🎨

> `get_design_context`（節點 22831-940580, Size=Small / Shape=Filled / State=Default）結構樹

```
MultiSelect（vertical stack, gap = {ds-space-margin-small}）
├── label-row?（horizontal, gap = 2px = {ds-space-margin-extra-small}）
│   ├── label-text
│   ├── required-asterisk?（*）
│   └── label-icon?（circle-info, 11×11）
├── trigger-container（h = 40px Small / Filled 底線式）
│   ├── left-icon?（16×16, opt）
│   ├── chips-input-area（flex-row, overflow-clip, gap = {ds-space-padding-2extra-large}=8px chip 間距）
│   │   ├── chip[]（pill shape，h = 24px，可 0–N 個）
│   │   │   ├── chip-label（11px / Medium）
│   │   │   └── close-icon?（×，16×16，chip 右側；Filled 狀態下顯示）
│   │   └── type-ahead-input（placeholder "type..." 緊跟末尾 chip）
│   └── clear-all-btn?（32×32，container 右側，clearIcon prop）
└── helper-row?（horizontal, gap = {ds-space-margin-small}）
    ├── helper-text
    └── alert-icon?（circle-check, 10×10, opt）
```

> **與 DropDownList 的 Anatomy 差異**：
> - 右側無固定 `arrow-down-btn`（MultiSelect 為 `clearIcon` 可選清除鈕）
> - chips-input-area 取代單一 text-area，chips 與 type-ahead 輸入共存
> - Chip 為獨立槽位，帶 close icon（State=Filled 時顯示）

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── 尺寸（size: Small，最常用；Medium / Large 量測詳下方）─────────────────
height_small:              40px                                  # 🎨 量測值（Same as DropDownList Small）
height_medium:             48px                                  # 🎨 量測值（Figma Medium Filled = 68px 含 label；container ≈ 48px）
height_large:              56px                                  # 🎨 量測值（Figma Large Filled = 76px 含 label；container ≈ 56px）
padding_left:              10px                                  # 🎨 量測值，無 {ds-space-*} token；值見 app.css
inner_left_gap:            6px                                   # 🎨 left-icon ↔ chips-area；值見 app.css
chip_area_inner_px:        4px                                   # 🎨 chips-area 左右各 4px；值見 app.css
label_input_gap:           "{ds-space-margin-small}"             # 🔗 4px（Default）/ 8px（Comfortable）
helper_gap:                "{ds-space-margin-small}"             # 🔗 4px（Default）/ 8px（Comfortable）
chip_gap:                  "{ds-space-padding-2extra-large}"     # 🔗 8px（chip 間距）

# ── 容器外形（shape: Filled 預設）────────────────────────────────────────
container_bg:              "{color-sf-surface-variant}"          # 🔗 rgb(237 240 247)
radius_top:                "{ds-radius-small}"                   # 🔗 4px（top-left + top-right）
radius_bottom:             0                                     # 🎨 Filled 底線式，無底圓角
border_side:               bottom only                           # 🎨 Filled 特徵
border_width_default:      "{ds-borderwidth-small}"              # 🔗 1px
border_width_active:       "{ds-borderwidth-medium}"             # 🔗 2px（focus / validation）
border_color_default:      "{color-sf-outline}"                  # 🔗 rgb(127 137 150)

# ── Chip（選中項目膠囊）────────────────────────────────────────────────
chip_height:               24px                                  # 🎨 量測值
chip_radius:               "{ds-radius-10extra-large}"           # 🔗 1000px（全圓 pill）
chip_border_width:         "{ds-borderwidth-small}"              # 🔗 1px
chip_border_color:         "{color-sf-outline}"                  # 🔗 rgb(127 137 150)（default 態）
chip_bg:                   "{color-sf-transparent}"              # 🔗 透明（border-only chip）
chip_padding_left:         6px                                   # 🎨 pl-[2px] wrapper + pl-[4px] inner = 6px；值見 app.css
chip_padding_right:        8px                                   # 🎨 量測值；值見 app.css
chip_text_color:           "{color-sf-on-surface-variant}"       # 🔗 rgb(60 74 91)
chip_font_size:            "{font-size-sf-text-xs}"              # 🔗 11px（Label/11pt/Medium）
chip_font_weight:          "{font-weight-sf-medium}"             # 🔗 500
chip_line_height:          1.3                                   # 🎨 Label/11pt/Medium
chip_letter_spacing:       0.1px                                 # 🎨 Label/11pt/Medium

# ── Chip close icon（×）──────────────────────────────────────────────
chip_close_icon_size:      16px                                  # 🎨 量測值（State=Filled 時顯示）
chip_close_icon_color:     "{color-sf-on-surface-variant}"       # 🔗 rgb(60 74 91)

# ── type-ahead placeholder（chips 後方）──────────────────────────────
typeahead_placeholder_color: "{ds-color-placeholder}"            # 🔗 rgb(103 113 126)
typeahead_font_size:       "{font-size-sf-text-md}"              # 🔗 14px（Body Content/14pt/Regular）
typeahead_font_weight:     "{font-weight-sf-normal}"             # 🔗 400
typeahead_line_height:     1.5                                   # 🎨
typeahead_letter_spacing:  0.24px                                # 🎨

# ── 標籤文字 ──────────────────────────────────────────────────────────
label_color:               "{color-sf-on-surface-variant}"       # 🔗 rgb(60 74 91)
label_font_size:           "{font-size-sf-text-md}"              # 🔗 14px（Label/14pt/Regular）
label_font_weight:         "{font-weight-sf-normal}"             # 🔗 400
label_line_height:         1.3                                   # 🎨
label_letter_spacing:      0px                                   # 🎨
required_color:            "{color-sf-danger}"                   # 🔗 rgb(244 73 62)
required_font_size:        "{font-size-sf-text-sm}"              # 🔗 12px

# ── 輔助文字 ──────────────────────────────────────────────────────────
helper_color:              "{color-sf-on-surface-variant}"       # 🔗 rgb(60 74 91)
helper_font_size:          "{font-size-sf-text-sm}"              # 🔗 12px（Label/12pt/Regular）
helper_font_weight:        "{font-weight-sf-normal}"             # 🔗 400
helper_line_height:        1.3                                   # 🎨
helper_letter_spacing:     0.1px                                 # 🎨

# ── Clear-all 按鈕 ────────────────────────────────────────────────────
clear_btn_size:            32px                                   # 🎨 量測值（32×32，與 DropDownList arrow-btn 同框）
clear_btn_radius:          "{ds-radius-2extra-large}"            # 🔗 16px（ripple 圓形）
clear_btn_bg_default:      "{color-sf-transparent}"              # 🔗 透明

# ── 字型 ──────────────────────────────────────────────────────────────
font_family:               "{font-family-sf-fontfamily}"         # 🔗 Roboto（EN）/ Noto Sans TC（ZH）
```

---

## 4. Variants　🎨🔗

> Figma 元件集（節點 22831-939764）暴露三個 variant 軸，由 `get_metadata` 確認。

```yaml
# ── Shape 軸（3 種輸入框外形）────────────────────────────────────────
shape:
  Filled:                                                        # 🎨 ERP 主要用法（surface-variant 底色 + 底線）
    container_bg: "{color-sf-surface-variant}"
    border_side:  bottom only
    radius_top:   "{ds-radius-small}"
    radius_bottom: 0
  Outlined:                                                      # 🎨 全框線（四邊 border）
    container_bg: "{color-sf-surface}"                           # 🔗 白底
    border_side:  all sides
    border_radius: "{ds-radius-small}"                           # 🔗 四角均 4px
  Line:                                                          # 🎨 底線式（無底色，無圓角）
    container_bg: "{color-sf-transparent}"
    border_side:  bottom only
    radius: 0

# ── Size 軸（4 種，均含 label + helper）──────────────────────────────
size:
  Extra_Small: { container_h: 32px }                             # 🎨 量測值（Figma Extra Small Filled = 52px 含 label）
  Small:       { container_h: 40px }                             # 🎨 量測值（主要尺寸）
  Medium:      { container_h: 48px }                             # 🎨 量測值（Figma Medium Filled = 68px 含 label）
  Large:       { container_h: 56px }                             # 🎨 量測值（Figma Large Filled = 76px 含 label）

# ── State 軸（10 種，詳見 §5）────────────────────────────────────────
state:
  Default | Hover | Focus | Active | Filled | Read_Only | Disabled | Success | Warning | Error
  # State=Filled = 已選取一或多個項目（chips 顯示，close icon 出現）
  # 詳細 token override 見 §5
```

---

## 5. States　🎨🔗

> 底線寬度規則同 DropDownList / TextBox 族群：default → `{ds-borderwidth-small}`（1px）；focus/validation → `{ds-borderwidth-medium}`（2px）。
> Chip 狀態為獨立子層，見下方「Chip 狀態」段。

```yaml
# ── Trigger 容器狀態 ─────────────────────────────────────────────────
default:
  container_bg:   "{color-sf-surface-variant}"                   # 🔗
  border_bottom:  "{ds-borderwidth-small} {color-sf-outline}"    # 🔗 1px rgb(127 137 150)
  label:          "{color-sf-on-surface-variant}"                # 🔗
  typeahead:      "{ds-color-placeholder}"                       # 🔗（無 chips，顯示 placeholder）

hover:                                                           # 📋（Figma State=Hover，同族推導）
  container_bg:   "{color-sf-surface-variant}"                   # 🔗（底色不變）
  border_bottom:  "{ds-borderwidth-small} {color-sf-on-surface-variant}"  # 🔗（border 加深）
  # hover state-layer：加疊 {color-sf-on-surface-opacity8}（8% 疊層）

focus:                                                           # 🔗（Figma $primary 確認）
  border_bottom:  "{ds-borderwidth-medium} {color-sf-primary}"   # 🔗 2px rgb(40 119 238)
  label:          "{color-sf-primary}"                           # 📋 Material 3 focus label 啟動色

active:                                                          # 📋（下拉展開中，同 focus 視覺）
  border_bottom:  "{ds-borderwidth-medium} {color-sf-primary}"   # 🔗 2px（同 focus）
  label:          "{color-sf-primary}"                           # 🔗

filled:                                                          # 🎨（Figma State=Filled，已選 chips 顯示）
  container_bg:   "{color-sf-surface-variant}"                   # 🔗
  border_bottom:  "{ds-borderwidth-small} {color-sf-outline}"    # 🔗 1px（回到 default 邊框）
  chips:          visible（chip close icon 出現）

read_only:                                                       # 📋（Figma State=Read Only）
  container_bg:   "{color-sf-surface-variant}"                   # 🔗（底色保留）
  border_bottom:  "{ds-borderwidth-small} {color-sf-outline}"    # 🔗
  chip_close:     hidden（不可移除 chip）
  type_ahead:     hidden（不可繼續輸入）

disabled:                                                        # 📋（Material 3 慣例，Figma 確認 opacity38）
  container_bg:   "{color-sf-surface-variant}"                   # 🔗
  label:          "{color-sf-on-surface-opacity38}"              # 🔗 38%
  chip_text:      "{color-sf-on-surface-opacity38}"              # 🔗
  typeahead:      "{color-sf-on-surface-opacity38}"              # 🔗
  border_bottom:  "{ds-borderwidth-small} {color-sf-on-surface-opacity12}"  # 🔗 12%（disabled border）

success:                                                         # 🎨（Figma $border-success 確認）
  border_bottom:  "{ds-borderwidth-medium} {color-sf-success}"   # 🔗 2px rgb(18 183 106)

warning:                                                         # 🎨（Figma $border-warning 確認）
  border_bottom:  "{ds-borderwidth-medium} {color-sf-warning}"   # 🔗 2px rgb(247 144 9)

error:                                                           # 🎨（Figma $border-error 確認）
  border_bottom:  "{ds-borderwidth-medium} {color-sf-error}"     # 🔗 2px rgb(244 73 62)
  label:          "{color-sf-error}"                             # 📋
  helper:         "{color-sf-error}"                             # 📋

# ── Chip 子元素狀態 ──────────────────────────────────────────────────
chip_default:                                                    # 🎨（Figma State=Filled 量測）
  bg:             "{color-sf-transparent}"                       # 🔗 透明底
  border:         "{ds-borderwidth-small} {color-sf-outline}"    # 🔗 1px rgb(127 137 150)
  text_color:     "{color-sf-on-surface-variant}"                # 🔗
  close_icon:     "{color-sf-on-surface-variant}"                # 🔗

chip_hover:                                                      # 📋（同族 Material 3 state layer）
  bg_overlay:     "{color-sf-on-surface-opacity8}"               # 🔗 8% 疊層

chip_remove_hover:                                               # 📋（close icon hover）
  close_bg:       "{color-sf-on-surface-opacity12}"              # 🔗 12% 疊層（circle hover）
```

---

## 6. Behavior 行為　📋

| 行為 | 說明 |
|------|------|
| **選取** | 點擊 / 鍵盤確認 dropdown item 後，該項目以 chip 形式出現於 trigger 容器內；trigger 不關閉，type-ahead 仍可繼續輸入篩選 |
| **移除 chip** | 點擊 chip 右側 × icon → 該項目從已選清單移除；或從 dropdown item 再次勾選取消 |
| **Select All** | Dropdown 頂部「全選」選項 → 一次選取所有可見選項；再次點擊取消全選 |
| **Overflow（chips 超出 trigger 寬度）** | Chips 在 trigger 內以 flex-wrap 排列，trigger 高度自適應增高（非固定高度）；**不使用 +N more badge**（Figma 截圖確認，不截斷） |
| **Clear All** | `clearIcon` = true 時，trigger 右側出現 × 按鈕，點擊清空所有已選 chips |
| **Type-ahead 篩選** | 在 trigger 內鍵入文字即時篩選 dropdown 選項；chips 與輸入框共存同行 |
| **Dropdown 關閉** | 點擊 trigger 外部 / `Esc` 鍵 → 關閉 dropdown；已選 chips 保留 |
| **Read Only** | chips 顯示但不可新增 / 移除；type-ahead 輸入框隱藏；close icon 隱藏 |

---

## 9. a11y 無障礙　📋

- Trigger 元素使用 `role="combobox"`，`aria-haspopup="listbox"`，`aria-expanded="true/false"`
- `aria-multiselectable="true"` 宣告多選能力
- Dropdown 清單使用 `role="listbox"`；各選項 `role="option"`，已選項 `aria-selected="true"`
- Chips 區域使用 `role="group"` 或 `aria-label="已選項目"`；各 chip 使用 `role="option"` + `aria-selected="true"`
- Close icon（× 按鈕）需有 `aria-label="移除 {chip-label}"`（清楚指稱被移除項目）
- Clear All 按鈕需有 `aria-label="清除所有選取"`
- `required` 欄位加 `aria-required="true"`；Error 態加 `aria-invalid="true"` + `aria-describedby` 指向 helper text
- 鍵盤：`Tab` 進入 trigger → `Enter` / `Space` 展開 dropdown → `↑↓` 移動選項 → `Enter` / `Space` 勾選 / 取消 → `Esc` 關閉
- Chips 鍵盤：`←→` 在 chips 間移動焦點；`Delete` / `Backspace` 移除聚焦中 chip
- Focus ring 由 Syncfusion Material 3 主題提供；mobile min touch target：44×44（clear-all 按鈕 32×32 需外補 padding）

---

## 12. API / Props　📋

**Figma 設計層 props**（從 `get_design_context` TypeScript interface 抽取）

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `label` | boolean | true | 是否顯示 label |
| `label1` | string | "Label" | label 文字 |
| `labelIcon` | boolean | false | 顯示 label 右側 info icon |
| `required` | boolean | false | 顯示必填 `*` |
| `leftIcon` | boolean | false | 顯示左側 icon |
| `leftIcon1` | slot | — | 自訂左側 icon 內容 |
| `clearIcon` | boolean | false | 顯示清除所有（×）按鈕於 trigger 右側 |
| `alertMessage` | boolean | false | 顯示 helper / alert text |
| `alertMessage1` | string | "Helping text" | helper 文字內容 |
| `alertIcon` | boolean | true | 顯示 helper 區域的 alert icon |
| `alertIcon1` | slot | — | 自訂 alert icon |
| `shape` | "Filled" / "Outlined" / "Line" | "Filled" | 輸入框外形 |
| `size` | "Extra Small" / "Small" / "Medium" / "Large" | "Small" | 尺寸 |
| `state` | "Default" / "Hover" / "Focus" / "Active" / "Filled" / "Read Only" / "Disabled" / "Success" / "Warning" / "Error" | "Default" | 驗證 / 互動狀態 |

**Syncfusion Runtime props（`<ejs-multiselect>`，playground 確認）**

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `dataSource` | Array / Object | — | 下拉選項資料來源 |
| `fields` | Object | — | 物件資料來源的 `value` / `text` 欄位映射 |
| `value` | Array | — | 目前已選值陣列（v-model） |
| `placeholder` | string | — | 未選取時的佔位文字（type-ahead placeholder） |
| `mode` | "Default" / "Box" / "CheckBox" / "Delimiter" / "SortedTag" | "Default" | 選取項目呈現模式（`Default` = chip mode；`Delimiter` = 逗號分隔文字） |
| `showClearButton` | boolean | false | 顯示清除所有按鈕 |
| `selectAllText` | string | "Select All" | 全選文字 |
| `unSelectAllText` | string | "Unselect All" | 取消全選文字 |
| `showSelectAll` | boolean | false | 顯示全選選項 |
| `allowFiltering` | boolean | false | 啟用 type-ahead 篩選 |
| `filterBarPlaceholder` | string | — | 篩選框 placeholder |
| `enabled` | boolean | true | 啟用 / 停用元件 |
| `readonly` | boolean | false | 唯讀 |
| `popupHeight` | string | — | 下拉選單高度（如 "200px"） |
| `maximumSelectionLength` | number | — | 最大可選數量 |
| `closePopupOnSelect` | boolean | true | 每選一項後是否關閉 popup（多選建議 false） |

---

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface §On-* §Outline §Status §Border Width §Space §Radius §Typography §`--ds-*`
- Prototype 落地（值權威 / class 命名）：`prototyper/assets/app.css`（Syncfusion Material 3 theme override）
- Code：`@syncfusion/ej2-vue-dropdowns`；playground 參考 `syncfusion-playground/app/pages/playground/`（對應 `multiselect.vue` 或 `dropdownlist.vue`）
- Figma：`https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/` 節點 `22831-939764`（元件集）/ `22831-940580`（Small/Filled/Default 量測節點）/ `22831-940856`（Small/Filled/Filled 量測節點）
- 同族元件：`DropDownList.md`（單選，同 Filled 族群）、`TextBox.md`（Filled 底線式輸入族群，Anatomy token 高度共用）
- 相關元件：`st-chip.md`（狀態 chip 獨立使用；MultiSelect chip 為輕量 border-only 版本，非狀態 chip）

---

## Figma 補入紀錄（2026-06-29）

> Layer 1–4 依 Token Matching 五層工作流程執行。來源：`get_variable_defs`（節點 22831-939764，39 變數）+ `get_screenshot`（整個元件集）+ `get_metadata`（子節點樹）+ `get_design_context`（節點 22831-940580 Small/Filled/Default、節點 22831-940856 Small/Filled/Filled）。

### ✅ 已確認對映（Figma 變數 → `athena-tokens.md`）

`ColorSf/*`（on-surface-variant / outline / surface-variant / on-surface / primary / success / warning / danger / error / transparent / surface）、`DsColor/placeholder`、`DsSpace/Margin/Small`、字型：`FontSizeSf/TextSm`（11px）/ `TextXs`（11px）/ `TextMd`（14px）/ `TextLg`（16px）、`FontWeightSf/Normal` / `Medium`、`FontFamilySf/Fontfamily`、排版樣式：`Label/12pt/Regular` / `Label/11pt/Medium` / `Label/12pt/Medium` / `Label/14pt/Regular` / `Body Content/12pt/Regular` / `Body Content/14pt/Regular` / `Body Content/16pt/Regular`、圓角：`Radius/Radius9Xl`（48px → `ds-radius-10extra-large` 1000px 同族）/ `Radius/RadiusSm`（4px）/ `Radius/RadiusNone`（0）。  
**39 個變數全數找到對應，無漂移。**

### ✅ Variant 軸確認

`get_metadata` 確認三個 variant 軸：
- **Shape**：Filled / Outlined / Line（3 種）
- **Size**：Extra Small / Small / Medium / Large（4 種）
- **State**：Default / Hover / Focus / Active / Filled / Read Only / Disabled / Success / Warning / Error（10 種）

Figma 中 "State=Filled" 代表「已選取一或多個項目」的靜態展示狀態（chips 顯示），與其他狀態不同軸性；已在 §4 / §5 中特別標注。

### ✅ Chip 視覺確認（截圖 + get_design_context 雙重驗證）

- Chip 形狀：全圓 pill（`Radius/Radius9Xl` = 48px，Syncfusion token → `{ds-radius-10extra-large}` 1000px）
- Chip 底色：透明（`{color-sf-transparent}`），僅 border 輪廓
- Chip border：`{ds-borderwidth-small}` / `{color-sf-outline}`（1px rgb(127 137 150)）
- Chip 文字：`{font-size-sf-text-xs}` 11px / `{font-weight-sf-medium}` 500（`Label/11pt/Medium`）
- Chips 排列：**inline-flex，不使用 +N more badge**；overflow 時 trigger 高度自適應增高（flex-wrap）
- Close icon：出現於 State=Filled；尺寸 16×16；顏色 `{color-sf-on-surface-variant}`

### ✅ 與 DropDownList 差異確認

| 項目 | DropDownList | MultiSelect |
|---|---|---|
| 右側槽 | 固定 arrow-down btn（32×32，不可省略） | 可選 clear-all btn（`clearIcon` prop，無 arrow-down） |
| 內容區 | 單一 selected-value text | chips-input-area（chips + type-ahead inline） |
| Chip | 無 | 有（pill，透明底，outline border） |
| Overflow 表示 | N/A | flex-wrap 增高，無 +N badge |
| Import 套件 | `@syncfusion/ej2-vue-dropdowns`（DropDownList） | `@syncfusion/ej2-vue-dropdowns`（MultiSelect） |

### ⚠️ Token gap（非阻塞，已標 🎨 量測值）

1. **`padding_left: 10px`**：`athena-tokens.md` 無 10px 間距 token → 同 TextBox / DropDownList 同 gap，值權威見 `prototyper/assets/app.css`
2. **`inner_left_gap: 6px`**：同上，scale 無 6px → 值權威見 `app.css`
3. **`chip_area_inner_px: 4px`**：text-area 左右 padding，無對應 token → 值權威見 `app.css`
4. **`chip_gap: 8px`** = `{ds-space-padding-2extra-large}`（Default=8px / Comfortable=12px）：兩密度有差異，Comfortable 密度下 chip 間距加大，待 ERP 確認密度切換行為
5. **Dropdown item 的 checkbox 樣式**：本次 `get_design_context` 未涵蓋 dropdown popup 節點；checkbox item 的視覺規格待補充（預期與 Syncfusion CheckBox 元件一致）

### ⬜ 待補（次要）

- Dropdown popup 節點的 `get_design_context`（checkbox item、hover state、select-all row）
- State=Active（下拉展開中）觸發的 dropdown popup 節點截圖
- Size: Extra Small / Medium / Large 的 container height 精確量測（目前依 Figma metadata 框高推算）
- Outlined / Line shape 的 chip border 處理（現有 chip border 與 Outlined container border 的視覺衝突確認）
- disabled 態 chip 的 Figma 截圖驗證
