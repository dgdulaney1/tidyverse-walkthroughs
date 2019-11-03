\_if, \_at, \_all versions of summarise(), rename(), and mutate()
================

**Background:** Summarise(), rename(), and mutate() are core tidyverse functions that allow us to calculate aggregate values, rename functions, and create new columns.

They also have alternate versions with the suffixes \_if, \_at, and \_all that allow you to use these functions on several variables at a time.

``` r
df <- data.frame("col1" = runif(100),
                 "col2" = runif(100),
                 "col3" = runif(100),
                 "col4" = sample(letters, 100, replace = TRUE))

head(df)
```

    ##        col1      col2      col3 col4
    ## 1 0.6592506 0.1308153 0.7058951    e
    ## 2 0.4154190 0.9131435 0.4273353    t
    ## 3 0.3326926 0.2235163 0.6329849    y
    ## 4 0.8938037 0.1310420 0.3042157    o
    ## 5 0.2643646 0.6746525 0.9750811    t
    ## 6 0.6688890 0.8774266 0.4689334    n

The \_if versions of these functions allow you to work with columns that meet a specific condition, e.g. only those that are numeric.

``` r
df %>% 
  summarise_if(is.numeric, mean) %>% 
  head()
```

    ##        col1      col2      col3
    ## 1 0.4861627 0.5323345 0.5426485

``` r
df %>% 
  rename_if(is.numeric, function(x) paste0("new_", x)) %>% 
  head()
```

    ##    new_col1  new_col2  new_col3 col4
    ## 1 0.6592506 0.1308153 0.7058951    e
    ## 2 0.4154190 0.9131435 0.4273353    t
    ## 3 0.3326926 0.2235163 0.6329849    y
    ## 4 0.8938037 0.1310420 0.3042157    o
    ## 5 0.2643646 0.6746525 0.9750811    t
    ## 6 0.6688890 0.8774266 0.4689334    n

``` r
times_100 <- function(x) x * 100

df %>% 
  mutate_if(is.numeric, list(times_100 = times_100)) %>% 
  head()
```

    ##        col1      col2      col3 col4 col1_times_100 col2_times_100
    ## 1 0.6592506 0.1308153 0.7058951    e       65.92506       13.08153
    ## 2 0.4154190 0.9131435 0.4273353    t       41.54190       91.31435
    ## 3 0.3326926 0.2235163 0.6329849    y       33.26926       22.35163
    ## 4 0.8938037 0.1310420 0.3042157    o       89.38037       13.10420
    ## 5 0.2643646 0.6746525 0.9750811    t       26.43646       67.46525
    ## 6 0.6688890 0.8774266 0.4689334    n       66.88890       87.74266
    ##   col3_times_100
    ## 1       70.58951
    ## 2       42.73353
    ## 3       63.29849
    ## 4       30.42157
    ## 5       97.50811
    ## 6       46.89334

------------------------------------------------------------------------

The \_at versions of these functions allow you to

The \_all versions of these functions allow you to

``` r
df %>% 
  select(col1:col3) %>% # selecting numeric columns only
  gather(col, val, col1:col3) %>% # reshaping data from wide to long
  group_by(col) %>% 
  summarise_all(list(min = min, mean = mean, max = max, sd = sd)) %>% 
  head()
```

    ## # A tibble: 3 x 5
    ##   col       min  mean   max    sd
    ##   <chr>   <dbl> <dbl> <dbl> <dbl>
    ## 1 col1  0.00653 0.486 0.991 0.291
    ## 2 col2  0.0246  0.532 0.984 0.308
    ## 3 col3  0.00125 0.543 0.998 0.309

``` r
times_100 <- function(x) x * 100

df %>% 
  select(col1:col3) %>% # selecting numeric columns only
  mutate_all(funs(times_100 = times_100)) %>% 
  head()
```

    ## Warning: funs() is soft deprecated as of dplyr 0.8.0
    ## please use list() instead
    ## 
    ##   # Before:
    ##   funs(name = f(.))
    ## 
    ##   # After: 
    ##   list(name = ~ f(.))
    ## This warning is displayed once per session.

    ##        col1      col2      col3 col1_times_100 col2_times_100
    ## 1 0.6592506 0.1308153 0.7058951       65.92506       13.08153
    ## 2 0.4154190 0.9131435 0.4273353       41.54190       91.31435
    ## 3 0.3326926 0.2235163 0.6329849       33.26926       22.35163
    ## 4 0.8938037 0.1310420 0.3042157       89.38037       13.10420
    ## 5 0.2643646 0.6746525 0.9750811       26.43646       67.46525
    ## 6 0.6688890 0.8774266 0.4689334       66.88890       87.74266
    ##   col3_times_100
    ## 1       70.58951
    ## 2       42.73353
    ## 3       63.29849
    ## 4       30.42157
    ## 5       97.50811
    ## 6       46.89334
