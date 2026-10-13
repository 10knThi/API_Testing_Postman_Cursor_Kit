# WELVIE LEARNING — Screen Map

Trích trực tiếp từ ô 画面ID / 画面名 / 画面URL của từng sheet BD. Cột "Đối ứng" lấy từ 備考 cạnh ô 画面ID.

## System admin (`~admin/...`)

| 画面ID | Sheet BD | 画面名 | URL | Đối ứng | Ghi chú |
| --- | --- | --- | --- | --- | --- |
| A001 | ログイン | Adminログイン画面 | ー | Có | URL thực tế `/admin/login` |
| A002 | ダッシュボード | ダッシュボード画面 | `~admin/dashboad` | Có | KPI: số入居者 theo status, số staff, biểu đồ theo tháng. URL BD sai chính tả (dashboad) |
| A003 | 法人 | 法人画面 | `~admin/tenant` | Có | Tạo/sửa/xóa tenant, tình trạng hợp đồng, CS phụ trách, nhóm親子, account lockout |
| A004 | ユーザー | ユーザー画面 | `~admin/users` | Có | Quản lý user (staff) |
| A005 | 研修進捗 | 研修進捗画面 | `~admin/training` | Có | Có tab 必須研修進捗, レポート確認, 全スタッフ実績 |
| A006 | 動画管理 | 動画管理画面 | `~admin/videos` | Có | Có tab カテゴリ管理 |
| A007 | 修了証管理 | 修了証管理画面 | `~admin/certificates` | **一旦対応不要** | Template修了証 + tab 発行履歴 |
| A008 | コンテンツ | コンテンツ管理画面 | `~admin/contents` | Có | Upload video seminar, hiện sang toàn bộ Company admin. Route thực tế đã test: `/admin/webinars` |
| A009 | 法定研修トピック | 法定研修トピック画面 | `~admin/statutory-topics` | **対応不要** | |
| A010 | 営業管理 | 営業管理画面 | `~admin/sales` | **対応不要** | |
| A011 | CS担当別ダッシュボード | CS担当別ダッシュボード画面 | `~admin/cs-dashboard` | Có | |
| A012 | お客様へのご案内 | お客様へのご案内画面 | `~admin/notifications` | Có | Ghi chú tạo ra sẽ hiển thị ở `ご案内` của Company admin |
| A013 | アドミンユーザー | アドミンユーザー画面 | `~admin/admin-users` | Có | Tạo account cho portal admin |
| A014 | サービス種別管理 | サービス種別管理画面 | `~admin/service-types` | Có | |

## Company admin (`~company/...`)

| 画面ID | Sheet BD | 画面名 | URL | Đối ứng | Ghi chú |
| --- | --- | --- | --- | --- | --- |
| C001 | ログイン | 各社管理ログイン画面 | ー | Có | URL thực tế `/company/login` |
| C002 | ダッシュボード | ダッシュボード画面 | `~company/dashboad` | Có | URL BD sai chính tả (dashboad) |
| C003 | 研修管理 | 研修管理画面 | `~company/progress` | Có | 4 tab in-scope: 進捗確認 / 研修報告確認 / 研修実績DL / 必須研修設定 |
| C004 | スタッフ管理 | スタッフ管理画面 | `~company/staffs` | Có | |
| C005 | ご案内 | ご案内画面 | `~company/announcements` | Có | Nhận ご案内 từ A012 |
| C006 | コンテンツ | コンテンツ画面 | `~company/contents` | Có | Nhận video từ A008 |
| C007 | スタッフへのお知らせ | スタッフへのお知らせ画面 | `~company/noties` | Có — 既存システムと同じにする | URL BD nghi typo (`noties`) |
| C008 | 会社情報 | 会社情報画面 | `~company/infomations` | Có | URL BD sai chính tả (infomations) |
| C009 | 契約情報 | 契約情報画面 | `~company/contract` | **一旦保留** | |
| C010 | 操作マニュアル | マニュアル画面 | `~company/manual` | **一旦保留** | |
| C011 | 資料ダウンロード | 資料ダウンロード画面 | `~company/documents` | **対応不要** | |
| C012 | お問い合わせ | お問い合わせ画面 | `~company/contact` | **一旦保留** | |

## Lưu ý khi dùng map này

- BD viết URL dạng `~xxx/yyy`; URL thật trên app là `/xxx/yyy`. Một số URL trong BD **sai chính tả** (`dashboad`, `infomations`, `noties`) — khi execute luôn lấy URL thật của app rồi ghi nhận sai lệch vào QnA, không FAIL vì typo BD.
- Màn `対応不要` / `一旦保留` / `一旦対応不要`: **không sinh checklist** trừ khi Bui yêu cầu rõ.
- Trạng thái checklist theo màn: xem `test case/welvie/` (tên file bắt đầu bằng 画面ID).
