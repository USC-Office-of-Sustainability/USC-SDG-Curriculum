# USC SDGmap R Package

Peter Wu at Carnegie Mellon was the inspiration of this project, and his
original R package can be found on
[Github](https://github.com/pwu97/SDGmapR). At USC, Brian Tinsley and
Julie Hopper in the Office of Sustainability have been working to
develop this package further and raise sustainability awareness in
higher education.

The goal of `SDGmapR` is to provide an open-source foundation for the
systematic mapping to the United Nations Sustainable Development Goals
(SDGs). In this R package one can find publicly available [SDG keyword
datasets](https://github.com/USC-Office-of-Sustainability/USC-SDGmap/tree/main/Keyword%20Lists)
in the `tidy` data format, the [UN Official SDG color
scheme](https://www.un.org/sustainabledevelopment/wp-content/uploads/2019/01/SDG_Guidelines_AUG_2019_Final.pdf)
and several functions related to the mapping of text to particular sets
of keywords.

## Installation

You can install the development version from
[Github](https://github.com/USC-Office-of-Sustainability/USC-SDGmap)
with:

``` r
# install.packages("devtools")
# devtools::install_github("USC-Office-of-Sustainability/USC-SDGmap")
```

## Publicly Available SDG Keywords

The table below lists publicly available SDG keywords that have been
published online. For this package, we used the CMU250 keyword list
combined with some words from USC’s Presidental Working Group (PWG)
keyword list which mapped to the CMU1000 keyword list. Instructions for
how to update or create your own keyword list will be described later in
this readme… <!-- add a link here -->

    ## Warning: package 'knitr' was built under R version 4.0.5

| Source                                                                                                                             | Dataset                | CSV                                                                                          | SDG17 |
|:-----------------------------------------------------------------------------------------------------------------------------------|:-----------------------|:---------------------------------------------------------------------------------------------|:------|
| [USC-CMU keyword list](www.google.com)                                                                                             | `cmu_usc_pwg_mapped`   | [Link](https://github.com/USC-Office-of-Sustainability/USC-SDGmap/tree/main/Datasets/)       | Yes   |
| [Core Elsevier (Work in Progress)](https://data.mendeley.com/datasets/87txkw7khs/1)                                                | `elsevier_keywords`    | [Link](https://github.com/pwu97/SDGmapR/blob/main/datasets/elsevier_keywords_cleaned.csv)    | No    |
| [Improved Elsevier Top 100](https://data.mendeley.com/datasets/9sxdykm8s4/2)                                                       | `elsevier100_keywords` | [Link](https://github.com/pwu97/SDGmapR/blob/main/datasets/elsevier100_keywords_cleaned.csv) | No    |
| [SDSN](https://ap-unsdsn.org/regional-initiatives/universities-sdgs/)                                                              | `sdsn_keywords`        | [Link](https://github.com/pwu97/SDGmapR/blob/main/datasets/sdsn_keywords_cleaned.csv)        | Yes   |
| [CMU Top 250 Words](https://www.cmu.edu/leadership/the-provost/provost-priorities/sustainability-initiative/sdg-definitions.html)  | `cmu250_keywords`      | [Link](https://github.com/pwu97/SDGmapR/blob/main/datasets/cmu250_keywords_cleaned.csv)      | No    |
| [CMU Top 500 Words](https://www.cmu.edu/leadership/the-provost/provost-priorities/sustainability-initiative/sdg-definitions.html)  | `cmu500_keywords`      | [Link](https://github.com/pwu97/SDGmapR/blob/main/datasets/cmu500_keywords_cleaned.csv)      | No    |
| [CMU Top 1000 Words](https://www.cmu.edu/leadership/the-provost/provost-priorities/sustainability-initiative/sdg-definitions.html) | `cmu1000_keywords`     | [Link](https://github.com/pwu97/SDGmapR/blob/main/datasets/cmu1000_keywords_cleaned.csv)     | No    |
| [University of Auckland (Work in Progress)](https://www.sdgmapping.auckland.ac.nz/)                                                | `auckland_keywords`    |                                                                                              | Yes   |
| [University of Toronto (Work in Progress)](https://data.utoronto.ca/sustainable-development-goals-sdg-report/sdg-report-appendix/) | `toronto_keywords`     |                                                                                              | Yes   |

## Creating / Choosing the keyword list

A keyword list for the purposes of this R package must include the
following columns: ‘keyword’, ‘goal’, ‘weight’, ‘pattern’, ‘color’

Each keyword has a weight representing its relevance to that SDG, and a
pattern which allows our function to read capitalization and italics in
the same way. We are working on finding a way to include a pattern which
considers the plural and singular versions of words as the same. Each
SDG already has its own color assigned by the UN.

If you would like to use the USC-CMU, CMU250, or any other keyword lists
located in the Datasets folder in this repository, then no extra work is
required! <!-- skip to different section -->

If you would like to create your own keyword list with unique weights,
things become a bit more difficult. At CMU, they used Google’s word2vec
software to establish weights for each word and sdg. We have not come
around to finding a more effective method to assign weights to these
words, but check back at a later date to see our progress on this
matter.

### Including your own keywords

We were motivated to use keywords from USC’s PWG in combination with
CMU250, so we expanded the CMU250 dataset to also include words in our
list that mapped to other data sets.

In the R file named ‘keyword_list_generator.R’, you can find the code we
used to generate our keyword list.

With this code, we added words from the PWG list (that did not have
weights assigned) that were included in the CMU1000 lis using left-joins
from the ‘dplyr’ library.

In addition, this R file has code to add a column to your initial
unweighted keyword list which records the dataset each word was mapped
to.

### Exclude words

At the bottom of the same R file, there is a list called
‘exclude_words’. These are the words which we don’t want to be included
in our mapping to college course descriptions. Many of these words like
“students” and “academics” would be overly mapped due to their relevance
to course descriptions. This file is where you would be able to update
this list as you see fit. Note: You can always use the same code chunk
in other places of the code if you want to exclude at later stages and
not at the very beginning.

## Cleaning up course data

At USC, we received a file called
‘USC_FULL_COURSE_LIST_20182_20183_20191’ which can be found in the
Datasets folder. THis csv file contains data for every single course
offered for 3 different semester at USC. The first thing we did was
clean up the data, get rid of repeats (and add a separate columns to
keep track of how many sections were offered), and rename the columns.

In the R file named ‘cleaning_course_data.R’, you can see code for how
to clean this course data. The three functions to do these are
‘clean_data’, ‘get_semesters’, and ‘transform_data’ which gets the
course data into a nice format. The produced course data can be found in
the Datasets folder with the name ‘usc_courses’.

## Mapping course descriptions to 17 SDGs

In the R file named ‘mapping_course_descriptions.R’, you can find code
for mapping the clean course data to the 17 SDG’s using loops and a
function called ‘tabulate_sdg_keywords’ (which can be found in the file
named after that function). Joining the course description keywords with
their goals and weights to the chosen keyword list, this file writes out
a csv file which we refer to as the ‘master_course_SDG_data’ which you
can find in the Datasets folder.

**Notice** that in the ‘tabulate_sdg_keywords’ function, there is a
parameter which requires a keyword list. Ensure that when you run this
code (this may take awhile), the ‘keywords’ parameter is set to the
keyword list of your choosing. By default, it is set to the Elsevier top
100 keywords list.

If you created your own keyword list, you need to update the body this
function to account for the name of yours since you cannot pass a csv
file directly into the function parameter. Look at the comments in the
file and follow the example of how I added the parameter “cmu_usc” to
the function.

## Susainability related courses

With the master data, we can look into which classes are sustainability
inclusive, sustainability focused, or not related to the SDGs. Classes
defined as not related were not mapped to any SDGs. Sustainbility
focused courses are ones which mapped to both environmental SDGs as well
as economic/social ones. The criteria for these are ‘environmental
goals: 13, 14, 15’ and ‘economic goals: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10,
11, 12, 16, 17’. The R file ‘sustainability_related_classes.R’ contains
the code for how to do this. At the end, you can sum up the total number
of sustainability focused or inclusive classes and report those numbers
to Stars under AC-1 credit. <!-- need to change wording -->

### Finding the sum of the weights for each SDG

In the R file named ‘total_weight_generator.R’, you can find code that
adds the sum of the weights for each SDG a course mapped to. The
resulting csv file ‘classes_total_weight’ contains unique course titles,
as well as 17 additional columns that show the combined weight of all
keywords by SDG. The final column shows the total weight of all the
keywords mapped from that course.

### Filtering the data by threshold

You can filter data so that only classes above a certain threshold are
met.
<!-- ask julie about whether you filter the data or the keywords -->

## Creating an RShiny app

The ShinyApp folder, contains all relevant files that pertain to
creating a Shiny dashboard like the one we created. The only necessary
files for the shiny app are the master course SDG data and the cleaned
course data, as well as the chosen keyword list. Inside the folder, a
subfolder called www stores all stylesheets and pictures for the app.
<!-- add link -->

Step by step instructions on how to work the R shiny app are to follow…
<!-- ask julie about this, how much code do i show -->
