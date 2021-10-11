Using a single SQL query - create a new output table which has the following details:

- How many times was each product viewed?
- How many times was each product added to cart?
- How many times was each product added to a cart but not purchased (abandoned)?
- How many times was each product purchased?
```
SELECT ph.page_id, 
       page_name, 
       product_category, 
       SUM(CASE WHEN event_type=1 THEN 1 ELSE 0 END) AS page_view, 
       SUM(CASE WHEN event_type=2 THEN 1 ELSE 0 END) AS add_to_cart
FROM clique_bait.events AS e
JOIN clique_bait.page_hierarchy AS ph
ON e.page_id=ph.page_id
WHERE product_category is not null
GROUP BY ph.page_id, page_name, product_category
```
