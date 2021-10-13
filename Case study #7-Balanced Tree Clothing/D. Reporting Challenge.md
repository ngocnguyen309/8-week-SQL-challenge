```
WITH monthly AS 
(
SELECT DATE_PART('month',start_txn_time) AS month_number, 
       txn_id
FROM balanced_tree.sales
GROUP BY DATE_PART('month',start_txn_time), 
         txn_id
)

SELECT month_number, 
       SUM(qty) AS quantity, 
       SUM(qty*price) AS gross_revenue, 
       SUM(qty*price*discount/100) AS discount_expense
FROM monthly AS m
JOIN balanced_tree.sales AS s
ON m.txn_id=s.txn_id
GROUP BY month_transaction
```
![image](https://user-images.githubusercontent.com/89729029/137060720-047e2c28-9665-4ff2-88ac-387079c03641.png)

```
SELECT month_number, 
       COUNT(DISTINCT m.txn_id)
FROM monthly AS m
GROUP BY month_number
```
![image](https://user-images.githubusercontent.com/89729029/137061246-ce84a953-c0cd-4e0a-8bd6-667fbf365569.png)
