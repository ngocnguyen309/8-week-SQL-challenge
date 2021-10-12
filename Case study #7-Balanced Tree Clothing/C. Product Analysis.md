__1. What are the top 3 products by total revenue before discount?__
```
SELECT product_name, 
       SUM(qty*s.price) AS revenue
FROM balanced_tree.product_details AS pd
JOIN balanced_tree.sales AS s
ON s.prod_id=pd.product_id
GROUP BY product_name
ORDER BY SUM(qty*s.price) DESC
LIMIT 3
```
![image](https://user-images.githubusercontent.com/89729029/136979896-c6467b00-9190-4303-ab35-e48ba9b6ac47.png)

__2. What is the total quantity, revenue and discount for each segment?__
```

```

__3. What is the top selling product for each segment?__
