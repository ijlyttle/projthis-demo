projthis Demonstration
================
2022-03-15 02:53:54 UTC

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

    ## [1] "n"

``` r
cran_top_downloads(when = "last-day", count = 100) %>%
  filter(str_detect(package, regex(letter, ignore_case = TRUE))) %>%
  head(10)
```

    ##    rank     package count       from         to
    ## 1     3 textshaping 47626 2022-03-13 2022-03-13
    ## 2     4       rlang 41786 2022-03-13 2022-03-13
    ## 3    12     pkgdown 31589 2022-03-13 2022-03-13
    ## 4    16    jsonlite 26994 2022-03-13 2022-03-13
    ## 5    26      crayon 21928 2022-03-13 2022-03-13
    ## 6    27     stringr 20627 2022-03-13 2022-03-13
    ## 7    30     stringi 19602 2022-03-13 2022-03-13
    ## 8    31       knitr 19371 2022-03-13 2022-03-13
    ## 9    32        xfun 19008 2022-03-13 2022-03-13
    ## 10   35     openssl 17839 2022-03-13 2022-03-13

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

I modified a bit at the start of the [Actions
file](https://github.com/ijlyttle/projthis-demo/blob/master/.github/workflows/project-run.yaml)
so that the build would be triggered by either:

  - a change to `README.Rmd`
  - the clock changing to 02:30 UTC

<!-- end list -->

``` yaml
on:

  # # runs whenever you push a specific branch
  # push:
  #   branches:
  #     - main
  #     - master

  # # runs on pull requests to a specific branch
  # pull_request:
  #   branches:
  #     - main
  #     - master

  # runs when we push README.Rmd
  push:
    paths:
      - README.Rmd

  # runs on a schedule using UTC - see https://en.wikipedia.org/wiki/Cron
  schedule:
    - cron:  '30 02 * * *' # 02:30 UTC, every day
```

I also modified a bit at the end of the Actions file, to specify the
rendering and deployment. In this case, we are rendering the
`README.Rmd` file, committing `README.md` then pushing back to the
original branch:

``` yaml
      # rename and adapt this step to build your project
      - name: Render README.Rmd
        # if you are running only R commands, this can be a convenient syntax
        run: |
          rmarkdown::render("README.Rmd")
        shell: Rscript {0}

      # rename and adapt this step to deploy your project
      - name: Commit README.md, push
        # if you need to combine R commands with shell commands, try this syntax
        #
        # caution to stay away from syntax like `git add -A` as you might be committing
        #  files that you would prefer not to commit:
        #  https://twitter.com/JennyBryan/status/1319320033063923712
        run: |
          git config --local user.email "actions@github.com"
          git config --local user.name "GitHub Actions"
          git add 'README.md'
          git commit -m 'automated run' || echo "No changes to commit"
          git push origin || echo "No changes to commit"
```

It is likely your build and deploy processes will be more complicated,
but this gives you a sense of how to get things working. In Actions, as
with any CI system, there is an element of “try stuff until it works”,
which can be frustrating.
