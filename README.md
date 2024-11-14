# Pizza Sales Analysis SQL Project

## Project Overview

**Project Title**: Pizza Sales Analysis    
**Database**: `pizzahouse`

## Overview

This project is designed to demonstrate SQL skills and techniques typically used by data analysts to explore, clean, and analyze pizza sales data. The project involves setting up a pizza sales database, performing exploratory data analysis (EDA), and answering specific business questions through SQL queries.

## Objectives

1. **Data Cleaning**: Identify and remove any records with missing or null values.
2. **Exploratory Data Analysis (EDA)**: Perform basic exploratory data analysis to understand the dataset.
3. **Business Analysis**: Use SQL queries to answer specific business questions and derive insights from the data.
4. **Data Analysis**: Identify total revenue, most ordered pizza types, pizza categories, pizza ordered per day.

## Project Structure

### 1. Database Setup

- **Database Creation**: The project starts by creating a database named `pizzahouse`.

```sql
CREATE DATABASE pizzahouse;

```

### 2. Data Analysis & Findings

The following SQL queries were developed to answer specific business questions:

1. **Retrieve the total number of orders placed**:
```sql
select count(order_id) as total_orders from orders;
```

2. **Calculate the total revenue generated from pizza sales.**:
```sql
SELECT 
    ROUND(SUM(order_details.quantity * pizzas.price),
            2) AS total_sales
FROM
    order_details
        JOIN
    pizzas ON pizzas.pizza_id = order_details.pizza_id;
```

3. **Identify the highest-priced pizza.**:
```sql
SELECT 
    pizza_types.name, pizzas.price
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
ORDER BY pizzas.price DESC
LIMIT 1;

```

4. **Identify the most common pizza size ordered.**:
```sql
SELECT 
    pizzas.size,
    COUNT(order_details.order_details_id) AS order_count
FROM
    pizzas
        JOIN
    order_details ON pizzas.pizza_id = order_details.pizza_id
GROUP BY pizzas.size
ORDER BY order_count DESC;
```

5. **List the top 5 most ordered pizza types along with their quantities.**:
```sql
SELECT 
    pizza_types.name, SUM(order_details.quantity) AS quantity
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.name
ORDER BY quantity DESC
LIMIT 5;
```

6. **Join the necessary tables to find the total quantity of each pizza category ordered.**:
```sql
SELECT 
    pizza_types.category,
    SUM(order_details.quantity) AS quantity
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category
ORDER BY quantity DESC;
```

7. **Determine the distribution of orders by hour of the day**:
```sql
SELECT 
    HOUR(order_time) AS hours, COUNT(order_id) AS order_count
FROM
    orders
GROUP BY hours;

-- Join relevant tables to find the category-wise distribution of pizzas.
SELECT 
    category, COUNT(name)
FROM
    pizza_types
GROUP BY category;
```

8. **Group the orders by date and calculate the average number of pizzas ordered per day. **:
```sql
 SELECT 
    ROUND(AVG(quantity), 0) as avg_pizza_ordered_per_day
FROM
    (SELECT 
        orders.order_date, SUM(order_details.quantity) AS quantity
    FROM
        orders
    JOIN order_details ON orders.order_id = order_details.order_id
    GROUP BY orders.order_date) AS order_quantity;
```

9. **Determine the top 3 most ordered pizza types based on revenue.**:
```sql
SELECT 
    pizza_types.name,
    SUM(order_details.quantity * pizzas.price) AS revenue
FROM
    pizza_types
        JOIN
    pizzas ON pizzas.pizza_type_id = pizza_types.pizza_type_id
        JOIN
    order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.name
ORDER BY revenue DESC
LIMIT 3;
```

10. **Calculate the percentage contribution of each pizza type to total revenue.**:
```sql
SELECT 
    pizza_types.category,
    ROUND((SUM(order_details.quantity * pizzas.price) / (SELECT 
                    ROUND(SUM(order_details.quantity * pizzas.price),
                                2) AS total_sales
                FROM
                    order_details
                        JOIN
                    pizzas ON pizzas.pizza_id = order_details.pizza_id)) * 100,
            2) AS revenue
FROM
    pizza_types
        JOIN
    pizzas ON pizzas.pizza_type_id = pizza_types.pizza_type_id
        JOIN
    order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category
ORDER BY revenue DESC;
```
11. **Analyze the cumulative revenue generated over time.**:
```sql
select order_date,
sum(revenue) over(order by order_date) as cum_revenue from
(select orders.order_date,
sum(order_details.quantity * pizzas.price) as revenue 
from order_details join pizzas on order_details.pizza_id = pizzas.pizza_id join orders
on orders.order_id = order_details.order_id 
group by orders.order_date) as sales ;
```
12. **Determine the top 3 most ordered pizza types based on revenue for each pizza category.**:
```sql
select name, revenue from
(select category, name , revenue,
rank() over(partition by category order by revenue desc) as rn
from 
(select pizza_types.category, pizza_types.name,
sum((order_details.quantity) * pizzas.price) as revenue
from pizza_types join pizzas 
on pizza_types.pizza_type_id = pizzas.pizza_type_id
join order_details
on order_details.pizza_id = pizzas.pizza_id
group by pizza_types.category , pizza_types.name) as a) as b
where rn <= 3;
```

## Findings

- **Pizza Types and Categories**: The analysis identifies the best selling pizzas type and category.
- **Orders and Revenue**: Total  numbers of orders and revenue generated.
- **Sales Trends**: The analysis shows the distribution of orders by hours of the day and average number of pizza ordered per day.


## Reports

- **Sales Summary**: A detailed report summarizing total orders, total revenue and cumulative revenue generated over time.
- **Trend Analysis**: Insights into sales trends across different hours of the day.
- **Order Insights**: Reports on top selling pizza category and most ordered quantity & size ordered and revenue generated out of it.

##Conclusion

The findings from this project can help drive business decisions by understanding sales, order patterns, and product performance.

This project is part of my portfolio, showcasing the SQL skills essential for data analyst roles.


Thank you for your support, and I look forward to connecting with you!
