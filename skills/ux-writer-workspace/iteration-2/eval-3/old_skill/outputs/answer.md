# CRS 預約中心 — 多語系錯誤訊息改寫

對象訊息：CRS 預約失敗（庫存衝突）+ 入住日期必填未填
語系範圍：繁體中文 / English / Tiếng Việt（越南文）

---

## 一、多語系破版風險分析

原句：
> 「預約失敗！因為 {hotel_name} 的 {room_type} 有庫存衝突，請調整時間或日期。」

| # | 風險 | 說明 | 影響語系 |
|---|------|------|---------|
| 1 | **語序綁死** | 中文「A 的 B」結構在英文／越南文需翻轉為「B at A」「B của A」，翻譯時若照抄會語意不順；變數位置一旦寫死在句中，譯文很難重排。 | EN / VI |
| 2 | **變數內嵌、無語境** | `{hotel_name}` `{room_type}` 直接塞句中，譯者看不出詞性與單複數，越南文／英文做數量或冠詞配合時會卡。 | EN / VI |
| 3 | **長度膨脹破版** | 越南文與英文平均比繁中長 30–60%。`{hotel_name}`（飯店全名常很長）+ `{room_type}` 兩個變數疊加，單行訊息易在 toast / dialog 寬度固定處折行或截斷。 | EN / VI |
| 4 | **「！」消費級語氣** | 驚嘆號屬消費級口吻，B2B 事務情境應移除；且全形「！」與英文 "!" 混用會造成標點不一致。 | 全部 |
| 5 | **「時間或日期」語意模糊** | 訂房衝突要調整的是「入住／退房日期」或「房型」，「時間」在訂房情境多餘，翻成英文／越南文更顯空泛、不可行動。 | 全部 |
| 6 | **未拆「原因 + 下一步」** | 原因（庫存衝突）與行動（調整）擠在同一長句，三語都更難讀；拆兩段可大幅降低破版與翻譯負擔。 | 全部 |

**結論**：建議改為「短主訊息 + 變數獨立呈現 + 可行動下一步」的**結構化分段**，讓變數不綁語序、長度可彈性折行，三語都能對齊翻譯。

---

## 二、改寫後的結構（translation-ready）

採用德安 ERP `無法[動作]：[原因]` 句型；變數以結構欄位呈現，不內嵌句中。

訊息結構（給工程／i18n 用的 key 設計）：

```
crs.booking.conflict.title   主訊息（含原因，固定句型）
crs.booking.conflict.detail  衝突標的（變數獨立成行：飯店 / 房型）
crs.booking.conflict.action  下一步引導（含 inline CTA）
```

- 變數 `{hotel_name}`、`{room_type}` 抽到 `detail` 的 label–value 結構，**不放主句**，避免語序與長度問題。
- 主句與行動句不含變數，可由各語系自由語序翻譯。
- 移除「！」；採事務性語氣。

---

## 三、庫存衝突訊息 — 繁中 + 英文

### 繁體中文

```
標題：無法預約：所選房型庫存衝突
明細：飯店：『{hotel_name}』
      房型：『{room_type}』
行動：請調整入住日期或改選其他房型。
```

單行精簡版（toast 一行受限時）：
```
無法預約：『{room_type}』庫存衝突，請調整入住日期或改選房型。
```

### English

```
Title:  Booking failed: room inventory conflict
Detail: Hotel: {hotel_name}
        Room type: {room_type}
Action: Adjust the check-in date or select a different room type.
```

Single-line（toast）：
```
Booking failed: {room_type} is unavailable for the selected dates. Adjust the check-in date or choose another room type.
```

> 越南文（VI）由本地化團隊翻譯，結構同上：主句不含變數、變數獨立成行，可直接套同一組 key。

---

## 四、新增「入住日期」必填未填錯誤訊息

情境：使用者未填入住日期即送出預約 → 欄位級驗證錯誤（明確不責備）。

### 繁體中文

```
欄位錯誤：請輸入入住日期
```
（送出時的彙整提示可用：請修正紅框欄位後再預約）

### English

```
Field error: Please enter the check-in date.
```

> 越南文（VI）套同一句型由本地化翻譯。

---

## 五、套用的文案規則摘要

- 採德安標準錯誤句型 `無法[動作]：[原因]`，原因具體、可行動。
- 變數以 `『{hotel_name}』`『{room_type}』 引用慣例呈現（引用 UI／動態值，本身不可點）。
- 移除驚嘆號與消費級語氣，符合 B2B 事務性 voice。
- 主句／明細／行動三段拆分，降低三語破版與翻譯耦合。
- 行動句改為具體可行動（調整入住日期 / 改選房型），取代模糊的「時間或日期」。
- 必填錯誤採「明確不責備」tone，動詞 + 對象（請輸入入住日期）。
