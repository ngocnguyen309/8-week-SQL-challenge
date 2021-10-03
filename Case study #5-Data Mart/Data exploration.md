__1. What day of the week is used for each week_date value?__
```
SELECT DISTINCT(TO_CHAR(week_date, 'day')) AS week_day 
FROM clean_weekly_sales;
```
![image](https://user-images.githubusercontent.com/89729029/135710988-38251f7e-04de-42e1-8f4e-07ea5e075b07.png)

__2. What range of week numbers are missing from the dataset?__
```
WITH week AS
(
SELECT GENERATE_SERIES(1,52) AS week_number
)

SELECT w.week_number
FROM week AS w
LEFT OUTER JOIN clean_weekly_sales AS c
ON w.week_number=c.week_number
WHERE c.week_number is NULL
```
![image](https://user-images.githubusercontent.com/89729029/135711310-d2a54af3-7676-4cc9-8a84-676dac184adc.png)

__3. How many total transactions were there for each year in the dataset?__
```
SELECT calendar_year, 
       COUNT(transactions) AS total
FROM clean_weekly_sales
GROUP BY calendar_year
ORDER BY calendar_year
```
![image](https://user-images.githubusercontent.com/89729029/135711398-8cd9fceb-28ae-4571-8e09-57ebd35eb8da.png)

__4. What is the total sales for each region for each month?__
```
SELECT region, 
       month_number, 
       SUM(sales) AS total
FROM clean_weekly_sales
GROUP BY region, month_number
ORDER BY region
```
![image](https://user-images.githubusercontent.com/89729029/135711515-abb4295d-c2e7-4976-82fc-8d401666b7c1.png)

__5. What is the total count of transactions for each platform?__
```
SELECT platform, 
       COUNT(transactions) AS total
FROM clean_weekly_sales
GROUP BY platform
```
![image](https://user-images.githubusercontent.com/89729029/135711596-b9f9cf81-b1ad-40a1-acd0-d79c12fe25b4.png)

__6. What is the percentage of sales for Retail vs Shopify for each month?__
```
WITH sales_platform AS 
(
SELECT platform, 
       month_number, 
       calendar_year, 
       SUM(sales) AS monthly_sales
FROM clean_weekly_sales
GROUP BY platform, 
         month_number, 
         calendar_year
)

SELECT calendar_year, 
       month_number, 
       ROUND(((MAX(CASE WHEN platform='Retail' THEN monthly_sales ELSE null END))/SUM(monthly_sales)),3) AS retail, 
       ROUND(((MAX(CASE WHEN platform='Shopify' THEN monthly_sales ELSE NULL END))/SUM(monthly_sales)),3) AS shopify
FROM sales_platform
GROUP BY calendar_year, 
         month_number
ORDER BY calendar_year, 
         month_number
```
![image](https://user-images.githubusercontent.com/89729029/135712809-7fa5e834-5dcd-4e8a-bb4b-59305653cb4e.png)

__7. What is the percentage of sales by demographic for each year in the dataset?__
```
WITH sales_demo AS 
(
SELECT calendar_year, 
       demographic, 
       SUM(sales) AS yearly_sales
FROM clean_weekly_sales                 
GROUP BY calendar_year, 
         demographic
)
                 
SELECT calendar_year, 
       ROUND(((MAX(CASE WHEN demographic='Couples' THEN yearly_sales ELSE NULL END))/SUM(yearly_sales)),3) AS Couples, 
       ROUND(((MAX(CASE WHEN demographic='Families' THEN yearly_sales ELSE NULL END))/SUM(yearly_sales)),3) AS Families, 
       ROUND(((MAX(CASE WHEN demographic='Unknown' THEN yearly_sales ELSE NULL END))/SUM(yearly_sales)),3) AS Unknown_type 
FROM sales_demo
GROUP BY calendar_year
```
![image](https://user-images.githubusercontent.com/89729029/135738854-b2d64e1d-e1b7-404d-ad63-6d94bdbbebce.png)

__8. Which age_band and demographic values contribute the most to Retail sales?__

                
