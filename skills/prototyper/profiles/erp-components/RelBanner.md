# rel-banner 關係情境橫幅（作業檔）

> 屬於 `profiles/erp-transaction.md` 的延伸元件規格。
> 載入時機：作業檔 Form View **依需求載入**（單據帶沖銷 / 退貨 / 來源情境說明時載入；由 `SKILL.md §支援檔案` 規定——該處為載入規則的**單一來源**）。
>
> 上層 profile：`profiles/erp-transaction.md`
> 同層元件：`ListSearch.md` / `DataGrid.md` / `FormGroup.md` / `FormFooter.md` / `SummaryCard.md` / `Stepper.md` / `Permissions.md` / `RelBanner.md` / `Skeleton.md`
>
> 對齊基準：`design-prototype/web-erp/庫存模組`（出庫單 / 入庫單沖銷情境）。

---

`rel-banner` 是 Form View 內**單據層級的情境說明橫幅**，用於交代「這張單為何存在 / 從哪來 / 是不是沖銷」等**關係脈絡**，置於 Summary Card 下方、Smart Bar / Section 之上。

與其他兩種橫幅的分工**不可混用**：

| 橫幅 | class | 用途 | 觸發 |
|---|---|---|---|
| **rel-banner** | `.rel-banner--info` / `--reverse` | 關係 / 來源 / 沖銷情境的**常駐**說明 | 單據本身屬性（如 `doc_type === 'reverse'`） |
| 再次核准警示 | `.form-banner.is-warning` | 「曾被取消核准，請重新確認」 | `was_unapproved / was_cancelled`（詳 `erp-transaction.md`） |
| 行內欄位錯誤 | `.help.is-error` | 單一欄位驗證訊息 | 欄位 `@blur` / 提交驗證 |

## Anatomy

```html
<!-- info：正向來源情境（如「本單由驗收單自動產生」） -->
<div class="rel-banner rel-banner--info">
  <iconify-icon icon="material-symbols:info-outline"></iconify-icon>
  <span>本入庫單由驗收單 <strong>{{ sourceNo }}</strong> 核准後自動產生，內容不可修改。</span>
</div>

<!-- reverse：沖銷 / 紅字情境（橘色警示語氣） -->
<div class="rel-banner rel-banner--reverse">
  <iconify-icon icon="material-symbols:warning-outline"></iconify-icon>
  <span>本單為 <strong>{{ originNo }}</strong> 的沖銷單，數量以負值表示。</span>
</div>
```

## 視覺規格

| 元素 | 規格 |
|---|---|
| `.rel-banner` | `display: flex; align-items: flex-start; gap: var(--space-md)`（8px）`; margin: var(--space-xl) var(--space-3xl) 0`（16px 上 / 24px 左右 / 對齊 main panel）`; padding: 12px 14px; border-radius: var(--radius-lg)`（8px）`; font-size: 13px; line-height: 1.6; border: 1px solid` |
| icon | `font-size: 18px; flex: none; margin-top: 1px` |
| `--info` | 背景 `rgba(var(--color-sf-primary), .06)`、邊框 `rgba(var(--color-sf-primary), .30)`、文字 `#1F3A5F`（深藍可讀）、icon 色 `rgb(var(--color-sf-primary))`（#2877EE） |
| `--reverse` | 背景 `rgba(var(--color-sf-warning), .08)`、邊框 `rgba(var(--color-sf-warning), .35)`、文字 `#7A4A00`（深棕可讀）、icon 色 `rgb(var(--color-sf-warning))`（#F79009） |
| `strong` | `font-weight: 700`，承襲橫幅文字色 |

> 兩種 variant 只差色票；語意——`--info` 中性說明（藍）、`--reverse` 沖銷 / 需注意（橘）。**禁**用紅色（紅保留給作廢 / 錯誤，避免語氣過重）。

## 互動與出現條件

- **常駐、不可關閉**：rel-banner 反映單據固有屬性，非提示型 toast，**不**提供關閉鈕。
- 出現條件由單據屬性決定，例：
  - `v-if="form.docType === 'reverse'"` → `--reverse`
  - `v-if="form.sourceNo"` → `--info`（交代來源單）
- 無情境時整段不渲染，**不**留空 banner（比照 Smart Bar 無關聯時整段移除）。
- 一張單可同時出現多條（如「來源 info」+「沖銷 reverse」），依序堆疊，彼此間距由 `margin-top` 控制。
