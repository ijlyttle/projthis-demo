projthis Demonstration
================
2020-10-25 21:59:24

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

    ## [1] "w"

``` r
cran_top_downloads(when = "last-day", count = 100) %>%
  filter(str_detect(package, regex(letter, ignore_case = TRUE))) %>%
  head(10)
```

    ##   rank         package count       from         to
    ## 1    2          aws.s3 87487 2020-10-24 2020-10-24
    ## 2    3 aws.ec2metadata 86200 2020-10-24 2020-10-24
    ## 3   21         pkgdown 22681 2020-10-24 2020-10-24
    ## 4   34        markdown 18364 2020-10-24 2020-10-24
    ## 5   36       rmarkdown 18046 2020-10-24 2020-10-24
    ## 6   47           withr 16180 2020-10-24 2020-10-24
    ## 7   85    RColorBrewer 11156 2020-10-24 2020-10-24

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
