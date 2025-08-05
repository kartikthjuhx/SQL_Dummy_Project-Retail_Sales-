# SQL_Dummy_Project-Retail_Sales-
# 🛍️ Retail Sales Analysis using SQL

This project performs a comprehensive **Retail Sales Analysis** using **PostgreSQL**, covering data cleaning, exploration, and analytical reporting based on transactional retail data.

---

## 📁 Dataset

**File**: `SQL - Retail Sales Analysis_utf.csv`  
This dataset contains transactional retail data including customer demographics, product information, pricing, and sales.

| Column              | Description                              |
|---------------------|------------------------------------------|
| transactions_id     | Unique transaction ID                    |
| sale_date           | Date of transaction                      |
| sale_time           | Time of transaction                      |
| customer_id         | Unique customer ID                       |
| gender              | Gender of customer                       |
| age                 | Age of customer                          |
| category            | Product category                         |
| quantity            | Quantity purchased                       |
| price_per_unit      | Price per item                           |
| cogs                | Cost of goods sold                       |
| total_sale          | Total sale amount (quantity × price)     |

---

## 📂 Project Structure
Retail-Sales-Analysis/
│
├── SQL - Retail Sales Analysis_utf.csv # Dataset
├── Dummy_Project_1.sql # SQL Queries
└── README.md # Project Documentation


---

## ⚙️ How to Run

1. Start PostgreSQL.
2. Run the SQL script `Dummy_Project_1.sql`.
3. Import the dataset into the table using:```sql
\copy retail_sales(transactions_id, sale_date, sale_time, customer_id, gender, age, category, quantity, price_per_unit, cogs, total_sale)
FROM 'your/path/to/SQL - Retail Sales Analysis_utf.csv'
WITH (FORMAT csv, HEADER true);

🔍 SQL Workflow Overview
🧱 1. Database & Table Creation
sql
Copy
Edit
CREATE DATABASE sql_project_p1;

DROP TABLE IF EXISTS retail_sales;
CREATE TABLE retail_sales (
  transactions_id INT PRIMARY KEY,
  sale_date DATE,
  sale_time TIME,
  customer_id INT,
  gender VARCHAR(15),
  age INT,
  category VARCHAR(15),
  quantity INT,
  price_per_unit FLOAT,
  cogs FLOAT,
  total_sale FLOAT
);
Sets up the project database and schema for retail sales data.

🧹 2. Data Cleaning
sql
Copy
Edit
-- Check for nulls
SELECT * FROM retail_sales
WHERE transactions_id IS NULL OR sale_date IS NULL OR ... ;

-- Delete nulls
DELETE FROM retail_sales
WHERE transactions_id IS NULL OR sale_date IS NULL OR ... ;
Ensures clean and complete data for accurate analysis.

📊 3. Data Exploration
sql
Copy
Edit
SELECT COUNT(*) FROM retail_sales;
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;
SELECT DISTINCT category FROM retail_sales;
Gives an overview of sales volume, unique customers, and product diversity.

📈 4. Sales Analysis
sql
Copy
Edit
-- Total sales and orders per category
SELECT category, SUM(total_sale) AS net_sale, COUNT(*) AS total_orders
FROM retail_sales
GROUP BY category;

-- Average age for Beauty category
SELECT ROUND(AVG(age), 2) AS avg_age
FROM retail_sales
WHERE category = 'Beauty';

-- High-value transactions
SELECT * FROM retail_sales WHERE total_sale > 1000;

-- Gender-based transactions
SELECT category, gender, COUNT(*) AS total_trans
FROM retail_sales
GROUP BY category, gender;
Analyzes performance by category, customer demographics, and transaction value.

🗓️ 5. Time-Based Analysis
sql
Copy
Edit
-- Best month each year based on average sales
SELECT year, month, avg_sale
FROM (
  SELECT 
    EXTRACT(YEAR FROM sale_date) AS year,
    EXTRACT(MONTH FROM sale_date) AS month,
    AVG(total_sale) AS avg_sale,
    RANK() OVER (PARTITION BY EXTRACT(YEAR FROM sale_date) ORDER BY AVG(total_sale) DESC) AS rank
  FROM retail_sales
  GROUP BY 1, 2
) AS t1
WHERE rank = 1;
Identifies seasonal trends and peak-performing months.

🧑‍💼 6. Customer Insights
sql
Copy
Edit
-- Top 5 customers
SELECT customer_id, SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5;

-- Unique customers per category
SELECT category, COUNT(DISTINCT customer_id) AS count_unique_customers
FROM retail_sales
GROUP BY category;
Helps in identifying loyal or high-value customers and category-wise reach.

🕒 7. Order Distribution by Shift
sql
Copy
Edit
WITH hourly_sales AS (
  SELECT *,
    CASE
      WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'MORNING'
      WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'AFTERNOON'
      ELSE 'EVENING'
    END AS shift
  FROM retail_sales
)
SELECT shift, COUNT(*) AS total_orders
FROM hourly_sales
GROUP BY shift;
Categorizes order flow by time of day: Morning, Afternoon, Evening.
