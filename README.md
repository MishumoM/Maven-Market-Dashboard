![image](https://github.com/MishumoM/Maven-Market-Dashboard/assets/127921798/0ced319b-dd98-4fca-aea4-ed5819d6da5e)# Maven-Market-Dashboard
During my Power BI course by Maven Analytics , I created an interactive Power BI report for Maven Market. Connected and cleaned multiple CSV files, built data relationships, and developed DAX measures. The report includes KPIs, maps, and charts, highlighting sales, returns, and revenue trends.

Problem Statement for Power BI Project

The retail chain Maven Market faces challenges in managing and interpreting its extensive sales data, which spans across multiple years and includes various customer demographics, product lines, and regional performance metrics. The company needs a comprehensive and dynamic reporting solution to consolidate data from multiple sources, provide actionable insights, and support data-driven decision-making. The goal of this project is to develop an interactive Power BI report that integrates customer, product, store, region, calendar, returns, and transaction data to track key performance indicators, identify trends, and optimize business strategies.


**Instructions Followed:**

### PART 1: Connecting & Shaping the Data

#### 1. Update Power BI Options and Settings
- **Deselect** "Autodetect new relationships after data is loaded" in the Data Load tab.
- **Set Locale** for import to "English (United States)" in the Regional Settings tab.

#### 2. Connect to MavenMarket_Customers CSV
- **Table Name:** Customers
- **Headers:** Promoted
- **Data Types:** 
  - `customer_id` → Whole Number
  - `customer_acct_num` → Text
  - `customer_postal_code` → Text
- **New Columns:**
  - `full_name` → `[first_name] & " " & [last_name]`
  - `birth_year` → `Year([birthdate])`
  - `has_children` → Conditional Column: `if [total_children] = 0 then "N" else "Y"`

#### 3. Connect to MavenMarket_Products CSV
- **Table Name:** Products
- **Headers:** Promoted
- **Data Types:** 
  - `product_id` → Whole Number
  - `product_sku` → Text
  - `product_retail_price` & `product_cost` → Decimal Number
- **Distinct Counts:** 
  - Brands: 111
  - Product Names: 1,560
- **New Columns:**
  - `discount_price` → `[product_retail_price] * 0.90`
- **Formatting:** Fixed Decimal, rounded to 2 digits
- **Group By:**
  - `product_brand` → Avg `product_retail_price` as `Avg Retail Price`
- **Spot Check:**
  - Washington products: $2.18
  - Green Ribbon: $2.21
- **Replace Null Values:**
  - `recyclable` and `low-fat` → 0

#### 4. Connect to MavenMarket_Stores CSV
- **Table Name:** Stores
- **Headers:** Promoted
- **Data Types:** 
  - `store_id` and `region_id` → Whole Number
- **New Columns:**
  - `full_address` → `[store_city] & ", " & [store_state] & ", " & [store_country]`
  - `area_code` → `Text.BeforeDelimiter([store_phone], "-")`

#### 5. Connect to MavenMarket_Regions CSV
- **Table Name:** Regions
- **Headers:** Promoted
- **Data Types:** 
  - `region_id` → Whole Number

#### 6. Connect to MavenMarket_Calendar CSV
- **Table Name:** Calendar
- **Headers:** Promoted
- **New Columns:**
  - Start of Week → `StartOfWeek([date], Sunday)`
  - Name of Day → `Text.From([date], "dddd")`
  - Start of Month → `StartOfMonth([date])`
  - Name of Month → `Text.From([date], "MMMM")`
  - Quarter of Year → `Quarter([date])`
  - Year → `Year([date])`

#### 7. Connect to MavenMarket_Returns CSV
- **Table Name:** Return_Data
- **Headers:** Promoted
- **Data Types:** 
  - All ID columns and `quantity` → Whole Numbers

#### 8. Connect to MavenMarket_Transactions Folder
- **Folder Path:** MavenMarket Transactions (containing both 1997 and 1998 CSV files)
- **Combine Files:**
  - Remove `Source.Name` column
- **Table Name:** Transaction_Data
- **Headers:** Promoted
- **Data Types:** 
  - All ID columns and `quantity` → Whole Numbers
- **Spot Check:**
  - `transaction_date` data from 1/1/1997 to 12/30/1998

#### 9. Exclude Tables from Report Refresh
- **Exclude:** All tables except Transaction_Data and Return_Data
- **Close & Apply:** Confirm 7 tables are accessible in both RELATIONSHIPS and DATA view.

#### 10. Save File
- **File Name:** MavenMarket_Report.pbix

### PART 2: Creating the Data Model

#### 1. Arrange Tables
- **MODEL View:** Lookup tables above data tables.
- **Relationships:**
  - `Transaction_Data` to `Customers`, `Products`, `Stores`
  - `Transaction_Data` to `Calendar` (inactive `stock_date` relationship)
  - `Return_Data` to `Products`, `Calendar`, `Stores`
  - `Stores` to `Regions` (snowflake schema)

#### 2. Confirm Relationships
- **Cardinality:** One-to-many (primary keys on the lookup side, foreign keys on the data side)
- **Filter Direction:** One-way (downstream from lookup to data tables)
- **Filter Context:** Flows from lookup to data tables
- **Data Table Connections:** Via shared lookup tables

#### 3. Hide Foreign Keys
- **Report View:** Hide all foreign keys in data tables and `region_id` in Stores table.

#### 4. Update Data Fields
- **Date Fields:** "M/d/yyyy" format
- **Currency Fields:** `$ English` format
- **Geographical Data:**
  - `customer_city` as City
  - `customer_postal_code` as Postal Code
  - `customer_country` as Country/Region
  - `store_city` as City
  - `store_state` as State or Province
  - `store_country` as Country/Region
  - `full_address` as Address

#### 5. Save File

### PART 3: Adding DAX Measures

#### 1. Calculated Columns
- **Calendar Table:**
  - `Weekend` → `if [Name of Day] in {"Saturday", "Sunday"} then "Y" else "N"`
  - `End of Month` → `EOMONTH([date], 0)`
- **Customers Table:**
  - `Current Age` → `DATEDIFF([birthdate], TODAY(), YEAR)`
  - `Priority` → `if [owns_home] = "Yes" and [membership_card] = "Golden" then "High" else "Standard"`
  - `Short_Country` → `UPPER(LEFT([customer_country], 3))`
  - `House Number` → `Text.BeforeDelimiter([customer_address], " ")`
- **Products Table:**
  - `Price_Tier` → `if [product_retail_price] > 3 then "High" else if [product_retail_price] > 1 then "Mid" else "Low"`
- **Stores Table:**
  - `Years_Since_Remodel` → `DATEDIFF([last_remodel_date], TODAY(), YEAR)`

#### 2. Measures
- **Transaction Data:**
  - `Quantity Sold` → `SUM(Transaction_Data[quantity])`
  - `Total Transactions` → `COUNTROWS(Transaction_Data)`
  - `Total Revenue` → `SUMX(Transaction_Data, Transaction_Data[quantity] * RELATED(Products[product_retail_price]))`
  - `Total Cost` → `SUMX(Transaction_Data, Transaction_Data[quantity] * RELATED(Products[product_cost]))`
  - `Total Profit` → `[Total Revenue] - [Total Cost]`
  - `Profit Margin` → `DIVIDE([Total Profit], [Total Revenue], 0)`
  - `YTD Revenue` → `TOTALYTD([Total Revenue], Calendar[date])`
  - `60-Day Revenue` → `CALCULATE([Total Revenue], DATESINPERIOD(Calendar[date], MAX(Calendar[date]), -60, DAY))`
  - `Last Month Transactions` → `CALCULATE([Total Transactions], PREVIOUSMONTH(Calendar[date]))`
  - `Last Month Revenue` → `CALCULATE([Total Revenue], PREVIOUSMONTH(Calendar[date]))`
  - `Last Month Profit` → `CALCULATE([Total Profit], PREVIOUSMONTH(Calendar[date]))`
  - `Revenue Target` → `[Last Month Revenue] * 1.05`
- **Return Data:**
  - `Quantity Returned` → `SUM(Return_Data[quantity])`
  - `Total Returns` → `COUNTROWS(Return_Data)`
- **Common:**
  - `Return Rate` → `DIVIDE([Quantity Returned], [Quantity Sold], 0)`
  - `Weekend Transactions` → `CALCULATE([Total Transactions], FILTER(Calendar, Calendar[Weekend] = "Y"))`
  - `% Weekend Transactions` → `DIVIDE([Weekend Transactions], [Total Transactions], 0)`
  - `Unique Products` → `DISTINCTCOUNT(Products[product_name])`
  - `All Transactions` → `CALCULATE([Total Transactions], ALL(Transaction_Data))`
  - `All Returns` → `CALCULATE([Total Returns], ALL(Return_Data))`

### PART 4: Building the Report

#### 1. Rename Tab and Insert Logo
- **Tab Name:** Topline Performance
- **Insert:** Maven Market logo

#### 2. Matrix Visual
- **Rows:** `Product_Brand`
- **Values:** `Total Transactions`, `Total Profit`, `Profit Margin`, `Return Rate`
- **Conditional Formatting:**
  - `Total Transactions` → Data Bars
  - `Profit Margin` → Color Scales (White to Green)


  - `Return Rate` → Color Scales (White to Red)
- **Top N Filter:** Top 30 product brands, sorted by `Total Transactions`

#### 3. KPI Cards
- **Total Transactions:**
  - **Trend Axis:** Start of Month
  - **Target Goal:** Last Month Transactions
  - **Title:** Current Month Transactions
- **Total Profit:**
  - **Trend Axis:** Start of Month
  - **Target Goal:** Last Month Profit
  - **Title:** Current Month Profit
- **Total Returns:**
  - **Trend Axis:** Start of Month
  - **Target Goal:** Last Month Returns
  - **Title:** Current Month Returns (Color coding: Low is Good)

#### 4. Map Visual
- **Location:** `store_city`
- **Values:** `Total Transactions`
- **Slicer:** `store_country`
  - **Selection Controls:** Show Select All option
  - **Orientation:** Horizontal

#### 5. Treemap Visual
- **Values:** `Total Transactions`
- **Group:** `store_country`, `store_state`, `store_city`

#### 6. Column Chart
- **Values:** `Total Revenue`
- **Axis:** Start of Week
- **Filter:** Report level filter to show only 1998 data
- **Title:** Weekly Revenue Trending

#### 7. Gauge Chart
- **Values:** `Total Revenue`
- **Target/Max Value:** Revenue Target
- **Filter:** Visual level Top N filter to show the latest Start of Month
- **Title:** Revenue vs. Target

#### 8. Edit Interactions
- **Matrix:** Prevent Treemap from filtering

#### 9. Bookmarks and Notes
- **Bookmark:** Select `USA` in slicer and drill down to `Portland`
  - **Name:** Portland 1000 Sales
- **New Page:** Notes
  - **Text Box:** "Portland hits 1,000 sales in December"
  - **Button:** Link to bookmark
  - **Test:** CTRL-click the button
- **Additional Insights:** Create additional bookmarks and notes as needed

#### 10. Additional Exploration
- **Practice:** Creating new visuals, pages, or bookmarks for further data exploration.

By following these detailed steps, you can replicate the entire process in Power BI and effectively document it on your GitHub and portfolio.
