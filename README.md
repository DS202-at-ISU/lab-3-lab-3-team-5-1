
<!-- README.md is generated from README.Rmd. Please edit the README.Rmd file -->

# Lab report \#3 - instructions

Follow the instructions posted at
<https://ds202-at-isu.github.io/labs.html> for the lab assignment. The
work is meant to be finished during the lab time, but you have time
until Monday evening to polish things.

Include your answers in this document (Rmd file). Make sure that it
knits properly (into the md file). Upload both the Rmd and the md file
to your repository.

All submissions to the github repo will be automatically uploaded for
grading once the due date is passed. Submit a link to your repository on
Canvas (only one submission per team) to signal to the instructors that
you are done with your submission.

# Lab 3: Avenger’s Peril

## As a team

Extract from the data below two data sets in long form `deaths` and
`returns`

``` r
av <- read.csv("https://raw.githubusercontent.com/fivethirtyeight/data/master/avengers/avengers.csv", stringsAsFactors = FALSE)
head(av)
```

    ##                                                       URL
    ## 1           http://marvel.wikia.com/Henry_Pym_(Earth-616)
    ## 2      http://marvel.wikia.com/Janet_van_Dyne_(Earth-616)
    ## 3       http://marvel.wikia.com/Anthony_Stark_(Earth-616)
    ## 4 http://marvel.wikia.com/Robert_Bruce_Banner_(Earth-616)
    ## 5        http://marvel.wikia.com/Thor_Odinson_(Earth-616)
    ## 6       http://marvel.wikia.com/Richard_Jones_(Earth-616)
    ##                    Name.Alias Appearances Current. Gender Probationary.Introl
    ## 1   Henry Jonathan "Hank" Pym        1269      YES   MALE                    
    ## 2              Janet van Dyne        1165      YES FEMALE                    
    ## 3 Anthony Edward "Tony" Stark        3068      YES   MALE                    
    ## 4         Robert Bruce Banner        2089      YES   MALE                    
    ## 5                Thor Odinson        2402      YES   MALE                    
    ## 6      Richard Milhouse Jones         612      YES   MALE                    
    ##   Full.Reserve.Avengers.Intro Year Years.since.joining Honorary Death1 Return1
    ## 1                      Sep-63 1963                  52     Full    YES      NO
    ## 2                      Sep-63 1963                  52     Full    YES     YES
    ## 3                      Sep-63 1963                  52     Full    YES     YES
    ## 4                      Sep-63 1963                  52     Full    YES     YES
    ## 5                      Sep-63 1963                  52     Full    YES     YES
    ## 6                      Sep-63 1963                  52 Honorary     NO        
    ##   Death2 Return2 Death3 Return3 Death4 Return4 Death5 Return5
    ## 1                                                            
    ## 2                                                            
    ## 3                                                            
    ## 4                                                            
    ## 5    YES      NO                                             
    ## 6                                                            
    ##                                                                                                                                                                              Notes
    ## 1                                                                                                                Merged with Ultron in Rage of Ultron Vol. 1. A funeral was held. 
    ## 2                                                                                                  Dies in Secret Invasion V1:I8. Actually was sent tto Microverse later recovered
    ## 3 Death: "Later while under the influence of Immortus Stark committed a number of horrible acts and was killed.'  This set up young Tony. Franklin Richards later brought him back
    ## 4                                                                               Dies in Ghosts of the Future arc. However "he had actually used a hidden Pantheon base to survive"
    ## 5                                                      Dies in Fear Itself brought back because that's kind of the whole point. Second death in Time Runs Out has not yet returned
    ## 6                                                                                                                                                                             <NA>

``` r
#View(av)
```

Get the data into a format where the five columns for Death\[1-5\] are
replaced by two columns: Time, and Death. Time should be a number
between 1 and 5 (look into the function `parse_number`); Death is a
categorical variables with values “yes”, “no” and ““. Call the resulting
data set `deaths`.

Similarly, deal with the returns of characters.

Based on these datasets calculate the average number of deaths an
Avenger suffers.

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ## ✔ ggplot2   3.5.2     ✔ tibble    3.3.0
    ## ✔ lubridate 1.9.4     ✔ tidyr     1.3.1
    ## ✔ purrr     1.1.0     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
#reshape deaths data
deaths <- av |> 
  select(
    URL,
    Name.Alias,
    starts_with("Death")
  ) |> 
  pivot_longer(
    Death1:Death5,
    names_to = "Time",
    values_to = "Death"
  ) |>
  mutate(
    Time = parse_number(Time)
  ) |>
  filter(Death != "")
deaths
```

    ## # A tibble: 194 × 4
    ##    URL                                                    Name.Alias  Time Death
    ##    <chr>                                                  <chr>      <dbl> <chr>
    ##  1 http://marvel.wikia.com/Henry_Pym_(Earth-616)          "Henry Jo…     1 YES  
    ##  2 http://marvel.wikia.com/Janet_van_Dyne_(Earth-616)     "Janet va…     1 YES  
    ##  3 http://marvel.wikia.com/Anthony_Stark_(Earth-616)      "Anthony …     1 YES  
    ##  4 http://marvel.wikia.com/Robert_Bruce_Banner_(Earth-61… "Robert B…     1 YES  
    ##  5 http://marvel.wikia.com/Thor_Odinson_(Earth-616)       "Thor Odi…     1 YES  
    ##  6 http://marvel.wikia.com/Thor_Odinson_(Earth-616)       "Thor Odi…     2 YES  
    ##  7 http://marvel.wikia.com/Richard_Jones_(Earth-616)      "Richard …     1 NO   
    ##  8 http://marvel.wikia.com/Steven_Rogers_(Earth-616)      "Steven R…     1 YES  
    ##  9 http://marvel.wikia.com/Clint_Barton_(Earth-616)       "Clinton …     1 YES  
    ## 10 http://marvel.wikia.com/Clint_Barton_(Earth-616)       "Clinton …     2 YES  
    ## # ℹ 184 more rows

``` r
#View(deaths)

#reshape return data
returns <- av |> 
  select(
    URL,
    Name.Alias,
    starts_with("Return")
  ) |> 
  pivot_longer(
    Return1:Return5,
    names_to = "Time",
    values_to = "Return"
  ) |>
  mutate(
    Time = parse_number(Time)
  ) |>
  filter(Return != "")
returns
```

    ## # A tibble: 89 × 4
    ##    URL                                                   Name.Alias  Time Return
    ##    <chr>                                                 <chr>      <dbl> <chr> 
    ##  1 http://marvel.wikia.com/Henry_Pym_(Earth-616)         "Henry Jo…     1 NO    
    ##  2 http://marvel.wikia.com/Janet_van_Dyne_(Earth-616)    "Janet va…     1 YES   
    ##  3 http://marvel.wikia.com/Anthony_Stark_(Earth-616)     "Anthony …     1 YES   
    ##  4 http://marvel.wikia.com/Robert_Bruce_Banner_(Earth-6… "Robert B…     1 YES   
    ##  5 http://marvel.wikia.com/Thor_Odinson_(Earth-616)      "Thor Odi…     1 YES   
    ##  6 http://marvel.wikia.com/Thor_Odinson_(Earth-616)      "Thor Odi…     2 NO    
    ##  7 http://marvel.wikia.com/Steven_Rogers_(Earth-616)     "Steven R…     1 YES   
    ##  8 http://marvel.wikia.com/Clint_Barton_(Earth-616)      "Clinton …     1 YES   
    ##  9 http://marvel.wikia.com/Clint_Barton_(Earth-616)      "Clinton …     2 YES   
    ## 10 http://marvel.wikia.com/Pietro_Maximoff_(Earth-616)   "Pietro M…     1 YES   
    ## # ℹ 79 more rows

``` r
#View(returns)


library(dplyr)
deaths |>
  summarize(mean(Time))
```

    ## # A tibble: 1 × 1
    ##   `mean(Time)`
    ##          <dbl>
    ## 1         1.14

## Individually

For each team member, copy this part of the report.

Each team member picks one of the statements in the FiveThirtyEight
[analysis](https://fivethirtyeight.com/features/avengers-death-comics-age-of-ultron/)
and fact checks it based on the data. Use dplyr functionality whenever
possible.

### FiveThirtyEight Statement

> Quote the statement you are planning to fact-check.

### Include the code

Make sure to include the code to derive the (numeric) fact for the
statement

### Include your answer

Include at least one sentence discussing the result of your
fact-checking endeavor.

David’s Work: “Out of 173 listed Avengers, my analysis found that 69 had
died at least one time after they joined the team.”

``` r
died_at_least_once <- deaths |>
  filter(Death == "YES") |>
  distinct(Name.Alias) |>
  nrow()
died_at_least_once
```

    ## [1] 64

``` r
View(died_at_least_once)
```

Based on my analysis, I filtered for avengers that have died at least
one time and selected distinct rows based on their names. The output of
this new dataframe is 64, which is not equal to the 69 figure that they
got, so I don’t believe their analysis was correct as there was actually
only 64 Avengers that have died at least one time.

Upload your changes to the repository. Discuss and refine answers as a
team.
