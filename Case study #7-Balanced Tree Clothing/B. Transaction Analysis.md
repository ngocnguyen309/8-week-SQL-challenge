__1. How many unique transactions were there?__
```
SELECT COUNT(DISTINCT txn_id) AS transactions
FROM balanced_tree.sales 
```
![image](https://user-images.githubusercontent.com/89729029/136971796-4c42ab73-d77d-41f3-bc33-38c5ccdbd3c2.png)

__2. What is the average unique products purchased in each transaction?__
```
WITH trans AS 
(
SELECT txn_id, 
       COUNT(DISTINCT prod_id) AS unique_product
FROM balanced_tree.sales
GROUP BY txn_id
)

SELECT ROUND(AVG(unique_product)::NUMERIC,2) AS average_product
FROM trans
```
![image](https://user-images.githubusercontent.com/89729029/136972435-cfd8ad16-d09a-45cd-9f27-bc44baca352b.png)

- On average, there are 6.04 unique products purchased in each transaction.

__3. What are the 25th, 50th and 75th percentile values for the revenue per transaction?__
```
WITH revenue_by_transaction AS
(
SELECT txn_id, 
       SUM(qty*price*(1-discount/100)) AS revenue
FROM balanced_tree.sales
GROUP BY txn_id
)

SELECT PERCENTILE_DISC(0.25) WITHIN GROUP (ORDER BY revenue) AS percentile25, 
       PERCENTILE_DISC(0.5) WITHIN GROUP (ORDER BY revenue) AS percentile50, 
       PERCENTILE_DISC(0.75) WITHIN GROUP (ORDER BY revenue) AS percentile75
FROM revenue_by_transaction
```
![image](https://user-images.githubusercontent.com/89729029/136978210-ac32e331-3097-4e48-9c03-7c07b9795009.png)

__4. What is the average discount value per transaction?__
```
WITH discount_by_transaction AS
(
SELECT txn_id, 
        SUM(discount*price) AS discount
FROM balanced_tree.sales
GROUP BY txn_id
)

SELECT ROUND(AVG(discount)::NUMERIC, 2)
FROM discount_by_transaction
```
![image](https://user-images.githubusercontent.com/89729029/136974507-3ded6feb-5043-477d-9586-68092f2aada7.png)

__5. What is the percentage split of all transactions for members vs non-members?__
```
WITH trans_by_member AS
(
SELECT member, 
       COUNT(DISTINCT txn_id) AS trans
FROM balanced_tree.sales
GROUP BY member
)

SELECT member, 
       trans, 
       ROUND((trans/SUM(trans) OVER()),2) AS percentage
FROM trans_by_member
GROUP BY member, trans
```
![image](https://user-images.githubusercontent.com/89729029/137067410-1cba9a42-680d-44d7-99cb-2af99922a7f7.png)

__6. What is the average revenue for member transactions and non-member transactions?__
```
SELECT ROUND(AVG(CASE WHEN member='t' THEN qty*price*(1-discount/100) END)::NUMERIC,2) AS be_member, 
       ROUND(AVG(CASE WHEN member='f' THEN qty*price*(1-discount/100) END)::NUMERIC,2) AS non_member
FROM balanced_tree.sales
```
![image](https://user-images.githubusercontent.com/89729029/136978509-8a631dfd-0009-4ae6-b3d1-52574386f4e4.png)


