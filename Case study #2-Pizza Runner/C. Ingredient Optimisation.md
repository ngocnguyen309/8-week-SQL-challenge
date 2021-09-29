__1. What are the standard ingredients for each pizza?__
```
SELECT pr.pizza_id, 
       topping_name
FROM pizza_recipe AS pr
JOIN pizza_runner.pizza_toppings AS pt
ON pt.topping_id=pr.topping_id
ORDER BY pr.pizza_id
```
![image](https://user-images.githubusercontent.com/89729029/135018375-12e29027-3ee9-482b-a6df-8818a6b34eb6.png)

__2. What was the most commonly added extra?__
```
WITH temp_tab AS
(
SELECT customer_id, 
       pizza_id, 
       UNNEST(STRING_TO_ARRAY(exclusion,',') :: INT[]) AS exclusion,
       UNNEST(STRING_TO_ARRAY(extra,',') :: INT[]) AS extra 
FROM customer_orders_temp
)

SELECT topping_name, 
       COUNT(extra)
FROM temp_tab AS t
JOIN pizza_runner.pizza_toppings AS pt
ON t.extra=pt.topping_id
GROUP BY topping_name
ORDER BY COUNT(extra) DESC
```
![image](https://user-images.githubusercontent.com/89729029/135020307-549030c6-cc31-40b3-813a-40b5b019a21c.png)
- Bacon is the most commonly added extra.

__3. What was the most common exclusion?__
```
SELECT topping_name, 
       COUNT(exclusion)
FROM temp_tab AS t
JOIN pizza_runner.pizza_toppings AS pt
ON t.exclusion=pt.topping_id
GROUP BY topping_name
ORDER BY COUNT(exclusion) DESC
```
![image](https://user-images.githubusercontent.com/89729029/135022636-f1507568-6781-499a-bf32-de8670d9541e.png)
- Cheese is the most common exclusion. Maybe, customer don't like this topping.

__4. Generate an order item for each record in the customers_orders table__
