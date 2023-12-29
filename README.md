-- Selecting all the cleaned up data to be used for the analysis.
SELECT * FROM [USA shopping];

-- How many unique states were recorded in this dataset?

SELECT DISTINCT(Location) 
FROM [USA shopping];

-- What are the most common forms of payment?

SELECT [Payment_Method], COUNT(*) AS Payment_Type 
FROM dbo.[USA shopping]
GROUP BY [Payment_Method] 
ORDER BY Payment_Type DESC;

-- What were the most common items purchased by gender?

SELECT [Gender], [Item_Purchased], COUNT(*) AS Item_Type
FROM dbo.[USA shopping]
GROUP BY [Item_Purchased], [Gender]
ORDER BY Item_Type DESC;

-- Which type of clothing had the highest average customer rating?

SELECT [Item_Purchased], ROUND(AVG([Review_Rating]), 2) AS Average_Score
FROM dbo.[USA shopping]
GROUP BY [Item_Purchased]
ORDER BY Average_Score DESC;

-- What type of clothing is most popular in each State?

WITH RankedItems AS (
    SELECT
        [Location],
        [Item_Purchased],
		COUNT(*) AS Purchases,
        ROW_NUMBER() OVER (PARTITION BY Location ORDER BY COUNT(*) DESC) AS Rank
    FROM
        dbo.[USA shopping]
    GROUP BY
        [Location], [Item_Purchased]
)

SELECT
    [Location],
    [Item_Purchased],
	Purchases
FROM
    RankedItems
WHERE
    Rank = 1;

-- Does having a subscription influence purchase behaviour?

SELECT [Subscription_Status], AVG([Previous_Purchases]) AS PurchasesNumber
FROM dbo.[USA shopping]
GROUP BY [Subscription_Status]
ORDER BY PurchasesNumber;

-- What was the most popular colour of item sold for men compared to women?

WITH RankedColor AS (
    SELECT 
    [Gender], 
    [Colour],
    COUNT(*) AS Count,
    ROW_NUMBER() OVER (PARTITION BY [Gender] 
    ORDER BY COUNT(*) DESC) AS Rank
    FROM dbo.[USA shopping]
    GROUP BY [Gender], [Colour]
)
SELECT [Gender], [Colour]
FROM [RankedColor]
WHERE 
    Rank = 1;
