---
name: Switch（Toggle Switch）
category: 表單與輸入
tier: lite
status: ✅ 已採用
authority: ERP CLAUDE.md（Syncfusion Switch, @syncfusion/ej2-vue-buttons）
figma-node: JhcpyIEEzSChcEXMRJUiIm/11932-33202
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs + get_metadata + get_design_context(×4 variants)；紀錄見文末「Figma 補入紀錄」
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺值一律 token-reference；對不上既有 token 時停下回報，勿臆造。
> §3–§5 已用 Figma 多工具校準（`get_variable_defs` / `get_metadata` / `get_design_context` × 4 節點，節點集 `11932:33202`）。

## 1. 概述　📋

單一布林值開關控制元件，立即生效（不需「儲存」確認）。允許使用者切換功能的啟用 / 停用狀態。

**何時使用**：
- 功能性開關（立即生效的布林設定，如「啟用通知」「自動儲存」）
- 設定檔頁面的逐項啟/停控制
- 單一選項的 ON/OFF 切換（語意明確為開啟與關閉）

**何時不用**：
- 需要「儲存」才生效的選項 → `Checkbox`（`ejs-checkbox`）
- 互斥單選 → `RadioButton`（`ejs-radiobutton`）
- 多選項 → `Checkbox`
- 動作觸發（如送出表單）→ `Button`

---

## 2. Anatomy　🎨

```
[touch-area / focus-ring-wrapper]
  └─ track                       ← 圓角膠囊形底座
       ├─ thumb                  ← 圓形滑塊，位置依 Active(ON/OFF) 左右切換
       └─ inline-text?           ← 可選，ON 態顯示「ON」/ OFF 態顯示「OFF」（Text variant 軸）
[gap-8px]
[label-container?]               ← 可選外部標籤（Label variant 軸）
  ├─ label-text
  ├─ required-mark?              ← 可選 * 號（required prop）
  └─ info-icon?                  ← 可選 circle-info 圖示（icon prop）
```

- **track**：圓角膠囊（`{ds-radius-10extra-large}`），ON 態填 primary，OFF 態透明底 + outline 邊框
- **thumb**：圓形白色滑塊；ON 時靠右，OFF 時靠左
- **inline-text**：嵌入 track 內的 ON / OFF 字串，12px，ON 態白色，OFF 態 on-surface（可透過 Text 軸隱藏）
- **label**：元件右側（或左側）的外部說明文字，16px；`required` 時補紅色 `*`；`icon` 時附 circle-info

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# 🎨 get_design_context 量測，4 個尺寸（track width × track height）
track_size:
  xs:     ON: 45×20px / OFF: 46×20px   # 🎨 量測值，待對齊 token
  small:  ON: 48×24px / OFF: 48×24px   # 🎨 量測值，待對齊 token
  medium: ON: 60×28px / OFF: 57×28px   # 🎨 量測值，待對齊 token（主要尺寸）
  large:  ON: 60×32px / OFF: 64×32px   # 🎨 量測值，待對齊 token

# track radius
radius: "{ds-radius-10extra-large}"    # 🎨🔗 Figma Radius/Radius10Xl = 1000px（全圓膠囊）

# thumb（Medium ON: 36px circle；Medium OFF: 16px circle）
thumb_size:
  medium_on:  36px   # 🎨 量測值（含 ripple 預留 area）
  medium_off: 16px   # 🎨 量測值

# OFF 態 track border
border_width:  "{ds-borderwidth-small}"         # 🔗 1px
border_color:  "{color-sf-outline}"             # 🎨🔗 $border = #7f8996

# inline-text（嵌入 track 內）
inline_text_font:  "{font-size-sf-text-sm} / {font-weight-sf-normal}"  # 🔗 12px / 400
inline_text_on:    "{color-sf-surface}"         # 🎨🔗 white（ON 態文字「ON」）
inline_text_off:   "{color-sf-on-surface}"      # 🎨🔗 #0f172a（OFF 態文字「OFF」）

# label（外部標籤）
label_font:    "{font-size-sf-text-lg} / {font-weight-sf-normal}"  # 🎨🔗 16px / 400（Figma: --fontsizesf/textlg）
label_color:   "{color-sf-on-surface}"          # 🎨🔗 #0f172a
required_color: "{color-sf-danger}"             # 🔗 #f4493e
label_gap:     8px                              # 🎨 track ↔ label 間距，量測值，待對齊 token

# gap（button container ↔ label container）
gap:           8px                              # 🎨 量測值，待對齊 token（同 Checkbox label_gap）
```

---

## 4. Variants　🎨🔗

> Figma 元件集（節點 `11932:33202`）含 4 個 variant 軸：
> `Size`（Extra Small / Small / Medium / Large）× `Active`（ON / OFF）× `State`（Default / Hover / Focus / Active / Disabled）× `Text`（ON / OFF）+ 附屬軸 `Label Position`（Right / Left）

```yaml
# ===== Active 軸 =====
on:                                        # 🎨 Figma Active=ON
  track_bg:     "{color-sf-primary}"       # 🎨🔗 #2877ee
  track_border: none                       # 🎨 border 消失（填底）
  thumb_pos:    right
  inline_text:  "ON"（若 Text=ON）

off:                                       # 🎨 Figma Active=OFF
  track_bg:     "{color-sf-transparent}"   # 🎨🔗 透明底
  track_border: "{ds-borderwidth-small} solid {color-sf-outline}"  # 🎨🔗 1px #7f8996
  thumb_pos:    left
  inline_text:  "OFF"（若 Text=ON）

# ===== Size 軸（量測尺寸，視覺比例相同） =====
extra_small:  track 45/46×20px   # 🎨
small:        track 48×24px      # 🎨
medium:       track 60/57×28px   # 🎨（預設）
large:        track 60/64×32px   # 🎨

# ===== Text 軸 =====
text_on:   嵌入 ON / OFF 字串可見
text_off:  嵌入文字隱藏（純圖形 thumb）

# ===== Label Position 軸（不影響色彩 token） =====
right:   label 在 track 右側（預設）
left:    label 在 track 左側
```

---

## 5. States　🎨🔗

> 以 Active=ON 為主描述，OFF 態 track 色換為 transparent + outline 邊框，其餘 state 邏輯相同。

```yaml
on_default:
  track_bg:     "{color-sf-primary}"
  track_border: none
  label_color:  "{color-sf-on-surface}"

on_hover:
  track_bg:     "{color-sf-primary}"
  ripple:       thumb 周圍 Material state-layer 出現（Syncfusion 原生 ripple）
  label_color:  "{color-sf-on-surface}"

on_focus:
  track_bg:     "{color-sf-primary}"
  focus_ring:   "{ds-shadow-focus-ring1}"   # 🔗 白 1px 內環 + 黑 3px 外環（與 Button / Checkbox 一致）
  label_color:  "{color-sf-on-surface}"
  # 🎨 Figma focus 節點：track 視覺同 default；ring 套在外層 wrapper

on_active:
  # 📋 短暫 pressed 態；Syncfusion 以白色 12% overlay 表達，視覺近 default
  base: on_default

on_disabled:
  track_bg:     "{color-sf-primary}"
  opacity:      38%                          # 🎨 整體 switch（Button container）套 opacity-38
  label_color:  "{color-sf-on-surface-opacity38}"  # 🎨🔗 rgba(15,23,42,0.38)

# ===== OFF 態各 state（track_bg = transparent，border = outline） =====
off_default:
  track_bg:     "{color-sf-transparent}"
  track_border: "{ds-borderwidth-small} solid {color-sf-outline}"
  label_color:  "{color-sf-on-surface}"

off_hover:
  base: off_default
  ripple: 同 on_hover（Syncfusion 原生 ripple）

off_focus:
  base: off_default
  focus_ring: "{ds-shadow-focus-ring1}"

off_active:
  base: off_default

off_disabled:
  track_bg:     "{color-sf-transparent}"
  track_border: "{ds-borderwidth-small} solid {color-sf-outline}"
  opacity:      38%
  label_color:  "{color-sf-on-surface-opacity38}"
```

---

## 9. a11y　📋

- `role="switch"`：由 Syncfusion 自動注入
- `aria-checked`：`"true"`（ON）/ `"false"`（OFF）；由 Syncfusion 依 `checked` prop 管理
- 鍵盤：`Space` 切換開關；`Tab` / `Shift+Tab` 移動焦點
- Focus ring：`{ds-shadow-focus-ring1}`（白 1px 內環 + 黑 3px 外環），與 Button / Checkbox 一致
- 禁用態：`disabled` prop（`aria-disabled="true"`）；不用 `readonly`（語意為不可互動）
- Mobile min touch target：44×44px（track 本身小，wrapper 需確保點擊面積）
- Label 文字對比：`{color-sf-on-surface}`（#0f172a on white > 16:1，WCAG AAA）
- Disabled label：`{color-sf-on-surface-opacity38}`（38% on white ≈ 3.0:1，WCAG intentional disabled 豁免）
- ON 態 track（primary #2877ee on white）：約 3.4:1；低於 AA 4.5:1 → 若用作純色圖形元件，參照 WCAG 1.4.11 非文字對比（3:1 達標）；ON/OFF 文字「ON」白色在 primary 上 ≈ 4.8:1（AA）

---

## 11. Content　📋

- Inline text（ON/OFF）：固定詞彙「ON」「OFF」，不自訂；可透過 `Text=OFF` variant 隱藏（純圖形 thumb 時）
- Label：動詞 + 名詞（「啟用通知」「自動儲存」），避免「開關」「切換」等冗詞
- Required mark `*`：僅在 Switch 為表單必填時顯示（設定檔場景罕見，一般不用）
- Disabled 態：必要時搭配 tooltip 說明停用原因；不要只靠 opacity 傳達語意

---

## 12. API / Props　📋

> Syncfusion `<ejs-switch>`（`@syncfusion/ej2-vue-buttons`）

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `checked` | boolean | `false` | 開關狀態（true = ON） |
| `disabled` | boolean | `false` | 停用 |
| `name` | string | — | 表單欄位名稱 |
| `value` | string | — | 表單提交值 |
| `onLabel` | string | `'ON'` | ON 態嵌入文字（空字串隱藏） |
| `offLabel` | string | `'OFF'` | OFF 態嵌入文字（空字串隱藏） |
| `change` | `(args: ChangeEventArgs) => void` | — | 值變更事件（立即觸發） |

```vue
<!-- 基本用法（帶 inline text） -->
<ejs-switch v-model:checked="isEnabled" />

<!-- 無 inline text（純圖形） -->
<ejs-switch v-model:checked="isEnabled" onLabel="" offLabel="" />

<!-- 帶外部 label（自行用 <label> 包裹） -->
<label class="switch-field">
  <ejs-switch v-model:checked="autoSave" />
  <span>自動儲存</span>
</label>

<!-- Disabled -->
<ejs-switch :checked="true" :disabled="true" />
```

> **注意**：Figma 中 `Label`（外部標籤）與 `icon`（circle-info）由元件 variant 控制；Syncfusion `ejs-switch` 本身不含 label prop，需自行用 `<label>` 或包裝元件實作，可參照 `Checkbox.md §12` 的包裹模式。

---

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §Outline / §Surface Background Base / §On-* 前景文字色 / §Radius / §Shadow Elevation / §Typography / §Border Width
- 語意對照：`../athena-design.md` §主色與動作
- Focus ring 來源：`../components/Button.md §5 States focus` / `../components/Checkbox.md §5 States`（`{ds-shadow-focus-ring1}` 同根）
- 相關比較：`../components/Checkbox.md`（布林語意相近；Switch 立即生效、Checkbox 需儲存）
- Prototype 落地：ERP 設定檔頁面功能開關
- Code：`@syncfusion/ej2-vue-buttons`（`SwitchComponent`，import as `<ejs-switch>`）
- Figma 元件集：[FAI2 Library · Toggle Switch](https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/?node-id=11932-33202)

---

## Figma 補入紀錄（2026-06-29）

> 來源：`get_variable_defs`（節點 `11932:33202`）+ `get_metadata`（同節點，完整 variant 結構樹）+ `get_design_context`（× 4 節點）：
> - `11932:33566`（Medium / ON / Default / Text=ON / Label Position=Right）→ ON 態色彩、track 尺寸、inline text、label 字型
> - `11932:33557`（Medium / OFF / Default / Text=ON / Label Position=Right）→ OFF 態色彩、border 色、thumb 位置
> - `11932:33704`（Medium / ON / Disabled / Text=ON / Label Position=Right）→ disabled 態 opacity-38、label disabled 色
> - `11932:33651`（Medium / ON / Focus / Text=ON / Label Position=Right）→ focus 態視覺（ring 由 shadow 控制）

**✅ 已確認 token 對映（Figma 變數 → `athena-tokens.md`）**：
- `$primary` / `ColorSf/primary` → `{color-sf-primary}`（#2877ee，ON 態 track 底色）
- `Border/$border` / `ColorSf/outline` → `{color-sf-outline}`（#7f8996，OFF 態 track 邊框 + thumb border）
- `BG/$transparent` / `ColorSf/transparent` → `{color-sf-transparent}`（OFF 態 track 背景）
- `$primary-text-color` / `ColorSf/surface` → `{color-sf-surface}`（#ffffff，ON 態 inline text 白色）
- `Text/$content-text-color` / `ColorSf/on-surface` → `{color-sf-on-surface}`（#0f172a，OFF 態 inline text + label 文字）
- `ColorSf/on-surface-opacity38` → `{color-sf-on-surface-opacity38}`（rgba(15,23,42,0.38)，disabled label）
- `Radius/Radius10Xl` → `{ds-radius-10extra-large}`（1000px，全圓膠囊 track）
- `--fontsizesf/textlg` → `{font-size-sf-text-lg}`（16px，label 字級）
- `--fontsizesf/textsm`（推導）→ `{font-size-sf-text-sm}`（12px，inline text 字級，Figma 量測值 12px 對齊）
- `--fontweightsf/normal` → `{font-weight-sf-normal}`（400）
- `--colorsf/danger` → `{color-sf-danger}`（#f4493e，required mark `*`）

**✅ Variant 結構確認（get_metadata）**：
4 軸：Size（Extra Small / Small / Medium / Large）× Active（ON / OFF）× State（Default / Hover / Focus / Active / Disabled）× Text（ON / OFF）；另含 Label Position（Right / Left）附屬軸。
Track 尺寸隨 Size 與 Active 雙軸變化（ON 略寬，因 thumb 右移後佔空間不同）。

**✅ disabled 態確認**：
Figma Disabled 節點：`Button container`（或 `Switch`）層整體套 `opacity-38`，label 使用 `{color-sf-on-surface-opacity38}`；與 Checkbox disabled 機制一致。

**✅ focus 態確認**：
Figma focus 節點視覺同 default（track 色不變），focus ring 由外層 wrapper 的 `box-shadow: {ds-shadow-focus-ring1}` 控制，與 Button / Checkbox focus 機制一致。

**⚠️ Token 缺口（2 項）**：

1. **track_size / thumb_size（量測 px）**：所有尺寸（track width/height、thumb size）均為量測值，`athena-tokens.md` 無對應尺寸 token。已標 🎨 量測值，值權威指向 `prototyper/assets/app.css`（canonical CSS）。

2. **gap / label_gap（8px）**：track ↔ label 外部 gap 量測為 8px，與 Checkbox 的 label_gap 同值；`athena-tokens.md` 中 `--ds-space-margin-medium`（Athena_Default = 8px）語意最近。已標 🎨 量測值待 DS owner 確認是否對齊 `{ds-space-margin-medium}`。

**✅ hover ripple 說明**：
Figma hover 態 thumb 周圍出現 Material state-layer ripple，以 image asset 表示（同 Checkbox）。Syncfusion `ejs-switch` 有內建 ripple 效果，落地時不需另加樣式。

**本次未新增 token**：所有確認值均能在現有 `athena-tokens.md` 找到對應，量測缺口已標 🎨，未在設計文件就地造值。
