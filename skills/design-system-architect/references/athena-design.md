# Athena Design — 使用指南（什麼時候用什麼）

> 本檔回答「**何時用哪個 token / 哪個元件**」，是 `athena-tokens.md`（純值查找）的語意對照層。
> 兩檔分工：查具體數值 → `athena-tokens.md`；判斷該選哪個 → 本檔。
>
> 色彩語意依 Material Design 3 color role 慣例（Syncfusion `sf` 主題即建構於此），可從 token 命名直接對應；間距依原檔雙密度（`Athena_Default` / `Athena_Comfortable`）。
> **元件清單**章節對應真實來源（Syncfusion playground + ERP `CLAUDE.md` 對照表），非臆造 — 範圍待確認，見章節說明。

## 目錄

- [色彩：什麼時候用什麼顏色](#色彩什麼時候用什麼顏色)
- [字級：什麼時候用什麼字體層級](#字級什麼時候用什麼字體層級)
- [間距：不同環境下用哪組 padding](#間距不同環境下用哪組-padding)
- [元件：有哪些元件、何時用](#元件有哪些元件何時用)

---

## 色彩：什麼時候用什麼顏色

### 角色配對原則（先理解這個再查表）

Material 3 的色彩是**成對使用**的：每個「容器/底色」都有對應的「on-* 前景色」確保對比。用色時先問三件事：

1. **這是什麼角色？** — 主操作(primary) / 次要(secondary) / 點綴(tertiary) / 狀態(status) / 中性(surface)
2. **是實心強調還是淺底？** — 實心用 `--color-sf-{role}` + `--color-sf-on-{role}`；淺底用 `--color-sf-{role}-container` + `--color-sf-on-{role}-container`
3. **是不是互動疊層？** — hover / focus / pressed 用對應的 `-opacity*` 疊層，不要自己調色

### 主色與動作

| 情境 | 用 token | 說明 |
|------|---------|------|
| 主要操作（Primary button、目前選取、連結、focus 框） | `--color-sf-primary` `rgb(40 119 238)` | 一個畫面語意上的主動作 |
| Primary 上的文字 / icon | `--color-sf-on-primary`（白） | — |
| 低強調的 primary 區塊（選取列底、highlight chip、tab 底） | `--color-sf-primary-container` + `--color-sf-on-primary-container` | 不搶主按鈕的注意力 |
| Primary 在深色背景需更深 | `--color-sf-primary-darken-opacity-5 / -10` | hover/pressed 的加深變體 |
| Primary 互動疊層 | `-opacity-5`(hover 輕) / `-opacity-8`(hover) / `-opacity-12`(focus/selected) / `-11 / -14 / -16`(更強疊層) | Material state layer，疊在元件上 |

### 次要 / 點綴

| 情境 | 用 token |
|------|---------|
| 次要操作、中性強調文字 | `--color-sf-secondary` `rgb(106 111 123)` |
| 次要按鈕底 / 卡片次級底 | `--color-sf-secondary-btn-bg` / `--color-sf-secondary-bg-color`（白） |
| 次要淺底容器 | `--color-sf-secondary-container` + `--color-sf-on-secondary-container` |
| 點綴 / 特殊強調（amber 黃） | `--color-sf-tertiary` `rgb(255 190 11)` + `--color-sf-tertiary-container` |

### 中性、背景、文字、邊框

| 情境 | 用 token |
|------|---------|
| 頁面 / 卡片背景 | `--color-sf-surface`（白）；`--color-sf-background` 為其別名 |
| 次級區塊底（輸入框 filled 底、表頭底） | `--color-sf-surface-variant` `rgb(237 240 247)` |
| 主要文字（標題、正文） | `--color-sf-on-surface` `rgb(15 23 42)` |
| 次要文字（說明、placeholder 旁註） | `--color-sf-on-surface-variant` `rgb(60 74 91)` |
| placeholder 文字 | `--ds-color-placeholder` `rgb(103 113 126)` |
| 一般邊框 | `--color-sf-outline` `rgb(127 137 150)` |
| 分隔線 / 輕邊框 | `--color-sf-outline-variant` `rgb(215 218 224)` |
| 停用文字（disabled） | `--color-sf-on-surface-opacity38`（Material 慣例：disabled 文字 38%） |
| 停用底 / 停用邊框 | `--color-sf-on-surface-opacity12`（disabled 容器 12%） |
| Hover 疊層（中性元件） | `--color-sf-on-surface-opacity4 / opacity5 / opacity8` |
| Modal / Dialog 遮罩 | `--color-sf-scrim-opacity50` `rgb(0 0 0 / 0.5)` |
| Tooltip / Snackbar 反色底與字 | `--color-sf-inverse-surface` + `--color-sf-inverse-on-surface` |

### 狀態色（danger / error / warning / success / info）

四組狀態色用法一致，記住三件套：**實心底 `{status}` + 字 `{status}-text`(白)** / **淺底 `{status}-container` + 字 `on-{status}-container`** / **疊層 `{status}-opacity8`(hover) `opacity12`(selected)**。

| 狀態 | 何時用 | 主色 | 淺底容器 |
|------|--------|------|---------|
| Danger / Error | 破壞性操作（刪除、作廢）、表單錯誤、錯誤訊息 | `--color-sf-danger` / `--color-sf-error` `rgb(244 73 62)` | `--color-sf-error-container` `rgb(253 216 213)` |
| Warning | 需注意但非錯誤（即將逾期、資料可能不完整） | `--color-sf-warning` `rgb(247 144 9)` | `--color-sf-warning-container` `rgb(255 244 214)` |
| Success | 成功完成、已核准、正向確認 | `--color-sf-success` `rgb(18 183 106)` | `--color-sf-success-container` `rgb(209 250 223)` |
| Info | 中性提示、說明、進行中 | `--color-sf-info` `rgb(46 144 250)` | `--color-sf-info-container` `rgb(224 240 255)` |

> ERP 狀態徽章對應建議：草稿=中性(outline/secondary)、已提交=info、已核准=success、已作廢=danger/error。

### 圖表與 `--ds-*` 色盤

- **多系列圖表**：依序用 `--color-sf-series-1` → `series-14`（前 4 個為中性灰階別名，第 5 個起才是飽和色，適合資料系列）。
- **`--ds-color-*`**：Athena 自有的具名色盤（blue / green / purple / red / orange / yellow / mint / lavender / peach …），用於插畫、標籤分類、非語意性的裝飾色；語意性用途（成功/錯誤等）一律走上面的 status 色，不要用具名色硬湊。

---

## 字級：什麼時候用什麼字體層級

字體家族依語系切換：英數 `Roboto`、中文 `Noto Sans TC`（同一變數 `--font-family-sf-fontfamily`，由語系決定）。

| 層級 token | 值 | 何時用 |
|-----------|----|--------|
| `--font-size-sf-h1` | 32px | 頁面主標題（少用，多為登入頁/空白頁大標） |
| `--font-size-sf-h2` | 28px | 區段大標 |
| `--font-size-sf-h3` | 24px | 對話框標題、卡片主標 |
| `--font-size-sf-h4` | 22px | 次級標題 |
| `--font-size-sf-h5` | 20px | summary card 標題、section 標題 |
| `--font-size-sf-h6` | 18px | 小節標題 |
| `--font-size-sf-text-lg` | 16px | 強調正文、表單重要欄位值 |
| `--font-size-sf-text-md` | 14px | **預設正文 / 表單 / 按鈕**（ERP 介面預設字級） |
| `--font-size-sf-text-sm` | 12px | 次要說明、表格內文、標籤 |
| `--font-size-sf-text-xs` | 11px | 密集表格、輔助標註 |
| `--font-size-sf-text-xxs` | 10px | 極密集場景、角標（謹慎用，低於此影響可讀性） |

字重：

| token | 值 | 何時用 |
|-------|----|--------|
| `--font-weight-sf-normal` | 400 | 正文 |
| `--font-weight-sf-medium` | 500 | 標籤、按鈕文字、表頭、需輕強調處 |
| `--font-weight-sf-bold` | 700 | 標題、關鍵數值（金額合計） |

---

## 間距：不同環境下用哪組 padding

原檔提供兩套密度模式，**同名 token、值不同**，依使用環境切換（詳值見 `athena-tokens.md` §Space 並列表）：

| 密度模式 | 何時用 | 特性 |
|---------|--------|------|
| **`Athena_Default`**（緊湊） | 資料密集場景：列表 Grid、表格、報表、需一屏多筆的 ERP 主畫面 | 每階較小（margin-medium = 8px），資訊密度高 |
| **`Athena_Comfortable`**（舒適） | 表單填寫、對話框、觸控場景、需降低操作壓力處 | 每階放大一級（margin-medium = 12px），呼吸感足 |

> 兩模式是「同一階梯往上挪一格」：Comfortable 的某階 ≈ Default 的下一階。切換密度時整頁一致採用同一模式，不要混用。

選 padding 的心法（8px 網格對齊）：

| 用途 | 建議階（Default 值 / Comfortable 值） |
|------|-----------------------------------|
| 元件內元素間距（icon 與文字） | `small` 4 / 8 |
| 按鈕、輸入框內距 | `medium`–`large` 8–12 / 12–16 |
| 卡片 / 區段內距 | `extra-large` 16 / 20 |
| 區段之間 margin | `3extra-large`–`4extra-large` 24–32 / 32–40 |
| 頁面外框 margin | `4extra-large`+ 32+ / 40+ |
| 圓角搭配 | 按鈕 `--ds-radius-small` 4（FAI2 Figma 校準 2026-06-18；輸入框待同步確認，先前誤記 8px）、卡片 `--ds-radius-extra-large` 12、pill / 全圓 `--ds-radius-10extra-large` 1000 |
| 邊框寬度 | 一般 `--ds-borderwidth-small` 1px、強調/focus `--ds-borderwidth-medium` 2px |

---

## 元件：有哪些元件、何時用

**完整元件目錄（94 個 + 客製 `Ds`，含用途、用在哪、採用狀態）見同目錄 `athena-components.md`**，避免兩處清單漂移，此處僅列每日最常用的核心集作快速參考：

| 元件 | 何時用 | 採用狀態 |
|------|--------|---------|
| Grid | 資料列表、報表、可排序/篩選表格 | ✅ 已產出 |
| Tab | 同一畫面切換多組內容 | ✅ 已產出 |
| Stepper | 單據狀態流程、多步驟精靈 | ✅ 已產出 |
| Dialog | 確認操作、deeplink 跳轉、表單 modal | ✅ 已產出 |
| Button / FAB | 操作 CTA / 浮動主操作 | ✅ 已產出 |
| TextBox / NumericTextBox | 文字 / 數值・金額輸入 | ✅ 已產出 |
| DropDownList | 單選下拉 | ✅ 已產出 |
| DatePicker | 日期選擇（另有客製 `DsDatePicker`） | ✅ 已產出 |
| Toast / Skeleton | 結果提示 / 載入佔位 | ✅ 已產出 |

> 選元件原則：Syncfusion 既有 → 客製 `Ds`（`DsDatePicker` / `DsStatusBadge` / `DsCard` / `DsSideNavMenu`）→ 行內組合。新增客製前先 audit `athena-components.md` 是否已有可用者。
