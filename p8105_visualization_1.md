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

## Advanced scatterplots

``` r
# adding color and smooth trend lines

weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point() + 
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](p8105_visualization_1_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

What about `aes` placement?

``` r
# note that placing the color aes within `geom_point` places a single smooth trend line through the scatterplot. R is reading the color aes within the whole scatterplot

weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point(aes(color = name)) + 
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](p8105_visualization_1_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

## Faceting

``` r
# `facet_grid` first argument = row definition second argument = column definition

weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.2, size = 0.3) + 
  geom_smooth(se = FALSE, size = 2) +
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](p8105_visualization_1_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

``` r
# below alpha = tmin specifies a color gradient mapped to variable tmin

weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, alpha = tmin, color = name)) +
  geom_point() + 
  geom_smooth(se = FALSE) +
  facet_grid(name ~ .)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](p8105_visualization_1_files/figure-gfm/unnamed-chunk-7-2.png)<!-- -->

Combining elements:

``` r
# size of points is mapped to precipitation amount

weather_df %>% 
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point(aes(size = prcp), alpha = 0.3) +
  geom_smooth(se = FALSE) +
  facet_grid(. ~ name) +
  theme_classic()
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](p8105_visualization_1_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

How many geoms must exist?

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

![](p8105_visualization_1_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

Neat geoms exist!

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_hex()
```

    ## Warning: Removed 15 rows containing non-finite values (stat_binhex).

![](p8105_visualization_1_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_density_2d() +
  geom_point(alpha = 0.3)
```

    ## Warning: Removed 15 rows containing non-finite values (stat_density2d).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](p8105_visualization_1_files/figure-gfm/unnamed-chunk-10-2.png)<!-- -->

## Univariate plots

Histograms

``` r
weather_df %>% 
  ggplot(aes(x = tmin)) +
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 15 rows containing non-finite values (stat_bin).

![](p8105_visualization_1_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

Adding color and stuff (can be weird):

``` r
weather_df %>% 
  ggplot(aes(x = tmin, fill = name)) +
  geom_histogram(position = "dodge")
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 15 rows containing non-finite values (stat_bin).

![](p8105_visualization_1_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

``` r
# using facets can make multiple levels easier to see

weather_df %>% 
  ggplot(aes(x = tmin)) +
  geom_histogram() +
  facet_grid(. ~ name)
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 15 rows containing non-finite values (stat_bin).

![](p8105_visualization_1_files/figure-gfm/unnamed-chunk-12-2.png)<!-- -->

A new geometry (density = smoothed histogram, loses outliers - adjust
with `adjust`):

``` r
weather_df %>% 
  ggplot(aes(x = tmin, fill = name)) +
  geom_density(alpha = 0.3, adjust = 0.5)
```

    ## Warning: Removed 15 rows containing non-finite values (stat_density).

![](p8105_visualization_1_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

## Box plots

``` r
weather_df %>% 
  ggplot(aes(x = name, y = tmin)) +
  geom_boxplot()
```

    ## Warning: Removed 15 rows containing non-finite values (stat_boxplot).

![](p8105_visualization_1_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

## Trendy plots

Violin plots:

``` r
# turning your head sideways lets you see densities (makes skewed data more obvious than box plots)

weather_df %>% 
  ggplot(aes(x = name, y = tmin, fill = name)) + 
  geom_violin(alpha = 0.5) +
  stat_summary(fun = median)
```

    ## Warning: Removed 15 rows containing non-finite values (stat_ydensity).

    ## Warning: Removed 15 rows containing non-finite values (stat_summary).

    ## Warning: Removed 3 rows containing missing values (geom_segment).

![](p8105_visualization_1_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

Ridge plots (good for many categories e.g. age distribution by US
state):

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = name)) +
  geom_density_ridges()
```

    ## Picking joint bandwidth of 1.67

    ## Warning: Removed 15 rows containing non-finite values (stat_density_ridges).

![](p8105_visualization_1_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

## Saving and embeding plots

``` r
weather_plot = 
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = 0.5)

ggsave("./results/weather_plot.pdf", weather_plot, width = 8, height = 5)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

Embedding:

``` r
weather_plot
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](p8105_visualization_1_files/figure-gfm/unnamed-chunk-18-1.png)<!-- -->

``` r
weather_plot
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](p8105_visualization_1_files/figure-gfm/unnamed-chunk-19-1.png)<!-- -->
