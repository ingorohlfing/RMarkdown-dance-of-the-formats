README
================
Ingo Rohlfing
2021-10-06

## Short explanation of motivation

R Markdown reports are a great tool for turning dynamic code and text
into a full-fledged report as PDF, Word file or another format. Everyone
who is familiar with R Markdown reports most likely knows Rmd scripts
that visually separate text and code output from chunks containing code.

There are two related facts about R Markdown that I only learned slowly,
over time:

1.  One can transform Rmd files into R scripts and vice versa.
2.  One can render R scripts into R Markdown reports.

As is probably true about all aspects of R Markdown, this is well
documented in the awesome [R Markdown
Cookbook](https://bookdown.org/yihui/rmarkdown-cookbook/) by Xie,
Dervieux and Riederer. Still, I wanted to post this here because I

-   wanted to go through the commands once myself and convert actual
    scripts;
-   believe these very useful features of R Markdown are not as widely
    known as they probably should.

## From an Rmd file to an R script

When you have an Rmd file like [this one](Rmd_to_R_script.Rmd) and want
to turn it into an R script, you need `knitr::purl()`. You can convert
an Rmd file into an R script in *three different ways*, depending on how
much information you want to preserve. This can be specified with the
`documentation` argument where:

-   `0`: plain code;
-   `1`: plain code + chunk settings;
-   `2`: the complete Rmd file, just in a different format.

### Rmd =&gt; plain code

This command reduces [the Rmd file](Rmd_to_R_script.Rmd) to [an R script
only with code and annotations](just_code_0.R). Since the identifying
information is part of the Rmd header, this information is now gone, as
is all text.

``` r
knitr::purl(input = "Rmd_to_R_script.Rmd", 
            output = "just_code_0.R",
            documentation = 0) # just the code
```

For illustration, this part of the Rmd file…

    Load some toy data.
    ```{r, demo-chunk, echo = T}
    # load some data
    data(mtcars)
    ```

becomes this in the script.

    # load some data
    data(mtcars)

### Rmd =&gt; code + chunk info

This command reduces [the Rmd file](Rmd_to_R_script.Rmd) to [an R script
only with code, annotations and chunk info](code_chunkinfo_1.R).

``` r
knitr::purl(input = "Rmd_to_R_script.Rmd", 
            output = "code_chunkinfo_1.R",
            documentation = 1) # code + chunk info
```

For illustration, this part of the Rmd file…

    Load some toy data.
    ```{r, demo-chunk, echo = T}
    # load some data
    data(mtcars)
    ```

becomes this in the script.

    ## ----demo-chunk, echo = T----------------------------------------------------
    # load some data
    data(mtcars)

When you turn the two R scripts into reports, they almost look the same
because the content is the same (compare the html reports for [plain
code](just_code_0.pdf) to [chunk info + code](code_chunkinfo_1.pdf). The
only reason I see for converting with `documentation = 1` is that you
can directly navigate to a selected chunk, as well as easily moving back
and forth between them.

### Rmd =&gt; identical R script

This command converts [the Rmd file](Rmd_to_R_script.Rmd) to [an R
script that has exactly the same content](complete_script_2.R).

``` r
knitr::purl(input = "Rmd_to_R_script.Rmd", 
            output = "complete_script_2.R",
            documentation = 2) # full conversion
```

For illustration, this part of the Rmd file…

    Load some toy data.
    ```{r, demo-chunk, echo = T}
    # load some data
    data(mtcars)
    ```

becomes this in the script.

    #' Load some toy data.
    ## ----demo-chunk, echo = T----------------------------------------------------
    # load some data
    data(mtcars)

The formatting is different in the script, but the exact same
information is preserved. You will see the knitted PDFs are the same
when you prepare a PDF report for [the Rmd file](Rmd_to_PDF.pdf) and the
[converted R script](complete_script_2.pdf). (I haven’t edited the R
script after conversion, so they also have the same title.)

## From an R script to an Rmd file

When you have an R script like [this one](R_script_to_Rmd.R) and want to
turn it into an Rmd file, you need `knitr::spin()`. This is the command
doing the job.

``` r
knitr::spin(hair = "R_script_to_Rmd.R", 
            format = "Rmd", 
            knit = FALSE) # we don't an html and md file for the R script now
```

For illustration, this part of the R script…

    #' Load some toy data.
    #+ demo-chunk, echo = TRUE
    # load some data
    data(mtcars)

becomes this in the Rmd file.

    Load some toy data.

    ```{r demo-chunk, echo = TRUE}
    # load some data
    data(mtcars)
    ```

When you look at the [generated Rmd file](R_script_to_Rmd.Rmd), it looks
exactly like the Rmd file `Rmd_to_R_script.R` that was previously
converted to R scripts (except for the different title in the file). You
only need to follow a small number of formatting rules when you want a
nice Rmd file from `knitr::spin()`, which you can see in the [R
script](R_script_to_Rmd.R) used as input (this is information about
formatting in the [R Markdown
cookbook](https://bookdown.org/yihui/rmarkdown-cookbook/spin.html)).

## Turn Rmd and R scripts into a report

Rmd files are known for allowing one to simply create reports (in html
format, PDF etc.) by hitting the “Knit” button in RStudio. The command
line equivalent is `rmarkdown::render()`, which, at a minimum, only
needs the Rmd file as input. For example, you can create the [PDF
RMD\_to\_pdf](Rmd_to_PDF.pdf) with the following line. PDF as an output
format is specified in the Rmd header, so I omit the `output_format`
argument here.

``` r
rmarkdown::render(input = "Rmd_to_R_script.Rmd", 
                  output_file = "Rmd_to_PDF")
```

The good thing now is that `rmarkdown::render()` works equally well with
*any* R script regardless of how it has been formatted. Of course, it
makes more sense to render a file such as
[R\_script\_to\_Rmd.R](R_script_to_Rmd.R) because it produces a more
attractive PDF. This [PDF](R_script_to_PDF.pdf) has been produced with
this command.

``` r
rmarkdown::render(input = "R_script_to_RMD.R", 
                  output_file = "R_script_to_PDF") # PDF is specified in header
```

Another good feature of `rmarkdown::render()` is that you can create a
report without having the file that is to be rendered open in RStudio.

### So, why is all of this of interest?

I think it is good to know about the two-way street between Rmd files
and R scripts and the all-purpose use of `rmarkdown::render()`. The main
advantage is that one does not have to think much about what format to
start with in an analysis. Rmd files and R scripts have both advantages
and disadvantages and one format is not clearly superior to the other.  
In some of my courses, for example, some course participants found it
easier to work with Rmd files, while others could better work with R
scripts. The good news about `purl()`, `spin()` and `render()` is that
everyone can work with the preferred format, produce the same beautiful
reports and easily convert Rmd files to R scripts and vice versa, if
necessary.

#### Packages used (on the surface)

Hadley Wickham, Jim Hester and Winston Chang (2021). *devtools: Tools to
Make Developing R Packages Easier.* R package version 2.4.2.
<https://CRAN.R-project.org/package=devtools>

JJ Allaire and Yihui Xie and Jonathan McPherson and Javier Luraschi and
Kevin Ushey and Aron Atkins and Hadley Wickham and Joe Cheng and Winston
Chang and Richard Iannone (2021). *rmarkdown: Dynamic Documents for R.*
R package version 2.11. <https://rmarkdown.rstudio.com>.

Yihui Xie (2021). *knitr: A General-Purpose Package for Dynamic Report
Generation in R.* R package version 1.36.

Yihui Xie (2015). *Dynamic Documents with R and knitr.* (2nd ed.)
Chapman and Hall/CRC.

Yihui Xie (2014). *knitr: A Comprehensive Tool for Reproducible Research
in R.* In Victoria Stodden, Friedrich Leisch and Roger D. Peng, editors,
*Implementing Reproducible Computational Research*. Chapman and
Hall/CRC.

Yihui Xie and J.J. Allaire and Garrett Grolemund (2018). *R Markdown:
The Definitive Guide.* Chapman and Hall/CRC.
<https://bookdown.org/yihui/rmarkdown>.

Yihui Xie and Christophe Dervieux and Emily Riederer (2020). *R Markdown
Cookbook.* Chapman and Hall/CRC.
<https://bookdown.org/yihui/rmarkdown-cookbook>.

#### Session parameters

    ## - Session info ---------------------------------------------------------------
    ##  setting  value                       
    ##  version  R version 4.1.1 (2021-08-10)
    ##  os       Windows 10 x64              
    ##  system   x86_64, mingw32             
    ##  ui       RTerm                       
    ##  language (EN)                        
    ##  collate  German_Germany.1252         
    ##  ctype    German_Germany.1252         
    ##  tz       Europe/Berlin               
    ##  date     2021-10-06                  
    ## 
    ## - Packages -------------------------------------------------------------------
    ##  package     * version date       lib source        
    ##  cachem        1.0.6   2021-08-19 [1] CRAN (R 4.1.1)
    ##  callr         3.7.0   2021-04-20 [1] CRAN (R 4.1.1)
    ##  cli           3.0.1   2021-07-17 [1] CRAN (R 4.1.1)
    ##  crayon        1.4.1   2021-02-08 [1] CRAN (R 4.1.1)
    ##  desc          1.3.0   2021-03-05 [1] CRAN (R 4.1.1)
    ##  devtools      2.4.2   2021-06-07 [1] CRAN (R 4.1.1)
    ##  digest        0.6.28  2021-09-23 [1] CRAN (R 4.1.1)
    ##  ellipsis      0.3.2   2021-04-29 [1] CRAN (R 4.1.1)
    ##  evaluate      0.14    2019-05-28 [1] CRAN (R 4.1.1)
    ##  fastmap       1.1.0   2021-01-25 [1] CRAN (R 4.1.1)
    ##  fs            1.5.0   2020-07-31 [1] CRAN (R 4.1.1)
    ##  glue          1.4.2   2020-08-27 [1] CRAN (R 4.1.1)
    ##  htmltools     0.5.2   2021-08-25 [1] CRAN (R 4.1.1)
    ##  knitr         1.34    2021-09-09 [1] CRAN (R 4.1.1)
    ##  lifecycle     1.0.1   2021-09-24 [1] CRAN (R 4.1.1)
    ##  magrittr      2.0.1   2020-11-17 [1] CRAN (R 4.1.1)
    ##  memoise       2.0.0   2021-01-26 [1] CRAN (R 4.1.1)
    ##  pkgbuild      1.2.0   2020-12-15 [1] CRAN (R 4.1.1)
    ##  pkgload       1.2.2   2021-09-11 [1] CRAN (R 4.1.1)
    ##  prettyunits   1.1.1   2020-01-24 [1] CRAN (R 4.1.1)
    ##  processx      3.5.2   2021-04-30 [1] CRAN (R 4.1.1)
    ##  ps            1.6.0   2021-02-28 [1] CRAN (R 4.1.1)
    ##  purrr         0.3.4   2020-04-17 [1] CRAN (R 4.1.1)
    ##  R6            2.5.1   2021-08-19 [1] CRAN (R 4.1.1)
    ##  remotes       2.4.0   2021-06-02 [1] CRAN (R 4.1.1)
    ##  rlang         0.4.11  2021-04-30 [1] CRAN (R 4.1.1)
    ##  rmarkdown     2.11    2021-09-14 [1] CRAN (R 4.1.1)
    ##  rprojroot     2.0.2   2020-11-15 [1] CRAN (R 4.1.1)
    ##  rstudioapi    0.13    2020-11-12 [1] CRAN (R 4.1.1)
    ##  sessioninfo   1.1.1   2018-11-05 [1] CRAN (R 4.1.1)
    ##  stringi       1.7.4   2021-08-25 [1] CRAN (R 4.1.1)
    ##  stringr       1.4.0   2019-02-10 [1] CRAN (R 4.1.1)
    ##  testthat      3.0.4   2021-07-01 [1] CRAN (R 4.1.1)
    ##  usethis       2.0.1   2021-02-10 [1] CRAN (R 4.1.1)
    ##  withr         2.4.2   2021-04-18 [1] CRAN (R 4.1.1)
    ##  xfun          0.26    2021-09-14 [1] CRAN (R 4.1.1)
    ##  yaml          2.2.1   2020-02-01 [1] CRAN (R 4.1.1)
    ## 
    ## [1] C:/Users/Ingo R/Documents/R/win-library/4.1
    ## [2] C:/Program Files/R/R-4.1.1/library
