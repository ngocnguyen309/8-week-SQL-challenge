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

