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
library(stringr)
```




```r
# load data
df_cs <- read_csv("data/cs_sample_100.csv",, col_types = cols(.default = "c")) %>%
  clean_names() %>%
  rename(ttf_category = in_current_subs,
         citation = together) %>%
  mutate(ttf_category = tolower(ttf_category),
         ttf_category = ifelse(ttf_category == "na", NA, ttf_category)  )

  
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

head(tidy_cs)
```

```
## # A tibble: 6 x 4
##   field ttf_category  ttf_value citation                                   
##   <chr> <chr>             <dbl> <chr>                                      
## 1 cs    open access         0.1 Conference Paper__Advances in Neural Infor…
## 2 cs    <NA>               NA   Article__Google Image Search for CEO Has B…
## 3 cs    licensed            0.1 Article__Linguistic Inquiry__1977          
## 4 cs    borrow direct      48   Article__Estimation and Tracking: Principl…
## 5 cs    licensed            0.1 Conference Paper__Proceedings of the ACM S…
## 6 cs    licensed            0.1 Conference Paper__Proceedings of SPIE - Th…
```

```r
df_ilr <- read_excel("data/ilr_sample_100.xlsx", 
    col_types = c("text", "text", "text", 
        "text", "numeric", "text", "text", 
        "text", "text", "text", "text", "text", 
        "text", "text", "text", "text", "text", 
        "text", "text", "text", "text")) %>%
  clean_names() %>%
  rename(ttf_category = source,
         citation = together) %>%
  mutate(ttf_category = tolower(ttf_category),
         ttf_category = ifelse(ttf_category == "na", NA, ttf_category)  )


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

head(tidy_ilr)
```

```
## # A tibble: 6 x 4
##   field ttf_category ttf_value citation                                    
##   <chr> <chr>            <dbl> <chr>                                       
## 1 ilr   open access        0.1 Article__Journal of Labor Economics__1997   
## 2 ilr   licensed           0.1 Article__International Journal of Sociology…
## 3 ilr   <NA>              NA   Article__NA__0000                           
## 4 ilr   licensed           0.1 Article__Annual Review of Economics__2009   
## 5 ilr   licensed           0.1 Article__Doctoral Education in the Humaniti…
## 6 ilr   licensed           0.1 Article__Personality and Social Psychology …
```




```r
# explore

df <- rbind(tidy_cs, tidy_ilr)

df %>%
  filter(!is.na(ttf_category)) %>%
  group_by(field) %>%
  summarize(n = n(),
            sum_ttf_value= sum(ttf_value,  na.rm = TRUE))
```

```
## # A tibble: 2 x 3
##   field     n sum_ttf_value
##   <chr> <int>         <dbl>
## 1 cs       95          252.
## 2 ilr      96          379
```

```r
df %>%
  filter(!is.na(ttf_category)) %>%
  group_by(field, ttf_category) %>%
  summarize(n = n(),
            sum_ttf_value = sum(ttf_value,  na.rm = TRUE))
```

```
## # A tibble: 9 x 4
## # Groups:   field [?]
##   field ttf_category      n sum_ttf_value
##   <chr> <chr>         <int>         <dbl>
## 1 cs    borrow direct     2          96  
## 2 cs    campus            3           3  
## 3 cs    ill               2         144  
## 4 cs    licensed         58           5.8
## 5 cs    open access      30           3  
## 6 ilr   campus           11          11  
## 7 ilr   ill               5         360  
## 8 ilr   licensed         47           4.7
## 9 ilr   open access      33           3.3
```



