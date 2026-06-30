---
name: Rating
category: 表單與輸入
tier: lite
status: ✅ 已產出
authority: ERP CLAUDE.md（Syncfusion Rating, @syncfusion/ej2-vue-inputs）
figma-node: JhcpyIEEzSChcEXMRJUiIm/11932-22246
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs + get_metadata + get_design_context(×4 variants) + get_screenshot；紀錄見文末「Figma 補入紀錄」
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺值一律 token-reference；對不上既有 token 時停下回報，勿臆造。
> §3–§5 已用 Figma 多工具校準（`get_variable_defs` / `get_metadata` / `get_design_context` × 4 節點 / `get_screenshot`，節點集 `11932:22246`）。

---

## 1. 概述　📋

星級 / 心型評分輸入元件，讓使用者用點選（或鍵盤）選取 0–5 顆的分數，支援半分（0.5 步進）與整分（1.0 步進）兩種模式。

**何時使用**：
- 讓使用者對項目（商品、服務、評論）給予星級評分
- 顯示唯讀平均分（`readOnly` 模式）
- 表單中的評分欄位（如「滿意度」「建議程度」）

**何時不用**：
- 數值輸入（需精確數字）→ `NumericTextBox`
- 百分比或進度表達 → ProgressBar
- 非 0–5 的量尺（如 NPS 0–10）→ `RadioButton` 群組或 `Slider`

---

## 2. Anatomy　🎨

```
[label-left?]
[star-group]
  ├─ atom × 5                ← 每顆 star/heart，固定 5 個
  │   ├─ filled（selected）  ← 整顆 primary 填色
  │   ├─ half（partial）     ← 左半 primary 填色、右半 unrated
  │   └─ empty（unrated）    ← 白底 + primary 輪廓
[label-right?]
```

- **star-group**：5 個 atom 並列，`items-start` 排列，無 gap（star image 本身含留白邊距）
- **atom**：單顆評分單元（star 或 heart），以 Figma image asset 實作三態；Syncfusion 以 SVG + CSS clip 對應
- **label**：可選文字（如「3.0/5」），可置於星群左側（`labelLeft`）或右側（`labelRight`）；字色 `{color-sf-on-secondary-container}`
- **label ↔ star-group 間距**：`gap-[8px]`（🎨 量測值，見 §3）

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# 🎨 get_design_context 量測；原始節點集為 size × shape × rating 11級，共 88 個 variant
atom_size:
  xs:     16px × 16px     # 🎨 Extra Small；5顆總寬 81px（Star），84px（Heart）
  small:  20px × 20px     # 🎨 Small；5顆總寬 101px（stars inner）；container 117px
  medium: 24px × 24px     # 🎨 Medium（預設）；5顆 container 117px
  large:  28px × 28px     # 🎨 Large；5顆總寬 141px

# 色彩（Figma 變數：rating/$rating-selected-color / rating/$rating-unrated-color）
selected_fill:   "{color-sf-primary}"               # 🔗 #2877ee；已選顆全色填充
unrated_fill:    "{color-sf-surface}"               # 🔗 #ffffff；未選顆白底
unrated_stroke:  "{color-sf-primary}"               # 🔗 #2877ee；未選顆 primary 輪廓
half_fill:       "{color-sf-primary}"               # 🔗 左半填 primary，右半同 unrated；由 Syncfusion clip 實作

# label
label_font_size:   "{font-size-sf-text-sm}"         # 🔗 12px
label_font_weight: "{font-weight-sf-normal}"        # 🔗 400
label_color:       "{color-sf-on-secondary-container}"  # 🔗 #1e222a
label_line_height: 1.3                              # 🎨 量測值（leading-[1.3]，Figma 原生）
label_tracking:    0.1px                            # 🎨 量測值（tracking-[0.1px]）

# layout
label_gap: 8px    # 🎨 label ↔ star-group 間距；對應 {ds-space-padding-small}（Athena_Default 4px）或
                  # {ds-space-margin-medium}（Athena_Default 8px）。語意近後者，但 token 用途待 DS owner 確認；暫標 🎨 量測值
```

---

## 4. Variants　🎨🔗

> Figma 元件集（節點 `11932:22246`）含 3 個 variant 軸：
> `Size`（Extra Small / Small / Medium / Large）× `Shape`（Star / Heart）× `Rating`（0 / 0.5 / 1.0 / 1.5 / … / 5.0，共 11 個值）
> Label 位置（Left / Right / 無）為 prop，非獨立 variant 軸。

```yaml
# ===== Size 軸（主要影響 atom 尺寸與容器寬） =====
extra_small:
  atom:  16px × 16px    # 🎨 最緊湊；適用表格列中欄位
  total_width_star: 81px   # 🎨 5顆
  total_width_heart: 84px  # 🎨 heart 略寬

small:
  atom:  20px × 20px    # 🎨 預設密度建議（表單 default）
  inner_width: 101px    # 🎨 stars inner container
  outer_width: 117px    # 🎨 含 label slot

medium:
  atom:  24px × 24px    # 🎨 comfortable 密度
  total_width: 117px    # 🎨

large:
  atom:  28px × 28px    # 🎨 強調型（如 modal 評分 CTA）
  total_width: 141px    # 🎨

# ===== Shape 軸（影響 atom 圖形，色彩 token 不變） =====
star:   星形（Syncfusion 預設）
heart:  心形（Syncfusion `shape="Heart"` prop）

# ===== Rating 軸（影響每顆 atom 的三態） =====
# atom 三態（以 rating=2.5 為例：顆 1–2 filled, 顆 3 half, 顆 4–5 empty）
filled:
  fill:   "{color-sf-primary}"     # 🔗 整顆 primary 填色
  stroke: none

half:
  fill_left:  "{color-sf-primary}"  # 🔗 左半 primary（Syncfusion clip mask）
  fill_right: "{color-sf-surface}"  # 🔗 右半白底
  stroke:     "{color-sf-primary}"  # 🔗 全輪廓 primary（half 態 border 延展至整顆）

empty:
  fill:   "{color-sf-surface}"      # 🔗 白底
  stroke: "{color-sf-primary}"      # 🔗 primary 輪廓
```

---

## 5. States　🎨🔗

> Rating 支援互動（可寫）與唯讀兩種模式。互動模式下有 hover / focus / disabled；唯讀模式僅 default。

```yaml
default:
  # 顯示當前 value，無額外覆蓋

hover:
  # 🎨 hover 時目標顆及左側全部暫時亮起（preview fill）；Syncfusion 原生行為，設計稿未另設 hover token
  preview_fill: "{color-sf-primary}"    # 🔗 同 selected_fill，preview 態
  # hover 不套 state-layer，直接以 preview 值覆蓋

focus:
  ring: "{ds-shadow-focus-ring1}"       # 🔗 鍵盤 focus 時整個元件外圈（與 Checkbox/Button 一致）

disabled:
  # 🎨 整體 opacity-38；Syncfusion disabled prop 啟動
  opacity: 38%    # 🎨 同 Checkbox disabled 邏輯
  label_color: "{color-sf-on-surface-opacity38}"   # 🔗 rgba(15,23,42,0.38)

readonly:
  # 唯讀：顯示固定 value，無 hover preview，無互動態
  # 色彩同 default；cursor: default
```

---

## 9. a11y　📋

- Syncfusion `<ejs-rating>` 自動產生 `role="slider"` + `aria-valuenow` / `aria-valuemin` / `aria-valuemax`
- 鍵盤：`←` / `→` 調整分數（依 step）；`Home` 設 0；`End` 設 max（5）；`Tab` / `Shift+Tab` 進出元件
- Focus ring：`{ds-shadow-focus-ring1}`（白 1px 內環 + 黑 3px 外環，與 Button / Checkbox 一致）
- Disabled：`disabled` prop；`aria-disabled="true"` 由 Syncfusion 自動注入；**禁用 `readonly`** 替代 disabled（語意不同）
- Label 文字對比：`{color-sf-on-secondary-container}`（#1e222a on white = > 15:1，WCAG AAA）
- Mobile min touch target：每顆 atom 至少 44×44px 觸控感應範圍（atom 本身小，需確保 wrapper 有足夠點擊面積，ERP 桌面版不做 mobile）
- 半分步進（`enableHalfStar`）：`aria-valuenow` 反映 0.5 精度，如 `2.5`

---

## 12. API / Props　📋

> Syncfusion `<ejs-rating>`（`@syncfusion/ej2-vue-inputs`）

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `value` | number | `0` | 目前評分值（0–max，支援半分） |
| `max` | number | `5` | 最大顆數 |
| `min` | number | `0` | 最小評分值 |
| `step` | number | `1` | 步進值；`0.5` = 半分模式 |
| `enableHalfStar` | boolean | `false` | 啟用半分視覺（左半填色）；需搭配 `step: 0.5` |
| `shape` | `'Star'` / `'Heart'` | `'Star'` | 星形或心形 |
| `labelPosition` | `'Left'` / `'Right'` / `'None'` | `'None'` | label 顯示位置 |
| `label` | string | `''` | label 文字（如 `'3.0/5'`） |
| `disabled` | boolean | `false` | 停用，不可互動 |
| `readOnly` | boolean | `false` | 唯讀，顯示但不可改 |
| `cssClass` | string | `''` | 自訂 class |
| `change` | `(args: RatingChangeEventArgs) => void` | — | 值變更事件 |

```vue
<!-- 基本互動用法（整分） -->
<ejs-rating v-model:value="score" :max="5" />

<!-- 半分模式 -->
<ejs-rating
  v-model:value="score"
  :max="5"
  :step="0.5"
  :enableHalfStar="true"
/>

<!-- 帶 label，顯示於右側 -->
<ejs-rating
  v-model:value="score"
  :max="5"
  :step="0.5"
  :enableHalfStar="true"
  labelPosition="Right"
  :label="`${score}/5`"
/>

<!-- 唯讀顯示 -->
<ejs-rating :value="3.5" :readOnly="true" :enableHalfStar="true" />

<!-- 停用 -->
<ejs-rating :value="2" :disabled="true" />

<!-- Heart 形狀 -->
<ejs-rating v-model:value="score" shape="Heart" />
```

---

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §Surface / §On-* 前景文字色 / §Typography / §Shadow
- 語意對照：`../athena-design.md` §主色與動作
- Focus ring 來源：`../components/Button.md §5 States focus`（`{ds-shadow-focus-ring1}` 同根）
- Code：`@syncfusion/ej2-vue-inputs`（`RatingComponent`，import as `<ejs-rating>`）
- Figma 元件集：[FAI2 Library · Rating](https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/?node-id=11932-22246)

---

## Figma 補入紀錄（2026-06-29）

> 來源：
> - `get_variable_defs`（節點 `11932:22246`）→ 設計變數定義
> - `get_metadata`（同節點）→ variant 結構樹（88 個 variant symbol）
> - `get_design_context`（× 4 節點）：
>   - `16995:358874`（Medium / Star / Rating=0）→ unrated 態、label 色彩、font、gap 量測
>   - `13908:4828`（Medium / Star / Rating=3.0）→ selected 態（Slider image）vs unrated（Rating image）
>   - `13908:4623`（Medium / Star / Rating=2.5）→ half 態（Container image，左半填色）
>   - `13908:3097`（Small / Star / Rating=0.5）→ Small 尺寸量測（atom 20px、inner 101px）
> - `get_screenshot`（同節點，1600px 高全集預覽）→ 視覺確認 star 輪廓、heart 形狀、four 尺寸排列

**✅ 已確認 token 對映（Figma 變數 → `athena-tokens.md`）**：
- `rating/$rating-selected-color: #2877EE` → `{color-sf-primary}`（已選填色）
- `rating/$rating-unrated-color: #FFFFFF,#2877EE,#2877EE`（三值：base 白底 + primary 邊框 + primary）：
  - 第一值 `#FFFFFF` → `{color-sf-surface}`（未選底色）
  - 第二值 `#2877EE` → `{color-sf-primary}`（未選輪廓色）
  - 第三值 `#2877EE` 同 primary，代表 Figma 變數額外 state-layer 定義
- `ColorSf/on-secondary-container: #1e222a` → `{color-sf-on-secondary-container}`（label 文字）
- `FontSizeSf/TextSm: 12px` → `{font-size-sf-text-sm}`（label 字級）
- `FontWeightSf/Normal: 400` → `{font-weight-sf-normal}`（label 字重）
- Focus ring → `{ds-shadow-focus-ring1}`（從 Button.md / Checkbox.md 同根延伸）

**✅ Variant 結構確認（get_metadata）**：
3 軸：Size（XS / Small / Medium / Large）× Shape（Star / Heart）× Rating（0 / 0.5 / 1.0 … 5.0，共 11 值）。
Label 方向（Left / Right / None）為 Figma prop，非獨立軸。
各 Size atom 尺寸：XS 16px / Small 20px / Medium 24px / Large 28px。

**✅ 三態確認（get_design_context atom 命名）**：
- `"_Atom / Rating"` + child `Rating` image → unrated（空心輪廓）
- `"_Atom / Rating"` + child `Slider` image → filled（primary 實心）
- `"_Atom / Rating"` + child `Container` image → half（半填，左 primary 右空心）

**⚠️ Token 缺口（1 項）**：
- **label_gap（8px）**：label ↔ star-group 外層 Container `gap-[8px]` 量測為 8px，對應 `{ds-space-margin-medium}`（Athena_Default 8px）語意最近，但 margin/padding token 在此情境語意不明確，與 Checkbox 的同位置 8px gap 缺口一致（見 `Checkbox.md Figma 補入紀錄`）。暫標 🎨 量測值，待 DS owner 確認是否統一對齊 `{ds-space-margin-medium}`。

**⚠️ 未識別的視覺細節（不阻斷，標記待確認）**：
- **label line-height 1.3 / tracking 0.1px**：從 Figma Tailwind output 量測，但 `athena-tokens.md` Typography 章節無 line-height / letter-spacing token（僅有 font-size / font-weight / font-family）。暫標 🎨 量測值，待 DS 是否補入對應 token。

**本次無需新增 token**：所有色彩值均能在 `athena-tokens.md` 找到對應；兩個 🎨 缺口（label gap、line-height/tracking）已標示且未在設計文件就地造值。
