# Prototyper Pitfalls（反覆出現的審查問題）

> 此檔累積**已經被抓到、又重複出現**的審查問題。每次製作 prototype 前掃一眼，**不要再犯同樣的錯**。
>
> 與 `SKILL.md` / `profiles/*.md` 的差異:
> - SKILL / profile = 規範本身（要怎麼做才對）
> - **pitfalls = 經驗教訓**（曾經做錯、容易再錯的具體 case）
>
> 起手骨架，先空著；之後每次 reviewer 抓到反覆犯的錯就 commit 一條進來。

---

## 寫入規則

每條 pitfall **盡量包含三個欄位**:

- **症狀**:具體看到什麼錯（程式碼片段 / 視覺現象）
- **正確做法**:對照 SKILL / profile 的哪一條
- **為什麼會反覆犯**:讓未來的自己 / 別人記得避開的線索

範例格式:

```
### [yyyy-mm-dd] 簡短描述

- **症狀**:`<select v-model="filters.status"><option value="all">全部</option>...`
- **正確做法**:第一個 option 必須 `value=""`（profile/erp.md §List View 七項自檢第 2 項）
- **為什麼會反覆犯**:寫程式直覺會給「全部」一個 'all' 字串，但搜尋邏輯預期空字串作為「不過濾」
```

---

## 通用（跨專案）

<!-- 樣式、結構、技術 baseline 相關 -->

_（尚無紀錄，遇到再累積）_

---

## ERP profile

<!-- App Shell / state machine / Smart Bar / handoff 五項相關 -->

_（尚無紀錄，遇到再累積）_

---

## 維護指引

- 每次 reviewer / PM 在審查時抓到的問題，先判斷:
  - 一次性誤解 → 對話內修正即可，**不寫入此檔**
  - 已經是第 2 次以上犯同樣錯 → **必須**寫入此檔
- 寫入時先檢查是否已有同類條目可合併
- 條目過時（如規範改了、原問題不再出現）就刪除，**不要保留歷史記錄**——這不是 changelog
- 保持每條 pitfall 簡潔，能讓「下次製作時 5 秒讀懂」是最低標
