
<!-- README.md is generated from README.Rmd. Please edit that file -->

# multidplyrio

<!-- badges: start -->

[![Lifecycle:
experimental](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://lifecycle.r-lib.org/articles/stages.html#experimental)
[![License](https://img.shields.io/badge/LICENSE-MIT-green.svg)](https://mit-license.org/)
[![R-CMD-check](https://github.com/isaac-florence/multidplyrio/workflows/R-CMD-check/badge.svg)](https://github.com/isaac-florence/multidplyrio/actions)
<!-- badges: end -->

This **multidplyr extension for I/O** package is an experimental
extension to the **multdidplyr** package by Hadley. **multidplyr** is a
framework for manipulating data distributed over several worker r
sessions using **dplyr** (tidyverse) syntax. Read more about
**multdidplyr** here:
[multidplyr.tidyverse.org](https://multidplyr.tidyverse.org)

**multidplyr** is fantastic for reducing time taken for lengthy
workloads, but needs to read data centrally before distributing to
workers for parallel processing, as well as reloading centrally before
writing to disk or SQL databases (for example). This mini extension
package provide functions that remove or reduce this issues.

These include:  
- Reading a csv file directly into workers  
- Writing to a csv file sequentially across workers  
- Reading data from a SQL database directly to workers  
- Writing to a SQL database table in parallel

## Installation

This package is in development and so is installable from GitHub using
**devtools**

``` r
devtools::install_github("https://github.com/isaac-florence/multidplyrio")
```

## Example

``` r
library(multidplyr)
library(multidplyrio)

# file path to a massive delimited file
file <- "my_massive_linelist.csv"

# creates one worker for each core
cluster <- new_cluster(parallel::detectCores())

# partitioned/distributed data frame that exists across the workers in `cluster`
party_df <- read_delim_distrib(file = file, cluster = cluster)

# The rows in the csv file are now evenly distributed across the workers ready for normal multidplyr manipulation,
# or summarising and bringing to the master/main session with `dplyr::collect()`
df <- party_df %>%
  mutate(date = as.Date(date)) %>% 
  filter(date >= as.Date("2021-01-01")) %>% 
  summarise(count = n(), average = mean()) %>% 
  collect()
```
