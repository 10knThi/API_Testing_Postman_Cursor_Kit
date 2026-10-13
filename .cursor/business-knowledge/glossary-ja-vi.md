# Glossary JP → VI

Thuật ngữ xuất hiện trong BD Welvie. Giữ nguyên bản tiếng Nhật khi viết checklist để đối chiếu BD, dùng bảng này để hiểu nghĩa.

## Tài liệu & quy trình

| JP | VI | Ghi chú |
| --- | --- | --- |
| 基本設計書 | Tài liệu thiết kế cơ bản (Basic Design) | |
| 画面ID / 画面名 / 画面URL | Mã màn / tên màn / URL màn | |
| 画面概要（目的） | Tổng quan & mục đích màn | |
| 範囲 | Phạm vi (khối chức năng trong màn) | |
| 項目名 / タイプ / フォーマット / 必須 / 内容 / 備考 | Tên item / kiểu / format / bắt buộc / nội dung / ghi chú | Header bảng item của BD |
| 更新履歴 | Lịch sử cập nhật | |
| 対応不要 | Không cần đối ứng | → out of scope |
| 一旦保留 / 保留 | Tạm hoãn | → out of scope tới khi có chỉ đạo |
| 要確認 | Cần xác nhận | → đưa vào QnA |
| 既存システムと同じにする | Làm giống hệ thống hiện tại | Có ràng buộc ẩn, cần hỏi |

## Tổ chức & vai trò

| JP | VI | Ghi chú |
| --- | --- | --- |
| 法人 | Pháp nhân / công ty khách hàng | = tenant |
| 法人種別 | Loại pháp nhân | 一般 / FC本部 / グループ親会社 / FC加盟店 / グループ子会社 |
| 本部 / エリア / 事務所 | Trụ sở / khu vực / chi nhánh | = role `tenant` / `area` / `branch` |
| 各社管理画面 | Màn quản trị của từng công ty | = portal Company admin |
| システム管理 | Quản trị hệ thống | = portal System admin |
| 親子グループ | Nhóm công ty mẹ–con | |
| CS担当 / 営業担当 | Người phụ trách CS / phụ trách bán hàng | |
| スタッフ | Nhân viên (staff) | Người học trên portal User |
| 入居者 | Người được chăm sóc (cư dân) | Xuất hiện ở KPI dashboard A002 |

## Đào tạo

| JP | VI | Ghi chú |
| --- | --- | --- |
| 研修 | Đào tạo / khoá học | |
| 研修管理 | Quản lý đào tạo | C003 |
| 進捗確認 | Xác nhận tiến độ | Tab C003 |
| 研修報告確認 | Xác nhận báo cáo đào tạo | Tab C003 |
| 研修実績DL | Tải kết quả đào tạo | Tab C003 |
| 必須研修 / 必須研修設定 | Đào tạo bắt buộc / thiết lập đào tạo bắt buộc | Tab C003 |
| 法定研修 | Đào tạo theo luật định | |
| 受講履歴 | Lịch sử học | |
| 修了証 | Chứng chỉ hoàn thành | A007 |
| 発行履歴 | Lịch sử phát hành | |
| 動画管理 | Quản lý video | A006 |
| ウェビナー | Webinar | A008 / `/admin/webinars` |
| デフォルト研修プラン | Kế hoạch đào tạo mặc định | |
| 運営指導対策 | Đối sách kiểm tra vận hành | |
| 訓練・委員会 | Huấn luyện & hội đồng | |
| サービス種別 | Loại hình dịch vụ | A014 |

## Thông báo & UI

| JP | VI | Ghi chú |
| --- | --- | --- |
| お知らせ | Thông báo (gửi staff) | C007 |
| スタッフへのお知らせ | Thông báo cho staff | C007 |
| ご案内 / お客様へのご案内 | Hướng dẫn / thông báo cho khách hàng | A012 → C005 |
| 配信する / 配信中 / 配信済み | Phát hành / đang phát hành / đã phát hành | Trạng thái お知らせ |
| 署名 / 署名必要 | Ký xác nhận / cần ký | Switch trong C007 |
| 確認スタッフ数 | Số staff đã xác nhận | |
| 未確認 | Chưa xác nhận | |
| 発行日 | Ngày phát hành | |
| 作成者 | Người tạo | |
| 新規作成 | Tạo mới | |
| 編集 / 削除 / キャンセル | Sửa / xóa / huỷ | |
| スイッチ | Switch (bật/tắt) | Boolean |
| テキストインプット | Input text | |
| データ表示 | Vùng hiển thị dữ liệu | |
| モーダル | Modal / popup | |
| バナー | Banner | |

## Validation & phi chức năng

| JP | VI | Ghi chú |
| --- | --- | --- |
| 必須 | Bắt buộc | |
| 〜を入力してください | Vui lòng nhập 〜 | Mẫu message required |
| 255文字以内に入力してください | Vui lòng nhập trong 255 ký tự | Mẫu message max length |
| 既に登録されています | Đã được đăng ký (trùng dữ liệu) | |
| 同時ログイン制御 | Kiểm soát login đồng thời | |
| 同時接続数 | Số kết nối đồng thời | |
| 推奨ブラウザ | Trình duyệt khuyến nghị | |
| 多言語 | Đa ngôn ngữ | |
| 論理削除 | Xóa mềm (logical delete) | |
| 権限を持つ / 持たない / 閲覧のみ | Có quyền / không có quyền / chỉ xem | `✅` / `❌` / `👁` |
