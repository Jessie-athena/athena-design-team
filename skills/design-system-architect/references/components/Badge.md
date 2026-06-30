---
name: Badge（徽章）
category: 回饋與狀態
tier: lite           # 原子計數/狀態標籤，無互動或資料生命週期
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: JhcpyIEEzSChcEXMRJUiIm/13245:4932
version: v0.1
last-synced: 2026-06-30
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺字面值（高度 / padding / 字級）權威＝`prototyper/assets/app.css`；本檔＝契約。

## 1. 概述　📋

**徽章（Badge）**：附著於其他元件（Avatar、Nav item、Button）上方或右側，顯示計數、狀態字或未讀圓點的微型標籤。本身不可點擊；提供視覺通知密度而非資訊詳情。

何時用 Chips：需要互動（點擊可篩選 / 刪除）→ Chips；Badge 為純顯示。

## 2. Anatomy　🎨

```
.badge（inline-flex, pill 或 slightly-rounded）
  ├─ [icon]（可選）  ← 10px 方向箭頭等微圖示
  └─ .badge__text   ← 數字 / 字母 / 文字（Word mode）

Dot mode：無文字，僅 8px（Small）/ 12px（Large）實心圓
Letter mode：單一字母，16px（Small）/ 20px（Large）正方形
Word mode：文字標籤，35px（Small h=16px / Large h=20px）pill
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── Small ──
small-word:
  height:       "16px"                                   # 🎨 h-[16px]
  padding:      "px-[8px]"                               # 🎨
  radius:       "12px（Full Rounded）/ {ds-radius-md}（Rounded）"  # 🎨

small-letter:
  size:         "16×16px"                                # 🎨
  radius:       "12px（Full Rounded）/ {ds-radius-md}（Rounded）"

small-dot:
  size:         "8×8px"                                  # 🎨

# ── Large ──
large-word:
  height:       "20px"                                   # 🎨 h-[20px]
  padding:      "見 app.css"                             # 🎨

large-letter:
  size:         "20×20px"                                # 🎨

large-dot:
  size:         "12×12px"                                # 🎨

# ── 通用字型 ──
font:           "{font-size-sf-text-xs} / {font-weight-sf-medium}"  # 🎨 11px/500，tracking 0.1px

# ── Contrast Ascent（Filled 實色）──
ascent-primary-bg:     "{color-sf-primary}"              # 🎨 #2877EE
ascent-primary-fg:     "{color-sf-surface}"              # 🔗 white
ascent-danger-bg:      "{color-sf-danger}"               # 🔗
ascent-danger-fg:      "{color-sf-surface}"              # 🔗
ascent-success-bg:     "{color-sf-success}"              # 🔗
ascent-warning-bg:     "{color-sf-warning}"              # 🔗
ascent-info-bg:        "{color-sf-info}"                 # 🔗
ascent-secondary-bg:   "{color-sf-on-surface}"           # 🔗 深色
ascent-light-bg:       "見 app.css"                      # 🎨 淺灰
ascent-dark-bg:        "{color-sf-on-surface-variant}"   # 🔗

# ── Contrast Subtle（淡色）──
subtle-primary-bg:     "見 app.css"                      # 🎨 rgba(primary, 0.12) 等
subtle-primary-fg:     "{color-sf-primary}"              # 🔗
subtle-danger/success/…: "見 app.css"                    # 🎨
```

## 4. Variants　🎨🔗

```yaml
# ── Figma Variants（FAI2 node 13245:4932） ──
Size=Small, Shape=Full Rounded, Type=Primary, Contrast=Ascent, Mode=Word:
  h: "16px", px: "8px", radius: "12px", bg: "{color-sf-primary}", fg: "white"

Size=Large, Shape=Full Rounded, Type=Primary, Contrast=Ascent, Mode=Word:
  h: "20px"

Size=Small, Shape=Rounded, Type=*:
  radius: "{ds-radius-md}"（矩形輕圓角版）

# ── 維度矩陣 ──
Size:     Small / Large
Shape:    Full Rounded（pill）/ Rounded（rect with light radius）
Type:     Primary / Secondary / Info / Success / Warning / Danger / Light / Dark
Contrast: Ascent（filled solid）/ Subtle（tinted light bg + colored text）
Mode:     Word（文字）/ Letter（單字母）/ Dot（圓點）
```

## 5. States　🎨🔗

Badge 無互動狀態；外觀固定。動態行為（計數更新、出現/消失）由父元件控制。

## 9. a11y　📋

- Badge 通常為裝飾性；需同時提供 `aria-label` 給 screen reader（如 `aria-label="3 則未讀通知"`）。
- Dot mode：`aria-label="有新通知"` 或 `aria-hidden="true"` 配合父元素說明。
- 顏色非唯一識別：Subtle contrast 對比度需驗證（11px/500 需達 4.5:1，使用 *-strong token 如必要）。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| size | 'Small' \| 'Large' | 'Small' | 尺寸 |
| shape | 'Full Rounded' \| 'Rounded' | 'Full Rounded' | 圓角類型 |
| type | 'Primary' \| 'Secondary' \| 'Info' \| 'Success' \| 'Warning' \| 'Danger' \| 'Light' \| 'Dark' | 'Primary' | 語意色 |
| contrast | 'Ascent' \| 'Subtle' | 'Ascent' | 填色模式 |
| mode | 'Word' \| 'Letter' \| 'Dot' | 'Word' | 顯示模式 |
| text | string | '99+' | Word / Letter 模式顯示文字 |
| icon | boolean | false | Word 模式左側微圖示 |

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §Danger / §Success / §Warning / §Info / §Secondary / §Typography
- 值權威：`prototyper/assets/app.css`（canonical CSS）
- 同層：`Chips.md`（互動標籤）；`Avatar.md`（Badge 常疊放於 Avatar 右上角）；`Message.md`（內嵌計數 Badge）
- Code：`@syncfusion/ej2-vue-notifications`（BadgeComponent）或客製 `.badge` markup
