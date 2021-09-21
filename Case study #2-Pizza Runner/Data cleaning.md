![image](https://user-images.githubusercontent.com/89729029/134155211-868b46d2-e1fb-49e7-bbd9-e8b8f2cd5ac6.png)
Replace __NULL__ value in *exclusions* field of *customer_orders* table with ''
```
CREATE TEMP TABLE customer_orders_temp AS
SELECT order_id, 
       customer_id, 
       pizza_id, 
		   (CASE WHEN exclusions IS NULL OR exclusions LIKE 'null' THEN '' 
       ELSE exclusions END) AS exclusion,
       (CASE WHEN extras LIKE 'null' THEN '' 
       ELSE extras END) AS extra
FROM pizza_runner.customer_orders;
```
