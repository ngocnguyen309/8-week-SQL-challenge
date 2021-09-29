__1. How many customers has Foodie-Fi ever had?__
```
SELECT COUNT(DISTINCT customer_id)
FROM foodie_fi.subscriptions 
```
![image](https://user-images.githubusercontent.com/89729029/135205474-dd089479-20ec-4a64-b39a-abe7c0875542.png)

- Foodie-Fi has 1000 unique customers.

__2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value?__
