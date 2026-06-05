# List 搜尋區結構與互動

> 屬於 `profiles/erp-transaction.md` 的延伸元件規格。
> 載入時機：隨 ERP profile **自動載入**（無論作業檔 / 設定檔；由 `SKILL.md §支援檔案` 規定——該處為載入規則的**單一來源**）。
>
> 上層 profile：`profiles/erp-transaction.md`
> 同層元件：`ListSearch.md` / `DataGrid.md` / `FormGroup.md` / `FormFooter.md` / `SummaryCard.md` / `Stepper.md`

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

| 項目 | 值 |
|---|---|
| 卡片背景 | `#FFFFFF` |
| 卡片邊框 | `1px solid var(--border-default)` |
| 卡片圓角 | `8px`（`--radius-lg`） |
| 卡片內距（展開） | `16px` 四邊 |
| 卡片內距（收合） | `12px 16px` |
| 欄位區與操作區水平 gap | `16px` |
| 欄位之間 gap（展開） | `12px 16px`（垂直 12 / 水平 16） |
| 欄位之間 gap（收合） | `8px` |
| 欄位 min-width | **150px** |
| 欄位高度（展開＋收合，皆含 label） | `62px`（label 18 + gap 4 + input 40） |
| input / select 高度 | `40px` |
| 操作按鈕尺寸 | `40 × 40px` |
| 操作按鈕之間 gap | `8px` |

## 展開狀態（預設）

- `.search-bar` → `display: flex; flex-wrap: nowrap; align-items: flex-start`
- `.search-bar__fields` → `display: flex; flex-wrap: wrap; align-items: flex-end; flex: 1 1 auto; min-width: 0`
- `.search-bar__actions` → `flex-shrink: 0; align-self: stretch; align-items: flex-end`
- 每個 `.field` → `flex: 1 1 150px; min-width: 150px`
- 寬度足夠 → 欄位等寬平均分配，並排同一行
- 寬度不足 → 欄位自動換行（操作區固定在第一行右上不被擠下）

## RWD 4 斷點對照

> 對應 `REFERENCE.md §8` 的 XL / L / M / S 四斷點。Search Bar 採「Flex Wrap → 自動收合」兩階段策略，避免水平捲動。

| 斷點 | 欄位排列 | 欄位 flex-basis | 收合行為 |
|---|---|---|---|
| **XL ≥ 1440** | 單列 5 欄 | `flex: 1 1 180px` | 不收合 |
| **L 1280–1439** | 單列 5 欄 | `flex: 1 1 160px` | 不收合 |
| **M 1024–1279** | 2 列換行（3+2 或 4+1） | `flex: 1 1 200px` | 偵測到換行 → 顯示「收合」按鈕 |
| **S 768–1023** | 預設收合，依寬度動態顯示 N 個欄位（至少 1 個） | 固定 `150px` | 進入頁面 `searchCollapsed = true`；點「展開」可顯示全部 |

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

- `.search-bar` 加上 `.is-collapsed`；卡片內距改 `12px 16px`
- `.search-bar__fields` → `flex-wrap: nowrap; overflow: hidden`（強制單行）
- 欄位 → `flex: 0 0 150px`；**label 保留顯示**（與展開狀態一致，方便辨識）
- 容納邏輯（即時計算）：

  ```
  available    = barInnerWidth - actionsWidth - outerGap(16)
  visibleCount = floor((available + innerGap(8)) / (fieldWidth(150) + innerGap(8)))
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
2. **欄位最小寬 150px**，是 RWD 換行的唯一觸發條件
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

| 按鈕類型 | 範例 | bg | border | text / icon | radius | padding |
|---|---|---|---|---|---|---|
| Primary CTA（動詞） | 「新增」/「查詢」 | `#2877EE` | 1px `#2877EE` | `#FFFFFF` / Roboto Medium 14px / line-height 150% / letter-spacing 0.24px | 4 | 10 × 16 |
| Secondary outline（動詞） | 「取消」/「清除」/「篩選」 | `rgba(255,255,255,0.0001)` 透明 | 1px `#7F8996`（$border） | `#0F172A` / Roboto Medium 14px | 4 | 10 × 16 |
| Icon Button（primary） | 列印 / 下載 / 收藏快捷 | `rgba(255,255,255,0.0001)` 透明 | 1px `#2877EE` | inner icon 20 × 20 / 色 `#2877EE` | 4 | 10 × 20 |
| Icon Button（danger） | 批次刪除 | `rgba(255,255,255,0.0001)` 透明 | 1px `#F4493E` | inner icon 20 × 20 / 色 `#F4493E` | 4 | 10 × 20 |
| 分隔線（Line） | 介於 icon button 群 | — | **1px `#D7DAE0`（$border-light）/ rotate 90deg** | — | — | width 40 / height 0 |
| Filter button | 「篩選」 | 同 Secondary outline | 同 Secondary outline | label + tune icon 20×20 色 `#0F172A` | 4 | 10 × 16 |

**統一尺寸**：
- 帶 label 按鈕高度 `40px`；Primary CTA 寬度依文字長度（範例「新增」134px、「查詢」97px）
- Icon Button 一律 `40 × 40px`
- 按鈕群之間 gap `12px`（toolbar 內）/ `8px`（search bar 操作區）

**動詞 CTA 不加 icon**：
- 「新增」「查詢」「清除」「取消」**禁加 icon**；CTA label 本身已說明動作
- icon button 才能用 icon（如列印、下載、批次刪除）
- 詳見 `profiles/erp-transaction.md §按鈕 icon 政策`

### §2 DropdownList filled token

`DropdownList` 用於 toolbar 的 filter 下拉（如「狀態」、「公司別」、「分類」等）。**預設 Filled 風格**（Material 3），不是 outlined。

#### 視覺規格

| 狀態 | bg | border-bottom | text | 其他 |
|---|---|---|---|---|
| Default（未選） | `#EDF0F7`（surface-variant） | 1px `#7F8996` | placeholder `#67717E` | — |
| Filled-in（已選） | `#EDF0F7` | 1px `#7F8996` | value `#49454E` | — |
| **Focus** | `#EDF0F7` | **2px `#2877EE`**（primary，加粗為 2px） | `#49454E` | **禁加 outline ring** |
| Selected item label（在 context menu 內） | — | — | **`#2877EE`** | 高亮整列 |

#### 結構

| 屬性 | 值 |
|---|---|
| 寬度 | 200px / min-width 150px |
| 高度 | 40px |
| border-radius | `4px 4px 0 0`（**僅上方圓角**，Material Filled 簽名） |
| padding | `0 0 0 10px`（左內距 10，右側留 32 給 caret icon 容器） |
| Inner gap（文字 ↔ icon） | 6px |
| Caret icon 容器 | 32 × 32（內含 `material-symbols:keyboard-arrow-down` 16×16 色 `#3C4A5B`） |

> Focus 時底線加粗為 2px primary 色，**不**加 outline ring（Tailwind 慣例 ring 在 Material Filled 是錯的，常見反射錯誤見 `pitfalls.md` [2026-05-20] Filled input 條目）。

### §3 Context Menu（DropdownList 展開）

點 DropdownList caret 後彈出的選項清單。

#### 容器

| 屬性 | 值 |
|---|---|
| 寬度 | 同 trigger 寬度（200px） |
| 背景 | `#FFFFFF`（疊一層 linear-gradient 0deg 但實質為純白） |
| border-radius | `4px` |
| z-index | 高於 toolbar（依專案 layer policy） |

#### 列項（每列 = Atom / Context Menu）

| 屬性 | 值 |
|---|---|
| 列高 | 32px |
| padding | `3px 8px` |
| inner gap | 65px（label 與右側 shortcut 之間，**目前未啟用 shortcut**，預留設計） |
| Label | Roboto 400 / 14px / line-height 150% / letter-spacing 0.24px / color `#0F172A` |

#### 互動狀態

| 狀態 | row bg | label color | 用途 |
|---|---|---|---|
| Default | transparent | `#0F172A` | 一般列項 |
| **Hover** | `rgba(15, 23, 42, 0.05)` | `#0F172A` | 滑鼠停留 |
| Selected | （由 selected item color 規則決定，目前僅 label 變 primary） | **`#2877EE`** | 已選項 |

> Hover 用 `rgba(15, 23, 42, 0.05)` 是 DS 規定，不是隨手寫的灰色。

#### Header（context menu 中分群標題）

`Atom / Context Menu Header`（高 32，含「Header」字標）：
- Label：Roboto Medium 14px / color `#0F172A`
- 與下一段之間用 1px `#D7DAE0`（`$flyout-border`）分隔

#### add 列（context menu 末尾 + Add 按鈕，可選）

當 DropdownList 支援「新增選項」時，context menu 最末加一列：
- 高 32px、border-top 1px `#D7DAE0`
- 含 Plus icon 16×16 色 `#2877EE` + label `#2877EE` Roboto Medium 14px
- 點擊觸發「新增此選項」流程
