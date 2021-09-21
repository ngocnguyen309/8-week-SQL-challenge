__1. How many unique nodes are there on the Data Bank system?__
```
SELECT COUNT(distinct node_id) 
FROM data_bank.customer_nodes; 
```
![image](https://user-images.githubusercontent.com/89729029/134109554-353db48f-a07a-4b65-ac8c-45fef3ab31bb.png)
- There are 5 unique nodes on the Data Bank system

__2. What is the number of nodes per region?__
```
SELECT r.region_id, 
       region_name, 
       COUNT(node_id) AS total_node
FROM data_bank.customer_nodes AS c
JOIN data_bank.regions AS r
ON c.region_id=r.region_id
GROUP BY r.region_id,region_name;
```
![image](https://user-images.githubusercontent.com/89729029/134109909-38b49494-14b8-4fce-a80d-0538bc2cfa92.png)

__3. How many customers are allocated to each region?__
```
SELECT region_name,
       r.region_id, 
       COUNT(distinct customer_id) 
FROM data_bank.customer_nodes as c
JOIN data_bank.regions as r
ON c.region_id=r.region_id
GROUP BY region_name,r.region_id
ORDER BY COUNT(distinct customer_id) DESC;
```
![image](https://user-images.githubusercontent.com/89729029/134110034-aa864e75-9463-47e1-93ff-58676fbf899b.png)

- Australia has the highest number of unique users
- Europe has the smallest unique users

__4. How many days on average are customers reallocated to a different node?__
```
WITH node as 
(
SELECT customer_id, 
       node_id, 
       start_date, 
       end_date,
       end_date - start_date AS diff
FROM data_bank.customer_nodes
WHERE end_date<>'9999-12-31'
GROUP BY customer_id, node_id, start_date, end_date
ORDER BY customer_id, node_id
),
    sum_diff as 
(
SELECT customer_id, 
       node_id, 
       SUM(diff) AS total_diff
FROM node
GROUP BY customer_id, node_id
)

SELECT AVG(total_diff) AS avg_day
FROM sum_diff
```
![image](https://user-images.githubusercontent.com/89729029/134110729-694203a3-f077-497d-b247-170609a78133.png)

- On average, customers are reallocated to a different node every 23.56 days

__--5. What is the median, 80th and 95th percentile for this same reallocation days metric for each region?__
> updating...
