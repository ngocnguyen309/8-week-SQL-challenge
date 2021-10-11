Using a single SQL query - create a new output table which has the following details:

- How many times was each product viewed?
- How many times was each product added to cart?
- How many times was each product added to a cart but not purchased (abandoned)?
- How many times was each product purchased?

__number of view and add to cart by visit_id__
```
SELECT ph.page_id, 
       page_name, 
       product_category, 
       SUM(CASE WHEN event_type=1 THEN 1 ELSE 0 END) AS page_view, 
       SUM(CASE WHEN event_type=2 THEN 1 ELSE 0 END) AS add_to_cart
FROM clique_bait.events AS e
JOIN clique_bait.page_hierarchy AS ph
ON e.page_id=ph.page_id
WHERE product_category IS NOT NULL
GROUP BY ph.page_id, page_name, product_category
```
__number of visit_id purchased__
```
SELECT DISTINCT visit_id 
FROM clique_bait.events 
WHERE event_type=3
```
__number of view, add to cart and purchase by visit_id__
```
SELECT v.visit_id, 
       page_id, 
       page_name, 
       product_category, 
       page_view, 
       add_to_cart, 
       (CASE WHEN v.visit_id IS NOT NULL THEN 1 ELSE 0 END) AS purchase
FROM view_and_add AS v
LEFT JOIN purchase_type AS p
ON v.visit_id=p.visit_id
```
