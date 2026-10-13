# Requirement -- Training Management (研修管理)

## Quy ước phạm vi

-   **Ô/cell màu xám (`#999999`)**: Không triển khai.
-   **Ô/cell màu vàng**: Nội dung **thay đổi so với thiết kế hiện tại và
    phải triển khai**.
-   Các nội dung không tô màu: Requirement hiện tại, vẫn triển khai nếu
    không nằm trong vùng xám.
-   Các ghi chú `要確認` / `一旦保留` không được tự chuyển thành
    requirement chính thức nếu thiết kế chưa chốt.

------------------------------------------------------------------------

# 1. Tiến độ đào tạo

## REQ-TRN-001 -- Chọn năm

-   Cung cấp dropdown chọn năm.
-   Format dự kiến: `令和yy年度（yyyy/4 ～ yyyy/3）`.
-   Cách hiển thị năm cần thống nhất với màn hình Admin.

**Source: No.1**

------------------------------------------------------------------------

## REQ-TRN-002 -- Xuất tiến độ đào tạo bắt buộc

-   Cung cấp button xuất tiến độ đào tạo bắt buộc.
-   Dữ liệu được xuất dưới dạng CSV.
-   Nội dung CSV dựa trên bảng tiến độ đang hiển thị.

**Source: No.2**

------------------------------------------------------------------------

## REQ-TRN-003 -- Tab tiến độ

-   Cung cấp tab `Tiến độ xác nhận`.
-   Tab hiển thị bảng tiến độ đào tạo.

**Source: No.3**

------------------------------------------------------------------------

## REQ-TRN-004 -- Giải thích ký hiệu

-   Hiển thị phần giải thích các ký hiệu cố định được sử dụng trong
    bảng.

**Source: No.5**

------------------------------------------------------------------------

## REQ-TRN-005 -- Lọc theo cơ sở

-   Cung cấp dropdown `Cơ sở`.
-   Cho phép lọc danh sách tiến độ theo cơ sở.

**Source: No.7**

------------------------------------------------------------------------

## REQ-TRN-006 🔶 CHANGED -- Danh sách tiến độ

Bảng phải hiển thị:

-   Cơ sở
-   Tên nhân viên
-   Dữ liệu từ tháng 4 đến tháng 3
-   Tỷ lệ xem

### Tỷ lệ xem

-   Chỉ tính các nội dung đã hoàn thành toàn bộ.

### 🔶 Business rule thay đổi -- Khi nhân viên chuyển cơ sở

1.  Lịch sử đào tạo của nhân viên phải được giữ nguyên, không bị mất.
2.  Nếu nhân viên đã gửi báo cáo nhưng báo cáo vẫn ở trạng thái chưa xác
    nhận và sau đó chuyển sang cơ sở khác:
    -   Báo cáo cũ vẫn được phép xem.
    -   Không được phép thao tác trên báo cáo cũ.
3.  Khi chọn `Tất cả cơ sở`:
    -   Nhân viên đã chuyển cơ sở được hiển thị thành 2 record:
        -   Record tại cơ sở trước khi chuyển.
        -   Record tại cơ sở sau khi chuyển.
4.  Nếu chuyển từ cơ sở A sang B vào tháng 10:
    -   Cơ sở A: hiển thị dữ liệu đến tháng 9; từ tháng 10 trở đi để
        trống.
    -   Cơ sở B: hiển thị dữ liệu từ tháng 10 trở đi; trước tháng 10 để
        trống.
5.  Nếu chuyển cơ sở trong cùng một tháng:
    -   Dữ liệu của tháng đó được hiển thị ở cả hai cơ sở.
6.  Nếu khóa học bắt buộc của cơ sở mới giống với khóa học bắt buộc của
    cơ sở cũ:
    -   Nhân viên phải học lại video đó.

**Source: No.8 -- phần business rule màu vàng là nội dung thay đổi và
phải triển khai.**

------------------------------------------------------------------------

# 2. Xác nhận báo cáo đào tạo

## REQ-TRN-007 -- Chọn khoảng thời gian

-   Cho phép chọn khoảng thời gian.
-   Chỉ hiển thị báo cáo thuộc khoảng thời gian đã chọn.

**Source: No.9**

------------------------------------------------------------------------

## REQ-TRN-008 -- Thống kê chất lượng báo cáo

Hiển thị số lượng báo cáo theo:

-   Tốt
-   Cần xác nhận
-   Cần cải thiện

**Source: No.10--12**

------------------------------------------------------------------------

## REQ-TRN-009 -- Tìm kiếm theo tên nhân viên

-   Cung cấp field nhập tên nhân viên.
-   Cho phép tìm kiếm.
-   Hỗ trợ tìm kiếm gần đúng.

**Source: No.13**

------------------------------------------------------------------------

## REQ-TRN-010 -- Lọc theo cơ sở

-   Áp dụng khi người dùng thuộc trụ sở chính.
-   Giá trị:
    -   Trụ sở chính
    -   Các cơ sở thuộc trụ sở chính

**Source: No.14**

------------------------------------------------------------------------

## REQ-TRN-011 -- Lọc theo chất lượng

Dropdown gồm:

-   Tất cả
-   Tốt
-   Cần xác nhận
-   Cần cải thiện

**UI:** text phải được hiển thị lớn hơn theo thiết kế.

**Source: No.15**

------------------------------------------------------------------------

## REQ-TRN-012 -- Lọc theo trạng thái xác nhận

Dropdown gồm:

-   Tất cả
-   Chưa xác nhận
-   Đã xác nhận

Giá trị mặc định:

-   `Tất cả`

**Source: No.16**

------------------------------------------------------------------------

## REQ-TRN-013 -- Clear filter

-   Cung cấp button `Clear`.
-   Khi nhấn `Clear`, đưa toàn bộ filter về giá trị mặc định.
-   Sau khi clear, hiển thị toàn bộ báo cáo.

**Source: No.17**

------------------------------------------------------------------------

# 3. Chi tiết và xử lý báo cáo

## REQ-TRN-014 -- Hiển thị thông tin báo cáo

Hiển thị:

-   Tên nhân viên
-   Pháp nhân/công ty
-   Ngày gửi báo cáo
-   Nội dung báo cáo

**Source: No.18**

------------------------------------------------------------------------

## REQ-TRN-015 -- Xác nhận báo cáo

-   Cho phép người quản trị nhấn `Đã xác nhận`.
-   Báo cáo chuyển sang trạng thái `Đã xác nhận`.

**Source: No.19**

------------------------------------------------------------------------

## REQ-TRN-016 -- Yêu cầu viết lại báo cáo

-   Cho phép yêu cầu nhân viên viết lại báo cáo.
-   Khi gửi yêu cầu:
    -   Cập nhật màn hình `Report Feedback` trên My Page của nhân viên.
    -   Hiển thị banner tương ứng trên Homepage.

**Source: No.20**

------------------------------------------------------------------------

## REQ-TRN-017 -- Gửi feedback

-   Cho phép nhập nội dung feedback.
-   Feedback được gửi đến nhân viên tương ứng.
-   Sau khi gửi:
    -   Cập nhật `Report Feedback` trên My Page.
    -   Hiển thị banner trên Homepage.
-   Nếu gửi feedback trước khi nhấn `Đã xác nhận`:
    -   Chuyển báo cáo sang trạng thái `Đã xác nhận`.

**Source: No.21**

------------------------------------------------------------------------

## REQ-TRN-018 -- Phân loại chất lượng báo cáo

Có 3 mức:

-   Tốt
-   Cần xác nhận
-   Cần cải thiện

Mặc định:

-   `Cần xác nhận`

**Source: No.22**

------------------------------------------------------------------------

# 4. Download kết quả đào tạo

## REQ-TRN-019 -- Chọn cơ sở

-   Cung cấp dropdown `Cơ sở`.
-   Chỉ áp dụng lựa chọn cơ sở cho người dùng thuộc trụ sở chính.

**Source: No.27**

------------------------------------------------------------------------

## REQ-TRN-020 -- Số nhân viên thuộc đối tượng

-   Hiển thị tổng số nhân viên của pháp nhân.
-   Nếu là trụ sở chính:
    -   Bao gồm nhân viên của trụ sở chính và các cơ sở trực thuộc.

**Source: No.28**

------------------------------------------------------------------------

## REQ-TRN-021 -- Số lượt đào tạo

-   Tính theo tổng số khóa đào tạo bắt buộc đã được thiết lập.
-   Nếu là trụ sở chính:
    -   Bao gồm trụ sở chính và các cơ sở trực thuộc.

**Source: No.29**

------------------------------------------------------------------------

## REQ-TRN-022 -- Download kết quả đào tạo bản in

-   Cung cấp button `Download`.
-   Download dữ liệu tương ứng với `研修実績（印刷用）`.
-   Output: CSV.

> Phần `Preview` của No.30 không triển khai vì field `Preview` được tô
> xám. Button `Download` vẫn triển khai.

**Source: No.30--31**

------------------------------------------------------------------------

## REQ-TRN-023 -- Download kết quả đào tạo chi tiết

-   Cung cấp button `Download`.
-   Download dữ liệu tương ứng với `研修実績（詳細）`.
-   Output: CSV.

> Phần `Preview` của No.32 không triển khai vì field `Preview` được tô
> xám. Button `Download` vẫn triển khai.

**Source: No.32--33**

------------------------------------------------------------------------

## REQ-TRN-024 -- Download kết quả đào tạo dùng cho hướng dẫn vận hành

-   Cung cấp button `Download`.
-   Download dữ liệu tương ứng với `研修実績（運営指導用）`.
-   Output: CSV.
-   CSV template cần được xác định.

> Phần `Preview` của No.34 không triển khai vì field `Preview` được tô
> xám. Button `Download` vẫn triển khai.

**Source: No.34--35**

------------------------------------------------------------------------

# 5. Thiết lập kế hoạch đào tạo bắt buộc

## REQ-TRN-025 -- Xem kế hoạch năm

-   Cung cấp button `Xem kế hoạch năm`.
-   Khi nhấn button, mở modal.
-   Modal hiển thị kế hoạch đào tạo đã thiết lập.

**Source: No.36**

------------------------------------------------------------------------

## REQ-TRN-026 -- Xuất kế hoạch năm

-   Cung cấp chức năng xuất kế hoạch đã thiết lập.
-   Dữ liệu được xuất theo dạng CSV.
-   Sử dụng CSV template hiện tại.

**Source: No.37**

> Tên button trong thiết kế có chữ `Excel`, nhưng nội dung requirement
> ghi CSV. Cần xác nhận tên button/format cuối cùng, nhưng không tự đổi
> CSV thành Excel.

------------------------------------------------------------------------

# 6. Thiết lập kế hoạch theo năm và dịch vụ

## REQ-TRN-027 -- Chọn năm

-   Cung cấp dropdown chọn năm.
-   Cho phép chọn năm trong tương lai.
-   Giới hạn năm tối đa cần được xác nhận.

**Source: No.44**

------------------------------------------------------------------------

## REQ-TRN-028 -- Chọn loại dịch vụ

-   Hiển thị các loại dịch vụ mà pháp nhân đang sử dụng dưới dạng tab.
-   Mỗi loại dịch vụ có một tab riêng.
-   Người dùng có thể chuyển đổi giữa các tab.

**UI change:** - Tăng kích thước text của tab. - Thay đổi màu tab để dễ
phân biệt.

**Source: No.45**

------------------------------------------------------------------------

## REQ-TRN-029 -- Thiết lập đào tạo bắt buộc theo tháng

Bảng gồm:

### Cột Category / Video name

-   Hiển thị danh mục / tên video.
-   Đối với đào tạo pháp định, hiển thị tag `Pháp định`.

### Cột tháng

-   Hiển thị các tháng.
-   Mỗi tháng có checkbox.
-   Cho phép chọn checkbox để thiết lập video đào tạo bắt buộc cho tháng
    tương ứng.

### Quy tắc hiển thị video

-   Khi chọn category, hiển thị các video thuộc category đó.
-   Category có tag `階層限定・必須研修対象外` được áp dụng cho đối
    tượng `Tất cả`.

**Source: No.46**

------------------------------------------------------------------------

# 7. Out of Scope -- Không triển khai

Các field/cụm chức năng được tô **xám** trong Basic Design không thuộc
phạm vi implementation:

    No. Nội dung
  ----- -----------------------------------------------------
      4 Cảnh báo chưa tham gia đào tạo pháp định
      6 Filter theo trạng thái ở danh sách tiến độ
     23 Chứng nhận hoàn thành
     24 In kết quả đào tạo
     25 Chi tiết kết quả đào tạo
     26 Kết quả đào tạo dùng cho hướng dẫn vận hành
     30 Preview kết quả đào tạo bản in
     32 Preview kết quả đào tạo chi tiết
     34 Preview kết quả đào tạo dùng cho hướng dẫn vận hành
     38 Số video đã thiết lập
     39 Số video trung bình phát hành mỗi tháng
     40 Tỷ lệ bao phủ đào tạo pháp định theo topic
     41 Số lượng thiết lập theo tháng
     42 Xem danh sách đã thiết lập
     43 Chi tiết đào tạo pháp định chưa thiết lập
     47 Số lượng nhân viên mới
     48 Quá hạn
     49 Còn trong 30 ngày
     50 Hoàn thành
     51 Danh sách nhân viên thuộc đối tượng
     52 Nhắc nhở

### Lưu ý về No.30 / 32 / 34

Chỉ **Preview** bị loại khỏi scope vì ô Preview được tô xám.

Các button **Download No.31 / 33 / 35 vẫn triển khai**.

------------------------------------------------------------------------

# 8. Nội dung thay đổi cần ưu tiên khi implementation

Các thay đổi được đánh dấu màu vàng trong sheet:

## CHANGE-01 -- Business rule khi chuyển cơ sở

Áp dụng cho `REQ-TRN-006`.

-   Giữ nguyên lịch sử học.
-   Giữ dữ liệu report cũ ở trạng thái chỉ xem khi nhân viên chuyển cơ
    sở.
-   Khi xem tất cả cơ sở, hiển thị 2 record trước/sau khi chuyển.
-   Phân chia dữ liệu theo tháng trước/sau thời điểm chuyển.
-   Nếu khóa học bắt buộc trùng nhau giữa cơ sở cũ và mới, nhân viên
    phải học lại.

> Đây là **thay đổi cần triển khai**, không phải TBD.

------------------------------------------------------------------------

# 9. Nội dung cần xác nhận

  -----------------------------------------------------------------------
  ID                                  Nội dung
  ----------------------------------- -----------------------------------
  TBD-01                              Format hiển thị năm ở màn hình
                                      Company có giống Admin hay không

  TBD-02                              Giới hạn năm tối đa có thể chọn ở
                                      chức năng thiết lập kế hoạch

  TBD-03                              Format cuối cùng của chức năng
                                      `年間計画Excelで出力`: Excel hay
                                      CSV

  TBD-04                              CSV template của
                                      `研修実績（運営指導用）`

  TBD-05                              Màu hiển thị của video không phải
                                      video dùng chung trong No.41 nếu
                                      chức năng này được mở lại
  -----------------------------------------------------------------------

> **No.41 hiện đang tô xám nên không triển khai.** Ghi chú `一旦保留`
> màu vàng trong sheet được hiểu là **tạm giữ/hold**, không chuyển thành
> requirement implementation.

------------------------------------------------------------------------

# 10. Nguyên tắc sử dụng Requirement

-   Requirement phải phản ánh **Basic Design hiện tại + các thay đổi
    được đánh dấu vàng**.
-   Không tạo requirement cho nội dung bị tô xám.
-   Không tự suy diễn các nội dung `要確認` thành behavior đã chốt.
-   Các business rule màu vàng phải được dùng làm input khi xây dựng
    test case full-flow.
