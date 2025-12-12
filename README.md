# 📦 Zepto Product Data Analysis (SQL Project)
## 📝 Overview

This project analyzes product-level inventory data from Zepto using MySQL.
The dataset contains information such as product name, category, price, discount, weight, stock availability, and quantity.

The goal is to clean the raw data, fix inconsistencies caused during CSV import, and perform meaningful business analysis using SQL.

## 🎯 Problem Statement

Zepto receives product data from multiple sources, often in inconsistent formats.
The challenges include:

Prices stored in paise instead of rupees

Boolean fields represented as "TRUE", "1", "false", etc.

Duplicate product names

MRP or selling price set to 0 (invalid data)

Missing values across multiple columns

This project aims to:

Clean and standardize the dataset

Resolve datatype issues

Remove incorrect records

Derive insights related to pricing, discounts, inventory, and category-level performance

## 📂 Dataset Description
Column	Description
sku_id	Unique product ID
category	Item category (e.g., Dairy, Snacks, Beverages)
name	Product name
mrp	Maximum Retail Price
discountPercent	Discount given (%)
availableQuantity	Inventory stock count
discountedSellingPrice	Selling price after discount
weightInGms	Product weight (grams)
outOfStock	Stock availability
quantity	Number of items
## 🧹 Data Cleaning Steps
✔ 1. Recreated the table with a structured schema
CREATE TABLE zepto (
  sku_id SERIAL PRIMARY KEY,
  category VARCHAR(120),
  name VARCHAR(150) NOT NULL,
  mrp NUMERIC(8,2),
  discountPercent NUMERIC(5,2),
  availableQuantity INTEGER,
  discountedSellingPrice NUMERIC(8,2),
  weightInGms INTEGER,
  outOfStock VARCHAR(10),
  quantity INTEGER
);

✔ 2. Removed products with zero-price values
DELETE FROM zepto
WHERE mrp = 0;

✔ 3. Converted price values from paise → rupees
UPDATE zepto
SET mrp = mrp / 100.0,
    discountedSellingPrice = discountedSellingPrice / 100.0;

✔ 4. Standardized boolean values

Interpreted TRUE/FALSE/1/0/true/false correctly for analysis.

✔ 5. Checked for missing values, duplicates, and inconsistent entries

## 📊 SQL Analysis Performed
Q1. Top 10 Best-Value Products (Highest Discounts)

Find the highest discounted products:

SELECT DISTINCT name, mrp, discountPercent
FROM zepto
ORDER BY discountPercent DESC
LIMIT 10;

Q2. High-MRP Products That Are Out of Stock
SELECT DISTINCT name, mrp
FROM zepto
WHERE (outOfStock IN ('TRUE','true','1',1))
  AND mrp > 300
ORDER BY mrp DESC;

Q3. Estimated Revenue Per Category
SELECT category,
SUM(discountedSellingPrice * availableQuantity) AS total_revenue
FROM zepto
GROUP BY category
ORDER BY total_revenue DESC;

Q4. Premium Products with Low Discounts (MRP > ₹500 & Discount < 10%)
SELECT DISTINCT name, mrp, discountPercent
FROM zepto
WHERE mrp > 500 AND discountPercent < 10
ORDER BY mrp DESC;

Q5. Top 5 Categories by Average Discount
SELECT category,
ROUND(AVG(discountPercent),2) AS avg_discount
FROM zepto
GROUP BY category
ORDER BY avg_discount DESC
LIMIT 5;

Q6. Price per Gram for Items Above 100g
SELECT DISTINCT name, weightInGms, discountedSellingPrice,
ROUND(discountedSellingPrice/weightInGms,2) AS price_per_gram
FROM zepto
WHERE weightInGms >= 100
ORDER BY price_per_gram;

Q7. Weight-Based Product Segmentation
SELECT DISTINCT name, weightInGms,
CASE 
  WHEN weightInGms < 1000 THEN 'Low'
  WHEN weightInGms < 5000 THEN 'Medium'
  ELSE 'Bulk'
END AS weight_category
FROM zepto;

Q8. Total Inventory Weight Per Category
SELECT category,
SUM(weightInGms * availableQuantity) AS total_weight
FROM zepto
GROUP BY category
ORDER BY total_weight DESC;

# 🛠 Technologies Used

MySQL

MySQL Workbench

CSV Import Tools

# 📈 Key Insights & Learning Outcomes

Data cleaning is essential before analysis.

CSV files may introduce datatype inconsistencies (especially booleans & numerics).

Discounts, pricing, and inventory patterns give strong business insights.

SQL CASE, aggregation, sorting, and grouping are powerful for category analysis.
