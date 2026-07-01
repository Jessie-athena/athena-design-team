---
name: Spinner（載入指示器）
category: 回饋與狀態
tier: lite           # 單一動畫 SVG，無資料或互動生命週期
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16995:281722
version: v0.1
last-synced: 2026-06-30
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺字面值（弧線色 / 尺寸）權威＝`prototyper/assets/app.css`；本檔＝契約。

## 1. 概述　📋

**圓弧旋轉載入指示器**：告知使用者系統正在處理中，需耐心等候。僅代表「等待中」，不顯示進度百分比（有進度需求改 ProgressBar）。常見場景：按鈕提交中、API 請求、頁面局部 loading。

## 2. Anatomy　🎨

```
.spinner（36×36px 正方形容器）
  └─ SVG 弧（CSS animation: rotate 360° infinite）
       ├─ 背景圓弧（淡色 track）
       └─ 前景弧（type 對應色，約 3/4 圓弧）
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
size:          "36×36px"                              # 🎨 FAI2 唯一尺寸（無 S/L variant）

arc-color:
  Primary:     "{color-sf-primary}"                   # 🎨 #2877EE 藍
  Secondary:   "{color-sf-on-surface}"                # 🎨 #0F172A 深色
  Info:        "{color-sf-info}"                      # 🎨 #2E90FA 亮藍
  Success:     "{color-sf-success}"                   # 🎨 #12B76A 綠
  Warning:     "{color-sf-warning}"                   # 🎨 #F79009 橙
  Danger:      "{color-sf-danger}"                    # 🎨 #F4493E 紅
  Light:       "見 app.css"                           # 🎨 淺灰色（低對比背景用）
  Dark:        "{color-sf-on-surface-variant}"        # 🎨 #3C4A5B 深灰

track-color:   "見 app.css"                           # 🎨 arc-color 低透明度
animation:     "rotate 0.8s linear infinite"          # 🎨 標準旋轉速率
```

## 4. Variants　🎨🔗

```yaml
# ── Figma Variants（FAI2 node 16995:281722） ──
Type=Primary:   size=36×36px, arc={color-sf-primary}
Type=Secondary: size=36×36px, arc={color-sf-on-surface}
Type=Info:      size=36×36px, arc={color-sf-info}
Type=Success:   size=36×36px, arc={color-sf-success}
Type=Warning:   size=36×36px, arc={color-sf-warning}
Type=Danger:    size=36×36px, arc={color-sf-danger}
Type=Light:     size=36×36px, arc=淡灰（暗色背景用）
Type=Dark:      size=36×36px, arc={color-sf-on-surface-variant}

# 尺寸 Variant（Figma 僅定義 36px，程式層可依需求縮放）
small-custom:   "24px（非 FAI2 標準，需自行縮放 SVG）"
large-custom:   "48px（同上）"
```

## 5. States　🎨🔗

```yaml
loading:   "弧線持續旋轉（animation running）"
paused:    "animation-play-state: paused（網路斷線時可暫停）"
```

## 6. 使用場景　📋

| 模式 | 適用情境 | 規則 |
|-----|---------|------|
| **全域式 Loading** | 頁面主內容操作後（如刪除清單、列表重新篩選） | Spinner 置於可互動區域中央；後方加半透明遮罩防止重複觸發 |
| **面板式 Loading** | 局部 Panel / Drawer 載入（如側欄詳情、彈窗內容） | Spinner 僅限制於該面板範圍，主內容可繼續操作 |
| **按鈕式 Loading** | 按鈕送出後等待 API 回應 | Spinner 置入按鈕內，按鈕同時 disabled 至完成；不使用頁面全覆蓋 |

> **Skeleton vs Spinner**：首次內容載入（頁面 / Tab 切換）優先用 `Skeleton`（保留版型，減少跳版）；操作型等待（送出、儲存、計算）改用 `Spinner`。

## 9. a11y　📋

- 元件根節點 `role="status"` + `aria-label="載入中"`（或動態描述「正在提交」）。
- 旋轉動畫 SVG 本身 `aria-hidden="true"`；語意由父層提供。
- 避免頁面同時出現超過 3 個 Spinner（認知負擔）。
- `@prefers-reduced-motion: reduce` → 弧線停止旋轉，改為靜態弧。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| type | 'Primary' \| 'Secondary' \| 'Info' \| 'Success' \| 'Warning' \| 'Danger' \| 'Light' \| 'Dark' | 'Primary' | 弧線色類型 |
| size | number | 36 | 容器邊長 px（依比例縮放 SVG） |
| label | string | '載入中' | `aria-label` 文字 |

> **Code**：`@syncfusion/ej2-vue-notifications`（`SpinnerComponent`）；`type` prop 對應 Syncfusion spinner type（如 `"Bootstrap"`/ `"Material"` 等，視主題而定）；或直接使用 CSS animation 的 `.spinner` 客製元素。

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary / §On-* / §Info / §Success / §Warning / §Danger
- 值權威：`prototyper/assets/app.css`（canonical CSS）
- 同層：`ProgressBar.md`（有進度百分比時改用）；`Skeleton.md`（內容佔位 loading）
- Code：`@syncfusion/ej2-vue-notifications`（SpinnerComponent）
