# Retail Sales Analysis SQL Project

## Project Overview

**Project Title**: Retail Sales Analysis  
**Level**: Beginner  
**Database**: `retailsales`

This project is designed to demonstrate SQL skills and techniques typically used by data analysts to explore, clean, and analyze retail sales data. The project involves setting up a retail sales database, performing exploratory data analysis (EDA), and answering specific business questions through SQL queries. This project is ideal for those who are starting their journey in data analysis and want to build a solid foundation in SQL.

## Objectives

1. **Set up a retail sales database**: Create and populate a retail sales database with the provided sales data.
2. **Data Cleaning**: Identify and remove any records with missing or null values.
3. **Exploratory Data Analysis (EDA)**: Perform basic exploratory data analysis to understand the dataset.
4. **Business Analysis**: Use SQL to answer specific business questions and derive insights from the sales data.

## Tasks/Questions to Answer
1. Find all sales made on November 5, 2022.
2. Find all transactions where the category is "clothing", the quantity is at least 4, and the dates are November 2022.
3. Calculate the total sales for each category.
4. Find the average age of customers who purchased items from the "Beauty" category.
5. Find all transactions where the total sales are greater than 1000.
6. Find the total number of transactions made by each gender in each category.
7. Calculate the average sale for each month. Use this to find the best-selling month for each year.
8. Find the top 5 customers based on highest total sales.
9. Find the number of unique customers who purchased items from each category.
10. Find the number of orders per shift (Morning - before 12pm, Afternoon - 12pm-5pm, Evening - after 5pm).


## Project Structure

### 1. Database Setup

- **Database Creation**: The project starts by creating a database named `retailsales`.
- **Table Creation**: A table named `retail_sales` is created to store the sales data. The table structure includes columns for transaction ID, sale date, sale time, customer ID, gender, age, product category, quantity sold, price per unit, cost of goods sold (COGS), and total sale amount.

```sql
-- Create Database Retail Sales Project
CREATE DATABASE RetailSales;

-- Create Table
CREATE TABLE retail_sales
	(
	transactions_id INT PRIMARY KEY,	
    sale_date DATE,	
    sale_time TIME,
	customer_id INT,
	gender VARCHAR(10),
	age INT,
	category VARCHAR(20),
	quantity INT,
	price_per_unit FLOAT,
	cogs FLOAT,
	total_sale FLOAT
    ); 
```

### 2. Data Exploration & Cleaning

- **Data Check**: Looking at the header of the table to ensure correctness.
- **Record Count**: Determine the total number of records in the dataset.
- **Sales Count**: Find out how many total sales are in the dataset.
- **Customer Count**: Find out how many unique customers are in the dataset.
- **Category Count**: Identify all unique product categories in the dataset.
- **Null Value Check**: Check for any null values in the dataset and delete records with missing data.

```sql
-- Check the Data
SELECT *
FROM retail_sales
LIMIT 10;

-- Ensuring correct # rows (1987)
SELECT COUNT(*) 
FROM retail_sales
LIMIT 10;

-- How many sales were there?
SELECT COUNT(*) AS total_sales
FROM retail_sales;

-- How many customers are there?
SELECT COUNT(DISTINCT(customer_id)) AS customer_total
FROM retail_sales;

-- How many categories are there?
SELECT COUNT(DISTINCT(category)) AS customer_total
FROM retail_sales; 

-- Clean Data
-- No Nulls imported from Excel (.csv) so no cleaning needed
SELECT *
FROM retail_sales
WHERE 
	transactions_id IS NULL
    OR sale_date IS NULL
    OR sale_time IS NULL
    OR customer_id IS NULL
    OR gender IS NULL
    OR age IS NULL
    OR category IS NULL
    OR quantity IS NULL
    OR price_per_unit IS NULL
    OR cogs IS NULL
    OR total_sale IS NULL;
```

### 3. Data Analysis & Findings

The following SQL queries were developed to answer specific business questions:

1. **Find all sales made on November 5, 2022.**:
```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

2. **Find all transactions where the category is "clothing", the quantity is at least 4, and the dates are November 2022.**:
```sql
SELECT *
FROM retail_sales
WHERE
category = 'Clothing'
    AND quantity >= 4
    AND YEAR(sale_date) = '2022'
    AND MONTH(sale_date) = '11';
```

3. **Calculate the total sales for each category.**:
```sql
SELECT
  category,
  sum(total_sale) AS net_sale,
  count(*) AS total_orders
FROM retail_sales
GROUP BY category;
```

4. **Find the average age of customers who purchased items from the "Beauty" category.**:
```sql
SELECT
  category,
  ROUND(avg(age), 2)	 AS avg_age
FROM retail_sales
WHERE category = 'Beauty';
```

5. **Find all transactions where the total sales are greater than 1000.**:
```sql
SELECT *
FROM retail_sales
WHERE total_sale > 1000;
```

6. **Find the total number of transactions made by each gender in each category.**:
```sql
SELECT 
  category,
  gender,
  COUNT(DISTINCT(transactions_id)) AS number_of_transactions
FROM retail_sales
GROUP BY category, gender;
```

7. **Calculate the average sale for each month. Use this to find the best-selling month for each year.**:
```sql
SELECT * FROM 
(
SELECT
YEAR(sale_date) AS year,
    MONTH(sale_date) AS month,
    ROUND(avg(total_sale),2) as avg_sales,
    RANK() OVER(PARTITION BY YEAR(sale_date) ORDER BY ROUND(avg(total_sale),2) DESC) as 'rank'
FROM retail_sales
GROUP BY year, month
ORDER BY year, avg_sales DESC
) as t1
WHERE t1.rank = '1';
```

8. **Find the top 5 customers based on highest total sales.**:
```sql
SELECT
  customer_id,
  sum(total_sale) as net_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY net_sales DESC
LIMIT 5;
```

9. **Find the number of unique customers who purchased items from each category.**:
```sql
SELECT 
  category,
  COUNT(DISTINCT(customer_id)) as customers
FROM retail_sales
GROUP BY category;
```

10. **Find the number of orders per shift (Morning - before 12pm, Afternoon - 12pm-5pm, Evening - after 5pm).**:
```sql
WITH hourly_sales
   AS
   (
   SELECT *,
		CASE
			WHEN HOUR(sale_time) < 12 THEN 'Morning'
            WHEN HOUR(sale_time) > 17 THEN 'Evening'
            ELSE 'Afternoon'
		END AS shift
	FROM retail_sales
    )
SELECT 
  shift,
  COUNT(DISTINCT(transactions_id)) as orders
FROM hourly_sales
GROUP BY shift
```

## Answers:
1. **Find all sales made on November 5, 2022.**:
   11 Sales
2. **Find all transactions where the category is "clothing", the quantity is at least 4, and the dates are November 2022.**:
   17 Transcactions
3. **Calculate the total sales for each category.**:
   Beauty - 611, Clothing - 698, Electronics - 678
4. **Find the average age of customers who purchased items from the "Beauty" category.**:
   40.42 years
5. **Find all transactions where the total sales are greater than 1000.**:
   306 Transactions
6. **Find the total number of transactions made by each gender in each category.**:
   Beauty/Female - 330, Beauty/Male - 281, Clothing/ Female - 347, Clothing/Male - 351, Electronics/Female - 335, Electronics/Male - 343
7. **Calculate the average sale for each month. Use this to find the best-selling month for each year.**:
   2022 - July (541.34 average sales), 2023 - February (535.53 average sales)
8. **Find the top 5 customers based on highest total sales.**:
   (1) customer 3, (2) customer 1, (3) customer 5, (4) customer 2, (5) customer 4
9. **Find the number of unique customers who purchased items from each category.**:
    Beauty - 141, Clothing - 149, Electronics - 144
10. **Find the number of orders per shift (Morning - before 12pm, Afternoon - 12pm-5pm, Evening - after 5pm).**:
    Morning - 548, Afternoon - 377, Evening - 1062

## Findings:
- **Customer Demographics**: The dataset includes customers from various age groups, with sales distributed across different categories such as Clothing and Beauty.
- **High-Value Transactions**: Several transactions had a total sale amount greater than 1000, indicating premium purchases.
- **Sales Trends**: Monthly analysis shows variations in sales, helping identify peak seasons.
- **Customer Insights**: The analysis identifies the top-spending customers and the most popular product categories.

## Reports

- **Sales Summary**: A detailed report summarizing total sales, customer demographics, and category performance.
- **Trend Analysis**: Insights into sales trends across different months and shifts.
- **Customer Insights**: Reports on top customers and unique customer counts per category.

## Conclusion

This project serves as a comprehensive introduction to SQL for data analysts, covering database setup, data cleaning, exploratory data analysis, and business-driven SQL queries. The findings from this project can help drive business decisions by understanding sales patterns, customer behavior, and product performance

## Author - Dominic Rinaldi

This project is part of my portfolio, showcasing the SQL skills essential for data analyst roles. If you have any questions, feedback, or would like to collaborate, feel free to get in touch!
- **LinkedIn**: [Connect with me professionally](https://www.linkedin.com/in/dominic-rinaldi-a71941159)

Thank you for your support, and I look forward to connecting with you!


