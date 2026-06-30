---
name: DsHeader（應用程式頂部列）
category: 佈局與導覽
tier: full           # 三版本 × 三狀態 × 多子元件（DsHeaderHome / breadcrumb / DsUtilityNavigation / venue selector）→ Full
status: ✅ 已產出（ERP 客製複合元件；App Shell 頂部列；三版本 A7/A8_V1/A8_V2 × 三狀態 Breadcrumb/Title/BookTable）
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: JhcpyIEEzSChcEXMRJUiIm/30206:22003
version: v0.1
last-synced: 2026-06-30
---

> 依 `../component-doc-schema.md`（Full 層）產出。視覺字面值（高度 / icon 尺寸 / 顏色實值）權威＝`prototyper/assets/app.css`；本檔＝契約（what/why/token-reference/state/a11y）。

## 1. 概述　📋

ERP App Shell 的**頂部應用列**：全寬固定於頁面頂部，呈現路徑導覽（Breadcrumb）或頁面標題（Title），並提供 Home 快速回首、收藏、通知、設定、用戶 Avatar 的工具列區域。

- **何時用**：所有 ERP 頁面 App Shell 必有，不可缺。
- **何時不用**：不在 Dialog / Drawer / Modal 內出現；手機版可能改用 TopAppBar（另件，ERP 目前未實作）。
- **三版本**：A8_V1（ERP 當前標準，白底）、A8_V2（輕量更新版，右側增加 history btn）、A7（舊款深色，用於特定產品線如 FBM 餐飲 POS）。

## 2. Anatomy　🎨

```
.erp-header（全寬，固定頂部）
  ├─ DsHeaderHome（Home icon btn，72×44px，圓角 8px）
  │
  ├─ [state=Breadcrumb] .erp-breadcrumb
  │   ├─ .breadcrumb-item（"Home"，16px/400）
  │   ├─ chevron-right（18px）
  │   ├─ .breadcrumb-item--collapsed（more-horiz icon，18px）
  │   ├─ chevron-right（18px）
  │   ├─ .breadcrumb-item（中層名，16px/400）
  │   ├─ chevron-right（18px）
  │   └─ .breadcrumb-item--active（當前頁，16px/500）
  │
  ├─ [state=Title] .erp-header__title
  │   ├─ .title-text（PageTitle，16px/500）
  │   └─ .icon-btn（star/favorite，40×40px，icon 20px）
  │
  ├─ [A8_V2 Title / BookTable，optional] .venue-select
  │   ├─ .divider（vertical separator line）
  │   └─ .venue-dropdown（[代碼]:[場館] / [代碼]:[公司] + arrow-drop-down 18px）
  │
  ├─ [A8_V2 Breadcrumb / Title] .icon-btn（history，40×40px，icon 20px）+ divider
  │
  └─ DsUtilityNavigation（h=44px，gap=4px）
      ├─ .util-item（notification：bell icon 24px + badge pill）
      ├─ .util-item（settings：settings icon 24px）
      └─ .util-item（user：Avatar 24px 帶 border ring）

[state=BookTable 專屬結構]
  ├─ DsHeaderHome
  ├─ SegmentControl（訂位/候位，pill 型，bg=white，active tab bg=primary-darken）
  ├─ .icon-btn（star/favorite，40×40px）
  ├─ [optional] .venue-select
  ├─ .note-pill（公告條，yellow，h=30px，w=400px，左側氣泡尾）
  └─ DsUtilityNavigation
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── Container ──
header:
  height（A8_V1 / A8_V2 Breadcrumb / BookTable）: "60px"            # 🎨 量測值
  height（A8_V2 Title）:                           "52px"            # 🎨 量測值
  bg（A8_V1 / A8_V2）: "transparent / white"                         # 📋 見 app.css
  bg（A7）:            "{color-sf-on-primary-container}"              # 🔗 #001D5A
  border-bottom（A7 Title/Breadcrumb）: "{ds-borderwidth-small} {color-sf-spreadsheet-gridline}"  # 🔗 #E5EAF3
  padding-x（A8）: "{ds-space-margin-large}"                          # 🔗 12px
  padding-y（A8）: "{ds-space-margin-medium}"                         # 🔗 8px
  padding-x（A7）: "{ds-space-padding-extralarge}"                    # 🔗 16px
  padding-y（A7）: "{ds-space-padding-medium}"                        # 🔗 8px
  gap（A8）:      "{ds-space-margin-extralarge}"                      # 🔗 16px
  gap（A7）:      "{ds-space-margin-3extralarge}"                     # 🔗 24px

# ── DsHeaderHome ──
home-btn:
  width:       "72px"                                                  # 🎨
  height:      "44px"                                                  # 🎨
  radius:      "{ds-radius-large}"                                     # 🔗 8px
  icon-size:   "32px"                                                  # 🎨（較常規 24px 更大）
  icon-color（A8）: 以 mask 方式上色 "{color-sf-on-surface}"           # 🎨 #0F172A，mask 技法見 app.css
  icon-color（A7）: "{color-sf-on-primary}"                            # 🔗 white

# ── Breadcrumb 項目 ──
breadcrumb-item:
  height:          "32px"                                              # 🎨
  padding-x:       "{ds-space-padding-medium}"                         # 🔗 8px
  radius:          "{ds-radius-small}"                                 # 🔗 4px（hover 時顯示）
  font:            "{font-size-sf-text-lg}"                            # 🔗 16px
  weight-parent（A8_V1）: "{font-weight-sf-normal}"                    # 🔗 400；fg={color-sf-on-surface} #0F172A
  weight-parent（A8_V2）: "{font-weight-sf-normal}"                    # 🔗 400；fg={color-sf-on-surface-variant} #3C4A5B（較淡）
  weight-active:   "{font-weight-sf-medium}"                           # 🔗 500；fg={color-sf-on-surface}
  weight-parent（A7）: "{font-weight-sf-normal}"                       # 🔗 400；fg={color-sf-on-primary} white
  weight-active（A7）: "{font-weight-sf-medium}"                       # 🔗 500；fg=white
  separator-icon:  "chevron-right，18px"                               # 🎨
  collapsed-icon:  "more-horiz，18px"                                  # 🎨（中間層收合）

# ── Title 文字 ──
title-text:
  font:    "{font-size-sf-text-lg}"                                    # 🔗 16px
  weight:  "{font-weight-sf-medium}"                                   # 🔗 500
  color（A8）: "{color-sf-on-surface}"                                 # 🔗 #0F172A
  color（A7）: "{color-sf-on-primary}"                                 # 🔗 white

# ── Star / History icon buttons ──
icon-btn:
  size:       "40×40px"                                                # 🎨
  icon-size:  "20px"                                                   # 🎨（star-outline / history icon）
  radius:     "{ds-radius-small}"                                      # 🔗 4px
  bg:         "transparent"                                            # 🔗

# ── DsUtilityNavigation ──
util-nav:
  height:  "44px"                                                      # 🎨
  gap:     "{ds-space-margin-small}"                                   # 🔗 4px

util-item:
  size:       "44×44px"                                                # 🎨
  icon-size:  "24px"                                                   # 🎨

notification-badge:
  bg:       "{color-sf-tertiary}"                                      # 🔗 #FFBE0B
  fg:       "{color-sf-on-tertiary}"                                   # 🔗 #1D1400
  font:     "{font-size-sf-text-sm} / {font-weight-sf-medium}"         # 🔗 12px / 500
  track:    "0.1px"                                                    # 🎨
  radius:   "{ds-radius-10extralarge}"                                 # 🔗 1000px（full pill）
  padding-x:"{ds-space-padding-small}"                                 # 🔗 4px
  position: "top-[3px] right-[3px]"                                    # 🎨
  text:     "9+"（≥10則 truncate）                                     # 📋

user-avatar:
  size:       "24×24px"                                                # 🎨（Avatar 元件 inner）
  border:     "{ds-borderwidth-small} {color-sf-primary-darken-opacity-5}"  # 🔗 #1F57D1 圓框
  radius:     "{ds-radius-10extralarge}"                               # 🔗 1000px

# ── Venue Select（A8_V2 Title / BookTable 可選）──
venue-select:
  separator:        "vertical 24px，rotate 90°"                        # 🎨 見 app.css divider line
  text-width:       "208px"                                            # 🎨（truncatable）
  font:             "{font-size-sf-text-lg}"                           # 🔗 16px
  weight-venue:     "{font-weight-sf-medium}"                          # 🔗 500（場館 / 公司名稱部分）
  separator-char:   "/"（weight=400，fg=white 在 A7 / fg=on-surface 在 A8） # 📋
  arrow-icon:       "arrow-drop-down，18px"                            # 🎨

# ── SegmentControl（BookTable 功能切換）──
segment-control:
  container-bg:     "{color-sf-surface}"                               # 🔗 white
  container-border: "{ds-borderwidth-small} {color-sf-outline-variant}"# 🔗 #D7DAE0
  container-radius: "48px"                                             # 🎨 pill
  container-padding:"4px"                                              # 🎨
  active-bg:        "{color-sf-primary-darken-opacity-5}"              # 🔗 #1F57D1
  active-fg:        "{color-sf-on-primary}"                            # 🔗 white
  active-shadow:    "0px 1px 3px 1px rgba(0,0,0,0.15)..."             # 🎨 見 app.css
  active-radius:    "40px"                                             # 🎨
  inactive-fg:      "{color-sf-on-primary-container}"                  # 🔗 #001D5A
  tab-font:         "{font-size-sf-text-lg} / {font-weight-sf-medium}" # 🔗 16px / 500
  tab-padding:      "px-[24px] py-[8px]"                              # 🎨

# ── Note Pill（BookTable 公告，可選）──
note-pill:
  height:    "30px"                                                     # 🎨
  width:     "400px"                                                    # 🎨
  bg:        "{color-sf-tertiary}"                                      # 🔗 #FFBE0B
  fg:        "{color-sf-on-tertiary}"                                   # 🔗 #1D1400
  radius:    "29px"                                                     # 🎨（full pill）
  icon-size: "16px"                                                     # 🎨（note-stack icon）
  font:      "{font-size-sf-text-md}"                                   # 🔗 14px
  shadow:    "見 app.css $appbar-bottom-shadow"                         # 🎨
  tail:      "左側 20×19px 氣泡尾（img asset）"                         # 🎨
```

## 4. Variants / Types　🎨🔗

```yaml
# ── Version × State 矩陣 ──
version=A8_V1, state=Breadcrumb:
  bg: white；breadcrumb parent fg=on-surface(400)；active fg=on-surface(500)
  right: DsUtilityNavigation（3 items）

version=A8_V1, state=Title:
  PageTitle（500）+ star btn；height=60px
  right: DsUtilityNavigation

version=A8_V2, state=Breadcrumb:
  breadcrumb parent fg=on-surface-variant（#3C4A5B，較淡）；active fg=on-surface
  right: history btn + divider + DsUtilityNavigation

version=A8_V2, state=Title:
  height=52px；PageTitle + star btn；[optional] divider + venue selector
  right: history btn + divider + DsUtilityNavigation

version=A8_V2, state=BookTable:
  height=60px；Home + SegmentControl + star + [optional] venue selector
  right: Note pill + DsUtilityNavigation

version=A7, state=Title:
  height=60px；deep navy bg；white text；PageTitle + [optional] venue selector
  right: divider + history icon + divider + import-contacts icon（無 notification/settings/user）

version=A7, state=Breadcrumb:
  deep navy bg；white breadcrumb text
  right: [optional] history icon + divider + import-contacts icon

version=A7, state=BookTable:
  two-row layout：
    第一行 toolbar（SegmentControl + [optional] venue + note + import-contacts）
    第二行 horizontal divider line

# ── Props 矩陣 ──
version:         A8_V1 / A8_V2 / A7
state:           Breadcrumb / Title / BookTable
showHistory:     boolean（A7 / A8_V2 右側 history btn）
showVenueSelect: boolean（場館選擇器）
```

## 5. States　🎨🔗

| 狀態 | 呈現 |
|---|---|
| default | 正常顯示 breadcrumb 或 title；utility nav 各 item 靜止 |
| star-inactive | star-outline icon（`material-symbols:star-outline`） |
| star-active | star 實心 icon（`material-symbols:star`） |
| notification-badge | notification icon 右上角顯示 count badge（tertiary yellow） |
| home-hover | home btn hover bg 輕提亮（見 app.css） |
| breadcrumb-item-hover | item bg rounded rect 顯示（見 app.css） |
| user-menu-open | avatar 下拉展開（另件 user menu 控制） |
| venue-dropdown-open | venue selector 展開 DropdownTree panel |

## 6. Behavior　📋

| 互動 | 行為 |
|---|---|
| 點擊 Home btn | 回首頁（view → 'list'；breadcrumb 回歸最上層） |
| 點擊 breadcrumb item | 導向對應層級（由 router 控制） |
| 點擊 star btn | 切換收藏狀態；同步更新 nav-rail 我的最愛清單 |
| 點擊 history btn | 開啟瀏覽歷程 panel（App Shell 層） |
| 點擊 notification | 開啟通知 panel |
| 點擊 settings | 進入系統設定頁 |
| 點擊 user avatar | 展開用戶選單（帳號資訊 / 登出） |
| 點擊 venue selector | 展開 DropdownTree 選擇場館（分店 / 公司） |
| SegmentControl tab 切換 | 切換功能模式（訂位 ↔ 候位），emit @change-function-tab |
| Note pill「詳情」連結 | 導向公告詳情頁或展開 panel |

## 7. RWD / 響應式　📋

- 桌面（≥ 1280px）：完整顯示所有元件。
- 窄螢幕（≤ 1024px）：breadcrumb 中間層收合為 `more-horiz` icon；venue selector 文字截斷；note pill 可縮短。
- `< 768px`：ERP 目前不支援行動版（設計為桌面優先）。

## 8. Keyboard　📋

- Tab 順序（A8_V1）：Home → breadcrumb items（左至右）→ notification → settings → user。
- Tab 順序（A8_V2）：Home → breadcrumb/title → history → notification → settings → user。
- Breadcrumb item：`Enter` 導向；最後一項為唯讀（`aria-current="page"`，不在 Tab 順序）。
- Star / History btn：`Enter` / `Space` 觸發。
- Utility nav：`Enter` / `Space` 觸發各動作；notification 展開後 `Tab` 可達 panel 內容；`Esc` 收合。
- Venue selector：`Enter` 展開 dropdown；`↑↓` 選擇；`Enter` 確認；`Esc` 收合。
- SegmentControl：`Tab` 切換 tabs；`Enter` / `Space` 選取。

## 9. a11y　📋

- Header root：`role="banner"`（HTML `<header>` 元素自帶）。
- Breadcrumb container：`role="navigation"` + `aria-label="頁面路徑"`→ `<ol>` 結構（詳 `Breadcrumb.md`）；active item `aria-current="page"`。
- Home btn：`aria-label="回首頁"`。
- Star btn：`aria-label="加入我的最愛"` / `"從我的最愛移除"`（依 state）。
- History btn：`aria-label="瀏覽歷程"`。
- Notification：`aria-label="通知（[count] 則未讀）"`；badge 內數字螢幕閱讀器可讀（`aria-label` 而非 visual-only）。
- Settings：`aria-label="設定"`。
- User avatar：`aria-label="用戶選單：[姓名縮寫]"`；展開時 `aria-expanded`。
- SegmentControl：`role="tablist"`；tabs `role="tab"` + `aria-selected`。
- Note pill：`role="status"`（一般公告）或 `role="alert"`（緊急通知）。
- 顏色對比：breadcrumb on-surface (#0F172A) on white ≥ 15:1 ✅；on-surface-variant (#3C4A5B) on white ≈ 7:1 ✅；notification badge fg (#1D1400) on #FFBE0B ≈ 10:1 ✅。

## 10. 跨平台 Adaptive　📋

- **App 版**：Header 改由 TopAppBar 處理（另件）；venue selector 改 sheet 或 dialog；breadcrumb 可能改 back btn 單步導覽。
- **深色模式**：A7 版本即為深色（on-primary-container bg）；A8 深色模式 token 待 DS 定義後回填。

## 11. Content　📋

- Breadcrumb 層：模組分類 > 功能名稱 > 單號（第三層也可為「新增」或作業號碼）；最多三層，中間層超出時以 `more-horiz` 收合。
- Title：功能頁名稱（不含路徑歷程）。
- Venue selector 格式：`[代碼]:[場館名] / [公司代碼]:[公司名]`；超長截斷。
- Notification count：≥ 10 顯示 `9+`（非精確數字；避免 badge 過寬）。
- Note pill：公告文字 ≤ 30 字；右側固定「詳情」underline link；文字截斷用 `text-ellipsis`。
- User initials：姓名縮寫 2 字元，如 "AN"；背景取 primary-darken。

## 12. API / Props　📋

> production 為 ERP 客製複合元件（prototype 對應 `.erp-header`）；本表為設計指引層。

| Prop | Type | Default | 說明 |
|---|---|---|---|
| version | 'A8_V1' \| 'A8_V2' \| 'A7' | 'A8_V1' | 版本型 |
| state | 'Breadcrumb' \| 'Title' \| 'BookTable' | 'Breadcrumb' | 顯示模式 |
| breadcrumb | BreadcrumbItem[] | [] | 路徑陣列（state=Breadcrumb 用） |
| title | string | — | 頁面標題（state=Title 用） |
| isFavorite | boolean | false | 是否已收藏當前頁 |
| showHistory | boolean | true | 是否顯示歷程按鈕（A7 / A8_V2 生效） |
| showVenueSelect | boolean | false | 是否顯示場館選擇器 |
| venue | VenueOption | — | 當前場館資料（showVenueSelect=true 時） |
| notificationCount | number | 0 | 未讀通知數 |
| user | { initials: string } | — | 用戶資訊（顯示 avatar 縮寫） |
| note | string | — | 公告文字（state=BookTable 用；空字串則不顯示 pill） |
| functionTab | 'book' \| 'waitlist' | 'book' | SegmentControl 當前 tab（state=BookTable 用） |

事件：`@go-home` / `@toggle-favorite` / `@navigate-breadcrumb(index)` / `@open-notification` / `@open-settings` / `@open-user-menu` / `@change-venue(venue)` / `@change-function-tab(tab)`

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §Tertiary / §On-* / §Surface / §Outline / §Radius / §Space / §Typography
- 值權威：`prototyper/assets/app.css`（canonical CSS）
- 同層：`DsSideNavMenu.md`（App Shell nav rail，與本元件同屬 App Shell chrome）；`Breadcrumb.md`（breadcrumb atom，嵌入 state=Breadcrumb 中）；`Avatar.md`（user util-item 內）；`Badge.md`（notification count badge）
- Code：ERP 客製複合元件（prototype 對應 `.erp-header` 區塊）
