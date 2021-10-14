Generate a table that has 1 single row for every unique visit_id record and has the following columns:

- user_id
- visit_id
- visit_start_time: the earliest event_time for each visit
- page_views: count of page views for each visit
- cart_adds: count of product cart add events for each visit
- purchase: 1/0 flag if a purchase event exists for each visit
- campaign_name: map the visit to a campaign if the visit_start_time falls between the start_date and end_date
- impression: count of ad impressions for each visit
- click: count of ad clicks for each visit
- (Optional column) cart_products: a comma separated text value with products added to the cart sorted by the order they were added to the cart (hint: use the sequence_number)

__CTE1__: JOIN __clique_bait.users__ AND __clique_bait.events__ to get _user_id_, _visit_id_ and _MIN(event_time)_ for each visit_id
```
WITH cte1 AS
(
SELECT user_id, 
       visit_id, 
       MIN(event_time) AS visit_start_time
FROM clique_bait.users AS u
JOIN clique_bait.events AS e
ON u.cookie_id=e.cookie_id
GROUP BY user_id, visit_id
),
```
__CTE2__: Calculate the number of views AS _page_views_ and addition to cart AS _cart_adds_ for each visit record.  
```
cte2 AS
(
SELECT user_id, 
       e.visit_id, 
       visit_start_time,
       DATE(visit_start_time) AS date_start, 
       SUM(CASE WHEN event_type=1 THEN 1 ELSE 0 END) AS page_views, 
       SUM(CASE WHEN event_type=2 THEN 1 ELSE 0 END) AS cart_adds
FROM clique_bait.events AS e
JOIN cte1
ON cte1.visit_id=e.visit_id
GROUP BY user_id, 
         e.visit_id, 
         visit_start_time,
         DATE(visit_start_time)
),
```
__CTE3__: Create a table to find out the visitors who purchased at least one product by filter the visit_id has __event_type=3__.
```
cte3 AS 
(
SELECT DISTINCT visit_id
FROM clique_bait.events
WHERE event_type=3
),
```
__CTE4__: JOIN CTE2 AND CTE3 to get 
```
cte4 AS
(                                   
SELECT user_id, 
       c2.visit_id, 
       visit_start_time, 
       date_start, 
       page_views, 
       cart_adds, 
       (CASE WHEN c3.visit_id IS NOT NULL THEN 1 ELSE 0 END) purchase
FROM cte2  AS c2                        
LEFT JOIN cte3  AS c3                   
ON c2.visit_id=c3.visit_id),
```
```
cte5 as 
(        
SELECT visit_id, 
       event_type
FROM clique_bait.events                                   
WHERE event_type=4 
OR event_type=5
),
```
```
cte6 AS (                                   
SELECT user_id, 
       c4.visit_id, 
       date_start, 
       visit_start_time, 
       page_views, 
       cart_adds, 
       purchase, 
       SUM(CASE WHEN event_type=4 THEN 1 ELSE 0 END) AS impression, 
       SUM(CASE WHEN event_type=5 THEN 1 ELSE 0 END) AS click
FROM cte4 AS c4
LEFT JOIN cte5 AS c5
ON c4.visit_id=c5.visit_id
GROUP BY user_id, 
         c4.visit_id, 
         date_start, 
         visit_start_time,
         page_views, 
         cart_adds, 
         purchase)
```
```
SELECT user_id, 
       visit_id, 
       visit_start_time, 
       page_views, 
       cart_adds, 
       purchase, 
       campaign_name, 
       impression, 
       click
FROM cte6 as c6                                   
JOIN clique_bait.campaign_identifier AS c                                 
ON date_start BETWEEN start_date AND end_date 
```



 
