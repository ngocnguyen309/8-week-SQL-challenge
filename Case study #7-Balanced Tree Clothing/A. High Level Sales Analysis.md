__1. What was the total quantity sold for all products?__
```
SELECT SUM(qty) AS quantity
FROM balanced_tree.sales 
```
![image](https://user-images.githubusercontent.com/89729029/136970800-086938cd-54cc-47f6-a301-c1c54f3da8ff.png)

__2. What is the total generated revenue for all products before discounts?__
```
SELECT SUM(qty*price) AS revenue
FROM balanced_tree.sales
```
![image](https://user-images.githubusercontent.com/89729029/136971168-76cb8b52-da2d-46d8-82bf-3c857a0f7c1c.png)

__3. What was the total discount amount for all products?__
```
SELECT SUM(price*discount/100) AS total_discount
FROM balanced_tree.sales 
```
![image](https://user-images.githubusercontent.com/89729029/136971452-0b499b25-d90c-4bbe-b87e-ff96375e3b36.png)
