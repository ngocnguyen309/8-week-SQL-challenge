```
WITH monthly AS 
(
SELECT DATE_PART('month',start_txn_time) AS month_transaction, 
       txn_id
FROM balanced_tree.sales
GROUP BY DATE_PART('month',start_txn_time), 
         txn_id
)

SELECT month_transaction, 
       SUM(qty) AS quantity, 
       SUM(qty*price) AS gross_revenue, 
       SUM(qty*price*discount/100) AS discount_expense
FROM monthly AS m
JOIN balanced_tree.sales AS s
ON m.txn_id=s.txn_id
GROUP BY month_transaction
```
![image](https://user-images.githubusercontent.com/89729029/137060467-5fb93cda-2afc-4405-a923-4afaced6a9b7.png)

