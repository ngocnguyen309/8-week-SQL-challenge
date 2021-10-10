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
