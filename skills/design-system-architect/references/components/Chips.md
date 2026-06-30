---
name: Chips（標籤選取器）
category: 回饋與狀態
tier: lite           # 互動標籤（篩選/選取/刪除），單一元件無資料列生命週期
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: JhcpyIEEzSChcEXMRJUiIm/12736:37836
version: v0.1
last-synced: 2026-06-30
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺字面值（高度 / 字級 / 圓角）權威＝`prototyper/assets/app.css`；本檔＝契約。

## 1. 概述　📋

**Chip（標籤）**：可點擊的小型標籤，用於篩選條件選取、分類標記或操作集合（Assist Chip）。有別於 Badge（純顯示），Chip 具備 hover / active / selected 互動狀態，可附帶刪除按鈕（right icon）或 avatar。

何時用 Badge：不可點擊的計數/狀態標示 → Badge；可互動的篩選標籤 → Chips。

## 2. Anatomy　🎨

```
.chip（inline-flex, pill 或 rounded-rect）
  ├─ .chip__avatar（可選）  ← 18px 圓形頭像（avatar=ON 時）
  ├─ .chip__left-icon（可選）← 12px 圖示（leftIcon=ON 時）
  ├─ .chip__label           ← 文字標籤
  └─ .chip__right-icon（可選）← 12px 關閉/確認圖示（rightIcon=ON 時）
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── Extra Small（已 Figma 確認） ──
xs:
  height:        "20px"                                   # 🎨 h-[20px]
  radius（Full Rounded）: "{ds-radius-full}"              # 🎨 rounded-[48px] pill
  radius（Rounded）: "{ds-radius-md}"                     # 🎨 4px
  padding:       "pl-[1px] pr-[8px]"                      # 🎨 right-only offset（label 左 pl-[7px]）
  label-font:    "{font-size-sf-text-xs} / {font-weight-sf-medium}"  # 🎨 11px/500，tracking 0.1px
  icon-size:     "12px"                                   # 🎨 left/right icon
  avatar-size:   "18px"                                   # 🎨 圓形 avatar（rounded-[12px]）

# ── Small / Medium（FAI2 frame 存在但量測值待確認） ──
small:
  height:        "見 app.css"                             # 🎨 預估 24px
medium:
  height:        "見 app.css"                             # 🎨 預估 32px

# ── 模式色（Filled Ascent Primary 示範） ──
filled-primary-bg:   "{color-sf-primary}"                 # 🎨 #2877EE
filled-primary-fg:   "{color-sf-on-primary}"              # 🔗 white

outlined-primary-border: "{color-sf-primary}"             # 🔗
outlined-primary-fg:     "{color-sf-primary}"             # 🔗
outlined-bg:             "transparent"                    # 🔗

flat-bg:         "transparent"                            # 🔗
flat-fg:         "{color-sf-on-surface}"                  # 🔗
flat-border:     "none"                                   # 🔗
```

## 4. Variants　🎨🔗

```yaml
# ── Figma Variants（FAI2 node 12736:37836） ──
Size=Extra Small, Shape=Full Rounded, Type=Primary, Mode=Filled, State=Enabled:
  h: "20px", radius: "48px", bg: "{color-sf-primary}", fg: "white"

Size=Extra Small, Shape=Full Rounded, Type=Primary, Mode=Outlined:
  h: "20px", border: "{color-sf-primary}", fg: "{color-sf-primary}"

Size=Extra Small, Shape=Full Rounded, Type=Primary, Mode=Flat:
  h: "20px", no border, fg: "{color-sf-on-surface}"

Size=Extra Small, Shape=Rounded（矩形圓角）:
  radius: "{ds-radius-md}"（4px）

# ── 維度矩陣 ──
Size:    Extra Small / Small / Medium（Small/Medium 量測值待確認）
Shape:   Full Rounded（pill）/ Rounded（矩形輕圓角）
Type:    Primary / Secondary / Info / Success / Warning / Danger / Light / Dark
Mode:    Filled / Outlined / Flat
State:   Enabled / Hover / Active / Focus / Disabled

# ── 配件 Variants ──
avatar=ON:     "leading 18px 圓形頭像"
leftIcon=ON:   "leading 12px 圖示"
rightIcon=ON:  "trailing 12px 圖示（close/check）"
label1=OFF:    "隱藏文字（僅圖示或 avatar 模式）"
```

## 5. States　🎨🔗

```yaml
enabled:   "預設靜止態"
hover:     "bg 略深 / border 加深；見 app.css"
active:    "按下態，更深底色"
focus:     "顯示 focus ring（{ds-borderwidth-medium} {color-sf-primary}）"
disabled:  "opacity 降低；cursor=not-allowed；pointer-events:none"
selected:  "Filled 且選中：通常加 check icon 於 left side"
```

## 9. a11y　📋

- 可選 Chip：`role="option"` 在 `role="listbox"` 群組中；`aria-selected`。
- 篩選 Chip 群：`role="group"` 包覆，`aria-label="篩選條件"`。
- 關閉 icon `aria-label="移除 [標籤名稱]"` 。
- 鍵盤：`Tab` focus；`Enter` / `Space` 選取；Delete / Backspace 移除（rightIcon 為 close 時）。
- 觸控目標：Chip 本身需 ≥ 44px 觸控熱區（可用 padding 補足不含視覺高度部分）。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| size | 'Extra Small' \| 'Small' \| 'Medium' | 'Extra Small' | 尺寸 |
| shape | 'Full Rounded' \| 'Rounded' | 'Full Rounded' | 圓角類型 |
| type | 'Primary' \| 'Secondary' \| ... | 'Primary' | 語意色 |
| mode | 'Filled' \| 'Outlined' \| 'Flat' | 'Filled' | 顯示模式 |
| state | 'Enabled' \| 'Hover' \| 'Active' \| 'Focus' \| 'Disabled' | 'Enabled' | 互動狀態 |
| label | string | 'Assist Chip' | 標籤文字 |
| label1 | boolean | true | 是否顯示文字 |
| avatar | boolean | false | 顯示 leading avatar |
| leftIcon | boolean | false | 顯示 leading icon |
| rightIcon | boolean | false | 顯示 trailing icon |

事件：`@click`（選取/取消）；`@remove`（rightIcon close 點擊）

> **Code**：`@syncfusion/ej2-vue-buttons`（`ChipListComponent`）。

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §On-* / §Radius / §Typography
- 值權威：`prototyper/assets/app.css`（canonical CSS）
- 同層：`Badge.md`（純顯示不可互動）；`Avatar.md`（Chip 內嵌 avatar）
- Code：`@syncfusion/ej2-vue-buttons`（ChipListComponent）
