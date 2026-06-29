---
name: Skeleton
category: 回饋與狀態
tier: lite
status: ✅ 已採用
authority: ERP CLAUDE.md（Syncfusion Skeleton, @syncfusion/ej2-vue-notifications）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16535-5838
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs + get_design_context（全 6 個 variant）+ get_screenshot（節點 16535-5838）
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺值一律 token-reference；對不上既有 token 時停下回報，勿臆造。
> §3–§5 已用 Figma `get_variable_defs` 與 `get_design_context`（節點集 16535-5838）校準；**未解 / token 缺口見文末「Figma 補入紀錄」**。

## 1. 概述　📋

資料載入中的視覺佔位元件（content placeholder）。在 Grid 列、Form section、Card 等內容尚未回傳前，以低對比度骨架填充空間、保持版型穩定，避免畫面在資料到位後「跳版」（layout shift）。

**何時用**：API 首次載入、Tab 切換、頁面轉場等待期。
**何時不用**：
- 操作進行中（儲存 / 提交 / 審核）→ 改用 Button loading 狀態或 Toast
- 等待時間 < 300ms → 不需 Skeleton，直接顯示即可
- 不確定性的非同步動作（如上傳）→ 改用進度條（ProgressBar）

## 2. Anatomy　🎨

Skeleton 是**純佔位複合元件**，由若干骨架矩形（skeleton bar）與骨架圓形（skeleton circle）組成，映射目標 UI 的視覺輪廓。

```
[ skeleton-circle ]  ← avatar / icon 佔位（圓形；size 視情境定）
[ skeleton-bar      ]  ← 標題行（較寬）
[ skeleton-bar  ]      ← 次要行（較窄）
```

**子元素**：
- `skeleton-circle`：圓形佔位（Ellipse，無圓角 token，shape 本身即圓）
- `skeleton-bar`（矩形）：高度 8–12px，圓角 `{ds-radius-extra-small}` 或 `{ds-radius-small}`
- `skeleton-input-block`：模擬表單欄位的複合組（左側方形 icon 區 + 右側雙行 bar 堆）
- shimmer 層：`$content-bg-color-alt2` 疊層（base 白底 + primary-opacity-8 漸層）

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── 底色 ──
base-color: "{color-sf-surface}"              # 🔗 #ffffff；Skeleton 填充在白色背景上

# ── Shimmer 主色（矩形骨架用） ──
shimmer-primary-tint: "{color-sf-primary-opacity-8}"   # 🔗 rgba(40,119,238,0.08)；即 $content-bg-color-alt2 的 primary tint 層
# 疊層公式：linear-gradient(white, white) + linear-gradient(primary-opacity-8, primary-opacity-8)
# = 白底上疊 8% primary 藍色薄膜（非動態漸層，靜態雙層 gradient 模擬骨架色）

# ── Shimmer 第二色系（部分複合 Style 用，purple tint） ──
shimmer-opacity-layer: 🎨 rgba(103,80,164,0.08)  # 對應 Opacity Layer/primary-opacity-8；athena-tokens.md 無對應，值見 app.css
# 僅出現於 Style #3 / Style #5 的較大文字塊骨架中，與主 primary-tint 交替使用

# ── 骨架圓角 ──
bar-radius-sm:  "{ds-radius-extra-small}"     # 🔗 2px；細文字行骨架（Style #1 / #2 / #3 / #5）
bar-radius-md:  "{ds-radius-small}"           # 🔗 4px；較大矩形塊骨架（Style #4 / #6）

# ── 骨架尺寸（量測值） ──
bar-height-xs:   🎨 8px    # 細文字行（Text input）
bar-height-sm:   🎨 12px   # 次標題行（Text input，Style #3 / #5）
circle-size-sm:  🎨 48px   # 小 avatar / icon 圓（Style #1 / #4）
circle-size-lg:  🎨 80px   # 大 avatar 圓（Style #6）
```

## 4. Variants　🎨

Figma 元件集（節點 16535-5838）以 `Types` 軸提供 6 個複合佈局 preset，代表常見的頁面載入場景：

```yaml
"Style #1":   # 48px 高；清單列 / 表格行佔位
  anatomy: 左側小圓（48×48, avatar/icon）+ 右側雙行文字 bar 堆（上寬下短）
  bar-radius: "{ds-radius-extra-small}"
  circle-size: 🎨 48px
  shimmer: primary-tint

"Style #2":   # 52px 高；單一表單欄位佔位
  anatomy: 左側方形 icon 塊 + 右側 2 + 2 行文字 bar 堆（分上下兩組）
  bar-radius: "{ds-radius-extra-small}"
  shimmer: primary-tint

"Style #3":   # 189px 高；複合表單 Section 佔位
  anatomy: 頂部 Style #2 表單行 + 全寬矩形大塊 + 雙行文字（12px 高）
  bar-radius: "{ds-radius-extra-small}"
  shimmer: primary-tint（表單行）＋ opacity-layer tint（大塊與雙行）

"Style #4":   # 167px 高；卡片 / 個人資料卡佔位（居中對齊）
  anatomy: 頂部居中圓形 + 下方短行 bar + 中行 bar + 全寬 bar + 較窄 bar（四行）
  bar-radius: "{ds-radius-small}"
  circle-size: 🎨 48px
  shimmer: primary-tint

"Style #5":   # 189px 高；含 Switch 的複合表單佔位
  anatomy: Switch 控制列圖片佔位 + 全寬矩形大塊 + 雙行文字（12px 高）
  bar-radius: "{ds-radius-extra-small}"
  shimmer: opacity-layer tint（大塊與雙行）

"Style #6":   # 176px 高；Profile 卡 / 橫向資料列佔位
  anatomy: 左側大圓（80px）+ 右側矩形大塊 + 短行 bar + 較短 bar（三欄）
  bar-radius: "{ds-radius-small}"
  circle-size: 🎨 80px
  shimmer: primary-tint
```

> **層級判定**：6 個 Style 是**同一元件集的 variant**（預設佈局組合），非獨立元件。Syncfusion `<ejs-skeleton>` 透過 `shape` 屬性（`Circle` / `Rectangle` / `Text`）組合使用，ERP 依場景選用對應 Style 或自訂組合。

## 5. States　🎨🔗

```yaml
loading:    # 骨架顯示中（資料載入中）
  display: 顯示 shimmer 骨架
  animation: Syncfusion ejs-skeleton 內建 shimmer wave（預設開啟）
  shimmer-color: "{color-sf-primary-opacity-8}"   # 🔗 wave 色彩為 primary-opacity-8 疊白底

loaded:     # 資料到位後
  display: 以真實內容替換骨架（v-if / transition-fade 切換）
  animation: 建議搭配 0.15s fade-out 避免閃跳

# ⚠️ Skeleton 本身無 hover / focus / active / disabled 互動態；它是純顯示佔位，無用戶操作意圖。
```

## 9. a11y　📋

- Skeleton 容器加 `aria-busy="true"` 標示內容正在載入。
- 容器同時加 `aria-label="載入中"` 或 `role="status"`，供 screen reader 讀出「載入中」文字；骨架矩形本身設 `aria-hidden="true"` 避免讀出無意義結構。
- Skeleton 顯示消失後，將 `aria-busy` 改回 `false` 並確保焦點維持（不因 DOM 替換而遺失焦點）。
- 顏色對比：骨架色為 `rgba(40, 119, 238, 0.08)` on white（約 1.05:1），**刻意低對比**作為視覺噪音最小化；不應作為資訊承載色，因此不適用 WCAG 4.5:1 標準（純裝飾性元素，WCAG 1.4.11 例外）。

## 12. API / Props　📋

Syncfusion `<ejs-skeleton>` 主要 Props：

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `shape` | `"Circle"` / `"Rectangle"` / `"Text"` / `"Square"` | `"Rectangle"` | 骨架基礎形狀 |
| `width` | string / number | `"100%"` | 寬度（px 或 %） |
| `height` | string / number | — | 高度（px 或 %） |
| `shimmerEffect` | `"Wave"` / `"Pulse"` / `"Fade"` / `"None"` | `"Wave"` | Shimmer 動畫類型 |
| `cssClass` | string | — | 附加 CSS class |

> ERP 慣例：複合佔位直接使用 Figma Style #1–#6 的組合作為 template；`<ejs-skeleton>` 以多個實例堆疊組成複合佈局，不在同一個元件內傳 variant prop。

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary（`color-sf-primary-opacity-8`）/ §Surface（`color-sf-surface`）/ §Radius（`ds-radius-extra-small` / `ds-radius-small`）
- 語意對照：`../athena-design.md` §載入與狀態
- Prototype 落地：Syncfusion Skeleton playground — `/Users/athena/working/ddd/syncfusion-playground/app/pages/playground/skeleton.vue`
- Code：`@syncfusion/ej2-vue-notifications`（`<ejs-skeleton>`）
- Figma 元件集：`https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/?node-id=16535-5838`

---

## Figma 補入紀錄（2026-06-29）

來源：`get_variable_defs` + `get_design_context`（全 6 個 variant，節點集 `16535-5838`）+ `get_screenshot`（視覺確認）。

### ✅ 已確認對映（Figma 變數 → `athena-tokens.md`）

| Figma 變數 / 值 | 對映 token | 狀態 |
|---|---|---|
| `ColorSf/surface` = `#ffffff` | `{color-sf-surface}` | ✅ |
| `ColorSf/primary-opacity-8` = `rgba(40,119,238,0.08)` | `{color-sf-primary-opacity-8}` | ✅ |
| `BG/$content-bg-color-alt2` = `#FFFFFF,#2877EE`（shimmer 雙色）| white base + `{color-sf-primary-opacity-8}` | ✅（概念對映）|
| `Color variables/surface` = `#ffffff` | `{color-sf-surface}` | ✅（別名） |
| `rounded-[2px]`（Style #1/#2 骨架列） | `{ds-radius-extra-small}` = 2px | ✅ |
| `rounded-[4px]`（Style #4/#6 骨架塊） | `{ds-radius-small}` = 4px | ✅ |

### ⚠️ Token 缺口（依規停下回報，未臆造）

| 值 | Figma 原名 | 狀態 |
|---|---|---|
| `rgba(103, 80, 164, 0.08)` | `Opacity Layer/primary-opacity-8` = `#6750a414` | **缺口**：此為 Material Purple 系（`#6750A4`），非 Athena primary（`#2877EE`）。`athena-tokens.md` 無對應 token。標 🎨，值見 `app.css`；不臆造新 token。出現於 Style #3 / #5 的大文字塊 shimmer，可能為 Syncfusion 預設 MD3 token，非 Athena 設計決策。**建議 DS owner 確認**：是沿用 SF Opacity Layer 值、還是統一換成 `{color-sf-primary-opacity-8}`。 |

### 📌 Figma 觀察

- Figma 提供的 6 個 Style 是**複合佈局 preset**（非單一 shape atom），等同於「清單列」「表單欄位」「卡片」「Profile 卡」等頁面場景的完整骨架組合。
- Shimmer 在 Figma 中以靜態雙層 `linear-gradient` 疊加呈現（非 CSS `@keyframes`）；真實動態效果由 Syncfusion `shimmerEffect: "Wave"` 提供。
- Style #5 包含 `Switch container` 圖片資產（非純骨架矩形），為特殊複合場景；ERP 使用時通常以 `<ejs-skeleton shape="Rectangle">` 替代圖片佔位。

### 📌 Shape Variant 清單（Figma 節點對應）

| Figma Variant | 節點 ID | 尺寸（w×h） | 場景描述 |
|---|---|---|---|
| Style #1 | `16535:5833` | 296×48 | 清單行（小圓 + 雙文字行） |
| Style #2 | `17025:42722` | 296×52 | 表單單欄（方形 icon + 雙行文字） |
| Style #3 | `17025:42827` | 296×189 | 複合表單 Section（含 purple shimmer） |
| Style #4 | `22829:611831` | 296×167 | 居中卡片（圓 + 四行文字） |
| Style #5 | `16535:5935` | 296×189 | 含 Switch 的複合表單（含 purple shimmer） |
| Style #6 | `22829:611839` | 296×176 | Profile 橫向卡（大圓 + 三行文字） |
