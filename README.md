create database Zepto_sql_project;
use  Zepto_sql_project;
SET SQL_SAFE_UPDATES = 0;

--   data exploration
--  count of rows
select count(*) as no_of_rows from zepto_v2;

-- sample data
SELECT  * FROM zepto_v2
LIMIT 10;

-- null values
SELECT * FROM zepto_v2
WHERE
Category IS NULL
or
name IS NULL
or
mrp IS NULL
or
discountPercent IS NULL
or
availableQuantity IS NULL
or
discountedSellingPrice IS NULL
or
weightInGms IS NULL
or
outOfStock IS NULL
or
quantity IS NULL

-- different product categories
SELECT DISTINCT Category FROM zepto_v2
ORDER BY Category;

-- products in stocks vs out of stocks
SELECT outOfStock,count(*) as total
FROM zepto_v2
GROUP BY outOfStock;

-- product name present multiple times
SELECT name,count(name) as total
FROM zepto_v2
GROUP BY name
HAVING count(name)>1;

-- data cleaning
-- products with price 0
SELECT * FROM zepto_v2
WHERE mrp=0 or discountedSellingPrice=0;

DELETE from zepto_v2
WHERE mrp=0;

-- covert paise  to rupee
ALTER TABLE zepto_v2
MODIFY mrp DECIMAL(10,2);
ALTER TABLE zepto_v2
MODIFY discountedSellingPrice DECIMAL(10,2);

UPDATE zepto_v2
SET mrp=mrp/100.0 ,
discountedSellingPrice=discountedSellingPrice/100.0;

-- data analysis
-- Q1. Find the top 10 best-value products based on the discount percentage.
SELECT DISTINCT name,mrp,discountPercent
FROM zepto_v2
ORDER BY discountPercent DESC
LIMIT 10;

-- Q2.What are the Products with High MRP but Out of Stock
SELECT DISTINCT name,mrp
FROM zepto_v2
WHERE outOfStock="TRUE" and mrp>300
ORDER BY mrp DESC;

-- Q3.Calculate Estimated Revenue for each category
SELECT  Category,
sum(discountedSellingPrice*availableQuantity) as total_Revenue
FROM zepto_v2
GROUP BY Category
ORDER BY total_Revenue;

-- Q4. Find all products where MRP is greater than ₹500 and discount is less than 10%.
SELECT name,mrp,discountPercent
FROM zepto_v2
where mrp > 500 and discountPercent<10
order by mrp DESC , discountPercent DESC;

-- Q5. Identify the top 5 categories offering the highest average discount percentage.
SELECT Category,
round(avg(discountPercent),2) as avg_discount
from zepto_v2
group by Category
ORDER BY avg_discount DESC
LIMIT 5;

-- Q6. Find the price per gram for products above 100g and sort by best value.
SELECT distinct name,weightInGms,discountedSellingPrice,
round((discountedSellingPrice/weightInGms), 2) as price_per_gram
from zepto_v2
WHERE weightInGms >100
ORDER BY price_per_gram ;

-- Q7.Group the products into categories like Low, Medium, Bulk.
SELECT distinct name,weightInGms,
case 
     when weightInGms <1000 then 'Low'
     when weightInGms < 5000 then 'Medium'
     else 'Bulk'
end as weight_category
from zepto_v2;
-- Q8.What is the Total Inventory Weight Per Category 
SELECT Category ,
sum(weightInGms*availableQuantity) as total_weight
from zepto_v2
group by Category
ORDER BY total_weight;
