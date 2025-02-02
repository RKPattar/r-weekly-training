
<!-- Please edit README.Rmd - not README.md -->

# Week 02: Data Wrangling

This week we’ll be building on what was covered last week, using `tidyr`
and `dplyr` to perform some more serious data wrangling, this time using
some real-world educational data taken from
[Ofsted](https://www.gov.uk/government/statistics/further-education-and-skills-inspections-and-outcomes-as-at-31-august-2021)
and the [Apprenticeships Statistical First Release
(SFR)](https://explore-education-statistics.service.gov.uk/find-statistics/apprenticeships-and-traineeships/2020-21#dataDownloads-1).
All data used in these examples is the most recent available data as of
December 2021.

# Resources

- [R for Data Science](https://r4ds.hadley.nz/data-transform.html):
  - See chapter 4 for adding columns with `mutate()`, removing rows with
    `filter()` and aggregating with `group_by()` and `summarise()`
  - See chapter 6 for ‘pivoting’, dealing with missing values and
    columns which have multiple observations per cell
  - See chapter 20 for joins (this chapter is especially relevant!)
  - See chapter 18 for working with dates and times

# Exercises

<div>

> **Submitting Solutions**
>
> Read exercises 3-10 of [Week
> 1](../week-01-version-control#03-check-the-state-of-the-repo) for a
> recap of how to get feedback on your solutions using GitHub

</div>

## 1. Setup/Data import

<div>

> **Difficulty**
>
> Not too bad (hopefully)

</div>

The data for these exercises has been saved in this repo as three
tables: [`course_details.csv`](course_details.csv),
[`ofsted.csv`](ofsted.csv), and [`starts.csv`](starts.csv). Load these
into R using the following steps:

1.  Manually download the files from the repo

2.  Save these as files in your local R project

3.  Read the files into your R session using `readr::read_csv()`. It’s
    probably a good idea to save these as objects named
    `course_details`, `starts`, `ofsted` and `ofsted_scores`

    Your project folder should end up looking something like this:

    ![example project
    structure](project-structure-example.jpg "example project structure")

    ..and your script should look something like this:

    ``` r
    # == 01. Load packages =========================================================
    library(tidyverse)

    # == 02. Load data =============================================================
    course_details <- read_csv("course_details.csv")
    starts         <- read_csv("starts.csv")
    ofsted         <- read_csv("ofsted.csv")
    ofsted_scores  <- read_csv("ofsted_scores.csv")
    ```

## 2. Basic wrangling

<div>

> **Difficulty**
>
> Not too bad (hopefully)

</div>

Answer the following questions using the **`ofsted`** dataset.

1.  Using `count()`, verify that each provider has only one recorded
    inspection in the data (otherwise we could get skewed results later
    on).

2.  Each provider in the `ofsted` data has a unique UK Provider
    Reference Number (UKPRN) associated with them. Using this
    information, check whether the `provider_name` column can also be
    used to uniquely identify providers in the data.

3.  What proportion of providers are ‘Independent specialist colleges’?

4.  Use `left_join()` to combine `ofsted` and `ofsted_scores`. What
    proportion of providers have ineffective safeguarding according to
    the data? Hint: look at `ofsted$inspection_number` and
    `ofsted_scores$inspection_id`

5.  {lubridate} is a package for working with dates. Using functions
    from this package, convert the `inspection_date` column to `date`
    format, and then create a new column giving the day of the week on
    which the inspection occurred. Using this column, find out if there
    is any pattern in how Ofsted chooses the day of the week to run
    inspections. Does this change based on inspection type?

## 3. More complex wrangling

<div>

> **Difficulty**
>
> Tricky

</div>

1.  Using `starts`, `ofsted` and `ofsted_scores`, work out the total
    number of apprenticeship starts associated with each ofsted score
    for overall effectiveness.

2.  Similarly to 3.1, work out the total number of apprenticeships
    starts associated with each ofsted score for apprenticeships. Using
    this information with your results from 3.1, see if you can draw any
    conclusions about how providers perform in apprenticeships compared
    with how they perform overall.

3.  Using the `ofsted` and `ofsted_scores` datasets, create a dataset
    which shows the distribution of scores **for apprenticeships** for
    each provider group.

4.  Create a lookup table using your answer to question 3.1. Your output
    should look something like this:

    | provider_group                                                |      good | requires_improvement | outstanding | inadequate |
    |:--------------------------------------------------------------|----------:|---------------------:|------------:|-----------:|
    | Independent learning providers (including employer providers) | 0.7109375 |            0.1822917 |   0.0677083 |  0.0390625 |
    | Adult community education providers                           | 0.8461538 |            0.1025641 |   0.0512821 |         NA |
    | Colleges                                                      | 0.5474453 |            0.3284672 |   0.1094891 |  0.0145985 |
    | Higher education institutions                                 | 0.8333333 |            0.1250000 |   0.0416667 |         NA |
    | Independent specialist colleges                               | 0.3333333 |            0.3333333 |          NA |  0.3333333 |

    Hint: `janitor::clean_names()` may come in handy here.

## 4. A recursive problem

<div>

> **Difficulty**
>
> Challenging

</div>

**Note**: These questions are intended to give you a real run for your
money. If you get frustrated and aren’t having fun, feel free to abandon
this question at any point - this is more tricky than most stuff you’ll
come across in real life! If you do want to give them a try it’s at
least recommended that you collaborate with someone else. Remember to
‘make your code beautiful’ before you submit :)

1.  A provider may be at risk of closing if their overall effectiveness
    is rated as ‘inadequate’. Create a dataset which gives the
    percentage of starts which are at risk for each combination of
    `delivery_region` and `std_fwk_flag`. Call the additional column
    `risk_factor`.

    (Hint: first create a dataset which gives a simple `TRUE`/`FALSE`
    for each provider to indicate whether they are at risk of closing.)

2.  Now, say that a provider is at risk of closing if they are rated
    inadequate in any category, or if they have ineffective
    safeguarding. Modify your solution to 4.1 to include a column
    `risk_category` which gives the inspection category/categories which
    the starts are at risk for.

3.  Modify your answer to 4.2 to include several more ‘breakdown
    columns’. These should be the following:

    ``` r
    breakdowns <- c(
      "risk_category", "delivery_region", "std_fwk_flag", "provider_type", 
      "provider_group", "route", "apps_level"
    )
    ```

4.  Your dataset from 4.3 will include *implicit missing values*. Use
    `tidyr::complete()` to make these explicit. (Hint: read [R for Data
    Science chapter 19](https://r4ds.hadley.nz/missing-values.html) if
    you’re not sure what this is all about)
