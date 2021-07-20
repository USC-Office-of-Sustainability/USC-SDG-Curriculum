
<!-- README.md is generated from README.Rmd. Please edit that file -->

# SDGmapR

<!-- badges: start -->
<!-- badges: end -->

The goal of `SDGmapR` is to provide an open-source foundation for the
systematic mapping to the United Nations Sustainable Development Goals
(SDGs). In this R package one can find publicly available SDG keyword
datasets in the `tidy` data format, the [2019 UN Official SDG color
scheme](https://www.un.org/sustainabledevelopment/wp-content/uploads/2019/01/SDG_Guidelines_AUG_2019_Final.pdf),
and several functions related to the mapping of text to particular sets
of keywords.

## Installation

You can install the development version from
[GitHub](https://github.com/) with:

``` r
# install.packages("devtools")
devtools::install_github("pwu97/SDGmapR")
```

## Publicly Available SDG Keywords

The table below lists publicly available SDG keywords that have been
published online. Some of the lists have weights associated with every
keyword, while some do not. For the purposes of the `SDGmapR` package,
we will assign an equal weight of 1.0 to every word if weights are not
given. Note that some datasets cover SDG 17, while some do not.

| Source                                                                                                          | Dataset              | CSV                                                                               | SDG17 |
|:----------------------------------------------------------------------------------------------------------------|:---------------------|:----------------------------------------------------------------------------------|:------|
| [Core Elsevier](https://data.mendeley.com/datasets/87txkw7khs/1)                                                | `elsevier_keywords`  |                                                                                   | No    |
| [Improved Elsevier Top 100](https://data.mendeley.com/datasets/9sxdykm8s4/2)                                    | `elsevier2_keywords` | [Link](https://github.com/pwu97/SDGmapR/blob/main/datasets/elsevier_keywords.csv) | No    |
| [SDSN](https://ap-unsdsn.org/regional-initiatives/universities-sdgs/)                                           | `sdsn_keywords`      | [Link](https://github.com/pwu97/SDGmapR/blob/main/datasets/sdsn_keywords.csv)     | Yes   |
| [University of Auckland](https://www.sdgmapping.auckland.ac.nz/)                                                | `auckland_keywords`  |                                                                                   | Yes   |
| [University of Toronto](https://data.utoronto.ca/sustainable-development-goals-sdg-report/sdg-report-appendix/) | `toronto_keywords`   |                                                                                   | Yes   |

## Example SDGMapR Usage

``` r
library(tidyverse)
library(SDGmapR)

# Load first 100 #tidytuesday tweets
tweets <- read_rds(url("https://github.com/rfordatascience/tidytuesday/blob/master/data/2019/2019-01-01/tidytuesday_tweets.rds?raw=true")) %>%
  head(1000)

# Map to SDG 1 using Improved Elsevier Top 100 Keywords
tweets_sdg1 <- tweets %>%
  mutate(sdg_1_weight = count_sdg_keywords(text, 1),
         sdg_1_words = tabulate_sdg_keywords(text, 1)) %>%
  arrange(desc(sdg_1_weight)) %>%
  select(text, sdg_1_weight, sdg_1_words)

# View SDG 1 mapping
tweets_sdg1 %>%
  select(text, sdg_1_weight, sdg_1_words)
#> # A tibble: 1,000 x 3
#>    text                                                 sdg_1_weight sdg_1_words
#>    <chr>                                                       <dbl> <named lis>
#>  1 "#TidyTuesday #rstats my latest tidy tuesday submis…        33.2  <chr [3]>  
#>  2 "#TidyTuesday - average income by state &amp; perce…        29.5  <chr [3]>  
#>  3 "#TidyTuesday changed state selection method! avg c…        26.6  <chr [2]>  
#>  4 "#tidytuesday week 29\nBusiness major gives highest…        15.2  <chr [2]>  
#>  5 "For this week's #TidyTuesday I decided to go to th…        14.2  <chr [2]>  
#>  6 "My first #TidyTuesday submission! Investigating co…        14.2  <chr [2]>  
#>  7 "#TidyTuesday Submission for this week showing dist…         9.34 <chr [2]>  
#>  8 "In honor of spurious correlations (also to practic…         8.73 <chr [2]>  
#>  9 "Do graduates from niche fields suffer from less un…         8.65 <chr [1]>  
#> 10 "1/2 For  #tidytuesday week 29, I was interested in…         8.65 <chr [1]>  
#> # … with 990 more rows

# View SDG 1 matched keywords
tweets_sdg1 %>%
  unnest(sdg_1_words)
#> # A tibble: 100 x 3
#>    text                                            sdg_1_weight sdg_1_words     
#>    <chr>                                                  <dbl> <chr>           
#>  1 "#TidyTuesday #rstats my latest tidy tuesday s…         33.2 "\\bpoverty\\b" 
#>  2 "#TidyTuesday #rstats my latest tidy tuesday s…         33.2 "\\bpoor\\b"    
#>  3 "#TidyTuesday #rstats my latest tidy tuesday s…         33.2 "\\bincome\\b"  
#>  4 "#TidyTuesday - average income by state &amp; …         29.5 "\\bpoverty\\b" 
#>  5 "#TidyTuesday - average income by state &amp; …         29.5 "\\bincome\\b"  
#>  6 "#TidyTuesday - average income by state &amp; …         29.5 "\\bpeople\\b"  
#>  7 "#TidyTuesday changed state selection method! …         26.6 "\\bpoverty\\b" 
#>  8 "#TidyTuesday changed state selection method! …         26.6 "\\bincome\\b"  
#>  9 "#tidytuesday week 29\nBusiness major gives hi…         15.2 "\\bunemploymen…
#> 10 "#tidytuesday week 29\nBusiness major gives hi…         15.2 "\\bemployment\…
#> # … with 90 more rows

# View Top SDG 1 Tweet
tweets_sdg1 %>%
  head(1) %>%
  select(text) %>%
  pull()
#> [1] "#TidyTuesday #rstats my latest tidy tuesday submission first my first violin plot looking at self employed % in a number of states. Second child poverty rate against income per cap. Stay tuned for blog post trying to understand why Puerto Rico is so Poor https://t.co/PIe7HNKcxP"

# Map to SDG 1 using Elsevier Core keywords
tweets %>%
  mutate(sdg_1_weight = count_sdg_keywords(text, 1, "elsevier")) %>%
  select(text, sdg_1_weight) %>%
  arrange(desc(sdg_1_weight))
#> # A tibble: 1,000 x 2
#>    text                                                             sdg_1_weight
#>    <chr>                                                                   <dbl>
#>  1 "#TidyTuesday changed state selection method! avg county income…            2
#>  2 "#TidyTuesday - average income by state &amp; percent of people…            2
#>  3 "#TidyTuesday #rstats my latest tidy tuesday submission first m…            2
#>  4 "For my #tidytuesday take 2, I tried to do a little bit of mode…            1
#>  5 "#TidyTuesday submission for last week (oops) showing the Gende…            1
#>  6 "#TidyTuesday I see many similar approaches. I first looked at …            1
#>  7 "#TidyTuesday Week 29 - College Majors &amp; Income\n\nVisualiz…            1
#>  8 "Income for recent college grads #TidyTuesday \n\ncode: https:/…            1
#>  9 "#TidyTuesday #r4ds @thomas_mock \nHow do genders compare as wo…            1
#> 10 "#TidyTuesday #r4ds #rstats #take2 @thomas_mock Examination of …            1
#> # … with 990 more rows

# Map to all SDGs using Elsevier Top 100
# tweets %>%
#   mutate(sdg_total_weight = count_sdgs_keywords(text)) %>%
#   select(text, sdg_total_weight)
```

<!-- What is special about using `README.Rmd` instead of just `README.md`? You can include R chunks like so: -->
<!-- ```{r cars} -->
<!-- summary(cars) -->
<!-- ``` -->
<!-- You'll still need to render `README.Rmd` regularly, to keep `README.md` up-to-date. `devtools::build_readme()` is handy for this. You could also use GitHub Actions to re-render `README.Rmd` every time you push. An example workflow can be found here: <https://github.com/r-lib/actions/tree/master/examples>. -->
<!-- You can also embed plots, for example: -->
<!-- ```{r pressure, echo = FALSE} -->
<!-- plot(pressure) -->
<!-- ``` -->
<!-- In that case, don't forget to commit and push the resulting figure files, so they display on GitHub and CRAN. -->
