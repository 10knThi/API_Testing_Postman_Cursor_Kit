# WELVIE LEARNING — Role & Permission

Nguồn: sheet `権限マトリックス` (System admin BD), sheet `Authority matrix` (Company admin BD), sheet `法人` (System admin BD — các switch chức năng).

Ký hiệu BD: `✅` = có quyền, `❌` = không có quyền, `👁` = chỉ xem, `◎` = login được, `×` = không login được.

## 1. Portal × Account role (login được hay không)

| Portal / Account role | System admin | Company admin | User | FC dashboard | Note |
| --- | --- | --- | --- | --- | --- |
| Top | ◎ | ◎ | ◎ | ◎ | 存在しないように (không tạo màn Top) |
| Admin | ◎ | × | × | × | 既存システムと同じにする |
| Company | × | ◎ | × | ◎ | **Chỉ `tenant_admin` mới login được** |
| User (Staff) | × | × | ◎ | × | |
| FC | × | × | × | ◎ | 一旦保留 |

Đây là nguồn expected cho các case cross-portal login (ví dụ: account Company admin login vào `/admin/login` phải thất bại).

## 2. Role của portal System admin

5 role: `super` (システム最高管理者), `content_admin` (コンテンツ管理者), `cs_lead` (CSリーダー), `cs` (CS担当者), `viewer` (閲覧専用).

| Menu | Route | super | content_admin | cs_lead | cs | viewer | Note |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Dashboard | `/admin` | 👁 | 👁 | 👁 | 👁 | 👁 | Số liệu đổi theo account: super/content_admin = KPI toàn bộ công ty; cs_lead/cs = KPI công ty mình phụ trách |
| 法人 Tenants | `/admin/tenants` | 👁 | 👁 | 👁 | 👁 | 👁 | |
| 法人 — 新規作成 | | ✅ | ❌ | ✅ | ✅ | ❌ | |
| 法人 — 削除 | | ✅ | ❌ | ❌ | ❌ | ❌ | Chỉ super xóa được |
| 法人 — 編集 | | ✅ | ❌ | ✅ | ✅ | ❌ | |
| ユーザー Staff | `/admin/users` | 👁 | ❌ | 👁 | 👁 | 👁 | |
| ユーザー — 新規作成 | | ✅ | ❌ | ✅ | ✅ | ❌ | |
| ユーザー — 削除 | | ✅ | ❌ | ✅ | ❌ | ❌ | Xóa là **logical delete** — dữ liệu 受講履歴 / 修了証 vẫn giữ trong DB |
| ユーザー — 編集 | | ✅ | ❌ | ✅ | ✅ | ❌ | |
| 研修の進捗 | `/admin/training` | 👁 | ❌ | 👁 | 👁 | 👁 | |
| 動画管理 | `/admin/videos` | ✅ | ✅ | 👁 | 👁 | 👁 | |
| 修了証管理 | `/admin/certificates` | ✅ | ✅ | ✅ | ✅ | 👁 | super/content_admin sửa được template; cs_lead/cs chỉ xem 発行履歴 |
| ウェビナー管理 | `/admin/webinars` | ✅ | ✅ | ✅ | ✅ | 👁 | |
| 法定研修トピック | `/admin/statutory-topics` | ✅ | ✅ | 👁 | 👁 | 👁 | |
| 営業管理 | `/admin/sales` | 👁 | 👁 | 👁 | 👁 | 👁 | 定例で確認要 |
| CS担当別DB | `/admin/cs-dashboard` | ✅ | ❌ | ✅ | 👁 | 👁 | |
| お客様へのご案内 | `/admin/notifications` | ✅ | ✅ | ✅ | ✅ | 👁 | super/cs_lead: tạo + duyệt + phát hành. content_admin/cs: tạo → lưu → **cần super hoặc cs_lead duyệt** mới phát hành được |
| アドミンユーザー | `/admin/admin-users` | ✅ | ❌ | ❌ | ❌ | ❌ | Chỉ super |
| サービス種別管理 | `/admin/service-types` | ✅ | ✅ | ✅ | ✅ | ❌ | |
| デフォルト研修プラン | `/admin/default-plans` | ✅ | ✅ | ✅ | ✅ | ❌ | |
| 運営指導対策管理 | `/admin/inspection` | ✅ | ✅ | ✅ | 👁 | ❌ | |
| 訓練・委員会管理 | `/admin/drill-committee` | ✅ | ✅ | ✅ | 👁 | ❌ | |
| システム通知管理 | `/admin/settings/notifications` | ✅ | ❌ | ✅ | ❌ | ❌ | |

Lưu ý: role `viewer` được BD ghi là **chưa chốt** ("当初の設計ではこのロールは含まれていなかった... 別途ご相談") → không kết luận FAIL cho case viewer khi chưa có xác nhận.

## 3. 法人種別 (Tenant type)

5 loại: `一般`, `FC本部`, `グループ親会社`, `FC加盟店/加盟法人/加盟店`, `グループ子会社`.

| Chức năng | 一般 | FC本部 | グループ親会社 | FC加盟店/加盟法人/加盟店 | グループ子会社 |
| --- | --- | --- | --- | --- | --- |
| FC dashboard access | ❌ | ✅ | ❌ | ❌ | ❌ |

## 4. Role của portal Company admin

3 role theo cấp tổ chức: `tenant` (本部), `area` (エリア), `branch` (事務所).

| Menu | Route | tenant (本部) | area (エリア) | branch (事務所) | Note |
| --- | --- | --- | --- | --- | --- |
| ご案内 | `/company/announcements` | 👁 | 👁 | 👁 | |
| Dashboard | `/company/dashboard` | 👁 | 👁 | 👁 | Chỉ xem công ty mình; FC本部 / グループ親会社 xem được cả chi nhánh / công ty con |
| 研修管理 | `/company/progress` | ✅ | ✅ | ✅ | Chỉ tác động công ty mình; FC本部 / グループ親会社 tác động được chi nhánh / công ty con — **要確認** |
| スタッフ管理 | `/company/staffs` | ✅ | ✅ | ✅ | Chỉ tạo/sửa/xóa staff công ty mình; FC本部 / グループ親会社 tác động được chi nhánh / công ty con |
| 会社情報 | `/company/information` | ✅ | 👁 | 👁 | Chỉ tenant sửa được |
| 契約情報 | `/company/contract` | 👁 | 👁 | 👁 | |
| 操作マニュアル | `/company/manual` | 👁 | 👁 | 👁 | Admin tạo nội dung |
| 資料ダウンロード | `/company/documents` | ✅ | ✅ | ✅ | Admin tạo nội dung — **要確認** |
| お問い合わせ | `/company/contact` | ✅ | ✅ | ✅ | |

**Gap đã biết**: matrix **không có dòng** cho `スタッフへのお知らせ` (C007) và `コンテンツ` (C006) → chưa có expected cho phân quyền 2 màn này. Xem `../test-knowledge/open-questions.md`.

## 5. Switch chức năng bật menu cho Company admin

Sheet `法人` (A003) — mỗi switch là Boolean, `ON にすると各社管理画面に該当メニューが追加される`:

| No (A003) | Switch | Trạng thái spec |
| --- | --- | --- |
| 75 | 訓練・委員会 | 保留 |
| 76 | 運営指導対策 | 保留 |
| 77 | コンテンツ | Huy đang xem xét spec → định nghĩa ở Company admin BD |
| 78 | スタッフへのお知らせ | Huy đang xem xét spec → định nghĩa ở Company admin BD |
| 79 | 外部サービスリンク | 保留 |
| 80 | 特定事業所加算（個別研修計画） | 保留 |
| 81 | 複数事業所所属（兼務） | 保留 |

Hệ quả khi test Company admin: menu vắng mặt **chưa chắc là bug** — phải kiểm tra switch tương ứng của法人 đó trên System admin trước khi kết luận FAIL.

## 6. Lệch route giữa BD và matrix

Matrix dùng route khác với ô 画面URL của sheet màn hình ở vài chỗ — khi test lấy route thật của app:

| Màn | 画面URL trong sheet | Route trong matrix |
| --- | --- | --- |
| 法人 | `~admin/tenant` | `/admin/tenants` |
| コンテンツ (A008) | `~admin/contents` | `/admin/webinars` (ウェビナー管理) |
| ダッシュボード (C002) | `~company/dashboad` | `/company/dashboard` |
| 会社情報 (C008) | `~company/infomations` | `/company/information` |

Ngoài ra matrix có 4 menu **không có sheet BD riêng**: `デフォルト研修プラン`, `運営指導対策管理`, `訓練・委員会管理`, `システム通知管理`.
