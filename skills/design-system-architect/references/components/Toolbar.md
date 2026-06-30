---
name: Toolbar（工具列）
category: 佈局與導覽
tier: lite
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: —       # 🎨 Figma 補入時填 <FILE_KEY>/<NODE_ID>
version: v0.1
last-synced: —
---

> 依 `../component-doc-schema.md`（Lite 層）產出。ERP 實作對應 prototyper 的 `.toolbar`；本檔為其設計契約。

## 1. 概述　📋

**操作工具列**：位於清單 / 表單上方，承載主要 CTA（新增）、被選取後的批次操作，以及右側次要操作（模式切換、欄位設定）。ERP 兩種用法：

1. **List View toolbar**：主要 CTA + 批次操作 + 模式切換 + 欄位設定
2. **Tab block header**：Tab 標籤列右側固定「新增明細」按鈕

何時不用：Form Header action 區（Footer 動作群）不是 Toolbar；主要導覽不是 Toolbar（改 AppBar / Sidebar）。

## 2. Anatomy　🎨

```
.toolbar（.e-toolbar）
  ├─ .toolbar__primary              ← 左側主操作區
  │   ├─ [template v-if="selectedRows.length === 0"]
  │   │   └─ [Button] 新增（主 CTA）
  │   └─ [template v-else]
  │       ├─ .toolbar__count        ← "已選 N 筆"
  │       └─ [Button × n]           ← 批次操作（批次提交、批次刪除…）
  └─ .toolbar__right                ← 右側次要操作區
      ├─ .view-switch               ← List / Form 模式切換（ButtonGroup）
      └─ [IconButton] 欄位設定      ← tune icon
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
bar:
  min-height:     "48px"                                     # 🎨 量測值
  padding-v:      "{ds-space-padding-medium}"                # 🔗 8px 上下
  padding-h:      "{ds-space-padding-extra-large}"           # 🔗 16px 兩端（對齊 Grid 邊界）
  bg:             "{color-sf-surface}"                       # 🔗
  border-b:       "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗

item-gap:
  between:        "{ds-space-padding-medium}"                # 🔗 8px（按鈕間距）

count-label:
  font:           "{font-size-sf-text-sm} / {font-weight-sf-medium}"  # 🔗 12px / 500
  fg:             "{color-sf-on-surface-variant}"            # 🔗
```

## 4. Variants　📋

```yaml
default（未選取）:  "左 = 主 CTA（新增）；右 = view-switch + 欄位設定"
batch-selected:    "左 = 已選 N 筆 + 批次操作；右 = 不變"
tab-header:        "左 = Tab 標籤列；右 = 固定新增明細按鈕（無 view-switch）"
```

## 5. States　🎨🔗

```yaml
bar:
  default:        { bg: "{color-sf-surface}" }               # 🔗
  batch-active:   { bg: "{color-sf-primary-container}" }     # 🔗 有選取時背景提示（選配）

view-switch-btn:
  inactive:       { fg: "{color-sf-on-surface-variant}" }    # 🔗
  active:         { fg: "{color-sf-primary}", bg: "{color-sf-primary-container}" }  # 🔗
```

## 9. a11y　📋

- 元素帶 `role="toolbar"` + `aria-label="<功能名稱> 工具列"`。
- 批次操作按鈕在無選取項目時設 `disabled`（**禁** `v-if` 讓其消失，改 `disabled` 保留鍵盤序）；或改用 `v-if` 但不影響主 CTA 的 Tab 順序。
- 模式切換按鈕帶 `aria-pressed`（active 狀態）。
- 操作按鈕帶 `aria-label`（圖示按鈕）。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| selectedCount | number | 0 | 已選取行數；0 = 顯示主 CTA，>0 = 顯示批次操作 |
| showViewSwitch | boolean | true | 是否顯示 List/Form 模式切換 |
| currentView | `list` \| `form` | `list` | 當前視圖模式 |

ERP 實作：直接使用 prototyper 的 `.toolbar` HTML 結構（`v-if="selectedRows.length === 0"` 切換）。

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface / §On-* / §Outline / §Space
- 值權威：`prototyper/assets/app.css`（`.toolbar`）
- 子元件：`Button.md`；`IconButton.md`；`ButtonGroup.md`（view-switch）
- 相關：`DataGrid.md`（Toolbar 通常配置於 Grid 上方）；`Tab.md`（Tab block header 含 Toolbar 右側按鈕）
- Code：`@syncfusion/ej2-vue-navigations`（`ToolbarPlugin`，`<ejs-toolbar>`）；ERP 直接 HTML
