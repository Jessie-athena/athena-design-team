---
name: DsDialogHeader（Dialog 標題列）
category: 回饋與狀態
tier: lite           # 靜態 header strip；3 type × 3 usage 組合；無 RWD / 狀態機生命週期
status: ✅ 已產出（ERP 客製複合元件；Dialog 頂部列；三配置 A7 title / A7_CRS 含來源編號 / A8 title+縮放）
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: JhcpyIEEzSChcEXMRJUiIm/26600:467352
version: v0.1
last-synced: 2026-06-30
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺字面值（高度 / padding / 顏色實值）權威＝`prototyper/assets/app.css`；本檔＝契約（what/why/token-reference/variant/a11y）。

## 1. 概述　📋

ERP Dialog 的**標題列**：固定於 Dialog 頂部，呈現頁面標題、可選來源單號、可選快捷操作（縮放 / 歷程 / 人員管理），並提供關閉按鈕。

- **何時用**：所有 ERP Dialog / Modal 的頂部標題區。
- **何時不用**：不在 Dialog 外使用；Drawer / Sheet 側邊標題另件。
- **三種配置**：
  - `type=title, usage=A7`：基本型，標題 + 關閉（× 16px icon）。
  - `type=含來源編號, usage=A7_CRS`：來源型，標題＋[單號] + 來源參照 + 右側審核資訊 + 人員操作 + 關閉。
  - `type=title+縮放, usage=A8`：A8 型，breadcrumb 樣式標題 + 縮放 / 歷程 icon btn + 關閉。

## 2. Anatomy　🎨

```
.dialog-header（全寬，h=46px，border-bottom，flex space-between）
  ├─ [A7] .header-title（PageTitle 16px/500）
  │   └─ .close-icon（16×16px × icon）
  │
  ├─ [A7_CRS] .header-left
  │   ├─ .title-group（PageTitle 500 + [單號] 400，gap=4px）
  │   ├─ .vertical-divider（h=22px，border-right）   ← show=true 才顯示
  │   └─ .ref-text（來源參照，16px/400/placeholder）  ← show=true 才顯示
  │   └─ .header-right
  │       ├─ .auditor-text（新增者資訊，14px/400/on-surface-variant）  ← show1=true
  │       ├─ .icon-btn（person-add，40×40，icon 20px）                  ← show1=true
  │       ├─ .icon-btn（person-edit，40×40，icon 20px）                 ← show1=true
  │       └─ .icon-btn（close，40×40，icon 20px）
  │
  └─ [A8] .header-title（breadcrumb 樣式 PageTitle）
      └─ .btn-group
          ├─ .icon-btn（pan-zoom，40×40，icon 20px）  ← show2=true
          ├─ .icon-btn（history，40×40，icon 20px）   ← show1=true
          ├─ .vertical-divider（h=24px）
          └─ .icon-btn（close，40×40，icon 20px）
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── Container（三配置共用）──
container:
  height:       "46px"                                               # 🎨 量測值
  width:        "100%（Dialog 全寬）"                                 # 📋
  bg-overlay:   "{color-sf-primary-opacity-8}"                       # 🔗 rgba(#2877EE, 0.08) 疊於 white
  border-bottom: "{ds-borderwidth-small} {color-sf-spreadsheet-gridline}"  # 🔗 1px #E5EAF3

container（A7 / A7_CRS）:
  padding-x:    "{ds-space-padding-3extralarge}"                     # 🔗 24px（A7 左右相同）
  padding-left（A7_CRS）: "{ds-space-padding-3extralarge}"           # 🔗 24px
  padding-right（A7_CRS）: "{ds-space-padding-medium}"               # 🔗 8px
  radius:       "top-left 8px + top-right 8px"                      # 🎨 對應 Dialog 圓角頂部

container（A8）:
  padding-left: "{ds-space-padding-3extralarge}"                     # 🔗 24px
  padding-right:"{ds-space-padding-medium}"                          # 🔗 8px
  radius:       "none（由 Dialog wrapper 控制）"                      # 📋

# ── Title 文字 ──
title:
  font:         "{font-size-sf-text-lg}"                             # 🔗 16px
  weight-title: "{font-weight-sf-medium}"                            # 🔗 500
  weight-docno: "{font-weight-sf-normal}"                            # 🔗 400（[單號] 部分）
  color:        "{color-sf-on-surface}"                              # 🔗 #0F172A
  gap（title + docno）: "{ds-space-margin-small}"                    # 🔗 4px

# ── 來源參照文字（A7_CRS）──
ref-text:
  font:         "{font-size-sf-text-lg}"                             # 🔗 16px
  weight:       "{font-weight-sf-normal}"                            # 🔗 400
  color:        "{ds-color-placeholder}"                             # 🔗 #67717E（placeholder 灰）

# ── 審核資訊（A7_CRS）──
auditor-text:
  font:         "{font-size-sf-text-md}"                             # 🔗 14px
  weight:       "{font-weight-sf-normal}"                            # 🔗 400
  color:        "{color-sf-on-surface-variant}"                      # 🔗 #3C4A5B

# ── Vertical Divider ──
divider-a7crs:  "h=22px，border-right {color-sf-outline-variant}"   # 🎨
divider-a8:     "h=24px，border-right {color-sf-outline}"           # 🎨

# ── Icon Buttons（A7_CRS / A8 右側）──
icon-btn:
  size:         "40×40px"                                            # 🎨
  bg:           "transparent"                                        # 🔗
  radius:       "{ds-radius-small}"                                  # 🔗 4px
  icon-size:    "20px"                                               # 🎨

# ── Close（A7 simple 型）──
close-icon-simple:
  size:         "16×16px"                                            # 🎨（非 icon-btn，純 icon）
  color:        "{color-sf-on-surface}"                              # 🔗
```

## 4. Variants　🎨🔗

```yaml
# ── Figma Variants（FAI2 node 26600:467352） ──
type=title, usage=A7:
  padding: px-24px；radius: tl/tr-8px
  left:  PageTitle（16px/500）
  right: close × icon（16px，純 icon 非 btn）
  show flags: 無（此型無 show prop 意義）

type=含來源編號, usage=A7_CRS:
  padding: pl-24px pr-8px；radius: tl/tr-8px
  left:  PageTitle + [單號] + divider + ref（show=true 顯示 divider+ref）
  right: auditor text + person-add btn + person-edit btn + close btn（show1=true 顯示 auditor+person btns）
         show3=true 顯示 [單號]

type=title+縮放, usage=A8:
  padding: pl-24px pr-8px；無 radius（由外層 Dialog 控制）
  left:  breadcrumb-style PageTitle（16px/500）
  right: [pan-zoom btn（show2=true）] + [history btn（show1=true）] + divider + close btn

# ── Show / Hide flags 對照 ──
show:  A7_CRS：顯示 divider + ref-text（來源參照區）
show1: A7_CRS：顯示 auditor info + person-add / person-edit btn
       A8：顯示 history btn
show2: A8：顯示 pan-zoom btn
show3: A7_CRS：顯示 [單號]（單號緊接 PageTitle 後）
```

## 5. States　🎨🔗

| 狀態 | 呈現 |
|---|---|
| default | 靜止（header 無 hover 效果；icon btn hover 見 app.css） |
| icon-btn-hover | icon-btn bg 輕提亮（見 app.css；primary-opacity-12 或 neutral overlay） |
| icon-btn-focus | focus ring（{ds-borderwidth-medium} {color-sf-primary}） |

## 9. a11y　📋

- Header root：`role="dialog"` 由父 Dialog 設定；本元件為 `<header>` 或無 landmark role。
- Close btn：`aria-label="關閉"` + `type="button"`。
- Pan-zoom btn：`aria-label="縮放至全頁"` + `type="button"`。
- History btn：`aria-label="瀏覽歷程"` + `type="button"`。
- Person-add btn：`aria-label="新增人員"` + `type="button"`。
- Person-edit btn：`aria-label="編輯人員"` + `type="button"`。
- PageTitle：用 `id` 連結父 Dialog 的 `aria-labelledby`（如 `aria-labelledby="dialog-title"`）。
- 色彩對比：title on-surface (#0F172A) on primary-8% (#EFF4FD) ≈ 14:1 ✅；auditor on-surface-variant (#3C4A5B) on same ≈ 7:1 ✅；ref placeholder (#67717E) on same ≈ 4.5:1 ✅（body text 16px）。

## 11. Content　📋

- PageTitle：功能頁名稱，≤ 20 字（超長截斷 `text-ellipsis`）。
- [單號]：系統自動填入，格式如 `SO_2024_001234`；空值時 show3=false 隱藏。
- 來源參照：格式為 `[單據類型] [單號]`，如 `預約單 CRS20240830001`；空值時 show=false 隱藏整個 divider+ref 區塊。
- 審核資訊：`新增者: [username] YYYY/MM/DD HH:MM`；空值時 show1=false。
- Close 按鈕：無 visible label（icon 語意清楚；aria-label="關閉"）。

## 12. API / Props　📋

> production 為 ERP 客製元件；本表為設計指引層。

| Prop | Type | Default | 說明 |
|---|---|---|---|
| type | 'title' \| '含來源編號' \| 'title+縮放' | 'title' | 配置型 |
| usage | 'A7' \| 'A7_CRS' \| 'A8' | 'A7' | 使用場景（A7=標準 ERP / A7_CRS=CRS 來源型 / A8=A8 縮放型） |
| title | string | 'Page Title' | Dialog 標題文字（連結至 aria-labelledby） |
| docNo | string | — | 來源單號（type=含來源編號；空字串時隱藏） |
| refText | string | — | 來源參照文字（如「預約單 CRS20240830001」；空值時隱藏） |
| auditorInfo | string | — | 審核資訊（如「新增者: jessie 2024/08/30 10:00」） |
| show | boolean | true | A7_CRS：顯示 divider + ref-text |
| show1 | boolean | true | A7_CRS：顯示 auditor info + person btns；A8：顯示 history btn |
| show2 | boolean | true | A8：顯示 pan-zoom btn |
| show3 | boolean | true | A7_CRS：顯示 [單號] |

事件：`@close` / `@zoom`（A8 pan-zoom）/ `@history`（A8）/ `@add-person`（A7_CRS）/ `@edit-person`（A7_CRS）

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §On-* / §Surface / §Outline / §Space / §Typography
- 值權威：`prototyper/assets/app.css`（canonical CSS）
- 同層：`Dialog.md`（父元件；本元件為 Dialog 的 header slot）；`DsHeader.md`（App Shell 頂部列，兩者結構相似但用途不同）；`IconButton.md`（icon-btn 用法）
- Code：ERP 客製元件（Dialog header slot）
