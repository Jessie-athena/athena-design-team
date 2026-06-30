---
name: Avatar（頭像）
category: 回饋與狀態
tier: lite           # 身份識別圖示，可附狀態 badge，無資料生命週期
status: ✅ 已產出
authority: 契約＝本檔；視覺值落地＝prototyper/assets/app.css（canonical CSS，值權威）
figma-node: JhcpyIEEzSChcEXMRJUiIm/16556:1053
version: v0.1
last-synced: 2026-06-30
---

> 依 `../component-doc-schema.md`（Lite 層）產出。視覺字面值（尺寸 / 字級 / 背景色）權威＝`prototyper/assets/app.css`；本檔＝契約。

## 1. 概述　📋

**使用者頭像（Avatar）**：以字母縮寫、真實照片、icon 或文字代號呈現使用者或實體身份。可附帶狀態指示器（Status）或通知計數 badge（Notification）。常用於 Comment、Message 列表、Nav 欄位、頁首使用者 icon。

## 2. Anatomy　🎨

```
.avatar（inline-flex, gap = size 決定）
  ├─ .av__container（正方形，依 Shape 設定圓角）
  │   ├─ Letter mode： 縮寫文字（1–2 字）
  │   ├─ Avatar mode： <img> 填滿容器
  │   ├─ Icon mode：  24px 圖示置中
  │   ├─ Word mode：  全名或名稱縮寫
  │   ├─ Add mode：   + icon（新增操作）
  │   └─ Count mode： 數字（群組溢位時「+N」）
  ├─ .av__status（可選）  ← 右下角在線/離線指示器
  └─ .av__notification（可選）← 右上角計數 badge
```

## 3. 視覺規格 Tokens　🎨🔗

```yaml
# ── 尺寸系列 ──
xs:     { size: "24px", text-size: "見 app.css",  radius-circle: "12px" }   # 🎨
small:  { size: "32px", text-size: "{font-size-sf-text-md} / 500",  radius-circle: "20px" }   # 🎨（Letter 14px/500）
medium: { size: "40px", text-size: "見 app.css",  radius-circle: "24px" }   # 🎨
large:  { size: "48px", text-size: "{font-size-sf-h6} / 500",  radius-circle: "32px" }        # 🎨（Letter 18px/500）
xl:     { size: "56px", text-size: "見 app.css",  radius-circle: "40px" }   # 🎨

# ── Shape 圓角 ──
shape-circle:             "按各尺寸 radius-circle（上表）"
shape-square:             "{ds-radius-none}"                                   # 🔗 0px
shape-rounded-rectangle:  "{ds-radius-large}"                                  # 🔗 8px

# ── Letter mode 色彩（統一） ──
letter-bg:   "{color-sf-primary-container-opacity65}"  # 🎨 #D5E4FF（primary 淡藍底）
letter-fg:   "{color-sf-on-primary-container}"         # 🎨 #001D5A（深色縮寫）
letter-gap:  "Small=8px, Large=16px（avatar ↔ label text）"  # 🎨

# ── Status indicator ──
status-size（Small）:  "10px"                           # 🎨 右下角
status-size（Large）:  "16px"                           # 🎨 absolute bottom-0 left=[size-10px]
status-online:         "見 app.css（綠色圓點）"         # 🎨

# ── Notification badge（右上角計數） ──
notif-size（Small）:  "16×16px"                         # 🎨 rounded-12px，top=[-4px]
notif-size（Large）:  "20×20px"                         # 🎨 absolute top=0 left=[size-10px]
notif-bg:             "{color-sf-primary}"              # 🎨
notif-fg:             "{color-sf-surface}"              # 🔗 white
notif-font:           "{font-size-sf-text-xs} / {font-weight-sf-medium}"  # 🎨 11px/500
```

## 4. Variants　🎨🔗

```yaml
# ── Figma Variants（FAI2 node 16556:1053） ──
Size=Small, Shape=Circle, Mode=Letter:
  size: "32px", radius: "20px", bg: "#D5E4FF", fg: "#001D5A", font: "14px/500"

Size=Large, Shape=Circle, Mode=Letter:
  size: "48px", radius: "32px", font: "18px/500（h6）"

Size=Extra Large, Shape=Circle, Mode=Letter:
  size: "56px"

# ── 維度矩陣 ──
Size:   Extra Small（24px）/ Small（32px）/ Medium（40px）/ Large（48px）/ Extra Large（56px）
Shape:  Circle / Square / Rounded Rectangle
Mode:   Letter / Avatar / Icon / Word / Add / Count

# ── 附加功能 Variants ──
status=ON:        "右下角在線指示器（Online / Offline）"
notification=ON:  "右上角計數 badge（未讀數）"
text=ON:          "右側顯示名稱文字（Primary）+ 次文字（Secondary）"
```

## 5. States　🎨🔗

```yaml
default:    "靜態展示（Avatar 不自帶 hover 樣式）"
hover:      "父層可加 hover 框（如 avatar group 展開）"
focus:      "可 focus 時：{ds-borderwidth-medium} focus ring"
```

Avatar 通常不獨立 focus；點擊行為由外層 `<button>` 包覆觸發。

## 9. a11y　📋

- Letter / Word mode：`aria-label="[姓名]"`。
- Avatar（圖片）mode：`alt="[姓名]"`；或 `alt=""` 若旁邊已有名稱文字。
- Add mode：`aria-label="新增成員"`。
- Count mode：`aria-label="另有 [N] 位"`。
- 通知 badge：`aria-label="[N] 則未讀"`（覆蓋在 container 上）。
- 在線狀態：`aria-label="[姓名] - 線上"` 或 `role="img"` + `aria-label`。

## 12. API / Props　📋

| Prop | Type | Default | 說明 |
|---|---|---|---|
| size | 'Extra Small' \| 'Small' \| 'Medium' \| 'Large' \| 'Extra Large' | 'Small' | 尺寸 |
| shape | 'Circle' \| 'Square' \| 'Rounded Rectangle' | 'Circle' | 形狀 |
| mode | 'Letter' \| 'Avatar' \| 'Icon' \| 'Word' \| 'Add' \| 'Count' | 'Letter' | 顯示模式 |
| textLabel | string | — | Letter / Word mode 顯示的縮寫或名稱 |
| src | string | — | Avatar mode 圖片 URL |
| status | boolean | false | 顯示在線狀態指示器 |
| notification | boolean | false | 顯示右上角計數 badge |
| text | boolean | false | 顯示右側名稱文字 |
| secondaryText | boolean | false | 顯示右側次要文字（需 `text=true`） |
| secondaryText1 | string | 'Secondary text' | 次要文字內容 |

## 13. 關聯　🔗

- Tokens：`../athena-tokens.md` §Primary-Container / §On-Primary-Container / §Radius / §Typography
- 值權威：`prototyper/assets/app.css`（canonical CSS）
- 同層：`Badge.md`（Avatar 右上角計數 badge 即 Badge 元件）；`Chips.md`（Chip 內可嵌 Avatar）
- Code：`@syncfusion/ej2-vue-avatars`（`AvatarComponent`）或客製 `.avatar` markup
