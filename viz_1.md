viz_1
================

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

    ## date created (size, mb): 2022-03-22 14:09:01 (7.641)

    ## file min/max dates: 1869-01-01 / 2022-03-31

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USC00519397.dly

    ## date created (size, mb): 2022-09-29 10:38:11 (1.699)

    ## file min/max dates: 1965-01-01 / 2020-03-31

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2022-09-29 10:38:17 (0.95)

    ## file min/max dates: 1999-09-01 / 2022-09-30

``` r
ggplot(weather_df, aes(x = tmin, y = tmax))
```

![](viz_1_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

``` r
weather_df %>% 
  drop_na() %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point()
```

![](viz_1_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

let make the same plot but different

``` r
weather_scatterplot = 
  weather_df %>% 
  drop_na() %>% 
  ggplot(aes(x = tmin, y = tmax)) 

#You can also save the output of ggplot() to an object and modify / print it later. This is often helpful, although it’s not my default approach to making plots.

weather_scatterplot +
  geom_point()
```

![](viz_1_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

Get more fancy

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_1_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
#OR you can stack, here only aes of color applies to the points 

weather_df %>% 
  ggplot(aes(x = tmin, y = tmax,)) + 
  geom_point(aes(color = name)) +
  geom_smooth()
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).
    ## Removed 15 rows containing missing values (geom_point).

![](viz_1_files/figure-gfm/unnamed-chunk-5-2.png)<!-- -->

``` r
# but here the color applies to all geometires being stacked onto plots & you get smooth lines for all names 

weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point() +
  geom_smooth()
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).
    ## Removed 15 rows containing missing values (geom_point).

![](viz_1_files/figure-gfm/unnamed-chunk-5-3.png)<!-- -->

``` r
# alpha changes color density and se FALSE gets rid of the error bars 
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax,)) + 
  geom_point(aes(color = name), alpha = .5) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).
    ## Removed 15 rows containing missing values (geom_point).

![](viz_1_files/figure-gfm/unnamed-chunk-5-4.png)<!-- -->

Make separate panels for each variable through facet wrap

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax,)) + 
  geom_point(aes(color = name), alpha = .5) +
  geom_smooth(se = FALSE)  + 
  facet_grid(name ~ .) #the "~ ." orients the plots into propper position 
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_1_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

comparing new variables, seasonal trend

``` r
weather_df %>% 
  ggplot(aes(x = date, y = tmax, color = name)) + 
  geom_point(aes(size = prcp), alpha = .5) +
  geom_smooth(se = FALSE) + 
    facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](viz_1_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

#Some quick stuff, C to F conversion

``` r
weather_df %>% 
  filter(name == "CentralPark_NY") %>% 
  mutate(
    tmax_fahr = tmax * (9 / 5) + 32,
    tmin_fahr = tmin * (9 / 5) + 32) %>% 
  ggplot(aes(x = tmin_fahr, y = tmax_fahr)) +
  geom_point(alpha = .5) + 
  geom_smooth(method = "lm", se = FALSE)
```

    ## `geom_smooth()` using formula 'y ~ x'

![](viz_1_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

#change shape of point for better visualization, In these cases
geom_hex(), geom_bin2d(), or geom_density2d() can be handy:

``` r
ggplot(weather_df, aes(x = tmax, y = tmin)) + 
  geom_hex()
```

    ## Warning: Removed 15 rows containing non-finite values (stat_binhex).

![](viz_1_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

# Univariate PLots … histograms, barplots, boxplots, violin

``` r
weather_df %>% 
  ggplot(aes(x = tmax)) +
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 3 rows containing non-finite values (stat_bin).

![](viz_1_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

``` r
#color is hard to see in historgram so the aes used is "fill"

weather_df %>% 
  ggplot(aes(x = tmax, fill = name)) +
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 3 rows containing non-finite values (stat_bin).

![](viz_1_files/figure-gfm/unnamed-chunk-10-2.png)<!-- -->

More options: Density Plot

``` r
weather_df %>% 
  ggplot(aes(x = tmax, fill = name)) +
  geom_density(alpha = .4, adjust = .5, color = "blue")
```

    ## Warning: Removed 3 rows containing non-finite values (stat_density).

![](viz_1_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

More options: Box Plot

``` r
weather_df %>% 
  ggplot(aes(y = tmax, x = name, fill = name)) +
  geom_boxplot()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

![](viz_1_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

More options: Violin Plot, they can be helpful for distribution of many
20+ variables

``` r
weather_df %>% 
  ggplot(aes(y = tmax, x = name)) +
  geom_violin(aes(fill = name), alpha = .5) + 
  stat_summary(fun = "median", color = "blue")
```

    ## Warning: Removed 3 rows containing non-finite values (stat_ydensity).

    ## Warning: Removed 3 rows containing non-finite values (stat_summary).

    ## Warning: Removed 3 rows containing missing values (geom_segment).

![](viz_1_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

More options: Ridge Plot, they can be helpful for distribution of many
20+ variables

``` r
weather_df %>% 
  ggplot(aes(x = tmax, y = name)) + 
  geom_density_ridges(scale = .85)
```

    ## Picking joint bandwidth of 1.84

    ## Warning: Removed 3 rows containing non-finite values (stat_density_ridges).

![](viz_1_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

##saving an embeding plots

``` r
weather_scatterplot = 
  weather_df %>% 
  ggplot(aes(x = date, y = tmax, color = name)) + 
  geom_point(aes(size = prcp), alpha = .5) + 
  geom_smooth( se = FALSE) + 
  facet_grid( . ~ name)

ggsave(
  file = "weather_scatterplot.pdf", 
  plot = weather_scatterplot, 
  width = 8, height = 5
)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

#you can keep making the figure larger but font will stay same

``` r
weather_scatterplot
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](viz_1_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->
