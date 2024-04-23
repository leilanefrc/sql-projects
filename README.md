SQL Projects
================

This is a repository for my SQL projects.

In all projects, I use R (R Markdown) with the packages `DBI` and
`RSQLite` to create a SQL database in the computer’s memory. Then, I use
the function `dbWriteTables()` to write the tables I want using data
files (a csv file in the example below):

``` r
library(DBI)

con <- dbConnect(RSQLite::SQLite(), dbname = ":memory:")

dbWriteTable(con, "table_name", read.csv("file_name.csv"))
```

## 1. Online Sports Revenue

<https://github.com/leilanefrc/sql-projects/blob/main/online-sports-revenue.md>
