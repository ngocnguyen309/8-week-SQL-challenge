__1. How many runners signed up for each 1 week period?__
```
SELECT DATEPART(week, CAST(registration_date AS date)) AS weeks, 
       COUNT(runner_id) AS total
FROM runners
GROUP BY DATEPART(week, CAST(registration_date AS date))
```

__2 What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?__
```
WITH average_time AS 
(
SELECT runner_id, 
       CAST(pickup_time AS datetime) AS pickup_times, 
       CAST(order_time AS datetime) AS order_times
FROM runner_orders AS r
JOIN customer_orders AS c
ON r.order_id=c.order_id
WHERE distance <> ''
)
SELECT runner_id, 
       AVG(DATEDIFF(minute, order_times, pickup_times)) AS mintues
FROM average_time
GROUP BY runner_id
```

__3 Is there any relationship between the number of pizzas and how long the order takes to prepare?__
```
WITH da AS
(
SELECT r.order_id, 
       COUNT(pizza_id) AS number_pizzas, 
       CAST(pickup_time AS datetime) AS pickup_times, cast(order_time as datetime) as order_times
FROM runner_orders AS r
JOIN customer_orders AS c
ON r.order_id=c.order_id
WHERE distance <> ''
GROUP BY r.order_id, 
         pickup_time, 
         order_time)
SELECT number_pizzas, 
       AVG(DATEDIFF(minute, order_times, pickup_times)) AS minutes
FROM da
GROUP BY number_pizzas
``` 

__4 What was the average distance travelled for each customer?__
```
SELECT customer_id, 
       ROUND(AVG(CAST(distance AS float)),2) AS avg_distance
FROM runner_orders AS r
JOIN customer_orders AS c
ON r.order_id=c.order_id
GROUP BY customer_id
```

__5 What was the difference between the longest and shortest delivery times for all orders?__
```
SELECT MAX(cast(duration as float))-min(cast(duration as float)) as dif_delivery_times
FROM runner_orders
```

__6. What was the average speed for each runner for each delivery and do you notice any trend for these values?__
```
SELECT runner_id, 
       avg(cast(distance as float)/cast (duration as float)) as average_speed
FROM runner_orders
WHERE duration<>''
GROUP BY runner_id
ORDER BY avg(cast(distance as float)/cast (duration as float)) desc
```
__7 What is the successful delivery percentage for each runner?__
```
SELECT runner_id, 
       CAST(SUM(CASE WHEN duration ='' THEN 0 ELSE 1 END) AS float)/CAST(COUNT(order_id) AS float)*100 AS percentage
FROM runner_orders
GROUP BY runner_id
```
