Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:
SELECT "country", 
	SUM("productPrice" * "productQuantity") AS transaction_revenue_by_country
FROM all_sessions
WHERE "productQuantity" IS NOT NULL
    AND "country" <> '(not set)'
GROUP BY "country"
ORDER BY transaction_revenue_by_country DESC

SELECT "city", 
	SUM("productPrice" * "productQuantity") AS transaction_revenue_by_city
FROM all_sessions
WHERE "productQuantity" IS NOT NULL
	AND "city" <> 'not available in demo dataset'
GROUP BY "city"
ORDER BY transaction_revenue_by_city DESC


Answer: 
Logically, I think country and city transaction revenues can not be combined to one SQL query because each city is part of a country and therefore the output of the country would not make sense.
The highest level of transaction revenues on the site for country is 'United States' and city is 'Mountain View'.



**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:

SELECT "country",
    AVG("productQuantity") AS avg_product_quantity
FROM all_sessions
WHERE "country" <> '(not set)'
    AND "productQuantity" IS NOT NULL
GROUP BY "country"
ORDER BY avg_product_quantity DESC


SELECT "city",
    AVG("productQuantity") AS avg_product_quantity
FROM all_sessions
WHERE "city" <> 'not available in demo dataset'
    AND "productQuantity" IS NOT NULL
GROUP BY "city"
ORDER BY avg_product_quantity DESC

Answer: The highest average number of products ordered from visitors by country is 'Spain' with 10.0 and by city is 'Madrid' with 10.0.


**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:
SELECT "country", "v2ProductName", "v2ProductCategory", "productQuantity"
FROM all_sessions
WHERE "productQuantity" IS NOT NULL
ORDER BY "v2ProductCategory" ASC

SELECT "city", "v2ProductName", "v2ProductCategory", "productQuantity"
FROM all_sessions
WHERE "productQuantity" IS NOT NULL
	AND "city" != 'not available in demo dataset'
	AND "city" !='(not set)'
ORDER BY "v2ProductCategory" ASC, "city" ASC


Answer:
In case people purchase the same product in the same category, I have also listed the product name. Since most of the actual buyers are from 'the United States', it was difficult to find patterns in other countries. However, all products related to 'Nest-USA' are purchased by buyers in 'the United States', and products related to the 'YouTube' brand are purchased only by buyers in 'North America' ('Canada' and 'the United States').
Among cities, consumers in 'Mountain View' and 'New York' made the most purchases, and among them, 'Mountain View' was the only one to purchase women's clothing and had a variety of purchase categories.



**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:
SELECT
    "country", "v2ProductName","v2ProductCategory", "productQuantity",
	RANK() OVER (PARTITION BY "country" ORDER BY "productQuantity" DESC) AS product_rank
FROM
    all_sessions
WHERE
    "productQuantity" IS NOT NULL
    AND "country" != 'not available in demo dataset'
	AND "v2ProductCategory" != '${escCatTitle}'
	AND "v2ProductCategory" != '(not set)'
ORDER BY  "country" ASC;


SELECT
    "city", "v2ProductName","v2ProductCategory", "productQuantity",
	RANK() OVER (PARTITION BY "city" ORDER BY "productQuantity" DESC) AS product_rank
FROM
    all_sessions
WHERE
    "productQuantity" IS NOT NULL
    AND "city" != 'not available in demo dataset'
	AND "city" !='(not set)'
	AND "v2ProductCategory" != '${escCatTitle}'
	AND "v2ProductCategory" != '(not set)'
ORDER BY  "city" ASC;


Answer:
In countries other than 'the United States', It was difficult to find a pattern because only one product was sold. In 'the United States', Leatherette Journal was the most sold with 65 units, and Reusable Shopping Bag took second and third place. The important thing is that those bags were sold in large quantities only in 'the United States'.
Since most cities only purchased one product, there was no point in looking for a top-seller. However, only 'Atlanta' and 'New York' were able to determine clear top-selling products with 4 and 2. In 'Atlanta', only the top-selling product, the reusable bag, was purchased. , 'New York' purchased 2 units of 'Nest® Protect Smoke + CO White Wired Alarm-USA' and it became a top-selling product, but since 3 products were purchased at a time, there was no significant differentiation, and products from the most diverse categories were purchased.


**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:
SELECT
    "country",
    SUM("productPrice" * "productQuantity") AS total_revenue,
    SUM("time") AS total_time
FROM
    all_sessions AS a
WHERE
    a."country" IS NOT NULL
    AND a."country" != 'not available in demo dataset'
    AND "productPrice" IS NOT NULL
    AND "productQuantity" IS NOT NULL
    AND "time" IS NOT NULL
GROUP BY
    a."country"
ORDER BY
    total_time DESC;


SELECT
    "city",
    SUM("productPrice" * "productQuantity") AS total_revenue,
    SUM("time") AS total_time
FROM
    all_sessions AS a
WHERE
    a."city" IS NOT NULL
    AND a."city" != 'not available in demo dataset'
	AND "city" !='(not set)'
    AND "productPrice" IS NOT NULL
    AND "productQuantity" IS NOT NULL
    AND "time" IS NOT NULL
GROUP BY
    a."city"
ORDER BY
    total_time DESC;


Answer:
When looking at the answers to the above questions and the above code comprehensively, 'United States' had the highest sales and the overwhelming majority of page visit times. However, the page access times for 'Argentina' and 'Ireland', which had the second highest sales, were not long. And Finland, which had the lowest sales, also had the lowest page access time.
Among cities, 'Mountain View' had the highest sales and connection time, and similar to the country results, 'Atlanta', which had the second highest sales, did not have the second highest connection time. 
To summarize, the group with the highest sales also had longer page access times. However, from second place onwards, sales and page access time were not proportional. However, it was proportional to the group with the lowest sales.






