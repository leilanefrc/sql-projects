Motorcycle Parts Sales
================
Leilane Cambara

This was a Datacamp project for a SQL project to analyse motorcycle
parts sales on Jupyter Notebook with PostgreSQL, but here I do it on R
Markdown and SQLite (using the packages DBI and RSQLite).

For a given table called `sales`, it was asked to calculate net revenue
for each product line,

- grouping results by month and warehouse,
- filtering so that only `"Wholesale"` orders are included and
- ordering by product line (ascending), month (ascending) and net
  revenue (descending).

``` sql
SELECT product_line, 
CASE
  WHEN strftime('%m', date) = '06' THEN 'June'
  WHEN strftime('%m', date) = '07' THEN 'July'
  WHEN strftime('%m', date) = '08' THEN 'August'
END AS month,
warehouse, round(SUM(total*(1-payment_fee)),2) AS net_revenue
FROM sales
WHERE client_type = 'Wholesale'
GROUP BY product_line, month, warehouse
ORDER BY product_line ASC, month ASC, net_revenue DESC;
```

<div class="knitsql-table">

| product_line          | month  | warehouse | net_revenue |
|:----------------------|:-------|:----------|------------:|
| Breaking system       | August | Central   |     3009.10 |
| Breaking system       | August | West      |     2475.71 |
| Breaking system       | August | North     |     1753.19 |
| Breaking system       | July   | Central   |     3740.94 |
| Breaking system       | July   | West      |     3030.39 |
| Breaking system       | July   | North     |     2568.55 |
| Breaking system       | June   | Central   |     3648.14 |
| Breaking system       | June   | North     |     1472.93 |
| Breaking system       | June   | West      |     1200.64 |
| Electrical system     | August | North     |     4673.99 |
| Electrical system     | August | Central   |     3095.22 |
| Electrical system     | August | West      |     1229.45 |
| Electrical system     | July   | Central   |     5521.94 |
| Electrical system     | July   | North     |     1693.06 |
| Electrical system     | July   | West      |      444.98 |
| Electrical system     | June   | Central   |     2875.93 |
| Electrical system     | June   | North     |     2002.30 |
| Engine                | August | Central   |     9433.48 |
| Engine                | August | North     |     2300.96 |
| Engine                | July   | Central   |     1808.77 |
| Engine                | July   | North     |      997.08 |
| Engine                | June   | Central   |     6483.40 |
| Frame & body          | August | Central   |     8571.50 |
| Frame & body          | August | North     |     7819.95 |
| Frame & body          | August | West      |      821.40 |
| Frame & body          | July   | North     |     6093.11 |
| Frame & body          | July   | Central   |     3103.82 |
| Frame & body          | June   | Central   |     5060.29 |
| Frame & body          | June   | North     |     4861.08 |
| Frame & body          | June   | West      |     2751.96 |
| Miscellaneous         | August | North     |     1823.03 |
| Miscellaneous         | August | Central   |     1722.40 |
| Miscellaneous         | August | West      |      805.31 |
| Miscellaneous         | July   | Central   |     3087.31 |
| Miscellaneous         | July   | North     |     2380.63 |
| Miscellaneous         | July   | West      |     1145.26 |
| Miscellaneous         | June   | West      |     2258.20 |
| Miscellaneous         | June   | Central   |     1859.34 |
| Miscellaneous         | June   | North     |      508.86 |
| Suspension & traction | August | Central   |     5362.59 |
| Suspension & traction | August | North     |     4874.51 |
| Suspension & traction | August | West      |     1069.99 |
| Suspension & traction | July   | Central   |     6392.23 |
| Suspension & traction | July   | North     |     3677.21 |
| Suspension & traction | July   | West      |     2909.98 |
| Suspension & traction | June   | North     |     7985.17 |
| Suspension & traction | June   | Central   |     3291.80 |
| Suspension & traction | June   | West      |     2348.83 |

48 records

</div>
