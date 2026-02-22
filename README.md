# Retail Sales Analysis SQL Project

## Project Overview
**Project Title:** Retail Sales Analysis  
**Database:** `SQL_Sales_P`

This project involves a comprehensive analysis of retail sales data using PostgreSQL. The workflow includes database setup, rigorous data cleaning (handling null values through statistical imputation), and exploratory data analysis (EDA) to uncover actionable business insights. This project demonstrates proficiency in advanced SQL techniques, including window functions, CTEs, and complex aggregations.



## Project Structure

### 1. Database & Table Setup
* **Database Creation:** Created a database named `SQL_Sales_P`.
```sql
CREATE DATABASE SQL_Sales_P;
```
* **Schema Design:** Defined a table `retail_sales` with structured data types (INT, DATE, TIME, FLOAT) to ensure data integrity and optimized querying.
```sql
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
		quantity INT,
		price_per_unit FLOAT,
		cogs FLOAT,
		total_sale FLOAT
	);
```
---
### 2. Data Cleaning & Exploration
* **Finding The Null Values In The Dataset**
```sql
SELECT * FROM retail_sales
WHERE 
	transactions_id IS NULL
	OR
	sale_date IS NULL
	OR
	sale_time IS NULL
	OR
	customer_id IS NULL
	OR
	gender IS NULL
	OR
	age IS NULL
	OR
	category IS NULL
	OR
	Quantity IS NULL
	OR
	price_per_unit IS NULL
	OR
	cogs IS NULL
	OR 
	total_sale IS NULL;
```
* **Null Value Handling:** * Implemented **Mean Imputation** for the `age` column to fill missing values with the statistical average, ensuring the dataset remains robust for demographic analysis.
```sql
UPDATE retail_sales
SET age = (SELECT AVG(age) FROM retail_sales)
WHERE age IS NULL;
```
  * Performed **Listwise Deletion** for records missing critical financial data (`total_sale`) to maintain the accuracy of revenue metrics.
  ```sql
  DELETE FROM retail_sales
  WHERE
	total_sale IS NULL;
```
* **Exploratory Data Analysis (EDA):** Conducted initial checks to determine total record counts, unique customer counts, and product category diversity.
* Total Record Counts
  ```sql
  SELECT 
	COUNT(*) 
  FROM retail_sales;
  ```
* Unique Customer Counts
  ```sql
  SELECT COUNT(DISTINCT customer_id) FROM retail_sales;
  ```
* Product Category Diversity
  ```sql
  SELECT DISTINCT category FROM retail_sales;
  ```
---

### 3. Data Analysis & Findings

The following SQL queries were developed to answer specific business questions:

1. **Write a SQL query to retrieve all columns for sales made on '2022-11-05**:
```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

2. **Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 4 in the month of Nov-2022**:
```sql
SELECT 
  *
FROM retail_sales
WHERE 
    category = 'Clothing'
    AND 
    TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
    AND
    quantity >= 4
```

3. **Write a SQL query to calculate the total sales (total_sale) for each category.**:
```sql
SELECT 
    category,
    SUM(total_sale) as net_sale,
    COUNT(*) as total_orders
FROM retail_sales
GROUP BY 1
```

4. **Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.**:
```sql
SELECT
    ROUND(AVG(age), 2) as avg_age
FROM retail_sales
WHERE category = 'Beauty'
```

5. **Write a SQL query to find all transactions where the total_sale is greater than 1000.**:
```sql
SELECT * FROM retail_sales
WHERE total_sale > 1000
```

6. **Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.**:
```sql
SELECT 
    category,
    gender,
    COUNT(*) as total_trans
FROM retail_sales
GROUP 
    BY 
    category,
    gender
ORDER BY 1
```

7. **Write a SQL query to calculate the average sale for each month. Find out best selling month in each year**:
```sql
SELECT 
       year,
       month,
    avg_sale
FROM 
(    
SELECT 
    EXTRACT(YEAR FROM sale_date) as year,
    EXTRACT(MONTH FROM sale_date) as month,
    AVG(total_sale) as avg_sale,
    RANK() OVER(PARTITION BY EXTRACT(YEAR FROM sale_date) ORDER BY AVG(total_sale) DESC) as rank
FROM retail_sales
GROUP BY 1, 2
) as t1
WHERE rank = 1
```

8. **Write a SQL query to find the top 5 customers based on the highest total sales.**:
```sql
SELECT 
    customer_id,
    SUM(total_sale) as total_sales
FROM retail_sales
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5
```

9. **Write a SQL query to find the number of unique customers who purchased items from each category.**:
```sql
SELECT 
    category,    
    COUNT(DISTINCT customer_id) as cnt_unique_cs
FROM retail_sales
GROUP BY category
```

10. **Write a SQL query to create each shift and number of orders (Example Morning <12, Afternoon Between 12 & 17, Evening >17)**:
```sql
WITH hourly_sale
AS
(
SELECT *,
    CASE
        WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
        WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
        ELSE 'Evening'
    END as shift
FROM retail_sales
)
SELECT 
    shift,
    COUNT(*) as total_orders    
FROM hourly_sale
GROUP BY shift
```
---


### 4. Key Business Insights & Conclusions
The following insights were derived through the analysis of **1,997 transactions** totaling **$911,720** in revenue:

* **Category Performance:**
    * **Electronics** is the revenue leader, contributing **$313,810**.
    * **Clothing** is the highest volume category, with **1,785 items** sold across **701 individual orders**.
* **Customer Demographics:**
    * Revenue is balanced across genders, with **Female customers** contributing **$465,400** and **Male customers** contributing **$446,320**.
    * The average customer age across all categories is consistent at approximately **41 years**.
* **Operational Trends:**
    * **Shift Analysis:** The **Evening shift** is the busiest period for the business, handling **1,062 transactions**—significantly higher than the Morning and Afternoon shifts.
    * **Seasonality:** Data reveals a major sales spike in **November and December**, indicating peak performance during the holiday season.

## Reports

- **Sales Summary**: A detailed report summarizing total sales, customer demographics, and category performance.
- **Trend Analysis**: Insights into sales trends across different months and shifts.
- **Customer Insights**: Reports on top customers and unique customer counts per category.

## Conclusion

This project serves as a comprehensive introduction to SQL for data analysts, covering database setup, data cleaning, exploratory data analysis, and business-driven SQL queries. The findings from this project can help drive business decisions by understanding sales patterns, customer behavior, and product performance.


## Technical Skills Demonstrated
* **Data Definition Language (DDL):** Schema creation and management.
* **Data Manipulation Language (DML):** Advanced updates and cleaning scripts.
* **Aggregations & Grouping:** Using `SUM`, `AVG`, `COUNT`, and `GROUP BY` to summarize large datasets.
* **Advanced Logic:** Implementing `CASE` statements for shift classification and business logic.
* **Window Functions:** Utilizing `RANK()`, `OVER()`, and `PARTITION BY` for ranking and time-series analysis.
* **Date/Time Functions:** Extracting specific time components (`EXTRACT`, `TO_CHAR`) for trend analysis.

## How to Use
1. **Clone the Repo:** `git clone https://github.com/your-username/retail-sales-sql.git`
2. **Import Data:** Use the provided SQL script to create the table and import the raw `.csv` data.
3. **Execute Analysis:** Run the analysis queries to replicate the findings or customize them for further exploration.

## Author - Ahmed Hewidi

This project is part of my portfolio, showcasing the SQL skills essential for data analyst roles. If you have any questions, feedback, or would like to collaborate, feel free to get in touch!

Thank you for your support, and I look forward to connecting with you!
