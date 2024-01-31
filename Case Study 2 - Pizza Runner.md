# Case Study 2 - Pizza Runner 🍕
<img src="https://github.com/spatel228/8-Week-SQL-Challenge/assets/119060595/7606ecea-c53a-4080-9bec-d7e416610d78" alt="Image" width = "400" height= "350">



## Introduction

Did you know that over 115 million kilograms of pizza is consumed daily worldwide??? (Well according to Wikipedia anyway…)

Danny was scrolling through his Instagram feed when something really caught his eye - “80s Retro Styling and Pizza Is The Future!”

Danny was sold on the idea, but he knew that pizza alone was not going to help him get seed funding to expand his new Pizza Empire - so he had one more genius idea to combine with it - he was going to Uberize it - and so Pizza Runner was launched!

Danny started by recruiting “runners” to deliver fresh pizza from Pizza Runner Headquarters (otherwise known as Danny’s house) and also maxed out his credit card to pay freelance developers to build a mobile app to accept orders from customers.

## Available Data
Because Danny had a few years of experience as a data scientist - he was very aware that data collection was going to be critical for his business’ growth.

He has prepared for us an entity relationship diagram of his database design but requires further assistance to clean his data and apply some basic calculations so he can better direct his runners and optimise Pizza Runner’s operations.

All datasets exist within the pizza_runner database schema - be sure to include this reference within your SQL scripts as you start exploring the data and answering the case study questions.

## Entity Relationship Diagram

<img height = 300 width = 550 src = "https://github.com/spatel228/8-Week-SQL-Challenge/assets/119060595/fad866ce-c80a-4ca8-a389-09b709311fa5">

### Table 1: runners
The runners table shows the registration_date for each new runner

| runner_id | registration_date |
|-----------|-------------------|
| 1         | 2021-01-01        |
| 2         | 2021-01-03        |
| 3         | 2021-01-08        |
| 4         | 2021-01-15        |

### Table 2: customer_orders

Customer pizza orders are captured in the customer_orders table with 1 row for each individual pizza that is part of the order.

The pizza_id relates to the type of pizza which was ordered whilst the exclusions are the ingredient_id values which should be removed from the pizza and the extras are the ingredient_id values which need to be added to the pizza.

Note that customers can order multiple pizzas in a single order with varying exclusions and extras values even if the pizza is the same type!

The exclusions and extras columns will need to be cleaned up before using them in your queries.
| order_id | customer_id | pizza_id | exclusions | extras | order_time |
|----------|-------------|----------|------------|--------|------------|
| 1        | 101         | 1        |            |        | 2021-01-01 18:05:02 |
| 2        | 101         | 1        |            |        | 2021-01-01 19:00:52 |
| 3        | 102         | 1        |            |        | 2021-01-02 23:51:23 |
| 3        | 102         | 2        |            | NaN    | 2021-01-02 23:51:23 |
| 4        | 103         | 1        | 4          |        | 2021-01-04 13:23:46 |
| 4        | 103         | 1        | 4          |        | 2021-01-04 13:23:46 |
| 4        | 103         | 2        | 4          |        | 2021-01-04 13:23:46 |
| 5        | 104         | 1        | null       | 1      | 2021-01-08 21:00:29 |
| 6        | 101         | 2        | null       | null   | 2021-01-08 21:03:13 |
| 7        | 105         | 2        | null       | 1      | 2021-01-08 21:20:29 |
| 8        | 102         | 1        | null       | null   | 2021-01-09 23:54:33 |
| 9        | 103         | 1        | 4          | 1, 5   | 2021-01-10 11:22:59 |
| 10       | 104         | 1        | null       | null   | 2021-01-11 18:34:49 |
| 10       | 104         | 1        | 2, 6       | 1, 4   | 2021-01-11 18:34:49 |

### Table 3: runner_orders
After each orders are received through the system - they are assigned to a runner - however not all orders are fully completed and can be cancelled by the restaurant or the customer.

The pickup_time is the timestamp at which the runner arrives at the Pizza Runner headquarters to pick up the freshly cooked pizzas. The distance and duration fields are related to how far and long the runner had to travel to deliver the order to the respective customer.

There are some known data issues with this table so be careful when using this in your queries - make sure to check the data types for each column in the schema SQL!

| order_id | runner_id | pickup_time       | distance | duration   | cancellation           |
|----------|-----------|-------------------|----------|------------|------------------------|
| 1        | 1         | 2021-01-01 18:15:34 | 20km     | 32 minutes |                        |
| 2        | 1         | 2021-01-01 19:10:54 | 20km     | 27 minutes |                        |
| 3        | 1         | 2021-01-03 00:12:37 | 13.4km   | 20 mins    | NaN                    |
| 4        | 2         | 2021-01-04 13:53:03 | 23.4     | 40         | NaN                    |
| 5        | 3         | 2021-01-08 21:10:57 | 10       | 15         | NaN                    |
| 6        | 3         | null              | null     | null       | Restaurant Cancellation|
| 7        | 2         | 2020-01-08 21:30:45 | 25km     | 25mins     | null                   |
| 8        | 2         | 2020-01-10 00:15:02 | 23.4 km  | 15 minute  | null                   |
| 9        | 2         | null              | null     | null       | Customer Cancellation  |
| 10       | 1         | 2020-01-11 18:50:20 | 10km     | 10minutes  | null                   |

### Table 4: pizza_names

At the moment - Pizza Runner only has 2 pizzas available the Meat Lovers or Vegetarian!

| pizza_id | pizza_name |
|----------|------------|
| 1        | Meat Lovers |
| 2        | Vegetarian |

### Table 5: pizza_recipes
Each pizza_id has a standard set of toppings which are used as part of the pizza recipe.
| pizza_id | toppings         |
|----------|------------------|
| 1        | 1, 2, 3, 4, 5, 6, 8, 10 |
| 2        | 4, 6, 7, 9, 11, 12 |

### Table 6: pizza_toppings
This table contains all of the topping_name values with their corresponding topping_id value
| topping_id | topping_name |
|------------|--------------|
| 1          | Bacon        |
| 2          | BBQ Sauce    |
| 3          | Beef         |
| 4          | Cheese       |
| 5          | Chicken      |
| 6          | Mushrooms    |
| 7          | Onions       |
| 8          | Pepperoni    |
| 9          | Peppers      |
| 10         | Salami       |
| 11         | Tomatoes     |
| 12         | Tomato Sauce |

### Data Cleaning & Transformation
#### Table: customer_orders

```sql
-- Update exclusions to an empty string where exclusions are 'null'
UPDATE customer_orders 
SET exclusions = ''
WHERE exclusions = 'null';

-- Update extras to an empty string where extras are 'null' or null
UPDATE customer_orders 
SET extras = ''
WHERE extras = 'null' OR extras IS NULL;
```

#### Table: runner_orders

```sql
BEGIN TRANSACTION;

-- Update pickup_time: Set to empty string where pickup_time is 'null'
-- Update distance: Remove 'km' or set to empty string if 'null'
-- Update duration: Remove 'minutes' or 'null', and replace spaces
-- Update cancellation: Set to empty string where cancellation is 'null' or null
UPDATE runner_orders
SET 
    pickup_time = CASE WHEN pickup_time = 'null' THEN '' ELSE pickup_time END,
    distance = 
        CASE 
            WHEN distance LIKE '%km%' THEN REPLACE(distance, 'km', '')
            WHEN distance = 'null' THEN ''
            ELSE distance
        END,
    duration = REPLACE(TRANSLATE(duration, 'minutesnull', '           '), ' ', ''),
    cancellation = CASE WHEN cancellation IS NULL OR cancellation = 'null' THEN '' ELSE cancellation END;

COMMIT;
```
### Transformed table
#### customer_orders
| order_id | customer_id | pizza_id | exclusions | extras | order_time                 |
|----------|-------------|----------|------------|--------|----------------------------|
| 1        | 101         | 1        |            |        | 2020-01-01 18:05:02.000    |
| 2        | 101         | 1        |            |        | 2020-01-01 19:00:52.000    |
| 3        | 102         | 1        |            |        | 2020-01-02 23:51:23.000    |
| 3        | 102         | 2        |            |        | 2020-01-02 23:51:23.000    |
| 4        | 103         | 1        | 4          |        | 2020-01-04 13:23:46.000    |
| 4        | 103         | 1        | 4          |        | 2020-01-04 13:23:46.000    |
| 4        | 103         | 2        | 4          |        | 2020-01-04 13:23:46.000    |
| 5        | 104         | 1        |            | 1      | 2020-01-08 21:00:29.000    |
| 6        | 101         | 2        |            |        | 2020-01-08 21:03:13.000    |
| 7        | 105         | 2        |            | 1      | 2020-01-08 21:20:29.000    |
| 8        | 102         | 1        |            |        | 2020-01-09 23:54:33.000    |
| 9        | 103         | 1        | 4          | 1, 5   | 2020-01-10 11:22:59.000    |
| 10       | 104         | 1        |            |        | 2020-01-11 18:34:49.000    |
| 10       | 104         | 1        | 2, 6       | 1, 4   | 2020-01-11 18:34:49.000    |

#### runner_orders

| order_id | runner_id | pickup_time           | distance | duration | cancellation            |
|----------|-----------|-----------------------|----------|----------|--------------------------|
| 1        | 1         | 2020-01-01 18:15:34   | 20       | 32       |                          |
| 2        | 1         | 2020-01-01 19:10:54   | 20       | 27       |                          |
| 3        | 1         | 2020-01-03 00:12:37   | 13.4     | 20       |                          |
| 4        | 2         | 2020-01-04 13:53:03   | 23.4     | 40       |                          |
| 5        | 3         | 2020-01-08 21:10:57   | 10       | 15       |                          |
| 6        | 3         |                       |          |          | Restaurant Cancellation |
| 7        | 2         | 2020-01-08 21:30:45   | 25       | 25       |                          |
| 8        | 2         | 2020-01-10 00:15:02   | 23.4     | 15       |                          |
| 9        | 2         |                       |          |          | Customer Cancellation   |
| 10       | 1         | 2020-01-11 18:50:20   | 10       | 10       |                          |

#### all other tables no change


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






