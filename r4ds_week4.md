R4DS Study Group - Week 4
================
Pierrette Lo
5/1/2020

  - [This week’s assignment](#this-weeks-assignment)
  - [Ch 3:8 Position adjustments](#ch-38-position-adjustments)
  - [Ch 3:9 Coordinate systems](#ch-39-coordinate-systems)
  - [Next Steps](#next-steps)

## This week’s assignment

  - The rest of Chapter 3

As always, start by loading {tidyverse}

``` r
library(tidyverse)
```

## Ch 3:8 Position adjustments

### Exercises

> 1.  What is the problem with this plot? How could you improve it?

``` r
ggplot(data = mpg, mapping = aes(x = cty, y = hwy)) + 
  geom_point()
```

![](r4ds_week4_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

Similar to the example in the text, this plot shows overplotting - many
of the points are overlapping (because the values of `hwy` and `cty` are
rounded to the nearest whole number), so you don’t get a true picture of
their distribution.

Solution - add jitter.

``` r
ggplot(data = mpg, mapping = aes(x = cty, y = hwy)) + 
  geom_jitter()
```

![](r4ds_week4_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

> 2.  What parameters to `geom_jitter()` control the amount of
>     jittering?

According to the help - the parameters are `height` and `width` - add
jitter in vertical and horizontal directions, respectively.

Use 0.5 to spread the points just enough that individual groups can’t be
distinguished (without overspreading).

``` r
ggplot(data = mpg, mapping = aes(x = cty, y = hwy)) + 
  geom_jitter(width = 0.1, height = 0.1)
```

![](r4ds_week4_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

> 3.  Compare and contrast `geom_jitter()` with `geom_count()`.

They are similar in that they allow you to see distributions of
overlapping points, but `geom_jitter` moves the points themselves,
whereas `geom_count` changes the size of the points based on number of
overlapping observations (while keeping the location accurate).

``` r
ggplot(data = mpg, mapping = aes(x = cty, y = hwy)) + 
  geom_count()
```

![](r4ds_week4_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

> 4.  What’s the default position adjustment for `geom_boxplot()`?
>     Create a visualisation of the `mpg` dataset that demonstrates it.

According to the help, the default is `position = "dodge2"` (which is
basically the same as “dodge”). You will need a grouping variable in
order to observe this. Note what happens if you try `position =
"identity"`.

``` r
ggplot(data = mpg, aes(x = drv, y = hwy, color = class)) +
  geom_boxplot()
```

![](r4ds_week4_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

## Ch 3:9 Coordinate systems

### Exercises

> 1.  Turn a stacked bar chart into a pie chart using `coord_polar()`.

A pie chart is basically a stacked bar graph with a single bar,
translated onto polar coordinates.

Start by making a bar graph with a dummy variable for `x`.

``` r
ggplot(mpg, aes(x = "", fill = class)) +
  geom_bar()
```

![](r4ds_week4_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

Then use `theta = "y"` in `coord_polar()` to map `y` (counts) to the
angle of each section.

Use `theme_void()` to get rid of the background, axis labels, etc.

``` r
ggplot(mpg, aes(x = "", fill = class)) +
  geom_bar() +
  coord_polar(theta = "y") +
  theme_void()
```

![](r4ds_week4_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

> 2.  What does labs() do? Read the documentation.

There are various functions (`labs()`, `ggtitle()`, `xlab()`, `ylab()`)
to add labels to your plot and/or axes.

Example:

``` r
ggplot(data = mpg, aes(x = manufacturer, y = hwy)) +
  geom_boxplot() +
  coord_flip() +
  labs(x = "Manufacturer",
       y = "Highway mileage (mpg)",
       title = "Mileage of vehicles by manufacturer",
       subtitle = "Boxplot shows IQR, median, range, and outliers")
```

![](r4ds_week4_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

Or if this is for a manuscript figure:

``` r
ggplot(data = mpg, aes(x = manufacturer, y = hwy)) +
  geom_boxplot() +
  coord_flip() +
  labs(x = "Manufacturer",
       y = "Highway mileage (mpg)",
       caption = "Figure 1A. Mileage of vehicles by manufacturer. 
       Boxplot shows IQR, median, range, and outliers.",
       tag = "A")
```

![](r4ds_week4_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

Bonus code to adjust caption formatting, position, etc.

``` r
ggplot(data = mpg, aes(x = manufacturer, y = hwy)) +
  geom_boxplot() +
  coord_flip() +
  labs(x = "Manufacturer",
       y = "Highway mileage (mpg)\n",
       caption = "<b>Figure 1A.</b> Mileage of vehicles by manufacturer. 
       Boxplot shows IQR, median, range, and outliers.",
       tag = "A") +
  theme(plot.caption = ggtext::element_markdown(hjust = 0),
        plot.caption.position = "plot")
```

![](r4ds_week4_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

## Next Steps

Check out the other built-in datasets in {ggplot2}
[here](https://ggplot2.tidyverse.org/reference/#section-data) and make a
visualization to share.

(You can also use your own dataset if it’s in an appropriate format or
you already know how to get it there.)
