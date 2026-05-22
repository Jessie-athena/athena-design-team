# Form Group 分群與 form-grid RWD

> 屬於 `profiles/erp-transaction.md` 的延伸元件規格。
> 載入時機：實作該元件（或審查含該元件的 prototype）時依需求載入；不會隨 erp profile 自動載入。
>
> 上層 profile：`profiles/erp-transaction.md`
> 同層元件：`ListSearch.md` / `DataGrid.md` / `FormGroup.md` / `FormFooter.md`

---

Form View 主體由多個 Group（`.form-section`）組成，每個 Group 內以 `.form-grid` 排版欄位。本節規範分群結構、grid RWD、輸入欄狀態與 group header 視覺；輸入欄樣式的「Filled vs Outlined」、「readonly vs disabled」原則見 `profiles/erp-transaction.md §輸入欄樣式`，本節不重述。

## Anatomy

```
.form-section
├─ .form-section__bar           ← 群組標題列（5×21 藍直條 + 14px Bold Primary 標題）
└─ .form-section__body
    └─ .form-grid               ← Grid 容器（決定 RWD 行為）
        └─ .form-field          ← 單一欄位（label + input + help）
            ├─ label            ← 13px Medium，必填 * 紅色
            ├─ input.filled     ← 40px 高、padding 0 10px
            └─ .help（.is-error）  ← min-height 16px（永遠佔位，避免錯誤訊息出現時欄位位移）
```

## Sizing & Spacing

| 元素 | 規格 |
|---|---|
| Section 之間垂直 gap | `24px` |
| 群組標題到內容 padding | `padding-bottom: 16px`（標題列） |
| Grid row / column gap | `gap: 16px` |
| Field 內 label → input | `gap: 6px` |
| Field 內 input → help | `.help { min-height: 16px }`（永遠保留佔位） |
| Input 高度 | `40px` |
| Input 內距 | 左右 `padding: 0 10px`；select 右側 `padding-right: 36px`（讓出 caret） |
| Textarea 預設高 | `min-height: 100px`，`resize: vertical` |

## form-grid 變體與 RWD

**核心原則：用 `minmax(300px, 1fr)` + `auto-fit` 自動換行；≤ 1024px 強制 2 欄。最小欄位寬度 300px。**

| Grid 變體 | template-columns | 適用 |
|---|---|---|
| `.form-grid`（預設） | `repeat(auto-fit, minmax(300px, 1fr))` | 一般 3~4 欄並列（基本資料） |
| `.form-grid--2` | `repeat(auto-fit, minmax(300px, 1fr))` | 地址 / 聯絡（配合 `--full` 跨欄） |
| `.form-grid--1` | `minmax(300px, 1fr)` | 單欄區塊（備註 / textarea） |

## 跨欄 modifier

- `.form-field--span-2` / `.form-field--span-3` — 桌機寬度下跨 2 / 3 欄
- `.form-field--full` — `grid-column: 1 / -1`，**永遠**整列

## 斷點對照表（對應 `REFERENCE.md §8` 四斷點）

| 代號 | 範圍 | 自動欄數 | padding | 跨欄 modifier 表現 |
|---|---|---|---|---|
| **XL** | ≥ 1440px | 4 欄（餘額空間平分） | `32px` 左右內距 | `--span-3` 維持 3 欄 |
| **L** | 1280–1439px | 4 欄（緊縮） | `24~32px` | `--span-3` 維持 3 欄 |
| **M** | 1024–1279px | 3 欄（auto-fit 自然收斂） | `24px` | `--span-3` → 降為 2 欄 |
| **S** | 768–1023px | **強制 2 欄** | **`20px`** | 所有 `--span` 皆變 2 欄 |
| — | < 768px | 不支援 | — | — |

### 為何 ≤ 1024 強制 2 欄而非繼續 auto-fit？

在 ~1000px 時 auto-fit 仍可能給 3 欄但每欄擠到 300px 邊界，標籤易折行；強制 2 欄能維持較寬欄位與更易讀的標籤。

```css
@media (max-width: 1024px) {
  .form-grid       { grid-template-columns: repeat(2, minmax(0, 1fr)); }
  .form-view__body { padding: 20px; }
}
```

## 複合欄位 `.field-phone`（含國碼 + 號碼）

- 容器 `.field-phone` → `display: flex; gap: 8px; min-width: 300px`
- 國碼 select → `width: 110px; flex: 0 0 110px`
- 號碼 input → `flex: 1 1 auto; min-width: 0`（避免 flex item 撐爆）
- 當父 grid 縮到 1 欄時，**複合欄位內部仍維持並排**，不再分行

## `.input.filled` 各狀態
| 狀態 | 視覺 |
|---|---|
| **Default** | 底色 `var(--bg-surface-variant)` (`#EDF0F7`)、底線 `1px solid var(--border-strong)` (`#7F8996`)、`border-radius: 4px 4px 0 0`（**僅上方圓角**，Material Filled 簽名特徵） |
| **Hover** | 底線顏色不變，僅 `cursor: text` |
| **Focus** | **底線 `2px solid rgb(var(--color-sf-primary))`**，**禁加 outline ring**（焦點靠底線變色表達） |
| **Filled**（有值） | 同 default |
| **Error**（`.is-error`） | 底線 `2px solid rgb(var(--color-sf-error))`；下方 `.help.is-error` 同色顯示訊息 |
| **Readonly** | 背景透明、底線 `1px solid var(--border-default)`、文字 `var(--text-primary)`；`cursor: default`；**不觸發** focus 樣式 |
| **Disabled** | 同 readonly；select 額外隱藏 caret 圖示 |

## `.input.filled.is-select`

- 用原生 `<select>` + `appearance: none` + 自繪 caret（右側 8px 處 `16×16` SVG triangle）
- 右側 `padding-right: 36px` 預留 caret 空間
- Hover → `cursor: pointer`
- Focus 行為與 text input 一致（2px Primary 底線）
- Disabled / Readonly → caret 隱藏、`padding-right` 收回 `10px`、`cursor: default`

## Textarea

- `min-height: 100px`、`resize: vertical`
- padding 改為 `10px` 四邊（承載多行）
- Readonly：背景透明，與 input 一致

## Group Header 樣式

```
.form-section__bar              高度自動，padding 0 0 16px 0
├─ ::before                     5×21 直條，rgb(var(--color-sf-primary))，margin-right 10px
└─ h2.form-section__title       font-size: 14px / font-weight: 700 / color: rgb(var(--color-sf-primary))
```

**規則**：

- 群組標題**不**加底線、**不**加背景
- **不**放任何輔助文字 / icon 在標題列右側（保持極簡）
- Group 之間以 `gap: 24px` 區隔，**不**用分隔線

## 欄位排列原則（撰寫表單時）

1. **同類欄位放同一 Section**，每個 Section 標題用 4~6 字描述（如「基本資料」「聯絡資訊」「補充說明」）
2. **必填欄位優先**放在 Section 前段
3. **代號 / 狀態類短欄位**放第一行右側，**地址 / 備註類長欄位**用 `--full` 整列
4. **每行不超過 4 欄**，視覺上一掃即看完一列
5. 若一列只剩 1~2 欄，補上空白 `<div class="form-field"></div>` 維持 grid 對齊
6. Help text 高度**永遠保留 `min-height: 16px`**，避免錯誤訊息出現時欄位位移

## Readonly / Archived 全域樣式

整張表單為唯讀（`.is-readonly-view`）或停用後檢視（`.is-archived-view`）時，所有 `.input` 變透明背景 + `1px solid var(--border-default)` 底線 + `var(--text-secondary)` 文字色 + `pointer-events: none`；select caret 隱藏。例外規則與切換時機**詳見 `profiles/erp-setup.md §設定檔資料狀態矩陣`**。

## A11y

- `<label>` 必須與 input 關聯（同 `.form-field` 內，建議用 `for` + `id` 或包覆方式）
- 必填用 `<span class="required">*</span>` 視覺標記 **且** input 加 `required` 屬性
- Error 訊息與欄位用 `aria-describedby` 連結（reviewer 用 keyboard reader 時可讀到錯誤）
- Tab 順序依 DOM 順序（grid 不改變 tab order）
- Select 支援鍵盤輸入首字快速跳選（原生行為，**不要 JS 攔截**）
