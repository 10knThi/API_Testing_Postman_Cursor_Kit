# WELVIE LEARNING — Tổng quan hệ thống

Nguồn: 2 tập BD `Welvie learning_System admin_基本設計書.xlsx` (v1.0 — 2026/06/09) và `Welvie learning_Company admin_基本設計書.xlsx` (v1.0 — 2026/07/29).

## Mục đích

Hệ thống quản lý đào tạo (learning / 研修) cho các法人 (công ty khách hàng) trong lĩnh vực chăm sóc. Nhà cung cấp quản trị toàn hệ thống qua portal System admin; mỗi công ty tự quản lý staff và tiến độ đào tạo qua portal Company admin; staff học và xác nhận thông báo trên portal User.

## Các portal

| Portal | Đối tượng | Prefix URL | BD |
| --- | --- | --- | --- |
| System admin | Nhà cung cấp (quản trị hệ thống) | `~admin/...` | System admin BD (A001–A014) |
| Company admin (各社管理画面) | Quản trị viên của từng法人 | `~company/...` | Company admin BD (C001–C012) |
| User | Staff của công ty | — | Không nằm trong 2 BD trên |
| FC dashboard | FC本部 | — | Chỉ có dòng quyền trong Authority matrix |

Sheet `権限マトリックス` (System admin BD) định nghĩa ma trận Portal × Account role cho 4 portal: System admin / Company admin / User / FC dashboard.

## Luồng dữ liệu liên portal (điểm cần lưu ý khi test)

- System admin tạo法人 và tài khoản Company admin → Company admin mới login được (sheet 法人 — A003).
- System admin bật/tắt các **switch chức năng** cho từng法人 → quyết định menu nào xuất hiện trên Company admin (ví dụ switch `スタッフへのお知らせ`).
- System admin upload video ở `コンテンツ` (A008) → hiển thị sang `コンテンツ` phía Company admin (C006).
- System admin tạo `お客様へのご案内` (A012) → hiển thị sang `ご案内` phía Company admin (C005).
- Company admin tạo `スタッフへのお知らせ` (C007) → hiển thị popup hoặc banner phía User tuỳ switch 署名; xóa thông báo thì bản phía User cũng mất.

Hệ quả: rất nhiều case của Company admin **không thể verify trọn vẹn chỉ bằng 1 portal**. Cần account tương ứng ở portal còn lại, nếu không thì để `UNCHECK` (xem quy ước ở `../test-knowledge/checklist-conventions.md`).

## Ràng buộc phi chức năng

Sheet `パフォーマンス定義` (System admin BD):

| No | Hạng mục | Yêu cầu |
| --- | --- | --- |
| 3 | 同時ログイン制御 | Cho phép **cùng lúc login nhiều account trên các PC/thiết bị khác nhau** — không chặn multi-session |
| 4 | 多言語 | System admin / Company admin / FC本部: **chỉ tiếng Nhật**. Portal User: JP, EN, VI, ZH, ID, NE, TL, MY |

Các hạng mục 同時接続数 và 推奨ブラウザ có trong BD nhưng **chưa điền giá trị** → chưa test được, xem `../test-knowledge/open-questions.md`.

## Quy ước tô màu trong BD

Sheet `ドキュメント作成・レビュー運用ルール` (System admin BD) định nghĩa vòng review bằng màu chữ/nền:

- Chữ đen / nền trắng: nội dung đã chốt.
- Nền xám, xám in đậm: **out of scope** — thống nhất với Bui là không test.
- Nền vàng: thay đổi được ưu tiên (áp dụng cho requirement `training_management_requirements_vi_v2.md`).

Khi trích BD bằng script, luôn đọc kèm style (fill + bold) để không test phần đã bị loại.
