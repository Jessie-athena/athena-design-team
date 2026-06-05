# Prototyper Reference（通用技術細節）

> `SKILL.md` 的詳細展開：token 對照、prototype → production 元件對照、`app.js` / `app.css` 起手式。技術 baseline 以 SKILL.md §4 為單一來源，本檔不重複。
>
> **此檔僅含跨專案通用內容**。頁面框架（Header / Nav-rail / Info Bar 結構）見 `profiles/Shared.md`；專案專屬的 state machine / list view / form view / handoff / App Shell 具體值（nav 項目、programID 格式等）見 `profiles/<project>.md`（如 `profiles/erp-transaction.md`）。

---

## §1 Source of Truth

- **Prototype = 真相來源**；規格文件與 production code 都對齊到 prototype
- 文件變更不回拋 prototype；prototype 修正後須重新 export

## §2 Tech Baseline

技術 baseline（HTML lang / CSS 載入順序 / Vue 3 CDN / `app.js` 與 `app.css` 分離 / Iconify web component icon）已列為 `SKILL.md §4 通用硬性限制`，本檔不重複，以 SKILL.md 為單一來源。Icon 常用對照見本檔 §icon 對照。

### §icon 對照（Material Symbols font → Iconify `material-symbols:`）

規則見 `SKILL.md §4` icon 條：實心 = 無後綴、線條 = 加 `-outline`（**僅可填色的形狀型有 `-outline`**）；底線改連字號。**App shell chrome（Header + Nav-rail）一律實心(無後綴)**——見 `profiles/Shared.md §App shell icon`。下表為 template / 常見 UI 用到的對照，**不確定某 glyph 有沒有 `-outline` 時以 Iconify material-symbols 集實際存在者為準**（筆畫型沒有 `-outline`，直接用 base 名會 404）。

| 用途 | 舊 font glyph | Iconify icon | 變體 |
|---|---|---|---|
| Home（App shell） | `home` | `material-symbols:home` | **實心** |
| 我的最愛（啟用） | `star` | `material-symbols:star` | **實心** |
| 我的最愛（未啟用） | `star_border` | `material-symbols:star-outline` | 線條（切換態例外） |
| 通知（App shell） | `notifications` | `material-symbols:notifications` | **實心** |
| 設定（App shell） | `settings` | `material-symbols:settings` | **實心** |
| Nav-rail 各項（App shell） | — | `material-symbols:account-balance` / `inventory-2` / `badge` / `cards-star` … | **實心** |
| 欄位設定 / tune | `tune` | `material-symbols:tune` | 筆畫（無 outline） |
| List 視圖 | `list` | `material-symbols:list` | 筆畫（無 outline） |
| Form 視圖 | `description` | `material-symbols:description-outline` | 線條 |
| 檢視 | `visibility` | `material-symbols:visibility-outline` | 線條 |
| 編輯 | `edit` | `material-symbols:edit-outline` | 線條 |
| 刪除 | `delete` | `material-symbols:delete-outline` | 線條 |
| 關閉 / chip 清除 | `close` | `material-symbols:close` | 筆畫（無 outline） |
| 空狀態 | `inbox` | `material-symbols:inbox-outline` | 線條 |
| 完成（stepper check） | `check` | `material-symbols:check` | 筆畫（無 outline） |
| 上一頁 / 上一筆 | `chevron_left` | `material-symbols:chevron-left` | 筆畫（無 outline） |
| 下一頁 / 下一筆 | `chevron_right` | `material-symbols:chevron-right` | 筆畫（無 outline） |
| 第一頁 | `first_page` | `material-symbols:first-page` | 筆畫（無 outline） |
| 最後一頁 | `last_page` | `material-symbols:last-page` | 筆畫（無 outline） |
| 更多操作 | `expand_more` | `material-symbols:expand-more` | 筆畫（無 outline） |
| Smart Bar 導向 | `arrow_outward` | `material-symbols:arrow-outward` | 筆畫（無 outline） |
| Toast 成功 | `check_circle` | `material-symbols:check-circle-outline` | 線條 |
| Toast 警告 | `warning` | `material-symbols:warning-outline` | 線條 |
| Toast 錯誤 | `error` | `material-symbols:error-outline` | 線條 |
| Toast / banner 資訊 | `info` | `material-symbols:info-outline` | 線條 |
| 搜尋（List 搜尋鈕） | `search` | `material-symbols:search` | 筆畫（無 outline） |
| 收合 / 展開（ListSearch） | `keyboard_arrow_up` / `_down` | `material-symbols:keyboard-arrow-up` / `-down` | 筆畫（無 outline） |
| select / dropdown caret | `keyboard_arrow_down` | `material-symbols:keyboard-arrow-down` | 筆畫（無 outline） |
| 新增明細列（inline grid） | `add_circle` | `material-symbols:add-circle`（button 用 **flat primary** 樣式：icon primary 色、無底色、無邊框） | 實心 |
| 儲存（inline 編輯） | `save` | `material-symbols:save-outline` | 線條 |
| 鎖列（已結轉，不可改） | `lock` | `material-symbols:lock-outline` | 線條 |
| 匯出 / 下載 | `file_download` | `material-symbols:file-download` | 筆畫（**無 `-outline`**，`file-download-outline` 不存在） |
| Modal 確認（confirm） | `help` | `material-symbols:help-outline` | 線條 |

> 動態 icon（nav-rail `item.icon`、`toastIcon()`、`modalIcon()`、`stateBanner.icon`）在 `app.js` data / helper 直接存**完整 Iconify id**（nav-rail 屬 App shell → 實心，如 `material-symbols:account-balance`；toast / modal / banner 等內容區依 DS，多為 `-outline`），template 用 `<iconify-icon :icon="...">` 綁定。`toastIcon()` / `modalIcon()` 起手式見本檔 §`app.js` 骨架。

## §3 File Layout

- 每模組一個 `.html`，檔名 = 模組中文名（與規格文件同名）
- 共用資源放 `ds/` 或 design system 統一目錄，**勿改**；新增 token 須先回上游 design system

---

## §4 資料來源權重明細

對應 `SKILL.md §1 資料來源權重`。SKILL.md 只列規則名稱與 R1 / R2 一行版；本節是完整四層表 + 派生規則 + 邊界情境。

### 四層權重表

| 順位 | 來源 | 管轄範圍 | 衝突時的處置 |
|---|---|---|---|
| 1 | **SKILL.md + profile**（標 IMPORTANT 的硬性限制） | 技術 baseline、檔案結構、不可妥協的規範 | 永遠最高；PRD / DS 不得覆寫 |
| 2 | **PRD**（規格文件 / 設計稿） | 欄位、資料、狀態流程、業務邏輯、明確指定的視覺 | DS 與其他文件不得擅自擴充或替換 |
| 3 | **Design System** | 元件視覺、token、互動預設 | PRD 未明確指定時用 DS 預設；PRD 明確指定時以 PRD 為準 |
| 4 | **其他**（同類舊模組、Figma frame、chat 描述） | 結構靈感、版型參考 | 只能當參考，**不得**當作「規格」直接搬用 |

### 兩條硬性派生規則（每次製作前自檢）

- **R1 — PRD 完整性原則**：**禁止**自動補 PRD 沒列的欄位 / List 欄 / form section / action / status。PRD 缺漏時停下來問使用者，不要自己編；profile 的「規格抽取表」要求的欄位若 PRD 沒給，視同缺漏。
- **R2 — DS 不覆寫 PRD 視覺**：PRD 對視覺有明確指定（如「filled input」、「summary 上下兩區」、「無 shadow」、特定排版）時以 PRD 為準；PRD 未提時才套 DS 預設。判斷不確定的視覺差異時，**先信 PRD，再回查 DS**。

> profile 內標 **IMPORTANT** 的條目（如 ERP 的 state machine 命名）視為 Tier 1，其餘 profile 內容仍為 Tier 2。

### 邊界情境

| 情境 | 判定 |
|---|---|
| PRD 跟同類舊模組欄位不一致 | 以 PRD 為準（Tier 2 > Tier 4） |
| PRD 沒提某欄位 / 動作，但同類舊模組有 | 觸發 R1，停下來問使用者「PRD 漏列還是刻意省略？」**禁直接搬用** |
| DS 沒提某互動 / 樣式，但 Figma frame 有 | Tier 4 < Tier 3；以 DS 預設為準，Figma 細節僅做結構靈感 |
| profile 標 IMPORTANT 的規則跟 PRD 衝突 | profile IMPORTANT 視為 Tier 1，**贏**；同時在 chat handoff 註明「PRD 對 X 的指定與 profile IMPORTANT 規則衝突，已採用 profile」 |

---

## §5 五階段工作流明細

對應 `SKILL.md §3 五階段工作流`。SKILL.md 只給五階段總覽表；本節是每階段的完整操作步驟。

### 階段 0｜跨專案複用（先問再做）

- 有同類舊模組？→ 從舊 `.html` 抽出介面規格（欄位 / 狀態 / 關聯），新模組直接引用，**不重畫**
- 沒有？→ 進階段 1

### 階段 1｜規格抽取（三段式：Pass 0 / Pass 1 / Pass 2）

**Pass 0｜找元件權威來源**

依 profile 規定的「元件權威來源」順序查找：

1. 該模組是否有對應的設計文件元件清單章節（profile 會列出該專案的章節命名慣例）？
   - 有 → 把該章節完整列出的元件當作**本模組的元件命名單一來源**
   - 無 → 落到 profile 內建的「PRD 元件詞彙 → 實作對照」最小集
2. 記錄本次採用的權威來源（檔名 + 章節），handoff 時要附上

**Pass 1｜純抽取，不選元件**

把 PRD 拆成「欄位 schema 表」並輸出給使用者確認，每行五欄：

| 欄位中文 | 區塊 | 元件（PRD 標示） | 必填 | 約束 / 關聯 |

規則：

- 「元件」欄若 PRD 有列就照填（如 `TextBox` / `DropDownList`），**禁**自己編
- PRD 沒列就填 `?`，等 Pass 2 推論
- PRD 沒列的欄位 / List 欄 / action **禁止**自動補（R1）；缺漏即停下來問

**Pass 2｜schema 查表轉實作**

對 schema 每行：

- prototype 階段 markup：依 profile 對照表查 `Prototype HTML` 欄
- handoff「production 對應元件」：填 Pass 0 權威來源裡的元件名（如 `<TextInput>` / `<DataGrid>`）
- 若 Pass 1 元件欄為 `?`，依 profile 的「Form section 元件推論規則」推論並回填，**標記為推論**讓 user 確認

### 階段 2｜製作 .html（核心）

1. 拿 Pass 1 確認過的 schema → 複製 profile 指定的 starter template → 目標路徑
   - 作業檔（transaction documents，含狀態流程）→ `${CLAUDE_SKILL_DIR}/templates/module-page.html`
   - 設定檔（master data，僅 active true/false）→ `${CLAUDE_SKILL_DIR}/templates/setup-page.html`
   - 類型判斷準則由 profile 規定（ERP 見 `${CLAUDE_SKILL_DIR}/profiles/erp-transaction.md §設定檔（Master Data）類型判斷`）
2. 替換 App Shell（結構與尺寸見 `profiles/Shared.md §頁面框架`；breadcrumb 層級、nav 項目、programID / version 等具體值見專案 profile）
3. 建構 List View（自檢項目見 profile，作業檔與設定檔有獨立清單）
4. 建構 Form View（自檢項目見 profile，作業檔與設定檔有獨立清單）
5. Modal / Toast / Empty State 範例（modal 兩款：`confirm` + `deeplink`，**`pick` 已淘汰**）

### 階段 3｜本機審查（自檢）

兩件事要做：

1. 跑 profile 的 Handoff Checklist，**逐項打勾**才算完成；任一 fail 回對應步驟修正
2. **每次都要掃一眼 `${CLAUDE_SKILL_DIR}/pitfalls.md`**——這份累積了反覆出現的審查問題，目的是不要再犯

### 階段 4｜chat handoff

依 profile 規範交付（如 ERP 要求 5 項）。沒有 profile 時最低限度提供：

1. 對應規格來源（路徑 / 連結 / 「依 chat 需求」）
2. 相比上版差異（首版寫「初版」）
3. 對齊方向（feature 編號 / 文件 / ticket）
4. 特別注意項（已知 trade-off、待 PM 確認的點）
5. **Pass 0 採用的元件權威來源** + **Pass 2 對應的 production 元件清單**（讓下游工程師知道 prototype 用的 HTML 對應到 production 哪個 component）

---

## §6 通用決策題

對應 `SKILL.md §4 通用硬性限制` 之外的常見抉擇。profile 可進一步擴充專案專屬決策題。

| 情境 | 決策 |
|---|---|
| List 第一欄一定要 checkbox？ | 是。即使目前無批次操作也保留（未來容易加） |
| 沒有金額欄位怎麼放合計列？ | 移除 `tfoot` 整段，不要保留空合計列 |
| 必填判斷只在前端？ | Prototype 階段視覺上有 `*` 即可；validation 邏輯由 production code 處理 |
| 狀態欄要 pill 還是 stepper？ | List 用 pill；Form summary card 用 stepper |
| 響應式欄位太多被截斷？ | 橫向 scroll；**禁**隱藏關鍵欄位 |
| 規格沒提「狀態流程」是不是缺漏？ | 不一定。設定檔（master data）本來就沒有狀態機，僅 `active`；參照 `erp-setup.md §作業檔 vs 設定檔 差異速查` |

---

## §7 Examples 擴充

對應 `SKILL.md §Example`（單一範例：PM 文件轉 prototype）。本節是另外兩種常見輸入情境的完整範例。

### 範例 A：從同類舊模組複製建立新設定檔

**輸入**：「參考 `prototype/project/地點設定檔.html` 做一個區域設定檔」

**預期流程**：

1. 觸發前確認：模組中文名（區域設定檔）、profile（ERP）、來源（同類舊模組）、輸出（`prototype/project/區域設定檔.html`）
2. 階段 0 跨專案複用：從舊 .html 抽介面規格（欄位 / 狀態 / 關聯），舊模組視為 Tier 4 參考
3. 判斷為設定檔（master data，僅 active）→ 複製 `templates/setup-page.html`
4. 套用 `erp-setup.md`（差異速查 + 設定檔資料狀態矩陣）
5. List/Form 完成後跑 ERP §設定檔 Handoff Checklist

**關鍵守則**：舊模組是 Tier 4 參考；若新模組另有 PRD（Tier 2），PRD 優先於舊模組。

### 範例 B：純 chat 描述，無正式文件

**輸入**：「幫我做一個請假申請的 prototype」

**預期流程**：

1. 觸發前**主動詢問** SKILL.md §2 四項：模組中文名、profile、來源（無 → 純 chat 描述）、輸出路徑
2. 依 profile 反問該專案要求的補資料（如 ERP 還要問 Odoo model、模組分類、作業檔/設定檔類型）
3. 規格不完整時**停下來問**，**禁**自動補 PRD 沒列的欄位（R1）；不為了「補齊」而擅自添加業務邏輯
4. 規格齊全後再進階段 2 製作

**關鍵守則**：無 PRD 時最容易踩 R1（自動補欄位）和 R2（DS 預設套到使用者意圖之上）；先問再做。

---

## §8 Responsive

- **設計基準**：1440px（XL）
- **主要作業寬度**：1280px（L）
- **不支援**：< 768px（mobile）；PM 明確要求才例外
- **降級策略**：< 1024px 一律以「橫向 scroll + sticky 欄」處理；**禁**隱藏關鍵欄位（具體欄位優先級由 profile 定義）

### 四斷點對照表

| 代號 | 範圍 | 角色 | 行為 |
|---|---|---|---|
| **XL** | ≥ 1440px | 設計基準 default | 所有欄位 / 篩選器完整顯示；form 4 欄；padding 32px |
| **L** | 1280–1439px | 主要作業寬度 | 維持完整顯示；欄寬可縮減；form 4 欄（緊縮） |
| **M** | 1024–1279px | 窄桌面 / 平板橫向 | List 搜尋區可換行；DataGrid 啟用橫向捲動；form 3 欄（auto-fit 收斂） |
| **S** | 768–1023px | 平板 | List 搜尋區預設收合；DataGrid 降低欄位優先級；**form 強制 2 欄**；padding 20px |
| — | < 768px | 不支援 | 非設計範圍，建議改用行動裝置專用視圖 |

### Form Grid 預設規則

```css
.form-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 16px;
}
.form-grid--1     { grid-template-columns: minmax(300px, 1fr); }
.form-field--full { grid-column: 1 / -1; }

@media (max-width: 1024px) {
  .form-grid       { grid-template-columns: repeat(2, minmax(0, 1fr)); }
  .form-view__body { padding: 20px; }
}
```

> **為何 ≤ 1024px 強制 2 欄而非繼續 auto-fit？** 在 ~1000px 時 auto-fit 仍可能給 3 欄但每欄擠到 300px 邊界，標籤易折行；強制 2 欄能維持較寬欄位與較易讀的標籤。

### List 與 Form 的細部 RWD 規則

各 view 的斷點細節（搜尋區欄位寬 / 收合行為、Grid 欄位優先級 P0–P3、Form Footer 縱向堆疊等）由專案 profile 補完。ERP 詳見：

- `profiles/erp-components/ListSearch.md → RWD 4 斷點對照`
- `profiles/erp-components/DataGrid.md → 欄位優先級 P0–P3`
- `profiles/erp-components/FormGroup.md → 斷點對照表`
- `profiles/erp-components/FormFooter.md → RWD 斷點`

---

## §9 Tokens

色彩 / 間距 / 圓角 / 陰影 / 字級必須使用 design tokens CSS 的 token，禁 inline hex、禁 `@apply`、禁新增 token 而不更新上游。

> 以下 token 列以 **Athena Design System** 作為 source of truth（`design-tokens-base.css` + `design-tokens-athena.css` + `colors_and_type.css`）。其他專案以對應 DS 的同名 token 為準；profile 內若用到此清單未列的 token 須在 profile 註明來源。

### 色彩 — 三層架構

DS 色彩採三層：

1. **Raw channels** (`--color-sf-*`)：comma-separated RGB（如 `--color-sf-primary: 40, 119, 238`）。**用於 `rgba()` wrapper**（半透明覆層、tint）。
2. **Semantic alias** (`--text-*` / `--bg-*` / `--border-*`)：已經 wrap 為 `rgb(...)` 的別名。**用於實心色**（文字、實心背景、邊框）。
3. **CSS variable consumer**（component 自己的 class）：盡量引用第 2 層；只有需要 alpha 時才用第 1 層。

#### 文字色

| Token | 用途 |
|---|---|
| `--text-primary` | 主要文字（near-black） |
| `--text-secondary` | 次要文字 / label（grey） |
| `--text-placeholder` | placeholder / 淡化文字 |
| `--text-inverse` | 反白文字（用於深色背景） |
| `--text-brand` | 品牌色文字（= primary blue） |
| `--text-success` / `--text-error` / `--text-warning` | 狀態色文字 |

#### 背景 / 表面

| Token | 用途 |
|---|---|
| `--bg-surface-default` | 頁面 / 卡片基本背景（白） |
| `--bg-surface-variant` | 微 tint 表面（如 input filled 背景、grid header 背景） |
| `--bg-surface-inverse` | 反色表面 |
| `--bg-primary` | 實心品牌色背景（primary 按鈕） |
| `--bg-primary-tint` | 品牌色 container tint |
| `--bg-app` | App-shell 底層背景 |

#### 邊框

| Token | 用途 |
|---|---|
| `--border-default` | 一般邊框（≈ `#D7DAE0`） |
| `--border-strong` | 強調邊框（≈ `#7F8996`，hover / focus 用） |
| `--border-focus` | Focus 邊框（= primary） |

#### Raw channels（rgba 用）

| Token | 值 | 範例 |
|---|---|---|
| `--color-sf-primary` | `40, 119, 238` | `rgba(var(--color-sf-primary), .08)` — primary 8% tint |
| `--color-sf-success` | `18, 183, 106` | `rgba(var(--color-sf-success), .12)` |
| `--color-sf-error` | `244, 73, 62` | `rgba(var(--color-sf-error), .12)` — 例如 voided pill 背景 |
| `--color-sf-warning` | `247, 144, 9` | `rgba(var(--color-sf-warning), .12)` |
| `--color-sf-primary-container` | `213, 228, 255` | container tint |

> 預先 wrap 的 alpha 變體也存在（如 `--color-sf-primary-opacity-8` = `40, 119, 238, 0.08`），可用 `rgba(var(--color-sf-primary-opacity-8))` 簡寫。

### 間距（t-shirt 命名）

| Token | 值 | 別名 |
|---|---|---|
| `--space-none` | 0 | — |
| `--space-xs` | 2px | — |
| `--space-sm` | 4px | — |
| `--space-md` | 8px | — |
| `--space-lg` | 12px | — |
| `--space-xl` | 16px | — |
| `--space-2xl` | 20px | — |
| `--space-3xl` | 24px | — |
| `--space-4xl` | 32px | — |
| `--space-5xl` | 40px | — |
| `--space-6xl` | 48px | — |
| `--space-7xl` | 56px | — |
| `--space-8xl` | 64px | — |

> `--ds-space-athena-padding-*` 為 padding 專用對應；通用情境用 `--space-*` 即可。

### 圓角

| Token | 值 |
|---|---|
| `--radius-none` | 0 |
| `--radius-xs` | 2px |
| `--radius-sm` | 4px |
| `--radius-md` | 6px |
| `--radius-lg` | 8px |
| `--radius-xl` | 12px |
| `--radius-2xl` | 16px |
| `--radius-3xl` | 18px |
| `--radius-4xl` | 20px |
| `--radius-5xl` | 24px |
| `--radius-full` | 1000px（pill / circle） |

### 陰影（Material 3 elevation）

| Token | 用途 |
|---|---|
| `--shadow-e1` | 靜止 card / dropdown 表面 |
| `--shadow-e2` | toast / snackbar |
| `--shadow-e3` | dialog |
| `--shadow-e4` | menu / popover（浮於 dialog 上方） |
| `--shadow-focus-ring` | 4px primary 16% focus ring |

> **DS 卡片預設無 shadow**（只用 1px outline-variant 邊框）。Shadow 只在 hover/active 或浮層出現。

### 字型

#### Family

- `--font-family-base`（中文）`Noto Sans TC`
- `--font-family-en` `Roboto`
- `--font-family-mono` `JetBrains Mono` / `Roboto Mono`

#### Size

| Token | 值 | 對應 Figma style 名 |
|---|---|---|
| `--font-size-xxs` | 10px | — |
| `--font-size-xs` | 11px | `body/sm` 變體 |
| `--font-size-sm` | 12px | `body/sm` |
| `--font-size-md` | 14px | `body/md` / `label/md` |
| `--font-size-lg` | 16px | `body/lg` / `heading/sm` |
| `--font-size-sf-h6` | 18px | — |
| `--font-size-sf-h5` | 20px | `heading/md` |
| `--font-size-sf-h4` | 22px | — |
| `--font-size-sf-h3` | 24px | `heading/lg` |
| `--font-size-sf-h2` | 28px | `display/md` |
| `--font-size-sf-h1` | 32px | `display/lg` |

#### Weight

| Token | 值 |
|---|---|
| `--font-weight-sf-normal` | 400 |
| `--font-weight-sf-medium` | 500 |
| `--font-weight-sf-bold` | 700 |

> Figma 的 `display/*` / `heading/*` / `body/*` / `label/*` 是**複合樣式**（size + weight + line-height + letter-spacing），對應到 CSS 時要從上表分別挑 token，或直接用 component-level utility class（如 DS components.css 內的 `.btn` 已預先綁好）。

---

## §10 元件對照（HTML prototype 階段不引外部 lib）

Prototype 階段所有元件用 **原生 HTML + Tailwind class + Vue directive** 寫成（template 已在 `templates/module-page.html` 內示範）。
**production 升級**才把這些對應到對應專案的元件庫（如 ERP 用 Syncfusion Vue 或 `Ds*` 客製元件）。

| Prototype HTML | Production 對應（依 profile） |
|---|---|
| `<input>` / `<select>` | ERP:`<ejs-textbox>` / `<ejs-dropdownlist>` |
| 自製 `<table class="grid">` | ERP:`<ejs-grid>` |
| `<dialog>` 自製 | ERP:`<ejs-dialog>` |
| `<button class="btn btn--primary">` | ERP:`<ejs-button isPrimary>` |
| `<span class="st-chip">` | ERP:`<DsStatusBadge>` |
| `<aside class="nav-rail">` | ERP:`<DsSideNavMenu>` |

> 其他專案的對應由各自 profile 補完。

---

## §11 `app.js` 起手式（reactive state + handlers）

> 共用 `app.js`，每個模組各自 mount。下例為通用骨架；專案專屬欄位（nav-rail 五項、state machine 命名等）請依 profile 調整。

```js
const { createApp, ref, reactive, computed, onMounted } = Vue

createApp({
  setup() {
    // ===== Shell（依 profile 填值） =====
    const breadcrumb = reactive({ module: '', feature: '', docNo: '' })
    const programId = ''
    const version = 'v1.0.0.0.0'
    const navItems = [/* profile 提供 */]
    const activeNav = ref('')

    // ===== View =====
    const view = ref('list')   // 'list' | 'form'

    // ===== Toast =====
    const toasts = ref([])
    const showToast = (kind, message) => {
      const id = Date.now() + Math.random()
      toasts.value.push({ id, kind, message })
      setTimeout(() => { toasts.value = toasts.value.filter(t => t.id !== id) }, 3000)
    }
    // toast / state-banner 等動態 icon：回傳完整 Iconify id（material-symbols: 集，線條用 -outline）
    const toastIcon = (kind) => ({
      success: 'material-symbols:check-circle-outline',
      warning: 'material-symbols:warning-outline',
      error:   'material-symbols:error-outline',
      info:    'material-symbols:info-outline',
    }[kind] || 'material-symbols:info-outline')
    // modal 標頭 icon（psi-transaction-page 等用）：同樣回傳完整 Iconify id
    // kind ∈ { confirm, info, warning, error, danger, deeplink }；danger 視覺同 error，deeplink 用導向 icon
    const modalIcon = (kind) => ({
      confirm:  'material-symbols:help-outline',
      info:     'material-symbols:info-outline',
      warning:  'material-symbols:warning-outline',
      error:    'material-symbols:error-outline',
      danger:   'material-symbols:error-outline',
      deeplink: 'material-symbols:arrow-outward',
    }[kind] || 'material-symbols:info-outline')

    // ===== State machine handlers（依 profile 命名） =====
    // 例:ERP profile 用 onSubmit / onApprove / onUnapprove / onVoid

    // ===== Stepper helpers（詳 profiles/erp-components/Stepper.md） =====
    // 每步 n 相對 stepCur()：n < 當前 = done（綠+check）、n = 當前 = current（藍+內白環）、之後 = pending 灰
    // lineClass(n) = 第 n 步「左側」連接線：is-done / is-current（正連向當前步）/ 灰
    // 基本型 3 步只用 draft/submitted/approved；擴充狀態機映射照抄不影響（驗收七值 / 結轉 6 值見 Stepper.md）
    const stepCur = () => ({ draft:1, submitted:2, approved:3, partial:4, received:5, settled:4, voided:3 }[form.status] || 1)
    const stepState = (n) => { const c = stepCur(); return n < c ? 'done' : n === c ? 'current' : 'pending' }
    const stepClass = (n) => { const st = stepState(n); return st === 'pending' ? '' : `stepper__step--${st}` }
    const lineClass = (n) => { const c = stepCur(); return n < c ? 'is-done' : n === c ? 'is-current' : '' }
    // 動態第 ④ 步型另加 step4Class / step4Label（見 Stepper.md §引用程式碼）；第 ④ 步永遠走 step4Class，不套 --current

    // ===== Smart Bar（ERP profile §Smart Bar `card-btn` 結構） =====
    // form.relations 形狀：[{ type, count, unit, title }, ...]
    //   count === null 的條目（如「會計傳票」）省略 count + 單位，永遠顯示
    //   count > 0 才顯示；count === 0 過濾掉
    const visibleRelations = computed(() =>
      (form.relations || []).filter(r => r.count == null || r.count > 0)
    )

    return {
      breadcrumb, programId, version, navItems, activeNav, view, toasts,
      stepState, stepClass, lineClass, visibleRelations, toastIcon, modalIcon,
    }
  }
}).mount('#app')
```

> ERP 完整版（含 navItems / state machine handlers）見 ERP repo `.claude/rules/prototype-design/PRODUCE.md` 附錄 B。

---

## §12 `app.css` 結構建議（章節順序）

```
/* 1. Reset / base                       */
/* 2. App Shell                          */
/*    .app-header / .nav-rail / info-bar */
/* 3. List View                          */
/*    .toolbar / .search-bar / .grid     */
/*    .pager / .empty-state              */
/* 4. Form View                          */
/*    .summary-card / .stepper           */
/*    .smart-bar                         */
/*    .form-section / .form-grid         */
/*    .form-tabs / .form-footer          */
/* 5. Overlay (Modal / Toast)            */
/* 6. Atoms (.btn / .chip / .pill)       */
/* 7. Responsive @1024px                 */
```

所有色彩 / 間距 / 陰影 / 字級必須引用 design tokens CSS 已定義的 CSS variables，**禁** hex、**禁** `@apply`。

