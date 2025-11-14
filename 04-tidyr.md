---
title: Data Wrangling with tidyr
teaching: 25
exercises: 15
source: Rmd
---

:::: instructor

- The primary goal is to help learners understand how to clean their data and change 
  table formats for different uses.
- Similarly to Data Wrangling with dplyr, this lesson works better if you can 
  use graphics to demonstrate the difference between long and wide table formats.
  We have created [this Google Slides deck](https://docs.google.com/presentation/d/1RjhLkrMPvq97XksvpOgTdIHkw-wHEpuOTqK_rrz0nY4/edit?usp=sharing) 
  for this purpose!

::::::::::::


::::::::::::::::::::::::::::::::::::::: objectives

- Describe the concept of a wide and a long table format and for which purpose 
  those formats are useful.
- Describe the roles of variable names and their associated values when a table 
  is reshaped.
- Reshape a tibble from long to wide format and back with the `pivot_wider` 
  and `pivot_longer` commands from the **`tidyr`** package.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- How can I reformat a tibble to meet my needs?

::::::::::::::::::::::::::::::::::::::::::::::::::

**`dplyr`** pairs nicely with **`tidyr`**, a package that enables you to swiftly
convert between different data formats (long vs. wide) for plotting and analysis.
To learn more about **`tidyr`** after the workshop, you may want to check out this
[handy data tidying with **`tidyr`** cheatsheet](https://github.com/rstudio/cheatsheets/blob/main/tidyr.pdf).

To make sure everyone will use the same data sets for this lesson, we'll be
reading in the updated version of the Check-In Dataset (as created in 
"Starting With Data"), as well as the Messy Dataset (which we will cover at
the end of this lesson).

## Reading in Data
To start, we will load in the **`tidyverse`** and **`here`** packages so we can 
read in our CSV files.

``` r
library(tidyverse)
library(here)
```

``` error
Error in library(here): there is no package called 'here'
```

Next, we will read in the Check-In Data:

``` r
data <- read_csv(here("data", "checkin_data_2.csv"))
```

``` error
Error in here("data", "checkin_data_2.csv"): could not find function "here"
```

## Reshaping with pivot\_wider() and pivot\_longer()

There are essentially three rules that define a "tidy" data set:

1. Each variable has its own column
2. Each observation has its own row
3. Each value must have its own cell

This graphic visually represents the three rules that define a "tidy" data set:

![Visual representation of the three rules that define tidy data: variables in columns, observations in rows, and values in cells](fig/tidy-data-wickham.png){alt="A visual representation of the three rules that define a 'tidy' data set."}
*R for Data Science*, Wickham H and Grolemund G ([https://r4ds.had.co.nz/index.html](https://r4ds.had.co.nz/index.html))
© Wickham, Grolemund 2017
This image is licensed under Attribution-NonCommercial-NoDerivs 3.0 United States (CC-BY-NC-ND 3.0 US)

In this section we will explore how these rules are linked to the different
data formats researchers are often interested in: "wide" and "long". This
tutorial will help you efficiently transform your data shape, regardless of its
original format. 

First, we will explore qualities of the `data` data and how they relate 
to these different types of data formats.

### Long and Wide Data Formats

In `data`, each row contains the values of variables associated
with each record collected (each ballot instance). As you may recall from 
"Starting With Data", it was stated that the `checkin_id` was added to provide 
a "unique key/ID" for each individual ballot. 

Since `checkin_id` is unique to each instance, we can use this variable as an
identifier corresponding to each of the 352112 observations.


``` r
data %>% 
  select(checkin_id) %>% 
  distinct() %>%
  nrow()
```

``` error
Error in UseMethod("select"): no applicable method for 'select' applied to an object of class "function"
```

As seen in the code below, for each check-in time corresponding to each device, no 
two `checkin_id`s are the same. Thus, this format is what we call a "long" data 
format, where each observation occupies only one row in the tibble.


``` r
data %>%
  filter(location == "LOCATION_001") %>%
  select(checkin_id, checkin_time, location) %>%
  sample_n(size = 10)
```

``` error
Error in UseMethod("filter"): no applicable method for 'filter' applied to an object of class "function"
```

If you were to look at the entire `data` data set, you would notice that the 
layout/format of the data adheres to rules 1-3, where:

1. each column is a variable
2. each row is an observation
3. each value has its own cell

As mentioned above, this is called a "long" data format. Additionally, you may 
notice that each column represents a different variable. In the "longest" data 
format there would only be three columns, one for the id variable, one for the 
observed variable, and one for the observed value (of that variable). This data 
format is quite unsightly and difficult to work with, so you will rarely see it 
in use.

Alternatively, in a "wide" data format we see modifications to rule 1, where
each column no longer represents a single variable. Instead, columns can
represent different levels/values of a variable. For instance, in some data you
encounter, the researchers may have chosen for every check-in hour to be a
different column.

These may sound like dramatically different data layouts, but there are some
tools that make transitions between these layouts much simpler than you might
think! The GIF below shows how these two formats relate to each other, and
gives you an idea of how we can use R to shift from one format to the other.

![Animation showing pivot_wider and pivot_longer functions transforming data between long and wide formats](fig/tidyr-pivot_wider_longer.gif){alt="A gif showing how long and wide tibble layouts relate to each other."}

Long and wide tibble layouts mainly affect readability. You may find that
visually you may prefer the "wide" format, since you can see more of the data on
the screen. However, all of the R functions we have used thus far expect for
your data to be in a "long" data format. This is because the long format is more
machine readable and is closer to the formatting of databases.


### Questions That Warrant Different Data Formats

In `data`, each row contains values associated with each
record (the unit). This may include values such as the ID of the ballot box, the 
ballot box's location, the precinct the ballot box belongs to, or the arrival time 
of the person submitting the ballot. This format allows for us to make comparisons 
across individual ballot instances!

However, what if we wanted to look at how many check-ins occurred each hour in 
regards to each polling location?

To facilitate this comparison, we would need to create a new table where each
row (the unit) represents a polling location (associated with the `location`
column), each column (after the first) represents an hour of the day (associated
with the `hour` column), and the values of each row containing the number of
check-ins recorded at that location during that hour.

Once we we've created this new table, we can explore the relationships within and
between locations. The key point here is that we are still following a tidy data
structure, but we have **reshaped** the data according to the observations of
interest.

Alternatively, let's say the check-in times were originally spread across 
multiple columns, and we were interested in visualizing, across multiple 
locations, how check-in activity has changed over the course of the day. This 
would require the check-in time to be included in a single column rather than
spread across multiple columns. Thus, we would need to transform the column 
names into the values of a variable.

We can do both of these transformations with two **`tidyr`** functions,
`pivot_wider()` and `pivot_longer()`.

## Pivoting Wider

`pivot_wider()` takes in three principal arguments:

1. the data to be transformed
2. the *names\_from* column variable (whose values will become new column names).
3. the *values\_from* column variable (whose values will fill the new column
  variables).

Further arguments include `values_fill` which, if set, fills in missing values
with the value provided, and `names_sort`, which, if set, sorts the columns in 
alphanumerical order.

Let's use `pivot_wider()` to transform `data` to create new columns for each
hour represented within the data.

To help with understanding, we will be walking through the transformation line-by-line. 

First we create a new object (`data_tc`) based on the `data` tibble:

``` r
data_tc <- data %>%
```

Our next step will be to get the values for each cell, so we will be using the 
`count()` function from the **`dplyr`** package. This is completed in the next 
line, grouping by `location` and `hour`:

``` r
count(location, hour) %>%
```

Finally, we will be creating and populating the new, "wide" data using the
counts and the column values! This can be seen below:

``` r
pivot_wider(
  names_from = hour,
  values_from = n,
  values_fill = 0
)
```

Now that we understand what's going on, let's combine all those chunks together 
and look at what our completed tibble looks like!

``` r
#create the object
data_tc <- data %>%
  #get the values
  count(location, hour) %>%
  #pivot the data
  pivot_wider(
  names_from = hour,
  values_from = n,
  values_fill = 0
)
```

``` error
Error in UseMethod("count"): no applicable method for 'count' applied to an object of class "function"
```

``` r
head(data_tc)
```

``` error
Error: object 'data_tc' not found
```

Oh no! It looks like the hours columns are out of order, with 6 sitting between
19 and 20. If we were to perform data analysis, this would not matter, but 
visually, this can be confusing or misleading, since we expect time to move from 
left to right in ascending order.

In order to fix this, we can add the aforementioned `names_sort` argument to the 
function to specify that the columns should be in order. This line has been added 
to the code block below:

``` r
#create the object
data_tc <- data %>%
  #get the values
  count(location, hour) %>%
  #pivot the data
  pivot_wider(
  names_from = hour,
  values_from = n,
  values_fill = 0,
  names_sort = TRUE #sorts the columns from left to right
)
```

``` error
Error in UseMethod("count"): no applicable method for 'count' applied to an object of class "function"
```

``` r
head(data_tc)
```

``` error
Error: object 'data_tc' not found
```

As seen by the outputted tibble above, the hour columns now appear in ascending 
order, making the table far easier to interpret at a glance!

Now that we've used `pivot_wider()` to make our data "wide", let's take a closer 
look at the resulting `data_tc` tibble to gain a better understanding.

First, let's check the dimensions:

``` r
dim(data_tc)
```

``` error
Error: object 'data_tc' not found
```

As we can see, there are 417 rows and 16 columns! Each row represents a unique 
location within the data set. We can verify this by counting the number of unique 
location values within `data`:

``` r
n_distinct(data$location)
```

``` error
Error in data$location: object of type 'closure' is not subsettable
```

This also returns 417, confirming that each row corresponds to a single, unique 
location within the data.

Next, let's look at the 16 columns of the tibble:

``` r
colnames(data_tc)
```

``` error
Error: object 'data_tc' not found
```

Notice there is no longer a column titled `hour`. This is because the `pivot_wider()` 
function, by default, removes the original column that the new column values 
were taken from. In this case, the values from the original `hour` column have 
now become columns with names that range from 6 to 20, representing the hours 
from 6AM to 8PM, and thus the `hour` column has been dropped.

This new format of the data allows us to do interesting things, like make a table
showing the number of check-ins across all locations at a particular time, with 
the rows being ordered from highest to lowest in terms of count:


``` r
data_tc %>%
  select(location, `7`) %>%
  arrange(desc(`7`))
```

``` error
Error: object 'data_tc' not found
```

Or, we can calculate the total amount of check-ins for each location across all 
hours, and sort the data to determine which location had the least check-ins:

``` r
data_tc %>%
  mutate(total_checkins = rowSums(data_tc[-1])) %>%
  select(location, total_checkins) %>%
  arrange(total_checkins)
```

``` error
Error: object 'data_tc' not found
```

:::::::::::::::::::::::::::::::::::::::  challenge

## Exercise

We created `data_tc` by reshaping the data. Replicate this process
to create a tibble named `data_total` that shows the total number of 
check-ins for each hour, across all locations.

The resulting tibble should have columns for each hour, sorted from earliest to 
latest similarly to the `data_tc` tibble. There should only be one 
row, representative of all locations, and an extra summary column, called 
`total_checkins`, that calculates the total number of check ins across the entire 
`data` data set.

:::::::::::::::  solution

## Solution


``` r
data_total <- data %>%
  count(hour) %>%
  pivot_wider(
    names_from = hour,
    values_from = n,
    values_fill = 0,
    names_sort = TRUE
    ) %>%
  mutate(total_checkins = rowSums(across(everything())))
```

``` error
Error in UseMethod("count"): no applicable method for 'count' applied to an object of class "function"
```

``` r
data_total
```

``` error
Error: object 'data_total' not found
```

``` r
#alternative solution:
data_total_2 <- data %>%
  count(hour) %>%
  pivot_wider(
    names_from = hour,
    values_from = n,
    values_fill = 0,
    names_sort = TRUE
    )
```

``` error
Error in UseMethod("count"): no applicable method for 'count' applied to an object of class "function"
```

``` r
data_total_2 <- data_total_2 %>%
  mutate(total_checkins = rowSums(data_total_2))
```

``` error
Error: object 'data_total_2' not found
```

``` r
data_total_2
```

``` error
Error: object 'data_total_2' not found
```

:::::::::::::::::::::::::


::::::::::::::::::::::::::::::::::::::::::::::::::

## Pivoting Longer

The opposing situation could occur if we had been provided with the `data_tc`
tibble, but instead of treating each hour as an individual column, we instead wish 
to treat them as values of a variable instead.

In this situation, we are gathering all of these columns and turning them into a pair
of new variables. One variable will include the column names as values (`checkin_hour`), 
and the other will contain the values in each cell previously associated with the 
column names (`checkin_count`)!

`pivot_longer()` takes four principal arguments:

1. the data to be transformed
2. the names of the columns we use to fill the a new values variable (or to 
   drop), referred to as *cols*.
3. the *names\_to* column variable we wish to create from the *cols* provided.
4. the *values\_to* column variable we wish to create and fill with values
   associated with the *cols* provided.



``` r
data_tc_long <- data_tc %>%
  pivot_longer(cols = `6`:`20`,
               names_to = "checkin_hour",
               values_to = "checkin_count")
```

``` error
Error: object 'data_tc' not found
```

Below, we will look at the two tibbles and compare their structures:

``` r
head(data_tc)
```

``` error
Error: object 'data_tc' not found
```


``` r
head(data_tc_long)
```

``` error
Error: object 'data_tc_long' not found
```

As you can see, the hours and their corresponding counts for each location are 
now separated into individual rows! Each location appears multiple times -- once 
for every hour -- rather than appearing just once, as in a wide-table format.

:::::::::::::::::::::::::::::::::::::::  challenge

## Exercise

In the last exercise, you created the wide tibble, `data_total`. In this 
exercise, your goal is to reverse this transformation using `pivot_longer()`.

Create a tibble called `data_total_long` that has two columns: one for the 
hour, and one for the corresponding check-in count. During your transformation, 
remove the `total_checkins` column.

:::::::::::::::  solution

## Solution


``` r
data_total_long <- data_total %>%
  select(-total_checkins) %>%
  pivot_longer(
    cols = everything(),
    names_to = "hour",
    values_to = "checkin_count"
  )
```

``` error
Error: object 'data_total' not found
```

``` r
data_total_long
```

``` error
Error: object 'data_total_long' not found
```

:::::::::::::::::::::::::


::::::::::::::::::::::::::::::::::::::::::::::::::

## Other Useful tidyr Functions

Throughout this lesson, we used only a portion of the commands that **`tidyr`** offers 
for data transformation. Below, we will be briefly covering some other functions 
that may prove useful throughout your future analyses (you can refer to the **`tidyr`** 
cheat sheet linked at the beginning of the lesson for more in-depth explanations): 

1. `separate_longer_delim()` -- splits one column into many rows, based on a 
delimiter. 

``` r
tibble(location = "1", count = "1,2,3") %>%
  separate_longer_delim(count, delim = ",")
```

``` output
# A tibble: 3 × 2
  location count
  <chr>    <chr>
1 1        1    
2 1        2    
3 1        3    
```

2. `separate_wider_delim()` -- splits one column into multiple columns, based on 
a delimiter.

``` r
tibble(date = "01/01/2025") %>%
  separate_wider_delim(date, delim = "/", names = c("month", "day", "year"))
```

``` output
# A tibble: 1 × 3
  month day   year 
  <chr> <chr> <chr>
1 01    01    2025 
```

3. `unite()` -- combines multiple columns into one.

``` r
tibble(city = "Providence", state = "RI") %>%
  unite("location", city, state, sep = ", ")
```

``` output
# A tibble: 1 × 1
  location      
  <chr>         
1 Providence, RI
```

4. `replace_na()` -- fills in missing values (`NA`) with a value of choice. The 
replacement must be in a list.

``` r
tibble(count = c(1, NA, 3)) %>%
  replace_na(list(count = 2))
```

``` output
# A tibble: 3 × 1
  count
  <dbl>
1     1
2     2
3     3
```

5. `drop_na()` -- removes rows that contain missing values (`NA`).

``` r
tibble(count = c(1, NA, 3)) %>%
  drop_na()
```

``` output
# A tibble: 2 × 1
  count
  <dbl>
1     1
2     3
```

6. `fill()` -- fills in missing values (`NA`) with the value either above 
(.direction = "down") or below (.direction = "up") it.

``` r
#below
tibble(count = c(1, NA, 3)) %>%
  fill(count, .direction = "up")
```

``` output
# A tibble: 3 × 1
  count
  <dbl>
1     1
2     3
3     3
```

``` r
#above
tibble(count = c(1, NA, 3)) %>%
  fill(count, .direction = "down")
```

``` output
# A tibble: 3 × 1
  count
  <dbl>
1     1
2     1
3     3
```

7. `complete()` -- fills in all combinations of variables that could exist, but 
don't within the inputted data.

``` r
tibble(location = c("A", "B", "B"), hour = c(3, 1, 2)) %>%
  complete(location, hour)
```

``` output
# A tibble: 6 × 2
  location  hour
  <chr>    <dbl>
1 A            1
2 A            2
3 A            3
4 B            1
5 B            2
6 B            3
```


## Applying What We Learned to Clean Data

### Introduction to the Messy Dataset

The Messy Dataset is an example of a "messy" data set that tracks when 
people check-in to a voting location! In the context of the data set, labels 
("provisional", "assistance", and "provisional and assistance") are used to 
explain why check-in times may be longer than average. If a check-in does not 
have a label, assistance was not needed, and the check-in can be considered 
"normal". Within this data set, missing data is encoded as "NULL".

The following is a visual representation of the data set's columns:

| column\_name                                | description                                                                                                                      | 
| ------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| CheckIn_Duration_Provisional                | Includes check-ins that fall under the "Provisional" label.                                                                      | 
| CheckIn_Duration_Assistance                 | Includes check-ins that fall under the "Assistance" label.                                                                       | 
| CheckIn_Duration_Provisional_and_Assistance | Includes check-ins that fall under the "Provisional and Assistance" label.                                                       | 
| CheckIn_Duration_                           | Includes check-ins that did not fall under any label, or in other words, were normal.    

As mentioned above, missing information in data is encoded as "NULL". This 
requires us to specify `na = "NULL"` within the `read_csv()` function, allowing 
R to automatically convert all the "NULL" entries in the data set into `NA`.

Below, we will be reading in the Check-In Dataset using the additional line:

``` r
messy_data <- read_csv(here("data", "messy_data.csv"), na = "NULL")
```

``` error
Error in here("data", "messy_data.csv"): could not find function "here"
```


### Tidying the Data
Throughout this next section, we're going to be tidying/cleaning the Check-In 
Data step-by-step to ensure understanding throughout!

We'll start by looking at the data so we can understand what we're working with:

``` r
messy_data
```

``` error
Error: object 'messy_data' not found
```

At first glance, we can see this data set is wide, with each label tacked onto 
the end of the phrase "CheckIn_Duration_" and underscores replacing spaces. 
Additionally, there is no label after "CheckIn_Duration_", which indicates this 
is likely representative of the normal check-ins!

However, looking at how many missing values there are, it may be a better choice 
to turn the data into "long" data, instead of "wide" data, with a `duration` column, 
and a `label` column. Let's apply this pivot to a new tibble, named `clean_data`, 
below:

``` r
clean_data <- messy_data %>%
  pivot_longer(cols = everything(),
               names_to = "label",
               values_to = "duration")
```

``` error
Error: object 'messy_data' not found
```

``` r
head(clean_data)
```

``` error
Error: object 'clean_data' not found
```

Oh no! That's a lot of `NA` values. Taking a closer look at the original data, 
we can see the first value within the data set consists of a duration of `80` 
underneath the `"CheckIn_Duration_"` column. Looking at our in-progress, "clean" 
data set, we can see the labels that do not apply to this duration are listed as 
`NA`. 

Since the labels that have a duration of `NA` do not matter within our data set, 
we can drop them from the tibble completely:

``` r
clean_data <- clean_data %>%
  drop_na()
```

``` error
Error: object 'clean_data' not found
```

``` r
head(clean_data)
```

``` error
Error: object 'clean_data' not found
```

Now we're getting somewhere! Next, when we loaded in the data set, it was noted that
underscores replaced spaces throughout the data. As seen below, the next step is 
to revert that change:

``` r
clean_data <- clean_data %>%
  #including "all" in the str replace call ensures both underscores are replaced
  mutate(label = str_replace_all(label, "_", " "))
```

``` error
Error: object 'clean_data' not found
```

``` r
head(clean_data)
```

``` error
Error: object 'clean_data' not found
```

Our next step is removing the "CheckIn Duration " phrase from each label, which 
we will be completing below:

``` r
clean_data <- clean_data %>%
  mutate(label = str_remove(label, "CheckIn Duration "))
```

``` error
Error: object 'clean_data' not found
```

``` r
head(clean_data)
```

``` error
Error: object 'clean_data' not found
```

After removing the "CheckIn Duration " prefix, we can see that some of our labels 
are now an empty strings. However, as you may recall from our initial analysis 
of the data, empty labels indicate that the check-in was normal! So, our next 
step will be replacing the empty labels with "Normal" labels:

``` r
clean_data <- clean_data %>%
  mutate(label = ifelse(label == "", "Normal", label))
```

``` error
Error: object 'clean_data' not found
```

``` r
head(clean_data)
```

``` error
Error: object 'clean_data' not found
```

Now, our data is clean! In practice, all of these functions can (and should!) be 
chained together using pipes (and comments), as seen in the code block below:

``` r
clean_data_final <- messy_data %>%
  #pivot longer by label
  pivot_longer(cols = everything(),
               names_to = "label",
               values_to = "duration") %>%
  #remove rows with missing values
  drop_na() %>%
  #replace underscores with spaces
  mutate(label = str_replace_all(label, "_", " ")) %>%
  #remove "CheckIn Duration " from each label
  mutate(label = str_remove(label, "CheckIn Duration ")) %>%
  #replace empty labels with "Normal"
  mutate(label = ifelse(label == "", "Normal", label))
```

``` error
Error: object 'messy_data' not found
```

``` r
head(clean_data_final)
```

``` error
Error: object 'clean_data_final' not found
```

Since our data has been cleaned, we can now export it as `clean_data.csv` for 
use in future analysis. As you may recall from "Starting with Data", we will be 
using the `write_csv()` function, specifying that we want our csv to go into our 
data folder:

``` r
write_csv(clean_data_final, "data/clean_data.csv")
```

``` error
Error: object 'clean_data_final' not found
```

:::::::::::::::::::::::::::::::::::::::: keypoints

- Use the `tidyr` package to change the layout of tibbles.
- Use `pivot_wider()` to go from long to wide format.
- Use `pivot_longer()` to go from wide to long format.

::::::::::::::::::::::::::::::::::::::::::::::::::

