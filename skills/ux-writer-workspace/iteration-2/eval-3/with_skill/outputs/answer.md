# CRS 預約失敗訊息 — 多語系審查與改寫

> 產品線：飯店 CRS（訂房中心）。CRS 屬「詞彙待補」產品線，撰寫前需與 PM 確認既有用語；本稿用語標為待確認。

## 一、原文

> 「預約失敗！因為 {hotel_name} 的 {room_type} 有庫存衝突，請調整時間或日期。」

## 二、多語系 / 破版風險分析

| # | 問題 | 為何有風險 | 處置 |
|---|------|-----------|------|
| 1 | **一句含 2 個語意變數**（`{hotel_name}`、`{room_type}`） | 違反「一句一變數」。中／英／越語序不同，雙變數內嵌會讓三語各自牽動句構，翻譯易破。 | 拆成 `Label: Value` 兩列，把變數抽出句子。 |
| 2 | **句中變數＋語序綁定**（「因為 X 的 Y 有庫存衝突」） | 越南文語序為「Y của X」、英文為「Y at X」，與中文「X 的 Y」相反；變數夾在句中會逼譯者調動詞序，產生語法錯誤或破版。 | 主訊息不放變數，原因獨立成行。 |
| 3 | **驚嘆號＋責備語氣**（「預約失敗！」） | 違反事務性原則與「嚴禁責備、不製造緊張」。B2B 工具不放驚嘆號。 | 改用阻擋句型，去驚嘆號。 |
| 4 | **句型選錯類別** | 「庫存衝突」是可預期的**驗證／阻擋類**（房況被佔用），應走 `無法[動作]：[原因]`，而非系統異常的同理安撫句。 | 套阻擋句型。 |
| 5 | **「時間或日期」語意含混** | 訂房情境的關鍵變數是「入住／退房日期」與「房型」；「時間」易誤解為時段。下一步指引要具體。 | 下一步明確指向「房型或入住日期」。 |
| 6 | **長度預算**：英文 / 越南文比中文長 30–40% | 主訊息＋兩列變數若塞進單行 toast 或窄欄會截斷。`Room type` / `Loại phòng`、`Check-in date` / `Ngày nhận phòng` 都偏長。 | 用多列結構（標題行＋明細行），各列可獨立換行，不依賴等寬。 |

**結論**：原文有真實破版風險（雙句中變數＋語序綁定為主因）。改為「標題（無變數）＋ `Label: Value` 明細列 ＋ 下一步」結構後可安全送翻。

## 三、改寫後文案（建議結構）

採「阻擋類」句型：標題行不含變數，變數全部下放成 `Label: Value` 明細，最後給可執行下一步。所有變數一句一個，無句中變數。

### 繁體中文
```
無法完成預約：所選房型在該日期已無空房
飯店：{hotel_name}
房型：{room_type}
請更換房型或調整入住日期後再試。
```

### English
```
Booking unavailable: the selected room type is sold out for these dates
Hotel: {hotel_name}
Room type: {room_type}
Choose a different room type or adjust the check-in date, then try again.
```

> 註：英文用 "Booking unavailable" 取代 "Booking failed!"，去除責備與驚嘆號，符合事務性語調；"sold out for these dates" 較 "inventory conflict" 更貼近訂房業者與旅客語感（CRS 用語待 PM 確認）。

## 四、補充：『入住日期』必填未填錯誤訊息

依 `Terminology.md` 必填模板 `請輸入 {FieldName}` / `Please enter {FieldName}`，變數置尾、無句中變數。「入住日期」為 CRS 待確認用語（暫定）。

### 繁體中文
```
請輸入『入住日期』
```

### English
```
Please enter the check-in date.
```

## 五、待 PM 確認項（CRS 詞彙待補）

- 「房型」對英文 `Room type` / 越南 `Loại phòng`、「入住日期」對 `Check-in date` / `Ngày nhận phòng` 是否為 CRS 既有標準詞。
- 「無空房 / sold out」與「庫存衝突 / inventory conflict」哪個是團隊既定用語。
- 越南文版本待詞彙定稿後補（本稿先交繁中＋英文，符合需求）。
