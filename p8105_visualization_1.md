Data Visualization
================
AJ Catalano
11/7/2021

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.1 ──

    ## ✓ ggplot2 3.3.5     ✓ purrr   0.3.4
    ## ✓ tibble  3.1.5     ✓ dplyr   1.0.7
    ## ✓ tidyr   1.1.4     ✓ stringr 1.4.0
    ## ✓ readr   2.0.2     ✓ forcats 0.5.1

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(ggridges)
```

Exploratory analysis may include:

-   visualization
-   checks for data completeness and reliablity
-   quantification of centrality and variabilty
-   initial evaluation of hypotheses
-   hypothesis generation

**Basic graph components:**

-   data
-   aesthetic mapping - what variables map to what features of the graph
    (e.g. axes, point size, colors)
-   geom - visual representaion of data (type of plot)

**Advanced graph components:**

-   facets - multiple panels
-   scales
-   statistics

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USC00519397", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2017-01-01",
    date_max = "2017-12-31") %>%
  mutate(
    name = recode(
      id, 
      USW00094728 = "CentralPark_NY", 
      USC00519397 = "Waikiki_HA",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2021-11-08 08:56:20 (7.611)

    ## file min/max dates: 1869-01-01 / 2021-11-30

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USC00519397.dly

    ## date created (size, mb): 2021-11-08 08:56:26 (1.697)

    ## file min/max dates: 1965-01-01 / 2020-02-29

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2021-11-08 08:56:29 (0.916)

    ## file min/max dates: 1999-09-01 / 2021-11-30

## Scatterplots

``` r
ggplot(
  weather_df, aes(x = tmin, y = tmax)) + 
    geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](p8105_visualization_1_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

``` r
# different approach to same plot:

weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](p8105_visualization_1_files/figure-gfm/unnamed-chunk-3-2.png)<!-- -->

Saving and editing plot object:

``` r
# first:

weather_plot =
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax))

# then:

weather_plot + geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](p8105_visualization_1_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->
