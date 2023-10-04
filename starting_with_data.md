Question 1: Filtering for which products were ordered the most from all the sessions that were through referrals

SQL Queries: 
SELECT DISTINCT 
	products."productSKU"  AS "ProductSKU", 
	products."productName" AS "Product Name", 
	products."orderedQuantity" AS "Ordered Quantity"
FROM all_sessions
INNER JOIN products ON all_sessions."productSKU" = products."productSKU" -- joining all_sessions table and products table on matching rows
WHERE "channelGrouping" = 'Referral'		-- filter to only get 'Referral' sessions
	AND "orderedQuantity" > 1000 			-- filtering out products of ordered quantity less than 1000
ORDER BY "orderedQuantity" DESC;

Answer: Most purchased product through recommendation ‘Kick Ball’ has been ordered 15170 times. It appears that the promotion of this product is going well.



Question 2: What is the name of the company's current product and how much is it worth?

SQL Queries:
SELECT  
    products."productName",
    SUM(sessions."productPrice" * products."stockLevel" ) AS total_storage_price 
-- The value was calculated by multiplying the number of items remaining by the price of those items.
FROM
    all_sessions AS sessions
INNER JOIN                              -- combine data from both tables based on the product SKU.
    products
	ON sessions."productSKU" = products."productSKU"
GROUP BY                                -- calculate the total storage price for each unique product name.
    products."productName"
ORDER BY                                -- orders the results by the "total_storage_price" 
    total_storage_price DESC
LIMIT 3;

Answer:
The most in-stock products and their values are listed below.
"Cam Outdoor Security Camera - USA" : 59979864, 
"Learning Thermostat 3rd Gen-USA - Stainless Steel" : 38084575, 
"Cam Indoor Security Camera - USA" :34844875 


Question 3: Among the products that are currently out of stock that people are interested in?
-> Among the products that are out of stock compared to the current number of orders, I selected products that people viewed.

SQL Queries:
SELECT                         -- specify two columns what i want to know from subquery
    subq."v2ProductName",
    subq.product_count
FROM                     
    (
        SELECT                 -- selects two columns 
            a."v2ProductName", 
            COUNT(a."productSKU") AS product_count
        FROM
            products AS p      --  Shorten long table names
        LEFT JOIN              -- combine "productSKU" datas from two tabels('all_sessions' and 'products')
            all_sessions AS a 
        ON
            p."productSKU" = a."productSKU"
        GROUP BY                -- make a group for 3 data columns
            p."stockLevel", p."orderedQuantity", a."v2ProductName"
        HAVING                  -- filter other resluts except the data that meet below condition
            p."stockLevel" < p."orderedQuantity"
    ) AS subq
ORDER BY
    subq."v2ProductName" ASC;

Answer:
People showed interest in a total of 19 sold-out products, including the '23 oz Wide Mouth Sport Bottle', 'Google Kick Ball', and 'Google Metallic Notebook Set'.


Question 4: What is the average review score of the top 5 most purchased products?

SQL Queries:

SELECT "name", "orderedQuantity", AVG("sentimentScore") AS product_sentimentScore   -- select query and calculate "sentimentScore" average value and it is called by 'product_sentimentScore'.
FROM products
GROUP BY "name", "orderedQuantity" -- make a group for 2 data columns
ORDER BY "orderedQuantity" DESC    -- orders the results by the "orderedQuantity"
LIMIT 5;                           -- Show only 5 results

Answer:
In order of most purchases, they are as follows.
'Kick Ball'(0.6), 
'22 oz Water Bottle'(0.9), 
'22 oz Water Bottle'(0.7), --The product name is the same as the item above, but the SKU is different.
'Sunglasses'(0.5), 
'Spiral Journal with Pen'(0.1) 