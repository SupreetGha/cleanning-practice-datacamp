# cleanning-practice-datacamp

## Table of Contents

### Project Overview 

This project analyzes sales data from a hypothetical Super Store to identify the top 5 products within each category based on total sales. The analysis aims to provide insights into the highest-performing products in each category, sorted for clarity and decision-making.

### Tools Used

SQL - Data Cleaning and Analysis

### Analysis

The SQL query identifies:

- Total sales for each product.
- Top 5 products for each category, sorted by sales in descending order.

```sql
WITH ranked_products AS (
    SELECT p.category, p.product_name, 
           ROUND(CAST(SUM(o.sales) AS NUMERIC), 2) AS product_total_sales, 
           ROUND(CAST(SUM(o.profit) AS NUMERIC), 2) AS product_total_profit, 
           RANK() OVER(PARTITION BY p.category ORDER BY SUM(o.sales) DESC) AS product_rank
    FROM public.orders AS o
    LEFT JOIN public.products AS p
    ON o.product_id = p.product_id
    GROUP BY p.category, p.product_name
)
SELECT category, product_name, product_total_sales, product_total_profit, product_rank
FROM ranked_products
WHERE product_rank <= 5
ORDER BY category, product_rank;

```

```sql
-- impute_missing_values
WITH missing AS (
	SELECT public.orders.product_id, public.orders.discount, public.orders.market, public.orders.region, public.orders.sales, public.orders.quantity
	FROM public.orders
	WHERE public.orders.quantity IS NULL
),

unit_prices AS (
	SELECT public.orders.product_id, (public.orders.sales/public.orders.quantity)::numeric AS unit_price, public.orders.discount
	FROM public.orders
	WHERE public.orders.quantity IS NOT NULL
)

SELECT DISTINCT m.product_id, m.discount, m.market, m.region, m.sales, m.quantity, 
       COALESCE(m.quantity, (m.sales::numeric / u.unit_price)) AS calculated_quantity
FROM missing AS m
INNER JOIN unit_prices AS u
ON m.product_id = u.product_id AND m.discount = u.discount
LIMIT 5;

```

### Findings 

- Technology's category had the highest total sales
- Smart Phone were the best seller


