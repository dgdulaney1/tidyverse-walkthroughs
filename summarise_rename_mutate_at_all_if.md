\_if, \_at, \_all versions of summarise(), rename(), and mutate()
================

Summarise(), rename(), and mutate() are core tidyverse functions that allow you to calculate aggregate values, rename functions, and create new columns.

They also have alternate versions with the suffixes \_if, \_at, and \_all that allow you to use these functions on several variables at a time.

``` r
df <- data.frame("col1" = runif(100),
                 "col2" = runif(100),
                 "col3" = runif(100),
                 "col4" = sample(letters, 100, replace = TRUE))

head(df)
```

    ##        col1       col2      col3 col4
    ## 1 0.2441653 0.93185941 0.8994790    d
    ## 2 0.5431627 0.03657278 0.1175429    x
    ## 3 0.6791824 0.72903068 0.6232248    j
    ## 4 0.6291522 0.18005678 0.2740330    n
    ## 5 0.4365116 0.38738835 0.3535608    i
    ## 6 0.5929938 0.13491731 0.3902710    q

------------------------------------------------------------------------

The \_if versions of these functions allow you to work with columns that meet a specific condition, e.g. only those that are numeric.

``` r
df %>% 
  summarise_if(is.numeric, mean) %>% 
  head()
```

    ##        col1      col2      col3
    ## 1 0.4774359 0.4985068 0.5043999

``` r
df %>% 
  rename_if(is.numeric, function(x) paste0("new_", x)) %>% 
  head()
```

    ##    new_col1   new_col2  new_col3 col4
    ## 1 0.2441653 0.93185941 0.8994790    d
    ## 2 0.5431627 0.03657278 0.1175429    x
    ## 3 0.6791824 0.72903068 0.6232248    j
    ## 4 0.6291522 0.18005678 0.2740330    n
    ## 5 0.4365116 0.38738835 0.3535608    i
    ## 6 0.5929938 0.13491731 0.3902710    q

``` r
times_100 <- function(x) x * 100

df %>% 
  mutate_if(is.numeric, list(times_100 = times_100)) %>% 
  head()
```

    ##        col1       col2      col3 col4 col1_times_100 col2_times_100
    ## 1 0.2441653 0.93185941 0.8994790    d       24.41653      93.185941
    ## 2 0.5431627 0.03657278 0.1175429    x       54.31627       3.657278
    ## 3 0.6791824 0.72903068 0.6232248    j       67.91824      72.903068
    ## 4 0.6291522 0.18005678 0.2740330    n       62.91522      18.005678
    ## 5 0.4365116 0.38738835 0.3535608    i       43.65116      38.738835
    ## 6 0.5929938 0.13491731 0.3902710    q       59.29938      13.491731
    ##   col3_times_100
    ## 1       89.94790
    ## 2       11.75429
    ## 3       62.32248
    ## 4       27.40330
    ## 5       35.35608
    ## 6       39.02710

------------------------------------------------------------------------

The \_at versions of these functions allow you to manually specify which columns to work with.

``` r
df %>% 
  summarise_at(vars(col1, col2), mean) %>% 
  head()
```

    ##        col1      col2
    ## 1 0.4774359 0.4985068

``` r
df %>% 
  rename_at(vars(col1, col3), function(x) paste0("new_", x)) %>% 
  head()
```

    ##    new_col1       col2  new_col3 col4
    ## 1 0.2441653 0.93185941 0.8994790    d
    ## 2 0.5431627 0.03657278 0.1175429    x
    ## 3 0.6791824 0.72903068 0.6232248    j
    ## 4 0.6291522 0.18005678 0.2740330    n
    ## 5 0.4365116 0.38738835 0.3535608    i
    ## 6 0.5929938 0.13491731 0.3902710    q

``` r
df %>% 
  mutate_at(vars(col2, col3), list(times_100 = times_100)) %>% 
  head()
```

    ##        col1       col2      col3 col4 col2_times_100 col3_times_100
    ## 1 0.2441653 0.93185941 0.8994790    d      93.185941       89.94790
    ## 2 0.5431627 0.03657278 0.1175429    x       3.657278       11.75429
    ## 3 0.6791824 0.72903068 0.6232248    j      72.903068       62.32248
    ## 4 0.6291522 0.18005678 0.2740330    n      18.005678       27.40330
    ## 5 0.4365116 0.38738835 0.3535608    i      38.738835       35.35608
    ## 6 0.5929938 0.13491731 0.3902710    q      13.491731       39.02710

------------------------------------------------------------------------

The \_all versions of these functions allow you to work with all columns at once.

``` r
df %>% 
  select(col1:col3) %>% # selecting numeric columns only
  gather(col, val, col1:col3) %>% # reshaping data from wide to long
  group_by(col) %>% 
  summarise_all(list(min = min, mean = mean, max = max, std_dev = sd)) %>% 
  head()
```

    ## # A tibble: 3 x 5
    ##   col       min  mean   max std_dev
    ##   <chr>   <dbl> <dbl> <dbl>   <dbl>
    ## 1 col1  0.0138  0.477 0.992   0.285
    ## 2 col2  0.00754 0.499 0.990   0.298
    ## 3 col3  0.00408 0.504 0.982   0.285

``` r
df %>% 
  rename_all(function(x) paste0("new_", x)) %>% 
  head()
```

    ##    new_col1   new_col2  new_col3 new_col4
    ## 1 0.2441653 0.93185941 0.8994790        d
    ## 2 0.5431627 0.03657278 0.1175429        x
    ## 3 0.6791824 0.72903068 0.6232248        j
    ## 4 0.6291522 0.18005678 0.2740330        n
    ## 5 0.4365116 0.38738835 0.3535608        i
    ## 6 0.5929938 0.13491731 0.3902710        q

``` r
df %>% 
  select(col1:col3) %>% # selecting numeric columns only
  mutate_all(list(times_100 = times_100, mean = mean)) %>% 
  head()
```

    ##        col1       col2      col3 col1_times_100 col2_times_100
    ## 1 0.2441653 0.93185941 0.8994790       24.41653      93.185941
    ## 2 0.5431627 0.03657278 0.1175429       54.31627       3.657278
    ## 3 0.6791824 0.72903068 0.6232248       67.91824      72.903068
    ## 4 0.6291522 0.18005678 0.2740330       62.91522      18.005678
    ## 5 0.4365116 0.38738835 0.3535608       43.65116      38.738835
    ## 6 0.5929938 0.13491731 0.3902710       59.29938      13.491731
    ##   col3_times_100 col1_mean col2_mean col3_mean
    ## 1       89.94790 0.4774359 0.4985068 0.5043999
    ## 2       11.75429 0.4774359 0.4985068 0.5043999
    ## 3       62.32248 0.4774359 0.4985068 0.5043999
    ## 4       27.40330 0.4774359 0.4985068 0.5043999
    ## 5       35.35608 0.4774359 0.4985068 0.5043999
    ## 6       39.02710 0.4774359 0.4985068 0.5043999
