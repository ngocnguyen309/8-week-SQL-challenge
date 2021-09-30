__1. How many customers has Foodie-Fi ever had?__
```
SELECT COUNT(DISTINCT customer_id)
FROM foodie_fi.subscriptions 
```
![image](https://user-images.githubusercontent.com/89729029/135205474-dd089479-20ec-4a64-b39a-abe7c0875542.png)

- Foodie-Fi has 1000 unique customers.

__2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value?__
```
SELECT TO_CHAR(start_date,'Mon') AS month,
       COUNT(s.plan_id) AS quantity
FROM foodie_fi.plans AS p
JOIN foodie_fi.subscriptions AS s
ON p.plan_id=s.plan_id
WHERE s.plan_id=0
GROUP BY DATE_TRUNC('month',start_date), TO_CHAR(start_date,'Mon')
ORDER BY DATE_TRUNC('month',start_date)
```
![image](https://user-images.githubusercontent.com/89729029/135274163-01637fda-f17c-4553-bde9-c97407464255.png)

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
```
SELECT COUNT(s.customer_id) AS customer_churn,
       ROUND((COUNT(s.customer_id) :: NUMERIC/(SELECT COUNT(distinct customer_id) FROM foodie_fi.subscriptions)),1) AS churn_rate
FROM foodie_fi.plans AS p
JOIN foodie_fi.subscriptions AS s
ON p.plan_id=s.plan_id
WHERE s.plan_id=4
```
![image](https://user-images.githubusercontent.com/89729029/135276323-dc488364-675b-4eeb-9475-a320d95982ef.png)

__5. How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?__
```
WITH churn_trial AS
(
SELECT customer_id,
       plan_id,
       ROW_NUMBER() OVER(PARTITION BY customer_id ORDER BY plan_id) AS rank_number
FROM foodie_fi.subscriptions)

SELECT COUNT(customer_id) AS churn_after_trial,
       ROUND((100*COUNT(customer_id) :: NUMERIC/(SELECT COUNT(DISTINCT customer_id) FROM churn_trial))) AS churn_rate
FROM churn_trial AS c
JOIN foodie_fi.plans AS p
ON c.plan_id=p.plan_id
WHERE c.plan_id=4
AND rank_number=2
```
![image](https://user-images.githubusercontent.com/89729029/135281540-106a672c-f5f5-4e7a-860e-228790f6b0c4.png)

__6. What is the number and percentage of customer plans after their initial free trial?__
```
WITH after_trial AS
(
SELECT customer_id,
       plan_id,
       LEAD(plan_id) OVER(PARTITION BY customer_id ORDER BY plan_id) AS next_plan
FROM foodie_fi.subscriptions
)

SELECT next_plan, 
       COUNT(customer_id) AS customer,
       ROUND((100*COUNT(customer_id):: NUMERIC/(SELECT COUNT(DISTINCT customer_id) FROM foodie_fi.subscriptions)),3) AS percentage
FROM after_trial
GROUP BY next_plan
HAVING next_plan IS NOT NULL
ORDER BY next_plan
```
![image](https://user-images.githubusercontent.com/89729029/135293748-928d8581-b6b5-47c6-abb8-916b032a3905.png)

__7. What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?__
```
WITH after_trial AS
(
SELECT customer_id,
       plan_id,
  	start_date
FROM foodie_fi.subscriptions
)

SELECT a.plan_id, 
	   plan_name, 
       COUNT(customer_id) AS customer,
       ROUND((100*COUNT(customer_id)::NUMERIC/(SELECT COUNT(DISTINCT customer_id) FROM after_trial)),2) AS percentage
FROM after_trial AS a
JOIN foodie_fi.plans AS p
ON a.plan_id=p.plan_id
WHERE start_date<='2020-12-31'
GROUP BY a.plan_id, plan_name
ORDER BY a.plan_id
```
![image](https://user-images.githubusercontent.com/89729029/135293422-1f8008d8-1623-4c5f-81b0-d41e6765c798.png)

__8. How many customers have upgraded to an annual plan in 2020?__
```
SELECT COUNT(DISTINCT customer_id)
FROM foodie_fi.subscriptions
WHERE DATE_PART('year',start_date)=2020
AND plan_id=3
```
![image](https://user-images.githubusercontent.com/89729029/135295399-704433fb-14fe-4f27-8e8e-9d7288cf3840.png)

__9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?__
```
WITH trial AS
(
SELECT customer_id, 
       plan_id, 
       start_date
FROM foodie_fi.subscriptions
WHERE plan_id=0),
annual AS
(
SELECT customer_id, 
       plan_id, 
       start_date AS annual_date
FROM foodie_fi.subscriptions
WHERE plan_id=3)

SELECT ROUND(AVG(annual_date-start_date),2) AS average_day
FROM trial AS t
JOIN annual AS a
ON t.customer_id=a.customer_id

```
![image](https://user-images.githubusercontent.com/89729029/135372960-434ab62f-c153-4fac-bf8c-5853b1774a87.png)

__10. Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)__
