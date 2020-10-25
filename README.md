
<!-- README.md is generated from README.Rmd. Please edit that file -->

# projthis-demo

<!-- badges: start -->

<!-- badges: end -->

The goal of this project is to show how you can use the [projthis]()
package, along with [usethis](), to build a project workflow that
manages package dependencies.

## 

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
