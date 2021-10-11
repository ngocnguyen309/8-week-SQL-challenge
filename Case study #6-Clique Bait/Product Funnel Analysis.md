Using a single SQL query - create a new output table which has the following details:

- How many times was each product viewed?
- How many times was each product added to cart?
- How many times was each product added to a cart but not purchased (abandoned)?
- How many times was each product purchased?

__Create table to calculate the number of page view and add to cart by visit_id__
```
SELECT visit_id,
       ph.page_id, 
       page_name, 
       product_category, 
       SUM(CASE WHEN event_type=1 THEN 1 ELSE 0 END) AS page_view, 
       SUM(CASE WHEN event_type=2 THEN 1 ELSE 0 END) AS add_to_cart
FROM clique_bait.events AS e
JOIN clique_bait.page_hierarchy AS ph
ON e.page_id=ph.page_id
WHERE product_category IS NOT NULL
GROUP BY visit_id,
         ph.page_id, 
         page_name, 
         product_category
```
__Next, another table to calculate the number of unique visit_id who purchased the product__
```
SELECT DISTINCT visit_id 
FROM clique_bait.events 
WHERE event_type=3
```
__Then, use LEFT JOIN to define the visitors who viewed page, added to cart and purchased products. The people maybe added the product to cart but didn't purchase any product would be assigned as 0__
```
SELECT v.visit_id, 
       page_id, 
       page_name, 
       product_category, 
       page_view, 
       add_to_cart, 
       (CASE WHEN p.visit_id IS NOT NULL THEN 1 ELSE 0 END) AS purchase
FROM view_and_add AS v
LEFT JOIN purchase_type AS p
ON v.visit_id=p.visit_id
```
__After that, we can define the times that product viewed, added to cart, abandoned and purchased by product category. A product is called 'abandoned' when the vistor add to cart without checking out__ 
```
SELECT page_id, 
       page_name, 
       product_category, 
       SUM(page_view) AS page_viewed, 
       SUM(add_to_cart) AS added_to_cart, 
       SUM(CASE WHEN add_to_cart=1 and purchase=0 THEN 1 ELSE 0 END) AS abandoned, 
       SUM(CASE WHEN add_to_cart =1 and purchase =1 THEN 1 ELSE 0 END) AS purchased
FROM combine
GROUP BY page_id, 
         page_name, 
         product_category

```
![image](https://user-images.githubusercontent.com/89729029/136769226-1b530a98-3434-475f-bdf6-b54991043c41.png)

![image](https://user-images.githubusercontent.com/89729029/136770283-938ce962-132a-4188-937f-6ec4d1271079.png)

__1. Which product had the most views, cart adds and purchases?__
- The most views and cart adds is Shellfish. It is clear that vistors has the most view with Oyster product on our website. However, Lobster is the most popular product which customers add to cart. Lobster is also the most purchased product, followed by Oyster. 

__2. Which product was most likely to be abandoned?__
- On the other hand, customer usually stop checking out with Russian Caviar of Luxury category.

__3. Which product had the highest view to purchase percentage?__
```
SELECT page_name, 
       product_category, 
       ROUND((purchased::NUMERIC/page_viewed),2) AS conversion_rate
FROM percentage
ORDER BY purchased/page_viewed DESC 
```
![image](https://user-images.githubusercontent.com/89729029/136775015-02ec1855-adc6-40ef-94c4-52ea75af8613.png)

- Lobster had the highest view to purchase percentage

