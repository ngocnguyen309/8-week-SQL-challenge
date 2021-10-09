Taking the week_date value of 2020-06-15 as the baseline week where the Data Mart sustainable packaging changes came into effect.

__1. What is the total sales for the 4 weeks before and after 2020-06-15? What is the growth or reduction rate in actual values and percentage of sales?__
```
SELECT distinct week_number
FROM clean_weekly_sales                 
WHERE week_day='2020-06-15'    

WITH week_sales AS 
(
SELECT week_number, 
       SUM(sales) AS weekly_sales
FROM clean_weekly_sales                                    
WHERE (week_number BETWEEN 21 AND 28)
AND calendar_year=2020
GROUP BY calendar_year,
         week_number                                   
),                                  
change AS
(                                  
SELECT SUM(CASE WHEN (week_number BETWEEN 21 AND 24) THEN weekly_sales END) AS four_weeks_before, 
       SUM(CASE WHEN (week_number BETWEEN 25 AND 28) THEN weekly_sales END) AS four_weeks_after 
FROM week_sales
)                 
                                  
SELECT four_weeks_before, 
       four_weeks_after, 
       (four_weeks_after-four_weeks_before) AS difference,
       ROUND(100*((four_weeks_after-four_weeks_before)/four_weeks_before),2) AS rate
FROM change     
```
![image](https://user-images.githubusercontent.com/89729029/136660748-74ecb214-e012-4734-ac19-7918ac0ecbf0.png)

- After lauching the program, there was an slight decreasing in sales compared to 4 weeks before.

__2. What about the entire 12 weeks before and after?__
```
WITH week_sales AS 
(
SELECT week_number, 
       SUM(sales) AS weekly_sales
FROM clean_weekly_sales                                    
WHERE (week_number BETWEEN 21 AND 28)
AND calendar_year=2020
GROUP BY calendar_year,
         week_number 
),                                  
change AS
(                                  
SELECT SUM(CASE WHEN (week_number BETWEEN 13 AND 24) THEN weekly_sales END) AS twelve_weeks_before, 
       SUM(CASE WHEN (week_number BETWEEN 25 AND 36) THEN weekly_sales END) AS twelve_weeks_after 
FROM week_sales
)                 
                                  
SELECT twelve_weeks_before, 
       twelve_weeks_after, 
       (twelve_weeks_after-twelve_weeks_before) AS difference,
       ROUND(100*((twelve_weeks_after-twelve_weeks_before)/twelve_weeks_before),2) AS rate
FROM change                                    
```
![image](https://user-images.githubusercontent.com/89729029/136660823-84012ea8-9f33-4b5e-bb3b-8b1bed4b61a0.png)

__3. How do the sale metrics for these 2 periods before and after compare with the previous years in 2018 and 2019?__
```
WITH week_sales AS 
(
SELECT calendar_year,
  	week_number, 
       SUM(sales) AS weekly_sales
FROM clean_weekly_sales                                    
GROUP BY calendar_year, week_number                                   
HAVING week_number BETWEEN 21 AND 28
),                                  
change AS
(                                  
SELECT calendar_year, SUM(CASE WHEN (week_number BETWEEN 21 AND 24) THEN weekly_sales END) AS four_weeks_before, 
       SUM(CASE WHEN (week_number BETWEEN 25 AND 28) THEN weekly_sales END) AS four_weeks_after 
FROM week_sales
GROUP BY calendar_year
)                 
                                  
SELECT calendar_year,
       four_weeks_before, 
       four_weeks_after, 
       (four_weeks_after-four_weeks_before) AS difference,
       ROUND(100*((four_weeks_after-four_weeks_before)/four_weeks_before),2) AS rate
FROM change
GROUP BY calendar_year, 
         four_weeks_before, 
         four_weeks_after
```
![image](https://user-images.githubusercontent.com/89729029/136660620-126e3cc2-56e8-4cb6-93b3-5bdf0f639dc7.png)

```
WITH week_sales AS 
(
SELECT calendar_year,
  	   week_number, 
       SUM(sales) AS weekly_sales
FROM clean_weekly_sales                                    
WHERE (week_number between 13 and 36)
GROUP BY calendar_year, week_number                                   
),                                  
change AS
(                                  
SELECT calendar_year,
  	   SUM(CASE WHEN (week_number BETWEEN 13 AND 24) THEN weekly_sales END) AS twelve_weeks_before, 
       SUM(CASE WHEN (week_number BETWEEN 25 AND 36) THEN weekly_sales END) AS twelve_weeks_after 
FROM week_sales
GROUP BY calendar_year)                 
                                  
SELECT calendar_year,
       twelve_weeks_before, 
       twelve_weeks_after, 
       (twelve_weeks_after-twelve_weeks_before) AS difference,
       ROUND(100*((twelve_weeks_after-twelve_weeks_before)/twelve_weeks_before),2) AS rate
FROM change
```
![image](https://user-images.githubusercontent.com/89729029/136660956-bf9aa188-ffe0-476b-8277-a80930a02984.png)
