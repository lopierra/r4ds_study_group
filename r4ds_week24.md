R4DS Study Group - Week 24
================
Pierrette Lo
9/18/2020

  - [This week’s assignment](#this-weeks-assignment)
  - [Ch 11:2 Getting Started](#ch-112-getting-started)

## This week’s assignment

  - Chapter 11

<!-- end list -->

``` r
library(tidyverse)
```

## Ch 11:2 Getting Started

### Notes

Off-topic: I recently discovered how to get a multi-line cursor in
RStudio\! Super useful if you need to do the same thing to multiple
lines of text/code.

`Ctrl/Cmd + Alt + Up/Down arrow` to select lines.

`Esc` to cancel.

E.g. add bullets to this list:

    My list:
    
    Item 1
    Item 2
    Item 3

Also off-topic: use `Ctrl/Cmd + I` to get nicely formatted code
indentation. This is good practice as it will help you keep track of
hierarchies in your code.

On topic:

  - This is briefly mentioned at the end of the chapter - the
    [{readxl}](https://readxl.tidyverse.org/) package is very useful for
    reading in Excel files

  - However, it’s best to use CSV format where possible. Excel files can
    encode a lot of information in formatting (fonts, colors, multiple
    headers, comments, etc.) that will be lost when imported into R.
    Best practice is to embed that information in the data itself, or in
    a separate file, and keep the data as a simple flat text file.

  - Reminder to use `read_csv()` (with underscore - the tidyverse
    version) and not `read.csv` (with period - the base R version) for
    reasons explained in the text.

  - You can also use the RStudio shortcut to import a dataset (by
    clicking on a CSV file in the Files pane), but be sure to copy the
    generated code into your script/notebook so you can remember later
    where your data came from\!

### Exercises

> 1.  What function would you use to read a file where fields were
>     separated with “|”?

    read_delim("myfile.txt", delim = "|")

> 2.  Apart from `file`, `skip`, and `comment`, what other arguments do
>     `read_csv()` and `read_tsv()` have in common?

Trick question\! All of the arguments are the same - just apply to
different types of files.

> 3.  What are the most important arguments to `read_fwf()`?

A fixed-width file is one where each column is delimited by a
prespecified max width (as opposed to commas, tabs, etc.)

Per the help (`?read_fwf`), the two arguments that you have to specify
(no defaults provided) are `file` and `col_positions` (where each column
starts and ends). There are a few helper functions that help determine
what the column positions are - see `?read_fwf` for more details.

> 4.  Sometimes strings in a CSV file contain commas. To prevent them
>     from causing problems they need to be surrounded by a quoting
>     character, like " or ’. By default, `read_csv()` assumes that the
>     quoting character will be ". What argument to `read_csv()` do you
>     need to specify to read the following text into a data frame?

    "x,y\n1,'a,b'"

This method of creating a dataframe “de novo” is a little weird.

First let’s parse the desired output:

  - This is a CSV input, so columns are separated by commas
  - Remember that means “new line” - i.e. next row
  - Since this is text, the entire input must be surrounded by double
    quotes ("") before you provide it to `read_csv()`
  - One of the cells is a character string ‘a,b’ which is surrounded by
    single quotes (since it is inside the double quotes)

So we want the dataframe to look like this:

    x y
    
    1 a,b

The `quote` argument to `read_csv` is where you specify what your
strings are surrounded by - in this case, single quotes.

``` r
read_csv("x,y\n1,'a,b'", quote = "'")
```

    ## # A tibble: 1 x 2
    ##       x y    
    ##   <dbl> <chr>
    ## 1     1 a,b

Note that in the help `?read_csv`, the default for `quote` is shown as

    "\""

Putting a backslash before a special character is known as “escaping” -
it tells R that you’re referring to the character " literally, not using
it for its usual function of surrounding strings.

So the above means that the default method of quoting strings is using a
double quote.

This will become useful later when you start using “regular
expressions”, which are sequences of characters used to search for
string patterns - more about this in
[Chapter 14](https://r4ds.had.co.nz/strings.html#matching-patterns-with-regular-expressions).

> 5.  Identify what is wrong with each of the following inline CSV
>     files. What happens when you run the code?

``` r
read_csv("a,b\n1,2,3\n4,5,6")
```

    ## Warning: 2 parsing failures.
    ## row col  expected    actual         file
    ##   1  -- 2 columns 3 columns literal data
    ##   2  -- 2 columns 3 columns literal data

    ## # A tibble: 2 x 2
    ##       a     b
    ##   <dbl> <dbl>
    ## 1     1     2
    ## 2     4     5

As indicated in the error message, the header (a, b) indicates 2
columns, but rows 1 and 2 (1, 2, 3 and 4, 5, 6) have 3 columns, so the
3rd column (3 and 6) is dropped.

``` r
read_csv("a,b,c\n1,2\n1,2,3,4")
```

    ## Warning: 2 parsing failures.
    ## row col  expected    actual         file
    ##   1  -- 3 columns 2 columns literal data
    ##   2  -- 3 columns 4 columns literal data

    ## # A tibble: 2 x 3
    ##       a     b     c
    ##   <dbl> <dbl> <dbl>
    ## 1     1     2    NA
    ## 2     1     2     3

Header shows 3 columns, but row 1 only has 2 (so the last column is
filled with NA), and row 2 has 4 columns, so the last value (4) is
dropped.

``` r
read_csv("a,b\n\"1")
```

    ## Warning: 2 parsing failures.
    ## row col                     expected    actual         file
    ##   1  a  closing quote at end of file           literal data
    ##   1  -- 2 columns                    1 columns literal data

    ## # A tibble: 1 x 2
    ##       a b    
    ##   <dbl> <chr>
    ## 1     1 <NA>

I think they intended for “1” to be a character, not a number, but they
didn’t escape the double quotes correctly.

I couldn’t get it to work inline, but there are better ways to convert
columns to different data types that will be mentioned later.

``` r
read_csv("a,b\n1,2\na,b")
```

    ## # A tibble: 2 x 2
    ##   a     b    
    ##   <chr> <chr>
    ## 1 1     2    
    ## 2 a     b

Not sure exactly what they’re looking for here, but the fact that 1 and
2 have been coerced to characters could be a problem later if you’re
expecting numbers.

``` r
read_csv("a;b\n1;3")
```

    ## # A tibble: 1 x 1
    ##   `a;b`
    ##   <chr>
    ## 1 1;3

Here it looks like the columns are separated by semicolons, not commas,
so you should use `read_csv2` instead:

``` r
read_csv2("a;b\n1;3")
```

    ## Using ',' as decimal and '.' as grouping mark. Use read_delim() for more control.

    ## # A tibble: 1 x 2
    ##       a     b
    ##   <dbl> <dbl>
    ## 1     1     3
