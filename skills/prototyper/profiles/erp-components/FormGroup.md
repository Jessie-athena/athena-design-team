# Form Group 分群與 form-grid RWD

> 屬於 `profiles/erp-transaction.md` 的延伸元件規格。
> 載入時機：隨 ERP profile **自動載入**（無論作業檔 / 設定檔；由 `SKILL.md §支援檔案` 規定——該處為載入規則的**單一來源**）。
>
> 上層 profile：`profiles/erp-transaction.md`
> 同層元件：`ListSearch.md` / `DataGrid.md` / `FormGroup.md` / `FormFooter.md` / `SummaryCard.md` / `Stepper.md` / `Permissions.md` / `RelBanner.md` / `Skeleton.md`
>
> **值權威＝`../../assets/app.css`（canonical CSS，產出時複製不重寫）**——所有尺寸 / 色 / 圓角字面值以此為準；本檔**不複印 CSS 數值**（要改數值改 app.css）。本檔為**用法權威**：class 套用、grid RWD 規則、欄位狀態行為、markup 結構。遇具體 px/hex 一律指 app.css，避免兩處漂移。

---

Form View 主體由多個 Group（`.form-section`）組成，每個 Group 內以 `.form-grid` 排版欄位。本節規範分群結構、grid RWD、輸入欄狀態與 group header 視覺；輸入欄樣式的「Filled vs Outlined」、「readonly vs disabled」原則見 `profiles/erp-transaction.md §輸入欄樣式`，本節不重述。

## Anatomy

```
.form-section
├─ .form-section__bar           ← 群組標題列（藍直條 + Bold Primary 標題；尺寸 / 色見 app.css）
└─ .form-section__body
    └─ .form-grid               ← Grid 容器（決定 RWD 行為）
        └─ .form-field          ← 單一欄位（label + input + help）
            ├─ label            ← Medium，必填 * 紅色（尺寸見 app.css）
            ├─ input.filled     ← Filled 輸入欄（高度 / 內距見 app.css）
            └─ .help（.is-error）  ← 永遠佔位（保留最小高度，避免錯誤訊息出現時欄位位移；值見 app.css）
```

## Sizing & Spacing（規則；尺寸值見 app.css）

| 元素 | 規則 |
|---|---|
| Section 之間垂直 gap | 固定垂直間距區隔（值見 app.css `.form-section`） |
| 群組標題到內容 padding | 標題列下方留白（值見 app.css `.form-section__bar`） |
| Grid row / column gap | row / column 等距（值見 app.css `.form-grid`） |
| Field 內 label → input | label 與 input 留小 gap（值見 app.css） |
| Field 內 input → help | `.help` 永遠保留佔位（最小高度，避免位移；值見 app.css） |
| Input 高度 | 固定行高輸入欄（值見 app.css） |
| Input 內距 | 左右內距；select 右側額外讓出 caret 空間（值見 app.css） |
| Textarea 預設高 | 有最小高、`resize: vertical`（值見 app.css `.input--textarea`） |

## form-grid 變體與 RWD

**核心原則：用 `auto-fit` + 最小欄位寬度自動換行；≤ 1024px 強制 2 欄。最小欄位寬度為 300px（此門檻為 RWD 規則，實際 `grid-template-columns` 值見 app.css `.form-grid`）。**

| Grid 變體 | 行為 | 適用 |
|---|---|---|
| `.form-grid`（預設） | auto-fit 多欄並列 | 一般 3~4 欄並列（基本資料） |
| `.form-grid--2` | auto-fit 多欄（配合 `--full` 跨欄） | 地址 / 聯絡 |
| `.form-grid--1` | 單欄 | 單欄區塊（備註 / textarea） |

## 跨欄 modifier

- `.form-field--span-2` / `.form-field--span-3` — 桌機寬度下跨 2 / 3 欄
- `.form-field--full` — `grid-column: 1 / -1`，**永遠**整列

## 斷點對照表（對應 `REFERENCE.md §8` 四斷點）

> 斷點門檻（1440 / 1280 / 1024 / 768）為**文件化 RWD 規則**；各斷點 padding 等實際值見 app.css。

| 代號 | 範圍 | 自動欄數 | padding | 跨欄 modifier 表現 |
|---|---|---|---|---|
| **XL** | ≥ 1440px | 4 欄（餘額空間平分） | 較寬左右內距（值見 app.css） | `--span-3` 維持 3 欄 |
| **L** | 1280–1439px | 4 欄（緊縮） | 略收內距（值見 app.css） | `--span-3` 維持 3 欄 |
| **M** | 1024–1279px | 3 欄（auto-fit 自然收斂） | 中內距（值見 app.css） | `--span-3` → 降為 2 欄 |
| **S** | 768–1023px | **強制 2 欄** | 較窄內距（值見 app.css） | 所有 `--span` 皆變 2 欄 |
| — | < 768px | 不支援 | — | — |

### 為何 ≤ 1024 強制 2 欄而非繼續 auto-fit？

在 ~1000px 時 auto-fit 仍可能給 3 欄但每欄擠到最小寬度邊界，標籤易折行；強制 2 欄能維持較寬欄位與更易讀的標籤。

> 實作：`@media (max-width: 1024px)` 將 `.form-grid` 改為固定 2 欄、收窄 `.form-view__body` padding（規則為 4 欄→2 欄；template-columns / padding 值見 app.css）。

## 複合欄位 `.field-phone`（含國碼 + 號碼）

- 容器 `.field-phone` → `display: flex`，國碼與號碼並排（gap / min-width 值見 app.css）
- 國碼 select → 固定窄寬不縮（`flex: 0 0 …`；寬度值見 app.css）
- 號碼 input → `flex: 1 1 auto; min-width: 0`（吃滿剩餘空間，避免 flex item 撐爆）
- 當父 grid 縮到 1 欄時，**複合欄位內部仍維持並排**，不再分行

## `.input.filled` 各狀態（行為規則；尺寸 / 色值見 app.css）
| 狀態 | 視覺 |
|---|---|
| **Default** | 底色 `var(--bg-surface-variant)`、底線 `var(--border-strong)`、**僅上方圓角**（Material Filled 簽名特徵） |
| **Hover** | 底線顏色不變，僅 `cursor: text` |
| **Focus** | 底線加粗轉 `rgb(var(--color-sf-primary))`，**禁加 outline ring**（焦點靠底線變色表達） |
| **Filled**（有值） | 同 default |
| **Error**（`.is-error`） | 底線轉 `rgb(var(--color-sf-error))`；下方 `.help.is-error` 同色顯示訊息 |
| **Readonly** | 背景透明、底線 `var(--border-default)`、文字 `var(--text-primary)`；`cursor: default`；**不觸發** focus 樣式 |
| **Disabled** | 同 readonly；select 額外隱藏 caret 圖示 |

## `.input.filled.is-select`

- 用原生 `<select>` + `appearance: none` + 自繪 caret（右側 SVG triangle；尺寸 / 位置見 app.css）
- 右側保留 `padding-right` 讓出 caret 空間（值見 app.css）
- Hover → `cursor: pointer`
- Focus 行為與 text input 一致（加粗 Primary 底線）
- Disabled / Readonly → caret 隱藏、`padding-right` 收回（值見 app.css）、`cursor: default`

## Textarea（`.input--textarea`）

- 有最小高、`resize: vertical`（值見 app.css）
- padding 改為四邊均等（承載多行；值見 app.css）
- Readonly：背景透明，與 input 一致

## Group Header 樣式

```
.form-section__bar              高度自動，底部留白（值見 app.css）
├─ ::before                     藍直條，rgb(var(--color-sf-primary))（尺寸 / 右間距見 app.css）
└─ h2.form-section__title       Bold，color: rgb(var(--color-sf-primary))（字級見 app.css）
```

**規則**：

- 群組標題**不**加底線、**不**加背景
- **不**放任何輔助文字 / icon 在標題列右側（保持極簡）
- Group 之間以固定間距區隔（值見 app.css），**不**用分隔線

## 欄位排列原則（撰寫表單時）

1. **同類欄位放同一 Section**，每個 Section 標題用 4~6 字描述（如「基本資料」「聯絡資訊」「補充說明」）
2. **必填欄位優先**放在 Section 前段
3. **代號 / 狀態類短欄位**放第一行右側，**地址 / 備註類長欄位**用 `--full` 整列
4. **每行不超過 4 欄**，視覺上一掃即看完一列
5. 若一列只剩 1~2 欄，補上空白 `<div class="form-field"></div>` 維持 grid 對齊
6. Help text 高度**永遠保留最小高度佔位**（值見 app.css），避免錯誤訊息出現時欄位位移

## Readonly / Archived 全域樣式

整張表單為唯讀（`.is-readonly-view`）或停用後檢視（`.is-archived-view`）時，所有 `.input` 變透明背景 + `var(--border-default)` 底線 + `var(--text-secondary)` 文字色 + `pointer-events: none`（底線粗細值見 app.css）；select caret 隱藏。例外規則與切換時機**詳見 `profiles/erp-setup.md §設定檔資料狀態矩陣`**。

## A11y

- `<label>` 必須與 input 關聯（同 `.form-field` 內，建議用 `for` + `id` 或包覆方式）
- 必填用 `<span class="required">*</span>` 視覺標記 **且** input 加 `required` 屬性
- Error 訊息與欄位用 `aria-describedby` 連結（reviewer 用 keyboard reader 時可讀到錯誤）
- Tab 順序依 DOM 順序（grid 不改變 tab order）
- Select 支援鍵盤輸入首字快速跳選（原生行為，**不要 JS 攔截**）
