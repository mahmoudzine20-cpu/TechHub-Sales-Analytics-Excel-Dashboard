📊 TechHub Sales Analytics – Excel Dashboard
> End-to-end Business Intelligence project built **entirely in Excel**: messy CSV files → **Power Query** cleaning → **Power Pivot** star schema → **Pivot Tables** → an interactive **Dashboard** with slicers and a timeline.
<img width="558" height="258" alt="image" src="https://github.com/user-attachments/assets/c65cf937-a6ec-40c2-b93d-4d00b5b91bf1" />

)
---
📌 Table of Contents
Project Overview
Tools & Skills
Dataset
Stage 1 – Data Cleaning (Power Query)
Stage 2 – Data Modeling (Power Pivot Star Schema)
Stage 3 – Analysis with Pivot Tables
Stage 4 – Dashboard
Key Insights
Repository Structure
How to Use
---
🎯 Project Overview
TechHub Company stores its sales data in separate files: a customers list, a products list, and a folder of monthly order files. The raw data was inconsistent (extra spaces, wrong casing, text in numeric fields, missing values, and mixed date formats), so it could not be analyzed directly.
This project covers the full analytics workflow:
Stage	What was done	Tool
1	Import, clean and standardize the raw data	Power Query
2	Build a star schema and relationships	Power Pivot (Data Model)
3	Summarize the data into KPIs and breakdowns	Pivot Tables
4	Design an interactive dashboard	Pivot Charts, Slicers, Timeline
Business questions answered
How much revenue did the company make, and from how many orders and customers?
Which product categories and products drive sales?
Which governorates (regions) perform best?
How do orders trend month by month during 2024?
What is the relationship between sales value and quantity sold per product?
---
🛠 Tools & Skills
Microsoft Excel (Power Query, Power Pivot, Pivot Tables, Pivot Charts, Slicers, Timeline)
Power Query M language: text cleaning, conditional columns, folder-based file combining, date reconstruction
Data Modeling: star schema, one-to-many relationships, calendar (date) table
Data Visualization & Dashboard Design
---
📂 Dataset
The data is split into three sources:
Source	Type	Columns	Description
`Customers.csv`	Single file	`CustomerID`, `CustomerName`, `Region`	Customer master data
`Products.csv`	Single file	`ProductID`, `ProductName`, `Category`, `Price`	Product catalog
`Orders/`	Folder of monthly CSV files	`OrderID`, `CustomerID`, `ProductID`, `OrderDate`, `Quantity`	Transaction data (one file per month, 2024)
Scale of the final data: 300 customers · 19,594 orders · 108,120 units sold · 5 categories · 25 products · 5 governorates.
---
🧹 Stage 1 – Data Cleaning (Power Query)
All cleaning was done in Power Query, so every step is recorded under Applied Steps and refreshes automatically when new data arrives. The full M code for each query is in the `power-query/` folder.
1️⃣ Customers query
Step	Purpose
Promote Headers	Use the first row as column names
Change Type	`CustomerID` → Whole Number, `CustomerName` / `Region` → Text
Trim	Remove leading/trailing spaces from `CustomerName`
Capitalize Each Word (`Text.Proper`)	Standardize customer names
Replace Value (`" "` → `""`)	Remove stray spaces inside `Region` values
Capitalize Each Word	Standardize region names (e.g. `Cairo`, `Giza`)
2️⃣ Products query
Step	Purpose
Promote Headers & Change Type	Set correct data types
Replace Value (`"Two Hundred"` → `"200"`)	Fix a price entered as text so it can be converted to a number
Change Type → Currency	Convert `Price` to a numeric currency type
Trim + Capitalize Each Word	Clean `Category` and `ProductName` (removes duplicates caused by spacing/casing)
3️⃣ Orders query (the most complex one)
Orders arrive as many CSV files in one folder, so the query combines them and then repairs the dates.
#	Step	Purpose
1	`Folder.Files` + filter hidden files	Load every order file in the folder
2	Invoke Custom Function (Transform File)	Apply the same transformation to each file
3	Expand table column	Combine all monthly files into one table
4	Change Type	Set types for IDs, quantity and date
5	Filter nulls/blanks in `ProductID` and `Quantity`	Remove incomplete rows
6	Replace `/` with `-` in `OrderDate`	Unify the date separator
7	Extract text between delimiters from `Source.Name`	Get the month from the file name
8	Split `OrderDate` by `-` into 3 parts	Date parts arrived in inconsistent order
9	Conditional column `year`	The part greater than 31 must be the year
10	Custom column `day`	The part ≤ 31 that is not the month is the day
11	Merge `Month` + `day` + `year`	Rebuild a clean date
12	Change Type → Date	Final `date` column
13	Add Index column → rename to `order ID`	Create a reliable unique key
14	Remove old `OrderID`, reorder columns	Final tidy table
Result: a clean, consistent `Orders` fact table with a proper `date` column, ready to load into the Data Model.
---
🧱 Stage 2 – Data Modeling (Power Pivot Star Schema)
The cleaned tables were loaded to the Data Model and organized as a star schema:
(<img width="726" height="371" alt="Screenshot 2026-11-15 153805" src="https://github.com/user-attachments/assets/9e3580e0-299a-46dc-99b5-1ee2411624ad" />
)
```
 Customers (1) ──────► (*) Orders (*) ◄────── (1) Products
                                │
                               (*)
                                ▲
                               (1)
                            Calendar
```
Table	Type	Key
Orders	Fact	`CustomerID`, `ProductID`, `date`
Customers	Dimension	`CustomerID` (one-to-many → Orders)
Products	Dimension	`ProductID` (one-to-many → Orders)
Calendar	Dimension (date table)	`Date` (one-to-many → Orders) – columns: `Year`, `Month Number`, `Month`, `MMM-YYYY`
Why a star schema? Dimensions filter the fact table in one direction, which gives faster calculations, cleaner relationships, and makes slicers work consistently across every pivot table and chart.
Measures created in Power Pivot:
Total Sales = Quantity × Product Price, summed across orders
Total Quantity = sum of `Quantity`
Total Orders = count of orders
Total Customers = distinct count of `CustomerID`
---
📑 Stage 3 – Analysis with Pivot Tables
Pivot tables were built on the Data Model to feed the dashboard KPIs and charts:
<img width="465" height="265" alt="Screenshot 2026-11-15 153912" src="https://github.com/user-attachments/assets/662f7bae-fc6f-4b0d-9044-04f40288bf4c" />

Pivot Table	Fields
KPI cards	Total Customers, Total Sales, Total Orders, Total Quantity
Sales by Category	Category → Total Sales
Quantity by Category	Category → Total Quantity
Orders by Category	Category → Total Orders
Sales by Region	Region → Total Sales
Monthly Orders	Calendar Year → Month → Total Orders
Product analysis	ProductName → Total Sales & Total Quantity
Top 5 Products	ProductName with a Top 5 value filter
---
📈 Stage 4 – Dashboard
The final dashboard ("Dashboard TechHub Company") combines everything on one screen:
<img width="558" height="258" alt="image" src="https://github.com/user-attachments/assets/06237040-9b44-4c60-a71e-c18eda010cef" />


KPI cards: Total Sales · Total Quantity · Total Orders · Total Customers
Visuals
Visual	Chart type
Total Sales by Category	Donut chart
Monthly Orders Trend (2024)	Line chart with markers
Top 5 Products	Bar chart
Total Orders by Category	Pie chart
Sales Value vs. Quantity Sold	Combo chart (columns + line)
Total Sales by Governorate	3D pie chart
Interactivity: slicers for Category and Region and a Date timeline (by months), all connected to every pivot table, so the whole dashboard updates together.
---
💡 Key Insights
Metric	Value
💰 Total Sales	$101,146,137
📦 Total Quantity	108,120 units
🧾 Total Orders	19,594
👥 Total Customers	300
Clothing is the top category by revenue ($29.1M, 29%), followed by Toys ($25.4M, 25%). Furniture is the lowest ($10.4M, 10%).
Orders and quantities are spread evenly across categories (3,800–4,000 orders each), so the revenue gap comes from price differences, not demand.
Regional sales are very balanced: Mansoura ($21.0M) leads, with Aswan, Cairo and Giza close behind; Alexandria is lowest ($18.7M).
Monthly order volume is stable throughout 2024 (1,623–1,644 orders per month), peaking in December (1,644).
Some products sell high volume but little revenue (e.g. Sofa: ~$0.26M, Headphones: $0.89M), which is a pricing/margin opportunity.
---
🗂 Repository Structure
```
TechHub-Sales-Analytics-Excel-Dashboard/
│
├── data/
│   ├── Customers.csv
│   ├── Products.csv
│   └── Orders/                      # monthly order files
│
├── power-query/
│   ├── Customers_query.m
│   ├── Products_query.m
│   └── Orders_query.m
│
├── TechHub_Dashboard.xlsx           # final workbook (model + pivots + dashboard)
│
├── images/
│   ├── data_model.png
│   ├── pivot_tables.png
│   └── dashboard.png
│
└── README.md
```
---
🚀 How to Use
Download or clone the repository.
Open `TechHub_Dashboard.xlsx` in Excel 2016 or later (Power Query and Power Pivot required; Excel for Windows recommended).
If you move the data folder, update the file paths: Data → Queries & Connections → Edit Source.
Click Data → Refresh All to reload the data.
Use the slicers and timeline on the dashboard to explore the data.
---
👤 Author
Mahmoud Zain
Data Analysis Project – Excel (Power Query · Power Pivot · Dashboard)
⭐ If you found this project helpful, give it a star!
