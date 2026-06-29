---
name: Dialog
category: 覆疊與回饋
tier: full
status: ✅ 已採用
authority: ERP CLAUDE.md（Syncfusion Dialog, @syncfusion/ej2-vue-popups, <ejs-dialog>）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16570-1110   # 🎨 FAI2 Library · Dialog 元件集（含 Small/Large 兩 variant）
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs + get_design_context（16570:1131 / 16572:3585）+ get_screenshot；紀錄見文末「Figma 補入紀錄」
---

> 依 `../component-doc-schema.md`（Full 層）產出。視覺值一律 token-reference；對不上既有 token 時停下回報，勿臆造。
> §3–§4 已用 Figma `get_variable_defs` + `get_design_context` 校準；**未解 / 缺口項見文末「Figma 補入紀錄」**。

---

## 1. 概述　📋

Dialog 是強制使用者確認或提示跳轉的模態遮罩層。ERP 中有兩種固定 kind：

| Kind | 用途 | 典型範例 |
|---|---|---|
| `confirm` | 確認破壞性或不可逆動作 | 「確認作廢？」「確認提交？」 |
| `deeplink` | 提示跳轉至關聯單據 | 「跳轉至已核准的傳票」 |

**已淘汰**：`pick`（選擇器 Modal）— 已改以 domain 篩選自動帶入，不再使用 Dialog 實作。

**何時用 Dialog**：動作需要明確二次確認、或跳轉需說明上下文。
**何時不用 Dialog**：表單驗證錯誤（用 Toast）、靜態資訊展示（用 Drawer / Popover）、選擇清單（domain 篩選取代）。

---

## 2. Anatomy　🎨

```
Dialog
├── overlay / scrim                 # 全屏半透明遮罩（點擊可選擇是否關閉）
└── dialog-container                # 白色卡片主體
    ├── header                      # 標題列
    │   ├── [leading-icon?]         # 可選前置圖示（18px / 20px）
    │   ├── title                   # 標題文字
    │   └── close-btn               # 右上角 × 關閉按鈕（16px icon）
    ├── body                        # 內文區
    │   └── description             # 說明文字（可多行）
    └── footer                      # 動作列
        ├── cancel-btn              # 取消（transparent bg + neutral text）
        └── confirm-btn             # 確認（transparent bg + primary text）
```

> 右下角有 `resizer` 圖示（Syncfusion 內建可縮放手把）；ERP 使用時通常不需開放縮放，可透過 `allowDragging: false` / `enableResize: false` 關閉。

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── Overlay / Scrim ──────────────────────────────────────────────────────────
overlay:
  bg: "{color-sf-scrim-opacity50}"           # 🔗 rgb(0 0 0 / 0.5)；athena-tokens.md §Scrim

# ── Dialog Container ─────────────────────────────────────────────────────────
container:
  bg: "{color-sf-flyout}"                    # 🔗 alias → color-sf-surface = white
  radius: "{ds-radius-large}"                # 🎨🔗 量測 8px；Figma container rounded-[8px] → ds-radius-large = 8px
  shadow: "{ds-shadow-lg}"                   # 🎨🔗 Figma $shadow-lg（已補入 athena-tokens.md §Shadow）
                                             #    0px 1px 3px rgba(0,0,0,0.3) + 0px 4px 8px 3px rgba(0,0,0,0.15)
  overflow: clip

# ── Size Variants（量測值）────────────────────────────────────────────────────
size_small:
  width: 420px                               # 🎨
  header_height: 52px                        # 🎨 pt-14 px-20；含 icon + title + close
  body_padding: "{ds-space-padding-2extra-large}"    # 🎨🔗 20px 四邊（Default）
  footer_height: 72px                        # 🎨 pt-20 pr-20 pb-16（gap-12）

size_large:
  width: 480px                               # 🎨
  header_padding_top: 22px                   # 🎨 （無對應整數 token；值見 app.css）
  header_padding_x: "{ds-space-padding-2extra-large}"   # 🎨🔗 24px（Comfortable 密度對應值）
  body_padding_top: "{ds-space-padding-2extra-large}"   # 🔗 20px（pt-20 px-24 pb-24）
  body_padding_x: "{ds-space-padding-2extra-large}"     # 🔗 24px
  body_padding_bottom: "{ds-space-padding-3extra-large}" # 🔗 24px（pb-24；Comfortable 值）
  footer_padding_x: "{ds-space-padding-2extra-large}"   # 🎨🔗 24px（pr-24）
  footer_padding_y: "{ds-space-padding-extra-large}"    # 🎨🔗 16px（py-16）

# ── Header ───────────────────────────────────────────────────────────────────
header:
  title_color: "{color-sf-on-surface}"       # 🔗 #0f172a
  title_font_small: "{font-size-sf-text-lg} / {font-weight-sf-normal}"  # 🎨🔗 16px/400（Label/16pt/Regular，lineHeight 1.3）
  title_font_large: "{font-size-sf-h4} / {font-weight-sf-normal}"       # 🎨🔗 22px/400（Title/large，lineHeight 1.0，letterSpacing 0.1px）
  leading_icon_size_small: 18px              # 🎨
  leading_icon_size_large: 20px              # 🎨
  icon_color: "{color-sf-on-surface-variant}"  # 🔗 #3c4a5b（Icon/$icon-color）
  close_btn_size: 16px                       # 🎨
  close_btn_color: "{color-sf-on-surface-variant}"  # 🔗 同 icon color

# ── Body ─────────────────────────────────────────────────────────────────────
body:
  text_color: "{color-sf-on-surface}"        # 🔗 #0f172a（Text/$content-text-color）
  text_font_small: "{font-size-sf-text-sm} / {font-weight-sf-normal}"   # 🎨🔗 12px/400（Body Content/12pt/Regular，lineHeight 1.5）
  text_font_large: "{font-size-sf-text-md} / {font-weight-sf-normal}"   # 🎨🔗 14px/400（Body Content/14pt/Regular，lineHeight 1.5，letterSpacing 0.24px）

# ── Footer Buttons ───────────────────────────────────────────────────────────
footer:
  gap: "{ds-space-padding-large}"            # 🎨🔗 12px（gap-12）
  cancel_bg: "{color-sf-transparent}"        # 🔗 rgba(255,255,255,0)（transparent）
  cancel_fg: "{color-sf-on-surface}"         # 🔗 #0f172a（Sec_btn/$secondary-text-color）
  confirm_bg: "{color-sf-transparent}"       # 🔗 透明底
  confirm_fg: "{color-sf-primary}"           # 🔗 #2877ee（Primary_btn/$primary-bg-color 作為文字色）
  btn_radius: "{ds-radius-small}"            # 🎨🔗 Radius/RadiusSm = 4px
  btn_font: "{font-size-sf-text-md} / {font-weight-sf-medium}"  # 🎨🔗 14px/500（Label/14pt/Medium）
  btn_height_small: 32px                     # 🎨 py-7 px-12
  btn_height_large: 40px                     # 🎨 py-10 px-16
```

---

## 4. Variants　🎨

Figma 元件集（節點 `16570:1110`）共 1 個 variant 軸（**Size**），外加多個 Boolean toggle：

### 4.1 Size 軸

```yaml
small:
  width: 420px                               # 🎨
  title_style: "Label/16pt/Regular（16px/400）"
  body_style:  "Body Content/12pt/Regular（12px/400）"
  btn_height: 32px                           # 🎨
  header_padding_x: "{ds-space-padding-2extra-large}"  # 🔗 20px

large:
  width: 480px                               # 🎨
  title_style: "Title/large（22px/400）"
  body_style:  "Body Content/14pt/Regular（14px/400）"
  btn_height: 40px                           # 🎨
  header_padding_x: "{ds-space-padding-2extra-large}"  # 🔗 24px（含 Comfortable 較寬鬆）
```

### 4.2 Boolean Props（Figma component property）

| Prop | Default | 說明 |
|---|---|---|
| `header` | true | 顯示/隱藏整個 header 列 |
| `title1` | true | 顯示標題文字 |
| `icon` | false | 前置圖示（可選） |
| `closeIcon` | true | 右上角 × 按鈕 |
| `description` | true | body 內文區 |
| `footer` | true | footer 動作按鈕列 |
| `resizer` | true | 右下角縮放手把（ERP 通常關閉） |

### 4.3 ERP Kind 對應

ERP 不直接映射 Figma size 至 kind，而依情境決定：

| ERP Kind | 建議 Size | Footer 按鈕文案 | 說明 |
|---|---|---|---|
| `confirm` | Small | 取消 / 確認 | 確認作廢、確認批次操作等 |
| `deeplink` | Small | 取消 / 跳轉 | 跳轉至關聯單據 |

---

## 5. States　🎨🔗

Dialog 本體的狀態較簡單；互動態主要在 footer 按鈕（見 `Button.md §5`）：

```yaml
open:
  container: 顯示，overlay 啟用
  focus_trap: 焦點鎖定於 dialog 內部

closing:
  animation: Syncfusion 內建 fade-out；持續時間見 app.css（值權威）

overlay_click:
  default: 不關閉（ERP confirm/deeplink 均需顯式操作）
  # Syncfusion: closeOnEscape: true（Esc 可關閉）; showCloseIcon 控制右上角 ×

# Footer 按鈕態（引用 Button.md §5）
cancel_btn:
  bg: transparent
  fg: "{color-sf-on-surface}"
  hover_overlay: "{color-sf-on-surface-opacity8}"   # 🔗 Material state layer
  active_overlay: "{color-sf-on-surface-opacity12}" # 🔗
confirm_btn:
  bg: transparent
  fg: "{color-sf-primary}"
  hover_overlay: "{color-sf-primary-opacity-8}"     # 🔗
  active_overlay: "{color-sf-primary-opacity-12}"   # 🔗 （athena-tokens.md §Primary opacity-12）
```

---

## 6. Behavior 行為　📋

| 情境 | 行為 |
|---|---|
| 點擊 overlay（scrim） | 不關閉（ERP 強制使用者明確選擇） |
| 點擊右上角 × | 關閉 dialog，等同 Cancel |
| 點擊 Cancel 按鈕 | 關閉 dialog，不執行動作 |
| 點擊 Confirm/確認 按鈕 | 執行動作後關閉；若動作需等待，按鈕進入 loading 態 |
| `confirm` kind | 說明文案應明確描述後果（「將作廢 N 筆，是否繼續？」）；確認按鈕顏色依動作語意（破壞性用 danger 文字色，見下注） |
| `deeplink` kind | body 說明跳轉目的地；確認按鈕文案用「前往」/「跳轉」而非「確認」 |
| 破壞性確認（作廢/刪除） | confirm 按鈕文字色改用 `{color-sf-danger}` — 注意 Figma variant 未明示此態，屬 ERP 層規範 📋 |
| ESC 鍵 | 關閉 dialog（Syncfusion `closeOnEscape: true`） |

---

## 7. RWD 響應式　📋

Dialog 本身不做 RWD（ERP prototype 規範：不做 < 768px 版本）。
- 預設桌面 ≥ 1280px：Small(420px) / Large(480px) 均居中顯示
- Syncfusion 可設 `width: '100vw'` 於平板回退，但 ERP 未要求，暫 N/A

---

## 8. Keyboard　📋

| 鍵 | 行為 |
|---|---|
| `Esc` | 關閉 dialog（Syncfusion `closeOnEscape: true`） |
| `Tab` | 在 dialog 內部可聚焦元素循環：close icon → cancel btn → confirm btn → 回 close icon |
| `Shift+Tab` | 反向循環 |
| `Enter` / `Space` | 觸發 focused 按鈕動作 |
| `Tab` 超出末尾 | focus trap：回到 dialog 第一個可聚焦元素，不逃出至 page |

> focus trap 由 Syncfusion `<ejs-dialog>` 內建處理；自訂 modal overlay 需手動實作 `useFocusTrap`。

---

## 9. a11y 無障礙　📋

- 容器：`role="dialog"` / `aria-modal="true"`（Syncfusion 自動加）
- 標題：`aria-labelledby` 指向 title 元素 id
- 有說明文字時：`aria-describedby` 指向 body 元素 id
- 開啟時：focus 移至 dialog 第一個可聚焦元素（通常 close icon 或 cancel btn）
- 關閉後：focus 回到觸發元素（Syncfusion 需手動在 `close` 事件回寫 `triggerEl.focus()`）
- close icon：需 `aria-label="關閉"` 或 visually hidden label（icon-only button 規範，見 Button.md §9）
- overlay/scrim：`aria-hidden="true"`；不是可互動元素
- Min touch target：44×44px（footer 按鈕高度 32/40px，行動版若啟用須留 padding 補足）

---

## 10. 跨平台 Adaptive　📋

ERP 目前僅桌面（≥ 1280px），無正式行動版本規劃。N/A。

---

## 11. Content 指引　📋

### confirm kind
- **title**：動詞 + 受詞（「確認作廢」「確認提交」），不用「警告」「注意」等情緒字
- **body**：說明後果與數量（「將作廢 N 筆，此動作無法復原。」）；破壞性動作加說明不可復原
- **cancel 按鈕**：「取消」（固定）
- **confirm 按鈕**：動詞（「作廢」「提交」「刪除」），不用泛「確定」

### deeplink kind
- **title**：目的地名稱（「已產生傳票」「關聯採購單」）
- **body**：簡述跳轉後的上下文或注意事項（可省略）
- **cancel 按鈕**：「取消」
- **confirm 按鈕**：「前往」

---

## 12. API / Props　📋

> Syncfusion `<ejs-dialog>` 關鍵 props（ERP 常用子集；完整 API 見 Syncfusion 官方文件）

| Prop | Type | ERP 建議值 | 說明 |
|---|---|---|---|
| `header` | string | 模組中文名 | dialog 標題 |
| `content` | string / slot | — | body 說明文字 |
| `showCloseIcon` | boolean | `true` | 右上角 × |
| `closeOnEscape` | boolean | `true` | Esc 關閉 |
| `isModal` | boolean | `true` | 模態（開啟 overlay） |
| `allowDragging` | boolean | `false` | ERP 不開放拖移 |
| `enableResize` | boolean | `false` | ERP 不開放縮放（隱藏 resizer） |
| `width` | string | `'420px'`（Small） / `'480px'`（Large） | 對應 Figma size |
| `animationSettings` | object | `{ effect: 'Zoom' }` | Syncfusion 預設 |
| `buttons` | array | 見下方 | footer 按鈕定義 |
| `open` | event | — | dialog 開啟後回呼（可在此移動 focus） |
| `close` | event | — | dialog 關閉後回呼（focus 回觸發元素） |

**buttons 範例（confirm kind）**：
```js
buttons: [
  {
    click: () => { dialogRef.value.hide() },
    buttonModel: { content: '取消', cssClass: 'e-flat' }
  },
  {
    click: () => { onConfirm(); dialogRef.value.hide() },
    buttonModel: { content: '確認', isPrimary: true }
  }
]
```

**buttons 範例（deeplink kind）**：
```js
buttons: [
  {
    click: () => { dialogRef.value.hide() },
    buttonModel: { content: '取消', cssClass: 'e-flat' }
  },
  {
    click: () => { router.push(targetRoute); dialogRef.value.hide() },
    buttonModel: { content: '前往', isPrimary: true }
  }
]
```

---

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Scrim / §Surface / §Shadow / §Radius / §Space / §Typography
- 語意對照：`../athena-design.md`（如有）§覆疊層 / §動作色
- 上游元件：`Button.md`（footer 按鈕繼承 variant / states）
- Prototype 落地：`prototyper/profiles/erp-components/`（見 `erp-transaction.md` §Modal / Toast 段）
- Code：`@syncfusion/ej2-vue-popups`（`<ejs-dialog>`）
- Figma 元件集：[FAI2 Library · Dialog](https://www.figma.com/design/JhcpyIEEzSChcEXMRJUiIm/?node-id=16570-1110)

---

## Figma 補入紀錄（2026-06-29）

**工具**：`get_variable_defs`（節點 `16570:1110`）+ `get_design_context`（節點 `16570:1131` Small、`16572:3585` Large）+ `get_screenshot`（`16570:1110`）。

### ✅ 已確認對映（Figma 變數 → `athena-tokens.md`）

| Figma 變數 | Token | 值 |
|---|---|---|
| `ColorSf/on-surface` | `{color-sf-on-surface}` | `#0f172a` |
| `ColorSf/on-surface-variant` | `{color-sf-on-surface-variant}` | `#3c4a5b`（icon 色） |
| `ColorSf/primary` | `{color-sf-primary}` | `#2877ee`（confirm text） |
| `ColorSf/surface` / `ColorSf/flyout` | `{color-sf-flyout}` / `{color-sf-surface}` | `#ffffff` |
| `ColorSf/transparent` | `{color-sf-transparent}` | `rgba(255,255,255,0)` |
| `Radius/RadiusSm` | `{ds-radius-small}` | `4px`（btn radius） |
| `$shadow-lg` | `{ds-shadow-lg}` | 雙層 drop-shadow（已補入 tokens §Shadow） |
| `FontSizeSf/TextLg` | `{font-size-sf-text-lg}` | `16px`（Small title） |
| `FontSizeSf/H4` | `{font-size-sf-h4}` | `22px`（Large title） |
| `FontSizeSf/TextSm` | `{font-size-sf-text-sm}` | `12px`（Small body） |
| `FontSizeSf/TextMd` | `{font-size-sf-text-md}` | `14px`（Large body + btns） |
| `FontWeightSf/Normal` | `{font-weight-sf-normal}` | `400` |
| `FontWeightSf/Medium` | `{font-weight-sf-medium}` | `500` |

### ✅ 已解析（推算對映）

- **container radius 8px**：Figma code 顯示 `rounded-[8px]`，非 Radius/RadiusSm(4px)；對映 `{ds-radius-large}` = 8px（`athena-tokens.md §Radius`，完全吻合）。
- **overlay scrim**：Dialog 本體不含 scrim 元素（scrim 由 Syncfusion isModal 管理），`get_variable_defs` 未回傳；對映 `{color-sf-scrim-opacity50}`（已在 `athena-tokens.md §Scrim`）。

### ⚠️ Token Gap / 待確認

1. **header_padding_top（Large = 22px）**：介於 `--ds-space-padding-large`(12px) 與 `--ds-space-padding-extra-large`(16px / Comfortable) 之間，**無正好對應的 token**。標 🎨；CSS 字面值以 `prototyper/assets/app.css` 為準，不在設計文件就地造值。
2. **footer padding（Small：pt-20 pr-20 pb-16 vs Large：py-16 pr-24）**：`pt-20` = 20px（`padding-extra-large` Default / `padding-large` Comfortable）；`pb-16` = 16px（`padding-extra-large` Default）；數值均有 token 對應，但 Default/Comfortable 語意需在 app.css 對應密度規則中確認。
3. **`BG/$flyout-bg-color: #FFFFFF,#FFFFFF`**：two-stop 同色 gradient，落地為純白，對映 `{color-sf-flyout}` 無資訊損失。
4. **`Primary_btn/$primary-bg-color` 作為文字色**：Figma 中 Save 按鈕的 text color 引用 `$primary-bg-color`（#2877EE），非獨立 text token。落地文字色對映 `{color-sf-primary}`，語意吻合；ERP 破壞性確認改 `{color-sf-danger}` 為 ERP 層規範（Figma 無此 variant）。
5. **`title_font_large` lineHeight = 1.0**：`Title/large` 的 lineHeight 為 1（即 = font-size，相當於無行距）；`athena-tokens.md` 無 lineHeight token（Typography 僅定義 size / weight），字面值見 app.css。

### 🔧 Figma Variant 軸總結

- **Size**：Small（w=420px）/ Large（w=480px）— 唯一明確 variant 軸
- Boolean toggles：`header` / `title1` / `icon` / `closeIcon` / `description` / `footer` / `resizer`（7 個）
- **無**獨立 kind（confirm / deeplink）variant — ERP kind 為應用層規範，非 Figma 元件層設計
