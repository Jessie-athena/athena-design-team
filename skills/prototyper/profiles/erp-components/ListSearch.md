# List 搜尋區結構與互動

> 屬於 `profiles/erp-transaction.md` 的延伸元件規格。
> 載入時機：隨 ERP profile **自動載入**（無論作業檔 / 設定檔；由 `SKILL.md §支援檔案` 規定——該處為載入規則的**單一來源**）。
>
> 上層 profile：`profiles/erp-transaction.md`
> 同層元件：`ListSearch.md` / `DataGrid.md` / `FormGroup.md` / `FormFooter.md` / `SummaryCard.md` / `Stepper.md` / `Permissions.md` / `RelBanner.md` / `Skeleton.md`

---

工具列下方獨立一列、白底卡片。左欄位、右操作的二段式版面，**操作區永不被擠壓、永不換行**。

## DOM Anatomy

```
.search-bar                       (外層 flex row · 永不換行)
├─ .search-bar__fields            (左：欄位區，flex-wrap 視狀態切換)
│  └─ .field × N                  (每個欄位 = label + input/select)
└─ .search-bar__actions           (右：操作區，flex-shrink: 0 永不被擠壓)
   ├─ .btn-icon.is-primary        (搜尋 🔍 — 主色填底)
   ├─ .btn-icon.is-outline        (清除 🗑 — 主色描邊)
   └─ .btn-icon.is-outline        (收合 ▲ / 展開 ▼ — 條件顯示)
```

## Token 與尺寸

> 卡片背景 / 邊框 / 圓角、各內距、gap、欄位高度、input 與按鈕尺寸等字面值見 app.css `.search-bar`（展開）/ `.search-bar.is-collapsed`（收合）/ `.search-bar__fields .field`；本表僅記語意角色。

| 項目 | 角色 |
|---|---|
| 卡片背景 / 邊框 / 圓角 | 白底卡片、`var(--border-default)` 邊、`--radius-lg` 圓角 |
| 卡片內距 | 展開四邊一致；收合上下較緊 |
| 欄位區與操作區水平 gap | 二段式版面的分隔距 |
| 欄位之間 gap | 展開（垂直 < 水平）/ 收合較緊 |
| 欄位 min-width | RWD 換行唯一觸發門檻（見 §RWD） |
| 欄位高度 | 展開＋收合皆含 label，兩態等高 |
| input / select 高度 | 與操作按鈕齊高 |
| 操作按鈕尺寸 / 之間 gap | 正方 icon button；固定 shrink |

## 展開狀態（預設）

- `.search-bar` → `display: flex; flex-wrap: nowrap; align-items: flex-start`
- `.search-bar__fields` → `display: flex; flex-wrap: wrap; align-items: flex-end; flex: 1 1 auto; min-width: 0`
- `.search-bar__actions` → `flex-shrink: 0; align-self: stretch; align-items: flex-end`
- 每個 `.field` → `flex: 1 1 <欄位 min-width>; min-width: <同值>`（欄位 min-width 值見 app.css `.search-bar__fields .field`）
- 寬度足夠 → 欄位等寬平均分配，並排同一行
- 寬度不足 → 欄位自動換行（操作區固定在第一行右上不被擠下）

## RWD 4 斷點對照

> 對應 `REFERENCE.md §8` 的 XL / L / M / S 四斷點。Search Bar 採「Flex Wrap → 自動收合」兩階段策略，避免水平捲動。

> 各斷點的欄位 flex-basis 字面值見 app.css `@media` 區塊（`.search-bar__fields .field`）；本表記斷點門檻與排列／收合行為。

| 斷點 | 欄位排列 | 收合行為 |
|---|---|---|
| **XL ≥ 1440** | 單列 5 欄（較寬 flex-basis） | 不收合 |
| **L 1280–1439** | 單列 5 欄（中 flex-basis） | 不收合 |
| **M 1024–1279** | 2 列換行（3+2 或 4+1，較寬 flex-basis） | 偵測到換行 → 顯示「收合」按鈕 |
| **S 768–1023** | 預設收合，依寬度動態顯示 N 個欄位（至少 1 個；固定欄寬） | 進入頁面 `searchCollapsed = true`；點「展開」可顯示全部 |

> **跨斷點切換規則**：S → M/L/XL 自動展開；M/L/XL → S **不**自動收合（保留使用者目前已展開的狀態，欄位以自然換行呈現）。只有首次進入 S 才會預設收合。

## RWD 換行觸發條件

換行於以下任一條件成立時發生：

1. **欄位數量 > 5**（預設 5 個 filter；新增第 6 個會直接掉到下一行）
2. **外層容器寬度不足**：依當前斷點的 flex-basis 計算容納寬度；容器寬度低於該臨界值 → 最右側欄位優先換行
3. **S 斷點首次進入**：直接套 `.is-collapsed`，跳過「先展開再收合」的中間狀態

## 換行偵測（JS）

- `ResizeObserver` 監聽 `.search-bar` 與 `.search-bar__fields` 尺寸變化
- 比對所有 `.field` 的 `offsetTop`：只要任一欄位的 `top > 第 1 個欄位的 top` → `searchWrapped = true`
- 並於 `document.fonts.ready` 之後再量測一次，避免字體載入前判斷錯誤

## 操作區按鈕顯示規則

| 狀態 | 按鈕數量 | 內容 |
|---|---|---|
| 未換行（內容符合容器寬度） | **2 個** | 搜尋 🔍、清除 🗑 |
| 已換行（內容超出 或 欄位 > 5） | **3 個** | 搜尋 🔍、清除 🗑、收合 ▲ |
| 收合狀態 | **3 個** | 搜尋 🔍、清除 🗑、展開 ▼ |

收合按鈕條件渲染：`v-if="searchWrapped || searchCollapsed"`

## 收合狀態（`.is-collapsed`）

點擊 ▲ → `searchCollapsed = true`，整張卡片切換為**單行模式**：

- `.search-bar` 加上 `.is-collapsed`；卡片內距收緊（值見 app.css `.search-bar.is-collapsed`）
- `.search-bar__fields` → `flex-wrap: nowrap; overflow: hidden`（強制單行）
- 欄位 → 固定不縮（`flex: 0 0` 欄寬，欄寬同 min-width；值見 app.css）；**label 保留顯示**（與展開狀態一致，方便辨識）
- 容納邏輯（即時計算；式中 `outerGap` / `innerGap` / `fieldWidth` 取 app.css `.search-bar` 對應數值）：

  ```
  available    = barInnerWidth - actionsWidth - outerGap
  visibleCount = floor((available + innerGap) / (fieldWidth + innerGap))
  ```

- 第 N 個欄位 `v-show="!searchCollapsed || collapsedVisibleCount > N-1"`
- **容納不下的欄位完全隱藏**（`display: none`，**不切半、不模糊邊緣**）
- 容器寬度變化時 `ResizeObserver` 即時重算
- 操作區 → `align-self: flex-end`（底部對齊輸入框）

## 收合按鈕 icon / title / aria-label 切換

| 屬性 | `searchCollapsed = false`（可收合） | `searchCollapsed = true`（已收合） |
|---|---|---|
| icon | `material-symbols:keyboard-arrow-up` ▲ | `material-symbols:keyboard-arrow-down` ▼ |
| title | 「收合搜尋」 | 「展開搜尋」 |
| aria-label | `collapse search` | `expand search` |

## 三種狀態速覽

```
[A] 寬螢幕 / 欄位 ≤ 5 / 一行容納得下
┌──────────────────────────────────────────────────────────────────┐
│ [館別代號 ] [館別名稱 ] [所屬區域 ] [公司別 ] [狀態 ]    🔍 🗑   │
└──────────────────────────────────────────────────────────────────┘

[B] 窄螢幕 / 欄位 > 5 → 欄位換行；操作區停留在第一行右上
┌──────────────────────────────────────────────────────────────────┐
│ [館別代號 ] [館別名稱 ] [所屬區域 ]                              │
│ [公司別 ]   [狀態 ]                                  🔍 🗑 ▲    │
└──────────────────────────────────────────────────────────────────┘

[C] 收合 → 單行；左側保留容納得下的欄位（含 label），右側 3 顆按鈕
┌──────────────────────────────────────────────────────────────────┐
│ [代碼  ▾] [名稱   ▾] [地點  ▾]                  🔍 🗑 ▼        │
└──────────────────────────────────────────────────────────────────┘
```

## 互動行為

| 互動 | 行為 |
|---|---|
| 任一輸入框按 `Enter` | 觸發搜尋（`applyFilters`） |
| 點 🔍 搜尋 | 套用所有 filter，頁碼重設為 1 |
| 點 🗑 清除 | 清空所有 filter 並重新查詢 |
| 點 ▲ 收合 | `searchCollapsed = true`，卡片變單行 |
| 點 ▼ 展開 | `searchCollapsed = false`，恢復多行展開 |
| 視窗 resize | ResizeObserver 自動重算 `searchWrapped` 與 `collapsedVisibleCount` |
| 跨斷點 S → M/L/XL | 自動展開（`searchCollapsed = false`） |
| 跨斷點 M/L/XL → S | **不**自動收合，保留目前狀態 |

## 設計原則（給 reviewer 引用）

1. **左欄位、右操作** 的二段式版面，操作區永不被擠壓、永不換行
2. **欄位最小寬**（值見 app.css `.search-bar__fields .field`）是 RWD 換行的唯一觸發條件
3. **操作按鈕數量隨狀態自動調整**（2 ↔ 3），收合按鈕僅在需要時出現
4. **收合不是「完全隱藏」**，而是把可容納的欄位保留在原處（單行、保留 label），常用 filter 仍可立即使用
5. **不顯示半個欄位** — 容納不下就完全隱藏，避免視覺破碎
6. 所有換行 / 容納計算均**即時動態，依容器寬度**，不依視窗寬度（適用側邊欄收合、嵌入面板等場景）
7. **不會中途自動收合** — 一旦使用者處於展開狀態，縮窄容器只會讓欄位換行，不會強迫收合

---

## Toolbar / Search Bar 按鈕 token 速查

> 本節適用 **List View 頂部 toolbar**（主 CTA / 批次操作 / icon button 群）**與**本檔的 search bar 操作區。所有按鈕共用同一組視覺 token，本表為 source of truth。

> Token 來自 Athena DS Figma toolbar 圖層 + CSS。AI 容易把按鈕反射做成 Bootstrap / Tailwind UI 樣式，請依本表逐項對照（特別是「主 CTA 動詞**無 icon**」與「分隔線是 40×0 旋轉 90deg」兩個易踩點）。

### §1 按鈕 token 表

> bg / border / text / icon 色、radius、padding 字面值見 app.css（Primary CTA `.btn--primary`、icon button `.btn-icon-sq--primary` / `--danger-outline`、分隔線 `.toolbar__sep`）；本表記語意角色與易踩點。

| 按鈕類型 | 範例 | 視覺角色 |
|---|---|---|
| Primary CTA（動詞） | 「新增」/「查詢」 | primary 填底、白字、Roboto Medium |
| Secondary outline（動詞） | 「取消」/「清除」/「篩選」 | 透明底 + `$border` 描邊、`$text-primary` 字 |
| Icon Button（primary） | 列印 / 下載 / 收藏快捷 | 透明底 + primary 描邊、inner icon primary 色 |
| Icon Button（danger） | 批次刪除 | 透明底 + error 描邊、inner icon error 色 |
| 分隔線（Line） | 介於 icon button 群 | `$border-light` 直立細線（旋轉 90deg） |
| Filter button | 「篩選」 | 同 Secondary outline + tune icon |

**統一尺寸**（值見 app.css）：
- 帶 label 按鈕齊高；Primary CTA 寬度依文字長度自適應
- Icon Button 一律正方
- 按鈕群之間 gap：toolbar 內較寬、search bar 操作區較窄

**動詞 CTA 不加 icon**：
- 「新增」「查詢」「清除」「取消」**禁加 icon**；CTA label 本身已說明動作
- icon button 才能用 icon（如列印、下載、批次刪除）
- 詳見 `profiles/erp-transaction.md §按鈕 icon 政策`

### §2 DropdownList filled token

`DropdownList` 用於 toolbar 的 filter 下拉（如「狀態」、「公司別」、「分類」等）。**預設 Filled 風格**（Material 3），不是 outlined。

#### 視覺規格

> bg / border-bottom / text 色字面值見 app.css `.select`（filled 風格）；本表記各態語意。

| 狀態 | 語意 |
|---|---|
| Default（未選） | surface-variant 底、`$border` 底線、placeholder 灰字 |
| Filled-in（已選） | 同底線、value 深字 |
| **Focus** | 底線**加粗為 primary 色**；**禁加 outline ring** |
| Selected item label（在 context menu 內） | label 轉 primary 色、高亮整列 |

#### 結構

> 寬度 / 高度 / radius / padding / icon 容器尺寸字面值見 app.css `.select`；本表記結構規則。

| 屬性 | 規則 |
|---|---|
| 寬度 | 固定寬 + min-width 下限（值見 app.css） |
| 高度 | 與其他 filter 控制元件齊高 |
| border-radius | **僅上方圓角**（Material Filled 簽名） |
| padding | 左內距 + 右側預留 caret icon 容器空間 |
| Inner gap（文字 ↔ icon） | 小間距 |
| Caret icon 容器 | 正方容器，內含 `material-symbols:keyboard-arrow-down` |

> Focus 時底線加粗為 primary 色，**不**加 outline ring（Tailwind 慣例 ring 在 Material Filled 是錯的，常見反射錯誤見 `pitfalls.md` [2026-05-20] Filled input 條目）。

### §3 Context Menu（DropdownList 展開）

點 DropdownList caret 後彈出的選項清單。

#### 容器

> 寬度 / 背景 / radius 字面值見 app.css；本表記規則。

| 屬性 | 規則 |
|---|---|
| 寬度 | 同 trigger 寬度 |
| 背景 | 純白（疊一層 linear-gradient 0deg 但實質純白） |
| border-radius | 小圓角 |
| z-index | 高於 toolbar（依專案 layer policy） |

#### 列項（每列 = Atom / Context Menu）

> 列高 / padding / 字級字面值見 app.css；本表記規則。

| 屬性 | 規則 |
|---|---|
| 列高 | 與其他選單列齊高 |
| padding | 上下窄、左右一致 |
| inner gap | label 與右側 shortcut 之間（**目前未啟用 shortcut**，預留設計） |
| Label | Roboto 400、`$text-primary` 色 |

#### 互動狀態

> row bg / label color 字面值見 app.css；本表記語意。

| 狀態 | 語意 | 用途 |
|---|---|---|
| Default | transparent 底、`$text-primary` 字 | 一般列項 |
| **Hover** | DS 規定的中性深色半透明疊層（**非隨手灰色**） | 滑鼠停留 |
| Selected | label 轉 primary 色 | 已選項 |

#### Header（context menu 中分群標題）

`Atom / Context Menu Header`（含「Header」字標）：
- Label：Roboto Medium、`$text-primary` 色
- 與下一段之間用 `$flyout-border` 細線分隔

#### add 列（context menu 末尾 + Add 按鈕，可選）

當 DropdownList 支援「新增選項」時，context menu 最末加一列：
- 列頂加 `$flyout-border` 細線
- 含 Plus icon + label（皆 primary 色、Roboto Medium）
- 點擊觸發「新增此選項」流程

---

## 批次選取 chip（`chip--selected`）

> 對齊基準：`design-prototype/web-erp/庫存模組`。List 進入批次模式（`selectedIds.length > 0`）時，toolbar 主操作切換為批次操作，並以 `chip--selected` 顯示「已選取 N 筆」+ 清除入口。`psi-transaction-page.html` 已採此結構，本節為其權威規格。

### Anatomy

```html
<span class="chip--selected">
  <span class="chip--selected__label">已選取 {{ selectedIds.length }} 筆</span>
  <button class="chip--selected__close" @click="clearSelection" aria-label="取消選取">
    <iconify-icon icon="material-symbols:close"></iconify-icon>
  </button>
</span>
```

### 視覺規格

> 尺寸 / 色 / 字級字面值見 app.css `.chip--selected` / `.chip--selected__close`；本表記語意與結構。

| 元素 | 規則 |
|---|---|
| `.chip--selected` | inline-flex、透明底 + info 色描邊、`--radius-sm` 圓角、info 色字、不換行；與 toolbar 按鈕齊高 |
| `.chip--selected__close` | 正方、透明底無框、info 色、可點 |
| close icon | `material-symbols:close` |
| close hover | info 色淡底 + 小圓角 |

### 互動

- 出現條件：`v-if="selectedIds.length > 0"`（且 `canSelect`，詳 `Permissions.md`）。
- 與 toolbar 主操作互斥：未選取顯示主 CTA（新增 / 匯入…）；選取 > 0 顯示批次操作群 + `chip--selected`。
- 點 close → `clearSelection` 清空 `selectedIds` 並退出批次模式（toolbar 切回主操作）。
- 高度與 toolbar 其他按鈕齊高，視覺對齊同一列。

> 用 info 色而非 primary 色：批次選取是「當前選取範圍」的中性提示，與主操作 CTA 的 primary 色刻意區隔（兩色值見 app.css）。

---

## 日期區間欄（`date-range`）

> 對齊基準：`design-prototype/web-erp/庫存模組`（領料單 / 出庫單搜尋區的「領料日期 / 出庫日期」）。搜尋區的「起訖日期」**標準採自訂雙月曆 `date-range`**，取代 `REFERENCE.md` 對照表中「兩個 `<input type="date">`」的最小實作。簡易 prototype 仍可退回兩個原生日期欄，但對齊庫存標準時用本元件。

### Anatomy

```html
<div class="field field--range">
  <label>領料日期</label>
  <div class="date-range">
    <button type="button" class="input filled date-range__trigger" :class="{ 'is-placeholder': !range.from && !range.to }" @click="rangeOpen = !rangeOpen">
      <span class="date-range__text">{{ rangeLabel }}</span>
      <span class="date-range__icon"><iconify-icon icon="material-symbols:calendar-today-outline"></iconify-icon></span>
    </button>
    <div v-if="rangeOpen" class="date-range__backdrop" @click="rangeOpen = false"></div>
    <div v-if="rangeOpen" class="date-range__pop">
      <div class="cal"><!-- 雙月：.cal__month × 2，每月 .cal__head + .cal__grid（.cal__dow / .cal__day / .cal__daynum） --></div>
      <div class="date-range__actions">
        <button class="cal__btn" @click="clearRange">清除</button>
        <button class="cal__btn cal__btn--primary" @click="applyRange">確定</button>
      </div>
    </div>
  </div>
</div>
```

### 視覺規格（重點）

> 尺寸 / 色 / 位移字面值見 app.css（`.date-range__trigger` / `.date-range__pop` / `.cal__month` / `.cal__daynum` / `.cal__day.is-start` / `.cal__day.in-range` / `.cal__btn`）；本表記結構與語意。

| 元素 | 規則 |
|---|---|
| `.date-range__trigger` | 沿用 `.input.filled`（filled 底 + 上圓角 + 底線；詳 `erp-transaction.md §輸入欄樣式`）；flex 兩端對齊、左對齊文字、可點 |
| placeholder 態 | `.is-placeholder .date-range__text` 轉 placeholder 灰字（`var(--text-placeholder)`） |
| `.date-range__icon` | `material-symbols:calendar-today-outline`，`var(--text-secondary)` 色 |
| `.date-range__pop` | 絕對定位於 trigger 下方、純白底 + `var(--border-default)` 邊 + `--radius-lg` 圓角 + menu 層陰影；z-index 高於 backdrop |
| `.cal__month` | 雙月並排，月與月以細線分隔 |
| `.cal__daynum` | 圓形日格（`--radius-full`）；hover primary 淡底 |
| 選取端點（`.is-start` / `.is-end`） | primary 實底、白字 |
| 區間中段（`.in-range`） | primary 淡藍帶（端點半寬） |
| `.cal__btn` | 透明底文字鈕、`--radius-sm`、primary 字；hover primary 淡底 |

### 互動

- 點 trigger 開 popup；點 backdrop / 再點 trigger / `Esc` 關閉。
- 選第一天為 start、第二天為 end；反序自動對調；區間中段顯示 `.in-range` 淡藍帶。
- 「確定」寫回 `range.from / range.to` 並更新 trigger 文字（格式 `YYYY-MM-DD ～ YYYY-MM-DD`）；「清除」清空兩端並回 placeholder。
- 與搜尋區其他欄位同高（trigger = filled 輸入欄高，值見 app.css），佔一個 `.field` 槽位（換行 / 收合行為比照 §RWD）。
