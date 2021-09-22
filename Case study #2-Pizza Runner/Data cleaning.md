### Customer_orders
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

### Runner_orders
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
