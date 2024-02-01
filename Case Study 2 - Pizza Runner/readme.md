
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

# <p align="center">**🍝 Solution - B. Runner and Customer Experience**</p>

### 1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)

```sql
SELECT 
  DATEPART(WEEK, registration_date) AS registration_week,
  COUNT(runner_id) AS runner_signup
FROM runners
GROUP BY DATEPART(WEEK, registration_date);

```
#### Answer
| registration_week | runner_signup |
|----------|----------|
| 1        | 1        |
| 2        | 2        |
| 3        | 1        |

### 2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?
```sql
SELECT 
    r.runner_id,
    AVG(DATEDIFF(MINUTE, c.order_time, r.pickup_time)) AS avg_time
FROM 
    customer_orders c 
JOIN 
    runner_orders r 
ON 
    c.order_id = r.order_id AND r.cancellation = ''
GROUP BY 
    r.runner_id;
d

```
#### Answer
| runner_id | avg_time |
|-----------|----------|
| 1         | 15       |
| 2         | 24       |
| 3         | 10       |

### 3. Is there any relationship between the number of pizzas and how long the order takes to prepare?
```sql
WITH cte AS (
    SELECT 
        c.order_id,
        COUNT(pizza_id) AS no_of_pizzas,
        AVG(DATEDIFF(MINUTE, c.order_time, r.pickup_time)) AS avg_time
    FROM 
        customer_orders c 
    JOIN 
        runner_orders r ON c.order_id = r.order_id AND r.cancellation = ''
    GROUP BY 
        c.order_id
)
SELECT 
    no_of_pizzas,
    AVG(avg_time) AS avg_prep_time
FROM 
    cte 
GROUP BY 
    no_of_pizzas;


```
#### Answer
| no_of_pizzas | avg_prep_time |
|--------------|---------------|
| 1            | 12            |
| 2            | 18            |
| 3            | 30            |

1. Orders with a single pizza have the shortest average preparation time of 12 minutes.
2. Larger orders with two pizzas show a slightly higher average preparation time of 18 minutes.
3. Orders with three pizzas exhibit the longest average preparation time, reaching 30 minutes.

### 4. What was the average distance travelled for each customer?
```sql
SELECT 
    c.customer_id,
    AVG(CAST(distance AS FLOAT)) AS avg_distance
FROM 
    customer_orders c 
JOIN 
    runner_orders r ON c.order_id = r.order_id AND r.cancellation = ''
GROUP BY 
    c.customer_id;
```
#### Answer
| customer_id | avg_distance |
|-------------|--------------|
| 101         | 20           |
| 102         | 16.733333        |
| 103         | 23.4         |
| 104         | 10           |
| 105         | 25           |

### 5. What was the difference between the longest and shortest delivery times for all orders?
```sql
SELECT 
    MAX(CAST(duration AS FLOAT)) - MIN(CAST(duration AS FLOAT)) AS time_diff
FROM 
    customer_orders c 
JOIN 
    runner_orders r ON c.order_id = r.order_id AND r.cancellation = '';

```
#### Answer
 |avg_distance |
|:-------------|
|30|

### 6. What was the average speed for each runner for each delivery and do you notice any trend for these values?
```sql
SELECT 
    r.runner_id,
    c.order_id,
    c.customer_id,
    COUNT(c.pizza_id) AS pizzas,
    SUM(CAST(distance AS FLOAT)) * 60 / SUM(CAST(duration AS FLOAT)) AS avg_speed
FROM 
    customer_orders c 
JOIN 
    runner_orders r ON c.order_id = r.order_id AND r.cancellation = '' 
GROUP BY 
    r.runner_id, c.customer_id, c.order_id;

```
#### Answer
| runner_id | order_id | customer_id | pizzas | avg_speed       |
|-----------|----------|-------------|--------|-----------------|
| 1         | 1        | 101         | 1      | 37.5            |
| 1         | 2        | 101         | 1      | 44.44           |
| 1         | 3        | 102         | 2      | 40.2            |
| 1         | 10       | 104         | 2      | 60              |
| 2         | 8        | 102         | 1      | 93.6            |
| 2         | 4        | 103         | 3      | 35.1            |
| 2         | 7        | 105         | 1      | 60              |
| 3         | 5        | 104         | 1      | 40              |

1. Runner 1 has varying average speeds across different deliveries, ranging from 37.5 to 60.
2. Runner 2 shows a wide range of average speeds, from 35.1 to 93.6, indicating significant variability in delivery performance.
3. Runner 3 has a single recorded delivery with an average speed of 40.
4. Runner 2 has a high average speed (93.6) for an order with one pizza (order_id 8).

### 7. What is the successful delivery percentage for each runner?
```sql
SELECT 
    runner_id,
    SUM(CASE WHEN cancellation <> '' THEN 0 ELSE 1 END) AS success,
    COUNT(cancellation) AS total_orders,
    CAST(SUM(CASE WHEN cancellation <> '' THEN 0 ELSE 1 END) AS FLOAT) / COUNT(cancellation) * 100 AS success_percentage
FROM 
    runner_orders
GROUP BY 
    runner_id;

```
#### Answer
| Runner ID | Success Count | Total Orders | Success Percentage |
|-----------|---------------|--------------|---------------------|
| 1         | 4             | 4            | 100%                |
| 2         | 3             | 4            | 75%                 |
| 3         | 1             | 2            | 50%                 |






