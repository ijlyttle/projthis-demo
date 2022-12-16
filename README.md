projthis Demonstration
================
2022-12-16 08:17:15 UTC

<!-- README.md is generated from README.Rmd. Please edit that file -->
<!-- badges: start -->
<!-- badges: end -->

The goal of this project is to show how you can use the [projthis]()
package, along with [usethis](), to build a project workflow that
manages package dependencies.

## Demo

This demo is run automatically by GitHub Actions, every day at 02:30
UTC, as well as whenever there is a change to `README.Rmd`.

For this demo, we will select a letter at random, then get a data frame
of the top ten most-downloaded packages (from the day’s top 100) that
contain this letter in its name.

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

    ## [1] "b"

``` r
cran_top_downloads(when = "last-day", count = 100) %>%
  filter(str_detect(package, regex(letter, ignore_case = TRUE))) %>%
  head(10)
```

    ##    rank      package count       from         to
    ## 1    12   data.table 47228 2022-12-14 2022-12-14
    ## 2    20       tibble 40032 2022-12-14 2022-12-14
    ## 3    22         tzdb 38905 2022-12-14 2022-12-14
    ## 4    47    lubridate 27115 2022-12-14 2022-12-14
    ## 5    50      isoband 26347 2022-12-14 2022-12-14
    ## 6    53 RColorBrewer 25440 2022-12-14 2022-12-14
    ## 7    58       gtable 24580 2022-12-14 2022-12-14
    ## 8    60        broom 24207 2022-12-14 2022-12-14
    ## 9    61        bslib 24002 2022-12-14 2022-12-14
    ## 10   68          bit 22435 2022-12-14 2022-12-14

## Steps

These are the steps I took to build this project and automate the GitHub
Actions workflow.

``` r
library("projthis")
library("usethis")
```

### Initial structure

1.  `proj_create("../projthis-demo")`
2.  `use_mit_license()`
3.  `use_git()`
4.  `use_github()`
5.  `use_readme_rmd()`
6.  edit `README.Rmd` and `DESCRIPTION`

## Create demo

1.  Add **Demo** section to `README.Rmd`
2.  `proj_update_deps()` to add packages to `DESCRIPTION`

## Add GitHub Action

We want to automate the build on a schedule.

1.  `proj_use_github_action()`
2.  Modify `.github/workflows/project-run.yaml` to customize trigger,
    what is built, and what is deployed.

This is a modification of the [pkgdown Actions
file](https://github.com/r-lib/actions/blob/v2/examples/pkgdown.yaml),
where the build is triggered by either:

- a change to `README.Rmd`
- the clock changing to 08:00 UTC

``` yaml
on:

  # # runs whenever you push a specific branch
  # push:
  #   branches: [main, master]

  # # runs on pull requests to a specific branch
  # pull_request:
  #   branches: [main, master]
  
  # runs when we push README.Rmd
  push:
    paths:
      - README.Rmd

  # runs on a schedule using UTC - see https://en.wikipedia.org/wiki/Cron
  schedule:
    - cron:  '00 02 * * *' # 08:00 UTC, every day
```

I also modified a bit at the end of the Actions file, to specify the
rendering and deployment. In this case, we are rendering the
`README.Rmd` file, committing `README.md` then pushing back to the
original branch:

``` yaml
      # rename and adapt this step to build your project
      - name: Render
        # if you are running only R commands, this can be a convenient syntax
        run: |
          rmarkdown::render("README.Rmd")
        shell: Rscript {0}

      # rename and adapt this step to deploy your project
      - name: Commit
        # if you need to combine R commands with shell commands, try this syntax
        #
        # caution to stay away from syntax like `git add -A` as you might be committing
        #  files that you would prefer not to commit:
        #  https://twitter.com/JennyBryan/status/1319320033063923712
        run: |
          git config --local user.email "actions@github.com"
          git config --local user.name "GitHub Actions"
          git add "README.md"
          git commit -m 'automated run' || echo "No changes to commit"
          git push origin || echo "No changes to commit"
```

It is likely your build and deploy processes will be more complicated,
but this gives you a sense of how to get things working. In Actions, as
with any CI system, there is an element of “try stuff until it works”,
which can be frustrating.
