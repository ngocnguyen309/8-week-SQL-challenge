__1. What is the unique count and total amount for each transaction type?__
```
SELECT txn_type AS type_bank, 
       COUNT(*) AS transactions, 
       SUM(txn_amount) AS amount
FROM data_bank.customer_transactions
GROUP BY txn_type
ORDER BY count(*)
```
![image](https://user-images.githubusercontent.com/89729029/134110905-7bff9bbb-7ab8-4419-952d-ef2699cce995.png)

- Deposit type has the largest number of transactions and total value

__2. What is the average total historical deposit counts and amounts for all customers?__
```
WITH average_total AS
(
SELECT customer_id, 
       txn_type AS type_bank, 
       COUNT(*) AS transactions, 
       AVG(txn_amount) AS amount
FROM data_bank.customer_transactions
GROUP BY customer_id, txn_type
)

SELECT AVG(transactions) AS avg_trans,
       AVG(amount) AS avg_value
FROM average_total
WHERE type_bank='deposit'
```
![image](https://user-images.githubusercontent.com/89729029/134110982-20321644-198b-4a8f-b29a-85efcfaa8825.png)

- The average total historical deposit count is 5.3
- Average total historical deposit amount is 508.6

__3. For each month - how many Data Bank customers make more than 1 deposit and either 1 purchase or 1 withdrawal in a single month?__
```
WITH da as 
(
SELECT DATE_PART('month',txn_date) AS months, 
       customer_id, 
       SUM(CASE WHEN txn_type='deposit' THEN 1 ELSE 0 END) AS deposit, 
  	SUM(CASE WHEN txn_type='purchase' THEN 1 ELSE 0 END) AS purchase, 
  	SUM(CASE WHEN txn_type='withdrawal' THEN 1 ELSE 0 END) AS withdrawal
FROM data_bank.customer_transactions
GROUP BY DATE_PART('month',txn_date), customer_id
)

SELECT months, COUNT(distinct customer_id) AS customer_count
FROM da
WHERE deposit>1 
AND (purchase>1 OR withdrawal>1)
GROUP BY months
```
![image](https://user-images.githubusercontent.com/89729029/134111060-0336b908-5ed0-47a7-9002-b1bb36410221.png)

__4. What is the closing balance for each customer at the end of the month?__
```
WITH monthly_change AS 
(
SELECT customer_id, 
       txn_type,
       (DATE_TRUNC('month',txn_date) + '1 month - 1 day'::INTERVAL) AS end_of_month,
       SUM (CASE WHEN txn_type ='deposit' THEN txn_amount ELSE (-txn_amount) END) AS amount
FROM data_bank.customer_transactions
GROUP BY customer_id, 
         txn_type,
         DATE_TRUNC('month',txn_date) + '1 month - 1 day'::INTERVAL
),
ending_month AS
(SELECT DISTINCT customer_id,
        ('2020-01-31'::DATE + GENERATE_SERIES(0,3)*'1 MONTH'::interval) AS end_month
FROM data_bank.customer_transactions
),
closing AS
(
SELECT e.customer_id, 
       end_month, 
       COALESCE(amount,0) AS monthly_changes, 
       SUM(amount) OVER(PARTITION BY e.customer_id ORDER BY end_month ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS closing_balance
FROM ending_month as e
LEFT JOIN monthly_change as m
on m.end_of_month=e.end_month
and m.customer_id=e.customer_id
),
solution AS 
(
SELECT customer_id,
       end_month,
       monthly_changes,
       closing_balance,
       ROW_NUMBER() OVER (PARTITION BY customer_id, end_month ORDER BY end_month) AS record_no
FROM closing
),
solution1 AS 
(
SELECT customer_id,
       end_month, 
       monthly_changes, 
       closing_balance,
       LEAD(record_no) OVER (PARTITION BY customer_id, end_month ORDER BY end_month) AS lead_no
FROM solution
)
SELECT customer_id, 
       end_month,
       monthly_changes, 
       closing_balance
FROM solution1
WHERE lead_no IS NULL
```
**monthly_changes**

![image](https://user-images.githubusercontent.com/89729029/134111363-a66c64e7-f261-4227-92ce-7e3705c8a92e.png)
**ending_month**

![image](https://user-images.githubusercontent.com/89729029/134111443-71207bd5-42ea-4033-aa31-b5718672eb68.png)

**closing_balance**
![image](https://user-images.githubusercontent.com/89729029/134111643-a2c09c39-7351-403e-9245-1fc6199310b0.png)

**solution**
![image](https://user-images.githubusercontent.com/89729029/134111723-79bdd3f4-ed12-4f8e-abaf-2d9bdd3e3974.png)

**solution1**
![image](https://user-images.githubusercontent.com/89729029/134111836-c28c196d-865a-4002-9070-1a902ac3b0f6.png)

**answer**
![image](https://user-images.githubusercontent.com/89729029/134111250-1c038e65-fb1c-44a9-b305-3f40a9955aae.png)

__5. What is the percentage of customers who increase their closing balance by more than 5%?__

```
WITH monthly_change AS 
(
SELECT customer_id, 
       txn_type,
       (DATE_TRUNC('month',txn_date) + '1 month - 1 day'::INTERVAL) AS end_of_month,
       SUM (CASE WHEN txn_type ='deposit' THEN txn_amount ELSE (-txn_amount) END) AS amount
FROM data_bank.customer_transactions
GROUP BY customer_id, 
         txn_type,
         DATE_TRUNC('month',txn_date) + '1 month - 1 day'::INTERVAL
),
ending_month AS
(SELECT DISTINCT customer_id,
        ('2020-01-31'::DATE + GENERATE_SERIES(0,3)*'1 MONTH'::interval) AS end_month
FROM data_bank.customer_transactions
),
closing AS
(
SELECT e.customer_id, 
       end_month, 
       COALESCE(amount,0) AS monthly_changes, 
       SUM(amount) OVER(PARTITION BY e.customer_id ORDER BY end_month ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS closing_balance
FROM ending_month as e
LEFT JOIN monthly_change as m
on m.end_of_month=e.end_month
and m.customer_id=e.customer_id
),
solution AS 
(
SELECT customer_id,
       end_month,
       monthly_changes,
       closing_balance,
       ROW_NUMBER() OVER (PARTITION BY customer_id, end_month ORDER BY end_month) AS record_no
FROM closing
),
solution1 AS 
(
SELECT customer_id,
       end_month, 
       monthly_changes, 
       closing_balance,
       LEAD(record_no) OVER (PARTITION BY customer_id, end_month ORDER BY end_month) AS lead_no
FROM solution
),
question4 AS
(
SELECT customer_id, 
       end_month,
       monthly_changes, 
       closing_balance
FROM solution1
WHERE lead_no IS NULL
),
question5 AS 
(
SELECT customer_id,
       end_month,
       closing_balance,
       LEAD(closing_balance) OVER(PARTITION BY customer_id ORDER BY end_month) AS next_balance
FROM question4
),
balance_changes AS 
(
SELECT customer_id, 
	end_month, 
       ROUND((next_balance-closing_balance)/closing_balance,2) as changes
FROM question5
WHERE (next_balance IS NOT NULL)
AND closing_balance <> 0
AND end_month='2020-01-31'
GROUP BY customer_id, 
         end_month, 
         next_balance, 
         closing_balance
HAVING ROUND((next_balance-closing_balance)/closing_balance,2) > 5
)

SELECT 100* COUNT(customer_id)::NUMERIC/(SELECT COUNT(DISTINCT customer_id) FROM question5 WHERE end_month='2020-01-31')
FROM balance_changes
```
- 7% of customers increase their closing balance by more than 5% in the following month
