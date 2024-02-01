# 🍜 Case Study #1: Danny's Diner 
<img src="https://8weeksqlchallenge.com/images/case-study-designs/1.png" alt="Image" width="400" height="350">

## Introduction

Danny seriously loves Japanese food so in the beginning of 2021, he decides to embark upon a risky venture and opens up a cute little restaurant that sells his 3 favourite foods: sushi, curry and ramen.

Danny’s Diner is in need of your assistance to help the restaurant stay afloat - the restaurant has captured some very basic data from their few months of operation but have no idea how to use their data to help them run the business.

## Problem Statement

Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. Having this deeper connection with his customers will help him deliver a better and more personalised experience for his loyal customers.

He plans on using these insights to help him decide whether he should expand the existing customer loyalty program - additionally he needs help to generate some basic datasets so his team can easily inspect the data without needing to use SQL.

Danny has provided you with a sample of his overall customer data due to privacy issues - but he hopes that these examples are enough for you to write fully functioning SQL queries to help him answer his questions!

## Datasets

Danny has shared with you 3 key datasets for this case study:

#### Sales
The sales table captures all customer_id level purchases with an corresponding order_date and product_id information for when and what menu items were ordered.
| customer_id | order_date | product_id |
|-------------|------------|------------|
| A           | 2021-01-01 | 1          |
| A           | 2021-01-01 | 2          |
| A           | 2021-01-07 | 2          |
| A           | 2021-01-10 | 3          |
| A           | 2021-01-11 | 3          |
| A           | 2021-01-11 | 3          |
| B           | 2021-01-01 | 2          |
| B           | 2021-01-02 | 2          |
| B           | 2021-01-04 | 1          |
| B           | 2021-01-11 | 1          |
| B           | 2021-01-16 | 3          |
| B           | 2021-02-01 | 3          |
| C           | 2021-01-01 | 3          |
| C           | 2021-01-01 | 3          |
| C           | 2021-01-07 | 3          |


#### Menu

The menu table maps the product_id to the actual product_name and price of each menu item.

| product_id | product_name | price |
|------------|--------------|-------|
| 1          | sushi        | 10    |
| 2          | curry        | 15    |
| 3          | ramen        | 12    |


#### Members

The final members table captures the join_date when a customer_id joined the beta version of the Danny’s Diner loyalty program.

| customer_id | join_date  |
|-------------|------------|
| A           | 2021-01-07 |
| B           | 2021-01-09 |


You can inspect the entity relationship diagram and example data below.

![model](https://github.com/spatel228/8-Week-SQL-Challenge/assets/119060595/6045c0cd-b084-4b94-b388-c9e76a2cd77e)


```SQL
CREATE database week1_Danny_Diner;
USE week1_Danny_Diner;
```
```SQL
CREATE TABLE sales
(
    "customer_id" VARCHAR(1),
    "order_date" DATE,
    "product_id" INTEGER
);


INSERT INTO sales
(
    "customer_id",
    "order_date",
    "product_id"
)
VALUES
('A', '2021-01-01', '1'),
('A', '2021-01-01', '2'),
('A', '2021-01-07', '2'),
('A', '2021-01-10', '3'),
('A', '2021-01-11', '3'),
('A', '2021-01-11', '3'),
('B', '2021-01-01', '2'),
('B', '2021-01-02', '2'),
('B', '2021-01-04', '1'),
('B', '2021-01-11', '1'),
('B', '2021-01-16', '3'),
('B', '2021-02-01', '3'),
('C', '2021-01-01', '3'),
('C', '2021-01-01', '3'),
('C', '2021-01-07', '3');
```
``` SQL
CREATE TABLE menu
(
    "product_id" INTEGER,
    "product_name" VARCHAR(5),
    "price" INTEGER
);

INSERT INTO menu
(
    "product_id",
    "product_name",
    "price"
)
VALUES
('1', 'sushi', '10'),
('2', 'curry', '15'),
('3', 'ramen', '12');
```
```SQL
CREATE TABLE members
(
    "customer_id" VARCHAR(1),
    "join_date" DATE
);

INSERT INTO members
(
    "customer_id",
    "join_date"
)
VALUES
('A', '2021-01-07'),
('B', '2021-01-09');
```

### Questions And Solutions

1. **What is the total amount each customer spent at the restaurant?**

``` sql
SELECT 
	s.customer_id as customer_id, 
	SUM(m.price) AS total_sales
FROM sales s
INNER JOIN menu m
	ON s.product_id = m.product_id
GROUP BY s.customer_id
ORDER BY s.customer_id ASC;
```
#### Answer:
| customer_id | total_sales |
| :---------: | :---------: |
| A           | 76          |
| B           | 74          |
| C           | 36          |

2. **How many days has each customer visited the restaurant?**

```sql
SELECT 
  customer_id, 
  COUNT(DISTINCT order_date) AS visit_count
FROM sales
GROUP BY customer_id;
```
| customer_id | visit_count |
| :---------: | :---------: |
| A           | 4           |
| B           | 6           |
| C           | 2           |

3. **What was the first item from the menu purchased by each customer?**

```sql
WITH cte
AS (SELECT s.customer_id,
           m.product_name,
           s.order_date,
           DENSE_RANK() OVER (PARTITION BY s.customer_id ORDER BY order_date ASC) AS rnk
    FROM sales AS s
        INNER JOIN menu AS m
            ON s.product_id = m.product_id
   ),
     distinct_Prod_cte
AS (SELECT DISTINCT
        customer_id,
        product_name
    FROM cte
    WHERE rnk = 1
   )
SELECT customer_id as Customers,
       STRING_AGG(product_name, ', ') AS Products
FROM distinct_Prod_cte
GROUP BY customer_id
```
#### Answer
| Customers | Products |
|:-----------:|:----------|
| A         | curry, sushi |
| B         | curry |
| C         | ramen |

4. **What is the most purchased item on the menu and how many times was it purchased by all customers?**

```sql
SELECT top 1
    m.product_name AS Products,
    COUNT(s.product_id) AS No_of_times_purchased
FROM sales s
    INNER JOIN menu m
        ON s.product_id = m.product_id
GROUP BY m.product_name
ORDER BY No_of_times_purchased DESC
```
#### Answer

| Products | No_of_times_purchased |
|----------|-----------------------|
| ramen    | 8                     |

5. **Which item was the most popular for each customer?**

```sql
WITH cte
AS (SELECT s.customer_id,
           m.product_name,
           COUNT(m.product_id) AS cnt,
           DENSE_RANK() OVER (PARTITION BY s.customer_id ORDER BY COUNT(s.customer_id) DESC) rnk
    FROM sales s
        JOIN menu m
            ON s.product_id = m.product_id
    GROUP BY s.customer_id,
             m.product_name
   )
SELECT customer_id as Customers,
       product_name AS Dishes,
       cnt AS "Order Count"
FROM cte
WHERE rnk = 1;
```
#### Answer

| Customers | Dishes | Order Count |
|-----------|--------|-------------|
| A         | ramen  | 3           |
| B         | sushi  | 2           |
| B         | curry  | 2           |
| B         | ramen  | 2           |
| C         | ramen  | 3           |

6. **Which item was purchased first by the customer after they became a member?**

```sql
WITH cte
AS (SELECT s.customer_id AS customer,
           me.product_name,
           s.order_date,
           m.join_date,
           RANK() OVER (PARTITION BY s.customer_id ORDER BY order_date ASC) rnk
    FROM sales s
        JOIN members m
            ON s.customer_id = m.customer_id
               AND m.join_date < s.order_date
        JOIN menu me
            ON s.product_id = me.product_id
   )
SELECT customer,
       product_name
FROM cte
WHERE rnk = 1
```
#### Answer

| Customer | Product_Name |
|----------|--------------|
| A        | ramen        |
| B        | sushi        |

7. **Which item was purchased just before the customer became a member?**

```sql
WITH cte
AS (SELECT s.customer_id AS customer,
           me.product_name,
           s.order_date,
           m.join_date,
           RANK() OVER (PARTITION BY s.customer_id ORDER BY order_date DESC) rnk
    FROM sales s
        JOIN members m
            ON s.customer_id = m.customer_id
        JOIN menu me
            ON s.product_id = me.product_id
    WHERE join_date > order_date
   )
SELECT customer,
       STRING_AGG(product_name, ',') AS Dishes
FROM cte
WHERE rnk = 1
GROUP BY customer
```
#### Answer

| Customer | Dishes    |
|----------|-----------|
| A        | sushi, curry |
| B        | sushi     |


8. **What is the total items and amount spent for each member before they became a member**

```sql
SELECT s.customer_id,
       COUNT(s.customer_id) AS order_count,
       SUM(price) total_amt_spent
FROM sales s
    JOIN members m
        ON s.customer_id = m.customer_id
           AND order_date < join_date
    JOIN menu me
        ON s.product_id = me.product_id
GROUP BY s.customer_id
```
#### Answer
| Customer | Order_Count | Total_Amt_Spent |
|----------|-------------|-----------------|
| A        | 2           | 25              |
| B        | 3           | 40              |


9. **If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?**

```sql
SELECT customer_id AS Customer,
       SUM(points) Points
FROM
(
    SELECT customer_id,
           CASE
               WHEN product_name = 'sushi' THEN
                   price * 20
               ELSE
                   10 * price
           END AS "points"
    FROM sales s
        JOIN menu me
            ON s.product_id = me.product_id
) a
GROUP BY customer_id
```
#### Answer
| Customer | Points |
|----------|--------|
| A        | 860    |
| B        | 940    |
| C        | 360    |

10. **In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi — how many points do customer A and B have at the end of January?**

```sql
SELECT s.customer_id as customer,
       SUM(   CASE
                  WHEN ME.product_name = 'sushi' THEN
                      price * 20
                  WHEN s.order_date >= m.join_date
                       AND S.order_date <= DATEADD(DAY, 6, M.join_date) THEN
                      price * 20
                  ELSE
                      price * 10
              END
          ) AS points
FROM SALES S
    JOIN MENU ME
        ON S.product_id = ME.product_id
           AND s.order_date <= DATEFROMPARTS(2021, 01, 31)
    JOIN members M
        ON S.customer_id = M.customer_id
GROUP BY s.customer_id
```
#### Answer
| Customer | Points |
|----------|--------|
| A        | 1370    |
| B        | 820   |

