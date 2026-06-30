---
name: Tab
category: 導覽與佈局
tier: full
status: ✅ 已產出
authority: ERP CLAUDE.md（Syncfusion Tab, @syncfusion/ej2-vue-navigations）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16545-8623   # 🎨 FAI2 Library · Tabs 元件集
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs + get_design_context + get_screenshot；紀錄見文末「Figma 補入紀錄」
---

> 依 `../component-doc-schema.md`（Full 層）產出。視覺值一律 token-reference；對不上既有 token 時停下回報，勿臆造。
> §3–§5 已用 Figma `get_variable_defs`＋`get_design_context`（節點 16545-8619 / 16546-8006 / 16547-8927）校準；**未解 / 衝突項見文末「Figma 補入紀錄」**。

---

## 1. 概述　📋

單據明細分頁、表單多區塊切換的主要導覽元件。Tab strip 提供 Horizontal（預設）與 Vertical 兩種排列，支援 2–8 個頁籤。

**何時用**：
- Form View 明細區塊分頁（如「明細」「稅務」「備註」）
- List View 篩選維度切換（可替代 filter chip 排）
- 設定模組的分類導覽（Vertical 模式）

**何時不用**：
- 頁面間跨路由切換 → 改用 Nav Rail 或 Side Menu
- 步驟引導流程 → 改用 Stepper（狀態有方向性、不可隨意跳躍）
- 僅兩個選項的切換 → 考慮 Toggle / SegmentedButton（空間更緊湊）

---

## 2. Anatomy　🎨

```
Tabs（容器）
├── [Scroll Button — Prev]          可選，scroll=true 時顯示；Horizontal 為 Chevron-left，Vertical 為 Chevron-up
├── Tab Strip（tab-strip）          裝載所有 tab-item 的橫列（Horizontal）或直欄（Vertical）
│   ├── Tab Item — inactive         未選中狀態，無 indicator
│   ├── Tab Item — active           選中狀態，含 active indicator
│   │   ├── [Icon?]                 可選（Figma 元件暴露，未見於基本集，屬擴充）
│   │   ├── Label                  tab 名稱文字
│   │   └── Active Indicator       3px 色塊：Horizontal = 底部橫線，Vertical = 左側直線
│   └── Tab Item — …               最多 8 項（Count=2–8）
└── [Scroll Button — Next]          可選，scroll=true 時顯示；Horizontal 為 Chevron-right，Vertical 為 Chevron-down
```

**子節點命名對應（Figma → 設計文件）**：

| Figma 層名 | 設計文件部位 |
|-----------|------------|
| `Atoms / Tabs`（inactive） | tab-item--inactive |
| `Atoms / Tabs`（active，含 indicator） | tab-item--active |
| `$primary`（3px 矩形） | active-indicator |
| `Icon Button`（scroll=true） | scroll-btn |
| `Horizontal container` / `Card` | tab-item 內容容器（不外露） |

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ─── Tab Strip ──────────────────────────────────────────────
tab-strip:
  background: "{color-sf-surface}"          # 🔗 ColorSf/surface rgb(255 255 255)
  border-bottom: none                       # 📋 Figma 無底線；靠 active-indicator 提示當前位置

# ─── Tab Item — 雙密度（Default / Comfortable）─────────────────
# Default = Size:Small（Figma）
tab-item:
  height:
    default:     38px                        # 🎨 Figma Small Horizontal = 38px
    comfortable: 48px                        # 🎨 Figma Large Horizontal = 48px
  padding-horizontal:
    default:     "{ds-space-padding-large}"  # 🔗 12px（Figma px-12 Small）
    comfortable: "{ds-space-padding-extra-large}"  # 🔗 16px（Figma px-16 Large）
  padding-vertical:
    default:     "{ds-space-padding-large}"  # 🔗 10px（Figma py-10 Small inactive）
    comfortable: "{ds-space-padding-large}"  # 🔗 10px（Figma py-10 Large inactive）
  # ⚠️ active tab 的 padding-top 於 Small = 10px / Large = 14px（因 indicator 在底部，
  #    active 態 pt 多出 3px 間距以維持 label 垂直居中）——值見 app.css
  gap:           "{ds-space-padding-medium}" # 🔗 8px（icon ↔ label gap；Figma gap-8）

# ─── Label ──────────────────────────────────────────────────
tab-label:
  font-size:    "{font-size-sf-text-md}"     # 🔗 FontSizeSf/TextMd = 14px
  font-weight:  "{font-weight-sf-medium}"    # 🔗 FontWeightSf/Medium = 500
  font-family:  "{font-family-sf-fontfamily}"# 🔗 FontFamilySf/Fontfamily = Roboto / Noto Sans TC
  line-height:  1.3                          # 🎨 Figma lineHeight: 1.3（Label/14pt/Medium）
  letter-spacing: 0.1px                      # 🎨 Figma letterSpacing: 0.1（Label style）；Large active = 0.24px（Body Content/14pt/Medium）
  color-inactive: "{color-sf-on-surface-variant}"  # 🔗 #3c4a5b（Text/$content-text-color-alt1）
  color-active:   "{color-sf-primary}"             # 🔗 #2877ee

# ─── Active Indicator ────────────────────────────────────────
active-indicator:
  height:     3px                            # 🎨 Figma $primary 矩形高 = 3px
  width:      100%                           # 🎨 撐滿 tab-item 寬度
  color:      "{color-sf-primary}"           # 🔗 $primary = ColorSf/primary #2877ee
  # Horizontal：底部橫線（bottom: 0）
  # Vertical：左側直線（left: 0，旋轉 90° 後高度等於 tab-item 高度）——見 §6

# ─── Scroll Buttons ──────────────────────────────────────────
scroll-btn:
  size-default:     38px                     # 🎨 Figma Small = 38×38
  size-comfortable: 40px                     # 🎨 Figma Large = 40×48（w-40 h-48）
  icon-size-default:     16px                # 🎨 Figma Small icon = 16px
  icon-size-comfortable: 20px               # 🎨 Figma Large icon = 20px
  background:  "{color-sf-transparent}"      # 🔗 transparent（Figma bg-colorsf/transparent）
  shadow:      none                          # 🎨 Figma shadow = 0px 0px 0px 0px（無）
```

---

## 4. Variants　🎨🔗

Figma 元件集（節點 16545-8623）暴露以下 variant 軸：

```yaml
# ── 軸 1：Size ──────────────────────────────────────────────
Size:
  Small:   # height 38px；padding 12px；icon 16px（Default 密度）
  Large:   # height 48px；padding 16px；icon 20px（Comfortable 密度）

# ── 軸 2：Alignment（排列方向）────────────────────────────────
Alignment:
  Horizontal:   # tab-strip 橫排，indicator 在底部
  Vertical:     # tab-strip 直排（左側），indicator 在左側

# ── 軸 3：Count（頁籤數量）──────────────────────────────────
Count:
  values: [2, 3, 4, 5, 6, 7, 8]   # Figma 明確示意 2–8

# ── 軸 4：Scroll ─────────────────────────────────────────────
scroll:
  false:  # 無 scroll button（預設）
  true:   # 兩端加 chevron scroll button（超出寬度時使用）

# ── 狀態差異（在同一 Figma variant 中內建）──────────────────
# 每個 Count variant 中，Tab 2 = active（含 indicator + primary 色）
# Tab 1 及其餘 = inactive（on-surface-variant 色，無 indicator）
# disabled / hover / focus 無獨立 Figma variant，依 §5 行為推導
```

**密度與 Syncfusion Size 對應**：

| Figma Size | 設計文件密度 | Syncfusion cssClass 建議 |
|-----------|------------|------------------------|
| Small | Default | 預設（無需 cssClass） |
| Large | Comfortable | `e-bigger`（Syncfusion density class） |

---

## 5. States　🎨🔗

```yaml
# tab-item 層級狀態
inactive:
  label-color: "{color-sf-on-surface-variant}"  # 🔗 #3c4a5b
  indicator:   none

active:
  label-color: "{color-sf-primary}"             # 🔗 #2877ee
  indicator:   3px {color-sf-primary}           # 🔗 底部（Horizontal）/ 左側（Vertical）

hover:
  # 📋 Figma 未單獨設計 hover variant；依 Syncfusion Material 主題慣例
  # 推測：background-overlay = "{color-sf-on-surface-opacity5}" (rgb 15 23 42 / 0.05)
  # ⚠️ 需 app.css 實際測量確認——值見 app.css

focus:
  ring: "{ds-shadow-focus-ring1}"               # 🔗 白 1px 內環 + 黑 3px 外環（鍵盤 focus 指示）

disabled:
  label-color: "{color-sf-on-surface-variant-opacity38}"  # 🔗 rgb(60 74 91 / 0.38)（推導，Figma 未含 disabled 態）
  indicator:   none
  cursor:      not-allowed
  # ⚠️ disabled 態無 Figma 佐證——值見 app.css

# Full 層必含：資料展示型輔助狀態
loading:
  # 📋 Tab 本身不直接進入 loading 態；content-panel 用 Skeleton 元件填充
  content-panel: "<Skeleton>"                   # 見 Skeleton 元件

empty:
  # 📋 content-panel 空時顯示 Empty State（inbox icon + 文案）
  # 見 prototyper Empty State 規範

error:
  # 📋 content-panel 資料載入失敗時顯示 error state
  # Tab header 本身不變色；僅 content-panel 內處理
```

---

## 6. Behavior 行為　📋

| 互動 | 規則 |
|-----|------|
| 點擊 tab-item | 切換 active 到該 tab；content-panel 切換對應內容；active indicator 移動 |
| Scroll（scroll=true） | 點擊左/右（上/下）chevron 將 strip 捲動；已在首個 tab → 左箭頭 disabled；已在末項 → 右箭頭 disabled |
| Tab 數量超出容器寬度 | 啟用 scroll=true，不換行；overflow:clip 裁切多餘 tab |
| Vertical 模式 indicator | active indicator 為左側 3px 直線（Figma 實作為旋轉 90° 的 3px 橫條，position:absolute left:0） |
| content-panel 高度 | 由 `heightAdjustMode` 控制（預設 `Content`，隨內容自適應；設 `Auto` 固定為最高分頁高度） |
| 動態增刪 tab | 透過 Syncfusion `addTab()`/`removeTab()` API；新增後可選擇是否立即 activate |
| 切換動畫 | Syncfusion 預設無動畫；可透過 `animation` prop 設定 |
| Disable 特定 tab | 透過 `e-tabitem` 的 `:disabled="true"` prop 設定 |

**ERP 使用限制**：

- Form View 中 Tab 的「新增明細」按鈕須置於 `form-tabs__head` 右側，而非 tab header 本身（見 prototype template §Tab block）
- ERP 目前僅使用 Horizontal 模式；Vertical 模式保留規格但尚未在現行 prototype 啟用

---

## 8. Keyboard　📋

| 按鍵 | 行為 |
|-----|------|
| `Tab` | 將 focus 移入 tab strip（tablist）；若已在 tab strip 內，移出至 content-panel |
| `←` / `→` | Horizontal 模式：移動 focus 至前/後 tab item |
| `↑` / `↓` | Vertical 模式：移動 focus 至前/後 tab item |
| `Home` | 移動 focus 至第一個 tab item |
| `End` | 移動 focus 至最後一個 tab item |
| `Enter` / `Space` | 啟動（activate）當前 focus 的 tab item |
| `Delete` | 若 tab 為 closable（含 close button）則關閉（移除）該 tab |

> Focus 模式：Syncfusion Tab 預設為「Automatic selection」——focus 移動即觸發切換；可設定 `scrollStep` 控制每次捲動距離。

---

## 9. a11y 無障礙　📋

**ARIA Roles**：

```html
<!-- tab strip -->
<div role="tablist" aria-label="頁籤名稱">
  <!-- inactive tab -->
  <button role="tab" aria-selected="false" aria-controls="panel-1" id="tab-1" tabindex="-1">
    Tab 1
  </button>
  <!-- active tab -->
  <button role="tab" aria-selected="true" aria-controls="panel-2" id="tab-2" tabindex="0">
    Tab 2
  </button>
</div>
<!-- content panels -->
<div role="tabpanel" id="panel-1" aria-labelledby="tab-1" hidden>…</div>
<div role="tabpanel" id="panel-2" aria-labelledby="tab-2">…</div>
```

**對比度**：
- Inactive label `{color-sf-on-surface-variant}` rgb(60 74 91) on white：需實測，若 < 4.5:1 考慮換用 `{color-sf-on-surface}` rgb(15 23 42)
- Active label `{color-sf-primary}` rgb(40 119 238) on white：約 3.9:1（< AA 4.5:1）⚠️ 純色文字用途需留意；若需 AA 合規，參照 `reference_ds_aa_strong_tokens.md` 改用強色 token

**觸控目標**：
- Small 38px / Large 48px 高度；水平 padding 12/16px → touch target 寬度隨文字，短標籤可能 < 44px
- 建議最短標籤長度至少 2 個中文字（約 28px + padding = 52px，符合 44px 標準）

**Screen Reader**：
- Syncfusion 已原生加入 `role="tablist"` / `role="tab"` / `aria-selected`
- 需確保 `e-tabitem` 的 `header.text` 為描述性文字（非「頁籤 1」泛稱）

---

## 10. 跨平台 Adaptive　📋

| 斷點 | 行為 |
|-----|------|
| Desktop ≥ 1280px | 預設 Horizontal，8 tabs 可全顯 |
| Tablet 768–1024px | 超過 5–6 tabs 建議啟用 scroll=true；Vertical 改為 Bottom header-placement |
| Mobile < 768px | ERP 不做 mobile；若需 App 版，Vertical 改為底部 Tab Bar（native pattern）|

> ERP Web 當前僅針對 ≥ 1280px；跨平台實作待 App 設計介入時擴充。

---

## 11. Content 指引　📋

- **標籤文字**：名詞短語（「明細」「稅務」「備註」），不加動詞，不加數字 badge（數量顯示改用 tab header 的 badge slot）
- **長度**：2–6 個中文字；超過 6 字截斷並加 tooltip
- **Empty state 文案**：由 content-panel 內容負責，非 tab header（tab header 永遠顯示標籤）
- **Disabled tab tooltip**：說明不可選原因（如「尚未保存草稿，無法查看稅務」）

---

## 12. API / Props　📋

> 來源：`@syncfusion/ej2-vue-navigations`（playground `tab.vue` 確認）

**`<ejs-tab>` 元件 Props**：

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `selectedItem` | number | 0 | 當前選中的 tab index |
| `headerPlacement` | `'Top'` / `'Bottom'` / `'Left'` / `'Right'` | `'Top'` | tab strip 位置 |
| `orientation` | `'Horizontal'` / `'Vertical'` | `'Horizontal'` | 排列方向（Vertical 時搭配 headerPlacement='Left'） |
| `heightAdjustMode` | `'Auto'` / `'Content'` / `'Fill'` / `'None'` | `'Content'` | content-panel 高度模式 |
| `scrollStep` | number | 50 | 點擊 scroll button 每次捲動的 px 數 |
| `cssClass` | string | `''` | 自訂 CSS class（`e-bigger` 對應 Comfortable/Large 密度） |
| `animation` | object | `{…}` | 切換動畫設定（`{ previous: { effect, duration }, next: { effect, duration } }`） |
| `overflowMode` | `'Scrollable'` / `'Popup'` | `'Scrollable'` | 超出容器時的溢出模式 |

**`<e-tabitem>` 子元件 Props**：

| Prop | Type | Default | 說明 |
|---|---|---|---|
| `header` | `{ text: string, iconCss?: string }` | — | tab header 設定（必填） |
| `disabled` | boolean | false | 禁用該 tab |

**事件**：

| Event | Payload | 說明 |
|---|---|---|
| `selecting` | `{ selectingIndex, selectedIndex }` | tab 切換前（可 cancel） |
| `selected` | `{ selectedIndex }` | tab 切換後 |
| `created` | — | 元件初始化完成 |

**基本用法（Vue 3 `<script setup>`，純 JS）**：

```vue
<script setup>
import { TabComponent as EjsTab, TabItemDirective as ETabitem, TabItemsDirective as ETabitems } from '@syncfusion/ej2-vue-navigations'
import { provide } from 'vue'
provide('tab', [])
</script>

<template>
  <ejs-tab :selected-item="0" @selected="onTabSelected">
    <e-tabitems>
      <e-tabitem :header="{ text: '明細' }">
        <template #content>
          <!-- 明細內容 -->
        </template>
      </e-tabitem>
      <e-tabitem :header="{ text: '稅務' }">
        <template #content>
          <!-- 稅務內容 -->
        </template>
      </e-tabitem>
    </e-tabitems>
  </ejs-tab>
</template>
```

---

## 13. 關聯　🔗

- **Tokens**：`../athena-tokens.md` §Primary / §On-* 前景文字色 / §Surface / §Space / §Typography
- **語意對照**：`../athena-design.md` §主色與動作 / §文字色
- **Prototype 落地**：`prototyper` → `prototype/project/*.html` 的 `.form-tabs` / `.form-tabs__head` / `.form-tabs__body`（值權威：`prototyper/assets/app.css`）
- **Code**：`@syncfusion/ej2-vue-navigations`（`<ejs-tab>` / `<e-tabitems>` / `<e-tabitem>`）
- **Playground 參考**：`/Users/athena/working/ddd/syncfusion-playground/app/pages/playground/tab.vue`
- **相關元件**：`DataGrid.md`（Tab 內常見的明細表格）、`Button.md`（Tab header 右側的 add 按鈕）

---

## Figma 補入紀錄（2026-06-29）

> 來源：`get_variable_defs`（節點 16545-8623）＋ `get_design_context`（節點 16545-8619 / 16546-8006 / 16547-8927）＋ `get_screenshot`（節點 16545-8623）。

**✅ 已確認對映（Figma 變數 → `athena-tokens.md`）**：

| Figma 變數 | token | 用途 |
|-----------|-------|------|
| `ColorSf/on-surface-variant` (#3c4a5b) | `{color-sf-on-surface-variant}` | inactive label |
| `ColorSf/primary` (#2877ee) | `{color-sf-primary}` | active label + indicator |
| `ColorSf/surface` (#ffffff) | `{color-sf-surface}` | tab strip 背景 |
| `BG/$content-bg-color` (#ffffff) | `{color-sf-surface}`（別名） | tab strip 背景 |
| `ColorSf/transparent` (rgba 0) | `{color-sf-transparent}` | scroll button 背景 |
| `FontSizeSf/TextMd` (14) | `{font-size-sf-text-md}` | label 字級 |
| `FontWeightSf/Medium` (500) | `{font-weight-sf-medium}` | label 字重 |
| `FontFamilySf/Fontfamily` | `{font-family-sf-fontfamily}` | label 字體 |
| `Text/$content-text-color-alt1` (#3C4A5B) | `{color-sf-on-surface-variant}` | inactive label（別名確認） |
| `$primary` (#2877EE) | `{color-sf-primary}` | active indicator 色 |

**✅ Figma 量測確認（`get_design_context` 單一 variant）**：

| 量測項目 | Small（Default） | Large（Comfortable） |
|---------|-----------------|---------------------|
| Tab item 高度 | 38px | 48px |
| 水平 padding | 12px（`{ds-space-padding-large}`） | 16px（`{ds-space-padding-extra-large}`） |
| 垂直 padding（inactive） | 10px（`{ds-space-padding-large}`） | 10px |
| active-indicator 高度 | 3px | 3px |
| scroll button icon | 16px | 20px |
| label gap（icon↔text） | 8px（`{ds-space-padding-medium}`） | 8px |
| line-height | 1.3 | 20px（fixed，Body Content style） |
| letter-spacing | 0.1px | 0.24px（active，Body Content/14pt/Medium） |

**✅ Figma variant 軸（get_design_context metadata 結構確認）**：
- `Size`：`Small` / `Large`（各 4 個截面）
- `Alignment`：`Horizontal` / `Vertical`（各 4 個截面）
- `Count`：`2` / `3` / `4` / `5` / `6` / `7` / `8`
- `scroll`：`false` / `true`（boolean，scroll button 顯隱）

**⚠️ token gap（對不上，標為量測值待 token 化）**：

1. **active tab padding-top offset（Large 14px vs 一般 10px）**：active 態 Large 的 `pt-[14px]` 非標準 padding token（`{ds-space-padding-extra-large}` = 16px，`{ds-space-padding-large}` = 12px，皆不符）。量測值見 app.css，**不臆造 token**。
2. **indicator 高度 3px**：不在任何現有 spacing / border-width token 內（`--ds-borderwidth-small` = 1px，`--ds-borderwidth-medium` = 2px，`--ds-borderwidth-large` = 4px）。標 🎨 量測值，值見 app.css。
3. **hover / disabled state**：Figma 元件集無獨立 hover / disabled variant；§5 hover 推導依 Material state-layer 慣例，disabled 依 Syncfusion 預設行為。**兩者均無 Figma 佐證，值見 app.css**。
4. **active label letter-spacing 差異**：Large active 用 Body Content/14pt/Medium（letterSpacing: 0.24px），Small 用 Label/14pt/Medium（letterSpacing: 0.1px）。兩者字級相同但 spacing 不同，Figma 未統一——已照實記錄，不調平。

**📋 人工補充（Figma 讀不出）**：§6 Behavior / §8 Keyboard / §9 a11y / §10 Adaptive / §11 Content — 均依 WCAG 2.1、Syncfusion 文件、ERP prototype 現行慣例填入。
