__Join All The Things__
```
SELECT m.customer_id, 
       order_date, 
       product_name, 
       price, 
       (CASE WHEN order_date>=join_date THEN 'Y' 
       ELSE 'N' END) AS member
FROM dannys_diner.sales AS s
JOIN dannys_diner.menu AS me
ON s.product_id=me.product_id
JOIN dannys_diner.members AS m
ON m.customer_id=s.customer_id
ORDER BY m.customer_id, order_date;
```
![image](https://user-images.githubusercontent.com/89729029/134113442-f33eb2a9-1f75-4171-9bb9-925a32e2e3bc.png)


__Rank all things__
```
WITH rank_all_thing AS 
(
SELECT m.customer_id, 
       order_date, 
       product_name, 
       price, 
       (CASE WHEN order_date>=join_date THEN 'Y' ELSE 'N' END) AS member
FROM dannys_diner.sales AS s
JOIN dannys_diner.menu AS me
ON s.product_id=me.product_id
JOIN dannys_diner.members AS m
ON m.customer_id=s.customer_id
ORDER BY m.customer_id, order_date)

SELECT *, 
      (CASE WHEN member='Y' THEN DENSE_RANK() OVER(PARTITION BY customer_id, member ORDER BY order_date) 
      ELSE null END) AS rank
FROM rank_all_thing;
```
![image](https://user-images.githubusercontent.com/89729029/134113512-5c952e17-6d0e-4392-83c8-a99c2e30ea48.png)
