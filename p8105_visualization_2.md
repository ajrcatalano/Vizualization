Visualization
================
AJ Catalano
11/8/2021

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
library(patchwork)
```

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

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .5)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](p8105_visualization_2_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

## Adding labels

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .5) +
  labs(
    title = "Temperature Plot",
    x = "Minimum Daily Temperature",
    y = "Maximum Daily Temperature",
    caption = "Data from R NOAA package; temperatures in 2017"
    )
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](p8105_visualization_2_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

## Scales

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .5) +
  labs(
    title = "Temperature Plot",
    x = "Minimum Daily Temperature",
    y = "Maximum Daily Temperature",
    caption = "Data from R NOAA package; temperatures in 2017"
    ) + 
  scale_x_continuous(
    breaks = c(-15, 0, 15),
    labels = c("-15 C", "0", "15")
  ) +
  scale_y_continuous(
    trans = "log",
    position = "right"
  )
```

    ## Warning in self$trans$transform(x): NaNs produced

    ## Warning: Transformation introduced infinite values in continuous y-axis

    ## Warning: Removed 90 rows containing missing values (geom_point).

![](p8105_visualization_2_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

#### Color scales

``` r
# playing with color hue and using scale_color_hue to rename key

weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .5) +
  labs(
    title = "Temperature Plot",
    x = "Minimum Daily Temperature",
    y = "Maximum Daily Temperature",
    caption = "Data from R NOAA package; temperatures in 2017"
    ) + 
  scale_color_hue(h = c(100, 200),
                  name = "Location")
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](p8105_visualization_2_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .5) +
  labs(
    title = "Temperature Plot",
    x = "Minimum Daily Temperature",
    y = "Maximum Daily Temperature",
    caption = "Data from R NOAA package; temperatures in 2017"
    ) + 
  viridis::scale_color_viridis(
    name = "Location",
    discrete = TRUE)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](p8105_visualization_2_files/figure-gfm/unnamed-chunk-6-2.png)<!-- -->

## Themes

Moving the legend:

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .5) +
  labs(
    title = "Temperature Plot",
    x = "Minimum Daily Temperature",
    y = "Maximum Daily Temperature",
    caption = "Data from R NOAA package; temperatures in 2017"
    ) + 
  viridis::scale_color_viridis(
    name = "Location",
    discrete = TRUE) + 
  theme(legend.position = "bottom")
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](p8105_visualization_2_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

Changing overall theme:

``` r
# notice how theme(legend.position = "bottom") is ignored:

weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .5) +
  labs(
    title = "Temperature Plot",
    x = "Minimum Daily Temperature",
    y = "Maximum Daily Temperature",
    caption = "Data from R NOAA package; temperatures in 2017"
    ) + 
  viridis::scale_color_viridis(
    name = "Location",
    discrete = TRUE) + 
  theme(legend.position = "bottom") + 
  ggthemes::theme_few()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](p8105_visualization_2_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

``` r
# now it's not ignored

weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .5) +
  labs(
    title = "Temperature Plot",
    x = "Minimum Daily Temperature",
    y = "Maximum Daily Temperature",
    caption = "Data from R NOAA package; temperatures in 2017"
    ) + 
  viridis::scale_color_viridis(
    name = "Location",
    discrete = TRUE) + 
  theme_minimal() + 
  theme(legend.position = "bottom")
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](p8105_visualization_2_files/figure-gfm/unnamed-chunk-8-2.png)<!-- -->

## Setting options

``` r
library(tidyverse)

knitr::opts_chunk$set(
  fig.width = .6,
  fig.asp = .6,
  out.width = "90%"
)

theme_set(theme_minimal() + theme(legend.position = "bottom"))

options(
  ggplot2.continuous.color = "viridis",
  ggplot2.continuous.fill = "viridis"
)

scale_colour_discrete = scale_color_viridis_d
scale_fill_discrete = scale_fill_viridis_d
```

## Data arguments in `geom`

``` r
central_park = 
  weather_df %>%
  filter(name == "CentralPark_NY")

waikiki =
  weather_df %>% 
  filter(name == "Waikiki_HA")

# two geometries that apply to separate datasets

ggplot(data = waikiki, aes(x = date, y = tmax, color = name)) +
  geom_point() +
  geom_line(data = central_park)
```

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](p8105_visualization_2_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

## `patchwork`

Faceting:

``` r
weather_df %>% 
  ggplot(aes(x = tmin, fill = name)) +
  geom_density(alpha = .5) + 
  facet_grid(. ~ name)
```

    ## Warning: Removed 15 rows containing non-finite values (stat_density).

![](p8105_visualization_2_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

Patchwork - multi-panel plots when faceting isn’t feasible:

``` r
tmax_tmin_p = 
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_point(alpha = .5) +
  theme(legend.position = "none")

prcp_dens_p = 
  weather_df %>% 
  filter(prcp > 0) %>% 
  ggplot(aes(x = prcp, fill = name)) +
  geom_density(alpha = .5) +
  theme(legend.position = "none")

tmax_date_p = 
  weather_df %>% 
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point() +
  geom_smooth(se = FALSE) +
  theme(legend.position = "none")

# not working like in lesson - revisit
patchwork::wrap_plots(tmax_date_p, prcp_dens_p)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](p8105_visualization_2_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

## Data manipulation

Controlling factors:

``` r
# fill = name is converting character variables into factors and arranging in alphabetical order

weather_df %>% 
  ggplot(aes(x = name, y = tmax, fill = name)) +
  geom_violin(alpha = .5)
```

    ## Warning: Removed 3 rows containing non-finite values (stat_ydensity).

![](p8105_visualization_2_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

``` r
# fixing it by manipulating data

weather_df %>% 
  mutate(
    name = factor(name),
    name = forcats::fct_relevel(name, c("Waikiki_HA"))
    ) %>% 
  ggplot(aes(x = name, y = tmax, fill = name)) +
  geom_violin(alpha = .5)
```

    ## Warning: Removed 3 rows containing non-finite values (stat_ydensity).

![](p8105_visualization_2_files/figure-gfm/unnamed-chunk-13-2.png)<!-- -->

How to get densities for tmin and tmax in a single panel:

``` r
weather_df %>% 
  filter(name == "CentralPark_NY") %>% 
  pivot_longer(
    tmax:tmin,
    names_to = "observation",
    values_to = "temperatures"
  ) %>% 
  ggplot(aes(x = temperatures, fill = observation)) +
  geom_density(alpha = .5)
```

![](p8105_visualization_2_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

``` r
weather_df %>% 
  pivot_longer(
    tmax:tmin,
    names_to = "observation",
    values_to = "temperatures"
  ) %>% 
  ggplot(aes(x = temperatures, fill = observation)) +
  geom_density(alpha = .5) +
  facet_grid(. ~ name)
```

    ## Warning: Removed 18 rows containing non-finite values (stat_density).

![](p8105_visualization_2_files/figure-gfm/unnamed-chunk-14-2.png)<!-- -->
