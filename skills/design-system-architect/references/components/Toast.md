---
name: Toast（Toaster）
category: 回饋與狀態
tier: full
status: ✅ 已採用
authority: ERP prototype（app.js `showToast` / `.toast-stack` / `.toast--*`）；Syncfusion `@syncfusion/ej2-vue-notifications`（`<ejs-toast>`）
figma-node: JhcpyIEEzSChcEXMRJUiIm/11767-1691
version: v0.1
last-synced: 2026-06-29   # 🎨 get_variable_defs + get_design_context + get_screenshot；紀錄見文末「Figma 補入紀錄」
---

> 依 `../component-doc-schema.md`（Full 層）產出。視覺值一律 token-reference；對不上既有 token 時標 🎨 指向 app.css。
> §3–§5 已用 Figma `get_variable_defs` + `get_design_context`（節點 `11767-1691` 及各 variant sub-node）校準；**未解 / 衝突項見文末「Figma 補入紀錄」**。

---

## 1. 概述　📋

操作結果的即時系統通知。觸發後自動在頁面角落浮現，3 秒後自動消失，不需使用者主動關閉（也可選配 close button 手動關閉）。

**何時用**：
- 動作完成回饋（「已提交」「已核准」「已作廢」）
- 非阻斷性警告（不阻止使用者繼續操作）
- 後台處理結果通知

**何時不用**：
- 需要使用者確認才能繼續的情況 → 用 `Dialog（confirm kind）`
- 需要在頁面停留供使用者閱讀的永久訊息 → 用 inline alert / banner
- 表單欄位驗證錯誤 → 用 field-level error message

**ERP 使用規範**：三種 kind（success / warning / error），3 秒自動消失。
- `success`：操作完成（提交、核准、儲存、複製）
- `warning`：解核、部分成功、需注意但不中斷流程
- `error`：操作失敗（API 錯誤、驗證失敗）

---

## 2. Anatomy　🎨

```
┌─────────────────────────────────────────────┐
│ [icon]  title text                    [✕]   │
│         [description?]                       │
│         [progress-bar?]                      │
│         [action-buttons?]                    │
└─────────────────────────────────────────────┘
```

| 子元素 | 必要性 | 說明 |
|--------|--------|------|
| `icon` | 選配（預設 ON） | 16px（Small）/ 20px（Large）；Iconify `material-symbols:info-circle`；顏色繼承前景 |
| `title` | 選配（預設 ON） | 單行文字；Medium weight |
| `close-btn` | 選配（預設 ON） | 右上角 × icon；16px（Small）/ 20px（Large） |
| `description` | 選配（預設 OFF） | 多行說明文字；14px Regular；line-height 1.5 |
| `progress-bar` | 選配（預設 OFF） | 線性進度指示；Small 4px 高 / Large 6px 高；前景色 `{color-sf-primary}` |
| `progress-wheel` | 選配（預設 OFF） | 圓形進度指示；替代 `progress-bar` |
| `action-buttons` | 選配（預設 OFF） | 最多 2 個（Submit 主要 + Close 次要）；尺寸跟隨 Toast size |

---

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── 容器（共用於所有 type） ──────────────────────────────
container:
  width: 520px                                           # 🎨 量測值，見 app.css
  radius: "{ds-radius-none}"                             # 🎨🔗 0px（Figma Numbers/Rounded Corner/None = 0）
  padding_x: "{ds-space-padding-extra-large}"            # 🔗 16px（Default）/ 20px（Comfortable）
  padding_y: "{ds-space-padding-large}"                  # 🔗 12px（Default）/ 16px（Comfortable）
  gap: "{ds-space-padding-large}"                        # 🔗 12px（icon↔content-area 間距）
  shadow_off: none                                       # 🎨 Shadow=OFF variant
  shadow_on: "{ds-shadow-lg}"                            # 🔗 Shadow=ON variant；Figma $shadow-lg 對映

# ── 文字（共用；全白系，不隨 type 變色） ─────────────────
text:
  title_color: "{color-sf-inverse-on-surface}"           # 🔗 #f2f0f4（所有 type 相同）
  title_size_small: "{font-size-sf-text-md}"             # 🔗 14px（Small；Figma Title/small）
  title_size_large: "{font-size-sf-text-lg}"             # 🔗 16px（Large；Figma Title/medium）
  title_weight: "{font-weight-sf-medium}"                # 🔗 500
  title_line_height: 1                                   # 🎨 Figma lineHeight: 1
  title_letter_spacing_small: 0                          # 🎨 Figma letterSpacing: 0
  title_letter_spacing_large: 0.1px                      # 🎨 Figma letterSpacing: 0.1（Title/medium）
  description_color: "{color-sf-inverse-on-surface}"     # 🔗 同 title
  description_size: "{font-size-sf-text-md}"             # 🔗 14px
  description_weight: "{font-weight-sf-normal}"          # 🔗 400
  description_line_height: 1.5                           # 🎨 Figma leading: 1.5
  description_tracking: 0.24px                           # 🎨 Figma tracking: 0.24px
  font_family: "{font-family-sf-fontfamily}"             # 🔗 Roboto（EN）/ Noto Sans TC（ZH）

# ── Icon / Close-btn 尺寸 ──────────────────────────────
icon:
  size_small: 16px                                       # 🎨 量測值
  size_large: 20px                                       # 🎨 量測值
  color: "{color-sf-inverse-on-surface}"                 # 🔗 繼承文字前景色（白系）

close_btn:
  size_small: 16px                                       # 🎨
  size_large: 20px                                       # 🎨
  padding_left: 10px                                     # 🎨（icon 容器 pl-[10px]）
  color: "{color-sf-inverse-on-surface}"                 # 🔗

# ── Progress Bar ──────────────────────────────────────
progress_bar:
  height_small: 4px                                      # 🎨
  height_large: 6px                                      # 🎨
  fill_color: "{color-sf-primary}"                       # 🔗 #2877ee
  track_bg: "{color-sf-primary-opacity-11}"              # 🔗 rgba(40,119,238,0.11)
  text_color: "{color-sf-surface}"                       # 🔗 white（百分比數字）

# ── 內容區 gap ─────────────────────────────────────────
content_area:
  row_gap: "{ds-space-padding-medium}"                   # 🔗 8px（title-row / description / progress-bar 之間）
  title_row_gap: "{ds-space-padding-medium}"             # 🔗 8px（title ↔ close-btn 之間）
```

---

## 4. Variants　🎨🔗

> Figma 元件集（節點 `11767-1691`）variant 軸：
> - `type`：Default / Success / Info / Warning / Error（5 種）
> - `size`：Small / Large（2 種）
> - `buttonPosition`：Button Left / Button Right（2 種；僅 action-buttons ON 時生效）
> - `shadow`：ON / OFF（2 種）
> - Props（boolean）：`icon` / `closeIcon` / `title` / `description` / `progressBar` / `progressWheel` / `button`

```yaml
# ── type 軸（容器背景色） ──────────────────────────────

default:   # 暗色中性（ERP 不主動使用；為 Figma DS 完整性保留）
  bg: "{color-sf-inverse-surface}"                       # 🔗 #303034（深灰）
  text_all: "{color-sf-inverse-on-surface}"              # 🔗 #f2f0f4

success:   # 操作完成
  bg: "{color-sf-success}"                               # 🔗 #12b76a
  text_all: "{color-sf-inverse-on-surface}"              # 🔗 #f2f0f4

info:      # 資訊通知（ERP 視需求使用）
  bg: "{color-sf-info}"                                  # 🔗 #2e90fa
  text_all: "{color-sf-inverse-on-surface}"              # 🔗 #f2f0f4

warning:   # 警告
  bg: "{color-sf-warning}"                               # 🔗 #f79009
  text_all: "{color-sf-inverse-on-surface}"              # 🔗 #f2f0f4

error:     # 錯誤
  bg: "{color-sf-danger}"                                # 🔗 #f4493e
  text_all: "{color-sf-inverse-on-surface}"              # 🔗 #f2f0f4

# ── size 軸 ─────────────────────────────────────────
small:
  height_approx: 42px                                    # 🎨 Figma 量測（title-only，無 description）
  title_font: "{font-size-sf-text-md}"                   # 🔗 14px
  icon_size: 16px                                        # 🎨
  close_btn_size: 16px                                   # 🎨
  progress_bar_h: 4px                                    # 🎨
  progress_wheel_size: 20px                              # 🎨
  action_btn_height: 32px                                # 🎨

large:
  height_approx: 50px                                    # 🎨 Figma 量測（title-only，無 description）
  title_font: "{font-size-sf-text-lg}"                   # 🔗 16px
  icon_size: 20px                                        # 🎨
  close_btn_size: 20px                                   # 🎨
  progress_bar_h: 6px                                    # 🎨
  progress_wheel_size: 22px                              # 🎨
  action_btn_height: 40px                                # 🎨

# ── shadow 軸 ────────────────────────────────────────
shadow_off:
  box_shadow: none
shadow_on:
  box_shadow: "{ds-shadow-lg}"                           # 🔗 Figma $shadow-lg（大提升）
  # ⚠️ Figma DOM 輸出為 drop-shadow()，非 box-shadow；
  # 落地實作見 app.css（drop-shadow 形式）；token 名對映正確，格式需確認

# ── action-buttons（選配）────────────────────────────
action_btn_primary:   # Submit
  bg: "{color-sf-primary}"                               # 🔗
  fg: "{color-sf-on-primary}"                            # 🔗
  border: "{ds-borderwidth-small} {color-sf-primary}"    # 🔗
  radius: "{ds-radius-small}"                            # 🔗 4px
action_btn_secondary:  # Close（白底）
  bg: "{color-sf-white}"                                 # 🔗
  fg: "{color-sf-on-surface}"                            # 🔗
  border: "{ds-borderwidth-small} {color-sf-white}"      # 🔗
  radius: "{ds-radius-small}"                            # 🔗 4px
  shadow: "drop-shadow(0px 1px 1.5px rgba(0,0,0,0.15), 0px 1px 1px rgba(0,0,0,0.3))"  # 🎨 見 app.css
```

---

## 5. States　🎨🔗

Toast 本身無 hover / active / focus 互動態（只讀元件）。生命週期以「可見性」為唯一狀態軸。

```yaml
entering:
  animation: slide-in-from-bottom + fade-in             # 📋 app.css `.toast` 進場動畫
  duration: 200ms                                       # 📋 建議值，見 app.css

visible:
  opacity: 1                                            # 正常顯示
  auto_dismiss_timer: 3000ms                            # 📋 由 app.js setTimeout 控制

leaving:
  animation: fade-out                                   # 📋 app.css；timer 到期或手動關閉觸發
  duration: 200ms                                       # 📋 建議值

dismissed:
  dom_removed: true                                     # 從 toasts[] 陣列移除，DOM 消失
```

---

## 6. Behavior 行為　📋

| 情境 | 行為 |
|------|------|
| 觸發 `showToast(kind, message)` | 推入 `toasts[]` ref，顯示於 `.toast-stack` |
| 3 秒後 | `setTimeout` 觸發，從 `toasts[]` 以 `id` 過濾移除 |
| 使用者點 ✕ | 立即從 `toasts[]` 移除（optional close btn） |
| 同時多則 | 疊加顯示（`.toast-stack` 縱向排列）；每則各自計時 |
| 最多則數 | 📋 待 DS 決定（建議 3 則；超出最舊的自動移除）—— 目前 ERP 無上限限制 |
| 頁面切換 | 跟隨 `#app` 根節點，保持顯示至各自計時結束 |
| 內容截斷 | 訊息文字過長時換行，不截斷；建議控制在 40 字內（見 §11） |

---

## 7. RWD 響應式　📋

Toast 不隨版面響應縮放：
- 固定寬度 520px（設計 canonical 值，🎨 量測）
- 置於 `position: fixed; bottom: right`，不佔文件流
- `max-width: calc(100vw - 32px)` 避免極小螢幕溢出（app.css 建議實作）
- 不做 mobile（< 768px）版本

---

## 8. Keyboard　📋

Toast 本身不可聚焦（非互動元件）：
- 鍵盤焦點不移入 Toast
- 若有 close button，`Tab` 鍵**不**自動跳入（避免打斷使用者操作流）
- 螢幕閱讀器透過 `aria-live` 自動朗讀，無需手動聚焦

---

## 9. a11y 無障礙　📋

- 容器 role：`role="alert"`（緊急）或 `role="status"`（一般資訊）
  - `success` / `error` / `warning` → `role="alert"` + `aria-live="assertive"`
  - `info` / `default` → `role="status"` + `aria-live="polite"`
- 焦點**不**因 Toast 出現而移動（不搶走使用者的 focus）
- close button 需 `aria-label="關閉通知"` 
- icon 為裝飾性：`aria-hidden="true"`
- 色彩對比：所有 type 的文字色 `{color-sf-inverse-on-surface}`（#f2f0f4）對底色，檢驗：
  - Default #303034：白文字，對比 > 10 ✅
  - Success #12b76a：🎨 需確認是否達 AA 4.5（近白文字對綠底；如不達請改用 `*-strong` token）
  - Info #2e90fa：🎨 同上，藍底白字需確認
  - Warning #f79009：🎨 橘底白字可能不達 AA，需確認；見 `reference_ds_aa_strong_tokens.md`
  - Error #f4493e：🎨 紅底白字需確認

---

## 10. 跨平台 Adaptive　📋

| 環境 | 行為 |
|------|------|
| Desktop（≥ 1280px）| 預設寬 520px，固定右下角 |
| Tablet（768–1024px）| `max-width: calc(100vw - 32px)` 收窄 |
| Mobile（< 768px）| 不主動支援；若渲染則全寬 |
| Odoo Web（ERP）| 僅 Web，無原生 App；不適用原生 Toast 元件 |

---

## 11. Content 指引　📋

| kind | 推薦文案模式 | 範例 |
|------|------------|------|
| success | `已[動詞]` / `[動詞]成功` | 「已提交」「已核准」「已複製為新單」 |
| warning | `已[動詞]，請注意…` | 「已解核」「部分資料更新失敗，其餘已完成」 |
| error | `[動詞]失敗：[原因]` | 「提交失敗：請先填寫必填欄位」 |
| info（選用）| 事實性說明 | 「列印中…」「同步完成」 |

**字數限制**：
- title-only 模式（ERP 主要用法）：建議 ≤ 20 字（避免換行）
- 含 description 模式：description ≤ 60 字；超出截斷或 scrollable

**禁止**：
- 只說「錯誤」「完成」無具體指示
- 把需要確認的操作放在 Toast（應改用 Dialog）
- 同時出現超過 3 則 Toast

---

## 12. API / Props　📋

### ERP prototype（`app.js showToast` 函式）

| 參數 | 型別 | 說明 |
|------|------|------|
| `kind` | `'success' \| 'warning' \| 'error'` | Toast 種類（ERP 限此三種） |
| `message` | `string` | 顯示文字（對應 Figma `title1` prop） |

**app.js 參考實作**：
```js
const showToast = (kind, message) => {
  const id = Date.now() + Math.random()
  toasts.value.push({ id, kind, message })
  setTimeout(() => { toasts.value = toasts.value.filter(t => t.id !== id) }, 3000)
}
const toastIcon = (kind) => ({
  success: 'material-symbols:check-circle-outline',
  warning: 'material-symbols:warning-outline',
  error:   'material-symbols:error-outline',
}[kind] || 'material-symbols:info-outline')
```

### Syncfusion `<ejs-toast>`（production 參考）

| Prop | Type | Default | 說明 |
|------|------|---------|------|
| `title` | string | — | 標題文字 |
| `content` | string | — | 內文（description） |
| `icon` | string | — | CSS class 或 icon name |
| `timeOut` | number | 5000 | 自動消失毫秒（ERP 設為 3000） |
| `showCloseButton` | boolean | false | 是否顯示關閉按鈕 |
| `showProgressBar` | boolean | false | 是否顯示進度條 |
| `position` | object | `{X:'Right',Y:'Bottom'}` | 顯示位置 |
| `cssClass` | string | — | `e-toast-success` / `-warning` / `-danger` |

---

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Status-Success / §Status-Warning / §Status-Danger / §Status-Info / §Inverse / §Shadow / §Space / §Typography
- 語意對照：`../athena-design.md` §狀態色
- Prototype 落地（class / markup / app.js 邏輯）：`prototyper/assets/app.css`（值權威）、`prototyper/profiles/erp-components/`（用法權威）
- Code：`@syncfusion/ej2-vue-notifications`（`<ejs-toast>`）；playground 參考 `syncfusion-playground/app/pages/playground/toast.vue`
- 對比警告參考：`~/.claude/projects/-Users-athena-working-ddd-ERP-System/memory/reference_ds_aa_strong_tokens.md`

---

## Figma 補入紀錄（2026-06-29）

> 來源：`get_variable_defs`（節點 `11767-1691`）+ `get_design_context`（sub-nodes：`11767:1626` Default-Small、`16535:4944` Success-Small、`16535:4867` Error-Small、`16535:5021` Warning-Small、`16535:5101` Info-Small、`11794:1843` Default-Large、`16852:17200` Default-Shadow-ON）+ `get_screenshot`（全集節點）。

**✅ 已確認對映（Figma 變數 → `athena-tokens.md`）**：

| Figma 變數 | Token |
|-----------|-------|
| `ColorSf/inverse-surface` | `{color-sf-inverse-surface}` |
| `ColorSf/inverse-on-surface` | `{color-sf-inverse-on-surface}` |
| `$success` / `ColorSf/success` | `{color-sf-success}` |
| `$warning` / `ColorSf/warning` | `{color-sf-warning}` |
| `$danger` / `ColorSf/danger` | `{color-sf-danger}` |
| `$info` / `ColorSf/info` | `{color-sf-info}` |
| `$shadow-lg` | `{ds-shadow-lg}` |
| `FontSizeSf/TextMd` | `{font-size-sf-text-md}` |
| `FontSizeSf/TextLg` | `{font-size-sf-text-lg}` |
| `FontWeightSf/Medium` | `{font-weight-sf-medium}` |
| `FontFamilySf/Fontfamily` | `{font-family-sf-fontfamily}` |
| `Numbers/Rounded Corner/None` = 0 | `{ds-radius-none}` |
| `ColorSf/primary`（progress bar） | `{color-sf-primary}` |
| `ColorSf/surface`（progress % text） | `{color-sf-surface}` |

**📐 已確認量測值（app.css 值權威）**：

| 量測項 | Small | Large |
|--------|-------|-------|
| 容器寬度 | 520px | 520px |
| 容器 padding | 12px 16px | 12px 16px（相同） |
| icon size | 16px | 20px |
| close btn size | 16px | 20px |
| progress-bar height | 4px | 6px |
| progress-wheel size | 20px | 22px |
| container 高度（title-only）| 42px | 50px |
| content-area row-gap | 8px | 8px |
| icon↔content gap | 12px | 12px |
| 容器 border-radius | 0px | 0px |

**🔧 結構確認**：
- Figma component props 含：`icon` / `closeIcon` / `title` / `description` / `progressBar` / `progressWheel` / `button`（全 boolean）；`title1` / `description1`（string）。
- action-buttons 為固定兩顆：`Submit`（primary 樣式）+ `Close`（white secondary 樣式），button position 控制對齊方向（Left / Right）。
- 所有 type 的前景文字色統一為 `ColorSf/inverse-on-surface`（#f2f0f4）——**不分色**（Figma 確認）。
- Default type 背景 = `ColorSf/inverse-surface`（#303034），與其他彩色 type 底色語意不同（暗色中性 vs 狀態色）。

**⚠️ 待確認 / Token Gap**：

1. **Shadow=ON 的 drop-shadow 格式**：`get_design_context` 輸出 `drop-shadow-[0px_4px_4px_rgba(0,0,0,0.15),0px_1px_1.5px_rgba(0,0,0,0.3)]`（CSS `drop-filter: drop-shadow`），而 `{ds-shadow-lg}` 定義為 `box-shadow`。兩者視覺效果相近但語法不同。**落地以 app.css 實作為準**；token 名稱對映仍視為 `{ds-shadow-lg}`（待 DS 確認是否統一為 drop-shadow 形式）。

2. **Warning / Success / Info / Error 白字對比**：`{color-sf-inverse-on-surface}`（#f2f0f4）對各彩色底：
   - Warning #f79009：預估對比約 2.9（不達 AA）→ 🎨 待驗算；若不達 AA 應評估改用 `--color-sf-on-warning-container` 或暗色文字。參見 `reference_ds_aa_strong_tokens.md`。
   - Success #12b76a：預估對比約 3.1（邊界值）→ 🎨 待驗算。
   - Info #2e90fa：預估對比約 4.8（接近 AA）→ 🎨 待驗算。
   - Error #f4493e：預估對比約 4.0（接近但未達 AA）→ 🎨 待驗算。

3. **Progress bar track-bg**：Figma 使用 `rgba(40,119,238, 0.11)` 硬寫（即 `{color-sf-primary-opacity-11}`），在彩色 Toast 上視覺效果待確認（理論上應用不透明白色或 `rgba(255,255,255,0.3)` 來匹配底色）。標 🎨 見 app.css 實際實作。

4. **ERP prototype 目前只用 success / warning / error**（`showToast` 三種 kind）：`default`（暗色）與 `info` 是否納入 ERP 使用情境，待 PM / DS 決定。
