---
name: Accordion（折疊面板）
category: 佈局與導覽
tier: lite
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: JhcpyIEEzSChcEXMRJUiIm/28b4d12d9f7ff11aa8c010ac8f5507183a33428c
version: v0.1
last-synced: 2026-03-22
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺字面值權威＝`prototyper/assets/app.css`；本檔＝契約。

## 1. 概述　📋

**可摺疊分組容器**：將大量相關內容分割為可展開 / 收合的區塊，減少頁面認知負載。ERP 常見場景：表單次要欄位分組（「進階設定」）、FAQ / 說明、設定分頁內的子分組。

何時不用 Accordion：
- 區塊間需要同時瀏覽比較 → 改 `Tab.md`
- 需逐步引導用戶完成流程 → 改 `Stepper.md`
- 內容量少（< 3 項）且不需隱藏 → 直接展示，無需折疊

## 2. Anatomy　🎨

```
.e-accordion
  └─ .e-acrdn-item              ← 每個折疊項
      ├─ .e-acrdn-header        ← 點擊展開 / 收合
      │   ├─ .e-acrdn-header-content  ← 標題文字
      │   └─ .e-toggle-icon           ← chevron（展開時旋轉 180°）
      └─ .e-acrdn-panel         ← 內容區（隱藏時 height: 0）
          └─ .e-acrdn-content
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
header:
  height:         "48px"                                     # 🎨 量測值
  padding-h:      "{ds-space-padding-extra-large}"           # 🔗 16px
  bg:             "{color-sf-surface}"                       # 🔗
  fg:             "{color-sf-on-surface}"                    # 🔗
  font:           "{font-size-sf-text-md} / {font-weight-sf-medium}"  # 🔗 14px / 500
  border-b:       "{ds-borderwidth-small} {color-sf-outline-variant}" # 🔗 項目間分隔

toggle-icon:
  size:           "16px"                                     # 🎨
  fg:             "{color-sf-on-surface-variant}"            # 🔗
  transition:     "transform 200ms ease"                     # 📋

content:
  padding:        "{ds-space-padding-extra-large}"           # 🔗 16px 四邊
  bg:             "{color-sf-surface}"                       # 🔗
```

## 4. Variants　📋

```yaml
expandMode-Multiple: "多個項目可同時展開（預設）"             # expandMode='Multiple'
expandMode-Single:   "同時只能開啟一個項目（手風琴效果）"      # expandMode='Single'
nested:              "Accordion 巢狀；ERP 最多 2 層"         # 📋 三層以上請用 Tab 分頁
```

## 5. States　🎨🔗

```yaml
header:
  default:      { bg: "{color-sf-surface}", fg: "{color-sf-on-surface}" }
  hover:        { bg: "見 app.css" }                         # 🎨 opacity8 疊層，app.css 值權威
  expanded:     { fg: "{color-sf-primary}" }                 # 🔗 主色標示已展開
  focused:      { outline: "見 app.css" }                    # 🎨 focus ring，app.css 值權威
  disabled:     { fg: "{color-sf-on-surface-opacity38}", cursor: "not-allowed" }  # 🔗

panel:
  expanding:    { height: "0 → auto（animation: SlideDown）" }
  collapsing:   { height: "auto → 0（animation: SlideUp）" }
```

## 9. a11y　📋

- 每個 header 元素為 `<button>` 帶 `aria-expanded`（true/false）+ `aria-controls="<panel-id>"`。
- 對應面板帶 `id` + `role="region"` + `aria-labelledby="<header-id>"`。
- 鍵盤操作：
  - `Tab` / `Shift+Tab`：在 header 間移動
  - `Enter` / `Space`：展開 / 收合目前 header
  - `↑↓`（選配）：在項目 header 間快速導覽
- 展開 / 收合動畫不超過 200ms，或提供 `prefers-reduced-motion` 停用。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| items | AccordionItem[] | [] | `{ header, content, expanded?, disabled?, cssClass? }` |
| expandMode | `Single` \| `Multiple` | `Multiple` | 展開模式 |
| animation | Object | `{ expand: { effect: 'SlideDown', duration: 400 }, collapse: { effect: 'SlideUp', duration: 400 } }` | 動效設定（設 `duration: 0` 可關閉動畫） |
| enableRtl | boolean | false | RTL 支援 |

事件：`@expanding(e)` / `@expanded(e)` / `@collapsing(e)` / `@collapsed(e)`

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface / §On-* / §Outline / §Space / §Typography
- 值權威：`prototyper/assets/app.css`
- 相關：`Tab.md`（同時比較多組內容時改用）；`Stepper.md`（流程導引改用）
- Code：`@syncfusion/ej2-vue-navigations`（`AccordionPlugin`，`<ejs-accordion>`）
