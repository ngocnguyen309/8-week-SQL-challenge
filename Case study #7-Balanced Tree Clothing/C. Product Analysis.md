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

__4. What is the total quantity, revenue and discount for each category?__
```
SELECT category_name, 
       SUM(qty) AS quantity, 
       SUM(qty*s.price*(1-discount/100)) AS net_revenue, 
       SUM(discount*s.price*qty) AS dicount_amount 
FROM balanced_tree.product_details AS d                                  
JOIN balanced_tree.sales AS s
ON s.prod_id=d.product_id
GROUP BY category_name     
```
![image](https://user-images.githubusercontent.com/89729029/137053371-8fca56d9-baea-451a-b33e-beea70693db1.png)

__5. What is the top selling product for each category?__
```
WITH cte1 AS 
(
SELECT category_name, 
       product_name, 
       SUM(qty) AS quantity 
FROM balanced_tree.product_details AS p
JOIN balanced_tree.sales AS s
ON p.product_id=s.prod_id
GROUP BY category_name, 
         product_name
),
cte2 AS 
(
SELECT category_name, 
       product_name, 
       quantity, 
       DENSE_RANK() OVER(PARTITION BY category_name ORDER BY quantity DESC) AS product_rank
FROM cte1)

SELECT category_name, 
       product_name, 
       quantity
FROM cte2
WHERE product_rank=1  
```
![image](https://user-images.githubusercontent.com/89729029/137053645-1bc45a47-c4d4-4f0c-a9d2-ac415188081f.png)

__6. What is the percentage split of revenue by product for each segment?__
```
WITH product_by_segment AS 
(
SELECT segment_name, 
       product_name,
       SUM(qty*s.price*(1-discount/100)) AS net_revenue 
FROM balanced_tree.product_details AS d                                  
JOIN balanced_tree.sales AS s
ON s.prod_id=d.product_id
GROUP BY segment_name, 
         product_name)

SELECT segment_name, 
       product_name, 
       net_revenue, 
       ROUND(((net_revenue::NUMERIC)/SUM(net_revenue) OVER(PARTITION BY segment_name)),2) AS percentage
FROM product_by_segment                  
```
![image](https://user-images.githubusercontent.com/89729029/137054466-c2314a00-a2ed-4cfe-b783-dc637366dbe3.png)
![image](https://user-images.githubusercontent.com/89729029/137054585-66123285-decb-4dfc-a4b4-abd5aa89821a.png)
![image](https://user-images.githubusercontent.com/89729029/137054636-9a498a68-e696-41b9-bb75-8f8a8cc5cef8.png)

__7. What is the percentage split of revenue by segment for each category?__
```
WITH segment_by_category AS 
(
SELECT category_name, 
       segment_name,
       SUM(qty*s.price*(1-discount/100)) AS net_revenue 
FROM balanced_tree.product_details AS d                                  
JOIN balanced_tree.sales AS s
ON s.prod_id=d.product_id
GROUP BY category_name, 
         segment_name)

SELECT category_name, 
       segment_name, 
       net_revenue, 
       ROUND(((net_revenue::NUMERIC)/SUM(net_revenue) OVER(PARTITION BY category_name)),2) AS percentage
FROM segment_by_category                   
```
![image](https://user-images.githubusercontent.com/89729029/137055053-54ae5160-66bf-40ce-ae5b-21f591f9ea8f.png)

__8. What is the percentage split of total revenue by category?__
```
WITH revenue_by_category AS 
(
SELECT category_name, 
       SUM(qty*s.price*(1-discount/100)) AS net_revenue
FROM balanced_tree.product_details AS d                             
JOIN balanced_tree.sales AS s
ON s.prod_id=d.product_id
GROUP BY category_name)
                         
SELECT category_name, 
       net_revenue, 
       ROUND((net_revenue/SUM(net_revenue) OVER()),2) AS percentage
FROM revenue_by_category       
```
![image](https://user-images.githubusercontent.com/89729029/137056989-dc0d7819-4fe3-47f0-af08-73f50e3db7de.png)

__9. What is the total transaction “penetration” for each product? (hint: penetration = number of transactions where at least 1 quantity of a product was purchased divided by total number of transactions)__
```
SELECT product_name, 
       COUNT(txn_id) AS amount
FROM balanced_tree.sales AS s
JOIN balanced_tree.product_details AS d
ON s.prod_id=d.product_id
WHERE qty>=1
GROUP BY product_name
ORDER BY COUNT(txn_id) DESC
```
![image](https://user-images.githubusercontent.com/89729029/137058586-9227975f-1db6-483d-9392-aa5d9abba7fc.png)

__10. What is the most common combination of at least 1 quantity of any 3 products in a 1 single transaction?__
```
updating
```

