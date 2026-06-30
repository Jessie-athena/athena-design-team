---
name: Tooltip（工具提示）
category: 回饋與狀態
tier: lite           # 懸浮說明泡泡，單純定位 + 文字展示，無資料生命週期
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16576:117024
version: v0.1
last-synced: 2026-06-30
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺字面值（寬度 / pointer 尺寸 / 圓角）權威＝`prototyper/assets/app.css`；本檔＝契約。

## 1. 概述　📋

**懸浮工具提示**：游標 hover 或 focus 時出現的輕量說明泡泡，展示圖示或欄位的補充文字。不可互動（無按鈕）；有時間限制（建議 3–5 秒後可關閉）。

何時**不用**：需要操作按鈕或連結 → 改用 Popover；需要多欄內容 → PredefinedDialog。

## 2. Anatomy　🎨

```
.tooltip（dark bg, 圓角, 可選 shadow）
  ├─ .tt__title（可選）  ← icon + 標題文字
  ├─ .tt__description    ← 說明文字（主要內容）
  └─ .tt__pointer（可選）← 指向目標的三角形，依 pointer 方向定位
```

Pointer 渲染：`absolute` 定位三角形 SVG；9 個位置（None / Top L/C/R / Bottom L/C/R / Center L/R）。

## 3. 視覺規格 Tokens　🎨🔗

```yaml
container:
  bg:              "{color-sf-inverse-surface}"              # 🎨 #303034（深炭色）
  radius:          "{ds-radius-md}"                         # 🔗 4px
  shadow（ON）:    "{shadow/md}"                            # 🎨 drop-shadow 1px + 3px

sizing（Small）:
  width:           "260px"                                  # 🎨 w-[260px]（max-w）
  padding:         "12px"                                   # 🎨 p-[12px]

sizing（Large）:
  width:           "320px"                                  # 🎨 w-[320px]
  padding:         "16px"                                   # 🎨 p-[16px]

title:
  font:            "{font-size-sf-text-md} / {font-weight-sf-medium}"  # 🎨 14px/500
  fg:              "{color-sf-inverse-on-surface}"          # 🎨 #F2F0F4（淺色文字）
  tracking:        "0.24px"                                 # 🎨
  gap:             "4px（no-shadow）/ 5px（shadow）"         # 🎨 icon ↔ text 間距

description（Small）:
  font:            "{font-size-sf-text-md} / 400"           # 🎨 14px/400，line-height 1.5
  fg:              "{color-sf-inverse-on-surface}"          # 🎨
  tracking:        "0.24px"                                 # 🎨

description（Large）:
  font:            "{font-size-sf-text-lg} / 400"           # 🎨 16px/400，line-height 1.5
  fg:              "{color-sf-inverse-on-surface}"          # 🎨

icon（title）:     "16px"                                   # 🎨

pointer（Small）:
  size:            "14×10px"                                # 🎨 width×height
  offset-top/bottom: "left-aligned at 4px（Top Left）or centered"  # 🎨

pointer（Large）:
  size:            "18×12px"                                # 🎨

pointer-color:     "{color-sf-inverse-surface}"             # 🎨 與 bg 同色，無可見邊框
```

## 4. Variants　🎨🔗

```yaml
# ── Figma Variants（FAI2 node 16576:117024） ──
Size=Small, Pointer=None:
  width: "260px", padding: "12px", no-pointer

Size=Small, Pointer=Top Left:
  pointer: "14×10px，top-[-10px] left-[4px]"

Size=Small, Pointer=Bottom Left:
  pointer: "14×10px，bottom-[-10px]，rotate 180°"

Size=Small, Pointer=Center Left:
  pointer: "10×14px（rotated 90°），left-[-10px] vertically-centered"

Size=Large, Pointer=None:
  width: "320px", padding: "16px"

Size=Large, Pointer=Top Center:
  pointer: "18×12px，top-[-12px] horizontally-centered"

shadow=ON / OFF:  "各 pointer 方向均有 shadow ON/OFF 兩版"

# ── 所有 Pointer 位置 ──
positions: "None / Top Left / Top Center / Top Right / Bottom Left / Bottom center / Bottom Right / Center Left / Center Right"

# ── 行為 Variants ──
title=ON:    "顯示標題列（icon 可選）"
title=OFF:   "僅顯示說明文字"
icon=ON:     "title 左側 16px 語意圖示"
```

## 5. States　🎨🔗

```yaml
visible:   "hover / focus 觸發後出現，`opacity:1 + transition`"
hidden:    "預設隱藏（`display:none` 或 `opacity:0`）"
```

## 9. a11y　📋

- Tooltip 觸發元素需 `aria-describedby="[tooltip-id]"`（description）或 `aria-labelledby`（title）。
- Tooltip 本身 `role="tooltip"` + `id`；`pointer-events:none`（不可 focus）。
- 鍵盤：觸發元素 focus 時顯示；`Esc` 或 Tab 離開時隱藏。
- 不得是唯一的資訊來源（視障用戶可能無法觸發）。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| size | 'Small' \| 'Large' | 'Small' | 尺寸 |
| pointer | 'None' \| 'Top Left' \| 'Top Center' \| 'Top Right' \| 'Bottom Left' \| 'Bottom center' \| 'Bottom Right' \| 'Center Left' \| 'Center Right' | 'None' | 指針方向 |
| shadow | boolean | false | 是否套用 shadow-md |
| title | boolean | true | 顯示標題列 |
| icon | boolean | false | title 左側圖示 |
| description | string | — | 說明文字 |

事件：`@open` / `@close`（透過 Syncfusion TooltipComponent 監聽）

> **Code**：`@syncfusion/ej2-vue-popups`（`TooltipComponent`）；`position` prop 對應 Figma pointer 位置（如 `"TopLeft"` `"BottomCenter"`）。

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Inverse-Surface / §Space / §Radius / §Shadow / §Typography
- 值權威：`prototyper/assets/app.css`（canonical CSS）
- 同層：`PredefinedDialog.md`（需互動時改用）；`Badge.md`（計數輔助說明）
- Code：`@syncfusion/ej2-vue-popups`（TooltipComponent）
