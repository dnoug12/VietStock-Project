# Hệ Thống Phân Tích và Trực Quan Hóa Dữ Liệu 📊

## Giới Thiệu
Dự án này nhằm xây dựng một hệ thống phân tích và trực quan hóa dữ liệu chứng khoán từ bước chuẩn bị, làm sạch, chuyển đổi đến mô hình hóa và trình bày báo cáo. Hệ thống giúp doanh nghiệp khai thác dữ liệu hiệu quả hơn để đưa ra quyết định chính xác.

## Công Nghệ Sử Dụng
- **SQL Server Express Edition & SSMS**: Quản lý và xử lý dữ liệu.
- **Power BI Desktop**: Trực quan hóa và xây dựng báo cáo.
- **DAX (Data Analysis Expressions)**: Viết các công thức tính toán và phân tích dữ liệu.

## Quy Trình Triển Khai

### 1. Chuẩn Bị Dữ Liệu
![Chuẩn Bị Dữ Liệu](link_mau_hinh_anh_1)
- Cài đặt SQL Server Express và SSMS để quản lý cơ sở dữ liệu.
- Khôi phục dữ liệu từ file backup (.bak) vào SQL Server bằng Wizard hoặc Script.

### 2. Làm Sạch Dữ Liệu
![Làm Sạch Dữ Liệu](link_mau_hinh_anh_2)
- Tạo một bản sao của cơ sở dữ liệu mẫu thành Staging Database để xử lý mà không ảnh hưởng dữ liệu gốc.
- Xóa các giao dịch không hợp lý và loại bỏ dữ liệu trùng lặp.
- Lưu trữ dữ liệu sạch vào bảng `GIAODICH_CLEAN` và tiếp tục xử lý dữ liệu.

### 3. Chuyển Đổi Dữ Liệu
![Chuyển Đổi Dữ Liệu](link_mau_hinh_anh_3)
- Định dạng lại cột ngày giao dịch (`NGAYGIAODICH`).
- Thêm các chỉ số như giá trần, giá sàn và tỷ lệ tăng giảm trong ngày.
- Chuẩn hóa dữ liệu và đưa vào bảng `GIAODICH_FINAL` để phân tích.

### 4. Mô Hình Hóa Dữ Liệu
![Mô Hình Hóa Dữ Liệu](link_mau_hinh_anh_4)
- Nhân bản dữ liệu từ Staging Database sang database mới `Spend`.
- Kết nối Power BI với SQL Server để tải dữ liệu vào Power Query.
- Phân loại dữ liệu theo các thực thể như sàn giao dịch, nhóm ngành, chứng khoán.
- Tạo các bảng tham chiếu (`tbl_SAN`, `tbl_NGANH`, `tbl_CHUNGKHOAN`) để tối ưu hóa phân tích.

### 5. Xử Lý Dữ Liệu với DAX
![Xử Lý Dữ Liệu với DAX](link_mau_hinh_anh_5)
- Tạo bảng Calendar hỗ trợ phân tích theo thời gian.
- Viết các công thức tính toán như tổng khối lượng giao dịch, tổng giá trị giao dịch.
- Phân loại trạng thái giao dịch (tăng, giảm, tham chiếu) dựa trên dữ liệu.

### 6. Trực Quan Hóa Dữ Liệu với Power BI
![Trực Quan Hóa Dữ Liệu](link_mau_hinh_anh_6)
- Thiết kế biểu đồ như Candlestick Chart để thể hiện xu hướng giá.
- Xây dựng báo cáo động với bộ lọc theo ngày, ngành, mã chứng khoán.
- Tạo dashboard tổng quan hiển thị các số liệu quan trọng.

### 7. Xuất Bản Báo Cáo
![Xuất Bản Báo Cáo](link_mau_hinh_anh_7)
- Xuất báo cáo lên Power BI Service để chia sẻ và theo dõi trực tuyến.
- Hoàn thiện tài liệu mô tả chi tiết quy trình triển khai.
- Soạn slide trình bày kết quả và bài học rút ra từ dự án.

## Kết Luận
![Kết Luận](link_mau_hinh_anh_8)
Dự án cung cấp một quy trình chuẩn để xử lý, phân tích và trực quan hóa dữ liệu chứng khoán. Hệ thống giúp doanh nghiệp theo dõi biến động thị trường, đưa ra quyết định nhanh chóng và chính xác. Các phương pháp làm sạch dữ liệu, chuyển đổi và mô hình hóa giúp đảm bảo dữ liệu có chất lượng cao, từ đó nâng cao giá trị phân tích.

