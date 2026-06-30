---
name: PredefinedDialog（預定義對話框）
category: 回饋與狀態
tier: full           # 多類型 Dialog（Alert / Confirm / Prompt / Image type）+ 尺寸 × Shadow 組合
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16573:786
version: v0.1
last-synced: 2026-06-30
---

> 依 `../component-doc-schema.md`（Full 層）產出。視覺字面值（寬度 / 圓角 / 按鈕高度）權威＝`prototyper/assets/app.css`；本檔＝契約（what/why/token-reference/state/a11y）。

## 1. 概述　📋

**四種預定義對話框**：Alert（單按鈕提示）、Confirm（雙按鈕確認）、Prompt（帶輸入框確認）、Image type（帶插圖確認）。提供比空白 Dialog 更快的接線方式，適用於「確認刪除」「低電量提示」「加入群組」等固定模式操作。

何時用空白 Dialog：需要自訂表單、複雜 layout 或多步驟流程 → 改 Dialog；僅需簡單的 yes/no 或文字輸入 → PredefinedDialog。

## 2. Anatomy　🎨

```
.predefined-dialog（flex column, shadow, 圓角, white bg）
  ├─ .pd__header（可選）  ← icon + title + close icon
  ├─ .pd__body            ← 訊息文字（Alert/Confirm）或 TextBox（Prompt）或插圖+文字（Image type）
  └─ .pd__footer          ← 按鈕組（right-aligned）
       ├─ [secondary btn] ← Cancel（on-surface text, transparent bg）
       └─ [primary/danger btn] ← OK / Join / Delete（color by action）
  └─ .pd__resizer（可選） ← 右下角 resize handle，12×12px
```

Image type 在 body 上方插入 140×140px 插圖區，`pt-[4px]`。

## 3. 視覺規格 Tokens　🎨🔗

```yaml
container:
  bg:             "{color-sf-surface}"                     # 🔗 white
  shadow:         "{shadow/lg}"                            # 🎨 0px 4px 8px 3px rgba(0,0,0,0.15) + 0px 1px 3px rgba(0,0,0,0.3)
  radius（default）: "{ds-radius-large}"                   # 🎨 8px（Alert / Confirm / Image type）
  radius（Prompt）: "{ds-radius-xl}"                       # 🎨 16px（rounded pill effect）
  width（Small）: "360px"                                  # 🎨
  width（Large）: "380px"                                  # 🎨

header:
  height（Small）:  "52px"                                 # 🎨 h-[52px]，含 pt-[14px]
  padding（Small）: "pt-[14px] px-[20px]"                 # 🎨
  padding（Large）: "pt-[22px] px-[24px]"                 # 🎨
  title-font（Small）: "{font-size-sf-text-lg} / 400"     # 🎨 16px/400（Roboto Normal）
  title-font（Large）: "{font-size-sf-h4} / 400"          # 🎨 22px/400，tracking 0.022px
  icon-size（Small）: "18px"                               # 🎨
  icon-size（Large）: "20px"                               # 🎨
  close-icon-size:   "16px"                                # 🎨

body:
  padding（Small）: "pt-[20px] px-[20px]"                 # 🎨
  padding（Large）: "pt-[24px] px-[24px]"                 # 🎨
  text-font（Small）: "{font-size-sf-text-sm} / 400"      # 🎨 12px/400，line-height 1.5
  text-font（Large）: "{font-size-sf-text-md} / 400"      # 🎨 14px/400，tracking 0.24px
  text-fg:           "{color-sf-on-surface}"               # 🔗

footer:
  height:          "72px"                                  # 🎨 h-[72px]，pb-[16px] pt-[20px]
  gap:             "12px"                                  # 🎨 按鈕間距
  btn-h（Small）:  "32px"                                  # 🎨 h-[32px]，px-[12px]
  btn-h（Large）:  "40px"                                  # 🎨 h-[40px]，px-[16px]
  btn-font:        "{font-size-sf-text-md} / {font-weight-sf-medium}"  # 🔗 14px/500，tracking 0.1px
  btn-radius:      "{ds-radius-md}"                        # 🔗 4px
  cancel-fg:       "{color-sf-on-surface}"                 # 🎨 #0F172A
  primary-fg:      "{color-sf-primary}"                    # 🎨 #2877EE
  danger-fg:       "{color-sf-danger}"                     # 🎨 #F4493E
  all bg:          "transparent"                           # 🎨 text-only buttons

image-area:
  size:            "140×140px"                             # 🎨 插圖容器（Image type only）
  padding:         "py-[4px] px-[24px]"                   # 🎨

resizer:
  size:            "12×12px"                               # 🎨 bottom-right corner
```

## 4. Variants / Types　🎨🔗

```yaml
# ── Figma Variants（FAI2 node 16573:786） ──
Type=Alert, Size=Small:
  width: "360px", body-text: "12px", btn: "32px h / 1 btn（OK）"

Type=Confirm, Size=Small:
  width: "360px", body-text: "12px", btns: "32px h / 2 btns（Cancel + Delete/OK）"

Type=Prompt, Size=Small:
  width: "360px", body: "TextBox（Outlined, h=40px）", btns: "32px h"
  radius: "rounded-[16px]"

Type=Alert, Size=Large:
  width: "380px", body-text: "14px", title: "22px（h4）", btn: "40px h"
  layout: "footer 左側 OK；右側 Cancel + Save 並排"

Type=Confirm, Size=Large:
  width: "380px", body-text: "14px", title: "22px（h4）", btns: "40px h"

Type=Prompt, Size=Large:
  width: "380px", body: "TextBox（Outlined, h=56px）", btns: "40px h"
  radius: "rounded-[16px]"

Type=Image type, Size=Large:
  width: "380px", height: "376px（total）", body: "插圖 140×140px + 14px 說明文字", btn: "OK only"

# ── 行為 Variants ──
shadow=ON:  "套用 {shadow/lg}"    # 預設應為 ON
shadow=OFF: "無 shadow"           # 嵌入面板時使用
icon=ON:    "header 左側顯示語意圖示（circle-info 等）"
closeIcon:  "header 右上角 ×，16px，點擊直接關閉"
resizer:    "右下角 resize handle（可拖曳調整大小）"
```

## 5. States　🎨🔗

| 狀態 | 呈現 |
|---|---|
| default | 白底 + shadow，footer 按鈕文字色各異 |
| btn-hover | 按鈕背景微透明（見 app.css）；cursor=pointer |
| btn-disabled | opacity 降低；cursor=not-allowed |
| overlay-blur | 底層內容加遮罩（由父層 Dialog Overlay 控制） |

## 6. Behavior　📋

| 互動 | 行為 |
|---|---|
| 點擊 Cancel | 關閉 Dialog，不觸發動作 |
| 點擊主按鈕（OK/Join/Delete） | 執行對應動作 → 關閉 Dialog |
| 點擊 close icon（`×`） | 同 Cancel |
| 點擊 overlay | 不關閉（預定義 Dialog 預設為強制操作） |
| Prompt TextBox | Enter 鍵＝點擊主按鈕；Escape 鍵＝Cancel |
| Resizer | 拖曳 right-bottom handle 調整 Dialog 尺寸 |

## 7. RWD　📋

PredefinedDialog 固定寬度（Small=360px / Large=380px），不做響應式縮放；超出 viewport 時由 Dialog Overlay 水平居中並確保垂直捲動可達。

## 8. Modal / Toast　📋

PredefinedDialog 本身即為 Modal；通常配合 Dialog Overlay（半透明遮罩）使用。確認成功後可搭配 Toast 反饋（`success / warning / error`）。

## 9. a11y　📋

- `role="dialog"` + `aria-modal="true"` + `aria-labelledby="[header id]"`。
- Focus trap 在 Dialog 開啟後移入並鎖定；關閉後還原至觸發元素。
- `Esc` 鍵關閉（同 Cancel）。
- Alert type：主按鈕自動 focus；Confirm：Cancel 按鈕 focus（防誤觸危險操作）。
- 危險操作按鈕（Delete）需 `aria-label="永久刪除"` 補充上下文。

## 10. 跨平台 Adaptive　📋

- **桌面**：完整 Small / Large 兩尺寸。
- **平板（M）**：Small（360px）為主；大螢幕可用 Large。
- **手機（S）**：全寬 Sheet（bottom-sheet 替代 center-dialog）；不用此元件。

## 11. Content　📋

- Alert body 文字：簡短事實陳述（≤ 2 句），無問句。
- Confirm body：問句結尾（「是否確定要…？」）。
- Prompt label：動詞開頭（「輸入您的名稱」）。
- 主按鈕：動詞（「確認」「刪除」「加入」），非名詞（「是」「好的」）。
- Image type 插圖：僅用系統內建 empty-state 插圖集，禁止自訂圖片。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| type | 'Alert' \| 'Confirm' \| 'Prompt' \| 'Image type' | 'Confirm' | 對話框類型 |
| size | 'Small' \| 'Large' | 'Small' | 尺寸規格 |
| title | string | — | 標題文字（`header=true` 時顯示） |
| header | boolean | true | 是否顯示 header 區 |
| closeIcon | boolean | false | 是否顯示 × 關閉按鈕 |
| icon | boolean | false | header 左側是否顯示語意圖示 |
| shadow | boolean | true | 是否套用 shadow-lg |
| resizer | boolean | false | 是否顯示 resize handle |
| footer | boolean | true | 是否顯示 footer 按鈕組 |
| confirmText | string | — | body 確認訊息文字（Alert/Confirm） |
| hideTextBox | boolean | true | Prompt：是否顯示輸入框（`true`=顯示） |

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §Surface / §On-* / §Danger / §Space / §Radius / §Shadow
- 值權威：`prototyper/assets/app.css`（canonical CSS）
- 同層：`Message.md`（inline 通知替代）；`Tooltip.md`（輕量提示）
- Code：`@syncfusion/ej2-vue-popups`（`DialogComponent`）；或客製 `.predefined-dialog` markup
