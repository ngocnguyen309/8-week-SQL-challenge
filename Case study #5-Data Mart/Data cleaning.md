```
CREATE TEMP TABLE clean_weekly_sales AS
(
SELECT TO_DATE(week_date, 'dd-mm-YY') AS week_day,
       DATE_PART('week', TO_DATE(week_date, 'dd-mm-YY')) AS week_number,
       DATE_PART('month', TO_DATE(week_date, 'dd-mm-YY')) AS month_number,
       DATE_PART('year', TO_DATE(week_date, 'dd-mm-YY')) AS calendar_year,
       segment,
       (CASE WHEN segment LIKE '%1' THEN 'Young Adults'
        WHEN segment LIKE '%2' THEN 'Middle Aged'
        WHEN segment = 'null' THEN 'Unknown' ELSE 'Retirees' END) AS age_band,
       (CASE WHEN  segment LIKE 'C%' THEN 'Couples'
        WHEN segment = 'null' THEN 'Unknown' ELSE 'Families' END) AS demographic,
        ROUND((sales::NUMERIC/transactions),2) AS avg_transaction
FROM data_mart.weekly_sales
)
```
- Convert the __week_date__ to a DATE format

- Add a __week_number__ as the second column for each week_date value, for example any value from the 1st of January to 7th of January will be 1, 8th to 14th will be 2 etc

- Add a __month_number__ with the calendar month for each week_date value as the 3rd column

- Add a __calendar_year__ column as the 4th column containing either 2018, 2019 or 2020 values

- Add a new column called __age_band__ after the original segment column using the following mapping on the number inside the segment value

- Ensure all __null__ string values with an _"unknown"_ string value in the original segment column as well as the new age_band and demographic columns

- Generate a new __avg_transaction__ column as the sales value divided by transactions rounded to 2 decimal places for each record
