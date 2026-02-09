---
title: Data Wrangling with dplyr
teaching: 25
exercises: 15
source: Rmd
---


:::: instructor

-   The main goal of this lesson is to introduce the dplyr package -- a powerful 
    tool for data manipulation in **`R`**.
-   We will cover the basics such as selecting columns, filtering rows, chaining 
    commands with pipes, creating new columns with mutate, and summarizing data 
    by grouping.
-   When covering pipes, some may find it helpful to read the pipe like the word 
    "then". Thus, when explaining the workflow, phrase it as "we take the data 
    *then* we `filter` our rows *then* we `select` our columns"
-   While the dplyr functions simplify data wrangling, it's important to ensure 
    learners grasp each concept step by step to build a solid foundation for their
    future data analysis workflow.
-   If you would like additional information and visual representation of the 
    function, the following site showcases some good tutorials and visuals: 
    <https://tidydatatutor.com/>

::::::::::::

::::::::::::::::::::::::::::::::::::::: objectives

-   Understand the purpose of the **`dplyr`** package.
-   Learn how to select specific columns from a tibble using **`select`**.
-   Learn how to filter rows based on conditions using **`filter()`**.
-   Use the pipe operator **`(%\>%)`** to seamlessly chain multiple dplyr commands.
-   Create new columns in a tibble with **`mutate()`**, deriving them from existing 
    data.
-   Apply the split-apply-combine strategy using **`group_by()`** and **`summarize()`** 
    to generate summary statistics.
    
::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

-   How can I select specific rows and columns from a tibble using dplyr?
-   How does the pipe operator **`(%\>%)`** help in combining multiple commands 
    into a single workflow?
-   What is the advantage of using **`mutate()`** for creating new variables, and 
    how does it work?
-   How can I summarize my data by grouping observations and applying summary 
    statistics with dplyr?

::::::::::::::::::::::::::::::::::::::::::::::::::

**`dplyr`** is a powerful and intuitive package in **`R`** designed to make data 
manipulation both easy and efficient. It is part of the tidyverse ecosystem, which 
emphasizes readable, consistent syntax for working with data. We're going to learn 
some of the most common **`dplyr`** functions:

-   `select()`: subset columns
-   `filter()`: subset rows on conditions
-   `mutate()`: create new columns by using information from other columns
-   `group_by()` and `summarize()`: create summary statistics on grouped data
-   `arrange()`: sort results
-   `count()`: count discrete values

As covered in "Starting with Data", **`dplyr`** is also part of the tidyverse and 
will be loaded in R's memory when we call `library(tidyverse)`.

:::::::::::::::::::::::::::::::::::::::: callout

## Note
The packages in the tidyverse, namely **`dplyr`**, **`tidyr`** and **`ggplot2`** 
accept both the British (e.g. *summarise*) and American (e.g. *summarize*) spelling
variants of different function and option names. For this lesson, we utilize the 
American spellings of different functions; however, feel free to use either whichever 
variant feels best for you!

::::::::::::::::::::::::::::::::::::::::::::::::::

To begin working with **`dplyr`**, let's start by loading in the packages and data 
set:

``` r
#load packages
library(tidyverse)
library(here)

#read in data
data <- read_csv(here("data", "checkin_data.csv"))
```

## Selecting Columns

The first function we will be covering is the **`select()`** function! This 
function allows us to select specific columns of our data set and accepts two primary 
types of arguments: the original data set, and the column(s) to isolate.

In our case, for example, we are interested in seeing ONLY the precinct id's in 
our data set, so our arguments will be `data` and `precinct`:

``` r
#selects JUST the precinct column
select(data, precinct)
```

``` output
# A tibble: 352,112 × 1
   precinct    
   <chr>       
 1 PRECINCT_001
 2 PRECINCT_001
 3 PRECINCT_001
 4 PRECINCT_001
 5 PRECINCT_001
 6 PRECINCT_001
 7 PRECINCT_001
 8 PRECINCT_001
 9 PRECINCT_001
10 PRECINCT_001
# ℹ 352,102 more rows
```

Using the **`select`** function, you can also select MULTIPLE columns. This can 
be particularly helpful with larger data sets. Theoretically, this function can 
be performed using subsetting instead of the **`select`** function, but it's best 
practice to use dplyr functions when possible:

``` r
#selects the precinct column AND the checkin_time column
select(data, precinct, checkin_time)
```

``` output
# A tibble: 352,112 × 2
   precinct     checkin_time       
   <chr>        <dttm>             
 1 PRECINCT_001 2018-11-06 07:02:36
 2 PRECINCT_001 2018-11-06 07:04:09
 3 PRECINCT_001 2018-11-06 07:05:13
 4 PRECINCT_001 2018-11-06 07:06:26
 5 PRECINCT_001 2018-11-06 07:08:08
 6 PRECINCT_001 2018-11-06 07:08:32
 7 PRECINCT_001 2018-11-06 07:09:36
 8 PRECINCT_001 2018-11-06 07:10:18
 9 PRECINCT_001 2018-11-06 07:12:57
10 PRECINCT_001 2018-11-06 07:13:41
# ℹ 352,102 more rows
```

In some cases, you may want to select multiple, adjacent columns. Instead of writing 
out each individual column name directly, they can be selected with a **`:`**, as 
seen below:

``` r
#selects all columns from checkin_time to precinct
select(data, checkin_time:precinct)
```

You can see a visualized example of the **`select()`** function on [tidy data tutor](https://tidydatatutor.com/vis.html#code=library%28dplyr%29%0Alibrary%28palmerpenguins%29%0A%0Aset.seed%282021-12-03%29%0A%0Asample_penguins%20%3C-%20penguins%20%25%3E%25%0A%20%20group_by%28species%29%20%25%3E%25%20%0A%20%20sample_n%283%29%20%25%3E%25%20%0A%20%20select%28species,%20island,%20bill_length_mm%29%20%25%3E%25%20%0A%20%20ungroup%28%29%0A%0Asample_penguins%20%25%3E%25%20%0A%20%20select%28species,%20bill_length_mm%29&d=2025-04-18&lang=r&v=v1)

## Filtering Rows

Our next function we will be covering is the **`filter()`** function! This function 
allows us to choose rows based on specific criteria, and accepts two arguments: 
the original data set, and the condition to select the rows based off of. In this 
case, we ONLY want rows where the precinct is "PRECINCT_001":


``` r
#filters rows where the precinct is "PRECINCT_001"
filter(data, precinct == "PRECINCT_001")
```

``` output
# A tibble: 648 × 6
   checkin_id     checkin_length checkin_time        location    precinct device
   <chr>                   <dbl> <dttm>              <chr>       <chr>    <chr> 
 1 CHECKIN_000001             45 2018-11-06 07:02:36 LOCATION_0… PRECINC… DEVIC…
 2 CHECKIN_000002             29 2018-11-06 07:04:09 LOCATION_0… PRECINC… DEVIC…
 3 CHECKIN_000003             65 2018-11-06 07:05:13 LOCATION_0… PRECINC… DEVIC…
 4 CHECKIN_000004             28 2018-11-06 07:06:26 LOCATION_0… PRECINC… DEVIC…
 5 CHECKIN_000005             17 2018-11-06 07:08:08 LOCATION_0… PRECINC… DEVIC…
 6 CHECKIN_000006             56 2018-11-06 07:08:32 LOCATION_0… PRECINC… DEVIC…
 7 CHECKIN_000007             64 2018-11-06 07:09:36 LOCATION_0… PRECINC… DEVIC…
 8 CHECKIN_000008            262 2018-11-06 07:10:18 LOCATION_0… PRECINC… DEVIC…
 9 CHECKIN_000009            245 2018-11-06 07:12:57 LOCATION_0… PRECINC… DEVIC…
10 CHECKIN_000010            260 2018-11-06 07:13:41 LOCATION_0… PRECINC… DEVIC…
# ℹ 638 more rows
```

You can also use comparison operators within **`filter()`** arguments! This includes 
less-than (<), less-than or equal-to (<=), greater-than (>), greater-than or equal-to (>=),
or not-equal-to (!=).

For example, you could filter for all rows where the check-in length is less-than 
or equal-to 20 seconds:

``` r
#filters rows with the "less-than or equal-to"/"<=" operator
filter(data, checkin_length <= 20)
```

``` output
# A tibble: 32,264 × 6
   checkin_id     checkin_length checkin_time        location    precinct device
   <chr>                   <dbl> <dttm>              <chr>       <chr>    <chr> 
 1 CHECKIN_000005             17 2018-11-06 07:08:08 LOCATION_0… PRECINC… DEVIC…
 2 CHECKIN_000017             19 2018-11-06 07:20:40 LOCATION_0… PRECINC… DEVIC…
 3 CHECKIN_000059             19 2018-11-06 08:07:12 LOCATION_0… PRECINC… DEVIC…
 4 CHECKIN_000079             20 2018-11-06 08:25:41 LOCATION_0… PRECINC… DEVIC…
 5 CHECKIN_000092             18 2018-11-06 08:37:45 LOCATION_0… PRECINC… DEVIC…
 6 CHECKIN_000094             19 2018-11-06 08:39:38 LOCATION_0… PRECINC… DEVIC…
 7 CHECKIN_000119             17 2018-11-06 08:57:22 LOCATION_0… PRECINC… DEVIC…
 8 CHECKIN_000162             19 2018-11-06 09:30:57 LOCATION_0… PRECINC… DEVIC…
 9 CHECKIN_000163             20 2018-11-06 09:32:14 LOCATION_0… PRECINC… DEVIC…
10 CHECKIN_000190             18 2018-11-06 09:49:41 LOCATION_0… PRECINC… DEVIC…
# ℹ 32,254 more rows
```

Similarly to the **`select()`** function, the **`filter()`** function also allows 
us to specify multiple conditions. However, instead of splitting these by commas, 
conditions are combined using ‘and’, ‘or’, or comparison statements. 
 
In an ‘and’ statement, an observation (row) must meet *all* criteria to be included 
in the resulting tibble. To form ‘and’ statements within dplyr, we can pass our 
desired conditions as arguments in the **`filter()`** function, separated by an 
ampersand (&). 

Below, let's filter rows that include "PRECINCT_001" as the precinct *and* "DEVICE_002" 
as the device:

``` r
#filters rows with the "and"/"&" logical operator
filter(data, precinct == "PRECINCT_001" & device == "DEVICE_002")
```

``` output
# A tibble: 265 × 6
   checkin_id     checkin_length checkin_time        location    precinct device
   <chr>                   <dbl> <dttm>              <chr>       <chr>    <chr> 
 1 CHECKIN_000006             56 2018-11-06 07:08:32 LOCATION_0… PRECINC… DEVIC…
 2 CHECKIN_000009            245 2018-11-06 07:12:57 LOCATION_0… PRECINC… DEVIC…
 3 CHECKIN_000019             41 2018-11-06 07:23:05 LOCATION_0… PRECINC… DEVIC…
 4 CHECKIN_000026             22 2018-11-06 07:33:38 LOCATION_0… PRECINC… DEVIC…
 5 CHECKIN_000028             21 2018-11-06 07:35:44 LOCATION_0… PRECINC… DEVIC…
 6 CHECKIN_000031             33 2018-11-06 07:37:36 LOCATION_0… PRECINC… DEVIC…
 7 CHECKIN_000041             56 2018-11-06 07:49:06 LOCATION_0… PRECINC… DEVIC…
 8 CHECKIN_000044             23 2018-11-06 07:52:08 LOCATION_0… PRECINC… DEVIC…
 9 CHECKIN_000046             24 2018-11-06 07:54:06 LOCATION_0… PRECINC… DEVIC…
10 CHECKIN_000057             48 2018-11-06 08:05:54 LOCATION_0… PRECINC… DEVIC…
# ℹ 255 more rows
```

In an ‘or’ statement, an observation (row) must meet *at least one* criteria to 
be included in the resulting tibble. To form ‘or’ statements within dplyr, we can pass our 
desired conditions as arguments in the **`filter()`** function, separated by a 
vertical bar (|). 

Below, let's filter rows that include "PRECINCT_001" *or* "PRECINCT_002" as the 
precinct:

``` r
#filters rows with the "or"/"|" logical operator
filter(data, precinct == "PRECINCT_001" | precinct == "PRECINCT_002")
```

``` output
# A tibble: 905 × 6
   checkin_id     checkin_length checkin_time        location    precinct device
   <chr>                   <dbl> <dttm>              <chr>       <chr>    <chr> 
 1 CHECKIN_000001             45 2018-11-06 07:02:36 LOCATION_0… PRECINC… DEVIC…
 2 CHECKIN_000002             29 2018-11-06 07:04:09 LOCATION_0… PRECINC… DEVIC…
 3 CHECKIN_000003             65 2018-11-06 07:05:13 LOCATION_0… PRECINC… DEVIC…
 4 CHECKIN_000004             28 2018-11-06 07:06:26 LOCATION_0… PRECINC… DEVIC…
 5 CHECKIN_000005             17 2018-11-06 07:08:08 LOCATION_0… PRECINC… DEVIC…
 6 CHECKIN_000006             56 2018-11-06 07:08:32 LOCATION_0… PRECINC… DEVIC…
 7 CHECKIN_000007             64 2018-11-06 07:09:36 LOCATION_0… PRECINC… DEVIC…
 8 CHECKIN_000008            262 2018-11-06 07:10:18 LOCATION_0… PRECINC… DEVIC…
 9 CHECKIN_000009            245 2018-11-06 07:12:57 LOCATION_0… PRECINC… DEVIC…
10 CHECKIN_000010            260 2018-11-06 07:13:41 LOCATION_0… PRECINC… DEVIC…
# ℹ 895 more rows
```


you can see a visualized examples of the **`filter()`** function on [tidy data tutor](https://tidydatatutor.com/vis.html#code=library%28dplyr%29%0Alibrary%28palmerpenguins%29%0A%0Aset.seed%282021-12-03%29%0A%0Asample_penguins%20%3C-%20penguins%20%25%3E%25%0A%20%20group_by%28species%29%20%25%3E%25%20%0A%20%20sample_n%283%29%20%25%3E%25%20%0A%20%20select%28species,%20bill_length_mm%29%20%25%3E%25%20%0A%20%20ungroup%28%29%0A%0Asample_penguins%20%25%3E%25%20%0A%20%20filter%28bill_length_mm%20%3E%2045%29&d=2025-04-18&lang=r&v=v1)

## Using Pipes

In many cases, you will want to apply multiple functions at the same time! Within 
dplyr, there are three ways to do this.

1. Intermediate Steps:
Using this method, you apply the first function to your data and save the result 
as a new object. After saving, the second function is applied to your new object
instead of the original data. 
While this method is easy to understand, it can create many extra, unnecessary 
objects in your R environment.

``` r
#step 1: apply filter function and save it to a new object (filtered_data)
filtered_data <- filter(data, precinct == "PRECINCT_005")

#step 2: apply select function on the filtered_data object
select(filtered_data, precinct, checkin_time)
```

``` output
# A tibble: 762 × 2
   precinct     checkin_time       
   <chr>        <dttm>             
 1 PRECINCT_005 2018-11-06 11:39:28
 2 PRECINCT_005 2018-11-06 11:26:09
 3 PRECINCT_005 2018-11-06 18:25:45
 4 PRECINCT_005 2018-11-06 07:01:07
 5 PRECINCT_005 2018-11-06 07:01:22
 6 PRECINCT_005 2018-11-06 07:02:02
 7 PRECINCT_005 2018-11-06 07:02:02
 8 PRECINCT_005 2018-11-06 07:02:38
 9 PRECINCT_005 2018-11-06 07:02:50
10 PRECINCT_005 2018-11-06 07:03:23
# ℹ 752 more rows
```

2. Nested Functions: 
Instead of saving intermediate results, you can instead put your first function 
inside the second. This is called nesting, and, while it works, can become confusing 
if more than two functions are put together.

``` r
# Do it all in one go, nesting the functions
select(filter(data, precinct == "PRECINCT_005"), precinct, checkin_time)
```

``` output
# A tibble: 762 × 2
   precinct     checkin_time       
   <chr>        <dttm>             
 1 PRECINCT_005 2018-11-06 11:39:28
 2 PRECINCT_005 2018-11-06 11:26:09
 3 PRECINCT_005 2018-11-06 18:25:45
 4 PRECINCT_005 2018-11-06 07:01:07
 5 PRECINCT_005 2018-11-06 07:01:22
 6 PRECINCT_005 2018-11-06 07:02:02
 7 PRECINCT_005 2018-11-06 07:02:02
 8 PRECINCT_005 2018-11-06 07:02:38
 9 PRECINCT_005 2018-11-06 07:02:50
10 PRECINCT_005 2018-11-06 07:03:23
# ℹ 752 more rows
```

3. Using Pipes: 
**`Pipes`** allow you to connect your commands in a simple, step-by-step way. These 
let you take the output of one function and send it directly to the next, which is 
useful when you need to do many things to the same data set! 
When analyzing code with pipes, you can think of it as the word “then". 

``` r
#takes the data THEN applies the filter function THEN applies the select function
data %>%
  filter(precinct == "PRECINCT_005") %>%
  select(precinct, checkin_time)
```

``` output
# A tibble: 762 × 2
   precinct     checkin_time       
   <chr>        <dttm>             
 1 PRECINCT_005 2018-11-06 11:39:28
 2 PRECINCT_005 2018-11-06 11:26:09
 3 PRECINCT_005 2018-11-06 18:25:45
 4 PRECINCT_005 2018-11-06 07:01:07
 5 PRECINCT_005 2018-11-06 07:01:22
 6 PRECINCT_005 2018-11-06 07:02:02
 7 PRECINCT_005 2018-11-06 07:02:02
 8 PRECINCT_005 2018-11-06 07:02:38
 9 PRECINCT_005 2018-11-06 07:02:50
10 PRECINCT_005 2018-11-06 07:03:23
# ℹ 752 more rows
```

In the above code, you may have noticed that the `data` data set was not included 
as an argument in either of the functions. Since pipes take the object on its left 
and pass it as the first argument to the function on its right, we don't need to 
explicitly include the tibble as an argument to the `filter()` and `select()` 
functions anymore.

In R, there are two main types of pipe operators: 
1. **`|>`**: called the *native* pipe — included with base **`R`**.
2. **`%>%`**: called the *magrittr* pipe — installed automatically with **`dplyr`**. 
   This pipe is the most common, and what we will be using throughout this lesson.

Both pipes behave the exact same way, so the choice of which one to use is a matter 
of taste.

:::::::::::::::::::::::::::::::::::::::  challenge

## Exercise

Using pipes, filter the `data` data set to include only observations where the 
`device` is `"DEVICE_738"` select only the columns `precinct`, `checkin_time`, 
and `device`.

:::::::::::::::  solution

## Solution


``` r
data %>%
  filter(device == "DEVICE_738") %>%
  select(precinct, checkin_time, device)
```

``` output
# A tibble: 134 × 3
   precinct     checkin_time        device    
   <chr>        <dttm>              <chr>     
 1 PRECINCT_332 2018-11-06 07:02:26 DEVICE_738
 2 PRECINCT_332 2018-11-06 07:03:10 DEVICE_738
 3 PRECINCT_332 2018-11-06 07:03:56 DEVICE_738
 4 PRECINCT_332 2018-11-06 07:04:27 DEVICE_738
 5 PRECINCT_332 2018-11-06 07:05:01 DEVICE_738
 6 PRECINCT_332 2018-11-06 07:06:00 DEVICE_738
 7 PRECINCT_332 2018-11-06 07:06:36 DEVICE_738
 8 PRECINCT_332 2018-11-06 07:07:03 DEVICE_738
 9 PRECINCT_332 2018-11-06 07:07:45 DEVICE_738
10 PRECINCT_332 2018-11-06 07:08:24 DEVICE_738
# ℹ 124 more rows
```
:::::::::::::::::::::::::


::::::::::::::::::::::::::::::::::::::::::::::::::



## Split-Apply-Combine Data Analysis

Many data analysis tasks follow a pattern known as *split-apply-combine*:
1. **Split** the data into groups.
2. **Apply** some analysis or calculation to each group.
3. **Combine** the results into a summary

The **`dplyr`** package makes this easy with two main functions:
- `group_by()` to define how you want to split the data.
- `summarize()` to apply one or more calculations on each group and return a summary.

### `group_by()`

The **``group_by()`** function allows us to treat parts of our data set as separate 
groups so other functions can work within each group instead of on the entire data 
set. This function accepts the one or more columns to group-by as arguments! 

Below, we will be grouping the data by location, and filtering the rows to only 
include the check-in(s) with the longest check-in length for each location:

``` r
#groups the data by location and applies filter
data %>%
  group_by(location) %>%
  filter(checkin_length == max(checkin_length))
```

``` output
# A tibble: 561 × 6
# Groups:   location [417]
   checkin_id     checkin_length checkin_time        location    precinct device
   <chr>                   <dbl> <dttm>              <chr>       <chr>    <chr> 
 1 CHECKIN_000032            300 2018-11-06 07:37:43 LOCATION_0… PRECINC… DEVIC…
 2 CHECKIN_000106            300 2018-11-06 08:51:39 LOCATION_0… PRECINC… DEVIC…
 3 CHECKIN_000640            300 2018-11-06 19:47:13 LOCATION_0… PRECINC… DEVIC…
 4 CHECKIN_000839            300 2018-11-06 16:50:29 LOCATION_0… PRECINC… DEVIC…
 5 CHECKIN_001137            299 2018-11-06 10:03:21 LOCATION_0… PRECINC… DEVIC…
 6 CHECKIN_002362            298 2018-11-06 19:09:12 LOCATION_0… PRECINC… DEVIC…
 7 CHECKIN_002572            299 2018-11-06 10:46:01 LOCATION_0… PRECINC… DEVIC…
 8 CHECKIN_003919            300 2018-11-06 18:05:53 LOCATION_0… PRECINC… DEVIC…
 9 CHECKIN_004805            298 2018-11-06 17:57:33 LOCATION_0… PRECINC… DEVIC…
10 CHECKIN_005944            300 2018-11-06 16:17:04 LOCATION_0… PRECINC… DEVIC…
# ℹ 551 more rows
```

Additionally, when multiple columns are provided, **``group_by()`** goes from 
left-to-right, grouping by the first column, then within each group by the second, 
and so on!

Below, we will be doing the same calculation that we did above, but instead of 
grouping only by location, we will be grouping by location *and* device:

``` r
#groups the data by location and applies filter
data %>%
  group_by(location, device) %>%
  filter(checkin_length == max(checkin_length))
```

``` output
# A tibble: 1,344 × 6
# Groups:   location, device [1,215]
   checkin_id     checkin_length checkin_time        location    precinct device
   <chr>                   <dbl> <dttm>              <chr>       <chr>    <chr> 
 1 CHECKIN_000032            300 2018-11-06 07:37:43 LOCATION_0… PRECINC… DEVIC…
 2 CHECKIN_000106            300 2018-11-06 08:51:39 LOCATION_0… PRECINC… DEVIC…
 3 CHECKIN_000640            300 2018-11-06 19:47:13 LOCATION_0… PRECINC… DEVIC…
 4 CHECKIN_000774            295 2018-11-06 12:52:23 LOCATION_0… PRECINC… DEVIC…
 5 CHECKIN_000839            300 2018-11-06 16:50:29 LOCATION_0… PRECINC… DEVIC…
 6 CHECKIN_001015            296 2018-11-06 08:36:18 LOCATION_0… PRECINC… DEVIC…
 7 CHECKIN_001137            299 2018-11-06 10:03:21 LOCATION_0… PRECINC… DEVIC…
 8 CHECKIN_001792            290 2018-11-06 08:00:47 LOCATION_0… PRECINC… DEVIC…
 9 CHECKIN_002210             75 2018-11-06 16:11:09 LOCATION_0… PRECINC… DEVIC…
10 CHECKIN_002362            298 2018-11-06 19:09:12 LOCATION_0… PRECINC… DEVIC…
# ℹ 1,334 more rows
```

As you can see, there are additional rows, since we are looking at the longest check-in
times for each device within each location, instead of just within each location!

After completing analysis, you may want to remove grouping! To do so, you can use 
the **`ungroup()`** function:

``` r
data %>%
  group_by(location, device) %>% 
  filter(checkin_length == max(checkin_length)) %>%
  ungroup()
```

``` output
# A tibble: 1,344 × 6
   checkin_id     checkin_length checkin_time        location    precinct device
   <chr>                   <dbl> <dttm>              <chr>       <chr>    <chr> 
 1 CHECKIN_000032            300 2018-11-06 07:37:43 LOCATION_0… PRECINC… DEVIC…
 2 CHECKIN_000106            300 2018-11-06 08:51:39 LOCATION_0… PRECINC… DEVIC…
 3 CHECKIN_000640            300 2018-11-06 19:47:13 LOCATION_0… PRECINC… DEVIC…
 4 CHECKIN_000774            295 2018-11-06 12:52:23 LOCATION_0… PRECINC… DEVIC…
 5 CHECKIN_000839            300 2018-11-06 16:50:29 LOCATION_0… PRECINC… DEVIC…
 6 CHECKIN_001015            296 2018-11-06 08:36:18 LOCATION_0… PRECINC… DEVIC…
 7 CHECKIN_001137            299 2018-11-06 10:03:21 LOCATION_0… PRECINC… DEVIC…
 8 CHECKIN_001792            290 2018-11-06 08:00:47 LOCATION_0… PRECINC… DEVIC…
 9 CHECKIN_002210             75 2018-11-06 16:11:09 LOCATION_0… PRECINC… DEVIC…
10 CHECKIN_002362            298 2018-11-06 19:09:12 LOCATION_0… PRECINC… DEVIC…
# ℹ 1,334 more rows
```

The final table will no longer be considered “grouped”, which can be helpful if
you plan to do further operations that don’t rely on grouping.

### `summarize()`

The **``summarize()`** function is often used alongside **``group_by()`**, as it 
allows us to reduce a group of rows to a single row per group. This function accepts 
one or more expressions that compute summary statistics as arguments!

Some common **``summarize()`** summary functions include:
- mean(): calculates the average of a numeric column
- max()/min(): returns the maximum or minimum of a group
- n(): counts the number of rows in a group
- n_distinct(): counts the number of unique values in a column

Suppose we want to see how many total check-ins there were for each precinct in 
our data set. We can do this by grouping the data by the `precinct` column using 
**``group_by()`** and then using the **``summarize()`** function to count each 
row within each precinct group, as seen below:

``` r
data %>%
  group_by(precinct) %>%
  summarize(total_checkins = n())
```

``` output
# A tibble: 420 × 2
   precinct     total_checkins
   <chr>                 <int>
 1 PRECINCT_001            648
 2 PRECINCT_002            257
 3 PRECINCT_003            806
 4 PRECINCT_004            466
 5 PRECINCT_005            762
 6 PRECINCT_006            676
 7 PRECINCT_007           1347
 8 PRECINCT_008           1652
 9 PRECINCT_009            742
10 PRECINCT_010            882
# ℹ 410 more rows
```

We can also apply **``summarize()`** on data that has been grouped by multiple 
columns! Below, we will be grouping by precinct and device, allowing us to see how
many check-ins occurred for each device within each precinct:

``` r
data %>%
  group_by(precinct, device) %>%
  summarize(total_checkins = n())
```

``` output
`summarise()` has grouped output by 'precinct'. You can override using the
`.groups` argument.
```

``` output
# A tibble: 1,778 × 3
# Groups:   precinct [420]
   precinct     device     total_checkins
   <chr>        <chr>               <int>
 1 PRECINCT_001 DEVICE_001            381
 2 PRECINCT_001 DEVICE_002            265
 3 PRECINCT_001 DEVICE_671              1
 4 PRECINCT_001 DEVICE_844              1
 5 PRECINCT_002 DEVICE_003            125
 6 PRECINCT_002 DEVICE_004            131
 7 PRECINCT_002 DEVICE_536              1
 8 PRECINCT_003 DEVICE_005            449
 9 PRECINCT_003 DEVICE_006            357
10 PRECINCT_004 DEVICE_006              1
# ℹ 1,768 more rows
```

You’re not limited to a single summary statistic, either! For example, you might 
want both the total number of check-ins *and* the number of unique devices for 
each precinct. You can combine these in one **`summarize()`** call:

``` r
data %>%
  group_by(precinct) %>%
  summarize(
    total_checkins = n(),
    unique_devices = n_distinct(device)
  )
```

``` output
# A tibble: 420 × 3
   precinct     total_checkins unique_devices
   <chr>                 <int>          <int>
 1 PRECINCT_001            648              4
 2 PRECINCT_002            257              3
 3 PRECINCT_003            806              2
 4 PRECINCT_004            466              5
 5 PRECINCT_005            762              5
 6 PRECINCT_006            676              2
 7 PRECINCT_007           1347              5
 8 PRECINCT_008           1652              5
 9 PRECINCT_009            742              7
10 PRECINCT_010            882              6
# ℹ 410 more rows
```

Additionally, if you need to exclude certain rows before summarizing, ensure you use 
**`filter()`** before grouping. For example, to include only check-ins from a 
specific location, you can do the following:

``` r
data %>%
  filter(location == "LOCATION_001") %>%
  group_by(precinct) %>%
  summarize(total_checkins = n())
```

``` output
# A tibble: 1 × 2
  precinct     total_checkins
  <chr>                 <int>
1 PRECINCT_001            646
```

Additional examples of the **`group_by()`** and **`summarize()`** functions can 
be found at [tidy data tutor](https://tidydatatutor.com/vis.html#code=library%28dplyr%29%0Alibrary%28palmerpenguins%29%0A%0Aset.seed%282021-12-03%29%0A%0Asample_penguins%20%3C-%20penguins%20%25%3E%25%0A%20%20group_by%28species%29%20%25%3E%25%20%0A%20%20sample_n%283%29%20%25%3E%25%20%0A%20%20select%28species,%20bill_length_mm%29%0A%0Asample_penguins%20%25%3E%25%20%0A%20%20summarise%28Avg_Bill_Length%20%3D%20mean%28bill_length_mm%29%29&d=2025-04-18&lang=r&v=v1)

### `arrange()`

After summarizing, you may want to sort your results. To do so, you can use the 
**`arrange()`** function to reorder rows. For example, to list precincts from lowest
to highest check-in counts, you can do the following:

``` r
data %>%
  group_by(precinct) %>%
  summarize(total_checkins = n()) %>%
  arrange(total_checkins)
```

``` output
# A tibble: 420 × 2
   precinct     total_checkins
   <chr>                 <int>
 1 PRECINCT_092              2
 2 PRECINCT_360             11
 3 PRECINCT_411             37
 4 PRECINCT_345             42
 5 PRECINCT_101             43
 6 PRECINCT_253             58
 7 PRECINCT_355             60
 8 PRECINCT_175             64
 9 PRECINCT_031             66
10 PRECINCT_403             68
# ℹ 410 more rows
```

Or, to instead arrange from highest to lowest, include desc() around the **`arrange()`**
attribute, as seen below:
# ```{r arrange-desc, purl=FALSE}
data %>%
  group_by(precinct) %>%
  summarize(total_checkins = n()) %>%
  arrange(desc(total_checkins))
```

An additional example of the **`arrange()`** function can be found [at tidy data tutor](https://tidydatatutor.com/vis.html#code=library%28dplyr%29%0Alibrary%28palmerpenguins%29%0A%0Aset.seed%282021-12-03%29%0A%0Asample_penguins%20%3C-%20penguins%20%25%3E%25%0A%20%20group_by%28species%29%20%25%3E%25%20%0A%20%20sample_n%283%29%20%25%3E%25%20%0A%20%20select%28species,%20bill_length_mm%29%20%25%3E%25%20%0A%20%20ungroup%28%29%0A%0Asample_penguins%20%25%3E%25%20%0A%20%20arrange%28bill_length_mm%29&d=2025-04-18&lang=r&v=v1)

### `count()`

When working with data, we often want to know how many observations we have for 
each factor or combination of factors. As you saw above, we were able to complete 
this using the **`group_by()`** function, followed by the **`summarize()`** function.

However, since this is such a common task, **`dplyr`** provides the **`count()`** 
function to make this task much quicker and easier to write and perform!

For example, if we want to count the number of check-ins for each precinct, instead 
of grouping by precinct and summarizing using the `n()` function, we can do the 
following:

``` r
data %>%
    count(precinct)
```

``` output
# A tibble: 420 × 2
   precinct         n
   <chr>        <int>
 1 PRECINCT_001   648
 2 PRECINCT_002   257
 3 PRECINCT_003   806
 4 PRECINCT_004   466
 5 PRECINCT_005   762
 6 PRECINCT_006   676
 7 PRECINCT_007  1347
 8 PRECINCT_008  1652
 9 PRECINCT_009   742
10 PRECINCT_010   882
# ℹ 410 more rows
```

Additionally, if you'd like your results sorted, instead of using the **`arrange()`**
function, you can add "sort = TRUE" as an argument to the **`count()`** function, 
as seen below:

``` r
data %>%
    count(precinct, sort = TRUE)
```

``` output
# A tibble: 420 × 2
   precinct         n
   <chr>        <int>
 1 PRECINCT_219  1968
 2 PRECINCT_016  1807
 3 PRECINCT_271  1798
 4 PRECINCT_317  1731
 5 PRECINCT_358  1717
 6 PRECINCT_239  1705
 7 PRECINCT_199  1700
 8 PRECINCT_323  1695
 9 PRECINCT_106  1680
10 PRECINCT_045  1671
# ℹ 410 more rows
```

:::::::::::::::::::::::::::::::::::::::  challenge

## Exercise

Using what you've learned above, determine how many check-ins were recorded for 
each device. Which device had the highest number of check-ins?

:::::::::::::::  solution

## Solution


``` r
data %>%
    count(device, sort = TRUE)
```

``` output
# A tibble: 1,215 × 2
   device         n
   <chr>      <int>
 1 DEVICE_255   898
 2 DEVICE_190   894
 3 DEVICE_642   887
 4 DEVICE_178   850
 5 DEVICE_435   821
 6 DEVICE_960   817
 7 DEVICE_959   812
 8 DEVICE_436   796
 9 DEVICE_641   782
10 DEVICE_822   769
# ℹ 1,205 more rows
```

"DEVICE_255" has the highest number of check ins, with 898 recorded!

:::::::::::::::::::::::::

For "PRECINCT_007", find the device that recorded the least amount of check-ins.

**Hint:** ensure you filter your data before applying split-apply-combine!

:::::::::::::::  solution

## Solution


``` r
data %>%
  filter(precinct == "PRECINCT_007") %>%
  group_by(device) %>%
  summarize(total_checkins = n()) %>%
  arrange(desc(total_checkins))
```

``` output
# A tibble: 5 × 2
  device     total_checkins
  <chr>               <int>
1 DEVICE_919            462
2 DEVICE_917            448
3 DEVICE_918            426
4 DEVICE_920             10
5 DEVICE_009              1
```

"DEVICE_009" had the least amount of check-ins, recording only 1.

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

## Mutating Data

Sometimes, you may want to create new columns based on values in existing columns. 
For example, if you have a column represented in seconds, and you might want to 
add a new column with the same information, but represented as minutes instead. 

To complete this, we use the **``mutate()`** function. This function allows us to 
create new columns OR modify existing columns by applying operations to each row 
of the data set!

For example, let's say that we want to create a new column that, as mentioned above,
converts the `checkin_length` column (which is in seconds) into minutes by dividing 
each value by 60. Below, we can use the mutate function to add this column to our data:

``` r
data %>%
    mutate(checkin_length_min = checkin_length / 60)
```

``` output
# A tibble: 352,112 × 7
   checkin_id     checkin_length checkin_time        location    precinct device
   <chr>                   <dbl> <dttm>              <chr>       <chr>    <chr> 
 1 CHECKIN_000001             45 2018-11-06 07:02:36 LOCATION_0… PRECINC… DEVIC…
 2 CHECKIN_000002             29 2018-11-06 07:04:09 LOCATION_0… PRECINC… DEVIC…
 3 CHECKIN_000003             65 2018-11-06 07:05:13 LOCATION_0… PRECINC… DEVIC…
 4 CHECKIN_000004             28 2018-11-06 07:06:26 LOCATION_0… PRECINC… DEVIC…
 5 CHECKIN_000005             17 2018-11-06 07:08:08 LOCATION_0… PRECINC… DEVIC…
 6 CHECKIN_000006             56 2018-11-06 07:08:32 LOCATION_0… PRECINC… DEVIC…
 7 CHECKIN_000007             64 2018-11-06 07:09:36 LOCATION_0… PRECINC… DEVIC…
 8 CHECKIN_000008            262 2018-11-06 07:10:18 LOCATION_0… PRECINC… DEVIC…
 9 CHECKIN_000009            245 2018-11-06 07:12:57 LOCATION_0… PRECINC… DEVIC…
10 CHECKIN_000010            260 2018-11-06 07:13:41 LOCATION_0… PRECINC… DEVIC…
# ℹ 352,102 more rows
# ℹ 1 more variable: checkin_length_min <dbl>
```

Admittedly, this operation doesn't tell us anything additional about our data, as 
it only converts part of our data into a different format. But, with a more complex 
operation we could, for example, add a column that says whether a check-in length 
is "abnormal" or not! 

For the sake of the example, let's say that any check-in length greater-than or equal-to 
200 seconds is abnormal:

``` r
data %>%
  mutate(checkin_category = ifelse(checkin_length >= 200, "abnormal", "normal"))
```

``` output
# A tibble: 352,112 × 7
   checkin_id     checkin_length checkin_time        location    precinct device
   <chr>                   <dbl> <dttm>              <chr>       <chr>    <chr> 
 1 CHECKIN_000001             45 2018-11-06 07:02:36 LOCATION_0… PRECINC… DEVIC…
 2 CHECKIN_000002             29 2018-11-06 07:04:09 LOCATION_0… PRECINC… DEVIC…
 3 CHECKIN_000003             65 2018-11-06 07:05:13 LOCATION_0… PRECINC… DEVIC…
 4 CHECKIN_000004             28 2018-11-06 07:06:26 LOCATION_0… PRECINC… DEVIC…
 5 CHECKIN_000005             17 2018-11-06 07:08:08 LOCATION_0… PRECINC… DEVIC…
 6 CHECKIN_000006             56 2018-11-06 07:08:32 LOCATION_0… PRECINC… DEVIC…
 7 CHECKIN_000007             64 2018-11-06 07:09:36 LOCATION_0… PRECINC… DEVIC…
 8 CHECKIN_000008            262 2018-11-06 07:10:18 LOCATION_0… PRECINC… DEVIC…
 9 CHECKIN_000009            245 2018-11-06 07:12:57 LOCATION_0… PRECINC… DEVIC…
10 CHECKIN_000010            260 2018-11-06 07:13:41 LOCATION_0… PRECINC… DEVIC…
# ℹ 352,102 more rows
# ℹ 1 more variable: checkin_category <chr>
```

This code filters out duplicate entries, showing just one record for each unique precinct and the new column we just added.

Additional examples of the **`mutate()`** function can be found at [tidy data tutor](https://tidydatatutor.com/vis.html#code=library%28dplyr%29%0Alibrary%28palmerpenguins%29%0A%0Aset.seed%282021-12-03%29%0A%0Asample_penguins%20%3C-%20penguins%20%25%3E%25%0A%20%20group_by%28species%29%20%25%3E%25%20%0A%20%20sample_n%283%29%20%25%3E%25%20%0A%20%20select%28bill_length_mm,%20bill_depth_mm%29%20%25%3E%25%20%0A%20%20ungroup%28%29%20%25%3E%25%0A%20%20select%28bill_length_mm,%20bill_depth_mm%29%0A%0Asample_penguins%20%25%3E%25%20%0A%20%20mutate%28bill_volume%20%3D%20bill_length_mm%20*%20bill_depth_mm%29&d=2025-04-18&lang=r&v=v1)

:::::::::::::::::::::::::::::::::::::::  challenge

## Exercise

Using what you've learned throughout this lesson, create a tibble called 
"avg_checkins" that meets the following criteria:
1. Includes only precincts from "PRECINCT_001" to "PRECINCT_035".
2. Removes the "PRECINCT_0" prefix from the precinct names and converts each precinct 
   name to a numeric value.
3. Calculates the average check-in length for each precinct, ensuring this column is 
   named "avg_checkin_length".
4. Contains two columns: "precinct" and "avg_checkin_length".
5. Sorts the tibble by precinct (1 to 35).

:::::::::::::::  solution

## Solution


``` r
avg_checkins <- data %>%
  mutate(precinct = as.numeric(str_remove(precinct, "PRECINCT_0"))) %>%
  filter(precinct >= 1 & precinct <= 35) %>%
  group_by(precinct) %>%
  summarize(avg_checkin_length = mean(checkin_length)) %>%
  arrange(precinct)
```

``` warning
Warning: There was 1 warning in `mutate()`.
ℹ In argument: `precinct = as.numeric(str_remove(precinct, "PRECINCT_0"))`.
Caused by warning:
! NAs introduced by coercion
```

:::::::::::::::::::::::::

Save your new "avg_checkins" into your data folder as "avg_checkins.csv"!

:::::::::::::::  solution

## Solution


``` r
write_csv(avg_checkins, "data/avg_checkins.csv")
```

:::::::::::::::::::::::::


::::::::::::::::::::::::::::::::::::::::::::::::::


::: keypoints
-   Use the `dplyr` package to manipulate tibbles.
-   Use `select()` to choose variables from a tibble.
-   Use `filter()` to choose data based on values.
-   Use `group_by()` and `summarize()` to work with subsets of data.
-   Use `mutate()` to create new variables.
:::
