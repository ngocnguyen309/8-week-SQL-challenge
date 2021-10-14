__Which areas of the business have the highest negative impact in sales metrics performance in 2020 for the 12 week before and after period?__

- region
- platform
- age_band
- demographic
- customer_type

__1. region__
```
WITH week_sales AS 
(
SELECT region,
  	   week_number, 
       SUM(sales) AS weekly_sales
FROM clean_weekly_sales                                    
WHERE (week_number between 13 and 36)
AND calendar_year=2020
GROUP BY region, 
         week_number                                   
),                                  
change AS
(                                  
SELECT region,
  	   SUM(CASE WHEN (week_number BETWEEN 13 AND 24) THEN weekly_sales END) AS twelve_weeks_before, 
       SUM(CASE WHEN (week_number BETWEEN 25 AND 36) THEN weekly_sales END) AS twelve_weeks_after 
FROM week_sales
GROUP BY region)                 
                                  
SELECT region,
       twelve_weeks_before, 
       twelve_weeks_after, 
       (twelve_weeks_after-twelve_weeks_before) AS difference,
       ROUND(100*((twelve_weeks_after-twelve_weeks_before)/twelve_weeks_before),2) AS rate
FROM change
GROUP BY region, 
         twelve_weeks_before,
         twelve_weeks_after
ORDER BY ROUND(100*((twelve_weeks_after-twelve_weeks_before)/twelve_weeks_before),2) DESC
```
![image](https://user-images.githubusercontent.com/89729029/136661169-5e820576-9272-46fa-b2c3-c40b2651888d.png)

__2. platform__
```
WITH week_sales AS 
(
SELECT platform,
  	   week_number, 
       SUM(sales) AS weekly_sales
FROM clean_weekly_sales                                    
WHERE (week_number between 13 and 36)
AND calendar_year=2020
GROUP BY platform, 
         week_number                                   
),                                  
change AS
(                                  
SELECT platform,
  	   SUM(CASE WHEN (week_number BETWEEN 13 AND 24) THEN weekly_sales END) AS twelve_weeks_before, 
       SUM(CASE WHEN (week_number BETWEEN 25 AND 36) THEN weekly_sales END) AS twelve_weeks_after 
FROM week_sales
GROUP BY platform)                 
                                  
SELECT platform,
       twelve_weeks_before, 
       twelve_weeks_after, 
       (twelve_weeks_after-twelve_weeks_before) AS difference,
       ROUND(100*((twelve_weeks_after-twelve_weeks_before)/twelve_weeks_before),2) AS rate
FROM change
GROUP BY platform, 
         twelve_weeks_before,
         twelve_weeks_after
ORDER BY ROUND(100*((twelve_weeks_after-twelve_weeks_before)/twelve_weeks_before),2) DESC
```
![image](https://user-images.githubusercontent.com/89729029/136661285-eb0128b5-c2e1-4584-ad59-53c383783868.png)

__3. age_band__
```
WITH week_sales AS 
(
SELECT age_band,
  	   week_number, 
       SUM(sales) AS weekly_sales
FROM clean_weekly_sales                                    
WHERE (week_number between 13 and 36)
AND calendar_year=2020
GROUP BY age_band, 
         week_number                                   
),                                  
change AS
(                                  
SELECT age_band,
  	   SUM(CASE WHEN (week_number BETWEEN 13 AND 24) THEN weekly_sales END) AS twelve_weeks_before, 
       SUM(CASE WHEN (week_number BETWEEN 25 AND 36) THEN weekly_sales END) AS twelve_weeks_after 
FROM week_sales
GROUP BY age_band)                 
                                  
SELECT age_band,
       twelve_weeks_before, 
       twelve_weeks_after, 
       (twelve_weeks_after-twelve_weeks_before) AS difference,
       ROUND(100*((twelve_weeks_after-twelve_weeks_before)/twelve_weeks_before),2) AS rate
FROM change
GROUP BY age_band, 
         twelve_weeks_before,
         twelve_weeks_after
ORDER BY ROUND(100*((twelve_weeks_after-twelve_weeks_before)/twelve_weeks_before),2) DESC
```
![image](https://user-images.githubusercontent.com/89729029/136661430-f0699974-e03f-49d5-a6c8-85505867dbe8.png)

__4. demoraphic__
```
WITH week_sales AS 
(
SELECT demographic,
  	   week_number, 
       SUM(sales) AS weekly_sales
FROM clean_weekly_sales                                    
WHERE (week_number between 13 and 36)
AND calendar_year=2020
GROUP BY demographic, 
         week_number                                   
),                                  
change AS
(                                  
SELECT demographic,
  	   SUM(CASE WHEN (week_number BETWEEN 13 AND 24) THEN weekly_sales END) AS twelve_weeks_before, 
       SUM(CASE WHEN (week_number BETWEEN 25 AND 36) THEN weekly_sales END) AS twelve_weeks_after 
FROM week_sales
GROUP BY demographic)                 
                                  
SELECT demographic,
       twelve_weeks_before, 
       twelve_weeks_after, 
       (twelve_weeks_after-twelve_weeks_before) AS difference,
       ROUND(100*((twelve_weeks_after-twelve_weeks_before)/twelve_weeks_before),2) AS rate
FROM change
GROUP BY demographic, 
         twelve_weeks_before,
         twelve_weeks_after
ORDER BY ROUND(100*((twelve_weeks_after-twelve_weeks_before)/twelve_weeks_before),2) DESC
```
![image](https://user-images.githubusercontent.com/89729029/136661657-4c807d56-8ed6-4e9d-a3d3-1cc29ea27cc4.png)

__5. customer_type__
```
WITH week_sales AS 
(
SELECT customer_type,
  	   week_number, 
       SUM(sales) AS weekly_sales
FROM clean_weekly_sales                                    
WHERE (week_number between 13 and 36)
AND calendar_year=2020
GROUP BY customer_type, week_number                                   
),                                  
change AS
(                                  
SELECT customer_type,
  	   SUM(CASE WHEN (week_number BETWEEN 13 AND 24) THEN weekly_sales END) AS twelve_weeks_before, 
       SUM(CASE WHEN (week_number BETWEEN 25 AND 36) THEN weekly_sales END) AS twelve_weeks_after 
FROM week_sales
GROUP BY customer_type)                 
                                  
SELECT customer_type,
       twelve_weeks_before, 
       twelve_weeks_after, 
       (twelve_weeks_after-twelve_weeks_before) AS difference,
       ROUND(100*((twelve_weeks_after-twelve_weeks_before)/twelve_weeks_before),2) AS rate
FROM change
GROUP BY customer_type, twelve_weeks_before,twelve_weeks_after
ORDER BY ROUND(100*((twelve_weeks_after-twelve_weeks_before)/twelve_weeks_before),2) DESC
```
![image](https://user-images.githubusercontent.com/89729029/136661750-37c5d762-a80a-41a9-ab00-5d40802cf046.png) 
