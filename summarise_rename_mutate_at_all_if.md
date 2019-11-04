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

    ##         col1       col2       col3 col4
    ## 1 0.02847733 0.76116557 0.38439998    v
    ## 2 0.60299920 0.73960866 0.08908891    z
    ## 3 0.12689284 0.19197360 0.91432255    d
    ## 4 0.16631687 0.01602532 0.98274516    i
    ## 5 0.67407956 0.59174452 0.37707009    k
    ## 6 0.99640697 0.42855371 0.04705588    n

------------------------------------------------------------------------

The \_if versions of these functions allow you to work with columns that meet a specific condition, e.g. only those that are numeric.

``` r
df %>% 
  summarise_if(is.numeric, mean) %>% 
  head()
```

    ##        col1      col2      col3
    ## 1 0.5244733 0.4524624 0.5074139

``` r
df %>% 
  rename_if(is.numeric, function(x) paste0("new_", x)) %>% 
  head()
```

    ##     new_col1   new_col2   new_col3 col4
    ## 1 0.02847733 0.76116557 0.38439998    v
    ## 2 0.60299920 0.73960866 0.08908891    z
    ## 3 0.12689284 0.19197360 0.91432255    d
    ## 4 0.16631687 0.01602532 0.98274516    i
    ## 5 0.67407956 0.59174452 0.37707009    k
    ## 6 0.99640697 0.42855371 0.04705588    n

``` r
times_100 <- function(x) x * 100

df %>% 
  mutate_if(is.numeric, list(times_100 = times_100)) %>% 
  head()
```

    ##         col1       col2       col3 col4 col1_times_100 col2_times_100
    ## 1 0.02847733 0.76116557 0.38439998    v       2.847733      76.116557
    ## 2 0.60299920 0.73960866 0.08908891    z      60.299920      73.960866
    ## 3 0.12689284 0.19197360 0.91432255    d      12.689284      19.197360
    ## 4 0.16631687 0.01602532 0.98274516    i      16.631687       1.602532
    ## 5 0.67407956 0.59174452 0.37707009    k      67.407956      59.174452
    ## 6 0.99640697 0.42855371 0.04705588    n      99.640697      42.855371
    ##   col3_times_100
    ## 1      38.439998
    ## 2       8.908891
    ## 3      91.432255
    ## 4      98.274516
    ## 5      37.707009
    ## 6       4.705588

------------------------------------------------------------------------

The \_at versions of these functions allow you to manually specify which columns to work with.

``` r
df %>% 
  summarise_at(vars(col1, col2), mean) %>% 
  head()
```

    ##        col1      col2
    ## 1 0.5244733 0.4524624

``` r
df %>% 
  rename_at(vars(col1, col3), function(x) paste0("new_", x)) %>% 
  head()
```

    ##     new_col1       col2   new_col3 col4
    ## 1 0.02847733 0.76116557 0.38439998    v
    ## 2 0.60299920 0.73960866 0.08908891    z
    ## 3 0.12689284 0.19197360 0.91432255    d
    ## 4 0.16631687 0.01602532 0.98274516    i
    ## 5 0.67407956 0.59174452 0.37707009    k
    ## 6 0.99640697 0.42855371 0.04705588    n

``` r
df %>% 
  mutate_at(vars(col2, col3), list(times_100 = times_100)) %>% 
  head()
```

    ##         col1       col2       col3 col4 col2_times_100 col3_times_100
    ## 1 0.02847733 0.76116557 0.38439998    v      76.116557      38.439998
    ## 2 0.60299920 0.73960866 0.08908891    z      73.960866       8.908891
    ## 3 0.12689284 0.19197360 0.91432255    d      19.197360      91.432255
    ## 4 0.16631687 0.01602532 0.98274516    i       1.602532      98.274516
    ## 5 0.67407956 0.59174452 0.37707009    k      59.174452      37.707009
    ## 6 0.99640697 0.42855371 0.04705588    n      42.855371       4.705588

------------------------------------------------------------------------

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
    ##   col        min  mean   max    sd
    ##   <chr>    <dbl> <dbl> <dbl> <dbl>
    ## 1 col1  0.000261 0.524 0.997 0.297
    ## 2 col2  0.00850  0.452 0.985 0.277
    ## 3 col3  0.00862  0.507 0.995 0.287

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

    ##         col1       col2       col3 col1_times_100 col2_times_100
    ## 1 0.02847733 0.76116557 0.38439998       2.847733      76.116557
    ## 2 0.60299920 0.73960866 0.08908891      60.299920      73.960866
    ## 3 0.12689284 0.19197360 0.91432255      12.689284      19.197360
    ## 4 0.16631687 0.01602532 0.98274516      16.631687       1.602532
    ## 5 0.67407956 0.59174452 0.37707009      67.407956      59.174452
    ## 6 0.99640697 0.42855371 0.04705588      99.640697      42.855371
    ##   col3_times_100
    ## 1      38.439998
    ## 2       8.908891
    ## 3      91.432255
    ## 4      98.274516
    ## 5      37.707009
    ## 6       4.705588
