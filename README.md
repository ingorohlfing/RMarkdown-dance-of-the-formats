README
================
Ingo Rohlfing
2021-10-01

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
Dervieux and Riederer. Still, I wanted to briefly address this here for
my own purposes and because others might find this then useful too.

## From Rmd to an R script

When you have an Rmd file like [this one](Rmd_to_R_script.Rmd) and want
to turn it into an R script, you need `knitr::purl()`. For the same Rmd
file, you can get three R scripts with different levels of
documentation. For the argument `documentation = 0`, one gets [a plain R
script](just_script_0.R) out of the Rmd file.

``` r
knitr::purl(input = "Rmd_to_R_script.Rmd", 
            output = "just_script_0.R",
            documentation = 0) # give me just the code
```
