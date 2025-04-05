# 📊 Data Analysis and Visualization System

## 🚀 Introduction

This project aims to build a complete data analysis and visualization system—from environment setup, data cleaning, and modeling to visualization and report publishing.

## 🛠️ Environment Setup

Please install the following software:
- **SQL Server Express Edition** – Used to store and query data.
- **SQL Server Management Studio (SSMS)** – Tool to manage SQL Server.
- **Power BI Desktop** – Data visualization tool.

**Restore sample data:**
- Use the `SAMPLE.bak` file in the `resource` folder to restore the database in SSMS.
- Perform the restore using the **Wizard** (recommended) or **Script**.

## 🧹 Data Preparation and Cleaning

- **Clone the sample database** to create a **Staging Database**, to prevent changes to the original data.
- **Remove duplicates** and check data consistency.
- **Save clean data** into tables like `GIAODICH_CLEAN_1`, `GIAODICH_CLEAN_2`.

```sql
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

SELECT * FROM GIAODICH_CLEAN

INSERT INTO GIAODICH_CLEAN
SELECT * FROM GIAODICH
```

> See more in `Process.sql` file.

## 🔄 Data Transformation

- **Standardize data format**, correct errors, convert data types.
- **Calculate additional indicators** such as ceiling/floor prices, percentage changes.
- **Create final datasets** ready for analysis and visualization.

**Example:**
```sql
SELECT	
	MaCK,
	FORMAT(TRY_CONVERT(datetime, NGAYGIAODICH), 'd', 'us') AS NGAYGIAODICH,
	GIAMOCUA,
	GIACAONHAT,
	GIATHAPNHAT,
	GIADONGCUA,
	KHOILUONGGIAODICH,
-- 2. Ceiling Price = Open Price × (100% + Volatility)
	FORMAT(GIAMOCUA * (1 + BIENDODAODONG), 'F', 'en-us') AS GIATRAN,
-- 3. Floor Price = Open Price × (100% – Volatility)
	FORMAT(GIAMOCUA * (1 - BIENDODAODONG), 'F', 'en-us') AS GIASAN,
-- 4. Daily % Change = (Close - Open) * 100 / Open
	FORMAT((GIADONGCUA - GIAMOCUA) * 100 / GIAMOCUA, 'F', 'en-us') AS TILETRONGNGAY,
	TENNHOMNGANH,
	MANHOMNGANH,
	THONGTINCONGTY,
	SAN,
	CONGTY,
	TENSAN_ANH,
	TENSAN_VIET,
	BIENDODAODONG,
	DUPLICATEROW
-- 5. Insert all into final table
INTO GIAODICH_FINAL
FROM GIAODICH_CLEAN_2
```

## 📊 Data Modeling

- **Create a new database "Spend"** from the staging database.
- **Connect Power BI to SQL Server** to load data.
- **Transform data in Power Query**, e.g., change data types, create lookup tables.
- You can choose to model in **SQL Server** or use **Auto Modeling** in Power BI.

## 📈 Data Analysis with DAX

- **Create Measures & Calculated Columns** for analysis.
- **Examples**:
Count the number of decreasing stocks:
```DAX
Decreasing Stocks = COUNTROWS(FILTER(TB_GIAODICH, TB_GIAODICH[TRANGTHAI] = "DECREASE"))
```
Set color based on price status:
```DAX
Status Color = SWITCH(
    TRUE(),
    [Highest Price] < [Open Price], "#ff3737",
    [Highest Price] > [Open Price], "#0f0",
    [Highest Price] = [Open Price], "#FFA500"
)
```
- **Build supporting tables** such as `Calendar`, `Volume Table`, `Color Table`:
```DAX
Calendar = ADDCOLUMNS(
    CALENDAR(MIN(TB_GIAODICH[NGAYGIAODICH]), MAX(TB_GIAODICH[NGAYGIAODICH])),
    "YEAR", YEAR([Date]),
    "MONTH", MONTH([Date]),
    "MonthName", FORMAT(MONTH([Date]), "MMMM"),
    "WEEKDAY", FORMAT(DAY([Date]), "DDDD"),
    "QUARTER", FORMAT(QUARTER([Date]), "Q"),
    "YEARQUARTER", FORMAT([Date], "YYYY") & "/Q" & FORMAT(QUARTER([Date]), "Q")
)
```

- **Calculate key indicators**, classify trading status:
```DAX
TRANGTHAI = IF(
	TB_GIAODICH[GIAMOCUA] > TB_GIAODICH[GIADONGCUA], "DECREASE",
	IF(TB_GIAODICH[GIAMOCUA] = TB_GIAODICH[GIADONGCUA], "UNCHANGED", "INCREASE")
)
```

## 🎨 Data Visualization

- **Design charts and dashboards** using Power BI.
- **Create dynamic reports**, with slicers and filters for easier analysis.
- **Use appropriate visual types**, like KPIs, TreeMap, Line Charts for stock data.

![Image](https://github.com/user-attachments/assets/ab801c09-5c4c-41dc-aec8-acbfe8899bce)

## 📌 Conclusion

- This project enhances your data processing skills, from SQL to Power BI, and helps build interactive dashboards that support business decision-making.
- The goal is to **practice data analysis and reporting skills** by building stock-related dashboards and calculating key market indicators.
- For more details on the objective of each section, refer to **report.pdf**.

## 📂 Folder Structure

```
📁 ProjectFolder/
️
📁 resource/              # Sample data like SAMPLE.bak
📁 SQL/                   # SQL scripts: Process.sql, Clean.sql, etc.
📁 PowerBI/               # Power BI report file (.pbix)
📁 images/                # Images used in README
📄 README.md              # Project documentation
📄 report.pdf             # Detailed explanation of the process and findings
📄 LICENSE                # (Optional) Open-source license
```

## 💡 Suggestions for Expansion

Here are some ideas to improve and expand the project:

- 🧠 **AI Integration**: Add machine learning models to forecast stock trends (Python or R).
- 📅 **ETL Automation**: Automate daily data updates using APIs (e.g., Vietstock, Fireant, SSI).
- 🌐 **Web Deployment**: Publish the report using Power BI Service or embed via iframe.
- 🔐 **Access Control**: Use Row-Level Security (RLS) to restrict data visibility by user.

## 🙋 Support & Contributions

If you encounter issues or want to contribute:
- Open an [issue](https://github.com/your-repo/issues) for bug reports or suggestions.
- Fork and submit a pull request to contribute code or improve the documentation.

## 📄 License

This project is licensed under the [MIT License](./LICENSE). Feel free to use, modify, and distribute it, provided that credit is given to the original author.

