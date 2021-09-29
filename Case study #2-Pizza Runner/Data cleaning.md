### 1. Customer_orders
![image](https://user-images.githubusercontent.com/89729029/134155211-868b46d2-e1fb-49e7-bbd9-e8b8f2cd5ac6.png)

- Replace __NULL__ value in *exclusions* field of *customer_orders* table with ' ' 
- Replace __NULL__ value in *extras* field of *customer_orders* table with ' ' 
```
CREATE TEMP TABLE customer_orders_temp AS
SELECT order_id, 
       customer_id, 
       pizza_id,
       (CASE WHEN exclusions IS NULL OR exclusions LIKE 'null' THEN '' 
       ELSE exclusions END) AS exclusion,
       (CASE WHEN extras IS NULL OR extras LIKE 'null' THEN '' 
       ELSE extras END) AS extra
FROM pizza_runner.customer_orders;
```
![image](https://user-images.githubusercontent.com/89729029/134159476-3b4c78cb-9503-4ab4-86bb-f41ee1e1fc52.png)

### 2. Runner_orders
![image](https://user-images.githubusercontent.com/89729029/134277702-63e40aec-1ddb-4a58-87e9-220c1ba7ef14.png)

- Replace __NULL__ value in *pickup_time* field of *runner_orders* table with ' ' 
- Replace __NULL__ value and __'%km'__ in *distance* field of *runner_orders* table with ' ' 
- Replace __NULL__ value and __'%min%'__ in *duration* field of *runner_orders* table with ' ' 

```
CREATE TEMP TABLE runner_orders_temp AS
SELECT order_id, 
       runner_id, 
       (CASE WHEN pickup_time LIKE 'null' THEN '' ELSE pickup_time END) as pickup_times,
       (CASE WHEN distance LIKE '%km' THEN REPLACE (distance,'km','')
             WHEN distance like 'null' THEN ''
             ELSE distance END) AS distances,
       (CASE WHEN duration LIKE '%min%' THEN REGEXP_REPLACE(duration,'[[:alpha:]]','','g')
             WHEN duration like 'null' then ''
             ELSE duration END) AS durations,
       (CASE WHEN cancellation like 'null' THEN '' ELSE cancellation END) as cancellations        
FROM pizza_runner.runner_orders;
```
![image](https://user-images.githubusercontent.com/89729029/134279761-82a10873-829f-4e8b-9d0c-fb0bc47da847.png)

### 3. Table 5: pizza_recipes
![image](https://user-images.githubusercontent.com/89729029/135016456-78a96197-134d-4445-8b87-3c6903579b19.png)

- __STRING_TO_ARRAY()__: convert a string with delimeter into an array
- __UNNEST()__: includes a row for each element of the specified array
```
SELECT pizza_id,
       UNNEST(STRING_TO_ARRAY(toppings,',') :: INT[]) AS topping_id 
FROM pizza_runner.pizza_recipes;
```
![image](https://user-images.githubusercontent.com/89729029/135016992-f2d1c44d-52f1-4e3d-8026-90d95d60e6ac.png)

