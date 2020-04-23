R4DS Study Group - Week 2
================
Pierrette Lo
4/16/2020

  - [This week’s assignment](#this-weeks-assignment)
  - [Ch 3:3 Aesthetic mappings](#ch-33-aesthetic-mappings)
  - [Ch 3:5 Facets](#ch-35-facets)

## This week’s assignment

  - Continue working through Chapter 3

## Ch 3:3 Aesthetic mappings

(Sorry about the weird punctuation in the section headers. Apparently
including a `.` in the header breaks the table of contents.)

### Notes

  - Briefly introduce the concept of notebooks and scripts
  - You only have to install a package once, but you’ll have to use
    `library()` to load it every time you start a new R session
  - Use `aes()` to connect a visual element to your data
  - Different aesthetics have different levels of effectiveness
    (e.g. shapes can be hard to distinguish if your plot is small)
  - If you want to make a universal change that is NOT linked to your
    data, put it outside of `aes()`

### Exercises:

Again, see
<https://jrnold.github.io/r4ds-exercise-solutions/data-visualisation.html>
for more detailed solutions.

Reload {tidyverse} since I started a new R session:

``` r
library(tidyverse)
```

    ## Warning: package 'tidyverse' was built under R version 3.6.1

    ## -- Attaching packages ------------------------------------------------------------------ tidyverse 1.3.0 --

    ## v ggplot2 3.3.0     v purrr   0.3.3
    ## v tibble  3.0.0     v dplyr   0.8.5
    ## v tidyr   1.0.0     v stringr 1.4.0
    ## v readr   1.3.1     v forcats 0.4.0

    ## Warning: package 'ggplot2' was built under R version 3.6.3

    ## Warning: package 'tibble' was built under R version 3.6.3

    ## Warning: package 'tidyr' was built under R version 3.6.1

    ## Warning: package 'purrr' was built under R version 3.6.1

    ## Warning: package 'dplyr' was built under R version 3.6.3

    ## -- Conflicts --------------------------------------------------------------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

> 1.  What’s gone wrong with this code? Why are the points not blue?

``` r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy, color = "blue"))
```

![](r4ds_week2_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

`color` is inside `aes()`, so you are saying you want to map `color` to
the variable “blue”. It will therefore assign the variable “blue” to the
first color in the default palette (pink). There is no variation in
color because the variable “blue” is interpreted as a categorical
variable with a single value (“blue”). (Notice also the unwanted
legend.)

How would you fix it?

``` r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy), color = "blue")
```

![](r4ds_week2_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

> 2.  Which variables in `mpg` are categorical? Which variables are
>     continuous? (Hint: type `?mpg` to read the documentation for the
>     dataset). How can you see this information when you run `mpg`?

``` r
glimpse(mpg)
```

    ## Rows: 234
    ## Columns: 11
    ## $ manufacturer <chr> "audi", "audi", "audi", "audi", "audi", "audi", "audi"...
    ## $ model        <chr> "a4", "a4", "a4", "a4", "a4", "a4", "a4", "a4 quattro"...
    ## $ displ        <dbl> 1.8, 1.8, 2.0, 2.0, 2.8, 2.8, 3.1, 1.8, 1.8, 2.0, 2.0,...
    ## $ year         <int> 1999, 1999, 2008, 2008, 1999, 1999, 2008, 1999, 1999, ...
    ## $ cyl          <int> 4, 4, 4, 4, 6, 6, 6, 4, 4, 4, 4, 6, 6, 6, 6, 6, 6, 8, ...
    ## $ trans        <chr> "auto(l5)", "manual(m5)", "manual(m6)", "auto(av)", "a...
    ## $ drv          <chr> "f", "f", "f", "f", "f", "f", "f", "4", "4", "4", "4",...
    ## $ cty          <int> 18, 21, 20, 21, 16, 18, 18, 18, 16, 20, 19, 15, 17, 17...
    ## $ hwy          <int> 29, 29, 31, 30, 26, 26, 27, 26, 25, 28, 27, 25, 25, 25...
    ## $ fl           <chr> "p", "p", "p", "p", "p", "p", "p", "p", "p", "p", "p",...
    ## $ class        <chr> "compact", "compact", "compact", "compact", "compact",...

Use `glimpse(mpg)` or `summary(mpg)` to look at a summary of the data.

General, categorical = <chr> (ie. characters) and continuous = <dbl> or
<int> (ie. numbers)

Categorical: `manufacturer`, `model`, `trans`, `drv`, `fl`, `class`

Continuous: `displ`, `year`, `cyl` (according to solution manual),
`cty`, `hwy`

Note: I disagree with the solution manual regarding `cyl` - even though
it’s a number, I think it could be a categorical variable because it has
a limited possible number of values (4, 6, 8, or 5). Depends on what
analysis you want to do.

> 3.  Map a continuous variable to `color`, `size`, and `shape.` How do
>     these aesthetics behave differently for categorical vs. continuous
>     variables?

``` r
ggplot(data = mpg) +
  geom_point(aes(x = displ, y = hwy, color = cty))
```

![](r4ds_week2_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
ggplot(data = mpg) +
  geom_point(aes(x = displ, y = hwy, size = drv))
```

    ## Warning: Using size for a discrete variable is not advised.

![](r4ds_week2_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

``` r
ggplot(data = mpg) +
  geom_point(aes(x = displ, y = hwy, shape = cty))
```

    ## Error: A continuous variable can not be mapped to shape

![](r4ds_week2_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

Color and size work with continuous variables; shape does not. Shapes
are not ordered - is a square greater than a circle?

Color is discrete for cat variables and a spectrum for cont variables.

Size elicits a warning if you try to use it with cont variables - it
will work but is not recommended.

> 4.  What happens if you map the same variable to multiple aesthetics?

``` r
ggplot(data = mpg) +
  geom_point(aes(x = displ, y = hwy, color = drv, size = drv))
```

    ## Warning: Using size for a discrete variable is not advised.

![](r4ds_week2_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

You can do this, but the information is redundant and could lead to
confusion - better to stick with one aesthetic per variable.

> 5.  What does the `stroke` aesthetic do? What shapes does it work
>     with? (Hint: use `?geom_point`)

For shapes that have a border (like 21), you can colour the inside and
outside separately. Use the stroke aesthetic to modify the width of the
border.

See [here](http://www.sthda.com/english/wiki/ggplot2-point-shapes) for
list of available shapes.

``` r
ggplot(data = mpg) +
  geom_point(aes(x = displ, y = hwy, fill = drv), shape = 21, size = 3, color = "black", stroke = 2)
```

![](r4ds_week2_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

color = color of border fill = color of inside area stroke = thickness
of border

> 6.  What happens if you map an aesthetic to something other than a
>     variable name, like `aes(colour = displ < 5)`? Note, you’ll also
>     need to specify x and y.

``` r
ggplot(data = mpg) +
  geom_point(aes(x = displ, y = hwy, color = displ < 5))
```

![](r4ds_week2_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

You can create new variables “on the fly” within the ggplot command.
(Remember the variable “blue” above?)

In this case you are creating a new variable `displ < 5` which has two
values, TRUE or FALSE, and mapping it to `color`.

## Ch 3:5 Facets

### Exercises

> 1.  What happens if you facet on a continuous variable?

``` r
ggplot(data = mpg) +
  geom_point(aes(x = displ, y = hwy)) +
  facet_wrap(~ cty)
```

![](r4ds_week2_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

The continuous variable is converted to a categorical variable, so there
is a facet for each unique value. Not very useful for `cty` but useful
for a semi-categorical value like `cyl`.

> 2.  What do the empty cells in plot with `facet_grid(drv ~ cyl)` mean?
>     How do they relate to this plot?

``` r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy)) +
  facet_grid(drv ~ cyl)
```

![](r4ds_week2_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

Blank facets indicate no overlap between the 2 categories (eg. there are
no observations of 4-cylinder, rear wheel drive cars). You can check the
overlap by running the code in the exercise (scatterplot of `drv` vs
`cyl`).

> 3.  What plots does the following code make? What does `.` do?

``` r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy)) +
  facet_grid(drv ~ .)
```

![](r4ds_week2_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

Remember the format of the formula in `facet_grid()` is rows \~ columns
(standard notation in R). The `.` means you want to ignore that side of
the formula. So `facet_grid(drv ~ .)` means you want to split the facets
into rows by `drv`, but don’t split into columns.

If you want columns, but not rows:

``` r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy)) +
  facet_grid(. ~ drv)
```

![](r4ds_week2_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

> 4.  Take the first faceted plot in this section.
> 
> What are the advantages to using faceting instead of the colour
> aesthetic? What are the disadvantages? How might the balance change if
> you had a larger dataset?

Faceted plot:

``` r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy)) + 
  facet_wrap(~ class, nrow = 2)
```

![](r4ds_week2_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

Same plot as above, using color instead of facets:

Colored plot:

``` r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy, color = class))
```

![](r4ds_week2_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

Faceting allows you to look at each category individually, but it can be
hard to compare across categories if there are a lot of them. On the
other hand, it can be difficult to distinguish between many different
colors.

See the [solutions
manual](https://jrnold.github.io/r4ds-exercise-solutions/data-visualisation.html#exercise-3.5.4)
for a more detailed explanation.

> 5.  Read `?facet_wrap`. What does `nrow` do? What does `ncol` do? What
>     other options control the layout of the individual panels? Why
>     doesn’t `facet_grid()` have `nrow` and `ncol` arguments?

`nrow` indicates the number of rows you want to break your facets into;
`ncol` indicates number of columns. If you only want to facet by 1
variable, `facet_wrap` is nice because it will optimize the layout over
the available space.

With `facet_grid`, rows and columns are linked to variables, so the
numbers of each depend on the number of categories in each variable -
you don’t get to specify.

Example of `facet_grid` with 1 variable, `class`:

``` r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy)) + 
  facet_grid(. ~ class)
```

![](r4ds_week2_files/figure-gfm/unnamed-chunk-17-1.png)<!-- -->

Example with `facet_wrap`:

``` r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy)) + 
  facet_wrap(~ class, ncol = 4)
```

![](r4ds_week2_files/figure-gfm/unnamed-chunk-18-1.png)<!-- -->

One option to point out is `scales` - the default is to scale the axes
the same for every facet (`scales = "fixed"`) so it’s easier to compare.
If you want each panel to be scaled according to the data it contains,
use `scales = "free"`.

Or you can free only the x or y axes using `scales = "free_x"` or
`scales = "free_y"`.

``` r
ggplot(data = mpg) + 
  geom_point(mapping = aes(x = displ, y = hwy)) + 
  facet_grid(. ~ class)
```

![](r4ds_week2_files/figure-gfm/unnamed-chunk-19-1.png)<!-- -->

> 6.  When using `facet_grid()` you should usually put the variable with
>     more unique levels in the columns. Why?

I think this just depends on your final layout for the plot. If it will
be landscape, then you would want more columns. If portrait, you would
want more rows.
