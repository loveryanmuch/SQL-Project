What issues will you address by cleaning the data?

Step 1. Modify the unit cost 
1) Change the price value for better readability.
    UPDATE analytics
    SET "unit_price" = "unit_price" / 1000000.0;

2) Reviewed the modified data 
    SELECT "unit_price" FROM analytics
    ORDER BY "visitNumber" DESC
    LIMIT 100

3) repeat step 1 and 2 for all_sessions' "productPrice" column

Step 2. Change all columns names to camel case.
1) Change the column names
ALTER TABLE analytics
RENAME COLUMN "timeonsite" TO "timeOnSite";

ALTER TABLE analytics
RENAME COLUMN "fullvisitorId" TO "fullVisitorId";

ALTER TABLE analytics
RENAME COLUMN "userid" TO "userId";

ALTER TABLE all_sessions
RENAME COLUMN "pageviews" TO "pageViews";

ALTER TABLE all_sessions
RENAME COLUMN "visitid" TO "visitId";

Step 3. Check if there are some different data in the two tables. 
1) Check if there are some different data in the two tables. 
SELECT "productSKU", "total_ordered" FROM sales_report
EXCEPT
SELECT "productSKU", "total_ordered" FROM sales_by_sku  -- returns rows that exist in the 'sales_report' table but not in the 'sales_by_sku' table.
UNION ALL                                               -- Combines the queries above and below.
SELECT "productSKU", "total_ordered" FROM sales_by_sku
EXCEPT
SELECT "productSKU", "total_ordered" FROM sales_report; -- returns rows that exist in the 'sales_by_sku' table but not in the 'sales_report' table.

2) The output showed that there were rows in sales_by_sku table that did not exist in sales_report table.

Step 4. standardize column names that represent the same column in different tables.
1) Identify which columns will be changed -> SKU, productName

2) Chage the Column names
ALTER TABLE products
RENAME COLUMN "SKU" TO "productSKU";

ALTER TABLE products
RENAME COLUMN "name" TO "productName";

Step 5. output as 0 when NULL
1) output as 0 when NULL
SELECT 
	 COALESCE("timeOnSite", 0) AS "timeOnSite",
	 COALESCE("productQuantity", 0) AS "productQuantity",
	 COALESCE("pageViews", 0) AS "pageViews",
	 COALESCE("productQuantity", 0) AS "productQuantity"
FROM all_sessions;

SELECT 
	 COALESCE("units_sold", 0) AS "units_sold",
	 COALESCE("revenue", 0) AS "revenue"
FROM analytics;

Step 6. Change the date type 
1) Change the date type 
ALTER TABLE all_sessions
ALTER COLUMN "date" TYPE date;

ALTER TABLE analytics
ALTER COLUMN "date" TYPE date;
