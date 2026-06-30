---
name: Message（訊息通知條）
category: 回饋與狀態
tier: full           # inline 通知帶 Mode × Type × Size × Button Position × Shadow 多維組合
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16526:7435
version: v0.1
last-synced: 2026-06-30
---

> 依 `../component-doc-schema.md`（Full 層）產出。視覺字面值（背景色 / 按鈕高度 / 間距）權威＝`prototyper/assets/app.css`；本檔＝契約。

## 1. 概述　📋

**行內通知條（Inline Message）**：固定於頁面內某區域，提供持續性的操作回饋或狀態提示，不需使用者主動關閉（對比 Toast 的自動消失）。適用場景：表單驗證結果、API 呼叫回饋、操作確認後的持續提示。

何時用 Toast：操作即時回饋、3 秒後自動消失 → Toast；需持續可見直到使用者處理 → Message；需要使用者確認才繼續 → Dialog。

## 2. Anatomy　🎨

```
.message（flex row, 固定高度, 有色背景）
  ├─ .msg__severity-icon   ← 左側語意圖示，16px（可選）
  ├─ .msg__content（flex column）
  │   ├─ .msg__header-row  ← title + link "看詳細內容" + badge（計數）+ action link
  │   ├─ .msg__description ← 說明文字（可選）
  │   └─ .msg__buttons     ← Submit（primary）+ Close（secondary）按鈕組（可選）
  └─ .msg__close（可選）    ← × 關閉圖示，16px，右側
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
container:
  size（Small）:      "520×150px"                           # 🎨 w-[520px] h-[150px]
  padding（Small）:  "px-[16px] py-[12px]"                 # 🎨
  gap:               "12px"                                 # 🎨 icon ↔ content gap

  bg（Subtle）:      "rgba({color-sf-primary}, 0.05)"      # 🎨 primary-5%+white（Default type）
  bg（Ascent）:      "見 app.css .msg--ascent"             # 🎨 較濃 primary 底色
  bg（Outline）:     "{color-sf-surface}"                   # 🔗 白底 + 邊框

title:
  font:              "{font-size-sf-text-md} / {font-weight-sf-medium}"  # 🎨 14px/500
  fg:                "{color-sf-on-surface}"                # 🎨 #0F172A
  line-height:       "1.0"                                  # 🎨 leading-none（緊湊）

description:
  font:              "{font-size-sf-text-md} / 400"         # 🎨 14px/400，tracking 0.24px
  fg:                "{color-sf-on-surface}"                # 🔗
  line-height:       "1.5"                                  # 🔗

action-link:
  font:              "{font-size-sf-text-md} / {font-weight-sf-medium}"  # 🔗 14px/500
  fg:                "{color-sf-info}"                      # 🎨 #2E90FA（跨 Type 固定）
  tracking:          "0.1px"                                # 🎨

severity-icon:
  size:              "16px"                                 # 🎨
  fg:                "{color-sf-on-surface-variant}"        # 🎨 #3C4A5B

close-icon:
  size:              "16px"                                 # 🎨
  fg:                "{color-sf-on-surface-variant}"        # 🎨 #3C4A5B
  left-padding:      "20px"                                 # 🎨 pl-[20px]（與 content 分離）

buttons（Small）:
  primary-h:         "32px"                                 # 🎨 h-[32px]，px-[12px]
  secondary-h:       "32px"                                 # 🎨
  primary-bg:        "{color-sf-primary}"                   # 🎨
  primary-fg:        "{color-sf-on-primary}"                # 🔗 white
  secondary-bg:      "{color-sf-surface}"                   # 🎨 white
  secondary-fg:      "{color-sf-on-surface}"                # 🎨
  btn-font:          "{font-size-sf-text-md} / {font-weight-sf-medium}"  # 🔗 14px/500

internal-badge:
  height:            "16px"                                 # 🎨 h-[16px]，px-[8px]
  radius:            "12px"                                 # 🎨 rounded-[12px] pill
  bg:                "{color-sf-primary}"                   # 🎨
  fg:                "{color-sf-surface}"                   # 🔗 white
  font:              "{font-size-sf-text-xs} / {font-weight-sf-medium}"  # 🎨 11px/500
```

## 4. Variants / Types　🎨🔗

```yaml
# ── Figma Variants（FAI2 node 16526:7435） ──
Size=Small × Mode=Subtle × Type=Default:
  width: "520px", height: "150px", bg: "rgba(primary, 0.05)+white"

Size=Large × Mode=Subtle × Type=Default:
  width: "520px", height: "150px"（同 Small，Large 差異待確認）

# ── Mode 維度 ──
Mode=Subtle:  "主色 5% 疊白（柔和提示）"
Mode=Ascent:  "較濃有色底（醒目提示）；見 app.css"
Mode=Outline: "白底 + 1px 邊框（融入內容區）"

# ── Type 維度（語意色） ──
Type=Default: "primary 藍底"
Type=Danger:  "danger 紅底"
Type=Success: "success 綠底"
Type=Info:    "info 藍底（比 Default 更淡）"
Type=Warning: "warning 橙底"

# ── Button Position ──
Button Position=Bottom Left:  "按鈕靠左（Submit + Close 橫排）"
Button Position=Bottom Right: "按鈕靠右"

# ── 行為 Variants ──
shadow=ON:    "套用 {shadow/sm}（懸浮通知）"
shadow=OFF:   "無 shadow（內嵌於頁面）"
severityIcon: "左側顯示 type 對應圖示（info / check / warning / x-circle）"
badge:        "title 右側計數 badge（顯示未讀數量）"
action:       "title 行右側 Action link"
more:         "title 行左側「看詳細內容」link"
closeIcon:    "右側 × 按鈕（使用者主動關閉）"
```

## 5. States　🎨🔗

| 狀態 | 呈現 |
|---|---|
| default | 有色背景；所有選配元素依 props 顯示 |
| hover（close icon） | opacity 略降；cursor=pointer |
| hover（action link） | underline |
| hover（btn） | 見 app.css |
| dismissed | `v-if` 控制，隱藏整個元件 |

## 6. Behavior　📋

| 互動 | 行為 |
|---|---|
| 點擊 Action link | 跳轉詳情或觸發 handler；`@click` |
| 點擊「看詳細內容」 | 展開詳細說明（含 chevron-down icon） |
| 點擊 Submit | 呼叫 `@onSubmit`；通常後端完成後改換 Toast |
| 點擊 Close / × | `v-if` 切換 `visible=false` 隱藏元件 |
| Badge 計數 | 顯示未處理項目數；由父層管理數值 |

## 7. RWD　📋

固定寬度 520px；若容器更窄，Message 橫向 scroll。手機場景改用 Toast 或 Bottom Sheet 替代。

## 9. a11y　📋

- `role="alert"` 搭配 `aria-live="assertive"`（Error/Danger）或 `aria-live="polite"`（其他）。
- × 關閉按鈕 `aria-label="關閉通知"`。
- Action link 的 `aria-label` 應說明跳轉目標（非僅「Action」）。
- 語意圖示 `aria-hidden="true"`（純裝飾）；type 資訊需從文字或 `aria-label` 傳達。

## 10. 跨平台 Adaptive　📋

- **桌面**：完整 520px 固定寬；Small / Large 尺寸。
- **平板**：同桌面（520px 通常在容器內）。
- **手機（S）**：全寬（`w-full`）；按鈕改堆疊（stacked）佈局；非此元件優先場景。

## 11. Content　📋

- Title：≤ 30 字，說明類型（「新訊息通知」「操作失敗」）。
- Description：≤ 2 句，補充原因或後續行動。
- Action link 文字：動詞（「查看詳情」「前往修正」）；非「點我」。
- 危險類型（Danger）：清楚描述問題，不用「錯誤」兩字打頭即結束。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| size | 'Small' \| 'Large' | 'Small' | 尺寸 |
| mode | 'Subtle' \| 'Ascent' \| 'Outline' | 'Subtle' | 底色模式 |
| type | 'Default' \| 'Danger' \| 'Success' \| 'Info' \| 'Warning' | 'Default' | 語意類型 |
| title | string | — | 標題文字 |
| description | string | — | 說明文字 |
| severityIcon | boolean | true | 顯示左側語意圖示 |
| closeIcon | boolean | true | 顯示 × 關閉按鈕 |
| shadow | 'ON' \| 'OFF' | 'OFF' | 是否套用 shadow |
| button | boolean | true | 顯示底部按鈕組 |
| buttonPosition | 'Bottom Left' \| 'Bottom Right' | 'Bottom Left' | 按鈕對齊位置 |
| badge | boolean | false | 顯示計數 badge |
| action | boolean | false | 顯示 Action link |

事件：`@close` / `@submit` / `@action`

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §Surface / §On-* / §Info / §Danger / §Success / §Warning / §Space
- 值權威：`prototyper/assets/app.css`（canonical CSS）
- 同層：`PredefinedDialog.md`（強制確認場景）；`Spinner.md`（載入中搭配）
- Code：`@syncfusion/ej2-vue-notifications`（`MessageComponent`）
