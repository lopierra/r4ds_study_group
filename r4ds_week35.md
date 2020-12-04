R4DS Study Group - Week 35
================
Pierrette Lo
12/4/2020

  - [Final notes about Chapter 13](#final-notes-about-chapter-13)
  - [This week’s assignment](#this-weeks-assignment)
  - [Ch 14:2 String basics](#ch-142-string-basics)

## Final notes about Chapter 13

Chapter 13.7 talks about “set operations” - these are for adding entire
rows to your starting table (vs. “joins”, which add columns).

The [{dplyr}
cheatsheet](https://github.com/rstudio/cheatsheets/raw/master/data-transformation.pdf)
provides a helpful visualization.

## This week’s assignment

  - Ch. 14

<!-- end list -->

``` r
library(tidyverse)
```

## Ch 14:2 String basics

### Exercises

> 1.  In code that doesn’t use {stringr}, you’ll often see `paste()` and
>     `paste0()`. What’s the difference between the two functions? What
>     stringr function are they equivalent to? How do the functions
>     differ in their handling of `NA`?

The `paste` functions are the equivalent of `str_c`. I usually use
`paste0` just because I learned it first.

  - `paste`: option to specify a separator (default is a space)
  - `paste0`: like `paste` but by default, squashes strings together
    with no space

Handling of missing values:

  - The `paste` functions coerce `NA` to the string “NA”
  - The `str_c` function leaves missing values as `NA`. You can use
    `str_replace_na` to convert `NA` to the string “NA”

e.g.:

``` r
string1 <- c("A", "B", NA)
string2 <- c(1, 2, 3)

paste(string1, string2)
```

    ## [1] "A 1"  "B 2"  "NA 3"

``` r
paste0(string1, string2)
```

    ## [1] "A1"  "B2"  "NA3"

``` r
str_c(string1, string2)
```

    ## [1] "A1" "B2" NA

> 2.  In your own words, describe the difference between the `sep` and
>     `collapse` arguments to `str_c()`.

If you’re combining two vectors using `str_c`:

  - `sep` specifies a separator between the elements of each vector
  - `collapse` specifies a separator to be used if you’re squashing all
    of the elements into a single string

e.g.:

``` r
string3 <- c("A", "B", "C")
string4 <- c(1, 2, 3)

str_c(string3, string4, sep = "-")
```

    ## [1] "A-1" "B-2" "C-3"

``` r
str_c(string3, string4, sep = "-", collapse = "|")
```

    ## [1] "A-1|B-2|C-3"

> 3.  Use `str_length()` and `str_sub()` to extract the middle character
>     from a string. What will you do if the string has an even number
>     of characters?

If odd number of characters:

``` r
string5 <- "abcdefg"

# use str_length() to get the length of string5
# use seq() to generate a sequence of numbers from 1 to length of string5
# use median() to get the middle position of that sequence (i.e. 3)
middle <- median(seq(str_length(string5)))

str_sub(string5, 
        start = middle, 
        end = middle)
```

    ## [1] "d"

If even number of characters:

Note that if you input a decimal to `str_sub`, it converts it to integer
by truncating, so it is always rounded down (e.g. `as.integer(3.8)
= 3`).

So our code above will still return a median of 3.5, and by default
`str_sub` will convert that to 3. If you want to follow normal rounding
rules, use `round()`.

``` r
string6 <- "abcdef"

middle <- median(seq(str_length(string6)))

str_sub(string6, 
        start = middle, 
        end = middle)
```

    ## [1] "c"

``` r
str_sub(string6, 
        start = round(middle), 
        end = round(middle))
```

    ## [1] "d"

> 4.  What does `str_wrap()` do? When might you want to use it?

This function wraps text so it fits within a certain width. Could be
useful if you were trying to fit a block of text into a table, for
example.

``` r
string7 <- "This chapter introduces you to string manipulation in R. You’ll learn the basics of how strings work and how to create them by hand, but the focus of this chapter will be on regular expressions, or regexps for short. Regular expressions are useful because strings usually contain unstructured or semi-structured data, and regexps are a concise language for describing patterns in strings. When you first look at a regexp, you’ll think a cat walked across your keyboard, but as your understanding improves they will soon start to make sense."

writeLines(string7)
```

    ## This chapter introduces you to string manipulation in R. You’ll learn the basics of how strings work and how to create them by hand, but the focus of this chapter will be on regular expressions, or regexps for short. Regular expressions are useful because strings usually contain unstructured or semi-structured data, and regexps are a concise language for describing patterns in strings. When you first look at a regexp, you’ll think a cat walked across your keyboard, but as your understanding improves they will soon start to make sense.

``` r
writeLines(str_wrap(string7, width = 10, exdent = 3))
```

    ## This
    ##    chapter
    ##    introduces
    ##    you to
    ##    string
    ##    manipulation
    ##    in R.
    ##    You’ll
    ##    learn the
    ##    basics
    ##    of how
    ##    strings
    ##    work and
    ##    how to
    ##    create
    ##    them by
    ##    hand, but
    ##    the focus
    ##    of this
    ##    chapter
    ##    will be
    ##    on regular
    ##    expressions,
    ##    or regexps
    ##    for short.
    ##    Regular
    ##    expressions
    ##    are useful
    ##    because
    ##    strings
    ##    usually
    ##    contain
    ##    unstructured
    ##    or semi-
    ##    structured
    ##    data, and
    ##    regexps
    ##    are a
    ##    concise
    ##    language
    ##    for
    ##    describing
    ##    patterns
    ##    in
    ##    strings.
    ##    When you
    ##    first
    ##    look at
    ##    a regexp,
    ##    you’ll
    ##    think a
    ##    cat walked
    ##    across
    ##    your
    ##    keyboard,
    ##    but as
    ##    your
    ##    understanding
    ##    improves
    ##    they will
    ##    soon start
    ##    to make
    ##    sense.

> 5.  What does `str_trim()` do? What’s the opposite of `str_trim()`?

`str_trim` removes whitespace from the start and end of a string (often
useful to apply to an entire column when cleaning up a dataset).

`str_pad` adds whitespace.

eg:

``` r
string8 <- "  abcde  "

str_trim(string8, side = "left")
```

    ## [1] "abcde  "

``` r
string9 <- "abcde"

str_pad(string9, width = 8, pad = "#")
```

    ## [1] "###abcde"

> 6.  Write a function that turns (e.g.) a vector `c("a", "b", "c")`
>     into the string `a, b, and c`. Think carefully about what it
>     should do if given a vector of length 0, 1, or 2.

Look at some test cases before making a function:

``` r
test_output <- str_c(c("a", "b", "c"), collapse = ", ")

str_sub(test_output, -2, -2) <- " and "

cat(test_output)
```

    ## a, b, and c

If you have a vector of 2, you don’t want to keep the comma:

``` r
test_output <- str_c(c("a", "b"), collapse = ", ")

str_sub(test_output, -3, -2) <- " and "

cat(test_output)
```

    ## a and b

Now make a function, taking into account the special cases of vectors
with length 0, 1, or 2

``` r
make_string <- function(input) {
  
  output <- str_c(input, collapse = ", ")
  
  if (length(input) >= 3) {
    str_sub(output, -2, -2) <- " and "
  } else if (length(input) == 2) {
    str_sub(output, -3, -2) <- " and "
  }
  
  cat(output)
  
}
```

Now test it with different inputs:

``` r
make_string(c("a", "b", "c"))
```

    ## a, b, and c

``` r
make_string(c("a", "b"))
```

    ## a and b

``` r
make_string("a")
```

    ## a

``` r
make_string("")

make_string(c("a", "b", "c", "d"))
```

    ## a, b, c, and d
