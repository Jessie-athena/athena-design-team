# Shared Profile — 頁面框架

> 跨專案共用的頁面骨架規則。所有專案 profile（如 `erp.md`）在本檔規則之上**附加**或**覆寫**特定行為。
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

### 整體 Layout 規格（必依）

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

- 高度 56px、垂直置中、`background: transparent`
- 結構從左到右：
  1. **Home button** — 寬 72px × 高 56px（**寬度必須對齊 nav-rail 72px**），icon 28px、filled 變體
  2. **Breadcrumb 列**（含 favorite ⭐ icon，緊接最後一層之後）— `flex: 1` 撐滿中間
  3. **Header actions**（右側）— `gap: 4px`，含通知（含 badge）/ 設定 / avatar
- Icon variant 一律 **filled**（`home` / `notifications` / `settings`，**禁** `*_outline`）
- 右側 icon 按鈕（`.erp-header__icon`）尺寸 40×40，hover `primary @ 8%` 底色、active `primary @ 12%`
- Notification badge：min-width 18px × 18px、bg `rgb(247, 192, 0)`、白字 10px/700、圓角 `var(--radius-full)`

### Breadcrumb

- font-family 英文用 `Roboto`；字級 16px、line-height 130%
- 結構：`item / sep / item / sep / current`
- 最後一層為 `.erp-breadcrumb__current`（**不可點**、weight 500、cursor default）
- 前面層級 `.erp-breadcrumb__item`（可點、weight 400、hover 加 `rgba(15, 23, 42, 0.04)` 底色）
- 分隔符：`/`、18px、灰色
- **Favorite icon 緊接 current 之後**（**不在右側 actions**）；`.erp-breadcrumb__fav`、40×40、未收藏 `star_border`、已收藏 `star`（金色 `rgb(247, 192, 0)`，FILL 1）

> 麵包屑**層數與各層語意**由專案 profile 定義（如 ERP：模組分類 / 功能名稱 / 單號 三層）。

---

## Nav-rail（左側）

- **寬度 72px**(全域固定，不可變)、高度 100%
- 背景深色 `rgb(var(--color-sf-on-primary-container))`、圓角 `var(--radius-xl)`
- 內部 `padding: 8px`、`flex-direction: column`、`gap: 8px`
- 結構從上到下：
  1. `.nav-rail__top` — **我的最愛**（56×64，下方 8px、`1px solid var(--border-strong)` 分隔線）
  2. `.nav-rail__items` — 主要 nav 按鈕清單（`flex: 1`，承接中間所有可變長度的 ItemMenu）
  3. **NAV 底部區**（`.nav-rail__items` 之後的區段，對應 Figma `DsNavigation/ItemSystem`）— 必含；放產品縮寫文字（如「ERP」）

### Nav 按鈕（`.nav-rail__btn`）

- 56 × 56、padding 4px 2px、圓角 `var(--radius-lg)`
- 結構：icon (24px, filled) + label (12px / weight 400 / line-height 1.3 / letter-spacing 0.1px)
- 預設色：白字
- Hover：`rgba(255,255,255,.08)`
- Focus-visible：`outline: 2px solid rgba(255,255,255,.5); outline-offset: -2px;`
- Active（`.is-active`）：bg `rgb(31, 87, 209)`（primary darken-5）、weight 500

> **必含產品縮寫**文字（如「ERP」），位置在 `.nav-rail__items` 之後的 NAV 底部區，**不可遺失**。
> Nav 項目（key / icon / label / 順序）由專案 profile 定義。

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

## 專案 profile 應補完

每個專案 profile 都應在本檔規則之上補上：

1. **Nav-rail 項目清單**（key / icon / label / 順序、產品縮寫文字）
2. **Breadcrumb 層級結構**（幾層、各層語意、新單時的空值處理）
3. **ProgramID 格式 與 範例**
4. **版號格式**
5. **Class prefix 覆寫**（若不沿用 `.erp-*`）
6. **任何結構覆寫的理由**（如必要時改變 nav-rail 寬度）
