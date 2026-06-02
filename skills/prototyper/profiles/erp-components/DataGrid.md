# DataGrid 結構與互動

> 屬於 `profiles/erp-transaction.md` 的延伸元件規格。
> 載入時機：實作該元件（或審查含該元件的 prototype）時依需求載入；不會隨 erp profile 自動載入。
>
> 上層 profile：`profiles/erp-transaction.md`
> 同層元件：`ListSearch.md` / `DataGrid.md` / `FormGroup.md` / `FormFooter.md`

---

List View 的表格本體；唯一的橫向 / 縱向捲軸來源。

## DOM Anatomy

```
.data-grid-block          ← 上邊框；包住 grid + pager 為一組
  └─ .grid-wrap           ← overflow: auto；唯一的捲軸來源
      └─ table.grid       ← width: max-content; min-width: 100%
  └─ .pager               ← 與 grid 相連，無上下額外邊框
```

## RWD 與寬度規則

- `table.grid` → `width: max-content; min-width: 100%`
  - 容器寬時：表格 = 容器 100%，欄位依 `min-width` 平均分配剩餘空間
  - 容器窄時：表格寬度 = 各欄 `min-width` 總和，`.grid-wrap` 顯示水平捲軸
- **不使用** `table-layout: fixed`（讓欄位可依內容自然撐開）
- 一般欄位 HTML **不要寫死** `style="width:..."`，由 CSS 統一控制

## 欄位 min-width

| 欄位類型 | min-width | width |
|---|---|---|
| 一般資料欄（`<th>` / `<td>`） | **200px** | auto |
| Checkbox 欄 `.col-check` | **50px** | 50px（固定，**禁**自適應） |
| 操作欄 `.col-actions`（1 顆按鈕） | **56px** | 56px（固定，套 `.col-actions--single`） |
| 操作欄 `.col-actions`（2 顆按鈕） | **96px** | 96px（固定，預設 `.col-actions`） |

> **操作欄寬度鎖定**：兩種寬度都用 `width / min-width / max-width + box-sizing: border-box` 三件套鎖死，避免 grid auto-fit 在窄視窗下擠壓按鈕。

## 欄位優先級與 RWD 顯示規則
> 採「優先級欄位 + 黏性欄位 + 橫向捲動」三層策略。重要欄位（勾選、代號、操作）永遠可見；次要欄位於窄視窗下隱藏；對應 §RWD 4 斷點 XL / L / M / S。

### 優先級分類

| 優先級 | 規則 | 典型欄位 |
|---|---|---|
| **P0** 必要 | 任何斷點皆顯示 | 勾選欄（sticky-left）、主鍵 / 代號（sticky-left）、名稱、狀態、操作欄（sticky-right） |
| **P1** 重要 | M 橫向捲動可見；S 隱藏 | 主要業務欄位（如「應收科目」「對應科目」等） |
| **P2** 次要 | M 橫向捲動可見；S 隱藏 | 補充業務欄位（如「銷貨價格表」「預設區域」等） |
| **P3** 輔助 | L 橫向捲動可見；M / S 隱藏 | 多公司 / 跨組織欄位（如「公司別」） |

### 實作要點

1. 低優先級欄位以 `data-prio="p1|p2|p3"` 標記，透過 `@media` 設定 `display: none`
2. 黏性欄位（勾選 / 代號 / 操作）採 `position: sticky`，背景色**必須不透明**（見 §資料列 → 斑馬紋），避免捲動時穿透
3. 欄位最小寬度遵守上表，避免文字折行；超出時以 `text-overflow: ellipsis` 截斷並提供 tooltip
4. 「設定」按鈕（toolbar 右側 `tune` icon）提供「自訂顯示欄位」開關，可覆寫斷點預設值並持久化至 `localStorage`

### 圖例

| 符號 | 意義 |
|---|---|
| ● | 顯示 |
| ↔ | 該斷點下顯示，但隨容器寬度進入橫向捲動 |
| — | 隱藏（`display: none`） |

> 各模組的具體欄位 → 優先級對照表，由該模組 prototype 製作時依本表分類，並在 chat handoff 附上「P0–P3 欄位分配」清單供 reviewer 對照。

## Sticky 凍結欄

- **左凍結**：`.sticky-left` → `position: sticky; left: <offset>`
  - 第 1 欄（checkbox）：`left: 0`
  - 第 2 欄（主鍵，如館別代號）：`left: 50px`（緊接 checkbox 寬度）
- **右凍結**：`.sticky-right` → `position: sticky; right: 0`（操作欄）
- z-index 階層：`thead th.sticky-*` = `3`；`tbody td.sticky-*` = `1`；一般 `thead th` = `2`
- **禁加 `box-shadow`** 強調凍結邊（會視覺破碎）

## 垂直捲軸 / 表頭凍結

- `.grid-wrap` 同時負責 X/Y 捲軸；`thead th` 用 `position: sticky; top: 0`
- 上下邊框由 `.data-grid-block` 的 `border-top` + `.pager` 一起處理；內部 `.grid-wrap` 自身 `border: 0`

## 表頭 `thead th` 樣式

- 高度 `45px`，padding `0 16px`
- 背景：5% Primary 疊白底 → `background: rgba(var(--color-sf-primary), .05)` on `var(--bg-surface-default)`
- 字級 14px / Medium / `color: var(--text-primary)`
- **無下邊框**
- 每欄右側用 `::after` 畫 `1px × 26px` 灰線分隔（`background: var(--border-strong)`）；**最後一欄不畫**
- 文字 `text-align: left`（**含「狀態」欄**）

## 資料列 `tbody td` 樣式

- 高度 `50px`，padding `0 16px`
- 字級 14px / `color: var(--text-primary)`
- 列間 `border-bottom: 1px solid var(--border-default)`
- 最後一列 `tr:last-child td { border-bottom: none }`（避免與外層 `.data-grid-block` 的下邊框疊出兩條）
- **斑馬紋**：
  - **奇數列**：純白 `#fff`（= `var(--bg-surface-default)`）
  - **偶數列・一般欄（非 sticky）**：`rgba(15, 23, 42, .04)` — 淺岩石色 4% 疊白（semi-transparent，視覺層次靠透明度疊出）
  - **偶數列・Sticky 左欄（勾選 / 主鍵）/ Sticky 右欄（操作）**：`rgb(245, 246, 248)`（= `#F5F6F8` 實色）— **必須不透明**，否則捲動時會穿透看到下層內容
  - 表頭背景同表頭值（5% Primary 疊白，見 §表頭 `thead th` 樣式）

  > 一般欄用半透明、sticky 欄用實色：兩者視覺接近（疊白後皆為 ≈ `#F5F6F8`），但 sticky 必須是 solid 才能正確遮蔽下層。app.css 已配置；token 對應由 DS 內部命名（`--bg-surface-variant` ≈ `#F5F6F8`）。

## 特殊資料型態

- **主鍵 / 代碼欄** `.code-cell`：`font-family: var(--font-family-mono); font-feature-settings: 'tnum'; font-weight: 500`
- **可點擊連結** `.link`：`color: rgb(var(--color-sf-primary)); font-weight: 500`；hover 加底線
- **次要資訊** `.text-secondary`（如電話）：`color: var(--text-secondary)` + `font-family: var(--font-family-mono)`
- **空值顯示**：一律 `—`（em dash），**禁**用「無」「N/A」
- **關聯欄位顯示**：只顯示**名稱**，**不顯示代碼前綴**（如「北區」而非「N · 北區」）

## 狀態 Chip `.st-chip`

- 高度 `28px`、`border-radius: var(--radius-full)`、padding `0 8px 0 6px`、`min-width: 49px`
- 字 12px / Medium / line-height 1.3 / letter-spacing 0.1px
- `.st-chip--active`：背景 `rgba(var(--color-sf-success), .12)`、邊框 + 文字 `rgb(var(--color-sf-success))`
- `.st-chip--inactive`：背景 `rgba(var(--color-sf-error), .12)`、邊框 + 文字 `rgb(var(--color-sf-error))`
- chip 在 cell 中**靠左**（隨欄位 `text-align: left`），不置中

## 操作按鈕 `.ico-btn`

- `40 × 40px`、icon `20px`、`border-radius: var(--radius-sm)`
- 預設背景透明
- Hover：`background: rgba(var(--color-sf-primary), .08)`
- `.is-edit` / `.is-view`（唯讀模式切 chevron）共用同一規格

## 互動規則

| 互動 | 行為 |
|---|---|
| 表頭 checkbox | 部分選取 → `indeterminate`；全選 / 全不選切換 |
| 列 checkbox | 勾選後該列加 `.is-selected` |
| 列 hover | 整列背景，sticky 欄補實色（見下方優先級表） |
| 列點擊 | **禁**整列可點（避免誤觸）。進詳細只有兩個入口：① 主鍵欄 `.link`、② 操作欄按鈕 |
| 唯讀模式 | 操作欄按鈕從鉛筆（`.is-edit`）切換成右箭頭 chevron（`.is-view`），title 改「檢視」；**禁直接隱藏按鈕**，保留入口 |
| 橫向捲動 | 左 / 右凍結欄保持可見；Sticky 欄一定要明確指定 `background` |
| 進入「批次模式」 | 由父層判斷 `selectedIds.length > 0` 切換 toolbar，與表格本身解耦 |

### 互動狀態優先級（由淺到深，覆蓋斑馬紋）

**預設斑馬紋  <  hover  <  selected  <  selected + hover**

| 狀態 | 普通 cell（非 sticky） | Sticky cell（必須實色） |
|---|---|---|
| Hover | `rgba(var(--color-sf-primary), .06)` | `rgb(232, 238, 252)` |
| Selected (`.is-selected`) | `rgba(var(--color-sf-primary), .10)` | `rgb(229, 235, 251)` |
| Selected + Hover | 取 selected + hover 疊加值（最深） | 取對應疊加實色 |

> **為什麼 sticky 必須補實色？** Sticky cell 會浮在下方滾動內容之上；若用 `rgba()` 透明色，scroll 時下層 cell 會穿透顯現，破壞 hover/selected 的視覺反饋。每個互動狀態都必須有對應的「疊白後固體色」配套。
> **禁**改用不同色相讓 sticky 欄看起來是獨立區塊；色相要與一般 cell 一致，只是 alpha 換實色。

## 空狀態

無資料時隱藏整個 `.data-grid-block`，改顯示 `.empty-state`（48px `material-symbols:inbox-outline` icon + 標題 + 說明）；**禁**保留空表頭。

---

## 行內編輯（Inline Editing）

> 適用情境：欄位數少（≤ 6）、修改快速、需保留同列／鄰列比對視角的明細列編輯。複雜分支（如 formula 計算）仍走 Drawer，詳本節末 §與 Drawer 模式取捨。

### 互動規則

#### 1. 進入編輯（Enter Edit Mode）

| 觸發方式 | 行為 |
|---|---|
| 點擊資料列右側 ✏️「編輯」icon | 該列整列進入編輯態（row-level inline edit），所有可編輯欄位同時變為輸入控制元件，焦點落在第一個可編輯欄 |
| 點擊「＋ 新增規則」 | 在 `tbody` 最末插入一空白編輯列，預設焦點落在第一個必填下拉 |
| 鍵盤 `Enter` 於選取列 | 等同點擊編輯 icon |
| 雙擊某儲存格（選用，進階模式） | 進入該列編輯態，焦點直接落在被雙擊的欄位 |

**互斥規則**：同一時間僅允許**一列**處於編輯態。嘗試編輯第二列時：

- 當前列「未變更」→ 直接切換
- 當前列「已變更」→ 彈 `modal--warning`：「尚有未儲存變更，是否放棄？」

#### 2. 編輯中（While Editing）

- **整列底色**：`background: rgb(var(--color-sf-primary-container) / 0.4)`（淡藍）以視覺區分
- **行高擴張**：由 readonly 的 `40px` 提升至 `56px`，提供足夠的輸入控制元件呼吸空間
- **右側動作欄替換**：✏️ ＋ 🗑 換成「✓ 確認」「✕ 取消」兩顆 icon button
- **其他列鎖定**：非編輯列的 hover、點擊、勾選 checkbox 一律無反應（透明灰幕 `rgba(0,0,0,0.02)`、`cursor: not-allowed`）
- **Footer 主要按鈕鎖定**：頁面底部「儲存變更」按鈕 `disabled`，tooltip：「請先完成列編輯」
- **頁面切換鎖定**：上 ／ 下一筆紀錄、麵包屑、Nav rail 均鎖定，離開前彈確認 modal

#### 3. 欄位聯動（Field Cascade）

以「銷貨價格表」的範例規則為示意；模組實作時依該模組欄位語意調整：

| 主欄位 | 變更後副作用 |
|---|---|
| `適用範圍 → 全部產品` | `適用項目` 欄置灰、值清空並顯示「全部產品」 |
| `適用範圍 → 產品類別 / 產品 / 變體` | `適用項目` 欄重新載入對應選項清單；原值若不在新清單則清空並紅框提示「請重新選擇」 |
| `計算方式 → 固定價格` | 顯示「固定價格」欄、隱藏「折扣百分比」；前者必填 |
| `計算方式 → 折扣` | 顯示「折扣百分比」欄（單位 `%`、`min=0`、`max=100`），隱藏「固定價格」 |
| `最小數量` | 接受 0–9999 之整數；輸入非整數時自動四捨五入；負值阻擋 |
| `有效期` | 起訖必須成對。僅填一邊時於離開欄自動填補（起 = `today`、迄 = `2099/12/31`）並顯示 info toast |

#### 4. 驗證（Validation）

- **即時驗證**：欄位失焦（`@blur`）時驗證；錯誤欄位加 `.is-error`，下方 `8px` helper 顯示紅字
- **儲存時驗證**：點「✓ 確認」時整列重驗，第一個錯誤欄自動 focus 並 scroll 到視野內
- **錯誤訊息**：採用既有 `fieldErrors` 字串樣板，例：
  - 「適用項目」為必填欄位
  - 「固定價格」須 > 0
  - 生效日不可晚於失效日

#### 5. 確認與取消

| 動作 | 行為 |
|---|---|
| 點 ✓ 或按 `Enter`（非 textarea 焦點下） | 通過驗證 → 寫回 `form.items[idx]` → 列收合為 readonly → 顯示 success toast「規則已更新」 |
| 點 ✕ 或按 `Esc` | 「新增中且未填」→ 直接移除；「有變更」→ 彈確認 modal；「無變更」→ 直接收合 |
| 點 `Tab` 於最後一欄 | 焦點循環回該列第一欄；不離開列 |
| 點列外空白 | 視同 `Esc`（按未變更 ／ 已變更分支處理） |

#### 6. 鍵盤捷徑（Keyboard）

| 按鍵 | 動作 |
|---|---|
| `Enter` | 確認列編輯（同 ✓） |
| `Esc` | 取消列編輯（同 ✕） |
| `Tab` / `Shift+Tab` | 列內前後欄位移動，迴圈不外溢 |
| `Ctrl/⌘ + Enter` | 確認並繼續新增下一列 |

### 樣式規範

> 所有 token 沿用 `design-tokens-athena.css` / `colors_and_type.css` 的語意別名，**不額外造新色**。

#### 1. Row 結構

| 狀態 | 高度 | 背景 | 上下框線 |
|---|---|---|---|
| Readonly | `40px` | `transparent` | `1px solid var(--border-default)`（底） |
| Hover | `40px` | `rgba(40,119,238,0.04)` | 同 readonly |
| Editing | `56px` | `rgba(40,119,238,0.08)` | 上下皆 `1px solid var(--color-sf-primary)`（外框感） |
| Error（編輯態 + 任一欄錯） | `56px` | `rgba(217,48,37,0.06)` | 上下皆 `1px solid var(--text-error)` |
| Disabled（他列鎖定） | `40px` | `rgba(0,0,0,0.02)` | 同 readonly；`cursor: not-allowed` |

#### 2. Cell 內控制元件

`input.input`、`select.input`（沿用既有）：

```
height           : 36px
padding          : 0 12px
border           : 1px solid var(--border-default)
border-radius    : var(--radius-xs)              /* 4px */
font-size        : var(--font-size-md)           /* 14px */
background       : var(--bg-surface-default)

:focus
  border         : 1px solid var(--border-focus) /* #2877EE */
  box-shadow     : var(--shadow-focus-ring)      /* 0 0 0 4px rgba(40,119,238,0.16) */

.is-error
  border         : 1px solid var(--text-error)
  box-shadow     : 0 0 0 4px rgba(217,48,37,0.12)
```

- **數值欄**（最小數量 / 固定價格 / 折扣 %）：`text-align: right`，後綴單位（`%` ／ 幣別 code）用 `<span class="muted">`，`14px`、`color: var(--text-secondary)`
- **下拉**：沿用既有 `.dropdown` + `.caret`，caret 圖示 `material-symbols:expand-more`
- **日期區間**：沿用 drawer 內的 `.date-range__trigger`，按鈕高 `36px`，點擊展開 `.date-range__pop`（雙月曆）
- **欄位間距**：cell `padding: 8px 12px`；同 cell 內多控制元件 `gap: 8px`

#### 3. 列內動作區（Action Cell）

| 狀態 | 顯示 |
|---|---|
| Readonly | ✏️ edit（`24px`, `.is-edit`）＋ 🗑 delete（`24px`, `.is-delete`） |
| Editing | ✓ check 主色實心 ＋ ✕ close 灰邊框 |
| Loading（儲存中） | `progress_activity` 旋轉，主色 |

`.ico-btn`（sizes & colors 已於本檔 §操作按鈕 `.ico-btn` 定義；以下為編輯態擴充）：

```
.is-confirm  → background: var(--bg-primary); color: #fff;
.is-cancel   → background: transparent; color: var(--text-secondary);
                border: 1px solid var(--border-default);
```

兩顆 icon button 間距 `gap: 4px`。

#### 4. Helper / Error 提示

- 緊貼 cell 下方，**不撐高列高**：採 `position: absolute; top: 100%; left: 12px` 浮出（單行省略，超出顯示 tooltip）
- 字級 `12px (--font-size-xs)`、顏色 `var(--text-error)`、`line-height: 1.4`
- 出現方向：預設往下；若被表格邊界裁切則翻轉往上

#### 5. 新增列（Empty / Insert Row）

- 列高 `56px`、背景 `var(--bg-surface-variant)`（淺灰）區隔
- 左側出現 ＋ icon 微動畫（`scale 0.8→1`，`0.2s ease-out`）
- 確認後背景淡入正常編輯態色；取消則整列以 `height: 0` collapse 移除（`0.2s`）

#### 6. 視覺動畫（Motion）

| 場景 | 動畫 |
|---|---|
| 進入編輯態 | 列高 `40 → 56px`、底色 fade-in，`transition: 180ms ease-out` |
| 離開編輯態 | 反向，`180ms ease-in` |
| 確認成功 | 列底色閃綠 `rgba(46,160,67,0.16) → 透明`，`600ms` |
| 驗證錯誤 | 錯誤欄 shake 動畫（`±4px`，3 次，`280ms`） |

#### 7. RWD 與寬度

- 列內控制元件 `min-width: 120px`；表格 horizontal scroll 沿用 `.dg-rules-scroll`
- `≥ 1280px` 正常顯示
- `< 1280px` 自動橫向滾動，**編輯列右側動作欄 sticky**（`position: sticky; right: 0`），避免操作鈕被裁切

#### 8. 無障礙（A11y）

- 編輯列 `role="row"` + `aria-rowindex`，編輯態加 `aria-busy="true"`、`aria-label="編輯中：<規則名稱>"`
- 每個輸入控制元件保留 `<label>` 對應原欄頭，視覺上以 `sr-only` 隱藏，避免重複佔位
- `Esc` / `Enter` 鍵盤焦點不可逃出該列（**focus-trap within row**）
- 動畫尊重 `prefers-reduced-motion: reduce` → 全部改為 `0ms`

### 與 Drawer 模式取捨

| 維度 | 行內編輯 | Drawer 編輯（現行） |
|---|---|---|
| 適用情境 | 欄位少（≤ 6）、修改快速、批量微調 | 欄位多、含進階參數（formula、捨入等） |
| 上下文 | 保留同列 ／ 鄰列比對視角 | 全屏專注，隱藏列上下文 |
| 操作步數 | 1 步入編輯、`Enter` 存檔 | 2 步（開抽屜 → 按鈕） |
| 建議 | 模組欄位 ≤ 5 且分支簡單時，可取代 80% Drawer 開啟 | 保留「更多操作」按鈕，作為複雜分支（如 formula 模式）的進階入口 |

**落地策略**：先行內編輯；當該列觸發複雜分支（如 `compute_price === 'formula'`）時於該列顯示「📋 進階」icon，自動降級回 Drawer，避免欄位過多塞爆列高。

---

## 進銷存明細 DataGrid（交易明細擴充）

> 適用進銷存作業檔（如請購單 / 採購單 / 銷貨單）Form View 內的明細 grid。搭配 `templates/psi-transaction-page.html`。交易明細與設定檔列表 / 上方 §行內編輯 的差異點集中於本節。

### 與前述行內編輯的差異

| 維度 | 前述（設定檔式快速編輯） | 交易明細（本節） |
|---|---|---|
| 欄位數 | ≤ 6 才建議 inline；多則走 Drawer | **可超過 6 欄仍維持 inline**（採購 / 銷貨明細天生寬表）；以水平捲動 + 操作欄 sticky-right 處理，**不**降級 Drawer |
| 載體 | `.grid-wrap` | `.dg-lines > .dg-lines__scroll`（Form View 內，與 List 的 `.dg__scroll` 同理，唯一橫向捲軸） |
| 新增列入口 | 「＋ 新增規則」文字列 | 表頭右側 `material-symbols:add-circle`（實心）icon（`ico-btn is-add-circle`）；button 用 **flat primary** 樣式（icon primary 色、**無底色、無邊框**，非一般 icon button 的 primary 填底） |

### Computed / related 欄位（唯讀，不可編）

交易明細常見「選商品 → 自動帶入多欄」與「即時算小計」：

| 欄位 | 規則 | 顯示 |
|---|---|---|
| 商品編號 / 商品名稱 | related，選商品後自動帶入 | `dim` 淡化色，readonly |
| 採購單位 / 上次進價 / 庫存 | 選商品時 onchange 帶入（單位可改、其餘唯讀） | 唯讀欄 `dim` |
| **預估小計 / 金額** | computed = `數量 × 單價`（即時，隨數量 / 單價變動重算） | readonly，`num` 右對齊；**不**做成可編輯欄 |

- 即時算式：數量或單價變動時 `金額` 立即更新（範本以 `{{ (qty || 0) * (price || 0) }}` 行內示意；production 由 computed 欄落地）
- 數值欄一律 `class="num"` 右對齊；千分位顯示；空值 `—`
- 編輯態的數值 input 也加 `.num`（右對齊），與顯示態一致

### 結轉後鎖列（carry-forward lock）

進銷存明細結轉下游單據後，該列不可再改（避免來源與下游不一致）：

- 判斷依據：明細列的下游關聯鍵存在（如 `po_line_id != null`）
- 視覺：列加 `is-row-locked`；操作欄的 `edit` / `delete` **改為單一 `material-symbols:lock-outline` icon**（`dim`，tooltip「已結轉，不可修改」）
- 「採購單號」等 related 欄：結轉後回填單號；尚未結轉顯示 `—`（`dim`）
- 與權限正交：即使有 `canEditLines`，已鎖列仍不可改

### 合計列（tfoot）

- 只 Σ 金額 / 預估小計欄，其餘欄空白；`form.lines.length > 0` 才渲染
- 該 Σ 同步為 Summary Card 的單指標（詳 `SummaryCard.md` Layout B「預估總金額」）；兩處數字必一致
