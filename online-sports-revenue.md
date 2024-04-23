Online Sports Revenue in SQL
================
Leilane Cambara

The trainers sale data of an online sports clothing company comes from
Datacamp, intended to be used in a Python project to answer different
questions:
<https://app.datacamp.com/learn/projects/analyzing_online_sports_revenue/guided/Python>.

The data come in four csv files, named “brands.csv”, “finance.csv”,
“info.csv”, and “reviews.csv”, which I do not make available since the
project is available to subscribers only.

Here, I use SQL in an R notebook to analyse revenue per brand (Adidas
and Nike).

### Setting up

First, I set up an SQL database in the memory using the packages DBI and
RSQLite and write the tables from the csv files.

``` r
library(DBI)

con <- dbConnect(RSQLite::SQLite(), dbname = ":memory:")

dbWriteTable(con, "brands", read.csv("~/GitHub/online-sports-revenue/files/brands.csv"))
dbWriteTable(con, "finance", read.csv("~/GitHub/online-sports-revenue/files/finance.csv"))
dbWriteTable(con, "info", read.csv("~/GitHub/online-sports-revenue/files/info.csv"))
dbWriteTable(con, "reviews", read.csv("~/GitHub/online-sports-revenue/files/reviews.csv"))
```

Here is a look at the first 5 entries in each table:

``` sql
SELECT * FROM brands LIMIT 5;
```

<div class="knitsql-table">

| product_id | brand  |
|:-----------|:-------|
| AH2430     |        |
| G27341     | Adidas |
| CM0081     | Adidas |
| B44832     | Adidas |
| D98205     | Adidas |

5 records

</div>

``` sql
SELECT * FROM finance LIMIT 5;
```

<div class="knitsql-table">

| product_id | listing_price | sale_price | discount | revenue |
|:-----------|--------------:|-----------:|---------:|--------:|
| AH2430     |            NA |         NA |       NA |      NA |
| G27341     |         75.99 |      37.99 |      0.5 | 1641.17 |
| CM0081     |          9.99 |       5.99 |      0.4 |  398.93 |
| B44832     |         69.99 |      34.99 |      0.5 | 2204.37 |
| D98205     |         79.99 |      39.99 |      0.5 | 5182.70 |

5 records

</div>

``` sql
SELECT * FROM info LIMIT 5;
```

<div class="knitsql-table">

| product_name                                     | product_id | description                                                                                                                                                                                                                                                                                      |
|:-------------------------------------------------|:-----------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|                                                  | AH2430     |                                                                                                                                                                                                                                                                                                  |
| Women’s adidas Originals Sleek Shoes             | G27341     | A modern take on adidas sport heritage, tailored just for women. Perforated 3-Stripes on the leather upper of these shoes offer a sleek look that mirrors iconic tennis styles.                                                                                                                  |
| Women’s adidas Swim Puka Slippers                | CM0081     | These adidas Puka slippers for women’s come with slim straps for a great fit. Feature performance logo on the footbed and textured Rubber outsole that gives unique comfort.                                                                                                                     |
| Women’s adidas Sport Inspired Questar Ride Shoes | B44832     | Inspired by modern tech runners, these women’s shoes step out with unexpected style. They’re built with a breathable knit upper, while the heel offers the extra support of an Achilles-hugging design. The cushioned midsole provides a soft landing with every stride.                         |
| Women’s adidas Originals Taekwondo Shoes         | D98205     | This design is inspired by vintage Taekwondo styles originally worn to perfect high kicks and rapid foot strikes. The canvas shoes make a streetwear fashion statement as a chic, foot-hugging slip-on. They’re shaped for a narrow, women’s-specific fit and ride on a soft gum rubber outsole. |

5 records

</div>

``` sql
SELECT * FROM reviews LIMIT 5;
```

<div class="knitsql-table">

| product_id | rating | reviews |
|:-----------|-------:|--------:|
| AH2430     |     NA |      NA |
| G27341     |    3.3 |      24 |
| CM0081     |    2.6 |      37 |
| B44832     |    4.1 |      35 |
| D98205     |    3.5 |      72 |

5 records

</div>

### Joining tables

In one query, I join the tables by their common column, “product_id”. I
do not want to include rows with empty values and NA, such that I
exclude them in the query.

``` sql
SELECT brands.product_id, brand, product_name, listing_price, sale_price, discount, revenue, rating, reviews
FROM (((brands 
INNER JOIN info ON brands.product_id = info.product_id)
INNER JOIN finance ON brands.product_id = finance.product_id)
INNER JOIN reviews ON brands.product_id = reviews.product_id)
WHERE brand != "" OR product_name != "" OR listing_price IS NOT NULL OR sale_price IS NOT NULL OR discount IS NOT NULL OR revenue IS NOT NULL OR rating IS NOT NULL OR reviews IS NOT NULL
LIMIT 10;
```

<div class="knitsql-table">

| product_id | brand  | product_name                                        | listing_price | sale_price | discount | revenue | rating | reviews |
|:-----------|:-------|:----------------------------------------------------|--------------:|-----------:|---------:|--------:|-------:|--------:|
| G27341     | Adidas | Women’s adidas Originals Sleek Shoes                |         75.99 |      37.99 |      0.5 | 1641.17 |    3.3 |      24 |
| CM0081     | Adidas | Women’s adidas Swim Puka Slippers                   |          9.99 |       5.99 |      0.4 |  398.93 |    2.6 |      37 |
| B44832     | Adidas | Women’s adidas Sport Inspired Questar Ride Shoes    |         69.99 |      34.99 |      0.5 | 2204.37 |    4.1 |      35 |
| D98205     | Adidas | Women’s adidas Originals Taekwondo Shoes            |         79.99 |      39.99 |      0.5 | 5182.70 |    3.5 |      72 |
| B75586     | Adidas | Women’s adidas Sport Inspired Duramo Lite 2.0 Shoes |         47.99 |      19.20 |      0.6 | 1555.20 |    1.0 |      45 |
| CG4051     | Adidas | Women’s adidas Sport Inspired Duramo Lite 2.0 Shoes |         47.99 |      23.99 |      0.5 |   86.36 |    4.4 |       2 |
| CM0080     | Adidas | Women’s adidas Swim Puka Slippers                   |          9.99 |       5.99 |      0.4 |   75.47 |    2.8 |       7 |
| B75990     | Adidas | WOMEN’S ADIDAS RUNNING DURAMO 9 SHOES               |         55.99 |      27.99 |      0.5 |  806.11 |    4.5 |      16 |
| EE5761     | Adidas | Men’s adidas Originals Forest Grove Shoes           |         65.99 |      39.59 |      0.4 | 2779.22 |    4.0 |      39 |
| EE4553     | Adidas | Women’s adidas Originals Swift Run Shoes            |         75.99 |      45.59 |      0.4 | 2954.23 |    2.7 |      36 |

Displaying records 1 - 10

</div>

### Total revenue per brand

Now, I group by brand to get the total and average revenue of each.

``` sql
SELECT brand, SUM(revenue) AS "total_revenue", AVG(revenue) AS "average_renevue"
FROM (((brands 
INNER JOIN info ON brands.product_id = info.product_id)
INNER JOIN finance ON brands.product_id = finance.product_id)
INNER JOIN reviews ON brands.product_id = reviews.product_id)
WHERE brand != "" OR product_name != "" OR listing_price IS NOT NULL OR sale_price IS NOT NULL OR discount IS NOT NULL OR revenue IS NOT NULL OR rating IS NOT NULL OR reviews IS NOT NULL
GROUP BY brand
LIMIT 10;
```

<div class="knitsql-table">

| brand  | total_revenue | average_renevue |
|:-------|--------------:|----------------:|
| Adidas |    11526619.1 |        4476.357 |
| Nike   |      802283.3 |        1472.079 |

2 records

</div>

Finally, I select the top 10 revenue from each brand.

### Top 10 revenue: Adidas

``` sql
SELECT brands.product_id, brand, product_name, sale_price, discount, revenue, rating, reviews
FROM (((brands 
INNER JOIN info ON brands.product_id = info.product_id)
INNER JOIN finance ON brands.product_id = finance.product_id)
INNER JOIN reviews ON brands.product_id = reviews.product_id)
WHERE brand = "Adidas" AND (product_name != "" OR listing_price IS NOT NULL OR sale_price IS NOT NULL OR discount IS NOT NULL OR revenue IS NOT NULL OR rating IS NOT NULL OR reviews IS NOT NULL)
ORDER BY revenue DESC
LIMIT 10;
```

<div class="knitsql-table">

| product_id | brand  | product_name                                                   | sale_price | discount |  revenue | rating | reviews |
|:-----------|:-------|:---------------------------------------------------------------|-----------:|---------:|---------:|-------:|--------:|
| FV7826     | Adidas | Unisex Originals CRAIG GREEN KONTUUR II SHOES                  |     239.99 |        0 | 37150.45 |    2.4 |      86 |
| FV6794     | Adidas | Unisex Originals CRAIG GREEN KONTUUR I SHOES                   |     239.99 |        0 | 34990.54 |    4.1 |      81 |
| EG5185     | Adidas | Men’s adidas Running Universal Works Ultraboost 19 Shoes       |     199.99 |        0 | 33838.31 |    3.9 |      94 |
| EF9623     | Adidas | Men’s adidas Originals ZX 4000 4D Shoes                        |     279.99 |        0 | 31246.88 |    3.1 |      62 |
| EF0893     | Adidas | Men’s Running Ultraboost PB Shoes                              |     179.99 |        0 | 30454.31 |    2.6 |      94 |
| EG0761     | Adidas | Women’s Running Ultraboost 20 Shoes                            |     169.99 |        0 | 29986.24 |    2.8 |      98 |
| EF2335     | Adidas | UNISEX adidas Originals Pharrell Williams Hu NMD Proud Shoes   |     219.99 |        0 | 29698.65 |    2.7 |      75 |
| EG1071     | Adidas | Women’s adidas by Stella Mccartney Running UltraBoost 20 Shoes |     179.99 |        0 | 28834.40 |    3.5 |      89 |
| EG5933     | Adidas | Men’s Originals Salvapor 032c Shoes                            |     169.99 |        0 | 28762.31 |    4.9 |      94 |
| BD7865     | Adidas | Men’s adidas Originals ZX 4000 4D Shoes                        |     299.99 |        0 | 28619.05 |    4.0 |      53 |

Displaying records 1 - 10

</div>

### Top 10 revenue: Nike

``` sql
SELECT brands.product_id, brand, product_name, sale_price, discount, revenue, rating, reviews
FROM (((brands 
INNER JOIN info ON brands.product_id = info.product_id)
INNER JOIN finance ON brands.product_id = finance.product_id)
INNER JOIN reviews ON brands.product_id = reviews.product_id)
WHERE brand = 'Nike' AND (product_name != "" OR listing_price IS NOT NULL OR sale_price IS NOT NULL OR discount IS NOT NULL OR revenue IS NOT NULL OR rating IS NOT NULL OR reviews IS NOT NULL)
ORDER BY revenue DESC
LIMIT 10;
```

<div class="knitsql-table">

| product_id | brand | product_name                    | sale_price | discount |  revenue | rating | reviews |
|:-----------|:------|:--------------------------------|-----------:|---------:|---------:|-------:|--------:|
| 310805-137 | Nike  | Air Jordan 10 Retro             |     159.95 |        0 | 64203.93 |    4.7 |     223 |
| 880848-005 | Nike  | Nike Zoom Fly                   |     144.95 |        0 | 27395.55 |    4.3 |     105 |
| 849559-004 | Nike  | Nike Air Max 2017               |     159.95 |        0 | 20441.61 |    2.9 |      71 |
| AH8050-100 | Nike  | Nike Air Max 270                |     139.95 |        0 | 20404.71 |    3.8 |      81 |
| CD4371-001 | Nike  | Nike React Infinity Run Flyknit |     139.95 |        0 | 17129.88 |    4.6 |      68 |
| AO4568-600 | Nike  | Nike ZoomX Vaporfly NEXT%       |     199.95 |        0 | 16195.95 |    4.7 |      45 |
| AH6789-023 | Nike  | Nike Air Max 270                |     139.95 |        0 | 15870.33 |    3.9 |      63 |
| AT2863-100 | Nike  | Nike Zoom Pegasus Turbo 2       |     159.95 |        0 | 15547.14 |    3.1 |      54 |
| AJ6900-102 | Nike  | Nike Air VaporMax Flyknit 3     |     169.95 |        0 | 15295.50 |    3.7 |      50 |
| AJ6910-009 | Nike  | Nike Air VaporMax Flyknit 3     |     169.95 |        0 | 14683.68 |    4.4 |      48 |

Displaying records 1 - 10

</div>
