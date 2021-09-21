__1. How many pizzas were ordered?__
```
SELECT COUNT(pizza_id) AS total
FROM pizza_runner.customer_orders;
```
![image](https://user-images.githubusercontent.com/89729029/134149131-1f67d5a1-77ae-4198-ab77-033c9402c9a7.png)

- There are 14 pizzas ordered

__2. How many unique customer orders were made?__
```
SELECT COUNT(DISTINCT order_id) AS total_order
FROM pizza_runner.customer_orders;
```
![image](https://user-images.githubusercontent.com/89729029/134149920-477aceaf-a9d7-4706-89fa-8395743511ed.png)

- There are 10 unique orders made

__3. How many successful orders were delivered by each runner?__
```
SELECT runner_id, 
       COUNT(*) AS total_order
FROM pizza_runner.runner_orders
WHERE distance IS NOT NULL
GROUP BY runner_id;
```
![image](https://user-images.githubusercontent.com/89729029/134181501-f8539de6-24ad-44ff-a08d-98fb408a38f8.png)

- id_1 has 4 sucessful orders
- id_2 has 3 sucessful orders 
- id_3 has only 1 sucessful order

__4. How many of each type of pizza was delivered?__
```
SELECT pizza_name, 
       COUNT (p.pizza_id)
FROM pizza_runner.runner_orders AS r
JOIN pizza_runner.customer_orders AS c
ON r.order_id=c.order_id
JOIN pizza_runner.pizza_names AS p
ON p.pizza_id=c.pizza_id
WHERE distance IS NOT NULL
GROUP BY pizza_name;
```
![image](https://user-images.githubusercontent.com/89729029/134181763-cce25ba3-487c-4807-9180-4dae635409d9.png)

- 9 "MeatLovers" pizza and 3 "Vegetatrian" pizza were delivered sucessfully to customers

__5. How many Vegetarian and Meatlovers were ordered by each customer?__
```
SELECT customer_id, 
       SUM (CASE WHEN c.pizza_id=1 THEN 1 ELSE 0 END) AS Meat_Lovers, 
       SUM (CASE WHEN c.pizza_id=2 THEN 1 ELSE 0 END) AS Vegetarian
FROM pizza_runner.runner_orders AS r
JOIN pizza_runner.customer_orders AS c
ON r.order_id=c.order_id
JOIN pizza_runner.pizza_names AS p
ON p.pizza_id=c.pizza_id
GROUP BY customer_id
ORDER BY customer_id;
```
![image](https://user-images.githubusercontent.com/89729029/134182007-8a5518b4-6829-49e2-8ec5-175d381a5bb0.png)

__6. What was the maximum number of pizzas delivered in a single order?__
```
SELECT r.order_id, 
       COUNT (pizza_id) as number_pizzas
FROM pizza_runner.runner_orders AS r
JOIN pizza_runner.customer_orders AS c
ON r.order_id=c.order_id
WHERE distance IS NOT NULL
GROUP BY r.order_id
ORDER BY COUNT (pizza_id) DESC;
```
![image](https://user-images.githubusercontent.com/89729029/134182396-d33b3c5a-3c2b-4032-8930-8905cc247cb8.png)

- The maximum number of pizzas delivered in a single order are 3 pizzas

__7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?__
```
WITH pizza_change AS 
(
SELECT customer_id, 
       pizza_id, 
       r.order_id, 
       COALESCE(exclusions,'') AS exclusions_after, 
       COALESCE(extras,'') AS extras_after
FROM pizza_runner.runner_orders AS r
JOIN pizza_runner.customer_orders AS c
ON r.order_id=c.order_id
WHERE distance IS NOT NULL
)

SELECT customer_id, 
       order_id, 
       SUM(CASE WHEN exclusions_after='' AND extras_after='' THEN 1 
       ELSE 0 END) AS no_changes, 
       SUM(CASE WHEN exclusions_after <>'' OR extras_after <>''THEN 1 
       ELSE 0 END) AS had_atleast_1
FROM pizza_change
GROUP BY customer_id, order_id
ORDER BY customer_id;
```
![image](https://user-images.githubusercontent.com/89729029/134182640-6a0a0770-921b-446f-84c6-5a3118efebba.png)

__8. How many pizzas were delivered that had both exclusions and extras?__
```
WITH pizza_both AS 
(
SELECT customer_id, 
       pizza_id, 
       r.order_id, 
       COALESCE(exclusions,'') AS exclusions_after, 
       COALESCE(extras,'') AS extras_after
FROM pizza_runner.runner_orders AS r
JOIN pizza_runner.customer_orders AS c
ON r.order_id=c.order_id
WHERE distance IS NOT NULL
)

SELECT customer_id, 
       order_id, 
       COUNT(DISTINCT pizza_id) AS both
FROM pizza_both
WHERE (exclusions_after <> '' 
AND extras_after<>'')
GROUP BY customer_id, order_id;
```
![image](https://user-images.githubusercontent.com/89729029/134182890-4b7fcf17-5c8a-488a-aa44-32b852445ce4.png)

- Only order #10 of customer_id 103 had both exclusions and extras

__9. What was the total volume of pizzas ordered for each hour of the day?__
```
SELECT DATE_PART('hour',order_time) AS hours, 
       COUNT(*) AS number_pizzas
FROM pizza_runner.customer_orders
GROUP BY DATE_PART('hour',order_time);
```
![image](https://user-images.githubusercontent.com/89729029/134183026-48c9574a-2998-43d4-afe4-f1e050869750.png)

- Highest volume of pizza ordered is at 13, 18 and 21 
- Lowest volume of pizza ordered is at 11, 19 and 23 

__10. What was the volume of orders for each day of the week?__
```
SELECT DATE_PART('day',order_time) AS days, 
       COUNT(DISTINCT order_id) AS number_pizzas
FROM pizza_runner.customer_orders
GROUP BY DATE_PART('day',order_time)
ORDER BY DATE_PART('day',order_time);
```
![image](https://user-images.githubusercontent.com/89729029/134183425-42f9e490-c667-49d6-89f8-2de2b44e450b.png)

