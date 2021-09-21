__1 How many pizzas were ordered?__
```
SELECT COUNT(pizza_id) AS total
FROM pizza_runner.customer_orders;
```
![image](https://user-images.githubusercontent.com/89729029/134149131-1f67d5a1-77ae-4198-ab77-033c9402c9a7.png)

- There are 14 pizzas ordered

__2 How many unique customer orders were made?__
```
SELECT COUNT(DISTINCT order_id) AS total_order
FROM pizza_runner.customer_orders;
```
![image](https://user-images.githubusercontent.com/89729029/134149920-477aceaf-a9d7-4706-89fa-8395743511ed.png)

- There are 10 unique orders made

__3 How many successful orders were delivered by each runner?__
```
SELECT runner_id, 
       COUNT(*) AS total_order
FROM pizza_runner.runner_orders
WHERE distance IS NOT NULL
GROUP BY runner_id;
```

- id_1 has 4 sucessful orders
- id_2 has 3 sucessful orders 
- id_3 has only 1 sucessful order

--4 How many of each type of pizza was delivered?
SELECT pizza_name, 
       COUNT (p.pizza_id)
FROM pizza_runner.runner_orders AS r
JOIN pizza_runner.customer_orders AS c
ON r.order_id=c.order_id
JOIN pizza_runner.pizza_names AS p
ON p.pizza_id=c.pizza_id
WHERE distance IS NOT NULL
GROUP BY pizza_name;
--9 "MeatLovers" pizza and 3 "Vegetatrian" pizza were delivered sucessfully to customers

--5 How many Vegetarian and Meatlovers were ordered by each customer?
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
--customer_id 101 ordered 2 "Meatlovers" pizza and 1 "Vegetarian" pizza
--customer_id 102 ordered 2 "Meatlovers" pizza  and 1 "Vegetarian" pizza
--customer_id 103 ordered 3 "Meatlovers" pizza  and 1 "Vegetarian" pizza
--customer_id 104 ordered 3 "Meatlovers" pizza 
--customer_id 105 order 1 "Vegetarian" pizza

--6 What was the maximum number of pizzas delivered in a single order?
SELECT r.order_id, 
       COUNT (pizza_id) as number_pizzas
FROM pizza_runner.runner_orders AS r
JOIN pizza_runner.customer_orders AS c
ON r.order_id=c.order_id
WHERE distance IS NOT NULL
GROUP BY r.order_id
ORDER BY COUNT (pizza_id) DESC;
--The maximum number of pizzas delivered in a single order are 3 pizzas

--7 For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
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

customer_id	no_changes	had_1_changes
101	            2	           0
102	            3	           0
103	            0	           3
104	            1	           2
105	            0	           1

--8 How many pizzas were delivered that had both exclusions and extras?
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
--Only order #10 of customer_id 103 had both exclusions and extras

--9 What was the total volume of pizzas ordered for each hour of the day?
SELECT DATE_PART('hour',order_time) AS hours, 
       COUNT(*) AS number_pizzas
FROM pizza_runner.customer_orders
GROUP BY DATE_PART('hour',order_time);
-- Highest volume of pizza ordered is at 13, 18 and 21 
-- Lowest volume of pizza ordered is at 11, 19 and 23 

--10. What was the volume of orders for each day of the week?
SELECT DATE_PART('day',order_time) AS days, 
       COUNT(DISTINCT order_id) AS number_pizzas
FROM pizza_runner.customer_orders
GROUP BY DATE_PART('day',order_time)
ORDER BY DATE_PART('day',order_time);
