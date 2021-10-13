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
       COUNT(DISTINCT m.txn_id) AS transactions,
       SUM(qty) AS quantity, 
       SUM(qty*price) AS gross_revenue, 
       SUM(qty*price*discount/100) AS discount_expense
FROM monthly AS m
JOIN balanced_tree.sales AS s
ON m.txn_id=s.txn_id
GROUP BY month_number
```
![image](https://user-images.githubusercontent.com/89729029/137061513-19140b18-1a92-47ca-90e6-450824d8f594.png)


