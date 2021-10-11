__1. How many users are there?__
```
SELECT COUNT(DISTINCT user_id) 
FROM clique_bait.users
```
![image](https://user-images.githubusercontent.com/89729029/136698537-6f17dbfb-e32f-4531-83ba-1f8188275c01.png)

- There are 500 users.

__2. How many cookies does each user have on average?__
```
WITH cookie AS
(
SELECT user_id, 
       COUNT(cookie_id) AS number_cookie
FROM clique_bait.users
GROUP BY user_id
)

SELECT AVG(number_cookie)::FLOAT AS average_cookie
FROM cookie
```
![image](https://user-images.githubusercontent.com/89729029/136698706-6b4fac7d-87c2-45b2-be46-94e407644b32.png)

- On average, each user has 3.564 cookies.

__3. What is the unique number of visits by all users per month?__
```
SELECT  DATE_PART('month', event_time) AS month_visit,
        COUNT(DISTINCT visit_id) AS number_visit
FROM clique_bait.events
GROUP BY DATE_PART('month', event_time)
```
![image](https://user-images.githubusercontent.com/89729029/136698969-948b853f-b2d2-416c-99e4-6576cb6db2ed.png)

__4. What is the number of events for each event type?__
```
SELECT event_name, 
       COUNT(e.event_type) AS number_events
FROM clique_bait.events AS e
JOIN clique_bait.event_identifier AS ei
ON e.event_type = ei.event_type
GROUP BY event_name
```
![image](https://user-images.githubusercontent.com/89729029/136699156-d3fec074-bc02-40a8-abdc-da0b0f37c583.png)

__5. What is the percentage of visits which have a purchase event?__
```
WITH events AS
(
SELECT event_name, 
       COUNT(DISTINCT visit_id) AS number_events
FROM clique_bait.events AS e
JOIN clique_bait.event_identifier AS ei
ON e.event_type = ei.event_type
GROUP BY event_name
)

SELECT 100*(number_events::FLOAT)/ (SELECT COUNT(DISTINCT visit_id) FROM clique_bait.events) AS percentage
FROM events
WHERE event_name='Purchase'
```
![image](https://user-images.githubusercontent.com/89729029/136723523-accc35a3-1685-4adc-addd-1a36234a71f9.png)

__6. What is the percentage of visits which view the checkout page but do not have a purchase event?__
```
SELECT ROUND(((COUNT(DISTINCT visit_id)::NUMERIC)/(SELECT COUNT(DISTINCT visit_id) FROM clique_bait.events WHERE event_type <> 3)),2) AS checkout_not_purchase
FROM clique_bait.events 
WHERE page_id=12
AND event_type=1
```
![image](https://user-images.githubusercontent.com/89729029/136723266-5d139741-4689-43b7-bbd0-81d5dd970b4d.png)

__7. What are the top 3 pages by number of views?__
```
SELECT page_name, 
       COUNT(e.event_type)
FROM clique_bait.events AS e
JOIN clique_bait.page_hierarchy AS p
ON e.page_id=p.page_id
WHERE event_type=1
GROUP BY page_name
ORDER BY COUNT(e.event_type) DESC
```
![image](https://user-images.githubusercontent.com/89729029/136723918-aa97a205-f2bd-4048-b010-590aa7965469.png)

__8. What is the number of views and cart adds for each product category?__
```
SELECT product_category, 
       SUM(CASE WHEN e.event_type =1 THEN 1 ELSE 0 END) AS number_view,
       SUM(CASE WHEN e.event_type=2 THEN 1 ELSE 0 END) AS add_to_cart
FROM clique_bait.events AS e
JOIN clique_bait.page_hierarchy AS p
ON e.page_id=p.page_id
WHERE product_category IS NOT NULL
GROUP BY product_category
```
![image](https://user-images.githubusercontent.com/89729029/136724275-ab355999-98d0-48e1-8d10-acac67be9071.png)

__9. What are the top 3 products by purchases?__
```
updating
```
