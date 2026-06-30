---
name: Card（卡片容器）
category: 佈局與導覽
tier: lite
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）；客製包裝見 DsCard.md（待建立）
figma-node: —       # 🎨 Figma 補入時填 <FILE_KEY>/<NODE_ID>
version: v0.1
last-synced: —
---

> 依 `../component-doc-schema.md`（Lite 層）產出。Syncfusion Card 以 CSS class 為主，無獨立 Vue plugin；ERP 客製包裝為 `DsCard`（待建立設計文件）。

## 1. 概述　📋

**卡片容器**：具有圓角與背景的區塊容器，將相關內容群組化。ERP 用途：Dashboard 統計區塊、設定檔分組、KPI 指標方塊。

何時不用 Card：主內容區的 section 分組 → 直接用 `form-section`（帶 bar + title 的分組，無 Card 外框）；需要 sticky 固定的摘要區 → `SummaryCard.md`。

## 2. Anatomy　🎨

```
.e-card
  ├─ .e-card-header（選配）
  │   ├─ .e-card-header-title      ← 主標題
  │   └─ .e-card-sub-title         ← 副標題 / 說明（選配）
  ├─ .e-card-image（選配）         ← 圖片區（ERP 較少用）
  ├─ .e-card-content               ← 主要內容插槽
  └─ .e-card-actions（選配）       ← 底部動作按鈕區
      ├─ [Button × n]
      └─ .e-card-separator         ← 按鈕間分隔（選配）
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
card:
  bg:             "{color-sf-surface}"                       # 🔗 白底（ERP 無 shadow 設計）
  border:         "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗 細邊框代替 shadow
  radius:         "{ds-radius-large}"                        # 🔗 8px
  padding:        "{ds-space-padding-extra-large}"           # 🔗 16px 內距

header:
  title-font:     "{font-size-sf-text-md} / {font-weight-sf-semi-bold}"  # 🔗 14px / 600
  title-fg:       "{color-sf-on-surface}"                    # 🔗
  subtitle-font:  "{font-size-sf-text-sm}"                   # 🔗 12px
  subtitle-fg:    "{color-sf-on-surface-variant}"            # 🔗
  padding-b:      "{ds-space-padding-medium}"                # 🔗 8px（header ↔ content 間距）

actions:
  padding-t:      "{ds-space-padding-medium}"                # 🔗 8px
  gap:            "{ds-space-padding-small}"                 # 🔗 4px（按鈕間）
  border-t:       "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗（選配分隔線）
```

## 4. Variants　📋

```yaml
default:          "header + content；無 shadow"              # ERP 標準
with-actions:     "content + actions 按鈕區"
horizontal:       "圖片（左）+ content（右）並排"            # Dashboard 橫版統計卡
clickable:        "整張 card 為可點擊區域（role=button）"
flat:             "無 border / 無 shadow（DsCard 預設）"     # 見 DsCard.md
```

## 5. States　🎨🔗

```yaml
default:          { bg: "{color-sf-surface}", border: "{ds-borderwidth-small} {color-sf-outline-variant}" }  # 🔗
hover（clickable）:{ box-shadow: "見 app.css", cursor: "pointer" }  # 🎨 輕微提升感
focus（clickable）:{ outline: "見 app.css" }                  # 🎨
disabled:         { opacity: "0.38", cursor: "not-allowed" } # 📋
```

## 9. a11y　📋

- 一般容器用語意 HTML（`<section>` + `<h2>` / `<h3>` 標題）；不需 ARIA role。
- Clickable Card 加 `role="button"` + `tabindex="0"` + `@click` + `@keydown.enter`。
- Actions 區的按鈕各帶明確 `aria-label`（圖示按鈕）。

## 12. API / Props　📋

Syncfusion Card 為純 CSS 元件，無 Vue plugin：

```vue
<!-- 使用 CSS class 組合 -->
<div class="e-card">
  <div class="e-card-header">
    <div class="e-card-header-title">標題</div>
  </div>
  <div class="e-card-content">內容</div>
</div>
```

ERP 客製包裝（見 DsCard.md 待建立）：

| Prop | Type | Default | 說明 |
|---|---|---|---|
| title | string | — | 卡片標題 |
| subtitle | string | — | 副標題 |
| noPadding | boolean | false | 移除內距（圖表滿版填充時使用）|
| clickable | boolean | false | 整張 card 可點擊 |

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface / §On-* / §Outline / §Space / §Radius
- 值權威：`prototyper/assets/app.css`
- 客製：`DsCard.md`（待建立，ERP 包裝層）；`SummaryCard.md`（財務 / 進銷存單據摘要卡，有 sticky + stepper）
- Code：`@syncfusion/ej2-layouts`（CSS only，`e-card` class）；`components/ds/DsCard.vue`
