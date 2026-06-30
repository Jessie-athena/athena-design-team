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

## 5. 使用者與角色 (Users & Roles)
| Key (概念) | 繁體中文 | 簡體中文 | 日文 | 英文 | 越南語 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **User** | 使用者 | 用户 | ユーザー | User | Người dùng |
| **Admin** | 管理員 | 管理员 | 管理者 | Admin | Quản trị viên |
| **Permissions** | 權限 | 权限 | 権限 | Permissions | Quyền hạn |
| **Role** | 角色 | 角色 | ロール | Role | Vai trò |
| **Organization** | 組織 | 组织 | 組織 | Organization | Tổ chức |

## 6. 訂單與交易 (Orders & Transactions)
| Key (概念) | 繁體中文 | 簡體中文 | 日文 | 英文 | 越南語 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Order** | 訂單 | 订单 | 注文 | Order | Đơn hàng |
| **OrderID** | 訂單編號 | 订单编号 | 注文番号 | Order ID | Mã đơn hàng |
| **Amount** | 金額 | 金额 | 金額 | Amount | Số tiền |
| **PaymentStatus** | 付款狀態 | 付款状态 | 支払い状況 | Payment Status | Trạng thái thanh toán |
| **Paid** | 已付款 | 已付款 | 支払い済み | Paid | Đã thanh toán |
| **Unpaid** | 未付款 | 未付款 | 未払い | Unpaid | Chưa thanh toán |
| **Invoice** | 發票 | 发票 | 請求書 | Invoice | Hoá đơn |

## 7. 報表與分析 (Reports & Analytics)
| Key (概念) | 繁體中文 | 簡體中文 | 日文 | 英文 | 越南語 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Report** | 報表 | 报表 | レポート | Report | Báo cáo |
| **Analytics** | 分析 | 分析 | 分析 | Analytics | Phân tích |
| **Data** | 數據 | 数据 | データ | Data | Dữ liệu |
| **Trend** | 趨勢 | 趋势 | トレンド | Trend | Xu hướng |
| **ExportReport** | 匯出報表 | 导出报表 | レポートをエクスポート | Export Report | Xuất báo cáo |

## 8. 時間與排程 (Time & Schedule)
| Key (概念) | 繁體中文 | 簡體中文 | 日文 | 英文 | 越南語 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Date** | 日期 | 日期 | 日付 | Date | Ngày |
| **Time** | 時間 | 时间 | 時間 | Time | Giờ |
| **StartTime** | 開始時間 | 开始时间 | 開始時間 | Start Time | Giờ bắt đầu |
| **EndTime** | 結束時間 | 结束时间 | 終了時間 | End Time | Giờ kết thúc |
| **ScheduledTask** | 排程任務 | 定时任务 | スケジュール | Scheduled Task | Nhiệm vụ theo lịch |

## 9. 表單常見欄位 (Common Form Fields)
| Key (概念) | 繁體中文 | 簡體中文 | 日文 | 英文 | 越南語 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **LastName** | 姓 | 姓 | 姓 | Last Name | Họ |
| **FirstName** | 名 | 名 | 名 | First Name | Tên |
| **MiddleName** | 中間名 | 中间名 | ミドルネーム | Middle Name | Tên đệm |
| **Email** | 電子郵件（B2B）/ Email（B2C） | 电子邮件 | メールアドレス | Email | Email |
| **PhoneNumber** | 手機號碼 | 手机号码 | 電話番号 | Phone Number | Số điện thoại |
| **Address** | 地址 | 地址 | 住所 | Address | Địa chỉ |

> Email 顯示規則：B2B 介面用「電子郵件」；B2C 介面用「Email」；越南語維持 Email 原字。
