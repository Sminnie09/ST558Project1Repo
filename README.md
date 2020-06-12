ST558 Project 1
================
Noel Hilliard
6/5/2020

  - [JSON Data](#json-data)
      - [What is it?](#what-is-it)
      - [Where does it get used?](#where-does-it-get-used)
      - [Why is it a good way to store
        data?](#why-is-it-a-good-way-to-store-data)
  - [JSON Packages in R](#json-packages-in-r)
  - [Functions](#functions)
      - [Franchise Data from API](#franchise-data-from-api)
      - [Franchise Team Totals Data from
        API](#franchise-team-totals-data-from-api)
      - [Franchise Season Records Data from
        API](#franchise-season-records-data-from-api)
      - [Franchise Goalie Records Data from
        API](#franchise-goalie-records-data-from-api)
      - [Franchise Skater Records Data from
        API](#franchise-skater-records-data-from-api)
  - [Contingency Tables](#contingency-tables)
      - [Active and Inactive Players in Goalie Records for Select
        Franchises](#active-and-inactive-players-in-goalie-records-for-select-franchises)
      - [Positions in Skater Records for Select
        Franchises](#positions-in-skater-records-for-select-franchises)
  - [Numeric Summaries](#numeric-summaries)
      - [Numeric Summary of Select Columns from Skater
        Records](#numeric-summary-of-select-columns-from-skater-records)
  - [Plots](#plots)
      - [Barplot](#barplot)
      - [Boxplot](#boxplot)
      - [Scatter Plot](#scatter-plot)

Project Objective: This project involves creating a vignette (an HTML
file with a table of contents) about reading in data in JSON format and
exploring it (summaries and graphs - more details on this later). You
will then create a blog post linking to your vignette.All project work
should be done in a github repo.

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

To get started, load the `httr` and `jsonlite` libraries. The `httr`
library will be used to contact the NHL records ‘Franchise’ API. The
`JSONlite` library will be used to convert JSON data to `R` objects.

``` r
#install.packages("httr")

library("httr")

#install.packages("jsonlite")

library("jsonlite")
```

There are five functions in this vignette:

  - `getFranchise`
  - `getTeamTotals`
  - `getSeasonRecords`
  - `getGoalieRecords`
  - `getSkaterRecords`

Each function uses a `GET` request to contact the host [NHL records
‘Franchise’ API]() by initializing the variables for the API call.
Each function returns a data frame of results for a specific table.
Next, the data is parsed through to create a useful `R` object by
changing the raw data from the API call into JSON format using the
`httr` function called `content`. Finally, the parsed JSON data is
converted to a dataframe and returned by the function.

## Franchise Data from API

The `getFranchise` function returns `id`, `firstSeasonId`, and
`lastSeasonId` and name of every team in the history of the NHL from the
API for each franchise in a dataframe.

``` r
getFranchise <- function(){

  base_url <- "https://records.nhl.com/site/api"
  tab_name <- "franchise"
  full_url <- paste0(base_url, "/", tab_name)

  get_franchise <- GET(full_url, timeout = 1000)

  get_franchise_text <- content(get_franchise, "text")

  get_franchise_json <- fromJSON(get_franchise_text, flatten = TRUE)

  return(data.frame(get_franchise_json))
}
```

The `getFranchise` function returns the following dataframe:

``` r
library(knitr) #library for kable function
kable(head(getFranchise())) #call function and return dataframe
```

| data.id | data.firstSeasonId | data.lastSeasonId | data.mostRecentTeamId | data.teamCommonName | data.teamPlaceName | total |
| ------: | -----------------: | ----------------: | --------------------: | :------------------ | :----------------- | ----: |
|       1 |           19171918 |                NA |                     8 | Canadiens           | Montréal           |    38 |
|       2 |           19171918 |          19171918 |                    41 | Wanderers           | Montreal           |    38 |
|       3 |           19171918 |          19341935 |                    45 | Eagles              | St. Louis          |    38 |
|       4 |           19191920 |          19241925 |                    37 | Tigers              | Hamilton           |    38 |
|       5 |           19171918 |                NA |                    10 | Maple Leafs         | Toronto            |    38 |
|       6 |           19241925 |                NA |                     6 | Bruins              | Boston             |    38 |

## Franchise Team Totals Data from API

The `getTeamTotals` function returns total stats for every franchise
from the API.

``` r
getTeamTotals <- function(){
  
  base_url <- "https://records.nhl.com/site/api"
  tab_name <- "franchise-team-totals"
  full_url <- paste0(base_url, "/", tab_name)

  get_TeamTotals <- GET(full_url, timeout = 1000)

  get_TeamTotals_text <- content(get_TeamTotals, "text")

  get_TeamTotals_json <- fromJSON(get_TeamTotals_text, flatten = TRUE)
  
  return(data.frame(get_TeamTotals_json))
  
}
```

The `getTeamTotals` function returns the following dataframe:

``` r
kable(head(getTeamTotals())) #call function and return dataframe
```

| data.id | data.activeFranchise | data.firstSeasonId | data.franchiseId | data.gameTypeId | data.gamesPlayed | data.goalsAgainst | data.goalsFor | data.homeLosses | data.homeOvertimeLosses | data.homeTies | data.homeWins | data.lastSeasonId | data.losses | data.overtimeLosses | data.penaltyMinutes | data.pointPctg | data.points | data.roadLosses | data.roadOvertimeLosses | data.roadTies | data.roadWins | data.shootoutLosses | data.shootoutWins | data.shutouts | data.teamId | data.teamName      | data.ties | data.triCode | data.wins | total |
| ------: | -------------------: | -----------------: | ---------------: | --------------: | ---------------: | ----------------: | ------------: | --------------: | ----------------------: | ------------: | ------------: | ----------------: | ----------: | ------------------: | ------------------: | -------------: | ----------: | --------------: | ----------------------: | ------------: | ------------: | ------------------: | ----------------: | ------------: | ----------: | :----------------- | --------: | :----------- | --------: | ----: |
|       1 |                    1 |           19821983 |               23 |               2 |             2937 |              8708 |          8647 |             507 |                      82 |            96 |           783 |                NA |        1181 |                 162 |               44397 |         0.5330 |        3131 |             674 |                      80 |           123 |           592 |                  79 |                78 |           193 |           1 | New Jersey Devils  |       219 | NJD          |      1375 |   104 |
|       2 |                    1 |           19821983 |               23 |               3 |              257 |               634 |           697 |              53 |                       0 |            NA |            74 |                NA |         120 |                   0 |                4266 |         0.0039 |           2 |              67 |                       0 |            NA |            63 |                   0 |                 0 |            25 |           1 | New Jersey Devils  |        NA | NJD          |       137 |   104 |
|       3 |                    1 |           19721973 |               22 |               2 |             3732 |             11779 |         11889 |             674 |                      81 |           170 |           942 |                NA |        1570 |                 159 |               57422 |         0.5115 |        3818 |             896 |                      78 |           177 |           714 |                  67 |                82 |           167 |           2 | New York Islanders |       347 | NYI          |      1656 |   104 |
|       4 |                    1 |           19721973 |               22 |               3 |              272 |               806 |           869 |              46 |                       1 |            NA |            84 |                NA |         124 |                   0 |                5356 |         0.0147 |           8 |              78 |                       0 |            NA |            64 |                   0 |                 0 |             9 |           2 | New York Islanders |        NA | NYI          |       148 |   104 |
|       5 |                    1 |           19261927 |               10 |               2 |             6504 |             19863 |         19864 |            1132 |                      73 |           448 |          1600 |                NA |        2693 |                 147 |               85564 |         0.5125 |        6667 |            1561 |                      74 |           360 |          1256 |                  66 |                78 |           403 |           3 | New York Rangers   |       808 | NYR          |      2856 |   104 |
|       6 |                    1 |           19261927 |               10 |               3 |              515 |              1436 |          1400 |             103 |                       0 |             1 |           137 |                NA |         263 |                   0 |                8132 |         0.0000 |           0 |             160 |                       0 |             7 |           107 |                   0 |                 0 |            44 |           3 | New York Rangers   |         8 | NYR          |       244 |   104 |

## Franchise Season Records Data from API

The `getSeasonRecords` function returns season records for the specified
`FranchiseID`.

``` r
getSeasonRecords <- function(FranchiseID){
  
 
  base_url <- "https://records.nhl.com/site/api"
  tab_name <- "franchise-season-records?cayenneExp=franchiseId="
  full_url <- paste0(base_url, "/", tab_name, FranchiseID)

  get_SeasonRecords <- GET(full_url,timeout = 1000)

  get_SeasonRecords_text <- content(get_SeasonRecords, "text")

  get_SeasonRecords_json <- fromJSON(get_SeasonRecords_text, flatten = TRUE)
 
  return(data.frame(get_SeasonRecords_json))
  
}
```

Here, the `getSeasonRecords` function returns the following dataframe
when 2 is specified as the `FranchiseID`:

``` r
kable(head(getSeasonRecords(2))) #call function and return dataframe
```

| data.id | data.fewestGoals | data.fewestGoalsAgainst | data.fewestGoalsAgainstSeasons | data.fewestGoalsSeasons | data.fewestLosses | data.fewestLossesSeasons | data.fewestPoints | data.fewestPointsSeasons | data.fewestTies | data.fewestTiesSeasons | data.fewestWins | data.fewestWinsSeasons | data.franchiseId | data.franchiseName | data.homeLossStreak | data.homeLossStreakDates  | data.homePointStreak | data.homePointStreakDates | data.homeWinStreak | data.homeWinStreakDates | data.homeWinlessStreak | data.homeWinlessStreakDates | data.lossStreak | data.lossStreakDates      | data.mostGameGoals | data.mostGameGoalsDates      | data.mostGoals | data.mostGoalsAgainst | data.mostGoalsAgainstSeasons | data.mostGoalsSeasons | data.mostLosses | data.mostLossesSeasons | data.mostPenaltyMinutes | data.mostPenaltyMinutesSeasons | data.mostPoints | data.mostPointsSeasons | data.mostShutouts | data.mostShutoutsSeasons | data.mostTies | data.mostTiesSeasons | data.mostWins | data.mostWinsSeasons | data.pointStreak | data.pointStreakDates | data.roadLossStreak | data.roadLossStreakDates  | data.roadPointStreak | data.roadPointStreakDates | data.roadWinStreak | data.roadWinStreakDates | data.roadWinlessStreak | data.roadWinlessStreakDates | data.winStreak | data.winStreakDates | data.winlessStreak | data.winlessStreakDates | total |
| ------: | :--------------- | :---------------------- | :----------------------------- | :---------------------- | :---------------- | :----------------------- | :---------------- | :----------------------- | :-------------- | :--------------------- | :-------------- | :--------------------- | ---------------: | :----------------- | ------------------: | :------------------------ | :------------------- | :------------------------ | :----------------- | :---------------------- | ---------------------: | :-------------------------- | --------------: | :------------------------ | -----------------: | :--------------------------- | -------------: | --------------------: | :--------------------------- | :-------------------- | --------------: | :--------------------- | ----------------------: | :----------------------------- | --------------: | :--------------------- | ----------------: | :----------------------- | ------------: | :------------------- | ------------: | :------------------- | :--------------- | :-------------------- | ------------------: | :------------------------ | :------------------- | :------------------------ | :----------------- | :---------------------- | ---------------------: | :-------------------------- | :------------- | :------------------ | :----------------- | :---------------------- | ----: |
|      41 | NA               | NA                      | NA                             | NA                      | NA                | NA                       | NA                | NA                       | NA              | NA                     | NA              | NA                     |                2 | Montreal Wanderers |                   2 | Dec 22 1917 - Dec 26 1917 | NA                   | NA                        | NA                 | NA                      |                      2 | Dec 22 1917 - Dec 26 1917   |               5 | Dec 22 1917 - Jan 05 1918 |                 10 | Dec 19 1917 - TAN 9 @ MWN 10 |             17 |                    37 | 1917-18 (22)                 | 1917-18 (22)          |               5 | 1917-18 (22)           |                      27 | 1917-18 (22)                   |               2 | 1917-18 (22)           |                 0 | 1917-18 (22)             |             0 | 1917-18 (22)         |             1 | 1917-18 (22)         | NA               | NA                    |                   3 | Dec 29 1917 - Jan 05 1918 | NA                   | NA                        | NA                 | NA                      |                      3 | Dec 29 1917 - Jan 05 1918   | NA             | NA                  | NA                 | NA                      |     1 |

## Franchise Goalie Records Data from API

The `getGoalieRecords` function returns goalie records for the specified
`FranchiseID`.

``` r
getGoalieRecords <- function(FranchiseID){
  
  base_url <- "https://records.nhl.com/site/api"
  tab_name <- "franchise-goalie-records?cayenneExp=franchiseId="
  full_url <- paste0(base_url, "/", tab_name, FranchiseID)

  get_GoalieRecords <- GET(full_url, timeout = 1000)

  get_GoalieRecords_text <- content(get_GoalieRecords, "text")

  get_GoalieRecords_json <- fromJSON(get_GoalieRecords_text, flatten = TRUE) 
  
  return(data.frame(get_GoalieRecords_json))
}
```

Here, the `getGoalieRecords` function returns the following dataframe
when 2 is specified as the `FranchiseID`:

``` r
kable(head(getGoalieRecords(2))) #call function and return dataframe
```

| data.id | data.activePlayer | data.firstName | data.franchiseId | data.franchiseName | data.gameTypeId | data.gamesPlayed | data.lastName | data.losses | data.mostGoalsAgainstDates | data.mostGoalsAgainstOneGame | data.mostSavesDates | data.mostSavesOneGame | data.mostShotsAgainstDates | data.mostShotsAgainstOneGame | data.mostShutoutsOneSeason | data.mostShutoutsSeasonIds | data.mostWinsOneSeason | data.mostWinsSeasonIds | data.overtimeLosses | data.playerId | data.positionCode | data.rookieGamesPlayed | data.rookieShutouts | data.rookieWins | data.seasons | data.shutouts | data.ties | data.wins | total |
| ------: | :---------------- | :------------- | ---------------: | :----------------- | --------------: | ---------------: | :------------ | ----------: | :------------------------- | ---------------------------: | :------------------ | :-------------------- | :------------------------- | :--------------------------- | -------------------------: | :------------------------- | ---------------------: | :--------------------- | :------------------ | ------------: | :---------------- | ---------------------: | ------------------: | --------------: | -----------: | ------------: | --------: | --------: | ----: |
|     250 | FALSE             | Bert           |                2 | Montreal Wanderers |               2 |                4 | Lindsay       |           3 | 1917-12-22                 |                           11 | NA                  | NA                    | NA                         | NA                           |                          0 | 19171918                   |                      1 | 19171918               | NA                  |       8450014 | G                 |                      4 |                   0 |               1 |            1 |             0 |         0 |         1 |     1 |

## Franchise Skater Records Data from API

The `getSkaterRecords` function returns skater records for the specified
`FranchiseID`.

``` r
getSkaterRecords <- function(FranchiseID){
  
  base_url <- "https://records.nhl.com/site/api"
  tab_name <- "franchise-skater-records?cayenneExp=franchiseId="
  full_url <- paste0(base_url, "/", tab_name, FranchiseID)

  get_SkaterRecords <- GET(full_url, timeout = 1000)

  get_SkaterRecords_text <- content(get_SkaterRecords, "text")

  get_SkaterRecords_json <- fromJSON(get_SkaterRecords_text, flatten = TRUE) 
  
  return(data.frame(get_SkaterRecords_json))
}
```

Here, the `getSkaterRecords` function returns the following dataframe
when 2 is specified as the `FranchiseID`:

``` r
kable(head(getSkaterRecords(2))) #call function and return dataframe
```

    ## No encoding supplied: defaulting to UTF-8.

| data.id | data.activePlayer | data.assists | data.firstName | data.franchiseId | data.franchiseName | data.gameTypeId | data.gamesPlayed | data.goals | data.lastName | data.mostAssistsGameDates                      | data.mostAssistsOneGame | data.mostAssistsOneSeason | data.mostAssistsSeasonIds | data.mostGoalsGameDates                        | data.mostGoalsOneGame | data.mostGoalsOneSeason | data.mostGoalsSeasonIds | data.mostPenaltyMinutesOneSeason | data.mostPenaltyMinutesSeasonIds | data.mostPointsGameDates                       | data.mostPointsOneGame | data.mostPointsOneSeason | data.mostPointsSeasonIds | data.penaltyMinutes | data.playerId | data.points | data.positionCode | data.rookiePoints | data.seasons | total |
| ------: | :---------------- | -----------: | :------------- | ---------------: | :----------------- | --------------: | ---------------: | ---------: | :------------ | :--------------------------------------------- | ----------------------: | ------------------------: | :------------------------ | :--------------------------------------------- | --------------------: | ----------------------: | :---------------------- | -------------------------------: | :------------------------------- | :--------------------------------------------- | ---------------------: | -----------------------: | :----------------------- | ------------------: | ------------: | ----------: | :---------------- | ----------------: | -----------: | ----: |
|   16889 | FALSE             |            0 | Billy          |                2 | Montreal Wanderers |               2 |                2 |          1 | Bell          | 1917-12-19, 1917-12-29                         |                       0 |                         0 | 19171918                  | 1917-12-19                                     |                     1 |                       1 | 19171918                |                                0 | 19171918                         | 1917-12-19                                     |                      1 |                        1 | 19171918                 |                   0 |       8445044 |           1 | C                 |                 1 |            1 |    11 |
|   16897 | FALSE             |            0 | Gerry          |                2 | Montreal Wanderers |               2 |                4 |          0 | Geran         | 1917-12-19, 1917-12-22, 1917-12-26, 1917-12-29 |                       0 |                         0 | 19171918                  | 1917-12-19, 1917-12-22, 1917-12-26, 1917-12-29 |                     0 |                       0 | 19171918                |                                0 | 19171918                         | 1917-12-19, 1917-12-22, 1917-12-26, 1917-12-29 |                      0 |                        0 | 19171918                 |                   0 |       8446580 |           0 | C                 |                 0 |            1 |    11 |
|   16901 | FALSE             |            1 | Harry          |                2 | Montreal Wanderers |               2 |                4 |          6 | Hyland        | 1917-12-29                                     |                       1 |                         1 | 19171918                  | 1917-12-19                                     |                     5 |                       6 | 19171918                |                                6 | 19171918                         | 1917-12-19                                     |                      5 |                        7 | 19171918                 |                   6 |       8447013 |           7 | R                 |                 7 |            1 |    11 |
|   16903 | FALSE             |            0 | Jack           |                2 | Montreal Wanderers |               2 |                1 |          0 | Marks         | 1917-12-29                                     |                       0 |                         0 | 19171918                  | 1917-12-29                                     |                     0 |                       0 | 19171918                |                                0 | 19171918                         | 1917-12-29                                     |                      0 |                        0 | 19171918                 |                   0 |       8447616 |           0 | L                 |                 0 |            1 |    11 |
|   16904 | FALSE             |            1 | Jack           |                2 | Montreal Wanderers |               2 |                4 |          3 | McDonald      | 1917-12-29                                     |                       1 |                         1 | 19171918                  | 1917-12-19, 1917-12-22, 1917-12-26             |                     1 |                       3 | 19171918                |                                3 | 19171918                         | 1917-12-19, 1917-12-22, 1917-12-26, 1917-12-29 |                      1 |                        4 | 19171918                 |                   3 |       8447761 |           4 | L                 |                 4 |            1 |    11 |
|   16908 | FALSE             |            0 | George         |                2 | Montreal Wanderers |               2 |                4 |          0 | O’Grady       | 1917-12-19, 1917-12-22, 1917-12-26, 1917-12-29 |                       0 |                         0 | 19171918                  | 1917-12-19, 1917-12-22, 1917-12-26, 1917-12-29 |                     0 |                       0 | 19171918                |                                0 | 19171918                         | 1917-12-19, 1917-12-22, 1917-12-26, 1917-12-29 |                      0 |                        0 | 19171918                 |                   0 |       8448052 |           0 | D                 |                 0 |            1 |    11 |

# Contingency Tables

This section covers using the functions above to create contingency
tables of the NHL data. Contingency tables are tables used to show the
frequency of categorical variables.

## Active and Inactive Players in Goalie Records for Select Franchises

``` r
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

# Plots

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
