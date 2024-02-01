
# <p align="center">**🍝 Solution - A. Pizza Metrics**</p>

### 1. How many pizzas were ordered?

```sql
SELECT 
    COUNT(*) AS total_pizza_ordered
FROM 
    customer_orders;
```
#### Answer
| total_pizza_ordered |
|----------------------|
| 14                   |


### 2. How many unique customer orders were made?

```sql
-- Count the number of distinct orders
SELECT 
    COUNT(DISTINCT order_id) AS unk_orders
FROM 
    customer_orders;

```
#### Answer
| unk_orders |
|----------------------|
| 10                   |


### 3. How many successful  orders were delivered by each runner?

```sql
-- Count the number of successful orders for each runner
SELECT 
    runner_id,
    COUNT(runner_id) AS successful_orders
FROM 
    runner_orders
WHERE 
    pickup_time <> ''
GROUP BY 
    runner_id;


```
#### Answer
| runner_id | successful_orders |
|-----------|-------------------|
| 1         | 4                 |
| 2         | 3                 |
| 3         | 1                 |

### 4. How many of each type of pizza was delivered?

```sql
SELECT 
    p.pizza_name Pizza_Name,
    COUNT(p.pizza_id) AS Pizza_count
FROM 
    customer_orders c
    JOIN pizza_names p ON c.pizza_id = p.pizza_id
    JOIN runner_orders ro ON c.order_id = ro.order_id
WHERE 
    cancellation = ''
GROUP BY 
    p.pizza_name;
```
#### Answer
| Pizza_Name  | Pizza_Count |
|-------------|-------------|
| Meatlovers  | 9           |
| Vegetarian  | 3           |

### 5. How many Vegetarian and Meatlovers were ordered by each customer?

```sql
SELECT 
    [customer_id],
    [Meatlovers],
    [Vegetarian]
FROM (
    SELECT 
        c.customer_id,
        p.pizza_name
    FROM 
        customer_orders c
        JOIN pizza_names p ON c.pizza_id = p.pizza_id
) source_
PIVOT (
    COUNT(pizza_name) FOR pizza_name IN ([Meatlovers], [Vegetarian])
) pt;

```
#### Answer
| customer_id | Meatlovers | Vegetarian |
|-------------|------------|------------|
| 101         | 2          | 1          |
| 102         | 2          | 1          |
| 103         | 3          | 1          |
| 104         | 3          | 0          |
| 105         | 0          | 1          |

### 6. What was the maximum number of pizzas delivered in a single order?

```sql
SELECT 
    r.order_id,
    COUNT(r.order_id) AS orders_count
FROM 
    customer_orders c
    JOIN runner_orders AS r ON c.order_id = r.order_id
WHERE 
    r.cancellation = ''
GROUP BY 
    r.order_id;

```
#### Answer

| order_id | orders_count |
|----------|--------------|
| 1        | 1            |
| 2        | 1            |
| 3        | 2            |
| 4        | 3            |
| 5        | 1            |
| 7        | 1            |
| 8        | 1            |
| 10       | 2            |

### 7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?

```sql
SELECT 
    c.customer_id,
    SUM(CASE WHEN c.exclusions = '' AND c.extras = '' THEN 1 ELSE 0 END) AS no_change,
    SUM(CASE WHEN c.exclusions <> '' OR c.extras <> '' THEN 1 ELSE 0 END) AS at_least_one_change
FROM 
    customer_orders c
    JOIN runner_orders AS r ON c.order_id = r.order_id
WHERE 
    r.cancellation = ''
GROUP BY 
    c.customer_id;
```
#### Answer
| customer_id | no_change | at_least_one_change |
|-------------|-----------|---------------------|
| 101         | 2         | 0                   |
| 102         | 3         | 0                   |
| 103         | 0         | 3                   |
| 104         | 1         | 2                   |
| 105         | 0         | 1                   |

### 8. How many pizzas were delivered that had both exclusions and extras?

```sql
SELECT 
    SUM(CASE WHEN c.exclusions <> '' AND c.extras <> '' THEN 1 ELSE 0 END)
    AS cnt_both_exclusions_and_extras
FROM 
    customer_orders c
    JOIN runner_orders AS r 
        ON c.order_id = r.order_id 
        AND r.cancellation = '' 
        AND c.exclusions IS NOT NULL 
        AND c.extras IS NOT NULL;

```
#### Answer
| cnt_both_exclusions_and_extras | 
|:-------------:|
|1|

### 9. What was the total volume of pizzas ordered for each hour of the day?

```sql
SELECT 
    DATEPART(HOUR, order_time) AS Order_hour,
    COUNT(DATEPART(HOUR, order_time)) AS Order_count
FROM 
    customer_orders
GROUP BY 
    DATEPART(HOUR, order_time);

```
#### Answer
| Order_hour | Order_count |
|------------|-------------|
| 11         | 1           |
| 13         | 3           |
| 18         | 3           |
| 19         | 1           |
| 21         | 3           |
| 23         | 3           |

### 10. What was the volume of orders for each day of the week?

```sql
SELECT 
    DATENAME(WEEKDAY, order_time) AS Order_hour,
    COUNT(DATENAME(WEEKDAY, order_time)) AS Order_count
FROM 
    customer_orders
GROUP BY 
    DATENAME(WEEKDAY, order_time);


```
#### Answer
| Order_hour | Order_count |
|------------|-------------|
| Friday     | 1           |
| Saturday   | 5           |
| Thursday   | 3           |
| Wednesday  | 5           |






