# Knowledge Index

Điểm vào duy nhất cho toàn bộ kiến thức dự án. Agent và người mới **đọc file này trước**, sau đó mở đúng file cần thiết — không đọc tràn lan toàn bộ knowledge base.

## Cấu trúc

```
<repo root>/
├── basic design/                 # BD gốc & requirement gốc (source of truth, không sửa)
├── test case/                    # Checklist, viewpoint, QnA, defect, test case
└── .cursor/
    ├── knowledge-index.md        # File này — bản đồ kiến thức
    ├── business-knowledge/       # Nghiệp vụ hệ thống dưới test (WHAT the system does)
    ├── test-knowledge/           # Cách test hệ thống đó (HOW we test it)
    ├── rules/                    # Quy tắc bắt buộc cho agent
    └── skills/                   # Kỹ năng chuyên biệt
```

Nguyên tắc phân tách: **business-knowledge** chỉ chứa sự thật về nghiệp vụ, trích từ BD/requirement. **test-knowledge** chứa quy ước, góc nhìn test, môi trường, bài học và kết quả kiểm thử. Không trộn 2 loại vào cùng file.

## business-knowledge/

| File | Nội dung | Khi nào đọc |
| --- | --- | --- |
| [`welvie-system-overview.md`](business-knowledge/welvie-system-overview.md) | Hệ thống WELVIE LEARNING, các portal, ràng buộc phi chức năng | Bắt đầu task mới trên dự án Welvie |
| [`welvie-screen-map.md`](business-knowledge/welvie-screen-map.md) | Map 画面ID → 画面名 → URL, trạng thái đối ứng (対応不要 / 一旦保留) | Xác định scope một sheet BD |
| [`welvie-roles-permissions.md`](business-knowledge/welvie-roles-permissions.md) | 法人種別, role tenant/area/branch, Authority matrix, switch bật/tắt menu | Test phân quyền, menu hiển thị |
| [`welvie-validation-messages.md`](business-knowledge/welvie-validation-messages.md) | Message validation chuẩn theo sheet `Validation rule` | Viết expected result cho input |
| [`glossary-ja-vi.md`](business-knowledge/glossary-ja-vi.md) | Thuật ngữ JP → VI dùng trong BD | Đọc BD tiếng Nhật |

## test-knowledge/

| File | Nội dung | Khi nào đọc |
| --- | --- | --- |
| [`test-viewpoint-catalog.md`](test-knowledge/test-viewpoint-catalog.md) | Danh mục góc nhìn test (VP) dùng chung | Bước 2 của qa-workflow |
| [`checklist-conventions.md`](test-knowledge/checklist-conventions.md) | Quy ước đặt tên file, cột CSV, trạng thái, UNCHECK cố ý | Trước khi sinh checklist |
| [`environment-and-accounts.md`](test-knowledge/environment-and-accounts.md) | Môi trường, URL login, danh sách account theo role | Trước khi execute |
| [`known-issues-and-lessons.md`](test-knowledge/known-issues-and-lessons.md) | Defect đã phát hiện + bài học khi test UI/API | Khi gặp hành vi lạ, trước khi kết luận FAIL |
| [`open-questions.md`](test-knowledge/open-questions.md) | QnA còn mở với dev/BA theo từng màn | Khi BD thiếu spec |

## Artifact (ngoài `.cursor/`)

| Thư mục | Nội dung | Index |
| --- | --- | --- |
| `basic design/` | BD xlsx + requirement gốc, read-only | [`basic design/README.md`](../basic%20design/README.md) |
| `test case/` | Checklist / viewpoint / QnA / defect (`welvie/`) và test case mẫu (`samples/`) | [`test case/README.md`](../test%20case/README.md) |

## Quy tắc cập nhật

- Chỉ ghi vào knowledge base điều **đã xác minh** — từ BD, từ evidence khi execute, hoặc từ câu trả lời của BA/dev. Giả định phải ghi rõ nhãn `Assumption`.
- Phát hiện mới khi execute → cập nhật `test-knowledge/known-issues-and-lessons.md`; spec mới được BA trả lời → cập nhật `business-knowledge/` và xoá mục tương ứng trong `open-questions.md`.
- BD gốc trong `basic design/` là read-only. Mọi diễn giải đặt trong `business-knowledge/` kèm trích dẫn (tên sheet + No. của item).
- Không commit mật khẩu / token vào repo. Xem quy ước ở `test-knowledge/environment-and-accounts.md`.
- Thêm file mới → thêm một dòng vào bảng tương ứng ở trên, nếu không thì coi như file đó không tồn tại.
