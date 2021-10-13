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
GROUP BY month_number
ORDER BY month_number
```
![image](https://user-images.githubusercontent.com/89729029/137061714-bf3bc3f4-997b-449c-a3b9-dcb0e9f30f46.png)

```

```
