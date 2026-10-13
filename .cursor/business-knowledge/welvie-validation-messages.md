# WELVIE LEARNING — Validation Messages

Nguồn: sheet `Validation rule` của Company admin BD (đã trích đủ 16 dòng) và sheet `Validation rule` của System admin BD (hiện chỉ trích phần Admin Login).

Dùng file này làm **expected result** cho mọi case input. Message phải khớp **nguyên văn tiếng Nhật**, kể cả dấu `。`.

## Company admin

### Company Login (C001)

| Field | Rule | Message |
| --- | --- | --- |
| Email | Required | `メールアドレスとパスワードを入力してください` |
| Email | Incorrect input | `メールまたはパスワードが間違っています。ご確認ください` |
| Password | Required | `メールアドレスとパスワードを入力してください` |
| Password | Incorrect input | `メールまたはパスワードが間違っています。ご確認ください` |

Sai email và sai password dùng **cùng một message** — đây là chủ ý (không tiết lộ email có tồn tại hay không), không phải bug.

### Create User (C004 スタッフ管理)

| Field | Rule | Message / Giới hạn |
| --- | --- | --- |
| Name | Required | `氏名を入力してください` — max 255, vượt: `255文字以内に入力してください` |
| TenantID | Required | `法人を選択してください` |
| Phone | Required | `電話番号またはメールアドレスのいずれかを入力してください` — max 11. Nếu đã nhập email thì không cần nhập |
| Phone | Already exists | `この電話番号は既に登録されています` |
| Email | Required | `電話番号またはメールアドレスのいずれかを入力してください` — nếu đã nhập phone thì không cần nhập |
| Email | Format / Already exists | `メール形式不正` / `このメールアドレスは既に登録されています` |
| Password | Required | `パスワードを入力してください` — nếu checkbox không được chọn thì không cần nhập |

Phone và Email là **cặp OR**: chỉ cần một trong hai.

### Giới hạn khác

| Đối tượng | Rule | Message |
| --- | --- | --- |
| Staff limit | Limit | `「{tenant}」のスタッフ登録上限 が{limit} 名となっています` |
| File CSV | CSV file only | Không hiển thị file khác CSV (không nêu message) |

### Notices (C007 スタッフへのお知らせ)

| Field | Rule | Message / Giới hạn |
| --- | --- | --- |
| NoticeTittle (sic) | Required | `タイトルを入力してください` — max 255, vượt: `255文字以内に入力してください` |
| NoticeDescription | Required | `お知らせ内容を入力してください` — max length = 10000, **không định nghĩa message khi vượt** |

## System admin

### Admin Login (A001)

| Field | Rule | Message |
| --- | --- | --- |
| Email | Required | `メールアドレスとパスワードを入力してください` |
| Email | Incorrect input | `メールまたはパスワードが間違っています。ご確認ください` |
| Password | Required | `パスワードを入力してください` |
| Password | Incorrect input | `メールまたはパスワードが間違っています。ご確認ください` |

Khác biệt cần nhớ: **Password required** ở portal admin là `パスワードを入力してください`, còn ở portal company là `メールアドレスとパスワードを入力してください`.

### Phần chưa trích

Sheet `Validation rule` của System admin BD còn các block khác (ngoài Admin Login) chưa được trích vào knowledge base. Khi cần expected cho màn A0xx, trích thêm rồi bổ sung vào đây.

## Cách quan sát message khi execute

Message **không** hiển thị inline dưới field mà bằng **toast ở đáy màn hình** (`react-hot-toast`), tự biến mất. Xem chi tiết cách bắt bằng MutationObserver ở `../test-knowledge/known-issues-and-lessons.md`.

Field email dùng `type=email` nên trình duyệt chặn trước bằng validation HTML5 — message lúc đó là của Chrome, **không phải** message BD. Phân biệt rõ 2 nguồn message trước khi kết luận FAIL.
