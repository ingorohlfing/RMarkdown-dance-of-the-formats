README
================
Ingo Rohlfing
2021-10-02

## Short explainer on motivation

R Markdown reports are a great tool for turning dynamic code and text
into a full-fledged report as PDF, Word file or another format. When one
is familiar with R Markdown reports, then one most likely knows Rmd
scripts visually separating text from chunks with code in them.

What I only learned over time, slowly, about R Markdown, are two related
things:

1.  One can transform Rmd files into R scripts and vice versa.
2.  One can render R scripts into R Markdown reports.

As probably everything with R Markdown, this is well documented in the
awesome [R Markdown
Cookbook](https://bookdown.org/yihui/rmarkdown-cookbook/) by Xie,
Dervieux and Riederer. Still, I wanted to post this here because I

-   wanted to go through the commands once myself and convert actual
    scripts;
-   believe these very useful features of R Markdown are not as widely
    known as they probably should.

## From Rmd to an R script

When you have an Rmd file like [this one](Rmd_to_R_script.Rmd) and want
to turn it into an R script, you need `knitr::purl()`. You can convert
an Rmd file into an R script in *three different ways*, depending on how
much info you want to preserve. This can be specified with the
`documentation` argument where

-   `0`: plain code;
-   `1`: plain code + chunk settings;
-   `2`: the complete Rmd file, just in a different format.

**Rmd =&gt; plain code**  
This command reduces [the Rmd file](Rmd_to_R_script.Rmd) to [an R script
only with code and annotations](just_code_0.R). Since the identifying
information is part of the Rmd header, this information is gone now as
is all tex.

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

becomes simply this in the script.

    # load some data
    data(mtcars)

**Rmd to code + chunk info** This command reduces [the Rmd
file](Rmd_to_R_script.Rmd) to [an R script only with code, annotations
nd chunk info](code_chunkinfo_1.R).

``` r
knitr::purl(input = "Rmd_to_R_script.Rmd", 
            output = "code_chunkinfo_1.R",
            documentation = 1) # just the code
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

When you then knit the two R scripts, they almost look the same because
the content is the same (compare the html reports for
[0](just_code_0.html) to [1](code_chunkinfo_1.html). The only reason I
see for converting with `documentation = 1` is that you can directly
navigate to a selected chunks and easily move back and forth between
them.
