__1. What is the total amount each customer spent at the restaurant?__
```
SELECT customer_id, 
       SUM(price) AS total_amount
FROM dannys_diner.sales AS s
JOIN dannys_diner.menu AS m
ON s.product_id=m.product_id
GROUP BY customer_id;
```
![image](https://user-images.githubusercontent.com/89729029/134112187-8a48a8f0-2228-49d6-9f3e-41342dc710b9.png)

- C spends $36
- B spends $74
- A spends $76

__2. How many days has each customer visited the restaurant?__
```
SELECT customer_id, 
       COUNT(distinct order_date) AS days
FROM dannys_diner.sales AS s
JOIN dannys_diner.menu AS m
ON s.product_id=m.product_id
GROUP BY customer_id;
```
![image](https://user-images.githubusercontent.com/89729029/134112257-7925819a-b71a-4b44-ae02-8c9cc34a210e.png)

- A visited the restaurant in 4 days
- B visited the restaurant in 6 days
- C visited the restaurant in 2 days

__3. What was the first item from the menu purchased by each customer?__
```
WITH first_item AS 
(
SELECT customer_id, 
       product_name, 
       DENSE_RANK() OVER(PARTITION BY customer_id ORDER BY order_date) AS ranks
FROM dannys_diner.sales AS s
JOIN dannys_diner.menu AS m
ON s.product_id=m.product_id
)

SELECT *
FROM first_item
WHERE ranks=1;
```
![image](https://user-images.githubusercontent.com/89729029/134112465-5b9caa34-42ae-43cd-aafb-cc4fc665c1e1.png)

- The first item A purchased are curry and sushi
- B purchased curry first
- C purchased ramen first

__4. What is the most purchased item on the menu and how many times was it purchased by all customers?__
```
SELECT product_name, 
       TOP 1 COUNT(s.product_id) AS total_amount
FROM dannys_diner.sales AS s
JOIN dannys_diner.menu AS m
ON s.product_id=m.product_id
GROUP BY product_name
ORDER BY COUNT(s.product_id) DESC;
```
![image](https://user-images.githubusercontent.com/89729029/134112591-19cbbbe2-a4f1-48e9-90a0-a68b6a3c85f1.png)

- Ramen is the most purchased item on the menu and it was purchased 8 times by customers

__5. Which item was the most popular for each customer?__
```
WITH popular_item AS 
(
SELECT customer_id, 
       product_name, 
       DENSE_RANK() OVER(PARTITION BY customer_id ORDER BY COUNT(s.product_id) DESC) as ranks
FROM dannys_diner.sales AS s
JOIN dannys_diner.menu AS m
ON s.product_id=m.product_id
GROUP BY customer_id, product_name
)

SELECT *
FROM popular_item
WHERE ranks=1;
```
![image](https://user-images.githubusercontent.com/89729029/134112694-270e1eaa-3a86-4700-9ace-1e0ccbea2f81.png)

__6. Which item was purchased first by the customer after they became a member?__
```
WITH first_after_member as 
(
SELECT s.customer_id, 
       product_name, 
       DENSE_RANK() OVER(PARTITION BY s.customer_id ORDER BY order_date) AS ranks
FROM dannys_diner.sales AS s
JOIN dannys_diner.menu AS m
ON s.product_id=m.product_id
JOIN dannys_diner.members AS me
ON s.customer_id=me.customer_id
WHERE order_date>=join_date
)

SELECT *
FROM first_after_member
WHERE ranks=1;
```
![image](https://user-images.githubusercontent.com/89729029/134112779-5c3be01f-8f02-491e-a387-7d56a1c94cab.png)

- Curry is the first item that A purchased after becoming a member
- B purchase sushi first after B became a member

__7. Which item was purchased just before the customer became a member?__
```
WITH item_before_member AS 
(
SELECT s.customer_id, 
       product_name, 
       DENSE_RANK() OVER(PARTITION BY s.customer_id ORDER BY order_date DESC) AS ranks
FROM dannys_diner.sales AS s
JOIN dannys_diner.menu AS m
ON s.product_id=m.product_id
JOIN dannys_diner.members AS me
ON s.customer_id=me.customer_id
WHERE order_date < join_date)

SELECT *
FROM item_before_member
WHERE ranks=1;
```
![image](https://user-images.githubusercontent.com/89729029/134112912-de40ce42-8199-4b62-8125-7f87fa192a23.png)

- A purchased sushi and curry just before becoming a member
- B purchased sushi just before becoming a member of the restaurant

__8. What is the total items and amount spent for each member before they became a member?__
```
SELECT s.customer_id, 
       COUNT(DISTINCT s.product_id) AS total_items, 
       SUM(price) AS total_amount
FROM dannys_diner.sales AS s
JOIN dannys_diner.menu AS m
ON s.product_id=m.product_id
JOIN dannys_diner.members AS me
ON s.customer_id=me.customer_id
WHERE order_date<join_date
GROUP BY s.customer_id;
```
![image](https://user-images.githubusercontent.com/89729029/134112912-de40ce42-8199-4b62-8125-7f87fa192a23.png)

- A spends $25 for 2 items (carry and sushi) before becoming a member
- B spends $40 for 2 items (sushi) before becoming a member

__9. If each $1 spent equates to 10 points and sushi has a 20 points multiplier - how many points would each customer have?__
```
WITH bill AS 
(
SELECT product_name, 
       product_id, 
       (CASE WHEN product_id=1 THEN price*10 
       ELSE price*20 END) AS points
FROM dannys_diner.menu
)

SELECT customer_id, 
       SUM(points) AS total_points
FROM bill as b
JOIN dannys_diner.sales AS s
ON b.product_id=s.product_id
GROUP BY customer_id;
```
![image](https://user-images.githubusercontent.com/89729029/134113211-08724a6a-1c33-465f-ad70-dfc043383bfd.png)

- A will has 1420 points, followed by B with 1280 poitns
- C has lowest point with only 720
