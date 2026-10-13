# Business Knowledge

Kiến thức **nghiệp vụ** của hệ thống dưới test. Trả lời câu hỏi "hệ thống phải làm gì", không chứa cách test.

## Nguồn

Toàn bộ nội dung được trích từ:

- `basic design/Welvie learning_Company admin_基本設計書.xlsx`
- `basic design/Welvie learning_System admin_基本設計書.xlsx`
- `basic design/training_management_requirements_vi_v2.md`

## Quy ước

- Mỗi phát biểu nghiệp vụ phải trích được nguồn: `sheet <tên sheet> No.<số>`. Ví dụ: *"Xóa お知らせ thì thông báo phía staff cũng bị xóa (sheet スタッフへのお知らせ No.4)"*.
- Phần BD **tô xám / xám đậm in đậm** là out of scope — ghi rõ khi liệt kê, không đưa vào phát biểu nghiệp vụ như thể đang áp dụng.
- Không suy diễn. Chỗ BD không nói → đưa vào `../test-knowledge/open-questions.md`, không viết vào đây.
- Giữ nguyên thuật ngữ tiếng Nhật của BD (kèm nghĩa VI lần đầu xuất hiện) để đối chiếu BD dễ dàng.

## Danh sách file

| File | Nội dung |
| --- | --- |
| `welvie-system-overview.md` | Tổng quan hệ thống, các portal, ràng buộc phi chức năng |
| `welvie-screen-map.md` | Map 画面ID / 画面名 / URL và trạng thái đối ứng |
| `welvie-roles-permissions.md` | 法人種別, role, Authority matrix, switch điều khiển menu |
| `welvie-validation-messages.md` | Message validation chuẩn |
| `glossary-ja-vi.md` | Thuật ngữ JP → VI |
