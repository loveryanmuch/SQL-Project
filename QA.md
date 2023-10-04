What are your risk areas? Identify and describe them.

1. Remove rows without data values (executed in cleaning_data step)
    1) Empty data values were filled with the number 0 or (not set).
queries:
SELECT 
	 COALESCE("timeOnSite", 0) AS "timeOnSite",            -- replace any NULL values in this columns with '0'
	 COALESCE("productQuantity", 0) AS "productQuantity",
	 COALESCE("pageViews", 0) AS "pageViews",
	 COALESCE("productQuantity", 0) AS "productQuantity"
FROM all_sessions;

SELECT 
	 COALESCE("units_sold", 0) AS "units_sold",            -- replace any NULL values in this columns with '0'
	 COALESCE("revenue", 0) AS "revenue"
FROM analytics;

SELECT 
	 COALESCE("totalTransactionRevenue", ('(not set)')) AS "totalTransactionRevenue",  -- replace any NULL values in this columns with '(net set)'
	 COALESCE("transactions", '(not set)') AS "transactions",
	 COALESCE("sessionQualityDim", '(not set)') AS "sessionQualityDim",
	 COALESCE("productRefundAmount",'(not set)') AS "productRefundAmount",
	 COALESCE("currencyCode", '(not set)') AS "currencyCode",
	 COALESCE("itemQuantity", '(not set)') AS "itemQuantity",
	 COALESCE("itemRevenue",'(not set)') AS "itemRevenue",
	 COALESCE("transactionRevenue", '(not set)') AS "transactionRevenue",
	 COALESCE("transactionId", '(not set)') AS "transactionId",
	 COALESCE("searchKeyword",'(not set)') AS "searchKeyword",
	 COALESCE("eCommerceAction_option", '(not set)') AS "eCommerceAction_option"
	 
FROM all_sessions;

    2) Check if there is any NULL data I missed
SELECT *
FROM all_sessions
WHERE "fullVisitorId" = NULL              -- Conditions for outputting data with NULL values                               
	OR "channelGrouping" = NULL 
	OR "time" = NULL 
	OR "country" = NULL 
	OR "city" = NULL 
	OR "totalTransactionRevenue" = NULL 
	OR "transactions" = NULL 
	OR "timeOnSite" = NULL 
	OR "pageViews" = NULL 
	OR "sessionQualityDim" = NULL 
	OR "date" = NULL 
	OR "visitId" = NULL 
	OR "type" = NULL 
	OR "productRefundAmount" = NULL 
	OR "productQuantity" = NULL 
	OR "productPrice" = NULL 
	OR "productRevenue" = NULL 
	OR "productSKU" = NULL 
	OR "v2ProductName" = NULL 
	OR "v2ProductCategory" = NULL 
	OR "productVariant" = NULL 	
	OR "currencyCode" = NULL 	
	OR "itemQuantity" = NULL
	OR "itemRevenue" = NULL 	
	OR "transactionRevenue" = NULL 	
	OR "transactionId" = NULL	
	OR "pageTitle" = NULL 	
	OR "searchKeyword" = NULL 	
	OR "pagePathLevel1" = NULL	
	OR "eCommerceAction_type" = NULL	
	OR "eCommerceAction_step" = NULL 	
	OR "eCommerceAction_option" = NULL 			
	;



2. Separate unusual data
    1)Data with product prices of 0 are collected separately and moved to a new table.
queries:
CREATE TABLE zero_product_price AS
SELECT *
FROM all_sessions
WHERE all_sessions."productPrice" = 0;

CREATE TABLE zero_unit_price AS
SELECT *
FROM analytics
WHERE analytics."unit_price" = 0;


    2) Make sure data is also persisted in 'all_sessions'.
INSERT INTO all_sessions
SELECT *
FROM zero_product_price

INSERT INTO analytics
SELECT *
FROM zero_unit_price

