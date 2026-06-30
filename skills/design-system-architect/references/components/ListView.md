---
name: ListView（清單檢視）
category: 資料展示與表格
tier: lite           # 單欄列表，無多欄排序 / 凍結 / 行內編輯生命週期
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: —        # 🎨 Figma 補入時填 <FILE_KEY>/<NODE_ID>（FAI2 componentKey: 42acc2c4563da884de6cb532acef0417a30d701c）
version: v0.2
last-synced: 2026-06-30
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺字面值權威＝`prototyper/assets/app.css`；本檔＝契約。

## 1. 概述　📋

**單欄式列表**：適合以「卡片 row」或「avatar + 主文字 + meta」格式呈現的資料集，不需要跨欄比較時使用。常見場景：通知列表、選取對話框的候選清單、設定項目列表。

何時用 DataGrid 替代：需跨多欄比較、需批次選取 + 排序 / 篩選 / 凍結 → 改 DataGrid；需 timeline 呈現 → 改 Gantt / Schedule。

## 2. Anatomy　🎨

```
.list-view
  ├─ .list-view__header       ← 可選；title + 計數 badge
  ├─ .list-view__list         ← <ul role="listbox"> 或 <ul role="list">
  │   └─ .list-view__item     ← <li>
  │       ├─ .list-view__leading   ← 可選；avatar / icon / checkbox
  │       ├─ .list-view__body      ← primary-text + secondary-text
  │       └─ .list-view__trailing  ← 可選；meta text / action button / chevron
  └─ .list-view__footer       ← 可選；Pager 或「載入更多」
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
item:
  min-height:  "48px"                                # 🎨 量測值（對應 WCAG 觸控 44px 目標）
  padding-v:   "{ds-space-padding-medium}"           # 🔗 8px 上下
  padding-h:   "{ds-space-padding-extra-large}"      # 🔗 16px 左右
  bg:          "{color-sf-surface}"                  # 🔗 白底
  divider:     "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗 1px 列間分隔
  gap:         "{ds-space-padding-large}"            # 🔗 12px（leading ↔ body ↔ trailing）

primary-text:
  font:        "{font-size-sf-text-md} / {font-weight-sf-medium}"  # 🔗 14px / 500
  fg:          "{color-sf-on-surface}"               # 🔗

secondary-text:
  font:        "{font-size-sf-text-sm}"              # 🔗 12px
  fg:          "{color-sf-on-surface-variant}"       # 🔗

leading:
  avatar-size: "40px"                                # 🎨 量測值
  icon-size:   "24px"                                # 🎨

trailing:
  meta-font:   "{font-size-sf-text-sm}"              # 🔗 12px
  meta-fg:     "{color-sf-on-surface-variant}"       # 🔗
```

## 4. Variants　🎨🔗

```yaml
default:       "leading=none；body=primary+secondary；trailing=meta"
with-avatar:   "leading=40px avatar；適用用戶 / 聯絡人清單"
with-checkbox: "leading=checkbox；多選場景（選取對話框）"
action-item:   "trailing=button；如通知列表的「標記已讀」"
dense:
  item-min-height: "40px"                           # 🎨
  padding-v:   "{ds-space-padding-small}"           # 🔗 4px；下拉選單候選清單
```

## 5. States　🎨🔗

```yaml
default:     { bg: "{color-sf-surface}" }
hover:       { bg: "見 app.css"  }                  # 🎨 primary 疊白實色，app.css 值權威
selected:    { bg: "見 app.css", fg: "{color-sf-primary}" }  # 🎨 selected 高亮，app.css 值權威
disabled:    { fg: "{color-sf-on-surface-opacity38}", cursor: "not-allowed" }  # 🔗
loading:     "Skeleton 列佔位（見 Skeleton.md）"
empty:       ".empty-state：inbox-outline icon + 訊息；**禁**保留空 ul"
```

## 9. a11y　📋

- 選取場景用 `role="listbox"` + 每項 `role="option"` + `aria-selected`；瀏覽場景用 `role="list"` + `role="listitem"`。
- 鍵盤：`↑↓` 在項目間移動（listbox 模式）；`Enter` / `Space` 選取；`Tab` 離開元件。
- 每項至少 48×48px 觸控目標（行動端）。
- 狀態不可僅靠顏色區分（selected 需視覺符號或文字輔助）。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| dataSource | Object[] | [] | 清單資料來源（每筆至少含 `id` / `text` 欄位） |
| showHeader | boolean | false | 顯示清單標題列 |
| headerTitle | string | '' | 標題文字（需搭配 `showHeader: true`） |
| showCheckBox | boolean | false | 顯示核取方塊（多選模式） |
| fields | `{ text?, groupBy?, child? }` | — | 欄位對應：`text`=顯示欄、`groupBy`=分組欄、`child`=子清單欄 |

事件：`@select({ data, isChecked? })` — 項目點擊 / 選取時觸發

> **Code**：`@syncfusion/ej2-vue-lists`（`ListViewComponent`）。`density` / `enablePager` 為設計層抽象概念，實作時分別以 Tailwind spacing 調整與外掛 Pager.md 元件完成。

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface / §On-* / §Space / §Typography
- 值權威：`prototyper/assets/app.css`（canonical CSS）
- 同層：`DataGrid.md`（多欄時改用）；`Pager.md`（頁尾分頁）；`Skeleton.md`（loading 佔位）
- Code：`@syncfusion/ej2-vue-lists`
