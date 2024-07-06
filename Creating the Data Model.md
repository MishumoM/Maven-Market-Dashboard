### Part 2: Creating the Data Model

1. **Arranged Tables in MODEL View**
   - Placed lookup tables above data tables

2. **Created Relationships**
   - Connected `Transaction_Data` to Customers, Products, Stores, and Calendar
   - Connected `Return_Data` to Products, Calendar, and Stores
   - Connected Stores to Regions (snowflake schema)

3. **Confirmed Relationships**
   - Ensured cardinality and filter directions were correct

4. **Hid Foreign Keys**
   - Hid all foreign keys in data tables and `region_id` in the Stores table

5. **Updated Data Fields**
   - Formatted date fields, currency fields, and geographical data
![image](https://github.com/MishumoM/Maven-Market-Dashboard/assets/127921798/ff4d790d-8609-49d5-86fa-ccd3a49e3fe0)
