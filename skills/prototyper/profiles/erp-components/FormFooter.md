# Form Footer 結構與互動（共通）

> 屬於 `profiles/erp-transaction.md` 的延伸元件規格。
> 載入時機：實作該元件（或審查含該元件的 prototype）時依需求載入；不會隨 erp profile 自動載入。
>
> 上層 profile：`profiles/erp-transaction.md`
> 同層元件：`ListSearch.md` / `DataGrid.md` / `FormGroup.md` / `FormFooter.md`

---

作業檔與設定檔的 Form Footer **共用本節結構、RWD、互動、A11y 規則**；右側按鈕內容差異（作業檔的狀態動作群 vs 設定檔的儲存變更 / 啟用按鈕）見對應章節：

- 作業檔右側按鈕內容 → `profiles/erp-transaction.md §Form View 七項自檢`第 7 項
- 設定檔右側按鈕內容 → `profiles/erp-setup.md §Form Footer（設定檔版）`

## Anatomy

```html
<footer class="form-footer">                    <!-- Container（sticky / flex-shrink:0）-->
  <div class="form-footer__nav">                <!-- 左：記錄分頁器 -->
    <button class="pg-btn">‹</button>
    <span  class="form-footer__pos">3 / 27</span>
    <button class="pg-btn">›</button>
  </div>

  <div class="form-footer__actions">            <!-- 右：操作群 -->
    <!-- 內容依「作業檔 / 設定檔」差異，順序：條件按鈕 → 更多操作 ▾ → 主 CTA -->
    <button class="btn btn-outline">啟用</button>
    <div class="btn-more">
      <button class="btn btn-outline btn-more__btn">更多操作 ▾</button>
      <ul class="btn-more__menu">…</ul>
    </div>
    <button class="btn btn-primary">儲存變更</button>
    <!-- 唯讀狀態改顯示： -->
    <span class="readonly-tag">👁 唯讀檢視</span>
  </div>
</footer>
```

**結構規則**

- Footer **必為 `<footer>`** 元素，且為 `.form-view` 的**最後子節點**（不是 `.form-view__body` 內部）
- 左區（`__nav`）與右區（`__actions`）以 `justify-content: space-between` 分置兩端，**中間不得插入任何內容**
- 右區順序固定：**條件按鈕 → 下拉群組 → 主 CTA**（由左至右視覺權重遞增）

## Container Token

| 屬性 | 值 |
|---|---|
| `position` | `sticky`（隨 `.form-view` flex column 釘底） |
| `flex-shrink` | `0`（永遠不被壓縮） |
| `display` / 排版 | `flex` / `align-items: center` / `justify-content: space-between` |
| `padding` | **桌機 `12px 24px`** ／ 平板 `12px 16px` ／ 手機 `10px 12px` |
| `background` | `var(--bg-surface-default)` |
| `border-top` | `1px solid var(--border-default)` |
| `box-shadow` | 內容可捲動時加 footer top shadow；內容未滿一頁時移除。**DS 目前無對應 token**（`--shadow-e1` 是 card / dropdown 用、方向錯），請先在 `REFERENCE.md §9` 新增 `--shadow-footer-top`（建議值 `0 -2px 8px rgba(var(--color-sf-neutral), .04)` 或對應 DS 規格）再引用；prototype 階段未補 token 前可暫時省略 shadow |
| `z-index` | `5`（高於表單內容，低於 modal / toast） |
| `min-height` | `64px`（保留 40px CTA + 12×2 padding） |

## 左：`__nav`（記錄分頁器）

| 屬性 | 值 |
|---|---|
| 元件 | `pg-btn 32×32` + `.form-footer__pos` |
| 顯示格式 | `{當前筆數} / {總筆數}`；**新增模式 `1 / {總筆數 + 1}`** |
| 字級 | 13px / `color: var(--text-secondary)` / `font-variant-numeric: tabular-nums` |
| 間距 | `gap: 4px`，pos 左右 `padding: 0 4px` |
| Hover | `background: rgba(var(--color-sf-primary), .08)`；顏色升至 `var(--text-primary)` |
| Disabled | `opacity: .38; cursor: not-allowed` |

## 右：`__actions`

| 屬性 | 值 |
|---|---|
| 排列 | `display: flex; align-items: center; gap: 8px` |
| 按鈕高度 | 統一 `40px`（CTA / Outline / Dropdown 同高） |
| 主 CTA | `.btn.btn-primary`，寬度自適應、永遠靠右 |
| Outline | `.btn-outline`：`border: 1px solid rgb(var(--color-sf-primary))`、`border-radius: var(--radius-sm)`、`padding: 10px 16px` |
| 下拉 menu | `position: absolute; left: 0; bottom: calc(100% + 4px)`（向上彈、左對齊按鈕） |

## RWD 斷點

| 斷點 | 行為 |
|---|---|
| **≥ 1024px（桌機）** | 預設樣式；左右兩段水平並列 |
| **768–1023px（平板）** | padding 改 `12px 16px`；`__actions` `gap: 6px`；下拉 menu `min-width: 180px` |
| **< 768px（手機）** | ① Footer 改 `flex-direction: column-reverse; align-items: stretch; gap: 8px`（CTA 在上、分頁器在下）<br>② 右區按鈕 `flex: 1`，主 CTA `min-width: 50%`；條件按鈕與「更多操作」各佔等寬<br>③ 「儲存變更」標籤可縮為「儲存」<br>④ 分頁器 `justify-content: center`<br>⑤ Footer 高度允許自動長至約 `120px` |
| **< 360px** | 下拉 menu 改 `position: fixed; left: 12px; right: 12px; bottom: 64px`（鋪滿避免被裁切） |

## 鍵盤 / 觸控

- 按鈕 hit-target **不小於 `40×40px`**（pg-btn `32×32` 在桌機 ok，手機自動放大至 `40×40`）
- Focus ring：使用 DS 預設 `outline: 2px solid rgb(var(--color-sf-primary)); outline-offset: 2px`，**不可移除**

## 互動規則

### 1. 記錄分頁器

| 觸發 | 行為 |
|---|---|
| 點 `‹` / `›` | 切換到上 / 下一筆。**若表單為 dirty**，先彈確認「尚未儲存的變更會遺失，是否繼續？」 |
| 新增模式（`isNew = true`） | `‹` `›` 皆 disabled；`pos` 顯示 `1 / {recordTotal + 1}` |
| 首筆 / 末筆 | 對應方向 disabled |
| 鍵盤 | `Alt + ←` / `Alt + →` 對應上下筆 |

### 2. 主 CTA「儲存變更」

| 狀態 | 條件 | 表現 |
|---|---|---|
| 顯示 | `canSave = true`（有編輯權限且表單有效） | `.btn-primary` |
| 隱藏 | 唯讀（無權限 / 已停用 / 角色限制） | 改顯示 `.readonly-tag「👁 唯讀檢視」` |
| Disabled | 表單未通過 validation 或正在送出 | `opacity: .38; pointer-events: none`；hover 顯示 tooltip 列出缺漏欄位 |
| Loading | 送出中 | 文字換成 spinner + 「儲存中…」；其餘 footer 按鈕 disabled |
| 鍵盤 | `Ctrl/Cmd + S` 觸發 | 同點擊 |
| 成功 | toast「已儲存」並把按鈕狀態回 idle | — |
| 失敗 | 維持資料、彈錯誤 modal、CTA 回 idle | — |

### 3.「啟用」按鈕（設定檔條件出現）

- 出現條件：`!isNew && !formData.active && !isReadOnly`
- 點擊：開啟 confirm modal「確定啟用此 XX？」，確認後呼叫 API；成功後此按鈕消失（`formData.active` 變 `true`）

### 4.「更多操作 ▾」下拉

| 行為 | 規則 |
|---|---|
| 開合 | 點按鈕 toggle；再次點按鈕關閉 |
| 外部關閉 | 點 menu 以外區域、按 `Esc`、滾動表單、視窗 resize 皆關閉 |
| 彈出位置 | 預設向上彈（`bottom: calc(100% + 4px)`），左對齊按鈕 left |
| 邊界翻轉 | 若上方空間 < `200px`，改向下彈；若右側超出 viewport，自動 right-align |
| 鍵盤 | `Enter / Space` 開合；開啟後 `↑/↓` 切換項目、`Enter` 觸發、`Esc` 關閉 |
| 項目 disabled | `.is-disabled` → `cursor: not-allowed`；hover 不變色；點擊無反應 |
| 內建項目 | 「儲存並新增」、「複製」、（可選）「刪除」、「停用」。新增模式下「複製」disabled |

### 5. 唯讀模式

- `.form-footer__actions` 只保留 `.readonly-tag`，**移除**條件按鈕 / 更多操作 / 主 CTA
- `__nav` 仍可切換筆數（瀏覽用途）

## Dirty-guard 對照表

| 動作 | 是否需 dirty-guard |
|---|---|
| 切換筆（上下筆） | ✅ |
| 點 Sidebar / 換路由 | ✅ |
| 關閉視窗 / Tab（`beforeunload`） | ✅ |
| 點「儲存變更」 / 「儲存並新增」 | ❌（直接走儲存） |
| 點「複製」 | ✅（複製前先保存當前） |

## 狀態矩陣（速查）

| 角色 / 狀態 | 啟用 | 更多操作 | 儲存變更 | 唯讀 tag | 分頁器 |
|---|---|---|---|---|---|
| Admin・既有・active | — | ✅ | ✅ | — | ✅ |
| Admin・既有・inactive | ✅ | ✅ | ✅ | — | ✅ |
| Admin・新增 | — | ✅(複製 disabled) | ✅ | — | disabled |
| AreaManager・既有 | — | ✅（限部分） | ✅ | — | ✅ |
| Viewer / 唯讀 | — | — | — | ✅ | ✅ |
| 送出中 | disabled | disabled | spinner | — | disabled |

## 進銷存作業檔狀態-按鈕矩陣

> 適用採進銷存擴充狀態機（6 值）的作業檔（詳 `erp-transaction.md §進銷存擴充狀態機`）。右區 `__actions` 依 `form.state` 顯示對應按鈕群；沿用本檔結構 / RWD / A11y，僅按鈕內容不同。`convert`（結轉）/ `reject`（退回）/ `cancel`（作廢）為進銷存動作。

| `form.state` | 右區按鈕（由左至右：條件按鈕 → 主 CTA） | 備註 |
|---|---|---|
| `draft` | `刪除`（outline-danger，`!isNew` 才顯示）/ `儲存草稿`（outline-primary）/ `提交`（primary） | 刪除 = 物理刪除（草稿限定） |
| `submitted` | `退回`（outline-danger，限主管）/ `儲存變更`（outline-primary）/ `核准`（primary，限主管） | 退回 → 草稿（二次確認） |
| `approved` | `取消核准`（outline-danger，限主管）/ `作廢`（outline-danger，限主管）/ `結轉採購單`（primary） | 已部分結轉則不顯示「取消核准」 |
| `partial` | `作廢`（outline-danger，限主管）/ `結轉採購單`（primary） | **不顯示**「取消核准」 |
| `done` | `作廢`（disabled，tooltip 提示改至下游單據作廢） | 終態 |
| `cancelled` | 無動作按鈕 | 終態 |
| 唯讀角色（`isReadOnly`） | 只剩 `👁 唯讀檢視` tag，移除所有動作按鈕 | `__nav` 仍可瀏覽切筆 |

- **動詞 CTA 一律無 icon**（提交 / 核准 / 退回 / 作廢 / 結轉採購單 / 儲存草稿 / 儲存變更），對齊 `erp-transaction.md §按鈕 icon 政策`
- **任一明細列編輯中**（`hasEditingRow`）：`儲存草稿` / `儲存變更` / `提交` / `核准` 等寫入類 CTA `:disabled`，避免半完成的列被帶入
- **結轉 / 退回 / 取消核准 / 作廢** 皆走 `confirm` modal 或精靈（詳 `erp-transaction.md §進銷存擴充狀態機` 與範本的 modal 占位）
- 本變體**無獨立「更多操作 ▾」下拉**（按鈕數有限）；若日後需「複製 / 列印」再依 §4 下拉規則加入

## A11y

- Container `<footer>` 使用語意標籤；**不需** `role="contentinfo"`（已是 footer 元素）
- 分頁按鈕加 `aria-label="上一筆 / 下一筆"`
- `.form-footer__pos` 加 `aria-live="polite"`，筆數變動可被朗讀
- 下拉按鈕：`aria-haspopup="menu" aria-expanded="true|false" aria-controls="more-menu-id"`
- 下拉 menu：`<ul role="menu">`、每個 `<li role="menuitem">`；disabled 項加 `aria-disabled="true"`
- 主 CTA disabled 時用 `aria-disabled="true"` **而非** `disabled` 屬性（避免 disabled 阻擋 tooltip 顯示缺漏訊息）
- 整個 Footer Tab 順序：`prev → pos → next → 條件按鈕 → 更多操作 → 主 CTA`

## 驗收清單（QA Checklist）

- [ ] Footer 永遠貼在 `.form-view` 底部，滾動表單不會推走它
- [ ] 新增模式下 `pos` 顯示 `1 / {total+1}` 且分頁器 disabled
- [ ] 「更多操作」彈出位置：向上、左對齊按鈕；空間不足時自動翻轉
- [ ] 唯讀模式右側只剩 `.readonly-tag`
- [ ] `Ctrl/Cmd + S` 在表單聚焦時可儲存
- [ ] 切換筆數時若 dirty 必跳確認；確認後才切換
- [ ] 手機（< 768px）時 footer 縱向堆疊，CTA 全寬
- [ ] 所有按鈕 focus ring 可見，鍵盤可完整操作
- [ ] 送出中所有 footer 按鈕 disabled，CTA 顯示 spinner
- [ ] Dropdown 在 `Esc` / 外部點擊 / scroll / resize 後關閉
