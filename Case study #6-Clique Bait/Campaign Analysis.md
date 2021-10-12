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

```
WITH visit AS 
(
SELECT visit_id, 
       MIN(event_time) AS visit_start_time, 
       SUM(CASE WHEN event_type=1 THEN 1 ELSE 0 END) AS page_views, 
       SUM(CASE WHEN event_type=2 THEN 1 ELSE 0 END) AS cart_add, 
       (CASE WHEN event_type=3 THEN 1 ELSE 0 END) AS purchase, event_type
FROM clique_bait.events
GROUP BY visit_id, event_type)

SELECT visit_id, 
       visit_start_time, 
       page_views, 
       cart_add, 
       purchase, 
       campaign_name
FROM visit AS v
JOIN clique_bait.campaign_identifier AS c
ON visit_start_time >= start_date
AND visit_start_time<=end_date
```
