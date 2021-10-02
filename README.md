README
================
Ingo Rohlfing
2021-10-03

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

### Rmd =&gt; plain code

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

### Rmd =&gt; code + chunk info

This command reduces [the Rmd file](Rmd_to_R_script.Rmd) to [an R script
only with code, annotations nd chunk info](code_chunkinfo_1.R).

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

When you then knit the two R scripts, they almost look the same because
the content is the same (compare the html reports for [plain
code](just_code_0.pdf) to [chunk info + code](code_chunkinfo_1.pdf). The
only reason I see for converting with `documentation = 1` is that you
can directly navigate to a selected chunks and easily move back and
forth between them.

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

The formatting is different in the script, but the exact same info is
preserved. You see knitted PDFs are the same when you prepare a PDF
report for [the Rmd file](Rmd_to_PDF.pdf) and the [converted R
script](complete_script_2.pdf). (I haven’t edited the R script after
conversion, so they also have the same title.)

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
exactly like the Rmd file `Rmd_to_R_script.R` that was converted to R
scripts before (except for the different title in the file). You need to
follow some formatting rules when you want a nice Rmd file from
`knitr::spin()`, which you can see in the [R script](R_script_to_Rmd.R)
used as input ([documentation in R Markdown
cookbook](https://bookdown.org/yihui/rmarkdown-cookbook/spin.html)).

## Turn Rmd and R scripts into a report

Rmd files are known for allowing one to easily create reports (in html
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
[R\_script\_to\_Rmd.R](R_script_to_Rmd.R) because it produces a nicer
PDF.

### So, why is all this interesting?

I think it is good to know about the two-way street between Rmd files
and R scripts and the all-purpose use of `rmarkdown::render()`. The main
advantage is that one does not have to think much about what format to
start with in an analysis. Rmd and R scripts both have their advantages
and disadvantages, so one format is not unequivocally superior to the
other.  
In some of my courses, for example, some course participants found it
easier to work with Rmd files, while others could better work with R
scripts. The good news about `purl()`, `spin()` and `render()` is that
everyone can work with the preferred format, produce the same beautiful
reports and easily convert Rmd to R scripts and vice versa, if
necessary.
