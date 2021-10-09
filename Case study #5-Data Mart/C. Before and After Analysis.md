Taking the week_date value of 2020-06-15 as the baseline week where the Data Mart sustainable packaging changes came into effect.

__1. What is the total sales for the 4 weeks before and after 2020-06-15? What is the growth or reduction rate in actual values and percentage of sales?__
```
SELECT distinct week_number
FROM clean_weekly_sales                 
WHERE week_day='2020-06-15'    

WITH week_sales AS (SELECT week_number, sum(sales) as weekly_sales
FROM clean_weekly_sales                                    
GROUP BY week_number                                   
HAVING week_number between 21 and 28),                                  
change AS(                                  
SELECT SUM(CASE WHEN (week_number between 21 and 24) then weekly_sales end) AS four_weeks_before, SUM(CASE WHEN (week_number between 25 and 28) then weekly_sales end) AS four_weeks_after 
FROM week_sales)                 
                                  
SELECT four_weeks_before, four_weeks_after, (four_weeks_after-four_weeks_before)/four_weeks_before as rate
FROM change   
```
