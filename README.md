ST558 Project 1
================
Noel Hilliard
6/5/2020

Project Objective: This project involves creating a vignette (an HTML
file with a table of contents) about reading in data in JSON format and
exploring it (summaries and graphs - more details on this later). You
will then create a blog post linking to your vignette.All project work
should be done in a github repo.

# Functions

``` r
#install.packages("httr")

library("httr")

#install.packages("jsonlite")

library("jsonlite")
```

## Franchise Data from API

``` r
getFranchise <- function(){

  base_url <- "https://records.nhl.com/site/api"
  tab_name <- "franchise"
  full_url <- paste0(base_url, "/", tab_name)

  get_franchise <- GET(full_url)

  get_franchise_text <- content(get_franchise, "text")

  get_franchise_json <- fromJSON(get_franchise_text, flatten = TRUE)

  return(data.frame(get_franchise_json))
}
```

## Franchise Team Totals Data from API

``` r
getTeamTotals <- function(){
  
  base_url <- "https://records.nhl.com/site/api"
  tab_name <- "franchise-team-totals"
  full_url <- paste0(base_url, "/", tab_name)

  get_TeamTotals <- GET(full_url)

  get_TeamTotals_text <- content(get_TeamTotals, "text")

  get_TeamTotals_json <- fromJSON(get_TeamTotals_text, flatten = TRUE)
  
  return(data.frame(get_TeamTotals_json))
  
}
```

## Franchise Season Records Data from API

``` r
getSeasonRecords <- function(FranchiseID){
  
 
  base_url <- "https://records.nhl.com/site/api"
  tab_name <- "franchise-season-records?cayenneExp=franchiseId="
  full_url <- paste0(base_url, "/", tab_name, FranchiseID)

  get_SeasonRecords <- GET(full_url)

  get_SeasonRecords_text <- content(get_SeasonRecords, "text")

  get_SeasonRecords_json <- fromJSON(get_SeasonRecords_text, flatten = TRUE)
 
  return(data.frame(get_SeasonRecords_json))
  
}
```

## Franchise Goalie Records Data from API

``` r
getGoalieRecords <- function(FranchiseID){
  
  base_url <- "https://records.nhl.com/site/api"
  tab_name <- "franchise-goalie-records?cayenneExp=franchiseId="
  full_url <- paste0(base_url, "/", tab_name, FranchiseID)

  get_GoalieRecords <- GET(full_url)

  get_GoalieRecords_text <- content(get_GoalieRecords, "text")

  get_GoalieRecords_json <- fromJSON(get_GoalieRecords_text, flatten = TRUE) 
  
  return(data.frame(get_GoalieRecords_json))
}
```

## Franchise Skater Records Data from API

``` r
getSkaterRecords <- function(FranchiseID){
  
  base_url <- "https://records.nhl.com/site/api"
  tab_name <- "franchise-skater-records?cayenneExp=franchiseId="
  full_url <- paste0(base_url, "/", tab_name, FranchiseID)

  get_SkaterRecords <- GET(full_url)

  get_SkaterRecords_text <- content(get_SkaterRecords, "text")

  get_SkaterRecords_json <- fromJSON(get_SkaterRecords_text, flatten = TRUE) 
  
  return(data.frame(get_SkaterRecords_json))
}
```

# Contingency Tables

## Active and Inactive Players in Goalie Records for Select Franchises

``` r
library(knitr) #Load knirt r package

#Create dataframe of goalie records for select franchises
GoalieRecords <- rbind(getGoalieRecords(5),getGoalieRecords(10), getGoalieRecords(20), 
                       getGoalieRecords(25), getGoalieRecords(15), getGoalieRecords(22))

#factor columns of interest
GoalieRecords$data.activePlayer <- factor(GoalieRecords$data.activePlayer)
GoalieRecords$data.franchiseName <- factor(GoalieRecords$data.franchiseName)

#create table
kable(table(GoalieRecords$data.franchiseName,GoalieRecords$data.activePlayer), 
      col.names = c("Inactive Player", "Active Player"))
```

|                     | Inactive Player | Active Player |
| ------------------- | --------------: | ------------: |
| Dallas Stars        |              34 |             3 |
| Edmonton Oilers     |              39 |             4 |
| New York Islanders  |              26 |             4 |
| New York Rangers    |              39 |             3 |
| Toronto Maple Leafs |              46 |             7 |
| Vancouver Canucks   |              34 |             5 |

## Positions in Skater Records for Select Franchises

``` r
#Create dataframe of goalie records for select franchises
SkaterRecords <- rbind(getSkaterRecords(5), getSkaterRecords(10), getSkaterRecords(20),
                       getSkaterRecords(25), getSkaterRecords(15), getSkaterRecords(22))

#factor columns of interest
SkaterRecords$data.franchiseName <- factor(SkaterRecords$data.franchiseName)
SkaterRecords$data.positionCode <- factor(SkaterRecords$data.positionCode)

#create table
kable(table(SkaterRecords$data.franchiseName, SkaterRecords$data.positionCode))
```

|                     |   C |   D |   L |   R |
| ------------------- | --: | --: | --: | --: |
| Dallas Stars        | 152 | 204 | 134 | 125 |
| Edmonton Oilers     | 127 | 183 | 114 |  85 |
| New York Islanders  | 135 | 178 | 108 |  88 |
| New York Rangers    | 228 | 308 | 230 | 217 |
| Toronto Maple Leafs | 237 | 289 | 198 | 177 |
| Vancouver Canucks   | 146 | 197 | 106 | 112 |

# Numeric Summaries

## Numeric Summary of Select Columns from Skater Records

``` r
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
TeamTotalsTable <- function(position){
  data <- SkaterRecords %>% filter(data.positionCode == position) %>% select(data.gamesPlayed, data.goals)
  kable(apply(data, 2, summary))
}

TeamTotalsTable("L")
```

|         | data.gamesPlayed | data.goals |
| ------- | ---------------: | ---------: |
| Min.    |           1.0000 |    0.00000 |
| 1st Qu. |          13.0000 |    1.00000 |
| Median  |          54.0000 |    6.00000 |
| Mean    |         111.9966 |   24.93596 |
| 3rd Qu. |         138.7500 |   24.00000 |
| Max.    |        1306.0000 |  393.00000 |

``` r
TeamTotalsTable("C")
```

|         | data.gamesPlayed | data.goals |
| ------- | ---------------: | ---------: |
| Min.    |           1.0000 |    0.00000 |
| 1st Qu. |          13.0000 |    1.00000 |
| Median  |          53.0000 |    6.00000 |
| Mean    |         117.6634 |   27.80683 |
| 3rd Qu. |         144.0000 |   26.00000 |
| Max.    |        1459.0000 |  583.00000 |
