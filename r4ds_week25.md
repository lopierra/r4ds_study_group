R4DS Study Group - Week 24
================
Pierrette Lo
9/18/2020

  - [This week’s assignment](#this-weeks-assignment)
  - [Ch 11:3 Parsing a Vector](#ch-113-parsing-a-vector)

## This week’s assignment

  - Chapter 11

<!-- end list -->

``` r
library(tidyverse)
```

## Ch 11:3 Parsing a Vector

### Notes

  - Off-topic \#1: If “Redo” (`Ctrl/Cmd-Y`) doesn’t work on your
    RStudio, go to Tools \> Modify Keyboard Shortcuts, search for “Paste
    Last Yank”, and delete the shortcut if it is also set to `Ctrl-Y`.

  - Off-topic \#2: these are my main uses for date format codes:

<!-- end list -->

``` r
# get today's date

Sys.Date()
```

    ## [1] "2020-09-24"

``` r
# today's date in different formats

format(Sys.Date(), "%B %d, %Y")
```

    ## [1] "September 24, 2020"

``` r
format(Sys.Date(), "%m/%d/%y")
```

    ## [1] "09/24/20"

``` r
# write out a CSV file with today's date in the filename
# paste0 collapses a series of strings/variables with no space between

head(mtcars) %>% 
  write_csv(paste0("mtcars_subset_", Sys.Date(), ".csv"))
```

### Exercises

> 1.  What are the most important arguments to `locale()`?

`locale` captures the defaults for date format, decimals, grouping
marks, etc. that are specific to different countries. You then feed the
`locale` object into your `read` or `parse` function. See `?locale()`
for details about arguments.

> 2.  What happens if you try and set `decimal_mark` and `grouping_mark`
>     to the same character? What happens to the default value of
>     `grouping_mark` when you set `decimal_mark` to “,”? What happens
>     to the default value of `decimal_mark` when you set the
>     grouping\_mark to “.”?

``` r
locale(decimal_mark = ",", grouping_mark = ",")
```

    ## Error: `decimal_mark` and `grouping_mark` must be different

``` r
locale(grouping_mark = ".")
```

    ## <locale>
    ## Numbers:  123.456,78
    ## Formats:  %AD / %AT
    ## Timezone: UTC
    ## Encoding: UTF-8
    ## <date_names>
    ## Days:   Sunday (Sun), Monday (Mon), Tuesday (Tue), Wednesday (Wed), Thursday
    ##         (Thu), Friday (Fri), Saturday (Sat)
    ## Months: January (Jan), February (Feb), March (Mar), April (Apr), May (May),
    ##         June (Jun), July (Jul), August (Aug), September (Sep), October
    ##         (Oct), November (Nov), December (Dec)
    ## AM/PM:  AM/PM

> 3.  I didn’t discuss the `date_format` and `time_format` options to
>     `locale()`. What do they do? Construct an example that shows when
>     they might be useful.

Defaults:

  - `%AD` = flexible YMD parser
  - `%AT` = flexible HMS parser

These should be sufficient for most dates/times - you would only need to
specify if your data is in an unusual format.

Example from the [solutions
manual](https://jrnold.github.io/r4ds-exercise-solutions/data-import.html#exercise-11.3.3):
date given as “Day 01 Month 02 Year 20”)

``` r
locale_custom <- locale(date_format = "Day %d Month %M Year %y")

locale_custom
```

    ## <locale>
    ## Numbers:  123,456.78
    ## Formats:  Day %d Month %M Year %y / %AT
    ## Timezone: UTC
    ## Encoding: UTF-8
    ## <date_names>
    ## Days:   Sunday (Sun), Monday (Mon), Tuesday (Tue), Wednesday (Wed), Thursday
    ##         (Thu), Friday (Fri), Saturday (Sat)
    ## Months: January (Jan), February (Feb), March (Mar), April (Apr), May (May),
    ##         June (Jun), July (Jul), August (Aug), September (Sep), October
    ##         (Oct), November (Nov), December (Dec)
    ## AM/PM:  AM/PM

> 4.  If you live outside the US, create a new locale object that
>     encapsulates the settings for the types of file you read most
>     commonly.

``` r
locale_quebec <- locale(date_names = "fr",
                        decimal_mark = ",",
                        grouping_mark = " ")

locale_quebec
```

    ## <locale>
    ## Numbers:  123 456,78
    ## Formats:  %AD / %AT
    ## Timezone: UTC
    ## Encoding: UTF-8
    ## <date_names>
    ## Days:   dimanche (dim.), lundi (lun.), mardi (mar.), mercredi (mer.), jeudi
    ##         (jeu.), vendredi (ven.), samedi (sam.)
    ## Months: janvier (janv.), février (févr.), mars (mars), avril (avr.), mai (mai),
    ##         juin (juin), juillet (juil.), août (août), septembre (sept.),
    ##         octobre (oct.), novembre (nov.), décembre (déc.)
    ## AM/PM:  AM/PM

> 5.  What’s the difference between `read_csv()` and `read_csv2()`?

`read_csv` = comma separator (most common in English-speaking locales)
`read_csv2` = semicolon separator (common in European locales)

> 6.  What are the most common encodings used in Europe? What are the
>     most common encodings used in Asia? Do some googling to find out.

See the [wikipedia
page](https://en.wikipedia.org/wiki/Character_encoding#Common_character_encodings)
for a list of encodings in different languages.

> 7.  Generate the correct format string to parse each of the following
>     dates and times:

Formats are listed/described in the help (`?strptime`)

First create the objects in the text:

``` r
d1 <- "January 1, 2010"
d2 <- "2015-Mar-07"
d3 <- "06-Jun-2017"
d4 <- c("August 19 (2015)", "July 1 (2015)")
d5 <- "12/30/14" # Dec 30, 2014
t1 <- "1705"
t2 <- "11:15:10.12 PM"
```

Then parse each with the appropriate format. If the parsing works, you
will get an object of class “Date”. Otherwise you will get a “parsing
failure” error.

``` r
d1_parsed <- parse_date(d1, format = "%B %d, %Y")

class(d1)
```

    ## [1] "character"

``` r
class(d1_parsed)
```

    ## [1] "Date"

The other examples:

``` r
# d1 <- "January 1, 2010"
parse_date(d1, format = "%B %d, %Y")
```

    ## [1] "2010-01-01"

``` r
# d2 <- "2015-Mar-07"
parse_date(d2, format = "%Y-%b-%d")
```

    ## [1] "2015-03-07"

``` r
# d3 <- "06-Jun-2017"
parse_date(d3, format = "%d-%b-%Y")
```

    ## [1] "2017-06-06"

``` r
# d4 <- c("August 19 (2015)", "July 1 (2015)")
parse_date(d4, format = "%B %d (%Y)")
```

    ## [1] "2015-08-19" "2015-07-01"

``` r
# d5 <- "12/30/14"
parse_date(d5, format = "%m/%d/%y")
```

    ## [1] "2014-12-30"

``` r
# t1 <- "1705"
parse_time(t1, format = "%H%M")
```

    ## 17:05:00

``` r
# t2 <- "11:15:10.12 PM"
parse_time(t2, format = "%I:%M:%S %p")
```

    ## 23:15:10
