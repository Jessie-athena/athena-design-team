---
name: prototyper
description: Turn an ERP module spec into a clickable prototype for reviewers — either a single-file HTML bundle (Vue 3 production CDN, no build step) or, when the user already has a Claude Design project and no Figma source exists to extract from, the content of that project's `page.html` / `*.dc.html` bundle. Use this skill whenever the user types「做 prototype」「PM 文件轉 prototype」「參考 [既有].html 做 [新模組]」「PRD 產出前端頁面」「把這份規格做成可以點的頁面」「讀取此份 PRD 並產出互動功能及前端頁面」「實作進 Claude Design」「改於 Claude Design 實現」, asks in English to "build a clickable HTML prototype from this PRD / Notion spec", "convert this requirement doc into an interactive page", or "implement this PRD into my Claude Design project", pastes a Figma frame asking for a clickable HTML conversion, references a PM doc path under `docs/notion/`, asks to convert a PRD or Notion page into an interactive prototype page, mentions targeting an existing Claude Design project (`claude.ai/design/p/...`, `page.html`, `*.dc.html`) with no Figma design to port from, or explicitly runs `/prototyper`. Skip for Figma motion / interaction design (use `figma-use` / `figma-generate-design`), production Vue SFC / Odoo Python code, pure requirement parsing without prototype output (use `requirement-analyst`), or porting an actual Figma design into an existing Claude Design project (use `figma-to-claude-design` — it owns the DesignSync read/write mechanics, but should defer to this skill's canonical CSS rules as the styling authority).
allowed-tools: Read Write Edit Glob Grep
---

# Prototyper（ERP）

> 把規格 / 同類舊模組 / chat 描述，**一步到位**轉成可給 reviewer 試玩的單檔 HTML prototype。
> 不是 Figma 動效設計，不是 production code。**單檔 HTML + Vue 3 production CDN**。

## ⚠️ 0. 先確認 skill 完整載入（每次必做）

**Claude Code（已安裝 skill）**：可直接讀 `${CLAUDE_SKILL_DIR}/...`，跳到 §1。

**claude.ai/design / 任何只能透過 URL 引用的環境**：使用者通常只貼一個 raw GitHub URL（例如本檔），這代表你**還沒讀到其他必要檔**。在動工前，**先把以下檔抓完**——缺一就會踩 [2026-05-11] AI 預設樣式 ≠ Design System 的反射陷阱：

| 必抓檔 | 用途 | Raw URL 模板 |
|---|---|---|
| `pitfalls.md` | 反覆出現的審查問題；**製作前必讀，不是製作後自檢** | `raw.githubusercontent.com/Jessie-athena/athena-design-team/main/skills/prototyper/pitfalls.md` |
| `profiles/Shared.md` | 跨專案共用 profile（**所有專案前置必讀**） | `raw.githubusercontent.com/Jessie-athena/athena-design-team/main/skills/prototyper/profiles/Shared.md` |
| `profiles/erp-transaction.md` | ERP 作業檔規則（必讀） | `raw.githubusercontent.com/Jessie-athena/athena-design-team/main/skills/prototyper/profiles/erp-transaction.md` |
| `profiles/erp-setup.md` | 模組類型為設定檔時加載 | `raw.githubusercontent.com/Jessie-athena/athena-design-team/main/skills/prototyper/profiles/erp-setup.md` |
| `profiles/erp-components/*.md` | ListSearch / DataGrid / FormGroup / FormFooter（4 份必抓）+ Stepper / SummaryCard（作業檔必抓）+ Permissions / RelBanner / Skeleton（依單據需求） | `raw.githubusercontent.com/Jessie-athena/athena-design-team/main/skills/prototyper/profiles/erp-components/<name>.md` |
| `templates/module-page.html` / `psi-transaction-page.html` / `setup-page.html` | starter template，依模組類型挑一份（財務 / 一般作業檔 → module-page；進銷存作業檔 → psi-transaction-page；設定檔 → setup-page） | `raw.githubusercontent.com/Jessie-athena/athena-design-team/main/skills/prototyper/templates/module-page.html` |
| `REFERENCE.md` | token / 元件對照 / `app.js` 起手式（按需） | `raw.githubusercontent.com/Jessie-athena/athena-design-team/main/skills/prototyper/REFERENCE.md` |

**抓取 troubleshooting**：

- `web_fetch` 被截斷時**換 `github_read_file` 工具**或改用 raw.githubusercontent.com 直接抓；不要假設第一次抓到的就是完整檔
- 抓不到時**停下來告訴使用者**「skill 沒完整載入，請改在 Claude Code 內執行或授權 GitHub 工具」；**禁**用「就照記憶或 DS 預設先寫」當 fallback——這正是反覆踩反射陷阱的源頭

## 支援檔案（按需載入）

- **跨專案共用 profile**：`${CLAUDE_SKILL_DIR}/profiles/Shared.md`（**所有專案前置必讀**；含頁面框架等共通骨架）
- **專案專屬 profile**：`${CLAUDE_SKILL_DIR}/profiles/<project>.md`（在共用 profile 之上**附加 / 覆寫**）
  - Athena ERP → `${CLAUDE_SKILL_DIR}/profiles/erp-transaction.md`（必讀，含 App Shell 覆寫 / state machine / chat handoff 5 項 / Form & List 樣式互動規則）
  - 若單據類型為**設定檔** (master data，依 `erp-transaction.md §類型判斷準則` 判定) → 另載入 `${CLAUDE_SKILL_DIR}/profiles/erp-setup.md`（含設定檔側欄、List/Form 自檢、資料狀態矩陣、刪除機制等）
  - **ERP 專案載入時，無論作業檔 / 設定檔，自動載入下列 4 份元件規格檔**（List View 與 Form View 幾乎都會用到，避免漏讀）：
    - `${CLAUDE_SKILL_DIR}/profiles/erp-components/ListSearch.md`（toolbar / search bar / RWD 收合）
    - `${CLAUDE_SKILL_DIR}/profiles/erp-components/DataGrid.md`（欄寬鎖 / sticky cell / 互動狀態 / 狀態 chip）
    - `${CLAUDE_SKILL_DIR}/profiles/erp-components/FormGroup.md`（form-grid 4 欄 / 跨欄 modifier / RWD）
    - `${CLAUDE_SKILL_DIR}/profiles/erp-components/FormFooter.md`（記錄分頁器 / 主 CTA / 狀態-按鈕矩陣 / dirty-guard）
  - **作業檔另載**（設定檔免載）：
    - `${CLAUDE_SKILL_DIR}/profiles/erp-components/Stepper.md`（狀態 Stepper：步序判定 / 動態第 ④ 步 / 驗收七值 / 庫存單 5 步含過場步（唯讀＋可編輯）/ voided-banner；設定檔無 Stepper）
    - `${CLAUDE_SKILL_DIR}/profiles/erp-components/SummaryCard.md`（Summary Card 兩種 Layout；單指標 + 4 步動態 stepper、6 值結轉模型插槽）
  - **依單據需求另載**（命中才載；對齊 `庫存模組` 標準）：
    - `${CLAUDE_SKILL_DIR}/profiles/erp-components/Permissions.md`（角色分流 / 唯讀檢視 / `perm-block` 無權限全頁遮罩）
    - `${CLAUDE_SKILL_DIR}/profiles/erp-components/RelBanner.md`（`rel-banner` 關係 / 沖銷情境橫幅）
    - `${CLAUDE_SKILL_DIR}/profiles/erp-components/Skeleton.md`（`.sk` 載入骨架）
- **反覆審查問題**：`${CLAUDE_SKILL_DIR}/pitfalls.md`（每次製作前掃一眼）
- **詳細展開（工作流明細 / 權重規則明細 / 決策題 / 完整 Examples / token / 元件對照 / `app.js` 起手式）**：`${CLAUDE_SKILL_DIR}/REFERENCE.md`
- **Starter templates**：
  - 財務 / 一般作業檔（含狀態流程，legacy `.erp-*`）：`${CLAUDE_SKILL_DIR}/templates/module-page.html`
  - 進銷存作業檔（DS 對齊 `.app-*`；交易明細 / 6 值擴充狀態機 / 單指標 Summary Card）：`${CLAUDE_SKILL_DIR}/templates/psi-transaction-page.html`
  - 設定檔（master data，僅 active true/false）：`${CLAUDE_SKILL_DIR}/templates/setup-page.html`
- **Canonical 樣式資產（產出時複製、逐字勿改）**：
  - `${CLAUDE_SKILL_DIR}/assets/app.css`——完整元件樣式（對齊 Figma `財務作業模組` 變數 + `庫存模組` 標準；icon 規則同時涵蓋 Iconify `<iconify-icon>` 與 `.material-symbols-outlined`）。複製到 bundle 的 `app.css`。
  - `${CLAUDE_SKILL_DIR}/assets/ds/`（`colors_and_type.css` + `design-tokens-base.css` + `design-tokens-athena.css`；`colors_and_type.css` 已 `@import` 另兩支 + Google Fonts）。複製到 bundle 的 `ds/`。
  - **產出流程**：複製上述資產 → 只新寫 `.html`（markup）與 `app.js`（互動）。**勿**依 `profiles/erp-components/*.md` 文字規格重寫 CSS（那些文字僅供解說 / 查 class 與行為）。

> **工具白名單**：本 skill frontmatter 的 `allowed-tools` 鎖定只用 `Read` / `Write` / `Edit` / `Glob` / `Grep`。設計用意——prototype 製作只需檔案讀寫與搜尋，**刻意不開放** `Bash` / `WebFetch` / `NotebookEdit` 等，避免製作流程被工具失控擴張。詳見 `README.md §工具白名單`。

## Instructions

### 1. 資料來源權重（衝突時的判定法）

四層權重，由上往下：

1. **SKILL.md + profile**（標 IMPORTANT 的硬限） — 永遠最高
2. **PRD**（規格 / 設計稿） — 欄位、狀態、業務邏輯
3. **Design System** — PRD 未指定的視覺
4. **其他**（舊模組、Figma frame、chat） — 結構靈感，不得當規格

派生規則（每次製作前自檢）：

- **R1 — PRD 完整性**：**禁**自動補 PRD 沒列的欄位 / List 欄 / action / status；缺漏停下來問
- **R2a — DS 不覆寫 PRD 視覺**：PRD 明確指定的視覺 > DS 預設；判斷不確定時，**先信 PRD，再回查 DS**
- **R2b — AI 反射不覆寫 DS 預設**：訓練資料中 Bootstrap / Tailwind UI Kit / generic web app 範例量遠多於企業內部 DS，沒被提醒就走反射預設（outlined input、所有按鈕都帶 icon、操作欄 hover 才浮出、卡片帶 shadow、`<input readonly>` 直接套 disabled 樣式…）。**禁**讓 AI 直覺壓過 DS；每次製作前**先讀 `pitfalls.md §通用 [2026-05-11] AI 預設樣式 ≠ Design System` 的反射對照表**，逐項對照

> **IMPORTANT:** 遇到衝突邊界情境（如「PRD 沒提但舊模組有」、「profile IMPORTANT 與 PRD 衝突」），**必讀 `REFERENCE.md §4 資料來源權重明細`**——一行版規則處理不了的，邊界表會給明確判定。

### 2. 觸發即先確認（缺一不開工）

開工前若使用者沒提供，**主動詢問**：

1. **模組中文名**（決定檔名與 `<title>`）
2. **專案 profile**（如 ERP；cwd 可推斷則略）
3. **來源**（PM 文件路徑 / 同類舊模組 / 純 chat 描述）
4. **輸出路徑**（預設 `prototype/project/<模組中文名>.html`，profile 可覆寫）
5. **交付目標**：本機單檔 HTML（預設）／ Claude Design bundle（使用者已在 claude.ai/design 建好專案、要求「實作進 Claude Design」「改於 Claude Design 實現」等）。兩者只影響 CSS 落地方式，不影響 §1 資料來源權重與 Pass 0/1/2 抽取流程；Claude Design 情境的規則見下方「交付目標 B」。

> profile 額外要問的項目（如 ERP 的 Odoo model、模組分類、作業檔/設定檔類型）由 profile 內定義。

#### 交付目標 B：Claude Design bundle

當交付目標是 Claude Design bundle（`page.html` 或具名 `*.dc.html`）時，以下規則**覆寫落地方式、不覆寫樣式來源**：

- **canonical 資產仍是唯一樣式來源，換目標不換來源**：`assets/app.css`（元件層）＋ `assets/ds/colors_and_type.css`（語義別名層——`app.css` 實際引用的 `--text-primary` / `--border-default` 等別名定義在此，本機路徑靠 `colors_and_type.css` 的 `@import` 鏈帶到，Claude Design 路徑必須另外確認這條別名鏈有落地）逐字複製，只是落地位置改變：
  - Claude Design bundle 內容檔慣例是單檔內嵌，本情境**明文豁免**「禁在 `.html` 內嵌 `<style>`」——把 canonical CSS 整段貼進 `<style>`，貼的仍是「複製」，一個字都不改；模組專屬樣式仍是複製完之後**末尾**追加 override 區塊。
  - 若該 bundle 本身已拆成多檔（既有專案曾拆出獨立 CSS 檔），優先沿用既有拆法，不要為了套這條規則硬併回單檔。
- **色彩/間距/字級 token 改吃 bundle 綁定的設計系統**：讀 bundle 的 `_ds/` 目錄取得實際 token 檔，**不要**額外把 `assets/ds/design-tokens-*.css` 也複製進去（會造成同名變數兩個來源）。動工前先確認 `_ds/` 的變數名稱與 canonical CSS 假設的一致（如 `--color-sf-*`）；對不上就是下一條的情境。
- **Token 或元件命名體系對不上時**：不要臆測映射或退回手刻，正式呼叫 `product-design-team` 走 `design-system-architect` 角色做交叉比對。
- **DesignSync 讀寫機制不屬本 skill**：bundle 結構判讀（哪些是內容檔／runtime 檔）、`finalize_plan`/`write_files`、寫入後 `get_file` 讀回逐字 diff、git 落地慣例，都是 `figma-to-claude-design` skill 的職責——本 skill 只管「canonical CSS 怎麼落進這個目標」，兩邊各司其職。

**Why**：曾有案例交付目標從本機 HTML 換成 Claude Design 後，「canonical CSS 複製」規則字面上撞上「禁內嵌 `<style>`」規則、又沒有明文例外，於是被判斷成「這條規則這裡不適用」，順勢連「複製 canonical」也一起放棄，退化成手刻 `.btn`/`.dg`/`.input`/`.modal`。手刻出來的規格（如 DataGrid 表頭 45px、資料列 50px、表頭 5% primary 底色）恰好就是 canonical 已經編碼好的數值，等於重複造輪還做不齊——覆蓋率只剩 canonical 的一半。矛盾規則沒被明文調和時，AI 容易整組放棄而不是只調和衝突的那一條。

### 3. 五階段工作流

| 階段 | 動作 |
|---|---|
| 0 | **跨專案複用**：有同類舊模組就抽介面規格直接引用，不重畫 |
| 1 | **規格抽取三段式**（Pass 0 / 1 / 2，詳見下方） |
| 2 | **製作 .html**：複製 profile 指定的 starter template → 替換 App Shell → 建 List View → 建 Form View → 補 Modal / Toast / Empty State |
| 3 | **本機審查**：跑 profile Handoff Checklist + 掃 `pitfalls.md` |
| 4 | **chat handoff**：依 profile 規範交付（ERP 5 項；通用底線 5 項見 REFERENCE） |

> 邊界判定（PRD 沒提但舊模組有 / profile 與 PRD 衝突等）與完整 Examples 見 `REFERENCE.md §5 五階段工作流明細`。

#### 階段 1 三段式：Pass 0 → Pass 1 → Pass 2（核心，**禁略過**）

**Pass 0｜找元件權威來源**

依 profile 規定的優先順序查找：
1. 該模組是否有對應的設計文件元件清單章節？（章節命名因模組不同，profile 會列清單）
   - 有 → 把該章節列出的元件當作**本模組的元件命名單一來源**
   - 無 → 落到 profile 內建的「PRD 元件詞彙 → 實作對照」最小集（如 `erp-transaction.md §PRD 元件對照 Table A`）
2. 記錄本次採用的權威來源（檔名 + 章節），handoff 時要附上

**Pass 1｜純抽取，不選元件 — 輸出五欄 schema 表給使用者確認**

把 PRD 拆成五欄表，**先給使用者校對**再進 Pass 2：

| 欄位中文 | 區塊 | 元件（PRD 標示） | 必填 | 約束 / 關聯 |

規則：
- 「元件」欄若 PRD 有列就照填（如 `TextBox` / `DropDownList`），**禁**自己編
- PRD 沒列就填 `?`，等 Pass 2 推論
- PRD 沒列的欄位 / List 欄 / action **禁止**自動補（R1 PRD 完整性原則）；缺漏即停下來問

> **IMPORTANT:** 略過「輸出 schema 表 → 使用者確認」這一步，等於跳過 PRD 抽取校對，後續若偏差就難救。

**Pass 2｜schema 查表轉實作**

對 Pass 1 確認過的 schema 每行：
- prototype HTML markup：依 profile 對照表查「Prototype HTML」欄
- handoff 對應的 production 元件：填 Pass 0 權威來源裡的元件名（如 `<TextInput>` / `<DataGrid>`）
- 若 Pass 1 元件欄為 `?`，依 profile 的「Form section 元件推論規則」推論並回填，**標記為推論**讓使用者確認

### 4. 通用硬性限制（每次輸出前自檢，違反即重做）

- **IMPORTANT:** 預設 `<html lang="zh-Hant-TW">`（多語環境由 profile 指定）。**Why**：lang 屬性決定瀏覽器字型回退、斷字規則、螢幕閱讀器發音；錯設 `en` 時中文常被誤套西文字型，行高與標點間距整批跑掉
- **IMPORTANT:** CSS 載入順序：design tokens CSS → `app.css`；icon 改用 **Iconify web component**，在 `<head>` 以 `<script src="https://code.iconify.design/iconify-icon/2.1.0/iconify-icon.min.js"></script>` 載入（這是 JS web component，不是 CSS，見下方 icon 條），**不再載 Material Symbols 字型**。**Why**：tokens 必須先載入才能被後續 stylesheet 引用；`app.css` 寫應用層覆寫必須最後，否則 token 變數抓不到值、自訂樣式被 DS 預設蓋掉；Iconify script 早於 body 載入，custom element 才能在首次渲染前定義好
- **IMPORTANT:** Vue 3 production CDN，**禁**引入其他 UI library。**Why**：prototype 用途是「reviewer 點一下開檔即試玩」，多加 library 增加下載 / 環境配置成本；視覺由 DS 已涵蓋，多餘 library 反成視覺噪音與整合阻力
- **IMPORTANT:** `app.css` 與 `ds/`（token）一律**從 skill 的 `${CLAUDE_SKILL_DIR}/assets/` 逐字複製，禁止自行重寫**——`assets/app.css` 是已對齊 Figma／標準的 canonical 元件樣式（完整、selector 覆蓋率 100%）。模組**專屬**樣式才在複製後的 `app.css` **末尾追加 override 區塊**（不得改動 canonical 規則）。互動寫到 `app.js`；**禁**在 `.html` 內嵌 `<style>` / `<script>`（CDN 與引用 `app.js` 的 `<script src>` 例外）。**Why**：實測「依文字規格重寫 app.css」selector 覆蓋率僅約標準一半、容器級數值（如 summary-card padding／漸層底）大量漂移——樣式準確對齊只能靠複製 canonical CSS，文字規格僅供解說。三檔分離仍利於後續 port 到 production Vue SFC
- **IMPORTANT:** Icon 一律用 **Iconify web component**（`<iconify-icon>`），且**只用 `material-symbols:` 圖示集**——它渲染的就是 DS 指定的 Material Symbols 同一套字形，只是換成 web component 載入，**禁**另引 mdi / lucide / tabler 等其他 icon set。**Why**：DS 仍是「用哪個 glyph、實心還是線條」的唯一來源，Iconify 只是 prototype 的交付機制；限定 `material-symbols:` 前綴才不會讓 prototype 視覺偏離 DS、避免混搭。寫法：
  - **標記**：`<iconify-icon icon="material-symbols:edit-outline" width="24"></iconify-icon>`；icon-only 按鈕仍需 `aria-label`
  - **實心 vs 線條是 DS 的決定，不要自己反射選**：對應 DS 的 FILL 1 / 0 —— 實心 = 無後綴（`material-symbols:visibility`）、線條 = 加 `-outline`（`material-symbols:visibility-outline`）。
    - **Athena DS 硬規則:App shell chrome 一律實心(FILL 1,無後綴)**——Header 的 首頁 / 通知 / 設定、Nav-rail 各項都用實心 glyph（`home` / `notifications` / `settings` / `account-balance` / `inventory-2` / `badge` / `cards-star`…）。**別反射套用「chrome 走線條(FILL 0)」的通則——那是本 skill 反覆踩的雷**(詳 `profiles/Shared.md §App shell icon` 與 `pitfalls.md`)。
    - **例外**:`★ 我的最愛` 是 DS 規定的選取切換態,啟用 `star`(實心)、未啟用 `star-outline`(線條),維持切換不動。
    - **內容區 icon**(操作鈕 / 空狀態 / 分頁 / 設定檔側欄等)依 DS 各自 variant(多為線條)。**不確定某個 icon 該實心還線條時回查 DS / template,別自行改動**
  - **筆畫型 icon 沒有 `-outline` 變體**（check / chevron-left / chevron-right / arrow-outward / expand-more / close / list / tune / first-page / last-page…）——直接用 base 名，硬加 `-outline` 會抓不到圖（不渲染）。**不確定某 glyph 有沒有 `-outline` 時，base 名永遠存在；以 Iconify material-symbols 集實際有的名稱為準**
  - **font glyph 名 → Iconify 名**：底線改連字號（`chevron_left`→`chevron-left`、`arrow_outward`→`arrow-outward`、`expand_more`→`expand-more`、`first_page`→`first-page`），`star_border`→`star-outline`
  - **尺寸**用 `width` / `height` 屬性或 CSS `font-size`；**顏色**靠 `currentColor` 繼承，沿用既有 token 配色（`color: rgb(var(--token))`），**禁** inline hex
  - **注意**：舊 font 版「禁用 `-outline` / `_outlined` 字尾」的規則**只適用字型**，**不適用** web component——這裡 `-outline` 正是線條變體的正確寫法，別搞反。常用對照表見 `REFERENCE.md §icon 對照`
- **IMPORTANT:** 色彩 / 間距 / 圓角 / 陰影 / 字級必須使用 design tokens；**禁** inline hex、**禁** `@apply`。**Why**：prototype → production 重用同一份 token CSS，數值一次對齊；inline hex 失去與 DS 連動，DS 更新時 prototype 顯示舊色；`@apply` 把 token 烘進 component-scoped CSS，反而切斷 token 引用鏈
- **IMPORTANT:** 寬度雙標（語義不同，勿混用）：**1440px** = Figma 設計畫布基準（design ↔ dev 規格對齊用，所有設計稿尺寸以此計）／**1280px** = prototype 預覽 viewport（template `<meta name="viewport" content="width=1280">` 已釘住，reviewer 開瀏覽器即以此寬看）。**不支援** `< 768px`（mobile）。RWD 4 斷點 XL / L / M / S 規格詳見 `REFERENCE.md §8 Responsive`；主要 grid 降欄關鍵斷點為 `@media (max-width: 1024px)` 強制 2 欄

> profile 可**附加更嚴格**規則（如 ERP 規定 state machine 命名），但**不可放寬**通用限制。
> 通用決策題（List 第一欄是否要 checkbox / 合計列處理 / 必填判斷邊界 / 響應式截斷 / 設定檔有無狀態流程等）→ `REFERENCE.md §6 通用決策題`

### 5. 輸出前 Checklist（通用最低限度）

profile 通常會擴充更嚴格的清單；本檔僅列共通底線：

- [ ] List View 與 Form View 兩種視圖都可切換
- [ ] 必填欄位有紅色 `*`
- [ ] 空狀態 / 刪除確認 / 儲存 toast 均能觸發
- [ ] 無 `@apply`、無 inline hex、無 TypeScript、無 `<style>` / `<script>` 內嵌（交付目標為 Claude Design bundle 時例外，見「交付目標 B」）
- [ ] 已掃過 `${CLAUDE_SKILL_DIR}/pitfalls.md`，沒有踩到既知地雷
- [ ] profile 的 Handoff Checklist 已逐項打勾
- [ ] R1 + R2 已自檢：未自動補 PRD 沒列的欄位；DS 預設未覆寫 PRD 明確指定的視覺
- [ ] **互動邏輯自檢**（含 modal / 驗證 / 狀態機的頁面必查；純靜態 List/Form 可略）：
  - 每個 error flag（如 `xxxErrors.foo`）都有對應的清除路徑（值改回合法、表單重置），不會永久卡在 `true`
  - 把 template 裡所有 `:class="{...}"` / `v-if` 用到的 state key 列一份清單，逐一回 JS 找賦值語句，確認 key 名**完全一致**（不是「同一組概念」，是同一個字串）——這是最常漏、也最難肉眼看出的死綁定
  - 每個 `watch()` 都處理「值被清空 / 回復預設」分支，不是只處理「新值有效」分支
  - dirty / pending 類旗標先判斷「內容是否真的變了」才設定，不要無條件在動作完成後就設 true
  - 對照 PRD 狀態機逐個 transition 確認 handler 存在，且從 UI 真的可以觸發到（不是寫了函式但沒有按鈕/連結呼叫它）

## Example

**輸入**：「這份 PM 文件 `docs/notion/出納模組/付款作業.md` 轉成 prototype」

**預期流程**：

1. §2 四項確認：模組中文名（付款作業）、profile（cwd 推斷為 ERP）、來源（已給 PM 路徑）、輸出（`prototype/project/付款作業.html`）
2. 載入 `${CLAUDE_SKILL_DIR}/profiles/Shared.md` + `${CLAUDE_SKILL_DIR}/profiles/erp-transaction.md` + `pitfalls.md`；判定類型 = 設定檔則另載 `${CLAUDE_SKILL_DIR}/profiles/erp-setup.md`
3. 依 erp-transaction.md「規格抽取表」從 PM 抽欄位、狀態、關聯、預設搜尋
4. 判斷為作業檔（有狀態流程）→ 複製 `templates/module-page.html`
5. 完成 List/Form View；跑 ERP 作業檔 Handoff Checklist
6. chat handoff 含 ERP 5 項

**關鍵守則**：依 R1（PRD 完整性），PRD 沒列的欄位 / 動作**不自動補**；缺漏就停下來問。

> 另外 2 種輸入情境的完整範例（A：從同類舊模組複製、B：純 chat 描述無正式文件）→ `REFERENCE.md §7 Examples 擴充`
