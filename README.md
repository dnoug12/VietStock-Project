# 📊 Hệ Thống Phân Tích và Trực Quan Hóa Dữ Liệu

## 🚀 Giới Thiệu

Dự án này nhằm xây dựng một hệ thống phân tích và trực quan hóa dữ liệu hoàn chỉnh, từ việc chuẩn bị môi trường, làm sạch dữ liệu, mô hình hóa đến trực quan hóa và xuất bản báo cáo.

## 🛠️ Cài Đặt Môi Trường

Cần cài đặt các phần mềm sau:
- **SQL Server Express Edition** – Cơ sở dữ liệu để lưu trữ và truy vấn dữ liệu.
- **SQL Server Management Studio (SSMS)** – Công cụ quản lý SQL Server.
- **Power BI Desktop** – Công cụ trực quan hóa dữ liệu.

**Khôi phục dữ liệu mẫu:**
- Sử dụng file backup (`SAMPLE.bak`) để phục hồi dữ liệu trong SSMS.
- Thực hiện restore bằng **Wizard** (Khuyến nghị) hoặc **Script**.

## 🧹 Chuẩn Bị và Làm Sạch Dữ Liệu

- **Nhân bản cơ sở dữ liệu mẫu** để tạo **Staging Database**, tránh ảnh hưởng dữ liệu gốc.
- **Xóa dữ liệu trùng lặp**, kiểm tra tính hợp lý của dữ liệu.
- **Lưu trữ dữ liệu sạch** vào các bảng như `GIAODICH_CLEAN_1`, `GIAODICH_CLEAN_2`.
```
	USE SAMPLE
	
	CREATE TABLE GIAODICH_CLEAN (
		MaCK nvarchar(255),
		NGAYGIAODICH varchar(8),
		GIAMOCUA float,
		GIACAONHAT float,
		GIATHAPNHAT float,
		GIADONGCUA float,
		KHOILUONGGIAODICH float,
		TENNHOMNGANH nvarchar(255),
		MANHOMNGANH nvarchar(255),
		THONGTINCONGTY nvarchar(255),
		SAN nvarchar(255),
		CONGTY nvarchar(255),
		TENSAN_VIET nvarchar(255),
		TENSAN_ANH nvarchar(255),
		BIENDODAODONG float
	)
	
	SELECT *
	FROM GIAODICH_CLEAN
	
	INSERT INTO GIAODICH_CLEAN
	SELECT * FROM GIAODICH
```
> Xem thêm ở file 'Process.sql'

## 🔄 Chuyển Đổi Dữ Liệu

- **Chuẩn hóa định dạng dữ liệu**, sửa lỗi, chuyển đổi kiểu dữ liệu.
- **Tính toán các chỉ số bổ sung** như giá trần, giá sàn, tỷ lệ tăng giảm.
- **Tạo bộ dữ liệu chuẩn** sẵn sàng cho phân tích và trực quan hóa.
- **Ví dụ**
```
SELECT	MaCK,
	FORMAT(try_CONVERT(datetime,NGAYGIAODICH), 'd','us') as NGAYGIAODICH,
	GIAMOCUA,
	GIACAONHAT,
	GIATHAPNHAT,
	GIADONGCUA,
	KHOILUONGGIAODICH,
--2.Thêm Giá trần = Giá tham chiếu (mở cửa) x (100% + Biên độ dao động)
	FORMAT( GIAMOCUA * (1 + BIENDODAODONG), 'F', 'en-us') AS GIATRAN,
--3.Thêm Giá sàn = Giá tham chiếu (mở cửa) x (100% – Biên độ dao động)
	FORMAT(GIAMOCUA * (1-BIENDODAODONG), 'F', 'en-us') AS GIASAN,
--4.Tỷ lệ tăng giảm trong ngày: TILETRONGNGAY = ([GIADONGCUA]-[GIAMOCUA])*100/[GIAMOCUA]
	FORMAT(GIADONGCUA - GIAMOCUA * 100/GIAMOCUA, 'F', 'en-us') AS TILETRONGNGAY,
	TENNHOMNGANH,
	MANHOMNGANH,
	THONGTINCONGTY,
	SAN,
	CONGTY,
	TENSAN_ANH,
	TENSAN_VIET,
	BIENDODAODONG,
	DUPLICATEROW
--5.Insert tất cả các dòng vào table GIAODICH_FINAL
INTO GIAODICH_FINAL
FROM GIAODICH_CLEAN_2
```


## 📊 Mô Hình Hóa Dữ Liệu

- **Tạo database mới "Spend"** từ Staging database.
- **Kết nối Power BI với SQL Server** để tải dữ liệu.
- **Chuyển đổi dữ liệu bằng Power Query**, như đổi kiểu dữ liệu, tạo bảng tham chiếu.

![Image](https://github.com/user-attachments/assets/1df4ffe5-0289-4d5e-af0c-474faf9fea46)

## 📈 Phân Tích Dữ Liệu với DAX

- **Tạo Measures & Calculated Columns** để phân tích dữ liệu.
- **Ví dụ***
- Đếm số lượng mã giảm bằng DAX
```
Số lượng mã Giảm = COUNTROWS(FILTER(TB_GIAODICH, TB_GIAODICH[TRANGTHAI] = "GIẢM"))
```
-  Hoặc đặt màu cho trạng thái mã cổ phiếu
```
Màu trạng thái trần = SWITCH(
    TRUE(),
    [Tổng giá cao nhất] < [Tổng giá mở cửa],
    "#ff3737",
    [Tổng giá cao nhất] > [Tổng giá mở cửa],
    "#0f0",
    [Tổng giá cao nhất] = [Tổng giá mở cửa],
    "#FFA500"
)
```
- **Xây dựng bảng hỗ trợ** như `Calendar`, `Khối Lượng Giao Dịch Table`, `Color Table`.
```
Calendar = ADDCOLUMNS(
    CALENDAR(MIN(TB_GIAODICH[NGAYGIAODICH]),MAX(TB_GIAODICH[NGAYGIAODICH])),
    "NAM", YEAR([Date]),
    "THANG", MONTH([Date]),
    "MonthName", FORMAT(MONTH([Date]),"MMMM"),
    "NGAYTRONGTUAN", FORMAT(DAY([Date]),"DDDD"),
    "QUY", FORMAT(QUARTER([Date]),"Q"),
    "NAMQUY",FORMAT([Date],"YYYY") & "/Q" & FORMAT(QUARTER([Date]),"Q")
)
```
- **Tính toán các chỉ số quan trọng**, phân loại trạng thái giao dịch.
```
TRANGTHAI = IF(TB_GIAODICH[GIAMOCUA] > TB_GIAODICH[GIADONGCUA] , "GIẢM",
            IF(TB_GIAODICH[GIAMOCUA] = TB_GIAODICH[GIADONGCUA], "THAM CHIẾU", "TĂNG"))
```


## 🎨 Trực Quan Hóa Dữ Liệu

- **Thiết kế biểu đồ và dashboard** với Power BI.
- **Tạo báo cáo động**, sử dụng slicer, filter để dễ dàng phân tích.
- **Sử dụng các loại biểu đồ phù hợp**, như KPI, TreeMap, Line chart cho các mã cổ phiếu chứng khoán.

![Image](https://github.com/user-attachments/assets/dbbd12da-8ab6-4cde-b251-ff5ca7a58645)

## 📌 Kết Luận

Dự án giúp nâng cao kỹ năng xử lý dữ liệu, từ SQL đến Power BI, và tạo ra một hệ thống báo cáo trực quan, hỗ trợ ra quyết định kinh doanh hiệu quả.
Mục tiêu của dự án hướng tới là tạo dashboard động cũng như tính toán các chỉ số cơ bản trong chứng khoán, nhằm thực hành các kỹ năng đã học.
Để biết thêm về mục tiêu của từng phần, vui lòng đọc file 'report.pdf'

