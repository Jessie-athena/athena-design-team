---
name: Ribbon（功能區工具列）
category: 佈局與導覽
tier: full
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: —       # 🎨 Figma 補入時填 <FILE_KEY>/<NODE_ID>
version: v0.1
last-synced: —
---

> 依 `../component-doc-schema.md`（Full 層）產出。Ribbon 目前僅於 `Spreadsheet.md` 中使用；一般 ERP 頁面不使用 Ribbon（改用 Toolbar）。

## 1. 概述　📋

**Office 風格分頁功能區**：以「Tab 分頁 → 群組 → 操作項目」三層組織大量工具操作，常見於文字編輯器、試算表。ERP 中僅 `Spreadsheet` 元件內建 Ribbon。

何時不用 Ribbon：一般 List / Form 頁面的操作列 → `Toolbar.md`（更輕量）；行內編輯快捷操作 → `ContextMenu.md`。

## 2. Anatomy　🎨

```
.e-ribbon
  ├─ .e-ribbon-tabs                ← Tab 分頁列（切換不同功能群）
  │   └─ .e-ribbon-tab             ← 單一 Tab（如：首頁 / 插入 / 版面配置）
  └─ .e-ribbon-container           ← 當前 Tab 的內容
      └─ .e-ribbon-groups          ← 群組容器（水平排列）
          └─ .e-ribbon-group       ← 單一群組（帶群組標籤）
              ├─ .e-ribbon-group-label  ← 群組名稱（底部小字）
              └─ .e-ribbon-items   ← 操作項目
                  └─ .e-ribbon-item     ← 單一項目（Button / DropDownButton / SplitButton / 色彩選擇等）
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
ribbon-bar:
  bg:             "{color-sf-surface}"                       # 🔗
  border-b:       "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗

tab:
  height:         "36px"                                     # 🎨 Tab 列高
  font:           "{font-size-sf-text-md} / {font-weight-sf-medium}"  # 🔗 14px / 500
  fg:             "{color-sf-on-surface-variant}"            # 🔗
  active-fg:      "{color-sf-primary}"                       # 🔗
  active-border-b: "2px {color-sf-primary}"                  # 🎨 active 下底線

container:
  height:         "72px"                                     # 🎨 Normal 模式群組高度
  height-simplified: "40px"                                  # 🎨 Simplified 模式高度
  padding-v:      "{ds-space-padding-medium}"                # 🔗 8px
  padding-h:      "{ds-space-padding-extra-large}"           # 🔗 16px

group:
  padding-h:      "{ds-space-padding-extra-large}"           # 🔗 16px（群組間隔）
  separator:      "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗 群組右側分隔線

group-label:
  font:           "{font-size-sf-text-sm}"                   # 🔗 12px
  fg:             "{color-sf-on-surface-variant}"            # 🔗
  padding-t:      "{ds-space-padding-small}"                 # 🔗 4px

ribbon-item（large button）:
  height:         "60px"                                     # 🎨（Normal 模式，icon + label 垂直）
  icon-size:      "24px"                                     # 🎨

ribbon-item（small button）:
  height:         "24px"                                     # 🎨（small icon + label 水平）
  icon-size:      "16px"                                     # 🎨
```

## 4. Variants　🎨🔗

```yaml
Normal:
  desc: "完整 Ribbon（Tab + 群組 + 大 / 小 button）；寬度 ≥ 1280px"
  container-height: "72px"

Simplified:
  desc: "單列簡化模式；群組標籤隱藏；item 縮為 small；寬度 960–1279px"
  container-height: "40px"

Overflow:
  desc: "寬度不足時，末尾群組 / 項目移入 overflow popup"
  trigger: "viewport < 960px 或容器寬度不足"
```

## 5. States　🎨🔗

```yaml
tab:
  default:        { fg: "{color-sf-on-surface-variant}", border: "none" }  # 🔗
  hover:          { fg: "{color-sf-on-surface}", bg: "見 app.css" }  # 🎨
  active:         { fg: "{color-sf-primary}", border-b: "2px {color-sf-primary}" }  # 🔗

ribbon-item（button）:
  default:        { bg: "transparent", fg: "{color-sf-on-surface}" }  # 🔗
  hover:          { bg: "見 app.css" }                       # 🎨
  active/pressed: { bg: "{color-sf-primary-container}" }     # 🔗
  disabled:       { fg: "{color-sf-on-surface-opacity38}", cursor: "not-allowed" }  # 🔗
  toggled-on:     { bg: "{color-sf-primary-container}", fg: "{color-sf-primary}" }  # 🔗（如 Bold 按鈕）
```

## 6. Behavior 行為　📋

| 動作 | 觸發 | 結果 | 條件 |
|------|------|------|------|
| 切換 Tab | 點擊 Tab | Ribbon 容器更新為對應群組；Tab 高亮 | — |
| 折疊 Ribbon | 雙擊 Tab | 容器高度縮至 0（只剩 Tab 列） | enableCollapse=true |
| 展開 Ribbon | 點擊折疊中的 Tab | 容器展開 | — |
| Overflow 展開 | 點擊末尾 `»` 按鈕 | 溢出群組 / 項目在 popup 中展示 | 寬度不足 |
| 快捷鍵觸發 | `Alt+F10` | 焦點移入 Ribbon Tab 列 | — |
| Tab KeyTip | `Alt` | 每個 Tab 顯示 KeyTip（按鍵提示徽章） | enableKeyTip=true |

## 7. RWD 響應式　📋

| 斷點 | 模式 | 行為 |
|------|------|------|
| ≥ 1280px | Normal | 完整 Ribbon；大 / 小 button 並存 |
| 960–1279px | Simplified | 單列模式；群組標籤隱藏 |
| < 960px | Overflow | 部分群組收入 overflow popup |

## 8. Keyboard 鍵盤　📋

| 按鍵 | 行為 |
|------|------|
| `Alt+F10` | 焦點移入 Tab 列第一個 Tab |
| `←` `→` | 在 Tab 間移動 |
| `↓` | 從 Tab 進入容器內的第一個項目 |
| `Tab` / `Shift+Tab` | 在 Ribbon 容器內的項目間移動 |
| `Enter` / `Space` | 觸發按鈕 / 開啟下拉 |
| `Esc` | 離開 Ribbon，焦點返回主內容 |
| `Alt` | 顯示 KeyTip（Office 式快捷提示）|

## 9. a11y　📋

- Ribbon 根帶 `role="toolbar"` + `aria-label="功能區"`。
- Tab 列帶 `role="tablist"`；每個 Tab 帶 `role="tab"` + `aria-selected` + `aria-controls`。
- 群組帶 `role="group"` + `aria-label="<群組標籤>"`。
- 按鈕項目：圖示按鈕帶 `aria-label`；toggle 按鈕帶 `aria-pressed`；disabled 帶 `aria-disabled="true"`。

## 10. 跨平台 Adaptive　📋

- Web（桌面）：Normal 或 Simplified 模式，視容器寬度自動切換。
- Web（平板 < 960px）：Overflow 模式；操作移至溢出選單。
- App：Ribbon **不使用**於 App；Spreadsheet 在 App 以 Bottom Sheet 工具列替代。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| tabs | RibbonTabModel[] | [] | `{ header, groups: [{ header, items }] }` |
| activeIndex | number | 0 | 預設 active Tab 索引 |
| layout | `Normal` \| `Simplified` | `Normal` | 顯示模式 |
| enableCollapse | boolean | true | 雙擊 Tab 折疊容器 |
| enableKeyTip | boolean | false | 顯示 KeyTip 快捷提示 |

事件：`@tabSelected(e)` / `@ribbonCollapsed` / `@ribbonExpanded` / `@overflowPopupOpen`

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Surface / §On-* / §Primary / §Outline / §Space / §Typography
- 值權威：`prototyper/assets/app.css`
- 僅用於：`Spreadsheet.md`（Ribbon 是 Spreadsheet 的內建 UI）
- 相關：`Toolbar.md`（一般頁面的輕量工具列替代）；`Tab.md`（一般頁面 Tab 切換）；`DropDownButton.md`（Ribbon item 常用）
- Code：`@syncfusion/ej2-vue-ribbon`（`RibbonPlugin`，`<ejs-ribbon>`）
