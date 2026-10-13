# Business Flows — WELVIE LEARNING

Luồng nghiệp vụ end-to-end, xây từ Basic Design và QA sheet nội bộ. Mô tả ở mức **nghiệp vụ**, không mô tả chi tiết click UI. Không chứa test case.

## Nguồn đã đọc

| Nguồn | Nội dung | Trạng thái |
| --- | --- | --- |
| `basic design/Welvie learning_System admin_基本設計書.xlsx` | 20 sheet — A001–A014 + 権限マトリックス, Validation rule, パフォーマンス定義 | Đã đọc |
| `basic design/Welvie learning_Company admin_基本設計書.xlsx` | 16 sheet — C001–C012 + Authority matrix, Validation rule | Đã đọc |
| `basic design/Welvie learning_User_基本設計書.xlsx` | 9 sheet — U001–U007 (portal staff), v1.0 — 2026/08/12 | Đã đọc |
| `basic design/QA sheet nội bộ.xlsx` | 103 QA (QA001–QA103) giữa team dev/QA và khách + 1 sheet so sánh chi phí AI phụ đề | Đã đọc |
| `basic design/training_management_requirements_vi_v2.md` | Requirement 研修管理 (C003) | Đã đọc |
| `.cursor/business-knowledge/welvie-roles-permissions.md`, `glossary-ja-vi.md`, `welvie-validation-messages.md`, `welvie-screen-map.md`, `welvie-system-overview.md` | Knowledge nội bộ | Đã đọc |
| `business-rules.md`, `status-transition.md` | — | **Không tồn tại** trong repo |

Quy ước trích dẫn: `sheet <tên sheet JP> No.<số item> (r<dòng Excel>)` cho BD; `QA<số>` cho QA sheet nội bộ. Dòng BD tô xám = out of scope, ghi nhãn `GRAY`.

**Thứ tự ưu tiên khi BD và QA sheet lệch nhau**: QA sheet là chốt sau (mỗi QA có ngày trả lời), nên **QA sheet thắng** và rule trong tài liệu này được viết theo QA sheet. Từng trường hợp bị ghi đè được liệt kê ở mục "Rule hiện hành khi BD và QA sheet lệch nhau" kèm cả hai vế, để biết chỗ nào trong BD cần sửa. Mục "Unclear / Needs Confirmation" chỉ giữ những gì QA sheet **chưa** trả lời. QA còn ở trạng thái `Đang chờ team xác nhận` thì chưa dùng làm expected result.

## Bản đồ luồng

| Nhóm | Flow |
| --- | --- |
| Khởi tạo & vòng đời 法人 | BF-01, BF-02, BF-03, BF-04, BF-05 |
| Tổ chức & nhân sự | BF-06, BF-07, BF-08 |
| Nội dung đào tạo | BF-09, BF-10 |
| Chu trình đào tạo (phía quản lý) | BF-11, BF-12, BF-13, BF-14 |
| Truyền thông tới khách hàng & staff | BF-15, BF-16, BF-17, BF-18 |
| Vận hành nội bộ nhà cung cấp | BF-19 |
| Portal User (staff) | BF-20, BF-21, BF-22, BF-23, BF-24, BF-25 |

---

## BF-01 — Đăng ký 法人 mới (onboarding khách hàng)

- **Actor**: System admin — role `super`, `cs_lead`, `cs` (`法人 — 新規作成`: content_admin ❌, viewer ❌).
- **Preconditions**: đã đăng nhập portal System admin; đã có サービス種別 (BF-06) và danh sách CS担当 (BF-19). 法人 thuộc nhóm thì công ty cha phải tồn tại trước.
- **Main business steps** (sheet 法人 No.40–63, r292–r464):
  1. **基本情報**: 法人名, 法人種別, 代表者名, ステータス, 都道府県, 住所, スタッフ上限数 (default 10), 契約日 (default ngày hiện tại), CS担当. Có `下書き保存` để lưu nháp.
  2. **サービス**: chọn 提供サービス種別 (nhiều), bật/tắt SMS通知（必須研修リマインド）, bật/tắt switch chức năng. QA012 đã chốt **bỏ phần đăng ký 事業所 khỏi bước này**, đổi tiêu đề `サービス・事業所` → `サービス`; 事業所 được thêm sau ở màn chi tiết 法人 (BF-06).
  3. **法人管理者**: 法人管理者名, メール, パスワード — mặc định `00000000`.
  4. **確認 → 登録する**: hệ thống **tự động gửi email thông tin đăng nhập** cho 法人管理者.
- **Business decision points**:
  - 法人種別 `FC加盟店` → bắt buộc chọn `FC本部`; `グループ子会社` → bắt buộc chọn `グループ親会社` (r293). QA054 bổ sung: `一般` và `グループ子会社` **vẫn thêm được 事業所**; `FC本部` và `FC加盟店` **chưa làm trong phase này**; chỉ `グループ親会社` mới hiển thị 子会社一覧.
  - Switch chức năng ON → menu tương ứng xuất hiện ở 各社管理画面: 「ONにすると各社管理画面に該当メニューが追加される」 (r378–r379).
  - SMS通知 `契約済み` → gửi remind ngày 10 và 25 mỗi tháng, trùng T7/CN thì lùi về ngày làm việc trước (r372).
  - QA009: `法人種別` và `ステータス` có giá trị mặc định khi mở màn nên **không cần validate**; `都道府県` chuyển thành dropdown và **cần validate** (không có mặc định); `スタッフ上限数` mặc định 10, min = 1.
  - QA011: bản nháp **không lưu** パスワード / パスワード（確認）; mở lại màn tạo thì 2 trường được fill lại `00000000`.
- **Status changes**: 法人 nhận `トライアル中` / `契約中` / `テスト` tại bước đăng ký (r295). `ロック中` / `解約` / `離脱` chỉ đặt được ở luồng 編集 (r507) — xem BF-04, BF-05.
- **Final business result**: 法人 tồn tại với サービス種別, switch chức năng và 1 tài khoản 法人管理者 đã nhận email đăng nhập → khách hàng dùng được portal Company admin (BF-03).
- **Related screens**: A003 法人 (`/admin/tenants`), A014 サービス種別管理, A013 アドミンユーザー, C001 各社管理ログイン.
- **Related business rules**: ràng buộc 法人種別 親子; mật khẩu mặc định `00000000`; スタッフ上限数 là trần đăng ký staff và **không tính tài khoản manager** (QA046); các switch `訓練・委員会`, `運営指導対策`, `外部サービスリンク`, `特定事業所加算`, `複数事業所所属` ở bước đăng ký là **GRAY** (r376–r382).

---

## BF-02 — Cấp và duy trì tài khoản 法人管理者

- **Actor**: System admin (`super`, `cs_lead`, `cs`).
- **Preconditions**: 法人 đã tồn tại (BF-01).
- **Main business steps** (sheet 法人 No.22–26, r166–r171):
  1. `法人管理者追加`: 名前, メール, パスワード → 「追加したメールで会社管理にログインできる」.
  2. `アカウント情報を送信`: gửi email chứa メール đăng nhập và パスワード.
  3. `編集`: sửa 名前 / メール. 4. `パスワード変更`. 5. `削除`: **thu hồi quyền quản trị** với công ty, không xóa tài khoản (r171).
- **Business decision points**: chỉ tài khoản có vai trò `tenant_admin` mới đăng nhập được portal Company admin (sheet 権限マトリックス r8: 「tenant_adminの場合のみログイン可能」).
- **Status changes**: BD không định nghĩa trạng thái riêng cho 法人管理者.
- **Final business result**: công ty có ≥ 1 người đăng nhập được portal Company admin.
- **Related screens**: A003 法人 (khối 法人管理者), C001 各社管理ログイン.
- **Related business rules**: một 法人 có thể có nhiều 法人管理者; tài khoản manager không bị tính vào trần staff (QA046).

---

## BF-03 — Company admin đăng nhập và nhìn thấy phạm vi dữ liệu của mình

- **Actor**: 法人管理者 (role `tenant` / `area` / `branch`).
- **Preconditions**: tài khoản đã được tạo (BF-02); 法人 **không** bị khóa (BF-04).
- **Main business steps**:
  1. Đăng nhập C001 bằng メールアドレス + パスワード; tuỳ chọn `ログイン情報保存` (No.3–4).
  2. Hệ thống chuyển sang C002 ダッシュボード: スタッフ数（契約上限）, 今月必須研修完了率, 新入社員, 事業所別 必須研修 視聴進捗, 改善が必要なレポート TOP3.
  3. Menu hiển thị theo switch chức năng của 法人 (BF-01) và theo Authority matrix của role.
- **Business decision points**:
  - Sai email hoặc sai password → cùng thông báo `メールまたはパスワードが間違っています。ご確認ください`.
  - Phạm vi dữ liệu: `本社` thấy 本社 + 事業所 trực thuộc; `事業所` chỉ thấy phạm vi của mình (ダッシュボード No.13). QA059 xác nhận: office admin **xem được** dashboard nhưng chỉ của office đó. QA059 cũng làm rõ 本社 (trụ sở chính) chính là tenant, nhưng **về mặt hệ thống là một office của chính nó**.
  - QA059: con số 「6名超過の意味は必須研修完了していない人」 nghĩa là số user được tick `新入社員` nhưng chưa hoàn thành khóa đào tạo dành cho nhân viên mới.
  - 今月必須研修完了率 **không tính** staff `休職中`.
- **Status changes**: không đổi trạng thái nghiệp vụ.
- **Final business result**: 法人管理者 vào được hệ thống với đúng phạm vi dữ liệu và đúng tập menu.
- **Related screens**: C001 ログイン, C002 ダッシュボード, Authority matrix.
- **Related business rules**: 同時ログイン制御 cho phép cùng account đăng nhập nhiều thiết bị (パフォーマンス定義 No.3); portal Company admin chỉ tiếng Nhật (No.4).

---

## BF-04 — Khóa và mở khóa truy cập

- **Actor**: System admin.
- **Preconditions**: 法人 (hoặc user) đang đăng nhập được.
- **Main business steps**:
  1. Khóa cấp 法人: nút `ロック` trên màn chi tiết → 「法人とその法人に所属しているユーザーはログインできない」 (No.13, r84). Hoặc đặt ステータス = `ロック中` khi 編集 (r507).
  2. Mở khóa: QA033 chốt **sau khi lock, nút `ロック` chuyển thành nút unlock**.
  3. QA021 chốt cơ chế ở cấp user: **user bị lock là do 法人 của user bị lock**, không set trực tiếp trạng thái lock trên từng user; `有効` và `ロック` ở màn danh sách user chỉ là 2 nút filter.
  4. Thao tác khóa được ghi vào 重要操作履歴 cùng CS担当変更, ステータス変更, 法人登録, 法人種別変更 (r253).
- **Business decision points**: khóa cấp 法人 ảnh hưởng toàn bộ user thuộc 法人. QA051 bổ sung: branch thuộc tenant bị xóa thì **trạng thái branch không đổi, chỉ bị locked không cho login**.
- **Status changes**: 法人 `契約中` / `トライアル中` → `ロック中` → (unlock) trở lại. User `有効` → `ロック` theo 法人.
- **Final business result**: đối tượng bị khóa không đăng nhập được; có đường mở lại; lịch sử thao tác được lưu.
- **Related screens**: A003 法人, A004 ユーザー, C001 各社管理ログイン, U001 ユーザーログイン.
- **Related business rules**: BD **không** định nghĩa thông báo hiển thị khi đăng nhập vào 法人 bị khóa, cũng không nói phiên đang mở có bị đăng xuất hay không → xem "Unclear".

---

## BF-05 — Kết thúc hợp đồng, đổi 法人種別 và xóa 法人

- **Actor**: System admin — `法人 — 削除` chỉ `super`; `法人 — 編集` cho `super`, `cs_lead`, `cs`.
- **Preconditions**: 法人 đang tồn tại.
- **Main business steps**:
  1. Đổi ステータス sang `解約` hoặc `離脱` qua luồng 編集 (r507).
  2. Đổi 法人種別 — QA060 chốt bộ quy tắc: `general → group parent` được (giữ UI); `general → group child` được, bắt buộc thêm group parent; `group child → group parent` được, **bỏ liên kết** group parent cũ; `group child → general` được, bỏ liên kết cũ; `group parent → general` **chỉ khi không còn group child nào**; `group parent → group child` chỉ khi không còn group child nào và phải thêm group parent.
  3. `この法人を完全に削除する`: xóa mềm 法人; nếu là 本社 thì **xóa luôn 事業所 và user** trực thuộc, dữ liệu vẫn giữ trong DB (No.82, r557).
  4. Khôi phục: QA066 mô tả tình huống 法人 chuyển từ `削除済み` về `契約中`, kèm theo **các user của 法人 cũng được khôi phục** → tồn tại đường khôi phục.
  5. Dashboard dùng thời điểm chuyển `契約中` / `解約` để tính 年間新規契約 và 年間解約社数.
- **Business decision points**:
  - 法人 là 本社 → xóa lan sang 事業所 + user.
  - QA049 / QA051: khi xét ràng buộc xóa サービス種別, 法人 ở trạng thái `Cancelled` được coi là **không còn liên kết** (giống `Deleted`); còn branch thuộc tenant đã xóa thì **chặn** xóa サービス種別.
  - BD **không** phân biệt điều kiện nghiệp vụ giữa `解約` và `離脱`.
- **Status changes**: `契約中` / `トライアル中` / `テスト` → `解約` / `離脱` / xóa mềm → có thể trở lại `契約中` (QA066).
- **Final business result**: công ty ngừng sử dụng hệ thống, KPI hợp đồng được cập nhật, dữ liệu lịch sử vẫn nằm trong DB và có khả năng khôi phục.
- **Related screens**: A003 法人, A002 ダッシュボード, A014 サービス種別管理.
- **Related business rules**: xóa là xóa mềm ở cấp 法人 / user / staff (`論理削除`); QA085 & QA087 chốt công thức lũy kế của dashboard — `累計契約中` = số tenant status contract (không gồm trial) + new contract − canceled (không gồm lock, test); `累計スタッフ数` = số user của tenant đang contract (gồm new contract) + new staff (không gồm tenant lock, trial, test).

---

## BF-06 — Thiết lập サービス種別 và 事務所 của 法人

- **Actor**: System admin (`サービス種別管理`: super/content_admin/cs_lead/cs ✅, viewer ❌).
- **Preconditions**: サービス種別 phải tồn tại trước khi gán cho 法人.
- **Main business steps**:
  1. A014: `種別追加` / `編集` / `削除`; `タグ追加` thêm 対象者タグ dùng khi tạo カテゴリ.
  2. A003 chi tiết 法人: `サービス種別を追加` kèm 事務所; `新規事務所追加` (事務所名, サービス種別, 住所, 事務所管理者 tuỳ chọn); `CSVインポート` thêm hàng loạt 事務所; `編集` / `削除` từng dòng. QA062 chốt **vẫn cho sửa サービス種別 trong tenant**, khôi phục lại các chức năng mục 29–34 (trừ mục 32 Area).
- **Business decision points**:
  - Một 事業所 gắn **một** サービス種別; một サービス種別 gắn nhiều 事業所 (r371, xác nhận lại ở QA061).
  - Không xóa được サービス種別 khi còn 法人 đang dùng (r45). QA037 mở rộng nguyên tắc: không xóa `Video-Category` khi còn liên kết video; không xóa `Service Type` khi còn liên kết category hoặc tenant; không xóa `Target Tag` khi còn liên kết. QA056 làm rõ: chặn xóa target tag **chỉ dựa trên việc user đã bị xóa mềm hay chưa**, không xét trạng thái tenant của user.
  - QA052: サービス種別 đã xóa mềm thì **tạo lại được** tên đó, nhưng bản ghi mới phải có ID khác.
  - QA022: cột 事業所数 đếm **cả tenant và office** (tenant có service A và office của tenant đó cũng có service A thì đếm là 2).
  - QA063: thay đổi hoặc xóa サービス種別 khi user đã hoàn thành bài học của service đó → phần hoàn thành **bị xóa mềm** và không hiển thị nữa; khi khôi phục thì cả service và phần bài học đã hoàn thành được revert.
  - QA064: vì nhiều office dùng chung サービス種別 mà kế hoạch đào tạo gắn với サービス種別, nên **chỉ tenant được sửa kế hoạch, office không** (xem BF-11).
- **Status changes**: サービス種別 có `有効` / `無効`, nhưng thao tác `無効化` là **GRAY** (r44).
- **Final business result**: 法人 có cấu trúc tổ chức (本社 + các 事業所 theo サービス種別) làm nền cho gán staff và thiết lập 必須研修.
- **Related screens**: A014 サービス種別管理, A003 法人 (khối 事務所), C004 スタッフ管理, C003 研修管理 tab 必須研修設定.
- **Related business rules**: 階層 của màn quản lý công ty là 「全員」「管理職向け」「中堅向け」 (sheet 動画管理 備考, r44).

---

## BF-07 — Tạo và quản lý staff (người học)

- **Actor**: hai đường song song — System admin (A004 ユーザー; `super`, `cs_lead`, `cs`) và 法人管理者 (C004 スタッフ管理; tenant/area/branch đều ✅).
- **Preconditions**: 法人 và 事業所 đã tồn tại; số staff chưa vượt スタッフ上限数.
- **Main business steps**:
  - Phía System admin: `新規登録` (氏名, 電話, メール, パスワード, 会社, 事業所, 階層, 新入社員); `一括登録（CSV）`; `編集`; `パスワードリセットメール送信` gửi mật khẩu mới tự sinh (No.16); `削除` (dữ liệu giữ trong DB).
  - Phía Company admin: `新規登録` → 名前, メールアドレス, 電話番号, 事業所, 階層, 新入社員, パスワード; `編集`; `ログイン情報送信`; `削除` (`データベースに残す`).
- **Business decision points**:
  - Bắt buộc có **メール hoặc 電話番号** (Validation rule: `電話番号またはメールアドレスのいずれかを入力してください`).
  - Chỉ nhập được パスワード khi có メール; checkbox mật khẩu mặc định tắt. `ログイン情報送信` chỉ khả dụng khi staff có メール.
  - Vượt trần → `「{tenant}」のスタッフ登録上限 が{limit} 名となっています`. QA046: trần này **không tính tài khoản manager**, chỉ tính tài khoản dùng để xem video.
  - 事業所 chọn được: `本社` chọn 本社 hoặc 事業所 trực thuộc; role 事業所 không có dropdown này.
  - 区分 `新入社員` áp dụng trong 90 ngày kể từ ngày đăng ký, từ ngày 91 thành `スタッフ` (sheet ユーザー r47). QA026: user vừa là 新入社員 vừa là quản lý/staff thì cột 区分 hiển thị **cả 2 tag**.
  - Trùng email / số điện thoại → message tương ứng. QA008: số điện thoại chỉ validate 11 số, **không yêu cầu số 0 ở đầu**.
  - QA066: số staff mới đăng ký trong tháng được tính theo **checkbox 新規登録**, và khi khôi phục user thì vẫn tính theo ngày đã tick trước đó.
- **Status changes**: QA021 chốt user chỉ có `Active` (`有効`) và `Locked` — lock kế thừa từ 法人 (BF-04); trạng thái `deleted` **không dùng**, user bị xóa thì biến khỏi danh sách nhưng vẫn lưu trong DB. Phía Company admin, `休職中` chỉ được ghi là 「休職中機能を追加」 mà không có chi tiết.
- **Final business result**: staff tồn tại, thuộc đúng 事業所 và 階層, đăng nhập được portal User để học (BF-20).
- **Related screens**: A004 ユーザー, C004 スタッフ管理, C002 ダッシュボード, U001 ユーザーログイン.
- **Related business rules**: スタッフ上限数 đặt khi tạo 法人, min = 1 (QA009), không cho sửa xuống ≤ 1 khi 編集; 階層 lấy từ タグ管理 và **không phải** cơ chế phân quyền.

---

## BF-08 — Staff chuyển 事業所 (異動) và hệ quả lên dữ liệu đào tạo

- **Actor**: 法人管理者 — **thao tác 異動 / 兼務 trên C004 là GRAY/out of scope** (No.10–11, No.22–32).
- **Preconditions**: staff đang thuộc một 事業所; công ty có ≥ 2 事業所.
- **Main business steps** (sheet 研修管理 No.8 備考 r50 + requirement CHANGE-01 + QA075):
  1. Staff được chuyển từ 事業所 A sang 事業所 B.
  2. Toàn bộ 受講実績 được giữ nguyên — QA075: 「khi chuyển đổi branch, toàn bộ thành tích học tập của user sẽ vẫn giữ nguyên và không bị mất」.
  3. Dữ liệu tiến độ chia theo mốc chuyển: 事業所 A giữ dữ liệu tới tháng chuyển, phần sau để trắng; 事業所 B hiển thị từ tháng chuyển, phần trước để trắng; chuyển trong cùng tháng thì tháng đó hiển thị ở **cả hai** 事業所.
  4. Khi chọn `全事業所`, staff xuất hiện thành **2 record** (trước và sau khi chuyển).
  5. 必須研修 của 事業所 mới trùng với研修 đã học ở 事業所 cũ → staff **phải học lại** video.
- **Business decision points**:
  - Báo cáo `未確認` tại thời điểm chuyển → chỉ xem, không thao tác. QA078 làm rõ: báo cáo đó **vẫn hiển thị ở cả hai branch** (và khi tenant xem all branch) nhưng không branch nào thao tác được.
  - QA075: staff chưa có thành tích học thì **không tính** vào 事業所 cũ.
  - QA075: staff bị xóa — nếu xem ở tháng kế tiếp thì không tính lại kết quả tháng trước; nếu xem trong cùng tháng thì vẫn tính lại (1 lần/ngày).
- **Status changes**: báo cáo `未確認` → read-only (BD không đặt tên trạng thái mới).
- **Final business result**: dữ liệu đào tạo được phân bổ đúng 事業所 theo thời gian; nghĩa vụ học lại được xác định lại theo kế hoạch của 事業所 mới.
- **Related screens**: C004 スタッフ管理 (thao tác — GRAY), C003 研修管理 tab 進捗確認 và tab 研修報告確認, U006 マイページ 視聴履歴.
- **Related business rules**: CHANGE-01 trong requirement; 視聴率 chỉ tính `全完了`. Lưu ý phía staff QA098 chốt 視聴履歴 **chỉ lấy branch hiện tại** — khác với phía công ty hiển thị 2 record → xem "Unclear".

---

## BF-09 — Chuẩn bị nội dung đào tạo (カテゴリ → 動画 → test → phát hành)

- **Actor**: System admin — `動画管理`: `super` ✅, `content_admin` ✅; `cs_lead`, `cs`, `viewer` 👁.
- **Preconditions**: đã có サービス種別 và 対象者タグ nếu cần gán phạm vi (BF-06).
- **Main business steps** (sheet 動画管理):
  1. Tạo (No.1, r40) / sửa (No.7, r46) / xóa (No.8, r47) カテゴリ — không xóa được khi còn 動画 thuộc category (r47, xác nhận ở QA037). Tạo và sửa **dùng chung một modal** (r51: 「（No1）カテゴリ作成と（No7）カテゴリ編集」). Modal đi theo thứ tự: `カテゴリ名` (bắt buộc) + `サムネイル画像` (không bắt buộc) → `区分` (bắt buộc) → 配信先 / 対象者 tuỳ 区分 → `閲覧権限` → `研修区分` → `新入社員研修対象` → `対象者` → nút lưu (No.23, r326 — đổi tên `変更内容を保存する` thành `保存`).
  2. Upload video hoặc `一括アップロード`; gắn test cho video hoặc `テスト一括登録`; đính kèm 付随資料.
  3. Đặt 区分 cho category — radio **bắt buộc**, chỉ chọn 1 trong **4**: `共通` / `サービス種別` / `自社研修` / `オプション` (No.14, r129). Bộ lọc ở danh sách category cũng có đủ 4 giá trị này cộng `すべて` (No.4, r43).
  4. Phát hành: đặt `公開`, hoặc bulk `一括公開` / `一括下書き`.
- **Business decision points**:
  - 区分 `共通` → 「共通カテゴリの動画は、全法人の全スタッフに自動で表示されます。配信先の個別設定は不要です。」 (r168). `サービス種別` → chọn nhiều サービス種別. `自社研修` → chọn 法人 cụ thể, áp dụng 法人全体 hoặc một サービス分類.
  - Category bật `新入社員研修対象` → toàn bộ video trong category thành đối tượng 新入社員研修. QA099: category tick "new staff" thì **chỉ user được tick "new staff" mới xem được**.
  - Video không có test → phần câu hỏi bị ẩn. QA015: chọn "không có bài test" chỉ **ẩn** bài test cũ chứ không xóa, mở lại thì hiện lại; kết quả bài test đã làm **giữ nguyên**; câu hỏi chỉ dạng một đáp án đúng; không có chức năng sắp xếp câu hỏi.
  - QA030: một video gắn được **nhiều** category (quan hệ N–N); video trong cùng category **được trùng title**; category **được trùng tên**. QA035: vì vậy chức năng cập nhật category hàng loạt cũng cho chọn nhiều category. Hai điểm này ghi đè BD (r47 「1本動画は1カテゴリのみ入る」 và rule `Unique` ở Validation rule r33) → xem UF-42, UF-41.
  - Ràng buộc nhập liệu khi tạo/sửa category (Validation rule r32–r34): `CategoryName` bắt buộc — `カテゴリ名を入力してください`, max **255** ký tự — `255文字以内に入力してください`; `CategoryType` bắt buộc — `カテゴリ区分を選択してください`. Khi gán category cho video: `VideoCategory` bắt buộc — `カテゴリを選択してください` (r38). Rule `Unique` của `CategoryName` (r33, message `カテゴリ名は既に入力されています。`) **không áp dụng** vì QA030 cho trùng tên.
  - QA096 / QA097: video thuộc nhiều category thì khi thiết lập kế hoạch phải **phân biệt video đó thuộc category nào**; phía staff video được tách thành nhiều record nhưng **hoàn thành một lần là tính hoàn thành cho tất cả**.
  - QA024 / QA027: filter `法定/任意` và `新入社員研修のみ` lọc **gián tiếp qua category**. QA038: khi kết hợp nhiều điều kiện thì dùng `AND`, không thỏa một điều kiện thì trả về rỗng.
  - QA020: số đếm video ở màn danh sách bao gồm **cả private và publish**.
  - QA041: import bài test vào video đang tắt test thì **tự động bật** test.
  - QA042: nếu một dòng import lỗi thì **rollback toàn bộ**, không import.
  - QA044: khi xóa nhiều video, các video xóa được thì xóa, video thất bại thì báo message — **không rollback**.
- **Status changes**: 動画 `下書き（未公開）` → `公開` và ngược lại. 完了率 tính theo `完了` / `未完了`, xem hết video nhưng chưa xong test = `未完了` (sheet 法人 r43) — lưu ý QA071 định nghĩa hoàn thành bài học theo việc **gửi report** (xem BF-12).
- **Final business result**: kho nội dung được phát hành tới đúng phạm vi người học, sẵn sàng đưa vào kế hoạch 必須研修.
- **Related screens**: A006 動画管理, A008 コンテンツ, A014 サービス種別管理, C003 tab 必須研修設定, U004 必須研修, U005 動画.
- **Related business rules (giới hạn đã chốt qua QA sheet)**:
  - Video: chỉ định dạng **MP4** (QA004); 1 video max **2GB**; một phiên upload max **20 video**, tổng max **5GB** (QA003); độ dài max **90 phút 59 giây** (QA019); bulk operation max **100 bản ghi** (QA039, QA043).
  - Test: câu hỏi max length **1000**, đáp án max length **200** (QA032); import test max **1000 dòng**, CSV max **2MB**, max **100 video** (QA040); import là **append** thêm câu hỏi, không xóa câu hỏi cũ, không validate trùng (QA034); template import cho chọn danh sách video để tải (QA036).
  - 付随資料: chỉ **PDF**, mỗi file max **50MB**, không giới hạn số lượng (QA014).
  - Ảnh category: đuôi png/jpg, giới hạn dung lượng để mặc định theo dev tới khi khách yêu cầu (QA028).
  - Mục GRAY trong tab カテゴリ管理: No.2 `案内` (r41) và No.20 `新規トピック` (r323, kèm 「流れとしてこのボタンは不要か要確認」). Cột `操作` của bảng danh sách category bị ghi 「操作は保留」 (r44 cột N) → xem UF-43.
  - Xóa video là **xóa cứng** kéo theo plan, training progress, video test, question và file trên S3 (QA037) — nhưng QA045 lại nói các thông số training progress đã tính vẫn giữ nguyên → xem "Unclear".
  - Trường `Video-URL` đã bị **bỏ** (QA016).
  - Phụ đề AI: khách chốt tiền đề mỗi video có sẵn kịch bản, phụ đề gen tự động từ kịch bản và cần khớp với kịch bản; việc dịch có thể dịch từ kịch bản thay vì từ phụ đề; chức năng để **phase sau**, nhà cung cấp AI chưa chọn (QA013, QA017 — vẫn `Đang chờ team xác nhận`; sheet `Dịch phụ đề với AI` so sánh chi phí OpenAI / Google / AWS).

---

## BF-10 — Phát hành webinar / コンテンツ tới các công ty

- **Actor**: System admin — `ウェビナー管理`: `super`, `content_admin`, `cs_lead`, `cs` ✅; `viewer` 👁.
- **Preconditions**: có file video webinar.
- **Main business steps**:
  1. `ウェビナー登録` / `編集` / `削除` tại A008 — 「アップした動画は全社（各社管理画面に）見えるようにする」 (r3).
  2. Đặt 公開状態.
  3. C006 コンテンツ phía Company admin hiển thị 動画名 / 動画発行日 / 動画の長さ; bấm `詳細` mở video ở tab mới.
- **Business decision points**:
  - `未公開` → 「未公開の場合に、各社管理画面で見れない」 (r88). QA093 xác nhận: danh sách phía công ty **chỉ hiển thị webinar public**.
  - QA093: `動画発行日` = **ngày chuyển sang trạng thái public gần nhất**; trường duration được giữ lại và **tự lấy theo độ dài video** ở FE; video là trường bắt buộc.
  - QA050: ngoài file video thì **chỉ title + status là required**.
  - QA048: **bỏ trường URL** khỏi webinar; validate và max size giống video thường.
  - QA053: danh sách webinar sắp xếp theo ID giảm dần.
  - QA055: webinar **xóa cứng** giống video (xóa cả file), webinar đang public cũng xóa trực tiếp được.
  - Menu `コンテンツ` chỉ xuất hiện ở Company admin khi switch `コンテンツ` của 法人 đang ON (sheet 法人 No.77, r552).
- **Status changes**: webinar `下書き（未公開）` → `公開`.
- **Final business result**: mọi công ty đang bật switch コンテンツ xem được webinar đã phát hành; Company admin chỉ có quyền xem.
- **Related screens**: A008 コンテンツ, C006 コンテンツ, A003 法人 (switch).
- **Related business rules**: Authority matrix của Company admin **không có dòng** cho C006 → phân quyền theo role chưa được định nghĩa.

---

## BF-11 — Thiết lập kế hoạch 必須研修 theo năm

- **Actor**: 法人管理者 — QA064 chốt **chỉ role tenant được sửa kế hoạch, office không được sửa** (vì kế hoạch gắn với サービス種別 dùng chung giữa các office). Điều này hẹp hơn Authority matrix (ghi 研修管理 ✅ cho cả 3 role).
- **Preconditions**: 法人 có ≥ 1 サービス種別; nội dung đào tạo đã phát hành (BF-09).
- **Main business steps** (sheet 研修管理 tab 必須研修設定):
  1. Chọn năm ở `期間選択` — BD cho phép chọn năm tương lai (No.44, r320).
  2. Chọn tab サービス種別 theo các サービス種別 của 法人 (No.45).
  3. Trên bảng kế hoạch: chọn カテゴリ → hiện 動画 thuộc category; tick theo **tháng** để gán 必須研修 (No.46).
  4. `年間計画を一覧で確認` xem lại kế hoạch; `年間計画Excelで出力` xuất kế hoạch năm.
- **Business decision points**:
  - Năm tài chính **cố định từ tháng 4 tới tháng 3 năm sau**; chọn năm 2026 nghĩa là 4/2026–3/2027 (QA067).
  - Category mang tag 法定 hiển thị nhãn `法定`; category có tag `階層限定・必須研修対象外` áp dụng cho đối tượng `全員`.
  - QA096: khi tick video vào kế hoạch phải **phân biệt video thuộc category nào**; thứ tự hiển thị theo title video → id video → title category → id category.
  - QA097: video đã được setting mà **đổi category** thì phần setting cho category trước đó **bị mất**.
  - QA099: kế hoạch trỏ tới video đang private → **không hiển thị** cho staff; category tick "new staff" → chỉ user "new staff" xem được.
  - QA084: một video được set bắt buộc ở **2 tháng** (ví dụ tháng 8 và 9) → hoàn thành ở tháng 8 thì tháng 9 **tự động được tính là hoàn thành**.
- **Status changes**: BD không định nghĩa trạng thái cho bản thân kế hoạch (không có nháp / đã duyệt).
- **Final business result**: mỗi tháng của năm được ấn định danh sách 研修 bắt buộc theo サービス種別, làm cơ sở tính 受講件数 và 視聴率, và là nguồn cho danh sách 必須研修 phía staff (BF-21).
- **Related screens**: C003 研修管理 tab 必須研修設定; A006 動画管理; C003 tab 進捗確認 và tab 研修実績DL; U004 必須研修.
- **Related business rules**: `受講件数` = tổng số 必須研修 đã thiết lập; `視聴率` chỉ tính `全完了`; staff chuyển 事業所 mà 研修 trùng thì phải học lại (BF-08).

---

## BF-12 — Chu trình báo cáo đào tạo: staff gửi → Company admin xác nhận

- **Actor**: staff (portal User — BF-21) và 法人管理者 (C003 tab 研修報告確認).
- **Preconditions**: 必須研修 đã thiết lập (BF-11); staff đã gửi báo cáo.
- **Main business steps**:
  1. Company admin lọc theo `期間選択`, `スタッフ名` (tìm mờ theo tên user — QA082), `事業所`, `品質`, `状態`; `クリア` để bỏ filter.
  2. Xem báo cáo: 名前 / 法人 / 報告提出日 / 報告内容. Danh sách sắp xếp theo **ngày staff gửi report**, cả khi xem một branch hay all branch (QA081).
  3. Đặt `品質`: `良好` / `要確認` (mặc định theo BD) / `要改善`.
  4. Xử lý: `確認済み` để chấp nhận, hoặc `書き直しを依頼` để yêu cầu viết lại — 「配信した時点で、スタッフの「マイページ」の「レポートフィードバック」の画面に更新される」 kèm banner trang chủ.
  5. `フィードバック`: nhập góp ý gửi staff, cũng cập nhật マイページ và banner.
- **Business decision points**:
  - **Hoàn thành bài học = staff gửi report**, bất kể bài học có test hay không (QA071). Ngày hoàn thành tính theo **lần gửi đầu tiên trong tháng**; ví dụ gửi 31/8, ngày 1/9 admin yêu cầu viết lại và staff gửi lại thì vẫn tính hoàn thành ngày 31/8 (QA077).
  - Báo cáo được phân loại theo tháng dựa trên **ngày staff gửi lần đầu** (QA079).
  - QA089 chốt quy tắc **tự động gán chất lượng**: report có 5 ký tự liên tục giống nhau (ví dụ `aaaaa`) → `要改善`; các trường hợp còn lại → `良好`. Ba nút `確認済み`, `書き直しを依頼`, `フィードバック` **luôn hiển thị**.
  - QA089: đã bấm `確認済み` vẫn có thể yêu cầu viết lại; staff sửa report thì **update trực tiếp vào report cũ** và phía admin **vẫn giữ trạng thái confirmed**; nếu staff học lại lần nữa (sau khi đã report) thì sinh ra **luồng report mới**.
  - QA091: lần học gần nhất đã report → lần học sau sinh **bản ghi mới**; lần học gần nhất chưa report → **update vào bản ghi hiện tại**.
  - QA090: khi ở trạng thái yêu cầu viết lại thì **không đổi được 品質** cho tới khi staff report lại, nhưng vẫn bấm được `確認済み` và vẫn cập nhật được feedback.
  - QA092: độ dài tối đa của feedback là **10000** ký tự. QA088: khi bấm feedback thì UI nên hiện nội dung feedback đã ghi trước đó và **update** thay vì tạo feedback mới.
  - QA080: thống kê chất lượng đầu tab **chỉ bị ảnh hưởng bởi filter tháng**, không bị ảnh hưởng bởi filter branch hay quality.
  - Báo cáo `未確認` của staff đã chuyển 事業所 → chỉ xem, không thao tác (BF-08, QA078).
- **Status changes**: trục xác nhận `未確認` → `確認済み`; trục chất lượng `要確認` (mặc định BD) / `良好` / `要改善`, có thể được hệ thống tự gán theo QA089. Trạng thái yêu cầu viết lại được QA090 gọi là `rewrite_require`.
- **Final business result**: báo cáo của staff được xác nhận và xếp loại chất lượng; staff nhận phản hồi trên マイページ (BF-24). Theo mô tả A007 (GRAY), báo cáo được duyệt là một điều kiện để 修了証 phát hành tự động.
- **Related screens**: C003 tab 研修報告確認, C002 ダッシュボード (`改善が必要なレポート TOP3`), A005 tab レポート確認, U006 マイページ レポートフィードバック.
- **Related business rules**: 「連続性の文字があったら検知し、要改善の一覧に出す」 (ダッシュボード No.17) — khớp với quy tắc 5 ký tự liên tục ở QA089. Gửi feedback **không làm thay đổi bất kỳ trạng thái nào** (QA088); ghi chú ở C003 No.21 cột `備考` nói feedback tự gán `確認済み` đã bị QA088 ghi đè → xem UF-01.

---

## BF-13 — Theo dõi tiến độ và xuất dữ liệu đào tạo

- **Actor**: 法人管理者 (C003 tab 進捗確認 và tab 研修実績DL).
- **Preconditions**: kế hoạch 必須研修 đã thiết lập (BF-11); staff đã có hoạt động học.
- **Main business steps**:
  1. Tab 進捗確認: chọn năm (`令和yy年度（yyyy/4~yyyy/3）`, max là **năm hiện tại** — QA073); lọc 事業所; xem bảng 事業所 / スタッフ名 / 4月–3月 / 視聴率; xuất `必須研修受講進捗出力`.
  2. Tab 研修実績DL: chọn 事務所 (chỉ role 本社); đối chiếu 対象スタッフ数 và 受講件数; lấy 3 loại dữ liệu `研修実績（印刷用）`, `研修実績（詳細）`, `研修実績（運営指導用）`.
- **Business decision points**:
  - QA068 chốt **ý nghĩa ký hiệu** trên bảng tiến độ tháng: `◎` = 全完了 (hoàn thành toàn bộ video bắt buộc của tháng); `n/m` = 一部視聴 (ví dụ `1/2`); `X` = 未視聴 (chưa hoàn thành video nào); `予` = 計画済み (tháng tương lai đã được setup); `—` = 設定なし (tháng tương lai chưa setup).
  - QA069: `視聴率` = tỷ lệ **user đạt 全完了** trên tổng user — 2 user mà 1 người full completed thì 50%; 2 user ở mức 4/7 và 6/7 thì **0%**.
  - QA070: bảng có phân trang **20 record/trang**; chức năng lọc theo status (全完了 / … / 設定なし) **không làm**.
  - QA072 & QA018: số liệu thống kê **cập nhật 1 lần/ngày**, không real time.
  - QA073: danh sách năm chọn được phụ thuộc dữ liệu migrate từ hệ thống cũ; năm không có dữ liệu thì chọn xong hiển thị rỗng.
  - QA095 chốt **đổi cách tải dữ liệu ở tab 研修実績DL sang bất đồng bộ**: bấm nút để hệ thống tổng hợp và lưu file, trong lúc tổng hợp thì chưa tải được; xong thì xuất hiện nút download; file tồn tại tới khi request lần nữa (ghi đè file cũ). Cách này cần sửa lại UI so với BD.
  - QA074 (`Đang chờ team xác nhận`): tenant xem được branch default lẫn branch trực thuộc nên vẫn cần chức năng chọn branch; nếu đăng nhập là branch thì không cần vì branch chỉ xem chính nó.
  - Các nút `Preview` và bộ nút 研修実績 cũ (No.23–26, No.30/32/34) là **GRAY**.
- **Status changes**: không đổi trạng thái nghiệp vụ (chỉ đọc và xuất dữ liệu).
- **Final business result**: công ty có số liệu tiến độ theo tháng / 事業所 / staff và các tệp kết quả đào tạo dùng cho báo cáo nội bộ và 運営指導.
- **Related screens**: C003 tab 進捗確認 + tab 研修実績DL, C002 ダッシュボード, A005 研修進捗.
- **Related business rules**: định dạng file `研修実績（運営指導用）` vẫn chưa chốt (QA103 `Đang chờ team xác nhận`); nút `年間計画Excelで出力` mang tên Excel nhưng nội dung ghi là CSV.

---

## BF-14 — Nhà cung cấp giám sát và can thiệp tiến độ đào tạo

- **Actor**: System admin — `研修の進捗`: `super`, `cs_lead`, `cs`, `viewer` 👁; `content_admin` ❌.
- **Preconditions**: các 法人 đã có staff và hoạt động học.
- **Main business steps** (sheet 研修進捗 A005):
  1. Tab 必須研修進捗: xem KPI toàn hệ thống, tìm/lọc 法人, mở 法人進捗詳細, xem 必須研修完了率 theo 事業所.
  2. Tab 全スタッフ実績: tìm staff, mở `詳細`, `編集` để sửa 感想, ステータス, 完了報告日時, hoặc bỏ qua test.
  3. Tab レポート確認: xem các báo cáo staff đã gửi.
- **Business decision points**:
  - KPI hiển thị theo account: `super` và `content_admin` thấy toàn bộ; `cs_lead` và `cs` chỉ thấy công ty mình phụ trách (権限マトリックス r20).
  - 法人 chưa thiết lập 必須研修 → cột 必須研修 hiển thị `未設定`.
  - 完了率 và số liệu thống kê **cập nhật 1 lần/ngày** (QA018, QA072) — thay cho mô tả 「1日に固定時点」 trong BD.
  - QA072: mỗi lần học là một bản ghi mới và có chức năng xem lại lịch sử làm bài phía admin; khi thống kê, chỉ cần trong tháng có ít nhất **một** lần staff tạo report cho video là tính hoàn thành bài học đó.
  - QA058 (`Đang chờ team xác nhận`): trường tìm kiếm 法人 chuyển sang search field thường (partial match) kèm nút `search`, bỏ dropdown và bỏ tìm tức thời theo từng ký tự để tránh ảnh hưởng hiệu năng.
- **Status changes**: trạng thái học của staff theo chuỗi `動画未聴視` → `動画視聴完了` → `研修完了報告済み` → `研修報告確認済み`; System admin sửa trực tiếp được ở tab 全スタッフ実績.
- **Final business result**: nhà cung cấp nắm mức độ tuân thủ đào tạo của từng 法人 và hiệu chỉnh dữ liệu khi cần.
- **Related screens**: A005 研修進捗, A002 ダッシュボード, A011 CS担当別ダッシュボード, A003 法人.
- **Related business rules**: các tab 新入社員研修, 法定研修設定, bảng 全スタッフ視聴実績 là **GRAY**; ảnh hưởng của việc bỏ qua test tới 修了証 và 完了率 chưa được định nghĩa.

---

## BF-15 — Phát hành ご案内 từ nhà cung cấp tới công ty và staff

- **Actor**: System admin — `お客様へのご案内`: `super`, `content_admin`, `cs_lead`, `cs` ✅; `viewer` 👁.
- **Preconditions**: đã có 法人 để chọn làm đối tượng phát hành.
- **Main business steps**:
  1. `新規作成` với 配信日, 配信対象, 表示先 và checkbox `非公開`.
  2. Bỏ chọn `非公開` → phát hành tự động; giữ `非公開` → dạng nháp.
  3. Tuỳ chọn `PR（㏚）`: 「各社管理画面でポップアップが表示される ※1回のみ表示する（各社管理画面で「確認しました」ボタンを押せば、なくなる）」.
  4. C005 phía Company admin và U007 phía staff hiển thị danh sách タイトル / 案内発行日 kèm nhãn `未読` nếu chưa xem; menu hiển thị số ご案内 chưa xác nhận.
  5. `編集` sau khi phát hành → **phát hành lại**; `非公開` để thu hồi; `削除` để xóa.
- **Business decision points**:
  - 配信対象: `全法人`, theo trạng thái `契約中` / `トライアル中`, hoặc chọn 法人 cụ thể.
  - 表示先: `管理画面・視聴ページ` / `管理画面のみ` / `視聴画面のみ` — 「管理画面は各社の管理画面 / 視聴画面はスタッフの画面」. Đây là điểm quyết định ご案内 có tới portal User (U007) hay không.
  - 配信日 chỉ chọn hôm nay hoặc tương lai.
- **Status changes**: `下書き（非公開）` → phát hành; đã phát hành → `非公開` khi thu hồi. BD **không** có `承認待ち` / `承認済み` / `配信済み`.
- **Final business result**: công ty và (tuỳ cấu hình) staff nhận được thông báo từ nhà cung cấp; số chưa đọc hiển thị trên menu.
- **Related screens**: A012 お客様へのご案内, C005 ご案内, U007 ご案内, U002 ホーム (nút ご案内 + số chưa xác nhận).
- **Related business rules**: sheet 権限マトリックス ghi luồng duyệt 「content_adminとcsの場合：作成→保存→superかつcs_leadに承認要→承認済みの場合に配信」 nhưng sheet A012 không mô tả bước duyệt → xem "Unclear".

---

## BF-16 — Company admin phát hành お知らせ tới staff

- **Actor**: 法人管理者.
- **Preconditions**: switch `スタッフへのお知らせ` của 法人 đang ON (sheet 法人 No.78, r553); 法人 đã có staff.
- **Main business steps** (sheet スタッフへのお知らせ C007):
  1. `新規作成` → nhập タイトル và 内容 (đều bắt buộc), bật/tắt switch `署名`.
  2. `配信する` phát hành cho toàn bộ staff; `キャンセル` để hủy.
  3. Danh sách hiển thị タイトル / 署名必要 / お知らせ発行日 / 作成者 / 確認スタッフ数 / お知らせ配信状態.
  4. `削除` — 「削除したらユーザーの方のお知らせも削除される」.
- **Business decision points**:
  - `署名` ON → 「ユーザー画面にお知らせポップアップを表示とする」; OFF → 「お知らせバナーに未確認のお知らせ数を表示する」 (No.7). Lưu ý User BD ghi 「お知らせモーダルが不要」 (ホーム No.7 備考) → xem "Unclear".
  - 署名 không cần → nhãn `署名必要` và `確認スタッフ数` không hiển thị.
  - Giới hạn: タイトル ≤ 255 ký tự, 内容 ≤ 10000 ký tự (Validation rule — Notices).
- **Status changes**: `配信中` (chưa tới toàn bộ staff) → `配信済み` (đã tới toàn bộ staff). Phía staff, お知らせ được gắn tag `確認済み` **ngay khi staff mở ra** (U006 No.14) — đây là cơ chế làm tăng 確認スタッフ数.
- **Final business result**: toàn bộ staff nhận được thông báo; với thông báo cần ký, số staff đã xác nhận được theo dõi qua 確認スタッフ数.
- **Related screens**: C007 スタッフへのお知らせ, A003 法人 (switch), U002 ホーム (nút お知らせ + số chưa xác nhận), U006 マイページ お知らせ.
- **Related business rules**: 画面ID C007 ghi 「既存システムと同じにする」; Authority matrix **không có dòng** cho C007.

---

## BF-17 — Nhắc nhở và thông báo hành chính tới công ty

- **Actor**: hệ thống (tự động) và System admin.
- **Preconditions**: 法人 bật SMS通知（必須研修リマインド） ở trạng thái `契約済み`.
- **Main business steps**: hệ thống gửi SMS nhắc 必須研修 vào ngày **10** và **25** mỗi tháng; System admin gửi `未収通知` (thông báo chưa thu tiền) tới 法人 đã chọn.
- **Business decision points**: `未契約` → không gửi nhắc; ngày gửi trùng T7/CN → lùi về ngày làm việc trước.
- **Status changes**: SMS通知 `未契約` / `契約済み`.
- **Final business result**: công ty được nhắc về nghĩa vụ đào tạo bắt buộc và khoản chưa thanh toán.
- **Related screens**: A003 法人 (SMS通知, 未収通知).
- **Related business rules**: template 未収通知 định nghĩa ở tài liệu khác; màn システム通知管理 (`/admin/settings/notifications`) có phân trang, số record/trang giống hệ thống cũ (QA029).

---

## BF-18 — Công ty gửi お問い合わせ tới nhà cung cấp

- **Actor**: 法人管理者 (Authority matrix: cả 3 role ✅).
- **Preconditions**: đã đăng nhập portal Company admin.
- **Main business steps** (sheet お問い合わせ C012 — sheet ghi `一旦保留`): chọn カテゴリ, nhập 件名 và 本文, `送信` → 「管理者側に送信する」; xem lại 過去のお問い合わせ (日時 / カテゴリ / 件名 / ステータス).
- **Business decision points**: chưa có — BD ghi 「管理者側に問い合わせを受け取る機能まだない」.
- **Status changes**: cột ステータス tồn tại nhưng tập giá trị và quy tắc chuyển chưa định nghĩa.
- **Final business result**: **chưa hoàn chỉnh** — luồng dừng ở phía gửi.
- **Related screens**: C012 お問い合わせ. Không có màn tương ứng phía System admin.
- **Related business rules**: màn ở trạng thái `一旦保留`.

---

## BF-19 — Vận hành nội bộ nhà cung cấp: tài khoản admin và chuyển giao CS

- **Actor**: System admin — `アドミンユーザー`: **chỉ `super`**.
- **Preconditions**: đăng nhập bằng tài khoản `super`.
- **Main business steps** (sheet アドミンユーザー A013):
  1. `新規追加` / `編集` tài khoản portal System admin và gán role (`super`, `content_admin`, `cs_lead`, `cs`, `viewer`).
  2. `無効化` hoặc `削除` tài khoản — dữ liệu vẫn giữ trong DB.
  3. `担当引継ぎ`: chuyển **hàng loạt** 法人 từ một CS担当 sang CS担当 khác.
  4. Gửi mail reset password cho admin user (QA008 — nút trong modal edit là nút gửi mail reset password).
- **Business decision points**:
  - QA007: admin **đang phụ trách 法人 thì không xóa và không vô hiệu hóa được**, hệ thống hiển thị `担当者{admin_name}は法人を管理中のため削除できません。削除する前に、CS担当者の変更を行ってください。` (trường hợp xóa) và `…無効化できません。無効化する前に、CS担当者の変更を行ってください。` (trường hợp vô hiệu hóa) → phải chạy `担当引継ぎ` trước.
  - QA008: mật khẩu admin user chỉ cần **từ 8 ký tự**, không có format đặc biệt; số điện thoại validate 11 số.
  - QA065: bỏ cột `CS担当` khỏi bảng 事業所一覧 ở màn chi tiết 法人 vì trùng với CS担当 của 法人.
  - Role `viewer` được BD ghi là **chưa chốt** phạm vi quyền.
- **Status changes**: tài khoản admin `有効` ⇄ `無効`.
- **Final business result**: nhân sự nhà cung cấp có đúng quyền truy cập; danh mục 法人 được gán lại người phụ trách khi thay đổi nhân sự.
- **Related screens**: A013 アドミンユーザー, A011 CS担当別ダッシュボード, A003 法人, sheet 権限マトリックス.
- **Related business rules**: đổi CS担当 được ghi vào 重要操作履歴 của 法人.

---

## BF-20 — Staff đăng nhập portal User

- **Actor**: staff (người học).
- **Preconditions**: staff đã được tạo (BF-07) và có メール hoặc 電話番号; 法人 không bị khóa (BF-04).
- **Main business steps** (sheet ログイン U001, `/user/login`):
  1. Chọn `システム言語` nếu cần (mặc định 日本語).
  2. Đăng nhập theo **một trong hai** đường:
     - **Bằng 電話番号**: nhập số điện thoại đã đăng ký → 「電話番号の場合、OTPに配信する」 (No.2) → xác thực bằng OTP.
     - **Bằng メール + パスワード** (No.5–6).
  3. Tuỳ chọn `ログイン情報保存` (No.3).
  4. `ログイン` → chuyển sang ホーム (U002).
  5. Quên mật khẩu: `パスワードをお忘れの方` → 「新しいパスワードが自動生成されて、入力しているメールに送信する想定」 (No.7).
- **Business decision points**: đăng nhập bằng số điện thoại thì đi đường OTP, không dùng mật khẩu; đăng nhập bằng email thì cần mật khẩu. Staff không có メール thì không nhận được thông tin đăng nhập qua mail (BF-07) và không dùng được chức năng quên mật khẩu.
- **Status changes**: không có trạng thái nghiệp vụ; mật khẩu bị thay khi dùng chức năng quên mật khẩu.
- **Final business result**: staff vào được portal học tập với ngôn ngữ mình chọn.
- **Related screens**: U001 ログイン, U002 ホーム, A004 ユーザー (nơi tạo tài khoản), C004 スタッフ管理 (`ログイン情報送信`).
- **Related business rules**: danh sách ngôn ngữ trong User BD gồm 5 lựa chọn (日本語 default, English, 中文, Tiếng Việt, မြန်မာ) trong khi sheet パフォーマンス定義 của System admin BD liệt kê 8 ngôn ngữ cho portal User → xem "Unclear".

---

## BF-21 — Staff học 必須研修: xem video → làm test → gửi báo cáo

- **Actor**: staff.
- **Preconditions**: đã đăng nhập (BF-20); công ty đã thiết lập kế hoạch 必須研修 cho tháng (BF-11); video đang ở trạng thái public (QA099).
- **Main business steps**:
  1. Từ ホーム (U002), staff thấy nút 「未完了の必須研修があります（xx件）」 — **chỉ hiển thị khi trong tháng còn 必須研修 chưa hoàn thành** (No.5) → đi tới `/user/required`.
  2. Tại 必須研修 (U004): chọn năm, xem 年度進捗, xem danh sách theo tháng — mặc định là danh sách video của tháng hiện tại, hiển thị tháng / số còn lại (完了/tổng) / nhãn 法定 cho video pháp định; **tháng chưa được thiết lập thì không hiển thị** (No.2–4).
  3. Hoặc từ 動画 (U005): tìm theo tên video/category, lọc `すべて` / `新人用` (chỉ hiện với staff là 新入社員) / `サービス種別` của 法人 mình, mở category rồi bấm `視聴する`.
  4. **Bước xem video**: thanh bước (ステップ) đổi màu theo tiến trình; nút `次へ進む` **bị vô hiệu tới khi xem xong video** (新入社員研修 No.8 — luồng xem dùng chung).
  5. **Bước làm test**: hiển thị test do admin tạo cho video; **video không có test thì bỏ qua bước này**; nút `テストを提出する` chỉ active khi đã trả lời hết.
  6. **Kết quả test**: nếu có câu sai → hiện 「不正解の回答がありました… 以下の問題が間違えています」 kèm danh sách câu sai, staff bấm `再度テストを受ける` (**reset các câu đã trả lời**) hoặc `トップへ戻る`. Nếu đúng hết → hiện 「全問正解！ {xx}問中{xx}問正解」 và nút `レポート入力へ進む`.
  7. **Bước viết báo cáo**: nhập report; nút tiếp tục **chỉ active khi nhập ≥ 20 ký tự**; sau đó chọn `書き直す` (hủy) hoặc `このまま提出` → 「提出した場合、会社管理画面で確認できる」.
- **Business decision points**:
  - **Hoàn thành bài học = gửi report**, bất kể có test hay không (QA071).
  - Một video được plan ở nhiều tháng: hoàn thành ở tháng trước thì tháng sau tự tính hoàn thành (QA084); nếu không hoàn thành tháng 8 mà hoàn thành tháng 9 thì UI staff hiển thị tháng 8 là đã hoàn thành nhưng **thống kê phía công ty của tháng 8 vẫn là chưa hoàn thành** (QA099).
  - Video thuộc nhiều category: hiển thị thành nhiều record nhưng **hoàn thành một lần là tính hoàn thành cho tất cả** (QA097).
  - Học lại: lần học gần nhất đã report → sinh bản ghi mới; chưa report → update bản ghi hiện tại (QA091). Ngày hoàn thành vẫn tính theo lần report đầu tiên trong tháng (QA077).
  - Category tick "new staff" → chỉ staff được tick `新入社員` xem được (QA099).
- **Status changes**: `動画未聴視` → `動画視聴完了` → `研修完了報告済み` → `研修報告確認済み` (chuỗi trạng thái ở A005). Phía staff, QA098 chốt: `完了` = **đã nộp report**, `視聴中` = chưa nộp report, `すべて` = cả hai và không hiện video chưa được công ty đưa vào kế hoạch.
- **Final business result**: bài học được ghi nhận hoàn thành, báo cáo xuất hiện ở C003 tab 研修報告確認 để công ty xử lý (BF-12), và tiến độ tháng của staff được cập nhật (số liệu tổng hợp 1 lần/ngày — QA072).
- **Related screens**: U002 ホーム, U004 必須研修, U005 動画, U003 新入社員研修 (dùng chung luồng xem), C003 研修管理, A005 研修進捗.
- **Related business rules**: test chỉ có dạng một đáp án đúng (QA015); câu hỏi ≤ 1000 ký tự, đáp án ≤ 200 ký tự (QA032); video đổi/xóa câu hỏi thì kết quả test đã làm vẫn giữ nguyên (QA015).

---

## BF-22 — Staff mới hoàn thành 新入社員研修 trong thời hạn

- **Actor**: staff được tick `新入社員` (BF-07).
- **Preconditions**: staff có 入社日 và 期限 (BD C002 nhắc 「各法人の管理画面で入社日を登録してください」 — dòng này là GRAY ở A005); category được tick 新入社員研修対象 (BF-09).
- **Main business steps** (sheet 新入社員研修 U003, `/user/new-emp`):
  1. ホーム hiển thị thông báo hạn: chưa quá hạn → 「新入社員研修があります（期限：yyyy/mm/dd）」; đã quá hạn → 「新入社員研修の期限を超過しています（期限：yyyy/mm/dd）」 (U002 No.4).
  2. Màn 新入社員研修 hiển thị banner tổng quan: **chưa quá hạn thì banner xanh kèm số ngày còn lại**, **quá hạn thì banner đỏ kèm số ngày vượt**, cùng 完了研修/研修総数 và 入社日 / 期限; bài đã hoàn thành được gắn tag `完了`.
  3. Staff bấm `視聴する` trên từng video và đi theo đúng luồng xem → test → report của BF-21.
- **Business decision points**: quá hạn hay chưa quyết định màu banner và nội dung thông báo ở ホーム; staff hết 90 ngày kể từ ngày đăng ký thì chuyển từ `新入社員` sang `スタッフ` (sheet ユーザー r47). QA099 chốt 新入社員 là **luồng riêng**, dùng target tag để xác định ai buộc phải xem video trong kế hoạch.
- **Status changes**: từng bài học theo BF-21; trạng thái quá hạn / chưa quá hạn của cả gói 新入社員研修.
- **Final business result**: staff mới hoàn thành gói đào tạo nhập việc; công ty theo dõi qua card 新入社員 ở C002 (số vượt nghĩa là staff mới chưa hoàn thành đào tạo — QA059).
- **Related screens**: U002 ホーム, U003 新入社員研修, C002 ダッシュボード, A005 研修進捗 (tab 新入社員研修 — GRAY).
- **Related business rules**: nút `新入社員研修` ở ホーム được ghi `一旦保留` (U002 No.8), tab 新入社員研修 ở A005 và 新入社員研修管理 ở C003 đều **GRAY** → luồng này chỉ in-scope một phần.

---

## BF-23 — Staff nhận và xác nhận お知らせ / ご案内

- **Actor**: staff.
- **Preconditions**: Company admin đã phát hành お知らせ (BF-16) hoặc System admin đã phát hành ご案内 với 表示先 bao gồm 視聴画面 (BF-15).
- **Main business steps**:
  1. Tại ホーム (U002), nút `お知らせ` hiển thị **số thông báo chưa xác nhận** — 「各社管理の「スタッフへのお知らせ」画面で作成したお知らせを表示する」 → `/user/notices` (No.7). Nút `ご案内` hiển thị số chưa xác nhận của ご案内 từ System admin → `/user/announcements` (追加 r48).
  2. Danh sách お知らせ (U006 No.13): hiển thị タイトル và 発行日; bấm vào để mở nội dung. Lọc theo `すべて` / `確認済み` / `未確認` (No.12).
  3. **Mở お知らせ ra thì tag `確認済み` được gắn tự động** (U006 No.14).
  4. Danh sách ご案内 (U007): タイトル / 案内発行日 / nhãn `未読` nếu chưa xem; bấm vào để xem chi tiết; 「メニューで表記されている数字が確認していないご案内数とする」.
- **Business decision points**: お知らせ có `署名` ON thì theo C007 sẽ hiện popup phía staff, nhưng User BD ghi 「お知らせモーダルが不要」 → xem "Unclear". ご案内 chỉ tới staff khi 表示先 của nó bao gồm 視聴画面 (BF-15).
- **Status changes**: お知らせ `未確認` → `確認済み` (tự động khi mở). ご案内 `未読` → đã đọc. Việc staff xác nhận làm tăng `確認スタッフ数` phía C007 và là cơ sở để お知らせ chuyển `配信中` → `配信済み`.
- **Final business result**: staff nắm thông tin từ công ty và từ nhà cung cấp; phía công ty đo được số staff đã xác nhận.
- **Related screens**: U002 ホーム, U006 マイページ (お知らせ), U007 ご案内, C007 スタッフへのお知らせ, C005 ご案内, A012 お客様へのご案内.
- **Related business rules**: xóa お知らせ phía công ty thì bản phía staff cũng mất (BF-16); ページUI của ご案内 phía staff làm giống ご案内 của 各社管理画面 (U002 備考).

---

## BF-24 — Staff nhận feedback và viết lại báo cáo

- **Actor**: staff.
- **Preconditions**: staff đã gửi báo cáo (BF-21); Company admin đã bấm `フィードバック` hoặc `書き直しを依頼` (BF-12).
- **Main business steps**:
  1. ホーム hiển thị nút 「レポートの書き直し依頼が x件 あります」 — **chỉ hiện khi report đã gửi bị feedback hoặc bị yêu cầu viết lại** → `/user/feedbacks` (U002 No.6). Banner cũng xuất hiện ở trang chủ (C003 No.20–21).
  2. Tại マイページ → `レポートフィードバック` (U006 No.6): lọc `未読のみ` / `全て`.
  3. **Trường hợp bị yêu cầu viết lại** (No.17): hiển thị tên video, nội dung report đã gửi, dòng 「【書き直し依頼】レポートの書き直しをお願いします。」 và thời điểm admin bấm yêu cầu.
  4. Bấm `レポートを書き直す` (No.18) → **đi trực tiếp tới trang viết report**; 「レポート書き直しの場合に、テストが終わったため、再度テストする必要がない」 — **không phải làm lại test**.
  5. **Trường hợp có feedback** (No.19): hiển thị tên video, nội dung report, comment của admin và thời điểm; staff bấm `既読にする` → mục đó rời khỏi danh sách `未読のみ`.
  6. **Trường hợp đã xác nhận** (No.21): hiển thị report kèm tag `確認済み` và nút đánh dấu đã đọc.
- **Business decision points**: viết lại report thì bỏ qua bước test; staff sửa report thì **update trực tiếp vào report cũ** và phía admin vẫn giữ trạng thái confirmed (QA089); nếu staff học lại video từ đầu sau khi đã report thì sinh **luồng report mới** (QA089, QA091).
- **Status changes**: từng mục feedback `未読` → `既読`. Báo cáo quay lại quy trình xử lý của BF-12 (admin có thể đổi 品質 sau khi staff report lại — QA090).
- **Final business result**: chất lượng báo cáo đào tạo được cải thiện qua vòng phản hồi giữa công ty và staff.
- **Related screens**: U002 ホーム, U006 マイページ レポートフィードバック, C003 tab 研修報告確認.
- **Related business rules**: feedback ≤ 10000 ký tự (QA092); thời điểm hiển thị lấy theo lúc admin bấm yêu cầu viết lại.

---

## BF-25 — Staff quản lý thông tin cá nhân, thành tích và lịch sử học

- **Actor**: staff.
- **Preconditions**: đã đăng nhập (BF-20).
- **Main business steps** (sheet マイページ U006, `/user/mypage`):
  1. Xem 自分情報: ảnh đại diện (**sửa được**), 名前 / 法人 / 受講数 (**không sửa được**), ポイント (`一旦保留`). Không có ảnh thì dùng ảnh mặc định; bấm ảnh ở ホーム cũng vào マイページ.
  2. Xem 表示カード: 総受講時間 (tổng thời lượng video đã xem xong), 連続受講, số メダル đạt được.
  3. Xem メダル: 10 mốc theo số video đã xem xong (1 video = 「はじめの一歩」, 3, 5, … tới 100 video).
  4. Xem 視聴履歴 (No.8): 「完了した動画のみ一覧にする」 — chỉ liệt kê bài học đã hoàn thành.
  5. Vào 設定 (No.10): sửa 氏名 (bắt buộc), メールアドレス, 電話番号 (**cần ít nhất một trong hai**) → `保存する`; đổi mật khẩu bằng cách nhập `パスワード変更` + `パスワード再入力`; đổi 文字サイズ; `ログアウト`.
- **Business decision points**:
  - QA101: `受講数` đếm các video **đã nộp report** (không phải chỉ xem hết video); `総受講時間` = tổng thời lượng các video đó; phạm vi đếm **giữ nguyên khi staff đổi branch** nên số liệu và メダル không bị giảm.
  - QA098: ở 視聴履歴, `完了` = đã nộp report, `視聴中` = chưa nộp report, ngày trên nhãn `完了` là **ngày nộp report**; staff đổi branch thì lịch sử **chỉ lấy branch hiện tại** (dữ liệu branch cũ không hiện nhưng vẫn lưu trong DB).
  - QA102: video bị admin chuyển sang unpublished thì lịch sử **vẫn hiển thị** kèm nhãn cho biết video đã unpublished; chuyển lại published thì hiển thị như trước.
  - 「変更した場合に管理者側にも更新される想定」 — staff tự sửa thông tin thì dữ liệu phía quản trị cũng được cập nhật.
- **Status changes**: không có trạng thái nghiệp vụ; thay đổi thông tin cá nhân và mật khẩu.
- **Final business result**: staff tự quản lý được thông tin, theo dõi được thành tích học tập; dữ liệu đồng bộ về phía quản trị.
- **Related screens**: U006 マイページ (+ 設定, 視聴履歴), U002 ホーム, A004 ユーザー, C004 スタッフ管理.
- **Related business rules**: `受講状況` được ghi 「不要」 và `修了証` ghi 「対応不要」 → **GRAY**; メダル chỉ làm theo spec, khách chưa định nghĩa hết các loại medal (QA101); ランク và ポイント `一旦保留`; màn 設定 bỏ các mục その他 / 利用規約 / プライバシーポリシー.

---

## Điểm trước đây chưa rõ, nay đã được QA sheet chốt

| Vấn đề | Kết luận | Nguồn |
| --- | --- | --- |
| Ý nghĩa ký hiệu bảng tiến độ tháng | `◎` 全完了 / `n/m` 一部視聴 / `X` 未視聴 / `予` 計画済み / `—` 設定なし | QA068 |
| Cách tính 視聴率 | Tỷ lệ user đạt 全完了 trên tổng user | QA069 |
| Định nghĩa "hoàn thành bài học" | Staff **gửi report**, bất kể có test hay không | QA071 |
| Ngày hoàn thành bài học | Lần gửi report **đầu tiên trong tháng** | QA077 |
| Mốc cập nhật 完了率 / thống kê | **1 lần/ngày**, không real time | QA018, QA072 |
| Trạng thái sau `書き直しを依頼` | Gọi là `rewrite_require`; đã confirmed vẫn yêu cầu viết lại được; staff sửa thì update report cũ, admin giữ confirmed; học lại sau khi report thì sinh luồng mới | QA089, QA090, QA091 |
| Quy tắc gán 品質 tự động | Report có 5 ký tự liên tục giống nhau → `要改善`; còn lại → `良好` | QA089 |
| Có unlock 法人 hay không | Có — sau khi lock, nút lock chuyển thành unlock | QA033 |
| Cơ chế lock user | User lock **theo 法人**, không set trực tiếp trên user | QA021 |
| Trạng thái `削除済み` của 法人 | Có đường khôi phục về `契約中`, user của 法人 cũng được khôi phục | QA066 |
| Trần staff tính những ai | Không tính tài khoản manager, chỉ tính tài khoản dùng để xem video; min = 1, default 10 | QA046, QA009 |
| Ai được sửa kế hoạch 必須研修 | **Chỉ tenant**, office không được sửa | QA064 |
| Năm tài chính | Cố định tháng 4 → tháng 3 năm sau | QA067 |
| Video plan ở nhiều tháng | Hoàn thành một tháng thì tháng còn lại tự tính hoàn thành | QA084 |
| Cách tải 研修実績DL | Chuyển sang **bất đồng bộ**: tạo file → lưu → tải; file tồn tại tới request kế tiếp | QA095 |
| Chính sách xóa | Video xóa cứng (kèm S3); category / service type / target tag bị chặn xóa khi còn liên kết | QA037, QA056 |
| Đổi 法人種別 | Có bộ quy tắc đầy đủ cho 6 hướng chuyển đổi | QA060 |

---

## Rule hiện hành khi BD và QA sheet lệch nhau

Những điểm BD ghi một đằng, QA sheet chốt một nẻo. **Lấy cột "Rule hiện hành" làm expected result**; cột BD chỉ giữ lại để biết chỗ nào trong tài liệu cần sửa. ID giữ nguyên như khi còn nằm ở mục "Unclear" để các tham chiếu cũ không gãy.

| ID | Điểm | BD ghi | QA sheet chốt | Rule hiện hành |
| --- | --- | --- | --- | --- |
| UF-01 | `フィードバック` có đổi trạng thái báo cáo không | C003 No.21 (r136, cột `備考`): chưa bấm `確認済み` mà gửi feedback thì **tự gán `確認済み`** | QA088: gửi feedback **không làm thay đổi bất kỳ trạng thái nào**; UI nên hiện feedback đã ghi trước đó và **update** thay vì tạo mới | Feedback **không** đổi trạng thái báo cáo. Ghi chú ở C003 No.21 `備考` cần bỏ khỏi BD. |
| UF-04 | Lock ở cấp user | ユーザー r139: màn sửa user có checkbox `ロック` | QA021: user lock **kế thừa từ 法人**, không set trực tiếp trên user; `有効` / `ロック` ở danh sách chỉ là nút filter | Không có thao tác lock trên từng user. Checkbox ở r139 cần bỏ khỏi BD. |
| UF-06 | Bước 2 của luồng tạo 法人 | Bước 2 `サービス・事業所` có phần đăng ký 事業所 | QA012: **bỏ** phần 事業所 khỏi bước 2, đổi tiêu đề thành `サービス` | Bước 2 chỉ chọn サービス種別 và switch chức năng; 事業所 thêm sau ở màn chi tiết 法人 (BF-06). |
| UF-07 | Trùng title video | Không quy định | QA005 ban đầu trả lời không cho trùng toàn hệ thống rồi tự ghi 「update lại spec là có trùng」; QA030 (muộn hơn) chốt **cho trùng** | Cho phép trùng title video trong cùng category. |
| UF-13 | Ai sửa được kế hoạch 必須研修 | Authority matrix: 研修管理 ✅ cho cả `tenant` / `area` / `branch` | QA064: kế hoạch gắn với サービス種別 dùng chung giữa nhiều office nên **chỉ tenant** được sửa | Chỉ role `tenant` sửa được kế hoạch; office chỉ xem. |
| UF-41 | Tên category có unique không | Validation rule r33: rule `Unique` kèm message `カテゴリ名は既に入力されています。` | QA030: 「các cate có thể trùng tên」 — trùng với 備考 của No.9 (r88) 「カテゴリ名が重複できる」 | **Cho phép trùng tên category**; rule `Unique` và message ở r33 không áp dụng. |
| UF-42 | Quan hệ video ↔ category | 動画管理 r47 cột N: 「1本動画は1カテゴリのみ入る」 | QA030 / QA035 / QA096 / QA097 đều xây trên quan hệ **N–N** | 1 video gắn được **nhiều** category. Ghi chú ở r47 đã lỗi thời. |

---

## Unclear / Needs Confirmation

Các điểm khiến luồng end-to-end **không** dựng được trọn vẹn. Không suy diễn, cần xác nhận với BA/dev. Mục này chỉ còn những điểm **QA sheet chưa chốt**: BD mâu thuẫn với BD, QA mâu thuẫn với QA, hoặc chưa nguồn nào trả lời.

### Xung đột giữa các nguồn

| ID | Điểm | Vấn đề |
| --- | --- | --- |
| UF-02 | Popup お知らせ phía staff | C007 No.7 nói 署名 ON → 「ユーザー画面にお知らせポップアップを表示」; User BD ホーム No.7 備考 ghi 「お知らせモーダルが不要」. |
| UF-03 | Số ngôn ngữ của portal User | sheet パフォーマンス定義 No.4 liệt kê 8 ngôn ngữ (JP, EN, VI, ZH, ID, NE, TL, MY); User BD U001/U002 chỉ liệt kê 5 (日本語, English, 中文, Tiếng Việt, မြန်မာ). Ngoài ra nhãn 「မြန်မာ（タイ語）」 dùng chữ Myanmar nhưng chú thích là **tiếng Thái**. |
| UF-05 | Xóa video và dữ liệu tiến độ | **QA mâu thuẫn với QA**: QA037 nói xóa video là xóa cứng và **plan / training progress bị xóa theo**; QA045 nói khi xóa video thì **thông số training progress trước đó vẫn giữ nguyên**. |
| UF-08 | Lịch sử học khi staff chuyển branch | **QA mâu thuẫn với QA**: phía công ty hiển thị **2 record** cho 2 branch (QA075); phía staff 視聴履歴 **chỉ lấy branch hiện tại**, dữ liệu branch cũ không hiện (QA098). |
| UF-09 | Luồng duyệt ご案内 | sheet 権限マトリックス (r36) mô tả `content_admin`/`cs` phải được `super`/`cs_lead` duyệt mới phát hành; sheet A012 chỉ có `非公開` / phát hành tự động, không có 承認待ち / 承認済み. |
| UF-10 | Switch chức năng khác nhau giữa 新規登録 và 編集 | Luồng đăng ký chỉ có `コンテンツ` và `スタッフへのお知らせ` (còn lại GRAY); luồng 編集 có đủ 7 switch. |
| UF-11 | Field `TenantID` trong Validation rule | Có rule `TenantID Required → 法人を選択してください` nhưng form tạo staff ở C004 không có field này. |
| UF-12 | Tên nút vs định dạng xuất | `年間計画Excelで出力` mang tên Excel nhưng mô tả nội dung là CSV. |
| UF-43 | Nút 編集 / 削除 category có in scope không | Bảng danh sách category (No.5, r44) có cột `操作` gồm 編集 và 削除, nhưng cột N cùng dòng ghi 「操作は保留」. Cả r44, r46 (No.7 編集) và r47 (No.8 削除) đều **không tô xám**, nên chưa rõ hai thao tác này có bị hoãn hay không. |

### Trạng thái thiếu định nghĩa chuyển đổi

| ID | Điểm | Vấn đề |
| --- | --- | --- |
| UF-14 | `解約` vs `離脱` | Cả hai là lựa chọn ở luồng 編集 nhưng không có tiêu chí nghiệp vụ để phân biệt. |
| UF-15 | お知らせ `配信中` → `配信済み` | Không nêu điều kiện/trigger chuyển ("đã tới toàn bộ staff" được đo thế nào). Cơ chế gắn tag `確認済み` khi staff mở (U006 No.14) có phải là căn cứ hay không cũng chưa nói. |
| UF-16 | ステータス của お問い合わせ | Cột tồn tại nhưng tập giá trị và quy tắc chuyển ghi 「要確認」. |
| UF-17 | `休職中` | Chỉ ghi 「休職中機能を追加」; dashboard loại 休職中 khỏi 完了率 nhưng không có luồng đặt trạng thái này. |
| UF-18 | Trạng thái "chưa gửi báo cáo" phía công ty | C003 chỉ có báo cáo đã có 報告提出日; phía staff có khái niệm `視聴中` (chưa nộp report — QA098) nhưng không rõ phía công ty có nhìn thấy nhóm này không. |

### Bước nghiệp vụ thiếu

| ID | Điểm | Vấn đề |
| --- | --- | --- |
| UF-19 | Hệ quả khóa 法人 lên phiên đang mở | Không nêu người đang đăng nhập có bị đăng xuất ngay hay không. |
| UF-20 | Thông báo khi đăng nhập vào 法人 `ロック中` / `解約` / `離脱` | Validation rule chỉ có message cho email/password sai (cả C001 và U001). |
| UF-21 | Import CSV tạo staff phía Company admin | C004 chỉ có tạo thủ công; rule `File CSV` / `CSV file only` trong Validation rule không gắn màn cụ thể. Phía System admin có `一括登録（CSV）`. |
| UF-22 | Mật khẩu staff khi không nhập | Checkbox mật khẩu mặc định tắt; BD không nêu hệ thống có sinh mật khẩu tự động hay staff chỉ đăng nhập được bằng OTP qua số điện thoại (U001 No.2). |
| UF-23 | Bước lưu kế hoạch 必須研修 | Tab 必須研修設定 mô tả tick checkbox theo tháng nhưng không mô tả bước lưu/áp dụng. |
| UF-24 | Luồng 異動 / 兼務 của staff | Thao tác trên C004 là GRAY, nhưng hệ quả lên dữ liệu đào tạo lại in-scope → BF-08 chỉ có phần hệ quả. |
| UF-25 | Nội dung email `ログイン情報送信` | Ghi 「別当で定義」. |
| UF-26 | 「登録後の流れ」 của 法人 | BD ghi 「登録後の流れ不要」 (r456). |
| UF-27 | Thời điểm kiểm tra trần staff | Message có placeholder `{limit}` nhưng không nêu validate khi tạo từng staff, khi import, hay cả hai. |
| UF-28 | Ảnh hưởng của việc bỏ qua test (A005 編集) | Không nêu tác động tới 修了証 và 完了率. |
| UF-29 | Định dạng file tải về | `研修実績（運営指導用）` vẫn đang chờ (QA103 `Đang chờ team xác nhận`); format import 事務所 và 一括登録 user ghi "định nghĩa ở tài liệu khác". |
| UF-30 | Hết hạn 新入社員研修 thì sao | U003 mô tả banner đỏ và số ngày vượt hạn, nhưng không nêu hệ quả nghiệp vụ (có bị chặn học, có báo cho quản lý, có ảnh hưởng thống kê). |
| UF-31 | Nguồn của 入社日 / 期限 của 新入社員研修 | A005 có ghi 「各法人の管理画面で入社日を登録してください」 nhưng dòng đó là **GRAY**, và C004 không có field 入社日. |
| UF-32 | OTP khi staff đăng nhập bằng số điện thoại | U001 No.2 chỉ ghi 「電話番号の場合、OTPに配信する」 — không nêu kênh gửi (SMS?), hiệu lực, số lần thử lại. |
| UF-33 | ポイント / ランク / メダル ngoài spec | ポイント và ランク `一旦保留`; メダル chỉ làm 10 mốc theo spec dù mock có nhiều loại hơn (QA101). |
| UF-34 | Phụ đề AI | Nhà cung cấp AI chưa chọn, chức năng để phase sau (QA013, QA017 vẫn `Đang chờ team xác nhận`). |
| UF-35 | Chiến lược chuyển đổi hệ thống | QA047: chạy song song hệ thống cũ và mới, migrate một phần rồi migrate toàn bộ (khi đó xóa cứng dữ liệu cũ và migrate lại). Ảnh hưởng tới dữ liệu lịch sử và danh sách năm chọn được (QA073) nhưng chưa có kế hoạch chi tiết. |

### Phân quyền thiếu

| ID | Điểm | Vấn đề |
| --- | --- | --- |
| UF-36 | Authority matrix của Company admin thiếu dòng | Không có dòng cho C006 `コンテンツ` và C007 `スタッフへのお知らせ`. |
| UF-37 | Role `viewer` của System admin | BD ghi chưa nằm trong thiết kế ban đầu, cần thảo luận riêng. |
| UF-38 | Vai trò manager trong công ty | 階層 (`スタッフ` / `リーダー` / `マネージャー`) là tag phân loại, không phải quyền; QA046 lại nhắc tới "acc manager" khi tính trần staff → chưa rõ manager là loại tài khoản riêng hay chỉ là tag. |
| UF-39 | Phạm vi xem của tenant vs branch ở tab 進捗確認 | QA074 vẫn `Đang chờ team xác nhận`. |
| UF-40 | Phạm vi tác động của FC本部 / グループ親会社 | Authority matrix ghi **要確認** cho 研修管理 và 資料ダウンロード; QA054 nói FC本部 và FC加盟店 **chưa làm trong phase này**. |

### Màn ngoài phạm vi (không dựng luồng)

`A007 修了証管理` (一旦対応不要), `A009 法定研修トピック` (対応不要), `A010 営業管理` (対応不要), `C009 契約情報` (一旦保留), `C010 操作マニュアル` (一旦保留), `C011 資料ダウンロード` (対応不要), `C012 お問い合わせ` (一旦保留 — vẫn ghi ở BF-18 vì có mô tả nghiệp vụ). Sheet 権限マトリックス có 4 menu không có sheet BD riêng: `デフォルト研修プラン`, `運営指導対策管理`, `訓練・委員会管理`, `システム通知管理`. Trong User BD, các mục GRAY gồm nút `戻る` ở ホーム, `受講状況` và `修了証` ở マイページ, filter của 視聴履歴, và mục `その他` ở 設定.
