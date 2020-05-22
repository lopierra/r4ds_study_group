R4DS Study Group - Week 7
================
Pierrette Lo
5/22/2020

  - [This week’s assignment](#this-weeks-assignment)
  - [Ch 5:4 Select](#ch-54-select)
  - [Ch 5:5 Mutate](#ch-55-mutate)

## This week’s assignment

  - The rest of Chapter 5 (5.4 through 5.7)

<!-- end list -->

``` r
library(tidyverse)
library(nycflights13)
```

### General Notes

  - Alex reminded me that you can change where your chunk output shows
    up by clicking the gear next to the “Knit” button. Choose “Chunk
    Output Inline” to see output under the chunk; choose “Chunk Output
    in Console” to see plots in the Viewer and other output in the
    console

  - Also, OCTRI’s *Data Wrangling with R* workshops would be a great
    review/complement to R4DS -
    <https://github.com/jminnier/berd_r_courses>

  - Shortcut for `%>%` = Ctrl/Cmd-Shift-M

  - Watch out for common mistake of using `%>%` instead of `+` between
    `ggplot` layers

  - Advantage to piping the dataset into your command - variable names
    will be autofilled

E.g. try typing

``` r
arrange(flights, air_time)

#vs

flights %>% 
  arrange(air_time)
```

## Ch 5:4 Select

### Exercises

> 1.  Brainstorm as many ways as possible to select `dep_time`,
>     `dep_delay`, `arr_time`, and `arr_delay` from flights.

``` r
# use column names
select(flights, dep_time, dep_delay, arr_time, arr_delay)
```

    ## # A tibble: 336,776 x 4
    ##    dep_time dep_delay arr_time arr_delay
    ##       <int>     <dbl>    <int>     <dbl>
    ##  1      517         2      830        11
    ##  2      533         4      850        20
    ##  3      542         2      923        33
    ##  4      544        -1     1004       -18
    ##  5      554        -6      812       -25
    ##  6      554        -4      740        12
    ##  7      555        -5      913        19
    ##  8      557        -3      709       -14
    ##  9      557        -3      838        -8
    ## 10      558        -2      753         8
    ## # ... with 336,766 more rows

``` r
# delete all the columns you don't want
select(flights, -(year:day), -sched_dep_time, - sched_arr_time, -(carrier:time_hour))
```

    ## # A tibble: 336,776 x 4
    ##    dep_time dep_delay arr_time arr_delay
    ##       <int>     <dbl>    <int>     <dbl>
    ##  1      517         2      830        11
    ##  2      533         4      850        20
    ##  3      542         2      923        33
    ##  4      544        -1     1004       -18
    ##  5      554        -6      812       -25
    ##  6      554        -4      740        12
    ##  7      555        -5      913        19
    ##  8      557        -3      709       -14
    ##  9      557        -3      838        -8
    ## 10      558        -2      753         8
    ## # ... with 336,766 more rows

``` r
# use column numbers
select(flights, 4, 6, 7, 9)
```

    ## # A tibble: 336,776 x 4
    ##    dep_time dep_delay arr_time arr_delay
    ##       <int>     <dbl>    <int>     <dbl>
    ##  1      517         2      830        11
    ##  2      533         4      850        20
    ##  3      542         2      923        33
    ##  4      544        -1     1004       -18
    ##  5      554        -6      812       -25
    ##  6      554        -4      740        12
    ##  7      555        -5      913        19
    ##  8      557        -3      709       -14
    ##  9      557        -3      838        -8
    ## 10      558        -2      753         8
    ## # ... with 336,766 more rows

``` r
# use `starts_with()`
select(flights, starts_with("dep_"), starts_with("arr_"))
```

    ## # A tibble: 336,776 x 4
    ##    dep_time dep_delay arr_time arr_delay
    ##       <int>     <dbl>    <int>     <dbl>
    ##  1      517         2      830        11
    ##  2      533         4      850        20
    ##  3      542         2      923        33
    ##  4      544        -1     1004       -18
    ##  5      554        -6      812       -25
    ##  6      554        -4      740        12
    ##  7      555        -5      913        19
    ##  8      557        -3      709       -14
    ##  9      557        -3      838        -8
    ## 10      558        -2      753         8
    ## # ... with 336,766 more rows

In practice, you usually want to avoid using column numbers to pick
columns. If you later add, subtract, or reorder the columns in your
dataset but reuse your old code, the column numbers will now be wrong\!

> 2.  What happens if you include the name of a variable multiple times
>     in a `select()` call?

``` r
select(flights, origin, origin, origin)
```

    ## # A tibble: 336,776 x 1
    ##    origin
    ##    <chr> 
    ##  1 EWR   
    ##  2 LGA   
    ##  3 JFK   
    ##  4 JFK   
    ##  5 LGA   
    ##  6 EWR   
    ##  7 EWR   
    ##  8 LGA   
    ##  9 JFK   
    ## 10 LGA   
    ## # ... with 336,766 more rows

Duplicated columns will be ignored. This is why `everything()` works to
select some columns and then `everything()` else without repeating the
previously selected columns.

``` r
select(flights, origin, everything())
```

    ## # A tibble: 336,776 x 19
    ##    origin  year month   day dep_time sched_dep_time dep_delay arr_time
    ##    <chr>  <int> <int> <int>    <int>          <int>     <dbl>    <int>
    ##  1 EWR     2013     1     1      517            515         2      830
    ##  2 LGA     2013     1     1      533            529         4      850
    ##  3 JFK     2013     1     1      542            540         2      923
    ##  4 JFK     2013     1     1      544            545        -1     1004
    ##  5 LGA     2013     1     1      554            600        -6      812
    ##  6 EWR     2013     1     1      554            558        -4      740
    ##  7 EWR     2013     1     1      555            600        -5      913
    ##  8 LGA     2013     1     1      557            600        -3      709
    ##  9 JFK     2013     1     1      557            600        -3      838
    ## 10 LGA     2013     1     1      558            600        -2      753
    ## # ... with 336,766 more rows, and 11 more variables: sched_arr_time <int>,
    ## #   arr_delay <dbl>, carrier <chr>, flight <int>, tailnum <chr>, dest <chr>,
    ## #   air_time <dbl>, distance <dbl>, hour <dbl>, minute <dbl>, time_hour <dttm>

> 3.  What does the `one_of()` function do? Why might it be helpful in
>     conjunction with this vector?

``` r
vars <- c("year", "month", "day", "dep_delay", "arr_delay")

select(flights, one_of(vars))
```

    ## # A tibble: 336,776 x 5
    ##     year month   day dep_delay arr_delay
    ##    <int> <int> <int>     <dbl>     <dbl>
    ##  1  2013     1     1         2        11
    ##  2  2013     1     1         4        20
    ##  3  2013     1     1         2        33
    ##  4  2013     1     1        -1       -18
    ##  5  2013     1     1        -6       -25
    ##  6  2013     1     1        -4        12
    ##  7  2013     1     1        -5        19
    ##  8  2013     1     1        -3       -14
    ##  9  2013     1     1        -3        -8
    ## 10  2013     1     1        -2         8
    ## # ... with 336,766 more rows

It’s like the `%in%` operator for `filter()`.

> 4.  Does the result of running the following code surprise you? How do
>     the select helpers deal with case by default? How can you change
>     that default?

``` r
select(flights, contains("TIME"))
```

    ## # A tibble: 336,776 x 6
    ##    dep_time sched_dep_time arr_time sched_arr_time air_time time_hour          
    ##       <int>          <int>    <int>          <int>    <dbl> <dttm>             
    ##  1      517            515      830            819      227 2013-01-01 05:00:00
    ##  2      533            529      850            830      227 2013-01-01 05:00:00
    ##  3      542            540      923            850      160 2013-01-01 05:00:00
    ##  4      544            545     1004           1022      183 2013-01-01 05:00:00
    ##  5      554            600      812            837      116 2013-01-01 06:00:00
    ##  6      554            558      740            728      150 2013-01-01 05:00:00
    ##  7      555            600      913            854      158 2013-01-01 06:00:00
    ##  8      557            600      709            723       53 2013-01-01 06:00:00
    ##  9      557            600      838            846      140 2013-01-01 06:00:00
    ## 10      558            600      753            745      138 2013-01-01 06:00:00
    ## # ... with 336,766 more rows

Per the help (`?contains()`), the select helper functions are by default
NOT case-sensitive.

To make it case-sensitive, use `ignore.case = F`

``` r
select(flights, contains("TIME", ignore.case = F))
```

    ## # A tibble: 336,776 x 0

``` r
# returns no rows
```

## Ch 5:5 Mutate

### Notes

  - `%/%` = integer division
  - `%%` = remainder after integer division

### Exercises

> 1.  Currently `dep_time` and `sched_dep_time` are convenient to look
>     at, but hard to compute with because they’re not really continuous
>     numbers. Convert them to a more convenient representation of
>     number of minutes since midnight.

For each time, you’ll need to use the “integer division” and “modulo”
operators, e.g.:

``` r
# integer division to get number of hours since midnight
517 %/% 100
```

    ## [1] 5

``` r
# modulo (remainder) to get minutes
517 %% 100
```

    ## [1] 17

``` r
# also use modulo to convert midnight to 0 (24 * 60 = 1440)
# (any time except midnight) %% 1440 == same
# midnight (1440) %% 1440 == 0
```

``` r
flights %>% 
  mutate(dep_time_minutes = (dep_time %/% 100 * 60 + dep_time %% 100) %% 1440,
         sched_dep_time_minutes = (sched_dep_time %/% 100 * 60 + sched_dep_time %% 100) %% 1440
  ) %>% 
  select(contains("dep_time"))
```

    ## # A tibble: 336,776 x 4
    ##    dep_time sched_dep_time dep_time_minutes sched_dep_time_minutes
    ##       <int>          <int>            <dbl>                  <dbl>
    ##  1      517            515              317                    315
    ##  2      533            529              333                    329
    ##  3      542            540              342                    340
    ##  4      544            545              344                    345
    ##  5      554            600              354                    360
    ##  6      554            558              354                    358
    ##  7      555            600              355                    360
    ##  8      557            600              357                    360
    ##  9      557            600              357                    360
    ## 10      558            600              358                    360
    ## # ... with 336,766 more rows

**Bonus** suggested in the solutions manual
(<https://jrnold.github.io/r4ds-exercise-solutions/transform.html#exercise-5.5.1>):

Write your own function to convert time to minutes\!

``` r
# "time" is a user-defined argument - you can call it whatever you want

time2mins <- function(time){
  (time %/% 100 * 60 + time %% 100) %% 1440
}
```

Now use it in the above code - so much more concise and less repetitive.

``` r
flights %>% 
  mutate(dep_time_minutes = time2mins(dep_time),
         sched_dep_time_minutes = time2mins(sched_dep_time)) %>% 
  select(contains("dep_time"))
```

    ## # A tibble: 336,776 x 4
    ##    dep_time sched_dep_time dep_time_minutes sched_dep_time_minutes
    ##       <int>          <int>            <dbl>                  <dbl>
    ##  1      517            515              317                    315
    ##  2      533            529              333                    329
    ##  3      542            540              342                    340
    ##  4      544            545              344                    345
    ##  5      554            600              354                    360
    ##  6      554            558              354                    358
    ##  7      555            600              355                    360
    ##  8      557            600              357                    360
    ##  9      557            600              357                    360
    ## 10      558            600              358                    360
    ## # ... with 336,766 more rows

General rule is to write a function when you’ve copied and pasted the
same block of code more than twice.

More about functions in Ch. 19: <https://r4ds.had.co.nz/functions.html>

> 2.  Compare `air_time` with `arr_time` - `dep_time`. What do you
>     expect to see? What do you see? What do you need to do to fix it?

This one requires some data detective work\!

You would expect that `arr_time` - `dep_time` == `air_time`, but that
turns out not to be the case.

``` r
flights %>% 
  mutate(arr_dep_diff = arr_time - dep_time) %>% 
  select(air_time, arr_dep_diff)
```

    ## # A tibble: 336,776 x 2
    ##    air_time arr_dep_diff
    ##       <dbl>        <int>
    ##  1      227          313
    ##  2      227          317
    ##  3      160          381
    ##  4      183          460
    ##  5      116          258
    ##  6      150          186
    ##  7      158          358
    ##  8       53          152
    ##  9      140          281
    ## 10      138          195
    ## # ... with 336,766 more rows

Check `?flights` and note that arrival and departure times are in local
time zone. So let’s check the difference between `air_time` and `arr -
dep`:

``` r
flights %>% 
  mutate(arr_dep_diff = time2mins(arr_time) - time2mins(dep_time),
         air_time_diff = air_time - arr_dep_diff) %>% 
  select(air_time, arr_dep_diff, air_time_diff)
```

    ## # A tibble: 336,776 x 3
    ##    air_time arr_dep_diff air_time_diff
    ##       <dbl>        <dbl>         <dbl>
    ##  1      227          193            34
    ##  2      227          197            30
    ##  3      160          221           -61
    ##  4      183          260           -77
    ##  5      116          138           -22
    ##  6      150          106            44
    ##  7      158          198           -40
    ##  8       53           72           -19
    ##  9      140          161           -21
    ## 10      138          115            23
    ## # ... with 336,766 more rows

If it’s just a time zone issue, why are the differences not multiples of
60?

Further digging in `?flights` reveals a link to the original data source
(<https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236>).

If you scroll through the fields and look at the “Departure Performance”
and “Arrival Performance” sections, you’ll see that there are variables
for taxiing that are probably counted in arrival/departure time but not
in air time. I suspect that `WheelsOff` - `WheelsOn` = `air_time`.

So the difference is due to some combination of time zone and taxiing
time.

> 3.  Compare `dep_time`, `sched_dep_time`, and `dep_delay.` How would
>     you expect those three numbers to be related?

I would expect that `dep_time` - `sched_dep_time` == `dep_delay`

`dep_delay` is in minutes, so first convert the two time variables to
minutes, and then create a new column for the difference:

``` r
flights_ex3 <- flights %>%
  mutate(dep_time_mins = time2mins(dep_time),
         sched_dep_time_mins = time2mins(sched_dep_time),
         sched_dep_diff = dep_time_mins - sched_dep_time_mins) %>% 
  select(dep_time_mins, sched_dep_time_mins, sched_dep_diff, dep_delay)
```

Now check whether the difference is identical to `dep_delay` for all
rows:

``` r
flights_ex3 %>% 
  filter(sched_dep_diff != dep_delay)
```

    ## # A tibble: 1,236 x 4
    ##    dep_time_mins sched_dep_time_mins sched_dep_diff dep_delay
    ##            <dbl>               <dbl>          <dbl>     <dbl>
    ##  1           528                1115           -587       853
    ##  2            42                1439          -1397        43
    ##  3            86                1370          -1284       156
    ##  4            32                1439          -1407        33
    ##  5            50                1305          -1255       185
    ##  6           155                1439          -1284       156
    ##  7            25                1439          -1414        26
    ##  8            66                1365          -1299       141
    ##  9            14                1439          -1425        15
    ## 10            37                1350          -1313       127
    ## # ... with 1,226 more rows

Looks like most of the discrepancies occur when a flight is scheduled to
depart before midnight but is delayed until after midnight. There are
also some discrepancies due to daylight savings time. As usual, this
data is more complicated than it first appeared\!

See the solution here for a detailed explanation:
(<https://jrnold.github.io/r4ds-exercise-solutions/transform.html#exercise-5.5.3>)

> 4.  Find the 10 most delayed flights using a ranking function. How do
>     you want to handle ties? Carefully read the documentation for
>     `min_rank()`.

In order to understand how ties are handled, you’ll need to look at
`?rank`. There are several methods, but the two used by the {dplyr}
ranking functions are:

  - “first” (used in the `row_number()` function) - each element gets a
    unique ranking value; first occurrence gets higher rank
  - “min” (used in the `min_rank()` function) - ties will share minimum
    possible rank - this is the most common usage in sports and other
    competitions

Example:

``` r
y <- c(100, 200, 300, 200, 400)

# first occurrence "wins" (gets higher rank)
row_number(y)
```

    ## [1] 1 2 4 3 5

``` r
# ties get same minimum rank, then it skips ahead
min_rank(y)
```

    ## [1] 1 2 4 2 5

``` r
# ties get same minimum rank, but doesn't skip ahead
dense_rank(y)
```

    ## [1] 1 2 3 2 4

Note that you want to use `desc()` so larger delays get a higher rank

``` r
flights %>% 
  mutate(delay_rank = min_rank(desc(dep_delay))) %>% 
  select(dep_delay, delay_rank) %>% 
  arrange(delay_rank)
```

    ## # A tibble: 336,776 x 2
    ##    dep_delay delay_rank
    ##        <dbl>      <int>
    ##  1      1301          1
    ##  2      1137          2
    ##  3      1126          3
    ##  4      1014          4
    ##  5      1005          5
    ##  6       960          6
    ##  7       911          7
    ##  8       899          8
    ##  9       898          9
    ## 10       896         10
    ## # ... with 336,766 more rows

I also tried the above code with `row_number()` and `dense_rank()` and
got the same top 10, since there were no ties.
