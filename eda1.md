---
title: "eda1"
author: "Adam Chandler"
date: "January 29, 2019"
output: 
  html_document: 
    keep_md: yes
---





```r
# load libraries
library(tidyverse)
```

```
## ── Attaching packages ────────────────────────────────── tidyverse 1.2.1 ──
```

```
## ✔ ggplot2 3.1.0     ✔ purrr   0.2.5
## ✔ tibble  2.0.0     ✔ dplyr   0.7.8
## ✔ tidyr   0.8.2     ✔ stringr 1.3.1
## ✔ readr   1.3.1     ✔ forcats 0.3.0
```

```
## ── Conflicts ───────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
```

```r
library(readxl)
library(janitor)
```




```r
# load data
df_cs <- read_csv("data/cs_sample_100.csv",, col_types = cols(.default = "c")) %>%
  clean_names() %>%
  rename(ttf_category = in_current_subs,
         citation = together)
  
tidy_cs <- df_cs %>%
  mutate(ttf_value = case_when(
    ttf_category == "open access" ~ 0.1,
    ttf_category == "licensed" ~ 0.1,
    ttf_category == "campus" ~ 1,
    ttf_category == "annex" ~ 24,
    ttf_category == "borrow direct" ~ 48,
    ttf_category == "ill" ~ 72) ) %>%
    mutate(field = "cs") %>%
  select(field, ttf_category, ttf_value, citation) 

  
df_ilr <- read_excel("data/ilr_sample_100.xlsx", 
    col_types = c("text", "text", "text", 
        "text", "numeric", "text", "text", 
        "text", "text", "text", "text", "text", 
        "text", "text", "text", "text", "text", 
        "text", "text", "text", "text")) %>%
  clean_names() %>%
  rename(ttf_category = source,
         citation = together)

tidy_ilr <- df_ilr %>%
  mutate(ttf_value = case_when(
    ttf_category == "open access" ~ 0.1,
    ttf_category == "licensed" ~ 0.1,
    ttf_category == "campus" ~ 1,
    ttf_category == "annex" ~ 24,
    ttf_category == "borrow direct" ~ 48,
    ttf_category == "ill" ~ 72) ) %>%
  mutate(field = "ilr") %>%
  select(field, ttf_category, ttf_value, citation) 
```




```r
# explore

df <- rbind(tidy_cs, tidy_ilr)

df %>%
  group_by(field, ttf_category) %>%
  summarize(n = n(),
            sum_ttf_value_sum = sum(ttf_value,  na.rm = TRUE))
```

```
## # A tibble: 11 x 4
## # Groups:   field [?]
##    field ttf_category      n sum_ttf_value_sum
##    <chr> <chr>         <int>             <dbl>
##  1 cs    borrow direct     2              96  
##  2 cs    campus            3               3  
##  3 cs    ILL               2               0  
##  4 cs    licensed         58               5.8
##  5 cs    open access      30               3  
##  6 cs    <NA>              5               0  
##  7 ilr   campus           11              11  
##  8 ilr   ILL               5               0  
##  9 ilr   licensed         47               4.7
## 10 ilr   NA                4               0  
## 11 ilr   open access      33               3.3
```



