# Athena Design Tokens（實際值來源）

> 本檔是 Athena 設計系統的 **真實 token 定義**，由 `Design.md`（CSS custom properties 匯出）轉為結構化文件。
> `design-system-architect/SKILL.md` Step 1 的三層架構範例為跨專案通用示意；**Athena 專案實作一律以本檔的值為準**。
>
> **維護原則**：本檔逐一保留原始 token，不刪值。原檔同名重複定義（density / 語系切換）一律拆成並列欄位呈現，不擇一刪除。
>
> **何時用哪個 token / 哪個元件** → 見同目錄 `athena-design.md`（語意對照層）；本檔只負責「查值」。
>
> 來源主題：色彩為 `Athena_Light`；數值類（borderwidth / space / radius / font）為 `Athena_Default`，間距另含 `Athena_Comfortable`。
> Token 前綴：`--color-sf-*` / `--font-*-sf-*` 為 Syncfusion 主題層；`--ds-*` 為 Athena 自有層。

## 目錄

- [Color](#color)
  - [Series 圖表色盤](#series-圖表色盤)
  - [Surface / Background / Base](#surface--background--base)
  - [Primary](#primary)
  - [Secondary](#secondary)
  - [Tertiary](#tertiary)
  - [Status — Danger / Error](#status--danger--error)
  - [Status — Info](#status--info)
  - [Status — Success](#status--success)
  - [Status — Warning](#status--warning)
  - [On-* 前景文字色](#on--前景文字色)
  - [Outline](#outline)
  - [Inverse](#inverse)
  - [Scrim / Shadow / Transparent](#scrim--shadow--transparent)
  - [Support / White / Black 透明度疊層](#support--white--black-透明度疊層)
  - [元件情境色（Diagram / Spreadsheet / Flyout）](#元件情境色diagram--spreadsheet--flyout)
  - [`--ds-*` Athena 自有色盤](#--ds--athena-自有色盤)
- [Border Width](#border-width)
- [Space（雙密度）](#space雙密度)
- [Radius](#radius)
- [Shadow / Elevation](#shadow--elevation)
- [Typography](#typography)
  - [Font Family](#font-family)
  - [Font Size](#font-size)
  - [Font Weight](#font-weight)
- [待確認項（原檔疑似筆誤，未改未刪）](#待確認項原檔疑似筆誤未改未刪)

---

## Color

> 主題：`Athena_Light`。`var(...)` 表示該 token 為其他 token 的別名（alias），保留原始引用關係。

### Series 圖表色盤

| Token | Value |
|-------|-------|
| `--color-sf-series-1` | `var(--color-sf-surface-variant)` |
| `--color-sf-series-2` | `var(--color-sf-outline-variant)` |
| `--color-sf-series-3` | `var(--color-sf-outline)` |
| `--color-sf-series-4` | `var(--color-sf-on-surface-variant)` |
| `--color-sf-series-5` | `rgb(99 85 199)` |
| `--color-sf-series-6` | `rgb(0 174 224)` |
| `--color-sf-series-7` | `rgb(130 193 0)` |
| `--color-sf-series-8` | `rgb(113 7 220)` |
| `--color-sf-series-9` | `rgb(5 187 61)` |
| `--color-sf-series-10` | `rgb(210 16 32)` |
| `--color-sf-series-11` | `rgb(250 210 0)` |
| `--color-sf-series-12` | `rgb(0 133 255)` |
| `--color-sf-series-13` | `rgb(146 4 234)` |
| `--color-sf-series-14` | `rgb(8 238 155)` |

### Surface / Background / Base

| Token | Value |
|-------|-------|
| `--color-sf-surface` | `rgb(255 255 255)` |
| `--color-sf-surface-variant` | `rgb(237 240 247)` |
| `--color-sf-surface-tint-color` | `rgb(40 119 238)` |
| `--color-sf-surface-opacity1` | `rgb(255 255 255 / 0.01)` |
| `--color-sf-background` | `var(--color-sf-surface)` |
| `--color-sf-black` | `rgb(0 0 0)` |
| `--color-sf-black-white` | `rgb(0 0 0)` |
| `--color-sf-black-opacity5` | `rgb(0 0 0 / 0.05)` |
| `--color-sf-content-text-color-alt2` | `var(--color-sf-on-secondary-container)` |

### Primary

| Token | Value |
|-------|-------|
| `--color-sf-primary` | `rgb(40 119 238)` |
| `--color-sf-primary-container` | `rgb(213 228 255)` |
| `--color-sf-primary-container-opacity65` | `rgb(213 228 255)` ⚠️ 見[待確認項](#待確認項原檔疑似筆誤未改未刪) |
| `--color-sf-primary-darken-opacity-5` | `rgb(31 87 209)` |
| `--color-sf-primary-darken-opacity-10` | `rgb(26 76 183)` |
| `--color-sf-primary-opacity-5` | `rgb(40 119 238 / 0.05)` |
| `--color-sf-primary-opacity-8` | `rgb(40 119 238 / 0.08)` |
| `--color-sf-primary-opacity-11` | `rgb(40 119 238 / 0.11)` |
| `--color-sf-primary-opacity-12` | `rgb(40 119 238 / 0.12)` |
| `--color-sf-primary-opacity-14` | `rgb(40 119 238 / 0.14)` |
| `--color-sf-primary-opacity-16` | `rgb(40 119 238 / 0.16)` |
| `--color-sf-primary-btn-hover-8` | `rgb(255 255 255 / 0.08)` |
| `--color-sf-primary-btn-pressed-12` | `rgb(255 255 255 / 0.12)` |

### Secondary

| Token | Value |
|-------|-------|
| `--color-sf-secondary` | `rgb(106 111 123)` |
| `--color-sf-secondary-container` | `rgb(181 199 228)` |
| `--color-sf-secondary-bg-color` | `rgb(255 255 255)` |
| `--color-sf-secondary-btn-bg` | `rgb(255 255 255)` |
| `--color-sf-secondary-opacity0` | `rgb(106 111 123 / 0)` |
| `--color-sf-secondary-opacity12` | `rgb(106 111 123 / 0.12)` |

### Tertiary

| Token | Value |
|-------|-------|
| `--color-sf-tertiary` | `rgb(255 190 11)` |
| `--color-sf-tertiary-container` | `rgb(255 242 194)` |

### Status — Danger / Error

| Token | Value |
|-------|-------|
| `--color-sf-danger` | `rgb(244 73 62)` |
| `--color-sf-danger-text` | `rgb(255 255 255)` |
| `--color-sf-danger-darken-opacity-5` | `rgb(197 33 24)` |
| `--color-sf-danger-opacity8` | `rgb(244 73 62 / 0.08)` |
| `--color-sf-danger-opacity12` | `rgb(244 73 62 / 0.12)` |
| `--color-sf-error` | `rgb(244 73 62)` |
| `--color-sf-error-container` | `rgb(253 216 213)` |

### Status — Info

| Token | Value |
|-------|-------|
| `--color-sf-info` | `rgb(46 144 250)` |
| `--color-sf-info-container` | `rgb(224 240 255)` |
| `--color-sf-info-text` | `rgb(255 255 255)` |
| `--color-sf-info-opacity8` | `rgb(46 144 250 / 0.08)` |
| `--color-sf-info-opacity12` | `rgb(46 144 250 / 0.12)` |

### Status — Success

| Token | Value |
|-------|-------|
| `--color-sf-success` | `rgb(18 183 106)` |
| `--color-sf-success-container` | `rgb(209 250 223)` |
| `--color-sf-success-text` | `rgb(255 255 255)` |
| `--color-sf-success-darken-opacity-5` | `rgb(41 150 31)` |
| `--color-sf-success-opacity8` | `rgb(18 183 106 / 0.08)` |
| `--color-sf-success-opacity12` | `rgb(18 183 106 / 0.12)` |

### Status — Warning

| Token | Value |
|-------|-------|
| `--color-sf-warning` | `rgb(247 144 9)` |
| `--color-sf-warning-container` | `rgb(255 244 214)` |
| `--color-sf-warning-text` | `rgb(255 255 255)` |
| `--color-sf-warning-opacity8` | `rgb(247 144 9 / 0.08)` |
| `--color-sf-warning-opacity12` | `rgb(247 144 9 / 0.12)` |

### On-* 前景文字色

| Token | Value |
|-------|-------|
| `--color-sf-on-background` | `rgb(15 23 42)` |
| `--color-sf-on-primary` | `rgb(255 255 255)` |
| `--color-sf-on-primary-container` | `rgb(0 29 90)` |
| `--color-sf-on-secondary` | `rgb(255 255 255)` |
| `--color-sf-on-secondary-container` | `rgb(30 34 42)` |
| `--color-sf-on-tertiary` | `rgb(29 20 0)` |
| `--color-sf-on-tertiary-container` | `rgb(45 32 0)` |
| `--color-sf-on-error` | `rgb(255 255 255)` |
| `--color-sf-on-error-container` | `rgb(96 20 16)` |
| `--color-sf-on-info` | `rgb(255 255 255)` |
| `--color-sf-on-info-container` | `rgb(0 58 109)` |
| `--color-sf-on-success` | `rgb(255 255 255)` |
| `--color-sf-on-success-container` | `rgb(5 91 56)` |
| `--color-sf-on-warning` | `rgb(31 20 0)` |
| `--color-sf-on-warning-container` | `rgb(66 42 0)` |
| `--color-sf-on-surface` | `rgb(15 23 42)` |
| `--color-sf-on-surface-variant` | `rgb(60 74 91)` |
| `--color-sf-on-surface-variant-opacity38` | `rgb(60 74 91 / 0.38)` |
| `--color-sf-on-surface-opacity4` | `rgb(15 23 42 / 0.04)` |
| `--color-sf-on-surface-opacity5` | `rgb(15 23 42 / 0.05)` |
| `--color-sf-on-surface-opactity6` | `rgb(15 23 42 / 0.06)` ⚠️ 見[待確認項](#待確認項原檔疑似筆誤未改未刪) |
| `--color-sf-on-surface-opacity8` | `rgb(15 23 42 / 0.08)` |
| `--color-sf-on-surface-opacity12` | `rgb(15 23 42 / 0.12)` |
| `--color-sf-on-surface-opacity24` | `rgb(15 23 42 / 0.24)` |
| `--color-sf-on-surface-opacity38` | `rgb(15 23 42 / 0.38)` |

### Outline

| Token | Value |
|-------|-------|
| `--color-sf-outline` | `rgb(127 137 150)` |
| `--color-sf-outline-variant` | `rgb(215 218 224)` |

### Inverse

| Token | Value |
|-------|-------|
| `--color-sf-inverse-surface` | `rgb(48 48 52)` |
| `--color-sf-inverse-on-surface` | `rgb(242 240 244)` |
| `--color-sf-inverse-primary` | `rgb(174 198 255)` |

### Scrim / Shadow / Transparent

| Token | Value |
|-------|-------|
| `--color-sf-scrim` | `rgb(0 0 0)` |
| `--color-sf-scrim-opacity50` | `rgb(0 0 0 / 0.5)` |
| `--color-sf-shadow` | `rgb(0 0 0)` |
| `--color-sf-transparent` | `rgb(255 255 255 / 0)` |

### Support / White / Black 透明度疊層

| Token | Value |
|-------|-------|
| `--color-sf-white` | `rgb(255 255 255)` |
| `--color-sf-white-black` | `rgb(255 255 255)` |
| `--color-sf-white-opacity8` | `rgb(255 255 255 / 0.08)` |
| `--color-sf-white-opacity12` | `rgb(255 255 255 / 0.12)` |
| `--color-sf-support-opacity-8` | `rgb(255 255 255 / 0.08)` |
| `--color-sf-support-opacity-12` | `rgb(255 255 255 / 0.12)` |

### 元件情境色（Diagram / Spreadsheet / Flyout）

| Token | Value |
|-------|-------|
| `--color-sf-flyout` | `var(--color-sf-surface)` |
| `--color-sf-diagram-palette-background` | `rgb(255 255 255)` |
| `--color-sf-diagram-palette-hover-background` | `rgb(15 23 42 / 0.05)` |
| `--color-sf-diagram-palette-selection-background` | `rgb(15 23 42 / 0.12)` |
| `--color-sf-spreadsheet-gridline` | `rgb(229 234 243)` |

### `--ds-*` Athena 自有色盤

| Token | Value |
|-------|-------|
| `--ds-color-blue` | `rgb(0 133 255)` |
| `--ds-color-sky-blue` | `rgb(194 229 255)` |
| `--ds-color-green` | `rgb(5 187 61)` |
| `--ds-color-mint` | `rgb(205 244 211)` |
| `--ds-color-purple` | `rgb(113 7 220)` |
| `--ds-color-lavender` | `rgb(220 204 255)` |
| `--ds-color-red` | `rgb(210 16 32)` |
| `--ds-color-pink` | `rgb(244 195 199)` |
| `--ds-color-orange` | `rgb(255 158 66)` |
| `--ds-color-peach` | `rgb(255 224 194)` |
| `--ds-color-yellow` | `rgb(250 210 0)` |
| `--ds-color-cream` | `rgb(255 236 189)` |
| `--ds-color-neutral-dark` | `rgb(60 74 91)` |
| `--ds-color-neutral-light` | `rgb(215 218 224)` |
| `--ds-color-placeholder` | `rgb(103 113 126)` |
| `--ds-color-view` | `rgb(32 165 109)` |

---

## Border Width

> 主題：`Athena_Default`。

| Token | Value |
|-------|-------|
| `--ds-borderwidth-none` | `0px` |
| `--ds-borderwidth-small` | `1px` |
| `--ds-borderwidth-medium` | `2px` |
| `--ds-borderwidth-large` | `4px` |

---

## Space（雙密度）

> 原檔在同一份 `:root` 中先定義 `Athena_Default`，再以 `Athena_Comfortable` **覆蓋同名變數**。
> 兩套值都是有效設定（依密度模式切換），故拆為兩欄並列保留，**不擇一刪除**。

### Margin

| Token | Athena_Default | Athena_Comfortable |
|-------|---------------|--------------------|
| `--ds-space-margin-extra-small` | `2px` | `4px` |
| `--ds-space-margin-small` | `4px` | `8px` |
| `--ds-space-margin-medium` | `8px` | `12px` |
| `--ds-space-margin-large` | `12px` | `16px` |
| `--ds-space-margin-extra-large` | `16px` | `20px` |
| `--ds-space-margin-2extra-large` | `20px` | `24px` |
| `--ds-space-margin-3extra-large` | `24px` | `32px` |
| `--ds-space-margin-4extra-large` | `32px` | `40px` |
| `--ds-space-margin-5extra-large` | `40px` | `48px` |
| `--ds-space-margin-6extra-large` | `48px` | `56px` |
| `--ds-space-margin-7extra-large` | `56px` | `64px` |
| `--ds-space-margin-8extra-large` | `64px` | `72px` |

### Padding

| Token | Athena_Default | Athena_Comfortable |
|-------|---------------|--------------------|
| `--ds-space-padding-extra-small` | `2px` | `4px` |
| `--ds-space-padding-small` | `4px` | `8px` |
| `--ds-space-padding-medium` | `8px` | `12px` |
| `--ds-space-padding-large` | `12px` | `16px` |
| `--ds-space-padding-extra-large` | `16px` | `20px` |
| `--ds-space-padding-2extra-large` | `20px` | `24px` |
| `--ds-space-padding-3extra-large` | `24px` | `32px` |
| `--ds-space-padding-4extra-large` | `32px` | `40px` |
| `--ds-space-padding-5extra-large` | `40px` | `48px` |
| `--ds-space-padding-6extra-large` | `48px` | `56px` |
| `--ds-space-padding-7extra-large` | `56px` | `64px` |
| `--ds-space-padding-8extra-large` | `64px` | `72px` |

---

## Radius

> 主題：`Athena_Default`。

| Token | Value |
|-------|-------|
| `--ds-radius-extra-small` | `2px` |
| `--ds-radius-small` | `4px` |
| `--ds-radius-medium` | `6px` |
| `--ds-radius-large` | `8px` |
| `--ds-radius-extra-large` | `12px` |
| `--ds-radius-2extra-large` | `16px` |
| `--ds-radius-3extra-large` | `18px` |
| `--ds-radius-4extra-large` | `20px` |
| `--ds-radius-5extra-large` | `24px` |
| `--ds-radius-6extra-large` | `32px` |
| `--ds-radius-7extra-large` | `36px` |
| `--ds-radius-8extra-large` | `40px` |
| `--ds-radius-9extra-large` | `48px` |
| `--ds-radius-10extra-large` | `1000px` |

---

## Shadow / Elevation

> 來源：FAI2 Figma Library **「Styles - Shadow」** 效果樣式集（節點 `25426:10916`，2026-06-18 完整補入）。下表 `box-shadow` 值**逐字照 Figma**（保留原多層順序與小數 alpha），未改寫。
> 命名沿用 `--ds-*` Athena 自有層慣例；Figma 原名為 `$shadow-*` / `$appbar-*-shadow`，對照如下。共 9 個（完整，非部分）。

| Token | Figma 原名 | box-shadow（逐字照 Figma） | 用途 |
|-------|-----------|--------------------------|------|
| `--ds-shadow-none` | `$shadow-none` | `0px 0px 0px 0px rgba(0, 0, 0, 0.25)` | 無陰影（佔位，保留疊放規格） |
| `--ds-shadow-sm` | `$shadow-sm` | `0px 1px 2px 0px rgba(0, 0, 0, 0.3), 0px 1px 3px 1px rgba(0, 0, 0, 0.15)` | 最小提升（hover 卡片等） |
| `--ds-shadow-md` | `$shadow-md` | `0px 2px 6px 2px rgba(0, 0, 0, 0.15), 0px 1px 2px 0px rgba(0, 0, 0, 0.3)` | 中提升（dropdown / menu） |
| `--ds-shadow-lg` | `$shadow-lg` | `0px 1px 3px 0px rgba(0, 0, 0, 0.3), 0px 4px 8px 3px rgba(0, 0, 0, 0.15)` | 大提升（popover / drawer） |
| `--ds-shadow-xl` | `$shadow-xl` | `0px 2px 3px 0px rgba(0, 0, 0, 0.3), 0px 6px 10px 4px rgba(0, 0, 0, 0.15)` | 更大提升（dialog） |
| `--ds-shadow-2xl` | `$shadow-2xl` | `0px 4px 4px 0px rgba(0, 0, 0, 0.3), 0px 8px 12px 6px rgba(0, 0, 0, 0.15)` | 最大提升（modal / 全屏 overlay） |
| `--ds-shadow-focus-ring1` | `$shadow-focus-ring1` | `0px 0px 0px 3px rgba(0, 0, 0, 1), 0px 0px 0px 1px rgba(255, 255, 255, 1)` | 鍵盤 focus 指示環 |
| `--ds-shadow-appbar-top` | `$appbar-top-shadow` | `0px -0.3px 0.9px 0px rgba(0, 0, 0, 0.1), 0px -1.6px 3.6px 0px rgba(0, 0, 0, 0.13)` | App bar 上緣陰影（向上投射） |
| `--ds-shadow-appbar-bottom` | `$appbar-bottom-shadow` | `0px 0.3px 0.9px 0px rgba(0, 0, 0, 0.1), 0px 1.6px 3.6px 0px rgba(0, 0, 0, 0.13)` | App bar 下緣陰影（向下投射） |

> 提升階梯：`none < sm < md < lg < xl < 2xl`（y 位移與模糊遞增）。`appbar-top` / `appbar-bottom` 為方向性邊緣陰影（y 為負/正），非提升階梯一員。
> **`--ds-shadow-focus-ring1`**：Figma 定義序為「黑 3px 外環 + 白 1px 內環」；**用於鍵盤 focus**，取代舊「2px primary outline」假設。落地若需「內白外黑」可見雙環，CSS 實作時把白色 1px 那層列前（視覺意圖），token 值本身以 Figma 為準保留原序。

---

## Typography

### Font Family

> 原檔以同名變數依語系切換（EN / ZH），兩值都保留。

| Token | EN | ZH |
|-------|----|----|
| `--font-family-sf-fontfamily` | `Roboto` | `Noto Sans TC` |

### Font Size

> 主題：`Athena_Default`。

| Token | Value |
|-------|-------|
| `--font-size-sf-text-xxs` | `10px` |
| `--font-size-sf-text-xs` | `11px` |
| `--font-size-sf-text-sm` | `12px` |
| `--font-size-sf-text-md` | `14px` |
| `--font-size-sf-text-lg` | `16px` |
| `--font-size-sf-h6` | `18px` |
| `--font-size-sf-h5` | `20px` |
| `--font-size-sf-h4` | `22px` |
| `--font-size-sf-h3` | `24px` |
| `--font-size-sf-h2` | `28px` |
| `--font-size-sf-h1` | `32px` |

### Font Weight

> 主題：`Athena_Default`。

| Token | Value |
|-------|-------|
| `--font-weight-sf-normal` | `400` |
| `--font-weight-sf-medium` | `500` |
| `--font-weight-sf-bold` | `700` |

---

## 待確認項（原檔疑似筆誤，未改未刪）

以下兩項在原 `Design.md` 中即如此，為避免擅自竄改 token 來源，原值保留並標注，待 DS owner 確認後再決定是否修正：

1. **`--color-sf-on-surface-opactity6`** — token 名稱中 `opactity` 疑為 `opacity` 拼錯（其餘同系列皆為 `opacity`）。值 `rgb(15 23 42 / 0.06)` 本身正常。修正名稱會影響任何已引用此 token 的程式碼，故不擅改。
2. **`--color-sf-primary-container-opacity65`** — 名稱帶 `opacity65`，但值 `rgb(213 228 255)` 與無透明度的 `--color-sf-primary-container` 完全相同、未帶 alpha。疑為原檔漏寫透明度（預期可能為 `/ 0.65`）。保留原值待確認。
