Limnotools Usage
================
Sam Albers and Doug Collinge
2017-02-02

Package loading
---------------

These packages are only need for this vignette and not for the limnotools package itself.

``` r
library(tidyverse)
```

### Development version of limnotools

Currently using sam\_exp branch

``` r
devtools::install_github("boshek/limnotools", ref = "sam_exp")

library(limnotools)
```

Split and merge algorithm
-------------------------

Implementation of the split-and-merge algorithm result in two parts:

-   wtr\_layer function
-   wtr\_segments function
-   Then plotting for visual verification

Simple application of the split and merge algorithm
---------------------------------------------------

Below is a simple one profile example of determining key water column parameters using the split-and-merge algorithm. Most users will only use two functions that are part of the split-and-merge algorithm. Thermocline depth and mix layer depth are calculated using the wtr\_layers() function. Segments of the water profile are calculated using wtr\_segments. The default behaviour for both functions is to run the algorithm *without* specifying the number of segments. Moreover, both functions adopt the convention of a minimum depth of 2.5 m, a maximum depth of 150 m and a error threshold of 0.1.

``` r
wldf <- wtr_layer(depth = latesummer$depth, measure = latesummer$temper)
wldf
```

    ##   nseg      mld    maxbd
    ## 1    3 6.718292 16.33231

Note that the axes of the water column profile have been reversed and flipped to better visualize the water column and conform to standard limnological displays.

``` r
plot(y = latesummer$depth, x = latesummer$temper, ylim = rev(range(latesummer$depth)))
abline(h = wldf$maxbd, col='blue')
abline(h = wldf$mld, col='red')
text(16, wldf$maxbd+3, "Thermocline", col = 'blue')
text(16, wldf$mld+3, "Mix Layer Depth", col = 'red')
```

![](limnotools_files/figure-markdown_github/unnamed-chunk-4-1.png)

More complicated example using many datafiles
---------------------------------------------

Many users will face situations where they have multiple profiles and would like to evaluate layers and/or segments on many files. There are several approaches to this type of 'grouping' problem in R. We will use the most popular approach - dplyr - which is part of the [tidyverse](https://CRAN.R-project.org/package=tidyverse). To generate data for this example we first need to combine all the internal dataframes from limnotools to illustrate mix layer estimation for many casts. To simplify and decrease runtime we will only do this for temperature and salinity.

``` r
## rbind all the dataframes together
earlyspring$group <- 'earlyspring'
latesummer$group <- 'latesummer'
rbind_df <- rbind(earlyspring, latesummer)
```

We can utilize the power of a dplyr pipe (%&gt;%) and gather to convert this data into a long form.

``` r
## Convert data into grouped format
wtrprof_df <- rbind_df %>%
  select(depth, temper, salinity, group) %>% ## only keep desired columns
  gather(variable, value, -depth, -group)  ## convert data to long format
```

Use group\_by() and do() to run wtr\_layer() by group and variable outputting a dataframe.

``` r
wl_df <- wtrprof_df %>%  
  group_by(variable, group) %>% ## group by variable and group
  do(wtr_layer(depth=.$depth,measure=.$value)) %>% ##do a water_layer calc
  select(-nseg) %>% ##nseg not needed here
  gather(Layer, value, -variable, -group) ##gather for plotting purposes
wl_df
```

    ## Source: local data frame [8 x 4]
    ## Groups: variable, group [4]
    ## 
    ##   variable       group Layer     value
    ##      <chr>       <chr> <chr>     <dbl>
    ## 1 salinity earlyspring   mld  2.634726
    ## 2 salinity  latesummer   mld  2.896572
    ## 3   temper earlyspring   mld  8.073714
    ## 4   temper  latesummer   mld  6.718292
    ## 5 salinity earlyspring maxbd 24.069020
    ## 6 salinity  latesummer maxbd 49.175213
    ## 7   temper earlyspring maxbd 22.636168
    ## 8   temper  latesummer maxbd 16.332306

The same applies to wtr\_segments()

``` r
s_df <- wtrprof_df %>%  
  group_by(variable, group) %>% ## group by variable and group
  do(wtr_segments(depth = .$depth, measure = .$value)) ##do a water_layer calc
s_df
```

    ## Source: local data frame [40 x 5]
    ## Groups: variable, group [4]
    ## 
    ##    variable       group  nseg     depth    measure
    ##       <chr>       <chr> <dbl>     <dbl>      <dbl>
    ## 1  salinity earlyspring    20  2.547000 0.05080000
    ## 2  salinity earlyspring    20  2.634726 0.05041180
    ## 3  salinity earlyspring    20  7.722812 0.05025533
    ## 4  salinity earlyspring    20  9.769743 0.05015157
    ## 5  salinity earlyspring    20 11.699707 0.05024573
    ## 6  salinity earlyspring    20 13.922089 0.05005259
    ## 7  salinity earlyspring    20 15.676602 0.05006517
    ## 8  salinity earlyspring    20 17.431114 0.05016102
    ## 9  salinity earlyspring    20 23.279489 0.04973940
    ## 10 salinity earlyspring    20 24.858551 0.04974008
    ## # ... with 30 more rows

Lastly we plot the mix layer depths and segments over the water profiles using the same limnological visualization convention described above and using ggplot2 (part of the tidyverse).

``` r
wtrprof_df %>%
  ggplot(aes(x = value,y = depth)) +
  geom_path(colour = 'purple') +
  geom_path(data = s_df, aes(x = measure, y = depth), colour = 'black') +
  geom_hline(data = wl_df, aes(yintercept = value, colour = Layer)) +
  scale_y_reverse() +
  facet_wrap(group~variable, scales = "free", ncol = 2) +
  labs(y = "Temperature/Salinity", x = "Depth (m)", 
       caption = "Black lines represent split-and-merge segments \n Mix layer depth =mld \n  Thermocline depth=maxbd")
```

![](limnotools_files/figure-markdown_github/unnamed-chunk-9-1.png)
