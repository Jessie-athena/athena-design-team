# Shared Profile — 頁面框架

> 跨專案共用的頁面骨架規則。所有專案 profile（如 `erp-transaction.md`）在本檔規則之上**附加**或**覆寫**特定行為。
> 本檔為通用基底，**不**討論單一專案的 state machine、業務邏輯或元件命名（那些屬於專案 profile）。

## 觸發載入

本檔為所有專案 profile 的**前置必讀**——使用 prototyper skill 時，先載入 `Shared.md`，再依專案載入對應 `<project>.md`。

---

## 頁面框架 (Page Framework)

整個應用視窗為 `100vh` grid，分兩列：

| 列 | 高度 | 內容 |
|---|---|---|
| Header | 56px | home + breadcrumb + 右側 actions |
| Body | 1fr | grid-template-columns: 72px 1fr → 左 nav-rail / 右 main panel |

Main panel 內部從上到下兩段：

| 段 | 用途 |
|---|---|
| 主要內容（List View 或 Form View） | 可變高度 |
| Info Bar | 28px，sticky 在 main panel 底部；左 programID / 右版號 |

### 整體 Layout 規格
```css
#app {
  height: 100vh;
  overflow: hidden;
  display: grid;
  grid-template-rows: 56px 1fr;
}
.erp-body {                /* 或語意中性命名 .app-body */
  display: grid;
  grid-template-columns: 72px 1fr;
  padding: 0 12px 12px 12px;
  gap: 8px;
  min-height: 0;
  overflow: hidden;
}
```

> Body grid 的左欄 72px = nav-rail；專案 profile **不可**改變此寬度（影響 header home-button 與 nav-rail 視覺對齊）。

---

## Header（頂部欄）

整個 Header 為 **4 層 auto-layout**（DsHeader 對齊 Figma）。不僅是「左 / 中 / 右」三段——`Left` 內含 ItemHome + Breadcrumb wrapper + Title + 右側 Utility Navigation 四個子節點，順序與尺寸**鎖定**，AI 用直覺擺會偏左 / 換行 / 顯不出 Title。

### 圖層樹

```
Header (1920×56, padding 8, gap 16)
├─ Left (flex-grow: 1, gap 8)
│  ├─ DsNavigation/ItemHome (72×44)         ← home button
│  ├─ star = Breadcrumb 容器 (152×44)        ← 含 PageTitle + favorite icon
│  │  ├─ Title (112×44, padding 0 8)
│  │  │  └─ PageTitle (Label/16pt/Medium)
│  │  └─ Icon Button (favorite, 40×40, radius 4)
│  └─ ……（breadcrumb 各層階段，由專案 profile 定義）
└─ Utility Navigation (140×40, gap 4)
   ├─ Speed Dial (44×44, 通知 + Badge)
   ├─ <sideMenu> (44×44, settings)
   └─ Avatar (44×44 wrapper)
```

### Token 速查

| 區段 | 尺寸 | padding / gap | 背景 / 色 |
|---|---|---|---|
| 外層 `Header` | 1920 × 56 | padding 8 / gap 16 | transparent |
| `Left` 容器 | 1748 × 44 / flex-grow 1 | padding 0 / gap 8 | — |
| `DsNavigation/ItemHome` | 72 × 44 | padding 12×8 / gap 4 | radius 8；icon `home` 32×32，色 `#001D5A`（$primary-darker） |
| `star`（Breadcrumb 容器） | 152 × 44 | padding 0 | — |
| `Title` | 112 × 44 | padding 0 × 8 | — |
| `PageTitle` | 96 × 21 | — | Label/16pt/Medium：Roboto 500 / 16px / line-height 130% / color `#0F172A` |
| `Icon Button`（favorite） | 40 × 40 | padding 10 × 20 / gap 4 | bg `rgba(255,255,255,0.0001)` / radius 4 / icon 20×20 色 `#3C4A5B` |
| `Utility Navigation` | 140 × 40 | padding 0 / gap 4 | — |
| `Speed Dial`（通知） | 44 × 44 | padding 12 × 8 / gap 4 / isolation | icon `notifications` 24×24 色 `#001D5A` |
| Notification `Badge` | 22 × 16（absolute right 3 / top 3） | padding 0 × 4 | bg `#FFBE0B`（$tertiary） / radius `1000px` / 文字 Roboto 500 / 12px / color `#1D1400` |
| `<sideMenu>`（settings） | 44 × 44 | padding 12 × 8 / gap 4 | icon `settings` 24×24 色 `#001D5A` |
| Avatar wrapper | 44 × 44 | padding 0 / gap 8 / isolation | — |
| Avatar 內圈（ring） | 30 × 30 | padding 3 / gap 10 | border 1px `#1F57D1`（$darken($primary,5%)）/ radius `1000px` |
| Avatar `_Avatar` | 24 × 24 | padding 4 × 5 / gap 10 | bg `#1F57D1` / radius 20 / 文字 Roboto 500 / 11pt / color `#F2F0F4` |

> ### App shell icon = 實心（Athena DS 硬規則）
> App shell chrome —— **Header**（首頁 `home` / 通知 `notifications` / 設定 `settings`）與 **Nav-rail** 各項（`cards-star` / `account-balance` / `inventory-2` / `badge` / `settings`…）—— 一律用 Iconify `material-symbols:` 的**實心 glyph（無後綴）**，**禁**加 `-outline`。
> **Why**：Athena DS 的 icon 對照表對 shell chrome 指定實心（FILL 1）。AI 常反射地把 chrome 做成線條（`home-outline` / `settings-outline`…），那是**錯的**，也是反覆出現的 review 問題（見 `pitfalls.md`）。
> **例外**：favorite 切換 `star ↔ star-outline`（見下方 Breadcrumb）。**內容區 icon**（操作鈕 / 空狀態 / 分頁 / 設定檔側欄等）依 DS 各自 variant（多為線條）。glyph 名與 Iconify 機制見 `SKILL.md §4 icon 條`；本檔不另立命名規則。

### Breadcrumb

- font-family 英文用 `Roboto`；字級 16px、line-height 130%
- 結構：`item / sep / item / sep / current`
- 最後一層為 `.erp-breadcrumb__current`（**不可點**、weight 500、cursor default）
- 前面層級 `.erp-breadcrumb__item`（可點、weight 400、hover 加 `rgba(15, 23, 42, 0.04)` 底色）
- 分隔符：iconify `material-symbols:chevron-right`（`<iconify-icon class="erp-breadcrumb__sep" icon="material-symbols:chevron-right">`）、18px、灰色。chevron 形狀即「層級」語意；**禁**用 `/`（`/` 是路徑語意），**亦不要用 `>` 字元**——統一用 chevron-right icon。chevron-right 屬筆畫型，無 `-outline` 變體（見 `SKILL.md §4 icon 條`）
  - **尺寸/顏色靠 `app.css` 既有 `.erp-breadcrumb__sep`**：`<iconify-icon>` 無內建尺寸，靠 CSS `font-size`（繼承到 icon）+ `color`（`currentColor`）決定。`.erp-breadcrumb__sep` 須有 `font-size: 18px` 與灰色 `color`，否則 icon 會以預設 1em / 繼承色渲染、尺寸跑掉。從文字 `>` 遷移到 icon 時若沒人改 app.css，**這是最容易被忽略的破點**（psi template 的 `.app-*` 家族對應 class 為 `.breadcrumb__sep`，規則同理）
- **Favorite icon 緊接 current 之後**（**不在右側 actions**）；`.erp-breadcrumb__fav`、40×40

> 麵包屑**層數與各層語意**由專案 profile 定義（如 ERP：模組分類 > 功能名稱 > 單號 三層）。
> Favorite 啟用/未啟用切換：啟用 = `material-symbols:star`（實心）、未啟用 = `material-symbols:star-outline`（線條）；切換規則參照 DS components.css §icon。

---

## Nav-rail（左側）

### Layout 預設狀態

**預設採用：Sidebar Collapsed（側欄收合）**

- **Collapsed**（預設）：寬度 72px，icon + 12px 短標籤（如「我的最愛」「財務」）；產品縮寫文字在底部
- **Expanded**（可選，規格 TBD）：寬版含完整 label 與群組結構；需要時由專案 profile 補完規格

所有 prototype 預設輸出 Collapsed 版型；未明確指定 Expanded 時，**不**自動切換。

### 規格（Collapsed）— 對齊 Figma DsNavigationA8

整個 Nav-rail 為**兩段直欄**：`Top`（我的最愛 + 分隔線）+ `Under`（主項目群、`flex-grow: 1` 撐滿剩餘空間 + ItemSystem 底部固定）。AI 用直覺擺常出現的錯：分隔線位置偏、ItemMenu gap 不一致、Active 用 8% tint 而非 darken 實色、底部 ItemSystem 跟 ItemMenu 數量漂動。

#### 圖層樹

```
DsNavigationA8 (72×100%, padding 8, gap 8, radius 12, bg #001D5A)
├─ Top (56×64, 我的最愛)
│  └─ ItemMenu (56×56, radius 8)
│     ├─ Icon (24×24, cards-star, #FFFFFF)
│     └─ Label "我的最愛" (12pt Regular, #FFFFFF)
│  └─ border-bottom: 1px #7F8996（分隔線）
└─ Under (56×剩餘, flex-grow: 1, gap 8)
   ├─ ButtonController (chevron up, 56×24, 預設 display: none — 收合操作未啟用)
   ├─ ItemMenu × N (56×56, radius 8)
   │  ├─ Active: bg #1F57D1 (=darken($primary, 5%))
   │  └─ Default: bg transparent
   └─ ItemSystem (56×56, radius 8, 含 ERP 縮寫字標)
```

#### Token 速查

| 區段 | 尺寸 | padding / gap | 背景 / 色 / 邊框 |
|---|---|---|---|
| 外層 `DsNavigationA8` | 72 × 100% / max 72 | padding 8 / gap 8 | bg `#001D5A`（$primary-darker） / radius 12 |
| `Top`（我的最愛區） | 56 × 64 | padding 0 0 8 | border-bottom 1px `#7F8996` |
| `Under`（主項目區） | 56 × 剩餘 / flex-grow 1 | padding 0 / gap 8 | — |
| `ItemMenu`（每項） | 56 × 56 / min 56 × 56 | padding 4 × 2 / gap 4 | radius 8 |
| `ItemMenu` Active | 同上 | — | **bg `#1F57D1`**（= $darken($primary, 5%)）；**不**是 8% tint |
| ItemMenu Icon | 24 × 24 | — | bg `#FFFFFF`（$primary-text-color） |
| ItemMenu Label | width auto × 16 | — | Roboto 400 / 12px / line-height 130% / letter-spacing 0.1px / color `#FFFFFF` |
| `ButtonController`（chevron up） | 56 × 24 | padding 4 × 8 | 預設 `display: none`（收合操作未啟用） |
| `ItemSystem`（ERP 縮寫） | 56 × 56 / min 56 × 56 | padding 4 × 2 / gap 4 | radius 8；ERP 字標 = Union 40.76 × 18.06，色 `#FFFFFF` |

#### 互動狀態

| 狀態 | 樣式 |
|---|---|
| Default | bg transparent / 白字 / Roboto 400 |
| Hover | `rgba(255,255,255,.08)` |
| Focus-visible | `outline: 2px solid rgba(255,255,255,.5); outline-offset: -2px;` |
| Active（`.is-active`） | bg **`#1F57D1`** / weight 500 |

> **必含產品縮寫**字標（如「ERP」），位置在 `Under` 區末端的 `ItemSystem`，**不可遺失**——它不是「擺最下面就好」，是 Figma DsNavigationA8 的固定第 N+1 子節點。
> Nav 項目（key / icon / label / 順序）由專案 profile 定義；`icon` 存完整 Iconify id，**Nav-rail 屬 App shell → 用實心 glyph（無後綴）**，如 `material-symbols:account-balance`（見上方 §App shell icon = 實心）。
> Icon glyph 名與 Iconify 機制依 `SKILL.md §4 icon 條`，本檔不另立規則。

---

## Info Bar（底部資訊欄）

- 位置：main panel 底部 sticky，視覺上與 main panel 連續（共用 background）
- 高度 28px、padding `0 16px`、`display: flex; justify-content: space-between; align-items: center;`
- 圓角：承接 main panel `radius-xl`（底部兩角圓 12px、上方無圓角）
- 字級 11px、letter-spacing 0.4px、`color: var(--text-secondary)`
- 左 = **programID**（格式由專案 profile 定義）
- 右 = **版號**（格式由專案 profile 定義）

> Class 命名：可使用 `.info-bar` / `.app-info-bar`（語意中性）或沿用 legacy `.erp-footer`（既有 ERP template）。**概念上**為 info bar，**不是** semantic `<footer>`——它是 main panel 內的最後一段，不是頁面層級的 footer。

---

## 命名前綴

預設使用 `.erp-*` 命名（沿用既有資產）；新專案可改用語意中性的 `.app-*` 前綴，但結構規格不變。命名前綴是專案 profile 可選的覆寫項。

---

## 無對應 profile 的頁型

模組不是傳統 ERP 交易單據 / 設定檔（例如跨系統維運主控台、通知中樞、報表儀表板等），沒有現成的 `profiles/<project>.md` 可套時：

- **只有 App Shell（Header + Nav-rail 結構）允許依 PRD 換掉**——例如整合式主控台可能不需要 breadcrumb 三層結構，改用更扁的 topbar。換掉骨架時，handoff 必須註明「App Shell 偏離 Shared.md 標準結構，理由：___」。
- **List / Form / Modal / Toast / 狀態徽章等元件層永遠沿用 canonical `app.css`**，**不**因為整頁「感覺不像 ERP」就連坐放棄。canonical 裡的按鈕、表格、表單欄位、彈窗、提示訊息本來就是跨頁型通用的元件，只有 App Shell 骨架才跟「是不是傳統 ERP 頁面」有關。
- 判斷順序：先問「這個具體元件（按鈕/表格/彈窗…）本身有沒有 PRD 明確指定的視覺？」——沒有就用 canonical，不要因為整頁模組類型特殊就跳過這一步逐個元件重新確認。

> **Why**：「這個模組不是傳統 ERP 作業/設定檔」是關於整頁定位的判斷，但很容易被過度推廣成「這個模組不適用 canonical 資產」，導致連跟頁型無關的元件層也被重新手刻一遍（見 `pitfalls.md`）。

---

## 專案 profile 應補完

每個專案 profile 都應在本檔規則之上補上：

1. **Nav-rail 項目清單**（key / icon / label / 順序、產品縮寫文字）
2. **Breadcrumb 層級結構**（幾層、各層語意、新單時的空值處理）
3. **ProgramID 格式 與 範例**
4. **版號格式**
5. **Class prefix 覆寫**（若不沿用 `.erp-*`）
6. **任何結構覆寫的理由**（如必要時改變 nav-rail 寬度）
