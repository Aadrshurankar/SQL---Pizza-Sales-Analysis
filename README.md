# 🍕 Pizza Sales Analysis using SQL

<p align="center">

<img src="https://img.shields.io/badge/MySQL-Data%20Analysis-blue?style=for-the-badge&logo=mysql&logoColor=white"/>

<img src="https://img.shields.io/badge/SQL-Basic%20%7C%20Intermediate%20%7C%20Advanced-orange?style=for-the-badge"/>

<img src="https://img.shields.io/badge/Project-Pizza%20Sales-success?style=for-the-badge"/>

</p>

<p align="center">
<b>Analyzing pizza sales data and extracting business insights using MySQL.</b>
</p>

---

## 📌 Project Overview

The **Pizza Sales Analysis** project is a SQL-based data analysis project built using **MySQL**.

The project analyzes pizza orders, sales quantities, prices, categories, order timings, and revenue performance.

It contains **13 SQL analysis questions**, starting with basic aggregation and gradually moving toward advanced SQL concepts such as:

- JOINs
- GROUP BY
- Aggregate Functions
- Subqueries
- Window Functions
- RANK()
- PARTITION BY
- Cumulative Revenue Analysis

The main objective is to convert raw pizza sales data into meaningful business insights.

---

# 🎯 Project Objectives

The project focuses on:

- Calculating total orders.
- Calculating total revenue.
- Identifying the highest-priced pizza.
- Finding the most common pizza size.
- Identifying the top 5 most ordered pizzas.
- Analyzing sales by pizza category.
- Analyzing orders by hour.
- Calculating average daily pizza orders.
- Finding top pizzas by revenue.
- Calculating category revenue contribution.
- Analyzing cumulative revenue.
- Ranking the top pizzas within each category.

---

# 🗃️ Database Structure

The project uses the following tables:

```text
pizza_dataset
│
├── orders
├── order_details
├── pizzas
└── pizza_types
```

### 📋 orders

Contains order-level information.

```text
order_id
order_date
order_time
```

### 🧾 order_details

Contains details about pizzas included in each order.

```text
order_details_id
order_id
pizza_id
quantity
```

### 🍕 pizzas

Contains pizza-level information such as size and price.

```text
pizza_id
pizza_type_id
size
price
```

### 📋 pizza_types

Contains pizza names and categories.

```text
pizza_type_id
name
category
```

---

# 🧠 SQL Concepts Used

## 🟢 Basic SQL

- SELECT
- FROM
- ORDER BY
- LIMIT
- GROUP BY
- COUNT()
- SUM()
- AVG()
- MIN()
- MAX()
- ROUND()

## 🟡 Intermediate SQL

- INNER JOIN
- Multiple Table JOIN
- GROUP BY
- Aggregate Functions
- Date Analysis
- Time Analysis
- Subqueries

## 🔴 Advanced SQL

- Window Functions
- SUM() OVER()
- RANK() OVER()
- PARTITION BY
- Cumulative Revenue
- Nested Subqueries

---

# 📊 SQL Analysis & Query Snippets

## 1️⃣ Total Number of Orders

**Business Question:**  
How many orders were placed?

```sql
SELECT 
    COUNT(order_id) AS total_no_of_orders
FROM orders;
```

---

## 2️⃣ Total Revenue

**Business Question:**  
What is the total revenue generated from pizza sales?

```sql
SELECT 
    ROUND(SUM(order_details.quantity * pizzas.price), 2) AS total_sales
FROM order_details
JOIN pizzas 
    ON pizzas.pizza_id = order_details.pizza_id;
```

### Concept Used

```text
JOIN + SUM() + ROUND()
```

---

## 3️⃣ Highest-Priced Pizza

**Business Question:**  
Which pizza has the highest price?

```sql
SELECT 
    pizza_types.name,
    pizzas.price
FROM pizza_types
JOIN pizzas 
    ON pizza_types.pizza_type_id = pizzas.pizza_type_id
ORDER BY pizzas.price DESC
LIMIT 1;
```

### Concept Used

```text
JOIN + ORDER BY + LIMIT
```

---

## 4️⃣ Most Common Pizza Size

**Business Question:**  
Which pizza size is ordered most frequently?

```sql
SELECT 
    pizzas.size,
    COUNT(order_details.order_details_id) AS order_count
FROM pizzas
JOIN order_details
    ON pizzas.pizza_id = order_details.pizza_id
GROUP BY pizzas.size
ORDER BY order_count DESC;
```

### Concept Used

```text
JOIN + COUNT() + GROUP BY + ORDER BY
```

---

## 5️⃣ Top 5 Most Ordered Pizza Types

**Business Question:**  
Which 5 pizza types have the highest order quantities?

```sql
SELECT 
    pizza_types.name,
    SUM(order_details.quantity) AS quantity
FROM pizza_types
JOIN pizzas
    ON pizza_types.pizza_type_id = pizzas.pizza_type_id
JOIN order_details
    ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.name
ORDER BY quantity DESC
LIMIT 5;
```

### Concept Used

```text
Multiple JOINs + SUM() + GROUP BY + LIMIT
```

---

# 6️⃣ Total Quantity by Pizza Category

**Business Question:**  
How many pizzas were ordered from each category?

```sql
SELECT 
    pizza_types.category,
    SUM(order_details.quantity) AS quantity
FROM pizza_types
JOIN pizzas
    ON pizza_types.pizza_type_id = pizzas.pizza_type_id
JOIN order_details
    ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category
ORDER BY quantity DESC;
```

---

# 7️⃣ Order Distribution by Hour

**Business Question:**  
At what hours are orders placed?

```sql
SELECT 
    HOUR(order_time) AS hour,
    COUNT(order_id) AS order_count
FROM orders
GROUP BY HOUR(order_time);
```

### Concept Used

```text
HOUR() + COUNT() + GROUP BY
```

---

# 8️⃣ Pizza Distribution by Category

**Business Question:**  
How many pizza types are available in each category?

```sql
SELECT 
    category,
    COUNT(name)
FROM pizza_types
GROUP BY category;
```

---

# 9️⃣ Average Number of Pizzas Ordered Per Day

**Business Question:**  
What is the average number of pizzas ordered per day?

```sql
SELECT 
    ROUND(AVG(quantity), 0)
FROM
(
    SELECT 
        orders.order_date,
        SUM(order_details.quantity) AS quantity
    FROM orders
    JOIN order_details
        ON orders.order_id = order_details.order_id
    GROUP BY orders.order_date
) AS order_quantity;
```

### Concept Used

```text
JOIN + GROUP BY + Subquery + AVG()
```

---

# 🔟 Top 3 Pizza Types by Revenue

**Business Question:**  
Which 3 pizza types generate the highest revenue?

```sql
SELECT 
    pizza_types.name,
    SUM(order_details.quantity * pizzas.price) AS revenue
FROM pizza_types
JOIN pizzas
    ON pizzas.pizza_type_id = pizza_types.pizza_type_id
JOIN order_details
    ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.name
ORDER BY revenue DESC
LIMIT 3;
```

---

# 1️⃣1️⃣ Revenue Contribution by Category

**Business Question:**  
What percentage of total revenue is contributed by each pizza category?

```sql
SELECT 
    pizza_types.category,
    ROUND(
        SUM(order_details.quantity * pizzas.price) /
        (
            SELECT 
                ROUND(
                    SUM(order_details.quantity * pizzas.price),
                    2
                )
            FROM order_details
            JOIN pizzas
                ON pizzas.pizza_id = order_details.pizza_id
        ) * 100,
        2
    ) AS revenue
FROM pizza_types
JOIN pizzas
    ON pizza_types.pizza_type_id = pizzas.pizza_type_id
JOIN order_details
    ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category
ORDER BY revenue DESC;
```

### Concept Used

```text
Subquery + JOIN + SUM() + ROUND()
```

> **Note:** The actual query calculates revenue contribution by **category**, not individual pizza type.

---

# 1️⃣2️⃣ Cumulative Revenue Analysis

**Business Question:**  
How does revenue accumulate over time?

```sql
SELECT 
    order_date,
    SUM(revenue) OVER (
        ORDER BY order_date
    ) AS sum_revenue
FROM
(
    SELECT 
        orders.order_date,
        SUM(
            order_details.quantity * pizzas.price
        ) AS revenue
    FROM order_details
    JOIN pizzas
        ON order_details.pizza_id = pizzas.pizza_id
    JOIN orders
        ON orders.order_id = order_details.order_id
    GROUP BY orders.order_date
) AS sales;
```

### Advanced Concepts

```text
Subquery
SUM() OVER()
ORDER BY
Cumulative Calculation
```

---

# 1️⃣3️⃣ Top 3 Pizzas by Revenue Within Each Category

**Business Question:**  
What are the top 3 revenue-generating pizzas within each category?

```sql
SELECT 
    name,
    revenue
FROM
(
    SELECT 
        category,
        name,
        revenue,
        RANK() OVER(
            PARTITION BY category
            ORDER BY revenue DESC
        ) AS rn
    FROM
    (
        SELECT 
            pizza_types.category,
            pizza_types.name,
            SUM(
                order_details.quantity * pizzas.price
            ) AS revenue
        FROM pizza_types
        JOIN pizzas
            ON pizzas.pizza_type_id = pizza_types.pizza_type_id
        JOIN order_details
            ON order_details.pizza_id = pizzas.pizza_id
        GROUP BY 
            pizza_types.category,
            pizza_types.name
    ) AS a
) AS b
WHERE rn <= 3;
```

### Advanced Concepts

```text
Multiple Subqueries
RANK()
OVER()
PARTITION BY
ORDER BY
```

---

# 🔥 SQL Techniques Demonstrated

| Technique | Usage |
|---|---|
| `COUNT()` | Count orders |
| `SUM()` | Calculate quantities and revenue |
| `AVG()` | Calculate average daily orders |
| `ROUND()` | Format numerical results |
| `GROUP BY` | Category and product analysis |
| `ORDER BY` | Ranking results |
| `LIMIT` | Top-N analysis |
| `JOIN` | Combine relational tables |
| `HOUR()` | Time-based analysis |
| Subquery | Complex calculations |
| `SUM() OVER()` | Cumulative revenue |
| `RANK()` | Category-wise ranking |
| `PARTITION BY` | Ranking within categories |

---

# 📈 Project Workflow

```text
                Raw Sales Data
                       │
                       ▼
                Database Tables
                       │
                       ▼
                  SQL Queries
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
       Basic       Intermediate   Advanced
       Analysis      Analysis      Analysis
          │            │            │
          └────────────┼────────────┘
                       ▼
                Business Insights
```

---

# 💡 Key Learning Outcomes

Through this project, I practiced:

- Writing SQL queries for real-world business questions.
- Working with multiple relational tables.
- Using JOINs to combine datasets.
- Performing sales and revenue calculations.
- Applying aggregate functions.
- Performing time-based analysis.
- Using subqueries for complex calculations.
- Applying window functions.
- Ranking products within categories.
- Performing cumulative revenue analysis.

---

# 📁 Repository Structure

```text
SQL---Pizza-Sales-Analysis/
│
├── Pizza Sales Analysis.sql
├── orders.csv
├── order_details.csv
├── pizzas.csv
├── pizza_types.csv
├── Questions.txt
└── README.md
```

---

# 🚀 How to Run the Project

### 1. Clone the Repository

```bash
git clone https://github.com/Aadrshurankar/SQL---Pizza-Sales-Analysis.git
```

### 2. Open MySQL Workbench

Create the database:

```sql
CREATE DATABASE pizza_dataset;

USE pizza_dataset;
```

### 3. Create the Tables

Run the table creation queries from:

```text
Pizza Sales Analysis.sql
```

### 4. Import the CSV Files

Import:

```text
orders.csv
order_details.csv
pizzas.csv
pizza_types.csv
```

### 5. Execute the Analysis

Run the SQL queries from:

```text
Pizza Sales Analysis.sql
```

---

# 🛠️ Tools Used

- **MySQL**
- **MySQL Workbench**
- **SQL**
- **CSV Dataset**

---

# 🎯 Project Level

```text
🟢 Basic
    ↓
🟡 Intermediate
    ↓
🔴 Advanced
```

This project demonstrates progression from basic SQL queries to advanced analytical SQL techniques.

---

# 👨‍💻 Author

## Aadrsh Urankar

**Junior Data Operation Executive | MIS Executive | Data Analyst**

📍 Pune, Maharashtra, India

### Skills

`Excel` • `SQL` • `MySQL` • `Python` • `Data Analysis` • `MIS Reporting`

### 🔗 Connect With Me

**GitHub:**  
https://github.com/Aadrshurankar

**LinkedIn:**  
https://www.linkedin.com/in/aadrsh-urankar777/

**Email:**  
aadrshurankar40@gmail.com

---

## ⭐ Support

If you found this project useful, please consider giving the repository a ⭐ **Star**.

---

<p align="center">

### 🍕 Turning Pizza Sales Data into SQL Insights 📊

</p>
