# Walmart Sales Data Analysis

## Overview
This project focuses on analyzing Walmart sales data to derive insights and answer specific business questions using SQL. The analysis involves data cleaning, feature engineering, exploratory data analysis (EDA), and answering business queries related to sales, products, customers, and branches.

## Prerequisites
1. MySQL installed and configured.
2. Data file for Walmart sales available locally.

## Database Setup
1. Create the database:
   ```sql
   CREATE DATABASE IF NOT EXISTS walmart;
   USE walmart;
   ```

2. Create the `Sales` table:
   ```sql
   CREATE TABLE Sales(
       invoice_id VARCHAR(30) NOT NULL PRIMARY KEY,
       branch VARCHAR(5) NOT NULL,
       city VARCHAR(30) NOT NULL,
       customer_type VARCHAR(30) NOT NULL,
       gender VARCHAR(10) NOT NULL,
       product_line VARCHAR(100) NOT NULL,
       unit_price DECIMAL(10,2) NOT NULL,
       quantity INT(20) NOT NULL,
       vat FLOAT(6,4) NOT NULL,
       total DECIMAL(12, 4) NOT NULL,
       date DATETIME NOT NULL,
       time TIME NOT NULL,
       payment VARCHAR(15) NOT NULL,
       cogs DECIMAL(10,2) NOT NULL,
       gross_margin_pct FLOAT(11,9),
       gross_income DECIMAL(12, 4),
       rating FLOAT(2, 1)
   );
   ```

3. Enable local file loading:
   ```sql
   SHOW GLOBAL VARIABLES LIKE 'local_infile';
   SET GLOBAL local_infile = 1;
   ```

4. Load the data:
   ```sql
   LOAD DATA LOCAL INFILE '<path_to_data_file>'
   INTO TABLE sales
   FIELDS TERMINATED BY ','
   ENCLOSED BY '"'
   LINES TERMINATED BY '\n'
   IGNORE 1 ROWS;
   ```

## Feature Engineering
1. Add `time_of_day` column:
   ```sql
   ALTER TABLE Sales ADD COLUMN time_of_day VARCHAR(20);
   UPDATE sales
   SET time_of_day = (
       CASE
           WHEN `time` BETWEEN "00:00:00" AND "12:00:00" THEN "Morning"
           WHEN `time` BETWEEN "12:01:00" AND "16:00:00" THEN "Afternoon"
           ELSE "Evening"
       END
   );
   ```

2. Add `day_name` column:
   ```sql
   ALTER TABLE sales ADD COLUMN day_name VARCHAR(10);
   UPDATE Sales SET day_name = DAYNAME(date);
   ```

3. Add `month_name` column:
   ```sql
   ALTER TABLE sales ADD COLUMN month_name VARCHAR(10);
   UPDATE Sales SET month_name = MONTHNAME(date);
   ```

## Exploratory Data Analysis (EDA)
### Generic Questions
1. **Number of distinct cities:**
   ```sql
   SELECT DISTINCT city FROM Sales;
   ```

2. **Branches and their cities:**
   ```sql
   SELECT DISTINCT branch, city FROM Sales;
   ```

### Product Analysis
1. **Distinct product lines:**
   ```sql
   SELECT COUNT(DISTINCT product_line) FROM Sales;
   ```

2. **Most common payment method:**
   ```sql
   SELECT payment, COUNT(payment) AS common_payment_method
   FROM Sales
   GROUP BY payment
   ORDER BY common_payment_method DESC
   LIMIT 1;
   ```

3. **Most selling product line:**
   ```sql
   SELECT product_line, COUNT(product_line) AS most_selling_product
   FROM Sales
   GROUP BY product_line
   ORDER BY most_selling_product DESC
   LIMIT 1;
   ```

4. **Total revenue by month:**
   ```sql
   SELECT month_name, SUM(total) AS total_revenue
   FROM Sales
   GROUP BY month_name
   ORDER BY total_revenue DESC;
   ```

### Sales Analysis
1. **Sales by time of day per weekday:**
   ```sql
   SELECT day_name, time_of_day, COUNT(invoice_id) AS total_sales
   FROM Sales
   GROUP BY day_name, time_of_day;
   ```

2. **Customer type generating highest revenue:**
   ```sql
   SELECT customer_type, SUM(total) AS total_sales
   FROM Sales
   GROUP BY customer_type
   ORDER BY total_sales DESC
   LIMIT 1;
   ```

### Customer Analysis
1. **Most common customer type:**
   ```sql
   SELECT customer_type, COUNT(customer_type) AS common_customer
   FROM Sales
   GROUP BY customer_type
   ORDER BY common_customer DESC
   LIMIT 1;
   ```

2. **Gender distribution per branch:**
   ```sql
   SELECT branch, gender, COUNT(gender) AS gender_distribution
   FROM Sales
   GROUP BY branch, gender;
   ```

## Conclusion
This analysis provides a comprehensive understanding of Walmart’s sales trends, customer preferences, and product performance, aiding in data-driven decision-making.


