**Step 1: Conecting and Shaping the Data**

1. ***Updated Power BI Options and Settings***
   - Deselected "Autodetect new relationships after data is loaded" in the Data Load tab.
   - Set Locale for import to "English (United States)" in the Regional Settings tab.

2. ***Connected to MavenMarket_Customers CSV***
   - Table Name: Customers
   - Promoted headers
   - Set data types for columns
   - Created new columns:
     - `full_name` (concatenated first and last name)
     - `birth_year` (extracted year from birthdate)
     - `has_children` (conditional column)

3. ***Connected to MavenMarket_Products CSV***
   - Table Name: Products
   - Promoted headers
   - Set data types for columns
   - Created new columns:
     - `discount_price` (calculated as 90% of retail price)
   - Formatted columns and handled null values

4. ***Connected to MavenMarket_Stores CSV***
   - Table Name: Stores
   - Promoted headers
   - Set data types for columns
   - Created new columns:
     - `full_address` (concatenated city, state, and country)
     - `area_code` (extracted from phone number)

5. ***Connected to MavenMarket_Regions CSV***
   - Table Name: Regions
   - Promoted headers
   - Set data types for columns

6. ***Connected to MavenMarket_Calendar CSV***
   - Table Name: Calendar
   - Promoted headers
   - Created new columns:
     - `Start of Week`, `Name of Day`, `Start of Month`, `Name of Month`, `Quarter of Year`, `Year`

7. ***Connected to MavenMarket_Returns CSV***
   - Table Name: Return_Data
   - Promoted headers
   - Set data types for columns

8. ***Connected to MavenMarket_Transactions Folder***
   - Combined CSV files from 1997 and 1998
   - Table Name: Transaction_Data
   - Promoted headers
   - Set data types for columns

9. ***Excluded Tables from Report Refresh***
   - Excluded all tables except `Transaction_Data` and `Return_Data`

10. ***Saved File***
    - File Name: MavenMarket_Report.pbix
