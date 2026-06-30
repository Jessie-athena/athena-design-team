---
name: ProgressBar（進度條）
category: 回饋與狀態
tier: lite           # 確定性進度顯示（圓弧或線型），無複雜互動
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: JhcpyIEEzSChcEXMRJUiIm/14002:48843
version: v0.1
last-synced: 2026-06-30
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺字面值（尺寸 / 弧線色）權威＝`prototyper/assets/app.css`；本檔＝契約。

## 1. 概述　📋

**確定性進度指示器**：以 0–100% 的數值顯示任務完成程度。有別於 Spinner（不確定等待），ProgressBar 代表「可量化的進展」。適用場景：檔案上傳、批次匯入、步驟完成度。

兩種型態：
- **Circle**（圓弧型）：視覺緊湊，適合 Card 內小型指標。
- **Linear**（線型）：適合頁面頂部全寬進度或表格內進度欄位。

## 2. Anatomy　🎨

```
Circle 型：
.progress-bar--circle（80×80px）
  ├─ SVG track（背景圓弧）
  ├─ SVG arc（進度弧，依 percentage 旋轉）
  └─ .pb__center-text（可選）← 百分比數字，center-absolute

Linear 型：
.progress-bar--linear
  ├─ .pb__track（full-width 背景條）
  └─ .pb__fill（依 percentage 寬度的前景條）
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
circle（Small）:
  size:          "80×80px"                             # 🎨 FAI2 Small 確認
  track-color:   "見 app.css"                          # 🎨 淡色圓弧（status-default 下 primary 透明）
  arc-color（Default）: "{color-sf-primary}"           # 🎨 #2877EE
  arc-color（Error）:   "{color-sf-danger}"            # 🎨 #F4493E
  arc-color（Success）: "{color-sf-success}"           # 🎨 #12B76A
  center-text-font: "16px / 400"                       # 🎨 Figma 用 Helvetica Neue；實作用系統字體
  center-text-fg:   "{color-sf-on-surface}"            # 🔗

linear（Small）:
  height:        "見 app.css"                          # 🎨 量測值待確認（通常 4–8px）
  track-color:   "見 app.css"                          # 🎨 primary 透明
  fill-color（Default）: "{color-sf-primary}"          # 🔗
  fill-color（Error）:   "{color-sf-danger}"           # 🔗
  fill-color（Success）: "{color-sf-success}"          # 🔗
  radius:        "{ds-radius-full}"                    # 🔗 pill 端點

percentage-range: "0%–100%（10% 遞增）"               # 🎨 Figma 以 10% 步進定義
```

## 4. Variants　🎨🔗

```yaml
# ── Figma Variants（FAI2 node 14002:48843） ──
Type=Circle, Size=Small, Status=Default:
  size: "80×80px", arc: "{color-sf-primary}", percentage: 0–100%

Type=Circle, Size=Small, Status=Error:
  size: "80×80px", arc: "{color-sf-danger}"

Type=Circle, Size=Small, Status=Success:
  size: "80×80px", arc: "{color-sf-success}"

Type=Linear, Size=Small, Status=Default/Error/Success:
  height: "見 app.css"

Size=Large: "Large 尺寸存在於 FAI2；量測值未取"

# ── 行為 Variants ──
value=ON:   "Circle 中心顯示百分比文字（如「40%」）"
value=OFF:  "僅顯示弧線，無文字"
indeterminate: "不確定模式：弧線持續循環（用 Spinner 替代此場景）"
```

## 5. States　🎨🔗

```yaml
Status=Default:  "進行中；primary 色弧/條"
Status=Error:    "發生錯誤；danger 色弧/條；通常在 100% 前停止"
Status=Success:  "完成；success 色弧/條；通常在 percentage=100% 時觸發"
```

## 9. a11y　📋

- `role="progressbar"` + `aria-valuemin="0"` + `aria-valuemax="100"` + `aria-valuenow="[percentage]"`。
- 同時提供 `aria-label`（如「上傳進度」）。
- 顏色不是唯一狀態區分：Error 狀態需搭配文字說明（「上傳失敗，請重試」）。
- `@prefers-reduced-motion`：停止補間動畫，直接跳至最終值。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| type | 'Circle' \| 'Linear' | 'Circle' | 進度條形態 |
| size | 'Small' \| 'Large' | 'Small' | 尺寸 |
| value | number | 0 | 進度百分比（0–100） |
| status | 'Default' \| 'Error' \| 'Success' | 'Default' | 狀態色 |
| showValue | boolean | false | Circle：是否顯示中心文字 |

> **Code**：`@syncfusion/ej2-vue-progressbar`（`ProgressBarComponent`）；`type="Circular"` 對應 Circle；`animation` 設定補間速率。

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §Danger / §Success / §Radius
- 值權威：`prototyper/assets/app.css`（canonical CSS）
- 同層：`Spinner.md`（不確定進度改用）；`Badge.md`（數量標示）
- Code：`@syncfusion/ej2-vue-progressbar`
