# Skeleton 載入骨架（作業檔 / 設定檔）

> 屬於 `profiles/erp-transaction.md` 的延伸元件規格。
> 載入時機：需呈現載入中骨架時**依需求載入**（由 `SKILL.md §支援檔案` 規定——該處為載入規則的**單一來源**）。
>
> 上層 profile：`profiles/erp-transaction.md`
> 同層元件：`ListSearch.md` / `DataGrid.md` / `FormGroup.md` / `FormFooter.md` / `SummaryCard.md` / `Stepper.md` / `Permissions.md` / `RelBanner.md` / `Skeleton.md`
>
> 對齊基準：`design-prototype/web-erp/庫存模組`（`.sk` / `.sk-line` / `.sk-pill`）。取代 `REFERENCE.md` 元件對照表中泛指的 `.skeleton`——新範本一律用 `.sk` 系列。

---

資料抓取期間，以**與最終內容同形狀**的 shimmer 佔位取代真實元件，避免版面跳動（CLS）。骨架是**結構預告**而非裝飾：骨架的數量 / 排列應貼近載入後的實際內容（如 List 先放 8–10 條 `sk-line`、Form 先放對應數量的欄位骨架）。

## Class

| class | 用途 | 形狀 |
|---|---|---|
| `.sk` | shimmer 基底（所有骨架共用動畫） | 由行內 `style="width;height"` 決定 |
| `.sk-line` | 文字 / 欄位列骨架 | `border-radius: var(--radius-sm)`（4px） |
| `.sk-pill` | 按鈕 / chip 骨架 | `width: 220px; height: 36px; border-radius: var(--radius-full)` |

## 視覺規格

```css
.sk {
  display: block;
  border-radius: var(--radius-md);                 /* 6px */
  background: linear-gradient(90deg, #EEF0F3 25%, #F6F7F9 37%, #EEF0F3 63%);
  background-size: 400% 100%;
  animation: sk-shimmer 1.2s ease infinite;
}
.sk-line { border-radius: var(--radius-sm); }       /* 4px */
.sk-pill { width: 220px; height: 36px; border-radius: var(--radius-full); }

@keyframes sk-shimmer { 0% { background-position: 100% 50%; } 100% { background-position: 0% 50%; } }
@media (prefers-reduced-motion: reduce) { .sk { animation: none; } }
```

- 漸層三段灰（`#EEF0F3 → #F6F7F9 → #EEF0F3`）是 DS 骨架色票，**禁**改用其他灰或加邊框。
- 尺寸用行內 `style` 表達骨架的寬高（如 `<span class="sk sk-line" style="width:60%;height:14px"></span>`），形狀類別只決定圓角。

## 用法樣板

```html
<!-- List 載入中：以 sk-line 取代資料列 -->
<div v-if="loading" class="dg__scroll">
  <div v-for="n in 8" :key="n" class="sk sk-line" style="width:100%;height:20px;margin:14px 16px"></div>
</div>

<!-- Form 欄位載入中：label + input 兩段骨架 -->
<div v-if="loading" class="form-field">
  <span class="sk sk-line" style="width:72px;height:14px"></span>
  <span class="sk sk-line" style="width:100%;height:40px"></span>
</div>

<!-- 按鈕 / chip 載入中 -->
<span v-if="loading" class="sk sk-pill"></span>
```

## 互動與出現條件

- `v-if="loading"` 顯示骨架、`v-else` 顯示真實內容；兩者互斥，**禁**同時出現。
- 骨架只在**首次載入 / 切換資料來源**時出現；行內局部更新（如存檔 spinner）不用骨架，改用按鈕內 spinner（詳 `FormFooter.md §主 CTA Loading`）。
- 與空狀態的分工：`loading` → 骨架；載入完成且無資料 → `empty-state`（`inbox` icon）；無權限 → `perm-block`（`lock` icon，詳 `Permissions.md`）。三者依序判斷，互斥。
- 一律尊重 `prefers-reduced-motion`：reduce 時停 shimmer 動畫（保留靜態灰塊佔位）。
