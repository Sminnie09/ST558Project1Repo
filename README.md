ST558 Project 1
================
Noel Hilliard
6/5/2020

Project Objective: This project involves creating a vignette (an HTML
file with a table of contents) about reading in data in JSON format and
exploring it (summaries and graphs - more details on this later). You
will then create a blog post linking to your vignette.All project work
should be done in a github repo.

**Table of Contents**

  - [JSON Data](#markdown-header-JSON-Data)
  - [Functions](#markdown-header-Functions)

# JSON Data

## What is it?

JavaScript Object Notation (JSON) data is a lightweight data-interchange
format used to store data. According to
[JSON.org](https://www.json.org/json-en.html), JSON data has two data
structures that are recognized by all programming languages: (1) A
collection of name/value pairs (2) An ordered list of values. The first
data structure is recognized as “an object, record, struct, dictionary,
hash table, keyed list, or associative array.” The second data structure
is recognized as “an array, vector list, or sequence.” There are seven
value types: string, number, object, array, true, false, and null
(<https://javaee.github.io/tutorial/jsonp001.html>) JSON data is a text
format that does not depend on language so it is compatible with several
languages including C, C++, C\#, Java, JavaScript, Perl, and
Python.[(JSON.org)](https://www.json.org/json-en.html)

## Where does it get used?

JSON data is used for a variety of applications. JSON data format can be
used for browser extensions and websites that are written in JavaScript.
It can be used as an alternate data format for XML to transmit data
between the client and server in web applications. Other web
applications include the use of JSON data in programming of Web Services
and APIs to provide access to public data. Lastly, JSON data is used for
serialization and transmission of the structured data over the
network.(<https://blog.eduonix.com/web-programming-tutorials/learn-use-json/>)

## Why is it a good way to store data?

JSON data is a good way to store data because it is a text only format.
The format can be conveniently transferred with servers and JSON is a
data format that can be used by any programming language.
(<https://www.w3schools.com/js/js_json_intro.asp>)

# JSON Packages in R

The three major packages to read JSON data into R are `rJSON`,
`RJSONIO`, and `RJSONlite`. All three packages have two identical
functions to convert between JSON data and `R` objects: `toJSON` and
`fromJSON`. In addtion to these functions, `rJSON` has a function called
`newJSONParser` to convert JSON objects into R objects. `RJSONIO` has an
additional function called `asJSVars` to serialize R objects. The last
package, `RJSONlite` is the one I chose to use for this vignette because
it can interact with APIs, building pipelines, and streaming data.
(<http://anotherpeak.org/blog/tech/2016/03/10/understand_json_3.html>)

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
GoalieRecords <- rbind(getGoalieRecords(5),getGoalieRecords(10), getGoalieRecords(20), getGoalieRecords(25), getGoalieRecords(15), getGoalieRecords(22))

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
SkaterRecords <- rbind(getSkaterRecords(5), getSkaterRecords(10), getSkaterRecords(20),getSkaterRecords(25), getSkaterRecords(15), getSkaterRecords(22))

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

SkatersTable <- function(position){
  data <- getSkaterRecords(5) %>% filter(data.positionCode == position) %>% select(data.assists,data.gamesPlayed, data.goals, data.points, data.seasons)
  kable(apply(data, 2, summary))
}
```

``` r
SkatersTable("L")
```

|         | data.assists | data.gamesPlayed | data.goals | data.points | data.seasons |
| ------- | -----------: | ---------------: | ---------: | ----------: | -----------: |
| Min.    |      0.00000 |           1.0000 |    0.00000 |     0.00000 |     1.000000 |
| 1st Qu. |      1.00000 |          10.2500 |    0.00000 |     1.25000 |     1.000000 |
| Median  |      7.00000 |          54.0000 |    7.00000 |    14.00000 |     2.000000 |
| Mean    |     30.75253 |         116.6263 |   27.56566 |    58.31818 |     2.893939 |
| 3rd Qu. |     37.00000 |         164.0000 |   29.00000 |    66.25000 |     4.000000 |
| Max.    |    312.00000 |         947.0000 |  296.00000 |   597.00000 |    14.000000 |

``` r
SkatersTable("C")
```

|         | data.assists | data.gamesPlayed | data.goals | data.points | data.seasons |
| ------- | -----------: | ---------------: | ---------: | ----------: | -----------: |
| Min.    |      0.00000 |           1.0000 |    0.00000 |     0.00000 |     1.000000 |
| 1st Qu. |      1.00000 |          15.0000 |    1.00000 |     2.00000 |     1.000000 |
| Median  |      9.00000 |          52.0000 |    7.00000 |    15.00000 |     2.000000 |
| Mean    |     39.81435 |         114.0295 |   28.03797 |    67.85232 |     2.860759 |
| 3rd Qu. |     38.00000 |         145.0000 |   26.00000 |    61.00000 |     3.000000 |
| Max.    |    567.00000 |        1062.0000 |  420.00000 |   987.00000 |    15.000000 |

``` r
SkatersTable("D")
```

|         | data.assists | data.gamesPlayed | data.goals | data.points | data.seasons |
| ------- | -----------: | ---------------: | ---------: | ----------: | -----------: |
| Min.    |      0.00000 |           1.0000 |   0.000000 |     0.00000 |     1.000000 |
| 1st Qu. |      1.00000 |          15.0000 |   0.000000 |     2.00000 |     1.000000 |
| Median  |      8.00000 |          58.0000 |   3.000000 |    11.00000 |     2.000000 |
| Mean    |     31.94118 |         122.3322 |   9.681661 |    41.62284 |     2.948097 |
| 3rd Qu. |     31.00000 |         151.0000 |   9.000000 |    41.00000 |     4.000000 |
| Max.    |    620.00000 |        1184.0000 | 148.000000 |   768.00000 |    20.000000 |

``` r
SkatersTable("R")
```

|         | data.assists | data.gamesPlayed | data.goals | data.points | data.seasons |
| ------- | -----------: | ---------------: | ---------: | ----------: | -----------: |
| Min.    |      0.00000 |           1.0000 |    0.00000 |     0.00000 |     1.000000 |
| 1st Qu. |      2.00000 |          17.0000 |    1.00000 |     3.00000 |     1.000000 |
| Median  |     11.00000 |          70.0000 |    9.00000 |    23.00000 |     2.000000 |
| Mean    |     37.24294 |         127.0621 |   32.37288 |    69.61582 |     3.056497 |
| 3rd Qu. |     42.00000 |         172.0000 |   36.00000 |    82.00000 |     4.000000 |
| Max.    |    417.00000 |        1188.0000 |  332.00000 |   713.00000 |    21.000000 |

## Barplot

``` r
library(ggplot2) #load ggplot for plots

#dataframe of subset of skater records 
SkaterRecords <- rbind(getSkaterRecords(5), getSkaterRecords(10), getSkaterRecords(20),getSkaterRecords(25), getSkaterRecords(15), getSkaterRecords(22))

#rename factor levels
SkaterRecords$data.activePlayer <- factor(SkaterRecords$data.activePlayer)
player_new <- c("Inactive Player", "Active Player")
levels(SkaterRecords$data.activePlayer) <- player_new

#
g <- ggplot(data = SkaterRecords, aes(x = data.franchiseName))
g + geom_bar(aes(fill = data.activePlayer), position = "dodge") + labs(x = "Franchise Name", fill = "") + theme(axis.text.x = element_text(size = 10, angle = 90))
```

![](README_files/figure-gfm/Skater%20bar%20plot-1.png)<!-- -->

## Boxplot

``` r
#create dataframe of select franchises from goalie records
GoalieRecords <- rbind(getGoalieRecords(5),getGoalieRecords(10), getGoalieRecords(20), getGoalieRecords(25), getGoalieRecords(15), getGoalieRecords(22))

#create barplot
g <- ggplot(GoalieRecords, aes(x = data.franchiseName, y = data.losses))
g + geom_boxplot() + theme(axis.text.x = element_text(size = 10, angle = 90)) + labs(x = "Franchise Name", y = "Losses")
```

![](README_files/figure-gfm/losses%20boxplot-1.png)<!-- -->

## Scatter Plot

``` r
SkaterRecords <- rbind(getSkaterRecords(23), getSkaterRecords(16), getSkaterRecords(3), getSkaterRecords(6))

g <- ggplot(SkaterRecords, aes(x = data.gamesPlayed, y = data.mostGoalsOneSeason))
g + geom_point(aes(color = data.franchiseName)) + labs(x = "Games Played", y = "Most Goals in One Season", color = "Franchise Name")
```

![](README_files/figure-gfm/scatter%20plot-1.png)<!-- -->
