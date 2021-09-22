__1. How many runners signed up for each 1 week period?__
```
SELECT DATE_PART('week', registration_date) AS week,
       DATE_PART('isoyear', registration_date) AS year, 
       COUNT(runner_id) AS total_runners
FROM pizza_runner.runners
GROUP BY DATE_PART('week', registration_date), DATE_PART('isoyear', registration_date)
ORDER BY DATE_PART('isoyear', registration_date)
```
![image](https://user-images.githubusercontent.com/89729029/134192354-bcb4f0e0-c032-4d4d-a2e1-20e385bfda0f.png)

__2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?__
```
WITH average_time AS 
(
SELECT runner_id, 
       CAST(pickup_time AS timestamp) AS pickup_times, 
       CAST(order_time AS timestamp) AS order_times
FROM pizza_runner.runner_orders AS r
JOIN pizza_runner.customer_orders AS c
ON r.order_id=c.order_id
WHERE distance <> ''
AND pickup_time not like 'null'
)
SELECT AVG(EXTRACT(minute FROM (pickup_times-order_times))) AS minutes
FROM average_time
```
![image](https://user-images.githubusercontent.com/89729029/134194460-8282cc1e-942c-413c-acd0-eb24e4700a08.png)

__3. Is there any relationship between the number of pizzas and how long the order takes to prepare?__
```
WITH pizza_and_preparation AS
(
SELECT c.order_id, 
	   count(pizza_id) as number_pizza, 
       order_time,
       pickup_time,
       EXTRACT (minute FROM (CAST(pickup_time AS timestamp)-order_time)) AS minutes
FROM pizza_runner.customer_orders AS c
JOIN pizza_runner.runner_orders AS r
ON c.order_id=r.order_id
WHERE pickup_time not like 'null'
GROUP BY c.order_id,
       order_time,
       pickup_time)
SELECT number_pizza,
       AVG(minutes)
FROM pizza_and_preparation
GROUP BY number_pizza
``` 
![image](https://user-images.githubusercontent.com/89729029/134276662-fd216a52-d6a3-4f04-98f0-581441d3ba37.png)

- On average, a pizza takes 12 minutes to prepare.
- It takes 18 minutes to prepare 2 pizzas, which means 9 minutes per pizza.

__4. What was the average distance travelled for each customer?__
```
SELECT customer_id, 
       AVG(CAST(distances AS float)) AS avg_distance
FROM runner_orders_temp AS r
JOIN customer_orders_temp AS c
ON r.order_id=c.order_id
WHERE pickup_times <>''
GROUP BY customer_id
```
![image](https://user-images.githubusercontent.com/89729029/134282535-785cfdb2-c3aa-4dac-8c26-e7515a323fc5.png)

__5. What was the difference between the longest and shortest delivery times for all orders?__
```
SELECT max(delivery_time)-min(delivery_time) AS difference
FROM (
SELECT r.order_id, 
       EXTRACT (minute FROM (CAST(pickup_times AS timestamp)-order_time)) AS delivery_time
FROM runner_orders_temp AS r
JOIN customer_orders_temp AS c
ON r.order_id=c.order_id
WHERE pickup_times <>''
GROUP BY r.order_id, pickup_times, order_time) AS sub                          
```
![image](https://user-images.githubusercontent.com/89729029/134283353-ab1d8329-93a5-477f-b0f5-94461beb1131.png)

__6. What was the average speed for each runner for each delivery and do you notice any trend for these values?__
```
SELECT r.runner_id, 
       r.order_id,
       COUNT(pizza_id) AS total_pizza,
       CAST(distances AS float)/CAST(durations AS float)*60 AS speed
FROM runner_orders_temp AS r
JOIN customer_orders_temp AS c
ON r.order_id=c.order_id
WHERE pickup_times <>''
GROUP BY r.runner_id, 
	 r.order_id, 
	 distances, 
	 durations
ORDER BY r.runner_id, 
	 CAST(distances AS float)/CAST(durations AS float)
```
![image](https://user-images.githubusercontent.com/89729029/134284900-a1c47bd4-7adb-4231-a75e-6db758386d34.png)
- Average speed of runner 1 is from 37.5 km/h to 60 km/h
- Average speed of runner 2 is from 35.09 km/h to 93.6 km/h
- Average speed of runner 3 is 40 km/h

__7. What is the successful delivery percentage for each runner?__
```
SELECT r.runner_id, 
       CAST(SUM(CASE WHEN pickup_times='' THEN 0 ELSE 1 END) AS FLOAT)/COUNT(DISTINCT order_id) AS percentage_delivery      
FROM runner_orders_temp AS r
GROUP BY r.runner_id;
```
![image](https://user-images.githubusercontent.com/89729029/134286161-3cfc703c-b19e-4639-99dc-007ee6509078.png)
