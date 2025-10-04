# Project Title:
# Sales Data Analysis and Customer Segmentation using SQL

## Project Description:

This project involves analyzing sales data using SQL to extract meaningful business insights, such as sales performance, customer segmentation, and product line trends. The analysis also includes metrics like total sales, order quantity, and deal sizes. Through SQL queries, we examine sales data over time, segment customers based on behavior, and visualize patterns in sales by geography and customer segmentation. The project also includes creating views, handling complex aggregations, and implementing case studies around sales trends.

## Objective:

- The main objective of this project is to leverage SQL to:
- Extract key metrics like total orders, turnover, and quantity ordered.
- Segment customers based on their behaviors (e.g., loyalty and frequency of purchaes).
- Track product line and deal size sales across different time frames and countries.
- Perform trend analysis to understand the sales performance over months and years.
- Generate insights into the effectiveness of sales strategies and customer retention programs.

## Key Features:

- `KPI Metrics:` Total unique customers, number of orders, turnover, total quantity ordered.
- `Customer Segmentation:` Classify customers into categories like "Champions", "Loyal", "Needs Attention", based on their purchase frequency, recency, and monetary value
- `Sales Performance` by Deal Size and Product Line: Assess sales based on product categories and deal sizes (small, medium, large).
- `Sales Trends Over` Time: Analyze sales trends over months, days, and years to identify patterns.
- `Geographical Sales Analysis:` Track sales performance across countries.
- `RFM Segmentation:` Implement Recency, Frequency, and Monetary analysis for customer behavior.

Queries overview:

### CS1. KPI Metrics Analysis
Calculates key performance indicators (KPIs) such as total orders, quantity ordered, turnover, and unique countries, helping to understand overall sales performance.

### CS2. Deal Size Analysis by Country
Summarizes sales by deal size (small, medium, large) across different countries, helping to analyze how deal size varies geographically.

### CS3. Sales by Country and Month
Aggregates sales data by country and month, providing insights into monthly sales trends across different countries.

### CS4. Sales by Product Line and Deal Size
Breaks down sales by product line and deal size, helping to track revenue contributions from various product categories and deal sizes.

### CS5. Sales Trend Analysis
Analyzes sales over time (monthly or yearly), identifying sales trends and helping track performance changes.

### CS6. Sales by Weekday and Market Share
Compares weekly sales performance and calculates each day's market share, providing insights into daily sales fluctuations and market dominance.

### CS7. Customer Segmentation (RFM)
Segments customers based on Recency, Frequency, and Monetary (RFM) analysis, categorizing them into different groups like 'Champions', 'Loyal', etc., for targeted marketing strategies.

### CS8. Customer Sales Segmentation by Country
Analyzes customer segmentation by RFM scores across different countries, providing insights into customer behavior and sales distribution by geography.

### CS9. Top Customers by Sales in USA
Identifies the top customers in the USA based on total sales, helping to highlight high-value customers and optimize business strategies.

### CS10. Product Categories Type
Lists distinct product categories, particularly focusing on specific product lines like 'Motorcycles', allowing for detailed product-wise revenue tracking.
These overviews capture the essence of each query while focusing on its core objective and outcome.

# Performing Analysis with MySQL
## DDL Statement for Sales_Data Table.
```sql
CREATE TABLE Sales_Data (
    ORDERNUMBER	INT,
    QUANTITYORDERED	INT,
    PRICEEACH	INT,
    ORDERLINENUMBER	INT,
    SALES	DOUBLE,
    ORDERDATE	VARCHAR(512),
    STATUS	VARCHAR(512),
    QTR_ID	INT,
    MONTH_ID	INT,
    YEAR_ID	INT,
    PRODUCTLINE	VARCHAR(512),
    MSRP	INT,
    PRODUCTCODE	VARCHAR(512),
    CUSTOMERNAME	VARCHAR(512),
    PHONE	VARCHAR(512),
    ADDRESSLINE1	VARCHAR(512),
    ADDRESSLINE2	VARCHAR(512),
    CITY	VARCHAR(512),
    STATE	VARCHAR(512),
    POSTALCODE	INT,
    COUNTRY	VARCHAR(512),
    TERRITORY	VARCHAR(512),
    CONTACTLASTNAME	VARCHAR(512),
    CONTACTFIRSTNAME	VARCHAR(512),
    DEALSIZE	VARCHAR(512)
);
```

### Ensure the table is formatted with properly labeled and meaningful column names.
```sql
CREATE OR REPLACE VIEW ORDER_DETAILS AS
WITH CTE AS 
(
    SELECT
        ORDERNUMBER AS ORDER_NUMBER,
        CUSTOMERNAME AS COMPANY_NAME,
        CONCAT(CONTACTFIRSTNAME, ' ', CONTACTLASTNAME) AS CUSTOMER_NAME,
        QUANTITYORDERED AS QTY_ORDERS,
        SALES AS SALES,
        STR_TO_DATE(ORDERDATE, '%d/%m/%y') AS ORDER_DATE,
        STATUS AS STATUS,
        PRICEEACH AS PRICE_EACH,
        ORDERLINENUMBER AS ORDER_LINE_NUMBER,
        QTR_ID,
        YEAR_ID AS YEAR,
        MONTH_ID AS MONTH,
        PRODUCTCODE AS PRODUCT_CODE,
        PRODUCTLINE AS PRODUCT_LINE,
        CITY AS CITY,
        STATE AS STATE,
        COUNTRY AS COUNTRY,
        DEALSIZE AS DEAL_SIZE
    FROM SALES_DATA
) 
SELECT * FROM CTE;
```

## CS1. KPI METRICS ANALYSIS:
```sql
SELECT 
    COUNT(DISTINCT ORDER_NUMBER) AS TOTAL_ORDERS_ID,
    COUNT(ORDER_LINE_NUMBER) AS TOTAL_ORDERS,
    SUM(QTY_ORDERS) AS TOTAL_QTY_ORDERS,
    ROUND(SUM(SALES), 0) AS TURNOVER,
    COUNT(DISTINCT COUNTRY) AS TOTAL_COUNTRY
FROM
    ORDER_DETAILS;
```
### Output:


## CS2. DEAL SIZE ANALYSIS BY COUNTRY:
```sql
SELECT 
    DEAL_SIZE,
    ROUND(SUM(SALES), 0) AS SALES_$
FROM 
    ORDER_DETAILS
GROUP BY 1
ORDER BY 2 DESC;
```
### Output:
| Pizza Size | Total Orders |
|------------|--------------|
| Medium     | 6,087,432    |
| Small      | 2,643,077    |
| Large      | 1,302,119    |


3. SALES BY MONTH:
```sql
SELECT 
    
    MONTHNAME(ORDER_DATE) AS MONTH,
    ROUND(SUM(SALES), 0) AS 'Total REV'
FROM 
    ORDER_DETAILS
GROUP BY 1, 2
ORDER BY 3 DESC;
```
### Output:
| Month      | Total REV |
|------------|--------------|
| November   | 2,118,886    |
| October    | 1,121,215    |
| May        |   923,973    |
| February   |   810,442    |
| January    |   785,874    |
| March      |   754,501    |
| April      |   669,391    |
| August     |   659,311    |
| December   |   634,679    |
| September  |   584,724    |
| July       |   514,876    |
| June       |   454,757    |


4. SALES BY PRODUCT LINE AND DEAL SIZE:
```sql
SELECT 
    PRODUCT_LINE,
    ROUND(SUM(CASE WHEN DEAL_SIZE='SMALL' THEN SALES ELSE 0 END), 0) AS 'SMALL_SALES_$',
    ROUND(SUM(CASE WHEN DEAL_SIZE='MEDIUM' THEN SALES ELSE 0 END), 0) AS 'MEDIUM_SALES_$',
    ROUND(SUM(CASE WHEN DEAL_SIZE='LARGE' THEN SALES ELSE 0 END), 0) AS 'LARGE_SALES_$'
FROM 
    ORDER_DETAILS
GROUP BY 1;
```
### Output:
| Product Line       | Small Sales $ | Medium Sales $ | Large Sales $ |
|-------------------|---------------|----------------|---------------|
| Motorcycles       | 326,874       | 680,370        | 159,145       |
| Classic Cars      | 692,715       | 2,430,258      | 796,642       |
| Trucks and Buses  | 254,506       | 817,028        | 56,256        |
| Vintage Cars      | 608,543       | 1,085,540      | 209,068       |
| Planes            | 364,366       | 538,606        | 72,032        |
| Ships             | 291,702       | 422,735        | 0             |
| Trains            | 104,371       | 112,895        | 8,977         |


5. SALES TREND ANALYSIS:
```sql
WITH SALES_TREND_TIME AS 
    (SELECT
        STR_TO_DATE(ORDER_DATE, '%d/%m/%y') AS DATE,
        YEAR AS YEAR_ID,
        MONTH(ORDER_DATE) AS MONTH_LINE,
        MONTHNAME(ORDER_DATE) AS MONTH_NAME,
        ORDER_DETAILS.*
    FROM ORDER_DETAILS)
SELECT 
    YEAR_ID, 
    MONTH_LINE, 
    MONTH_NAME,
    ROUND(SUM(SALES), 0) AS TOTAL_SALES
FROM 
    SALES_TREND_TIME
GROUP BY 1, 2, 3
ORDER BY 1, 2;
```
### Output:
| Year | Month | Month Name | Total Sales |
|------|-------|------------|-------------|
| 2003 | 1     | January    | 129,754     |
| 2003 | 2     | February   | 140,836     |
| 2003 | 3     | March      | 174,505     |
| 2003 | 4     | April      | 201,610     |
| 2003 | 5     | May        | 192,673     |
| 2003 | 6     | June       | 168,083     |
| 2003 | 7     | July       | 187,732     |
| 2003 | 8     | August     | 197,809     |
| 2003 | 9     | September  | 263,973     |
| 2003 | 10    | October    | 568,291     |
| 2003 | 11    | November   | 1,029,838   |
| 2003 | 12    | December   | 261,876     |
| 2004 | 1     | January    | 316,577     |
| 2004 | 2     | February   | 311,420     |
| 2004 | 3     | March      | 205,734     |
| 2004 | 4     | April      | 206,148     |
| 2004 | 5     | May        | 273,438     |
| 2004 | 6     | June       | 286,674     |
| 2004 | 7     | July       | 327,144     |
| 2004 | 8     | August     | 461,501     |


6. SALES BY WEEKDAY AND MARKET SHARE:
```sql
WITH SALES_TREND_TIME AS 
    (SELECT
        STR_TO_DATE(ORDER_DATE, '%d/%m/%y') AS DATE,
        DAYNAME(ORDER_DATE) AS DAY,
        MONTHNAME(ORDER_DATE) AS MONTH_NAME,
        ORDER_DETAILS.*
    FROM ORDER_DETAILS)
SELECT 
    DAY,
    ROUND(SUM(SALES), 0) AS WEEKLY_SALES_COMPARISON_$,
    ROUND((ROUND(SUM(SALES), 0)) * 100 / ((SELECT ROUND(SUM(SALES), 0) FROM ORDER_DETAILS)), 1) AS MARKET_SHARE_PERCENTAGE
FROM 
    SALES_TREND_TIME
GROUP BY 1
ORDER BY 2 DESC;
```
### OUTPUT:
| Day       | Weekly Sales Comparison $ | Market Share % |
|-----------|--------------------------|----------------|
| Friday    | 2,140,654                | 21.3           |
| Thursday  | 1,947,412                | 19.4           |
| Wednesday | 1,947,146                | 19.4           |
| Tuesday   | 1,900,773                | 18.9           |
| Monday    | 1,254,535                | 12.5           |
| Sunday    | 421,782                  | 4.2            |
| Saturday  | 420,327                  | 4.2            |


7. CUSTOMER SEGMENTATION (RFM):
```sql
CREATE OR REPLACE VIEW RFM AS
WITH CUSTOMER_SUMMARY_TABLE AS 
    (SELECT 
        CUSTOMERNAME,
        DATEDIFF((SELECT MAX(STR_TO_DATE(ORDERDATE, '%d/%m/%y')) FROM SALES_DATA), MAX(STR_TO_DATE(ORDERDATE, '%d/%m/%y'))) AS R_VALUES,
        COUNT(DISTINCT ORDERNUMBER) AS F_VALUES,
        ROUND(SUM(SALES), 0) AS M_VALUES
    FROM SALES_DATA 
    GROUP BY CUSTOMERNAME),
RFM_SCORE AS 
    (SELECT S.*,
            NTILE(5) OVER(ORDER BY R_VALUES DESC) AS R_SCORE,
            NTILE(5) OVER(ORDER BY F_VALUES DESC) AS F_SCORE,
            NTILE(5) OVER(ORDER BY M_VALUES DESC) AS M_SCORE
    FROM CUSTOMER_SUMMARY_TABLE AS S),
RFM_COMBINATION_SCORE AS
    (SELECT R.*, 
            (R_SCORE + F_SCORE + M_SCORE) AS TOTAL_RMF,
            CONCAT_WS('', R_SCORE, F_SCORE, M_SCORE) AS RFM_SCORE_COMBINATION
    FROM RFM_SCORE AS R)
SELECT 
    RC.*,
    CASE 
        WHEN RFM_SCORE_COMBINATION IN (455, 542, 544, 552, 553, 452, 545, 554, 555) THEN 'CHAMPIONS'
        WHEN RFM_SCORE_COMBINATION IN (344, 345, 353, 354, 355, 443, 451, 342, 351, 352, 441, 442, 444, 445, 453, 454, 541, 543, 515, 551) THEN 'LOYAL CUSTOMERS'
        WHEN RFM_SCORE_COMBINATION IN (414, 415, 214, 211, 212, 213, 241, 251, 312, 314, 311, 313, 315, 243, 245, 252, 253, 255, 242, 244, 254) THEN 'PROMISING CUSTOMERS'
        WHEN RFM_SCORE_COMBINATION IN(513, 413, 511, 411, 512, 341, 412, 343, 514) THEN 'POTENTIAL LOYALISTS'
        WHEN RFM_SCORE_COMBINATION IN (141, 142, 143, 144, 151, 152, 155, 145, 153, 154, 215) THEN 'NEEDS ATTENTION'
        WHEN RFM_SCORE_COMBINATION IN (113, 111, 112, 114, 115) THEN 'ABOUT TO SLEEP'
        ELSE 'OTHER'
    END AS CUSTOMER_SEGMENTATION
FROM 
    RFM_COMBINATION_SCORE AS RC;
    ```
### Output:
| Customer Name                     | R Values | F Values | M Values | R Score | F Score | M Score | Total RMF | RFM Score Combination | Customer Segmentation     |
|----------------------------------|----------|----------|----------|---------|---------|---------|-----------|---------------------|--------------------------|
| Euro Shopping Channel             | 0        | 26       | 912,294  | 5       | 1       | 1       | 7         | 511                 | POTENTIAL LOYALISTS      |
| Mini Gifts Distributors Ltd.      | 2        | 17       | 654,858  | 5       | 1       | 1       | 7         | 511                 | POTENTIAL LOYALISTS      |
| Australian Collectors, Co.        | 183      | 5        | 200,995  | 3       | 1       | 1       | 5         | 311                 | PROMISING CUSTOMERS      |
| Muscle Machine Inc                | 181      | 4        | 197,737  | 3       | 1       | 1       | 5         | 311                 | PROMISING CUSTOMERS      |
| La Rochelle Gifts                 | 0        | 4        | 180,125  | 5       | 1       | 1       | 7         | 511                 | POTENTIAL LOYALISTS      |
| Dragon Souveniers, Ltd.           | 90       | 5        | 172,990  | 4       | 1       | 1       | 6         | 411                 | POTENTIAL LOYALISTS      |
| Land of Toys Inc.                 | 197      | 4        | 164,069  | 2       | 1       | 1       | 4         | 211                 | PROMISING CUSTOMERS      |
| The Sharp Gifts Warehouse         | 39       | 4        | 160,010  | 5       | 2       | 1       | 8         | 521                 | OTHER                    |
| AV Stores, Co.                    | 195      | 3        | 157,808  | 3       | 3       | 1       | 7         | 331                 | OTHER                    |
| Anna's Decorations, Ltd           | 83       | 4        | 153,996  | 4       | 1       | 1       | 6         | 411                 | POTENTIAL LOYALISTS      |
| Souveniers And Things Co.         | 2        | 4        | 151,571  | 5       | 1       | 1       | 7         | 511                 | POTENTIAL LOYALISTS      |
| Corporate Gift Ideas Co.          | 97       | 4        | 149,882  | 4       | 1       | 1       | 6         | 411                 | POTENTIAL LOYALISTS      |
| Salzburg Collectables             | 14       | 4        | 149,799  | 5       | 1       | 1       | 7         | 511                 | POTENTIAL LOYALISTS      |
| Danish Wholesale Imports          | 46       | 5        | 145,042  | 5       | 1       | 1       | 7         | 511                 | POTENTIAL LOYALISTS      |
| Saveley & Henriot, Co.            | 455      | 3        | 142,874  | 1       | 2       | 1       | 4         | 121                 | OTHER                    |
| L'ordine Souveniers               | 21       | 3        | 142,601  | 5       | 2       | 1       | 8         | 521                 | OTHER                    |
| Rovelli Gifts                     | 200      | 3        | 137,956  | 2       | 2       | 1       | 5         | 221                 | OTHER                    |
| Reims Collectables                | 62       | 5        | 135,043  | 4       | 1       | 1       | 6         | 411                 | POTENTIAL LOYALISTS      |
| Scandinavian Gift Ideas           | 89       | 3        | 134,259  | 4       | 4       | 1       | 9         | 441                 | LOYAL CUSTOMERS           |
| Online Diecast Creations Co.      | 208      | 3        | 131,685  | 2       | 2       | 2       | 6         | 222                 | OTHER                    |


8. CUSTOMER SALES SEGMENTATION BY COUNTRY:
```sql
SELECT 
    COUNTRY,
    SUM(CASE WHEN CUSTOMER_SEGMENTATION = 'CHAMPIONS' THEN QUANTITYORDERED ELSE 0 END) AS 'CHAMPIONS',
    SUM(CASE WHEN CUSTOMER_SEGMENTATION = 'LOYAL CUSTOMERS' THEN QUANTITYORDERED ELSE 0 END) AS 'LOYAL CUSTOMERS',
    SUM(CASE WHEN CUSTOMER_SEGMENTATION = 'LOYAL CUSTOMERS' THEN QUANTITYORDERED ELSE 0 END) AS 'LOYAL CUSTOMERS',
    SUM(CASE WHEN CUSTOMER_SEGMENTATION = 'POTENTIAL LOYALISTS' THEN QUANTITYORDERED ELSE 0 END) AS 'POTENTIAL LOYALISTS',
    SUM(CASE WHEN CUSTOMER_SEGMENTATION = 'NEEDS ATTENTION' THEN QUANTITYORDERED ELSE 0 END) AS 'NEEDS ATTENTION',
    SUM(CASE WHEN CUSTOMER_SEGMENTATION = 'ABOUT TO SLEEP' THEN QUANTITYORDERED ELSE 0 END) AS 'ABOUT TO SLEEP',
    SUM(CASE WHEN CUSTOMER_SEGMENTATION = 'OTHER' THEN QUANTITYORDERED ELSE 0 END) AS 'OTHER'
FROM 
    SALES_DATA AS OD
LEFT JOIN 
    RFM AS R ON OD.CUSTOMERNAME = R.CUSTOMERNAME
GROUP BY 1;
```

9. TOP CUSTOMERS BY SALES IN USA:
```sql
SELECT 
    *
FROM
    (SELECT 
        CUSTOMERNAME AS COMPANY_NAME,
        ROUND(SUM(SALES), 0) AS TOTAL_SALES,
        COUNT(DISTINCT ORDERLINENUMBER) AS TOTAL_ORDERS
    FROM 
        SALES_DATA
    WHERE COUNTRY = 'USA'
    GROUP BY CUSTOMERNAME) AS SUMMARY_TABLE
HAVING TOTAL_SALES BETWEEN 75000 AND 100000
ORDER BY 2 DESC
LIMIT 5;
```

10. PRODUCT CATEGORIES TYPE:
```sql
SELECT DISTINCT 
    PRODUCT_LINE
FROM 
    ORDER_DETAIL;
```


