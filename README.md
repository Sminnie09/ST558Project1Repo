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
  
  return(data.frame(get_TeamTotals_json[[1]]))
  
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

##
