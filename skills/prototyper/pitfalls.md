# Prototyper Pitfalls（反覆出現的審查問題）

> **製作前必讀**——不是製作後自檢清單。SKILL.md §1 R2b 規定每次製作前先把整份過一遍，特別是「DS 反射對照表」每一條，內化後再開始畫 prototype。製作完成後在階段 3 自檢時再對一次，但**主要的攔截點在開工前**。
>
> 此檔累積**已經被抓到、又重複出現**的審查問題。
>
> 與 `SKILL.md` / `profiles/*.md` 的差異:
> - SKILL / profile = 規範本身（要怎麼做才對）
> - **pitfalls = 經驗教訓**（曾經做錯、容易再錯的具體 case）

---

## 寫入規則

每條 pitfall **盡量包含三個欄位**:

- **症狀**:具體看到什麼錯（程式碼片段 / 視覺現象）
- **正確做法**:對照 SKILL / profile 的哪一條
- **為什麼會反覆犯**:讓未來的自己 / 別人記得避開的線索

範例格式:

```
### [yyyy-mm-dd] 簡短描述

- **症狀**:`<select v-model="filters.status"><option value="all">全部</option>...`
- **正確做法**:第一個 option 必須 `value=""`（profile/erp-transaction.md §List View 七項自檢第 2 項）
- **為什麼會反覆犯**:寫程式直覺會給「全部」一個 'all' 字串，但搜尋邏輯預期空字串作為「不過濾」
```

---

## 通用（跨專案）

<!-- 樣式、結構、技術 baseline 相關 -->

### [2026-05-25] 使用者只貼 pitfalls.md URL 就開工，沒抓 SKILL.md / profile

- **症狀**：使用者在 claude.ai/design（或任何只能透過 URL 引用的環境）說「使用 `skills/prototyper`」並貼 pitfalls.md 連結 → assistant 以為只要這份 → 沒讀 SKILL.md 的階段 0a 規定、沒讀 profile 的 App Shell / state machine → 已經畫完 prototype 才被使用者提醒比對 pitfalls → 6 條反射違規（outlined input、CTA 加 icon、sticky rgba 透色、icon variant rounded、欄寬只設 width、modal 焦點沒處理）一次浮現。
- **正確做法**：**讀到這一條時，立刻回頭把 SKILL.md §0 列出的必抓檔全部抓完再開工**——SKILL.md / profiles/<project>.md / templates/*.html。pitfalls.md **不是獨立規範**，是 SKILL.md 的附件。
- **為什麼會反覆犯**：（1）pitfalls 的標題與 DS 反射對照表看起來就像「夠完整的指引」，AI 容易把它當成 SKILL 本體；（2）web_fetch 截斷會讓 assistant 以為「拿到的就是全部」；（3）使用者不一定會主動列出所有檔，只會丟一個 URL 期待 skill 自己擴開。

### [2026-05-11] AI 預設樣式 ≠ Design System

- **症狀**:生出來的 prototype 像 Bootstrap / 一般 web app — outlined input、所有按鈕都帶 icon、操作欄 hover 才浮出、卡片有 shadow、`<input readonly>` 直接套 disabled 樣式、**App shell chrome（header 首頁/通知/設定、nav-rail 各項）被做成 `-outline` 線條版**、新增按鈕寫成 `<button><iconify-icon icon="material-symbols:add"></iconify-icon>新增</button>`。
- **正確做法**:每次製作前對照「DS 反射對照表」（下方）；template 已預設成 DS-correct 樣式，**不要改回 AI 直覺**。專案的 DS 來源在 `profiles/<project>.md` 內指定（ERP → Claude Design 內部 design system，README 已記於 `profiles/erp-transaction.md`）。
- **為什麼會反覆犯**:訓練資料中 Bootstrap / Tailwind UI Kit / generic web app 的範例量遠多於任何企業內部 DS。沒被提醒就走預設。

#### DS 反射對照表（每次製作前掃）

| 反射做法（錯） | DS 正確做法 |
|---|---|
| input / select / textarea 用 `<input>` 預設樣式（看起來是 outlined） | 套 `.input.filled`（背景 surface-variant + 底線）為預設；`.outlined` 只在特定情境用 |
| 把 read-only 欄位直接套 `disabled` | `readonly` 屬性 + DS 的 readonly 樣式（背景仍 surface-variant、文字 secondary、cursor: default）；**禁**用 disabled 屬性表達 read-only |
| 自行決定 icon 實心/線條而不查 DS | 實心/線條依 **Design System** `components.css` / DS README §icon；用 Iconify web component 寫，線條 = `-outline` 後綴、筆畫型無 outline（詳 SKILL.md §4 icon 條 + REFERENCE §icon 對照）。**注意**：font 版「禁 `_outline` 字尾」不適用 web component |
| **App shell chrome 用 `-outline` 線條版**（`home-outline` / `notifications-outline` / `settings-outline` / `account-balance-outline`…）| **App shell chrome 一律實心(無後綴)**:`home` / `notifications` / `settings` / `account-balance` / `inventory-2` / `badge` / `cards-star`——Athena DS 規定 shell = 實心(FILL 1)，**別反射套「chrome 走線條」通則**。例外:favorite 切換 `star ↔ star-outline`。內容區 icon（操作鈕/空狀態/分頁/設定檔側欄）才依 DS 走線條。詳 `profiles/Shared.md §App shell icon` |
| DataGrid 操作欄做成 hover 才浮出 | 操作欄永遠顯示，凍結在右側 |
| 卡片預設加 `box-shadow` | 預設**無 shadow**（只用 1px outline-variant 邊框）；shadow 僅出現在 hover 或 dialog/popover 等浮層 |
| Summary card 是一塊，內容平鋪 | Summary 區分**上區（title + stepper / status pill）+ 下區（指標 / 關聯資訊）**兩塊，`position: sticky`，無 shadow，padding 24px |
| Smart Bar 用 `<a><iconify-icon icon="material-symbols:link"></iconify-icon>標題</a>` | 用 `.smart-bar > .card-btn` 結構，**無 link icon**；每個 card-btn 內含 count + 單位 + 標題 + `arrow-outward`；詳細結構見 profile |
| Stepper 用一個 `<li>` 配 `is-current`/`is-done` 兩態 | 三態（pending / active / done）+ 連接線（bar）；done 顯示 check icon、active 內白環、pending 灰；詳見 profile |
| 主要 CTA 按鈕（新增 / 提交 / 儲存）帶 icon 強調 | CTA 標籤本身就是動詞，**禁加 icon**；icon 只用在 icon-only 按鈕（如 settings / close）或導向類（如 chevron-left「上一筆」） |
| 表單區塊（DynamicForm）外層再包 border + padding | **無外框、padding 0**；外距由父層 section 控制 |
| Section header 與下方內容貼太近 | `DsSectionHeader` 下方 padding 16px |

### [2026-05-14] AI 自動補 PRD 沒列的資訊 / DS 預設覆寫 PRD 視覺

- **症狀（資料層）**：PRD 列了 8 個欄位，生出來的 List View 出現 10 欄；PRD 沒提的「建立者 / 建立時間」自動被加進 form section。
- **症狀（介面層）**：PRD 註明 input 用 filled 樣式、卡片無 shadow，但生出來的卡片帶 shadow、input 是 outlined。
- **正確做法**：SKILL.md §1「資料來源權重規則」R1 + R2。資料層 PRD 是 source of truth，PRD 沒列就停下來問；介面層 PRD 明確指定時優先於 DS 預設。
- **為什麼會反覆犯**：訓練資料中「補齊」是常見的助手行為（user 給少寫多）；DS 對 AI 來說是「已知預設」，比 PRD 視覺指定（散落在多處的文字描述）更容易被當作 baseline。

### [2026-05-14] Form section 沒列「元件」就用 generic web 預設

- **症狀**：PRD Form section 表格只有 `欄位 / 必填 / 說明`（沒列「元件」欄），AI 自行挑成 Bootstrap-ish 預設樣式 — outlined input、`disabled` 表 readonly、checkbox 表 boolean、generic `<select>` 沒 caret icon。產出與 Athena DS 完全脫節。
- **正確做法**：跑 `REFERENCE.md §5 階段 1` 的 Pass 0/1/2 三段式 — Pass 0 找模組對應的設計文件 § 元件清單章節（出納 §2.1.1 / 應付 §3.1 / 應收 §2 互動元素清單）；無設計文件時退到 `profiles/erp-transaction.md §PRD 元件對照 Table A`；Form section 沒列「元件」欄時跑 Table B 推論規則並標記推論結果讓 user 確認。
- **為什麼會反覆犯**：（1）PRD 的 List/Search section 已標 `元件` 欄但 Form section 通常省略，AI 看到 Form section 就退回訓練資料預設；（2）AI 對 `<TextInput>` / `<DataGrid>` 等 shared-ui wrapper 沒概念，會優先用 raw `<input>`；（3）布林欄位的反射是 `<input type="checkbox">` 或 Switch，但設定檔 `active` 慣例是 Dropdown「啟用 / 停用」。

### [2026-05-20] Filled input 套錯視覺（白底 + 1px 邊 vs Material 風格）

- **症狀**：(a) 套成「白底 `#FFFFFF` + 1px solid `#D5D8DC` 完整邊框 + 圓角 4px」(Bootstrap / Tailwind outlined 風格)；(b) Focus 加 outline ring（如 `box-shadow: 0 0 0 2px rgba(37,99,235,.20)`）；(c) Label / placeholder 顏色與字級亂選；(d) 必填星號顏色用紅色但非 `#F4493E`；(e) 上下圓角都做（應僅上方 `4px 4px 0 0`）。
- **正確做法**：`.input.filled` 是 **Material 3 Filled 風格**：
  - 背景 `#EDF0F7`（surface-variant）
  - 底部 underline `1px solid #7F8996`（無左右上邊框）
  - `border-radius: 4px 4px 0 0`（**僅上方圓角**，Material Filled 簽名）
  - 高度 40px、padding `0 0 0 10px`
  - Label：Roboto 14px Regular / line-height 130% / `#3C4A5B`
  - Placeholder：Roboto 14px Regular / line-height 150% / letter-spacing 0.24px / `#67717E`
  - 必填星號：12px / letter-spacing 0.1px / `#F4493E`
  - **Focus**：底線加粗 `2px solid` primary 色，**禁加 outline ring**（焦點靠底線變色表達）
  - 詳 `erp-transaction.md §輸入欄樣式 → Filled 詳細視覺規格`
- **為什麼會反覆犯**：(1) 訓練資料中 Bootstrap / Tailwind / Tailwind UI 預設 input 都是 outlined 白底 + 完整 1px 邊，AI 直覺套上；(2)「Filled」一詞在 web 通用語境意義不明（Material Filled vs 一般「filled in」），AI 沒區分；(3) Focus 加 ring（Tailwind 慣例）vs Material Filled 用 underline 變化的差異容易混淆。

### [2026-05-20] Breadcrumb 分隔符用 `/` 或 `>` 字元，而非 chevron-right icon

- **症狀**：生出來的 breadcrumb 分隔符寫成文字字元 —— `<span class="erp-breadcrumb__sep">/</span>`（路徑風）或 `<span class="erp-breadcrumb__sep">&gt;</span>`（`>` 字元）；或 PRODUCE 風格的描述文字寫「模組分類 / 功能名稱 / 單號」。
- **正確做法**：分隔符一律用 iconify `material-symbols:chevron-right`（`<iconify-icon class="erp-breadcrumb__sep" icon="material-symbols:chevron-right">`）。chevron-right 的 `>` 形狀即「層級」語意；**禁**用 `/`（路徑語意，URL / 檔案系統用，不該用在 navigation breadcrumb），**也不要用 `>` 純文字字元**——統一走 icon。`profiles/Shared.md §Breadcrumb` 是視覺 SoT；`profiles/erp-transaction.md` 層級結構與 Handoff Checklist 須一致；template `module-page.html` / `setup-page.html` 已預設成 chevron-right icon，**不要改回 `/` 或 `>` 文字**。chevron-right 屬筆畫型，無 `-outline` 變體（見 `SKILL.md §4 icon 條`）。
- **為什麼會反覆犯**：訓練資料中 web app breadcrumb 文字分隔符（Bootstrap `/`、純 `>`）的範例量遠多於 icon 分隔符，AI 沒被提醒就走文字直覺；且早期版本 Athena template 一度用 `/`、後又改 `>` 文字，舊 prototype 看一眼又會被誤導回文字字元。

---

## ERP profile

<!-- App Shell / state machine / Smart Bar / handoff 五項相關；設定檔特化規則相關 -->

### [2026-05-18] 刪除按鈕用紅框白底紅字（非實心紅底）

- **症狀**：List 列尾或 Form 底部刪除按鈕做成「實心紅底白字」（filled `.btn--danger`，紅 bg + 白字）而非「紅框白底紅字」（`.btn--outline-danger`，紅描邊 + 白底 + 紅字）；hover 時還會切換 icon 顏色；批次刪除做成 filled 紅 button-with-label。
- **正確做法**：List / Form 的刪除按鈕一律走「紅框白底紅字」家族（outline 或 icon-only）—— 列尾用 `.ico-btn.is-delete`（純紅 icon, 無框）、批次列用 `.btn-icon--danger-square`（紅框 + 白底 + 紅 icon）、Form 底部用 `.btn--outline-danger`（紅框 + 白底 + 紅字）；背景透明 / 白，hover 加 `error @ 8%` 底色，**不**改 icon 顏色。實心 `.btn--danger`（紅底白字）**僅出現於 Modal 主按鈕**。詳細視覺見 `erp-setup.md §設定檔刪除機制`。
- **為什麼會反覆犯**：Bootstrap / Material / Tailwind UI 範例的「危險按鈕」幾乎都是 filled red，AI 直覺套上；訓練資料裡「紅框白底紅字」/ icon-only-danger 出現頻率低，需要被明確覆寫。

### [2026-05-18] 刪除確認 Modal 主按鈕預設焦點誤觸

- **症狀**：刪除 confirm modal 開啟後 Enter 直接執行刪除；主按鈕在 Tab order 第一位；`Delete` / `Backspace` 鍵被綁為刪除快捷鍵。
- **正確做法**：刪除類 confirm modal（danger / warning kind）**預設焦點放在「取消」按鈕**；Tab order 取消 → 主按鈕；`Esc` 與 `Enter` 都對應「取消」；只有使用者主動 Tab 到主按鈕並 Enter 才執行刪除。`Delete` / `Backspace` **MUST NOT** 綁為刪除快捷鍵。
- **為什麼會反覆犯**：一般 confirm modal 的 UX 預設「主按鈕焦點」以利快速確認；但**破壞性動作要反過來**——預設焦點放安全選項。AI 沒被提醒就走一般習慣。

### [2026-05-20] DataGrid 欄寬未鎖 / sticky cell hover-selected 沒補實色

- **症狀**：(a) Checkbox 欄寬走預設或寫成 40px / 56px（正確 **50px**）；(b) 操作欄沒用 `.col-actions` / `.col-actions--single` 區分，2 顆按鈕用 80px、1 顆用 40px 等亂值（正確 **96px / 56px**）；(c) sticky 欄寬度沒用 `width + min + max + box-sizing` 三件套鎖死，被 auto-fit 擠壓；(d) **hover/selected 直接套 `rgba(primary, .06/.10)` 到 sticky cell** → scroll 時下層內容穿透顯現；(e) selected + hover 疊加沒處理，hover 時 selected 視覺消失；(f) sticky cell hover 用了不同色相(如灰色)，讓凍結欄看起來像獨立區塊。
- **正確做法**：嚴守 `profiles/erp-components/DataGrid.md → 欄位 min-width / Sticky 凍結欄 / 互動狀態優先級`：
  - **欄寬鎖定**：Checkbox `.col-check` = 50px / 操作 2 顆 `.col-actions` = 96px / 操作 1 顆 `.col-actions--single` = 56px（三件套 width/min/max + box-sizing）
  - **Sticky offset 連動**：sticky-left 第 2 欄 `left: 50px`（與 checkbox 寬一致）
  - **互動優先級**：default < hover < selected < selected + hover
  - **Sticky 補實色**：hover sticky = `rgb(232, 238, 252)`、selected sticky = `rgb(229, 235, 251)`、selected + hover 取對應疊加實色；**禁**用 `rgba()` 透明色在 sticky cell
  - **色相一致**：sticky 與一般 cell 同色相，只差 alpha 換實色
- **為什麼會反覆犯**：(1) 大多 grid library（DataTables、Material Table 等）預設沒 sticky cell，AI 沒概念要為 sticky 補實色；(2) hover/selected 直覺寫法是 `tbody tr:hover { background: rgba(...) }`，沒區分一般 cell 與 sticky cell；(3) 欄寬具象值（checkbox 50 / actions 56 或 96）容易被「差不多就好」的直覺取代，必須鎖死。

---

## 維護指引

- 每次 reviewer / PM 在審查時抓到的問題，先判斷:
  - 一次性誤解 → 對話內修正即可，**不寫入此檔**
  - 已經是第 2 次以上犯同樣錯 → **必須**寫入此檔
- 寫入時先檢查是否已有同類條目可合併
- 條目過時（如規範改了、原問題不再出現）就刪除，**不要保留歷史記錄**——這不是 changelog
- 保持每條 pitfall 簡潔，能讓「下次製作時 5 秒讀懂」是最低標
