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

```
