R4DS Study Group - Week 27
================
Pierrette Lo
10/9/2020

  - [This week’s assignment](#this-weeks-assignment)
  - [Ch 12:4 Separating and uniting](#ch-124-separating-and-uniting)
  - [Ch 12:5 Missing values](#ch-125-missing-values)

## This week’s assignment

  - Chapter 12

<!-- end list -->

``` r
library(tidyverse)
```

## Ch 12:4 Separating and uniting

### Exercises

> 1.  What do the `extra` and `fill` arguments do in `separate()`?
>     Experiment with the various options for the following two toy
>     datasets.

  - `extra` = what to do if there are too many pieces
  - `fill` = what to do if there are not enough pieces

In the first example, by default the extra piece “g” is dropped with a
warning:

``` r
tibble(x = c("a,b,c", "d,e,f,g", "h,i,j")) %>% 
  separate(x, c("one", "two", "three"))
```

    ## Warning: Expected 3 pieces. Additional pieces discarded in 1 rows [2].

    ## # A tibble: 3 x 3
    ##   one   two   three
    ##   <chr> <chr> <chr>
    ## 1 a     b     c    
    ## 2 d     e     f    
    ## 3 h     i     j

Can keep the extra value using “merge”:

``` r
tibble(x = c("a,b,c", "d,e,f,g", "h,i,j")) %>% 
  separate(x, c("one", "two", "three"),
           extra = "merge")
```

    ## # A tibble: 3 x 3
    ##   one   two   three
    ##   <chr> <chr> <chr>
    ## 1 a     b     c    
    ## 2 d     e     f,g  
    ## 3 h     i     j

In the second example, the 2nd row doesn’t have enough pieces, so by
default is filled with `NA` on the right:

``` r
tibble(x = c("a,b,c", "d,e", "f,g,i")) %>% 
  separate(x, c("one", "two", "three"))
```

    ## Warning: Expected 3 pieces. Missing pieces filled with `NA` in 1 rows [2].

    ## # A tibble: 3 x 3
    ##   one   two   three
    ##   <chr> <chr> <chr>
    ## 1 a     b     c    
    ## 2 d     e     <NA> 
    ## 3 f     g     i

Fill from left instead:

``` r
tibble(x = c("a,b,c", "d,e", "f,g,i")) %>% 
  separate(x, c("one", "two", "three"),
           fill = "left")
```

    ## # A tibble: 3 x 3
    ##   one   two   three
    ##   <chr> <chr> <chr>
    ## 1 a     b     c    
    ## 2 <NA>  d     e    
    ## 3 f     g     i

> 2.  Both `unite()` and `separate()` have a remove argument. What does
>     it do? Why would you set it to `FALSE`?

`remove` indicates whether you remove the original column(s) that is
being separated/united. It can be helpful to leave these in so you can
check.

Eg:

``` r
tibble(x = c("a,b,c", "d,e", "f,g,i")) %>% 
  separate(x, c("one", "two", "three"),
           remove = FALSE)
```

    ## Warning: Expected 3 pieces. Missing pieces filled with `NA` in 1 rows [2].

    ## # A tibble: 3 x 4
    ##   x     one   two   three
    ##   <chr> <chr> <chr> <chr>
    ## 1 a,b,c a     b     c    
    ## 2 d,e   d     e     <NA> 
    ## 3 f,g,i f     g     i

> 3.  Compare and contrast `separate()` and `extract()`.

  - separate = split a column by indicating what to separate by
  - extract = split a column by using regular expressions to indicate
    what to capture (more flexible)

green1 blue25 red2699

e.g. a column where the values are “green1”, “blue2”, “red3”, etc. -
there isn’t a common separator or position number, so you can use regex
to capture all of the alphabets

> Why are there three variations of separation (by position, by
> separator, and with groups), but only one unite?

There are different options for searching for separators to split up a
column, but only one option is needed for merging a column.

## Ch 12:5 Missing values

### Exercises

> 1.  Compare and contrast the fill arguments to pivot\_wider() and
>     complete().

Example copied from text:

  - explicit missing value: 2015 Q4
  - implicit missing value: 2016 Q1

<!-- end list -->

``` r
stocks <- tibble(
  year   = c(2015, 2015, 2015, 2015, 2016, 2016, 2016),
  qtr    = c(   1,    2,    3,    4,    2,    3,    4),
  return = c(1.88, 0.59, 0.35,   NA, 0.92, 0.17, 2.66)
)
```

`complete` fills in NA for 2016 Q1:

``` r
stocks %>% 
  complete(year, qtr)
```

    ## # A tibble: 8 x 3
    ##    year   qtr return
    ##   <dbl> <dbl>  <dbl>
    ## 1  2015     1   1.88
    ## 2  2015     2   0.59
    ## 3  2015     3   0.35
    ## 4  2015     4  NA   
    ## 5  2016     1  NA   
    ## 6  2016     2   0.92
    ## 7  2016     3   0.17
    ## 8  2016     4   2.66

Use `fill` argument to replace NA with 0:

``` r
stocks %>% 
  complete(year, qtr, fill = list(return = 0))
```

    ## # A tibble: 8 x 3
    ##    year   qtr return
    ##   <dbl> <dbl>  <dbl>
    ## 1  2015     1   1.88
    ## 2  2015     2   0.59
    ## 3  2015     3   0.35
    ## 4  2015     4   0   
    ## 5  2016     1   0   
    ## 6  2016     2   0.92
    ## 7  2016     3   0.17
    ## 8  2016     4   2.66

`pivot_wider` also fills 2016 Q1 with NA:

``` r
stocks %>% 
  pivot_wider(names_from = year, values_from = return)
```

    ## # A tibble: 4 x 3
    ##     qtr `2015` `2016`
    ##   <dbl>  <dbl>  <dbl>
    ## 1     1   1.88  NA   
    ## 2     2   0.59   0.92
    ## 3     3   0.35   0.17
    ## 4     4  NA      2.66

Use `values_fill` argument:

(Note that it only fills the implicit values - the NA that was already
there for 2015 Q4 doesn’t get replaced.)

``` r
stocks %>% 
  pivot_wider(names_from = year, 
              values_from = return,
              values_fill = 0)
```

    ## # A tibble: 4 x 3
    ##     qtr `2015` `2016`
    ##   <dbl>  <dbl>  <dbl>
    ## 1     1   1.88   0   
    ## 2     2   0.59   0.92
    ## 3     3   0.35   0.17
    ## 4     4  NA      2.66

> 2.  What does the `.direction` argument to `fill()` do?

Indicates which direction to copy/paste (up or down)

Example from `?fill`:

``` r
tidy_pets <- tibble::tribble(
  ~rank, ~pet_type, ~breed,
  1L,        NA,    "Boston Terrier",
  2L,        NA,    "Retrievers (Labrador)",
  3L,        NA,    "Retrievers (Golden)",
  4L,        NA,    "French Bulldogs",
  5L,        NA,    "Bulldogs",
  6L,     "Dog",    "Beagles",
  1L,        NA,    "Persian",
  2L,        NA,    "Maine Coon",
  3L,        NA,    "Ragdoll",
  4L,        NA,    "Exotic",
  5L,        NA,    "Siamese",
  6L,     "Cat",    "American Short"
)
```

Fill “up”:

``` r
tidy_pets %>% 
  fill(pet_type, .direction = "up")
```

    ## # A tibble: 12 x 3
    ##     rank pet_type breed                
    ##    <int> <chr>    <chr>                
    ##  1     1 Dog      Boston Terrier       
    ##  2     2 Dog      Retrievers (Labrador)
    ##  3     3 Dog      Retrievers (Golden)  
    ##  4     4 Dog      French Bulldogs      
    ##  5     5 Dog      Bulldogs             
    ##  6     6 Dog      Beagles              
    ##  7     1 Cat      Persian              
    ##  8     2 Cat      Maine Coon           
    ##  9     3 Cat      Ragdoll              
    ## 10     4 Cat      Exotic               
    ## 11     5 Cat      Siamese              
    ## 12     6 Cat      American Short

Note that you get the wrong result if you fill “down”:

``` r
tidy_pets %>% 
  fill(pet_type, .direction = "down")
```

    ## # A tibble: 12 x 3
    ##     rank pet_type breed                
    ##    <int> <chr>    <chr>                
    ##  1     1 <NA>     Boston Terrier       
    ##  2     2 <NA>     Retrievers (Labrador)
    ##  3     3 <NA>     Retrievers (Golden)  
    ##  4     4 <NA>     French Bulldogs      
    ##  5     5 <NA>     Bulldogs             
    ##  6     6 Dog      Beagles              
    ##  7     1 Dog      Persian              
    ##  8     2 Dog      Maine Coon           
    ##  9     3 Dog      Ragdoll              
    ## 10     4 Dog      Exotic               
    ## 11     5 Dog      Siamese              
    ## 12     6 Cat      American Short
