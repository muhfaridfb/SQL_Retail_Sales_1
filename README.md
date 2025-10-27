# Retail Sales Analysis SQL Project - Project  1

## Project Overview

**Project Title**: Retail Sales Data Exploration and Analysis  

This project showcases the use of SQL for data analysis tasks commonly performed by data analysts, including data exploration, cleaning, and deriving business insights from retail sales data. The goal of this project is to practice and demonstrate SQL proficiency by building a database, performing data cleaning, exploring datasets, and answering analytical questions.  

The project serves as a practical exercise for beginners to strengthen their understanding of SQL commands such as `SELECT`, `GROUP BY`, `JOIN`, window functions, and data manipulation techniques.

---

## Objectives

1. **Database Setup** – Create and populate a retail sales database named `sql_project_p2`.  
2. **Data Cleaning** – Identify and remove records with missing or invalid data.  
3. **Exploratory Data Analysis (EDA)** – Explore customer behavior, sales patterns, and product categories.  
4. **Business Queries** – Use SQL to answer data-driven business questions and uncover insights.

---

## Project Structure

### 1. Database Setup

- **Database Creation**:  
  A new database named `sql_project_p2` is created to store retail transaction data.

- **Table Creation**:  
  The `retail_sales` table stores sales information including transaction details, customer demographics, and sales metrics.

```sql
CREATE DATABASE sql_project_p2;

DROP TABLE IF EXISTS retail_sales;
CREATE TABLE retail_sales
(
    transactions_id INT PRIMARY KEY,
    sale_date DATE,
    sale_time TIME,	
    customer_id INT,
    gender VARCHAR(15),
    age INT,
    category VARCHAR(15),
    quantiy INT,
    price_per_unit FLOAT,	
    cogs FLOAT,
    total_sale FLOAT
);
```

---

### 2. Data Exploration & Cleaning

Before analysis, the dataset is explored and cleaned to ensure data quality.

```sql
-- Preview Data
SELECT * FROM retail_sales LIMIT 10;

-- Count Total Records
SELECT COUNT(*) FROM retail_sales;

-- Check for NULL Values
SELECT * FROM retail_sales
WHERE transactions_id IS NULL
   OR sale_date IS NULL
   OR sale_time IS NULL
   OR gender IS NULL
   OR category IS NULL
   OR quantiy IS NULL
   OR cogs IS NULL
   OR total_sale IS NULL;

-- Remove NULL Records
DELETE FROM retail_sales
WHERE transactions_id IS NULL
   OR sale_date IS NULL
   OR sale_time IS NULL
   OR gender IS NULL
   OR category IS NULL
   OR quantiy IS NULL
   OR cogs IS NULL
   OR total_sale IS NULL;
```

---

### 3. Exploratory Data Analysis (EDA)

```sql
-- Total sales records
SELECT COUNT(*) AS total_sales FROM retail_sales;

-- Unique customers
SELECT COUNT(DISTINCT customer_id) AS unique_customers FROM retail_sales;

-- Unique product categories
SELECT COUNT(DISTINCT category) AS total_categories FROM retail_sales;

-- List all categories
SELECT DISTINCT category FROM retail_sales;
```

---

### 4. Business Analysis Queries

```sql
-- 1. Retrieve all sales made on ‘2022-11-05’
SELECT * FROM retail_sales
WHERE sale_date = '2022-11-05';

-- 2. Find transactions in the 'Clothing' category with quantity ≥ 4 during November 2022
SELECT * FROM retail_sales
WHERE category = 'Clothing'
  AND quantiy >= 4
  AND TO_CHAR(sale_date, 'YYYY-MM') = '2022-11';

-- 3. Calculate total sales for each category
SELECT category, SUM(total_sale) AS net_sale
FROM retail_sales
GROUP BY category;

-- 4. Find the average age of customers in the 'Beauty' category
SELECT ROUND(AVG(age), 2) AS avg_age
FROM retail_sales
WHERE category = 'Beauty';

-- 5. Find all transactions with total_sale greater than or equal to 1000
SELECT * FROM retail_sales
WHERE total_sale >= 1000;

-- 6. Count the total number of transactions made by each gender per category
SELECT category, gender, SUM(transactions_id) AS total_trans
FROM retail_sales
GROUP BY category, gender
ORDER BY category;

-- 7. Find the best-selling month for each year based on average sales
SELECT year, month, avg_sale
FROM (
  SELECT 
    EXTRACT(YEAR FROM sale_date) AS year,
    EXTRACT(MONTH FROM sale_date) AS month,
    AVG(total_sale) AS avg_sale,
    RANK() OVER (
      PARTITION BY EXTRACT(YEAR FROM sale_date)
      ORDER BY AVG(total_sale) DESC
    ) AS rank
  FROM retail_sales
  GROUP BY 1, 2
) AS t1
WHERE rank = 1;

-- 8. Identify top 5 customers with the highest total sales
SELECT customer_id, SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5;

-- 9. Count unique customers per category
SELECT category, COUNT(DISTINCT customer_id) AS unique_customers
FROM retail_sales
GROUP BY category;

-- 10. Classify transactions by time of day and count orders
WITH hourly_sale AS (
  SELECT *,
    CASE
      WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
      WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
      ELSE 'Evening'
    END AS shift
  FROM retail_sales
)
SELECT shift, COUNT(*) AS total_orders
FROM hourly_sale
GROUP BY shift;
```

---

## Findings

- Customers come from diverse age groups and genders.  
- High-value sales (≥1000) are observed across multiple categories.  
- Certain months consistently show higher average sales, highlighting seasonal behavior.  
- Clothing and Beauty categories perform strongly in total revenue.  
- Top 5 customers contribute significantly to total sales volume.

---

## Conclusion

This project demonstrates essential SQL data analysis techniques — from setting up databases and cleaning data to performing exploratory and business analysis. The insights support better understanding of retail sales patterns, customer segmentation, and product performance trends.
