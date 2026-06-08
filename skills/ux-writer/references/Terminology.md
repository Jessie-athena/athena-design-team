# 多語系常用詞彙清單 (Terminology List)

> 需要跨語系（台/簡中/日/英/越）一致的介面詞彙、狀態標籤或訊息模板時，查閱本檔。
> ERP 單據專屬術語（單號/明細/傳票/草稿…）見 `erp-conventions.md`，目前以繁中為準。

## 0. 使用規範
- **一致性優先**：同一專案中，同一功能必須使用表中的統一對照詞。
- **產業慣用**：如越南語的 "Email" 實務上多直接用原字，不強制翻為本地詞彙。
- **B2B 語境**：本清單專為 SaaS、ERP、POS 等管理系統設計，語氣中性專業。

## 1. 核心介面詞彙 (Core UI Terms)
| Key (概念) | 繁體中文 | 簡體中文 | 日文 | 英文 | 越南語 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Dashboard** | 儀表板 | 仪表板 | ダッシュボード | Dashboard | Bảng điều khiển |
| **Settings** | 設定 | 设置 | 設定 | Settings | Cài đặt |
| **Home** | 首頁 | 首页 | ホーム | Home | Trang chủ |
| **Parameter** | 參數 | 参数 | パラメータ | Parameter | Tham số |
| **Profile** | 個人資料 | 个人資料 | プロフィール | Profile | Hồ sơ |

## 2. 操作動詞 (Action Verbs)
| Key (概念) | 繁體中文 | 簡體中文 | 日文 | 英文 | 越南語 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Create** | 建立 | 建立 | 新規作成 | Create | Tạo |
| **Update** | 更新 | 更新 | 更新 | Update | Cập nhật |
| **Delete** | 刪除 | 删除 | 削除 | Delete | Xóa |
| **Save** | 儲存 | 保存 | 保存 | Save | Lưu |
| **Confirm** | 確認 | 确认 | 確認 | Confirm | Xác nhận |
| **Cancel** | 取消 | 取消 | キャンセル | Cancel | Hủy |
| **Search** | 搜尋 | 搜索 | 検索 | Search | Tìm kiếm |
| **Filter** | 篩選 | 筛选 | フィルタ | Filter | Lọc |
| **Export** | 匯出 | 导出 | 書き出し | Export | Xuất |

## 3. 系統回饋與狀態 (Status & Feedback)
| Key (概念) | 繁體中文 | 簡體中文 | 日文 | 英文 | 越南語 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Success** | 成功 | 成功 | 成功 | Success | Thành công |
| **Warning** | 警告 | 警告 | 警告 | Warning | Cảnh báo |
| **Error** | 錯誤 | 错误 | エラー | Error | Lỗi |
| **NoData** | 無資料 | 无数据 | データなし | No data | Không có dữ liệu |
| **Loading** | 載入中 | 加载中 | 読み込み中 | Loading | Đang tải |

## 4. 情境化提示訊息模板 (Contextual Messaging)

### 重複性驗證 (Duplicate Check)
- **繁體中文**: `{XXX}` 代號與現有資料重複，請重新輸入。
- **簡體中文**: `{XXX}` 代码已存在，请输入其他值。
- **日文**: `{XXX}` のコードは使用されています。別の値を入力してください。
- **英文**: `{XXX}` code already exists. Please enter a different value.
- **越南語**: `{XXX}` đã tồn tại. Vui lòng nhập giá trị khác.
- **規範**: 應描述「狀態」與「行動建議」，避免使用「無效」等負面詞語。

### 必填檢查 (Required Field)
- **繁體中文**: 請輸入 `{FieldName}`。
- **日文**: `{FieldName}` を入力してください。
- **英文**: Please enter `{FieldName}`.
- **越南語**: Vui lòng nhập `{FieldName}`.

> 模板皆採 `Label: Value` 或變數置尾，避免句中變數造成翻譯破版（見 SKILL.md「i18n 核心規範」）。
