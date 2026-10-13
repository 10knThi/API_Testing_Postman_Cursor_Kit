# Basic Design

Nơi lưu **tài liệu đặc tả gốc** (source of truth). Thư mục này **read-only về nội dung**: không sửa, không chú thích trực tiếp vào file. Mọi diễn giải nghiệp vụ đặt ở `.cursor/business-knowledge/`.

## Danh sách tài liệu

| File | Loại | Phạm vi | Version |
| --- | --- | --- | --- |
| `Welvie learning_System admin_基本設計書.xlsx` | Basic Design | Portal System admin — màn A001–A014, `権限マトリックス`, `Validation rule`, `パフォーマンス定義` | v1.0 — 2026/06/09 |
| `Welvie learning_Company admin_基本設計書.xlsx` | Basic Design | Portal Company admin — màn C001–C012, `Authority matrix`, `Validation rule` | v1.0 — 2026/07/29 |
| `Welvie learning_User_基本設計書.xlsx` | Basic Design | Portal User (staff) — màn U001–U007 (`ログイン`, `ホーム`, `新入社員研修`, `必須研修`, `動画`, `マイページ`, `ご案内`) | v1.0 — 2026/08/12 |
| `QA sheet nội bộ.xlsx` | QA log | 103 QA (QA001–QA103) giữa team và khách, chốt nhiều quy tắc nghiệp vụ **mới hơn BD**; kèm sheet `Dịch phụ đề với AI` so sánh chi phí nhà cung cấp AI | Cập nhật liên tục |
| `training_management_requirements_vi_v2.md` | Requirement (VI) | Module 研修管理 (C003) — bản requirement tiếng Việt, có phần gạch xám out of scope và phần vàng ưu tiên | v2 |

## Quy ước đọc BD

- Ô 備考 cạnh 画面ID chứa trạng thái đối ứng: `対応不要`, `一旦保留`, `既存システムと同じにする`. Đọc trước khi sinh checklist cho cả màn.
- Dòng **tô xám / xám in đậm** = out of scope, không test. Khi trích bằng script phải đọc kèm style (fill + bold), nếu không sẽ test cả phần đã bị loại.
- Sheet `ドキュメント作成・レビュー運用ルール` (System admin BD) định nghĩa ý nghĩa màu trong vòng review.
- BD có typo đã biết: URL `dashboad`, `noties`, `infomations`; tên field `NoticeTittle`. Đây là lỗi tài liệu, không phải bug app.
- `QA sheet nội bộ.xlsx` là **chốt sau** BD (mỗi QA có ngày trả lời). Khi QA sheet và BD lệch nhau thì lấy QA sheet làm yêu cầu hiện hành, nhưng phải ghi xung đột vào mục "Unclear / Needs Confirmation" của `.cursor/business-knowledge/business-flows.md` thay vì bỏ một bên. QA còn ở trạng thái `Đang chờ team xác nhận` thì **chưa dùng làm expected result**.

## Liên kết

| Cần gì | Xem ở đâu |
| --- | --- |
| Map 画面ID → URL → trạng thái đối ứng | `.cursor/business-knowledge/welvie-screen-map.md` |
| Ma trận quyền, 法人種別, switch bật menu | `.cursor/business-knowledge/welvie-roles-permissions.md` |
| Message validation nguyên văn | `.cursor/business-knowledge/welvie-validation-messages.md` |
| Thuật ngữ JP → VI | `.cursor/business-knowledge/glossary-ja-vi.md` |
| Checklist / test case sinh ra từ BD này | `../test case/welvie/` |
| Câu hỏi còn mở với BA/dev | `.cursor/test-knowledge/open-questions.md` |

## Thêm tài liệu mới

1. Đặt file vào đây, giữ nguyên tên gốc (kể cả tiếng Nhật) để đối chiếu với bản BA gửi.
2. Thêm một dòng vào bảng "Danh sách tài liệu" ở trên, ghi rõ version và ngày.
3. Nếu là bản cập nhật của tài liệu cũ: giữ cả 2 bản, thêm hậu tố version vào tên bản mới, và ghi chú điểm khác biệt vào `.cursor/test-knowledge/known-issues-and-lessons.md`.
