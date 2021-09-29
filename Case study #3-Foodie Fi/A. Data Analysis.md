__1. How many customers has Foodie-Fi ever had?__
```
SELECT COUNT(DISTINCT customer_id)
FROM foodie_fi.subscriptions 
```
![image](https://user-images.githubusercontent.com/89729029/135205474-dd089479-20ec-4a64-b39a-abe7c0875542.png)

- Foodie-Fi has 1000 unique customers.

__2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value?__
```
SELECT DATE_TRUNC('month',start_date) AS start_dates, 
       COUNT(s.plan_id) AS quantity
FROM foodie_fi.plans AS p
JOIN foodie_fi.subscriptions AS s
ON p.plan_id=s.plan_id
WHERE s.plan_id=0
GROUP BY DATE_TRUNC('month',start_date)
ORDER BY DATE_TRUNC('month',start_date)
```
![image](https://user-images.githubusercontent.com/89729029/135206900-b16b6088-3c93-4893-8d88-9307203a7698.png)


__3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name?__
```
SELECT plan_name,
       COUNT(s.plan_id) AS events
FROM foodie_fi.plans AS p
JOIN foodie_fi.subscriptions AS s
ON p.plan_id=s.plan_id
GROUP BY DATE_PART('year',start_date), plan_name
HAVING DATE_PART('year',start_date) > 2020
ORDER BY COUNT(s.plan_id) DESC
```
![image](https://user-images.githubusercontent.com/89729029/135208307-fe0610aa-2db7-49bc-91a9-da6ade7e2e5d.png)

__4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?__

