__1. What are the top 3 products by total revenue before discount?__
```
SELECT product_name, 
       SUM(qty*s.price) AS gross_revenue
FROM balanced_tree.product_details AS pd
JOIN balanced_tree.sales AS s
ON s.prod_id=pd.product_id
GROUP BY product_name
ORDER BY SUM(qty*s.price) DESC
LIMIT 3
```
![image](https://user-images.githubusercontent.com/89729029/137051210-5cc86e66-930b-483d-aa33-466dc1d0ae72.png)

__2. What is the total quantity, revenue and discount for each segment?__
```
SELECT segment_name, 
       SUM(qty) AS quantity, 
       SUM(qty*s.price*(1-discount/100)) AS net_revenue, 
       SUM(discount*s.price*qty) AS dicount_amount 
FROM balanced_tree.product_details AS d                                  
JOIN balanced_tree.sales AS s
ON s.prod_id=d.product_id
GROUP BY segment_name                                                            
```
![image](https://user-images.githubusercontent.com/89729029/137051673-12e7cb3f-3ae0-4306-b380-fb87346fbd5a.png)

__3. What is the top selling product for each segment?__
```
WITH cte1 AS 
(
SELECT segment_name, 
       product_name, 
       SUM(qty) AS quantity 
FROM balanced_tree.product_details AS p
JOIN balanced_tree.sales AS s
ON p.product_id=s.prod_id
GROUP BY segment_name, 
         product_name
),
cte2 AS 
(
SELECT segment_name, 
       product_name, 
       quantity, 
       DENSE_RANK() OVER(PARTITION BY segment_name ORDER BY quantity DESC) AS product_rank
FROM cte1)

SELECT segment_name, 
       product_name, 
       quantity
FROM cte2
WHERE product_rank=1                     
```
![image](https://user-images.githubusercontent.com/89729029/137053099-0bef0cf7-3fe4-43be-8f36-ba05466184ed.png)
