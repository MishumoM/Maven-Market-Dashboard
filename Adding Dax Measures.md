**PART 3: Adding DAX Measures**



**1. Calculated columns:**


***Calendar Table:***

Weekend = if [Name of Day] in {"Saturday", "Sunday"} then "Y" else "N"

End of Month = EOMONTH([date], 0)

***Customers Table:***

Current Age = DATEDIFF([birthdate], TODAY(), YEAR) 

Priority = if [owns_home] = "Yes" and [membership_card] = "Golden" then "High" else "Standard" 

Short_Country = UPPER(LEFT([customer_country], 3)) 

House Number = Text.BeforeDelimiter([customer_address], " ")

***Products Table:***

Price_Tier = if [product_retail_price] > 3 then "High" else if [product_retail_price] > 1 then "Mid" else "Low" 

***Stores Table:***

Years_Since_Remodel = DATEDIFF([last_remodel_date], TODAY(), YEAR)



**2.Measures Transaction Data:**


Quantity Sold = SUM(Transaction_Data[quantity]) 

Total Transactions = COUNTROWS(Transaction_Data) 

Total Revenue = SUMX(Transaction_Data, Transaction_Data[quantity] * RELATED(Products[product_retail_price])) 

Total Cost = SUMX(Transaction_Data, Transaction_Data[quantity] * RELATED(Products[product_cost])) 

Total Profit = [Total Revenue] - [Total Cost] 

Profit Margin = DIVIDE([Total Profit], [Total Revenue], 0) 

YTD Revenue = TOTALYTD([Total Revenue], Calendar[date]) 

60-Day Revenue = CALCULATE([Total Revenue], DATESINPERIOD(Calendar[date], MAX(Calendar[date]), -60, DAY)) 

Last Month Transactions = CALCULATE([Total Transactions], PREVIOUSMONTH(Calendar[date])) 

Last Month Revenue = CALCULATE([Total Revenue], PREVIOUSMONTH(Calendar[date])) 

Last Month Profit = CALCULATE([Total Profit], PREVIOUSMONTH(Calendar[date])) 

Revenue Target = [Last Month Revenue] * 1.05 

Quantity Returned = SUM(Return_Data[quantity]) 

Total Returns = COUNTROWS(Return_Data) 

Return Rate = DIVIDE([Quantity Returned], [Quantity Sold], 0) 

Weekend Transactions = CALCULATE([Total Transactions], FILTER(Calendar, Calendar[Weekend] = "Y")) 

% Weekend Transactions = DIVIDE([Weekend Transactions], [Total Transactions], 0) 

Unique Products = DISTINCTCOUNT(Products[product_name]) 

All Transactions = CALCULATE([Total Transactions], ALL(Transaction_Data)) 

All Returns = CALCULATE([Total Returns], ALL(Return_Data))
