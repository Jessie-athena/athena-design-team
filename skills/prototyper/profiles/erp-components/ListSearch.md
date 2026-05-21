# List 搜尋區結構與互動

> 屬於 `profiles/erp-transaction.md` 的延伸元件規格。
> 載入時機：實作該元件（或審查含該元件的 prototype）時依需求載入；不會隨 erp profile 自動載入。
>
> 上層 profile：`profiles/erp-transaction.md`
> 同層元件：`ListSearch.md` / `DataGrid.md` / `FormGroup.md` / `FormFooter.md`

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
   └─ .btn-icon.is-outline        (收合 / 展開 ▲▼ — 條件顯示)
```

## Token 與尺寸

| 項目 | 值 |
|---|---|
| 卡片背景 | `var(--bg-surface-default)` |
| 卡片邊框 | `1px solid var(--border-default)` |
| 卡片圓角 | `var(--radius-lg)` |
| 卡片內距（展開） | `16px` 四邊 |
| 卡片內距（收合） | `12px 16px` |
| 欄位區與操作區水平 gap | `16px` |
| 欄位之間 gap（展開） | `12px 16px`（垂直 12 / 水平 16） |
| 欄位之間 gap（收合） | `8px` |
| 欄位 min-width | **150px** |
| 欄位高度（展開，含 label） | `62px`（label 18 + gap 4 + input 40） |
| 欄位高度（收合，僅 input） | `40px` |
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

> 對應 `REFERENCE.md §10` 的 XL / L / M / S 四斷點。Search Bar 採「Flex Wrap → 自動收合」兩階段策略，避免水平捲動。

| 斷點 | 欄位排列 | 欄位 flex-basis | 收合行為 |
|---|---|---|---|
| **XL ≥ 1440** | 單列 5 欄 | `flex: 1 1 180px` | 不收合 |
| **L 1280–1439** | 單列 5 欄 | `flex: 1 1 160px` | 不收合 |
| **M 1024–1279** | 2 列換行（3+2 或 4+1） | `flex: 1 1 200px` | 偵測到換行 → 顯示「收合」按鈕 |
| **S 768–1023** | **預設收合**，依寬度動態顯示 N 個欄位（至少 1 個） | 固定 `150px` | 預設 `.is-collapsed = true`；點「展開」可顯示全部 |

> S 斷點下進入頁面時 **預設 `searchCollapsed = true`**，避免擠壓主要表格區域。

## RWD 換行觸發條件

換行於以下任一條件成立時發生：

1. **欄位數量 > 5**（預設 5 個 filter；新增第 6 個會直接掉到下一行）
2. **外層容器寬度不足**：依當前斷點的 flex-basis 計算容納寬度；容器寬度低於該臨界值 → 最右側欄位優先換行
3. **S 斷點預設收合**：進入頁面時直接套 `.is-collapsed`，跳過「先展開再收合」的中間狀態

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
- 欄位 → `flex: 0 0 150px; height: 40px`；**label 隱藏**（`display: none`），只顯示 input
- 容納邏輯（即時計算）：

  ```
  available = barInnerWidth - actionsWidth - fieldsPaddingRight
  visibleCount = floor((available + gap) / (fieldWidth + gap))
  ```

- 第 N 個欄位 `v-show="!searchCollapsed || collapsedVisibleCount > N-1"`
- **容納不下的欄位完全隱藏**（`display: none`，**不切半、不模糊邊緣**）
- 容器寬度變化時 `ResizeObserver` 即時重算
- 操作區 → `align-self: center`（垂直置中）

## 收合按鈕 icon / title / aria-label 切換

| 屬性 | `searchCollapsed = false`（可收合） | `searchCollapsed = true`（已收合） |
|---|---|---|
| icon | `keyboard_arrow_up` ▲ | `keyboard_arrow_down` ▼ |
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

[C] 收合 → 單行；左側保留容納得下的欄位（無 label），右側 3 顆按鈕
┌──────────────────────────────────────────────────────────────────┐
│ [TP01]   [輸入名稱關鍵字]  [全部 ▾]              🔍 🗑 ▼        │
└──────────────────────────────────────────────────────────────────┘
```

## 互動行為

| 互動 | 行為 |
|---|---|
| 任一輸入框按 `Enter` | 觸發搜尋（`onSearch`） |
| 點 🔍 搜尋 | 套用所有 filter，頁碼重設為 1 |
| 點 🗑 清除 | 清空所有 filter 並重新查詢 |
| 點 ▲ 收合 | `searchCollapsed = true`，卡片變單行 |
| 點 ▼ 展開 | `searchCollapsed = false`，恢復多行展開 |
| 視窗 resize | ResizeObserver 自動重算 `searchWrapped` 與 `collapsedVisibleCount` |

## 設計原則（給 reviewer 引用）

1. **左欄位、右操作** 的二段式版面，操作區永不被擠壓、永不換行
2. **欄位最小寬 150px**，是 RWD 換行的唯一觸發條件
3. **操作按鈕數量隨狀態自動調整**（2 ↔ 3），收合按鈕僅在需要時出現
4. **收合不是「完全隱藏」**，而是把可容納的欄位保留在原處（單行、無 label），常用 filter 仍可立即使用
5. **不顯示半個欄位** — 容納不下就完全隱藏，避免視覺破碎
6. 所有換行 / 容納計算均**即時動態，依容器寬度**，不依視窗寬度（適用側邊欄收合、嵌入面板等場景）
