projthis Demonstration
================
2020-10-25 22:29:05

<!-- README.md is generated from README.Rmd. Please edit that file -->

<!-- badges: start -->

<!-- badges: end -->

The goal of this project is to show how you can use the [projthis]()
package, along with [usethis](), to build a project workflow that
manages package dependencies.

## Demo

For this demo, we will select a letter at random, then get a data frame
of the top ten most-downloaded packages (from the day’s top 100) that
contain this letter.

``` r
library("cranlogs")
library("dplyr")
library("stringr")
library("conflicted")

conflict_prefer("filter", "dplyr")
```

``` r
letter <- sample(letters, 1)
letter
```

    ## [1] "r"

``` r
cran_top_downloads(when = "last-day", count = 100) %>%
  filter(str_detect(package, regex(letter, ignore_case = TRUE))) %>%
  head(10)
```

    ##    rank   package  count       from         to
    ## 1     1  magrittr 103420 2020-10-24 2020-10-24
    ## 2     4 rsconnect  84364 2020-10-24 2020-10-24
    ## 3    10     rlang  39474 2020-10-24 2020-10-24
    ## 4    11     vctrs  33425 2020-10-24 2020-10-24
    ## 5    12     dplyr  32797 2020-10-24 2020-10-24
    ## 6    20     rgeos  22977 2020-10-24 2020-10-24
    ## 7    22    vdiffr  22350 2020-10-24 2020-10-24
    ## 8    23    gargle  22281 2020-10-24 2020-10-24
    ## 9    24    gmailr  21922 2020-10-24 2020-10-24
    ## 10   27    pillar  20494 2020-10-24 2020-10-24

## Steps

These are the steps I took to build this project and automate the GitHub
Actions workflow.

``` r
library("projthis")
library("usethis")
```

### Initial structure

1.  `proj_create("../projthis-demo")`
2.  edit `Title` in `DESCRIPTION`
3.  `use_mit_license()`
4.  `use_git()`
5.  `use_github()`
6.  `use_readme_rmd()`
7.  edit `README.Rmd` and `DESCRIPTION`

## Create demo

1.  add **Demo** section to `README.Rmd`
2.  `proj_update_deps()` to add packages to `DESCRIPTION`

## Add GitHub Action

We want to automate the build on a schedule.

1.  `proj_use_github_action()`
2.  Modify action.
