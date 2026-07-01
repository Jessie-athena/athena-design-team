---
name: DsNavigationA8（側邊導覽選單 / Nav Rail）
category: 佈局與導覽
tier: full           # 兩段結構（close/open）× 三層選單 × hover/active/selected 多層 states → Full
status: ✅ 已產出（ERP 客製複合元件；Code 元件名 DsSideNavMenu；close=72px rail；open=72+260px rail+sidebar）
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: JhcpyIEEzSChcEXMRJUiIm/30206:22044
version: v0.1
last-synced: 2026-06-30
---

> 依 `../component-doc-schema.md`（Full 層）產出。視覺字面值（寬度 / padding / 顏色實值）權威＝`prototyper/assets/app.css`；本檔＝契約（what/why/token-reference/state/a11y）。

## 1. 概述　📋

ERP App Shell 的**側邊導覽選單（Nav Rail）**：全高固定於左側，提供模組層（L1：我的最愛 / 財務 / 進銷存 / 人事 / 設定檔）快速切換；點擊 L1 展開同側 Sidebar Panel 呈現 L2/L3 功能目錄。

- **何時用**：每頁 App Shell 必有，不可缺。
- **何時不用**：不在任何 Dialog / Drawer 內出現；手機版（< 768px）改用底部導覽列（Bottom Navigation，另件，ERP 目前未實作）。
- **兩段結構**：`close`（72px rail only）↔ `open`（72px rail + 260px sidebar panel 並排）。

## 2. Anatomy　🎨

```
.nav-rail（72px，深藍色 rail）
  ├─ .nav-rail__top（border-bottom 分隔線下方）
  │   └─ .nav-item（我的最愛，56×56，icon + label）
  ├─ .nav-rail__body（flex-1，overflow hidden）
  │   ├─ .nav-item[default]（財務 / 進銷存 / 人事 / 設定檔）
  │   └─ .nav-item[active]（當前選中 L1，帶 active bg）
  └─ .nav-rail__system（ERP 品牌 logo，56×56，底部固定）

展開時（state=open），rail 右側緊接 sidebar panel：
.nav-sidebar（260px，white/primary-5% 漸層 bg）
  ├─ .nav-sidebar__title（模組名稱 + 收合按鈕）
  └─ .nav-sidebar__list（可捲動，overflow hidden）
      ├─ .nav-sidebar__item--l2（第二層，帶 chevron-down 展開）
      │   └─ .nav-sidebar__item--l3（第三層，縮排 24px）
      │       └─ [selected] .nav-sidebar__item--l3[active]（primary-container bg）
      └─ .nav-sidebar__item--l2[collapsed]（chevron-right，子項收合）
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── Nav Rail ──
rail:
  width:        "72px"                                               # 🎨 量測值
  height:       "100%（全高）"                                        # 📋
  bg:           "{color-sf-on-primary-container}"                    # 🔗 #001D5A 深藍
  radius:       "{ds-radius-extralarge}"                             # 🔗 12px（全四角）
  padding:      "{ds-space-margin-medium}"                           # 🔗 8px all sides
  gap-items:    "{ds-space-margin-medium}"                           # 🔗 8px（nav-item 間距）

nav-item（Default）:
  size:         "56×56px"                                            # 🎨 量測值
  radius:       "{ds-radius-large}"                                  # 🔗 8px
  padding-x:    "{ds-space-padding-extrasmall}"                      # 🔗 2px
  padding-y:    "{ds-space-padding-small}"                           # 🔗 4px
  icon-size:    "24px"                                               # 🎨
  icon-color:   "{color-sf-surface}"                                 # 🔗 white
  label-font:   "{font-size-sf-text-sm} / {font-weight-sf-normal}"   # 🔗 12px / 400
  label-color:  "{color-sf-surface}"                                 # 🔗 white
  label-track:  "0.1px"                                              # 🎨

nav-item（Active）:
  bg:           "{color-sf-primary-darken-opacity-5}"                # 🔗 #1F57D1
  # icon / label 同 Default

rail-top-separator:
  border:       "{ds-borderwidth-small} solid {color-sf-outline}"    # 🔗 1px solid #7F8996

# ── Nav Sidebar（展開面板）──
sidebar:
  width:        "260px"                                              # 🎨 量測值
  bg:           "linear-gradient（rgba(primary,0.08) over white）"    # 🎨 見 app.css（無單一 token）
  border-left:  "{ds-borderwidth-small} {color-sf-outline-variant}"  # 🔗 1px #D7DAE0
  radius-right: "{ds-radius-extralarge}"                             # 🔗 12px（br + tr 兩角）
  padding:      "{ds-space-padding-medium}"                          # 🔗 8px all sides

sidebar-title:
  font:         "{font-size-sf-h4}"                                  # 🔗 22px（large text）
  weight:       "{font-weight-sf-normal}"                            # 🔗 400
  color:        "{color-sf-primary}"                                 # 🔗 #2877EE
  padding-x:    "{ds-space-margin-large}"                            # 🔗 12px
  padding-y:    "{ds-space-margin-medium}"                           # 🔗 8px

collapse-btn:
  size:         "36×36px"                                            # 🎨
  border:       "{ds-borderwidth-small} {color-sf-outline}"          # 🔗 1px #7F8996
  radius:       "{ds-radius-small}"                                  # 🔗 4px
  icon:         "keyboard-tab-rtl，16px"                             # 🎨

sidebar-item-l2:
  min-height:   "40px"（最高 56px）                                   # 🎨
  padding-y:    "{ds-space-margin-small}"                            # 🔗 4px
  padding-x:    "{ds-space-padding-medium}"                          # 🔗 8px
  font:         "{font-size-sf-text-md}"                             # 🔗 14px
  weight:       "{font-weight-sf-normal}"                            # 🔗 400
  color:        "{color-sf-on-surface}"                              # 🔗 #0F172A
  chevron:      "14px（展開=chevron-down；收合=chevron-right）"        # 🎨

sidebar-item-l3:
  indent-left:  "{ds-space-padding-3extralarge}"                     # 🔗 24px（pl-24px）
  min-height:   "40px"                                               # 🎨
  padding-y:    "{ds-space-margin-small}"                            # 🔗 4px
  font / color: 同 l2

sidebar-item-l3（selected）:
  bg:           "{color-sf-primary-container-opacity65}"             # 🔗 #D5E4FF
  radius:       "{ds-radius-large}"                                  # 🔗 8px
  # weight / color 不加粗，同 default l3
```

## 4. Variants　🎨🔗

```yaml
# ── Figma Variants（FAI2 node 30206:22044） ──
Type=menu, State=close, System=ERP:     width=72px；rail only
Type=menu, State=open, System=ERP:      width=72px+260px；sidebar 顯示 L2/L3
Type=menu, State=hover, System=ERP:     sidebar 某 L2/L3 item hover 高亮（詳 app.css）
Type=collect, State=open, System=ERP:   L2 項展示收合狀態（chevron-right）
Type=collect, State=hover, System=ERP:  sidebar hover on collected（收合）L2 item

# ── 維度矩陣 ──
Type:    menu（L2 展開）/ collect（L2 收合）
State:   close / open / hover
System:  ERP（深藍）/ FBM（另一產品線，結構相同；本檔僅記錄 ERP）
```

## 5. States　🎨🔗

| 狀態 | Rail 呈現 | Sidebar 呈現 |
|---|---|---|
| close（預設） | 僅 rail；L1 items default/active | 無 |
| open | rail + sidebar 並排；active L1 加亮 | 顯示 active L1 的 L2/L3；active L3 selected bg |
| rail item hover | bg 輕提亮（見 app.css） | 無變化 |
| sidebar L2 hover | 無變化 | L2 row highlight（見 app.css） |
| sidebar L3 hover | 無變化 | L3 row highlight |
| sidebar L3 selected | 無變化 | `#D5E4FF` bg + rounded 8px |

## 6. Behavior　📋

| 互動 | 行為 |
|---|---|
| 點擊 L1（close 態） | sidebar 展開（state→open）；切換 active L1；sidebar 呈現該 L1 的 L2/L3 |
| 點擊 L1（open 態，同模組） | sidebar 不收合；若點不同 L1 則換 sidebar 內容 |
| 點擊 collapse btn | sidebar 收合（state→close） |
| 點擊 L2 item | 展開/收合該 L2 的 L3 子項（chevron toggle） |
| 點擊 L3 item | 導向對應功能頁；L3 設為 selected；L1/L2 維持 active |
| 我的最愛（L1） | 顯示收藏的 L3 清單；空狀態顯示提示文字 |
| CTA 限制 | Nav Rail 僅作導覽用途，**禁**混置操作型 CTA 按鈕（如「新增」）於 rail 或 sidebar 中 |

## 7. RWD / 斷點　📋

| 斷點 | 呈現 |
|---|---|
| ≥ 1280px | 完整 rail（close/open）；open 時 sidebar 與 main content 並排或覆蓋 |
| ≤ 1024px | rail 常駐 close；sidebar 改 overlay 模式（focus trap；詳 `Sidebar.md`） |
| < 768px | rail + sidebar 隱藏；改 App Bottom Navigation（ERP 目前未實作） |

## 8. Keyboard　📋

- L1 nav items：`Tab` 可達；`Enter` / `Space` 展開 sidebar / 切換模組。
- Sidebar L2 展開/收合：`Enter` / `Space` 切換 chevron。
- Sidebar 內 `↑↓` 移動項目；`Enter` 導向頁面；`Esc` 收合 sidebar。
- Collapse btn：`Tab` 可達；`Enter` / `Space` 收合。

## 9. a11y　📋

- Rail root：`role="navigation"` + `aria-label="主導覽"`。
- L1 items：`role="menuitem"`；active 項 `aria-current="true"`。
- Sidebar panel：`role="region"` + `aria-label="[模組名] 選單"`。
- L2 展開項：`aria-expanded="true/false"`；L3 list `role="menu"`。
- 當前 L3（selected）：`aria-current="page"`。
- Collapse btn：`aria-label="收合選單"` / `"展開選單"`（依 state 動態切換）。
- 色彩對比：白字 (#FFF) on 深藍 (#001D5A) ratio > 8:1 ✅；sidebar L3 selected (#0F172A) on #D5E4FF ≈ 7:1 ✅；sidebar title (#2877EE) on white ≈ 3.5:1（22px large text threshold ≥ 3:1 ✅）。

## 10. 跨平台 Adaptive　📋

- **桌面**：full rail + sidebar；sidebar 推擠或覆蓋 main content（依產品版面決策）。
- **平板（M）**：rail close 常駐；sidebar 改 Over overlay；focus trap 啟動（詳 `Sidebar.md §Over 模式`）。
- **手機（S）**：nav rail 隱藏；Bottom Navigation 替代（ERP 目前僅桌面版）。

## 11. Content　📋

- L1 label：固定模組名（我的最愛 / 財務 / 進銷存 / 人事 / 設定檔）；ERP 5 項固定順序，不可重排。
- L2/L3 labels：各模組 route config 決定；建議 ≤ 10 中文字（超長用 `text-ellipsis`）。
- 我的最愛空狀態：L2 顯示「尚無收藏項目」（app.js 控制）。
- Collapse btn：無 visible label（靠 icon 語意 + aria-label 補充）。

## 12. API / Props　📋

> production 為 ERP 客製複合元件（`DsSideNavMenu.vue`）；本表為設計指引層。

| Prop | Type | Default | 說明 |
|---|---|---|---|
| open | boolean | false | sidebar 是否展開 |
| activeL1 | string | — | 當前 L1 key（'fav' / 'finance' / 'psi' / 'hr' / 'config'） |
| activeL3 | string | — | 當前 L3 路由 key；決定 selected 項與 active L2 展開狀態 |
| navItems | NavItem[] | — | L1→L2→L3 路由樹（含 icon、label、key、children） |

事件：`@navigate(l3Key)` / `@toggle(open)`

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §On-* / §Surface / §Outline / §Radius / §Space / §Typography
- 值權威：`prototyper/assets/app.css`（canonical CSS）
- 同層：`DsHeader.md`（App Shell header，與本元件同屬 App Shell chrome）；`Sidebar.md`（Syncfusion 通用 Sidebar Drawer，結構不同）；`Breadcrumb.md`（header 麵包屑對應 L3 active）
- Code：ERP 客製元件 `components/ds/DsSideNavMenu.vue`
