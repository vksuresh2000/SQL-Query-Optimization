# SQL Query Optimization - Complex Queries  

## Overview  
This repository contains **optimized SQL queries** with performance tuning techniques.  
The queries focus on:  
✔️ Using **CTEs (Common Table Expressions)** for better query execution.  
✔️ **Indexing strategies** for faster lookups.  
✔️ **Reducing aggregation overhead** using efficient filtering.  

---

## Optimized Query - Top Selling Products  
### Slow Query (Without Optimization) 

SELECT p.product_id, p.product_name, SUM(o.quantity * o.price) AS total_sales
FROM Orders o
JOIN Products p ON o.product_id = p.product_id
GROUP BY p.product_id, p.product_name
HAVING SUM(o.quantity * o.price) > 50000
ORDER BY total_sales DESC;

****Problems:

    Slow Aggregation: Large dataset aggregation increases execution time.
    HAVING Clause Delay: Filtering occurs after aggregation, making it inefficient.


Optimized Query (Using CTE & Indexing)

WITH Sales AS (
    SELECT product_id, SUM(quantity * price) AS total_sales
    FROM Orders
    GROUP BY product_id
    HAVING SUM(quantity * price) > 50000
)
SELECT p.product_id, p.product_name, s.total_sales
FROM Sales s
JOIN Products p ON s.product_id = p.product_id
ORDER BY s.total_sales DESC;


**Performance Tuning Techniques**

    --Use Indexing

CREATE INDEX idx_orders_product ON Orders(product_id);

    --Check Execution Plan


EXPLAIN PLAN FOR

WITH Sales AS (
    SELECT product_id, SUM(quantity * price) AS total_sales
    FROM Orders
    GROUP BY product_id
    HAVING SUM(quantity * price) > 50000
)
SELECT p.product_id, p.product_name, s.total_sales
FROM Sales s
JOIN Products p ON s.product_id = p.product_id
ORDER BY s.total_sales DESC;

SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY);

