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

The `getFranchise` function returns a dataframe with the following
columns:

``` r
names(getFranchise())
```

    ## [1] "data.id"               "data.firstSeasonId"    "data.lastSeasonId"    
    ## [4] "data.mostRecentTeamId" "data.teamCommonName"   "data.teamPlaceName"   
    ## [7] "total"

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

The `getTeamTotals` function returns a dataframe with the following
columns:

``` r
names(getTeamTotals())
```

    ##  [1] "data.id"                 "data.activeFranchise"   
    ##  [3] "data.firstSeasonId"      "data.franchiseId"       
    ##  [5] "data.gameTypeId"         "data.gamesPlayed"       
    ##  [7] "data.goalsAgainst"       "data.goalsFor"          
    ##  [9] "data.homeLosses"         "data.homeOvertimeLosses"
    ## [11] "data.homeTies"           "data.homeWins"          
    ## [13] "data.lastSeasonId"       "data.losses"            
    ## [15] "data.overtimeLosses"     "data.penaltyMinutes"    
    ## [17] "data.pointPctg"          "data.points"            
    ## [19] "data.roadLosses"         "data.roadOvertimeLosses"
    ## [21] "data.roadTies"           "data.roadWins"          
    ## [23] "data.shootoutLosses"     "data.shootoutWins"      
    ## [25] "data.shutouts"           "data.teamId"            
    ## [27] "data.teamName"           "data.ties"              
    ## [29] "data.triCode"            "data.wins"              
    ## [31] "total"

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

Here, the `getSeasonRecords` function returns a dataframe for the
specified franchise with the following columns. Here is an example of
the columns when 2 is specified as the `FranchiseID`:

``` r
names(getSeasonRecords(2))
```

    ##  [1] "data.id"                        "data.fewestGoals"              
    ##  [3] "data.fewestGoalsAgainst"        "data.fewestGoalsAgainstSeasons"
    ##  [5] "data.fewestGoalsSeasons"        "data.fewestLosses"             
    ##  [7] "data.fewestLossesSeasons"       "data.fewestPoints"             
    ##  [9] "data.fewestPointsSeasons"       "data.fewestTies"               
    ## [11] "data.fewestTiesSeasons"         "data.fewestWins"               
    ## [13] "data.fewestWinsSeasons"         "data.franchiseId"              
    ## [15] "data.franchiseName"             "data.homeLossStreak"           
    ## [17] "data.homeLossStreakDates"       "data.homePointStreak"          
    ## [19] "data.homePointStreakDates"      "data.homeWinStreak"            
    ## [21] "data.homeWinStreakDates"        "data.homeWinlessStreak"        
    ## [23] "data.homeWinlessStreakDates"    "data.lossStreak"               
    ## [25] "data.lossStreakDates"           "data.mostGameGoals"            
    ## [27] "data.mostGameGoalsDates"        "data.mostGoals"                
    ## [29] "data.mostGoalsAgainst"          "data.mostGoalsAgainstSeasons"  
    ## [31] "data.mostGoalsSeasons"          "data.mostLosses"               
    ## [33] "data.mostLossesSeasons"         "data.mostPenaltyMinutes"       
    ## [35] "data.mostPenaltyMinutesSeasons" "data.mostPoints"               
    ## [37] "data.mostPointsSeasons"         "data.mostShutouts"             
    ## [39] "data.mostShutoutsSeasons"       "data.mostTies"                 
    ## [41] "data.mostTiesSeasons"           "data.mostWins"                 
    ## [43] "data.mostWinsSeasons"           "data.pointStreak"              
    ## [45] "data.pointStreakDates"          "data.roadLossStreak"           
    ## [47] "data.roadLossStreakDates"       "data.roadPointStreak"          
    ## [49] "data.roadPointStreakDates"      "data.roadWinStreak"            
    ## [51] "data.roadWinStreakDates"        "data.roadWinlessStreak"        
    ## [53] "data.roadWinlessStreakDates"    "data.winStreak"                
    ## [55] "data.winStreakDates"            "data.winlessStreak"            
    ## [57] "data.winlessStreakDates"        "total"

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

Here, the `getGoalieRecords` function returns a dataframe for the
specified franchise with the following columns. Here is an example of
the columns when 2 is specified as the `FranchiseID`:

``` r
names(getGoalieRecords(2))
```

    ##  [1] "data.id"                      "data.activePlayer"           
    ##  [3] "data.firstName"               "data.franchiseId"            
    ##  [5] "data.franchiseName"           "data.gameTypeId"             
    ##  [7] "data.gamesPlayed"             "data.lastName"               
    ##  [9] "data.losses"                  "data.mostGoalsAgainstDates"  
    ## [11] "data.mostGoalsAgainstOneGame" "data.mostSavesDates"         
    ## [13] "data.mostSavesOneGame"        "data.mostShotsAgainstDates"  
    ## [15] "data.mostShotsAgainstOneGame" "data.mostShutoutsOneSeason"  
    ## [17] "data.mostShutoutsSeasonIds"   "data.mostWinsOneSeason"      
    ## [19] "data.mostWinsSeasonIds"       "data.overtimeLosses"         
    ## [21] "data.playerId"                "data.positionCode"           
    ## [23] "data.rookieGamesPlayed"       "data.rookieShutouts"         
    ## [25] "data.rookieWins"              "data.seasons"                
    ## [27] "data.shutouts"                "data.ties"                   
    ## [29] "data.wins"                    "total"

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

Here, the `getSkaterRecords` function returns a dataframe for the
specified franchise with the following columns. Here is an example of
the columns when 2 is specified as the `FranchiseID`:

``` r
names(getSkaterRecords(2))
```

    ##  [1] "data.id"                          "data.activePlayer"               
    ##  [3] "data.assists"                     "data.firstName"                  
    ##  [5] "data.franchiseId"                 "data.franchiseName"              
    ##  [7] "data.gameTypeId"                  "data.gamesPlayed"                
    ##  [9] "data.goals"                       "data.lastName"                   
    ## [11] "data.mostAssistsGameDates"        "data.mostAssistsOneGame"         
    ## [13] "data.mostAssistsOneSeason"        "data.mostAssistsSeasonIds"       
    ## [15] "data.mostGoalsGameDates"          "data.mostGoalsOneGame"           
    ## [17] "data.mostGoalsOneSeason"          "data.mostGoalsSeasonIds"         
    ## [19] "data.mostPenaltyMinutesOneSeason" "data.mostPenaltyMinutesSeasonIds"
    ## [21] "data.mostPointsGameDates"         "data.mostPointsOneGame"          
    ## [23] "data.mostPointsOneSeason"         "data.mostPointsSeasonIds"        
    ## [25] "data.penaltyMinutes"              "data.playerId"                   
    ## [27] "data.points"                      "data.positionCode"               
    ## [29] "data.rookiePoints"                "data.seasons"                    
    ## [31] "total"

# Contingency Tables

This section covers using the functions above to create contingency
tables of the NHL data. Contingency tables are tables used to show the
frequency of categorical variables.

## Active and Inactive Players in Goalie Records for Select Franchises

Below, the goalie records of select franchises are binded together into
1 dataframe and the 2 categorical variables of interest are:
`ActivePlayer` and `franchiseName`. Based on the contingency table, all
of the selected franchises have more inactive players than active
players.

``` r
library(knitr)
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

The dataframe for this contingency table was created in a similar way to
the table above by binding together the skater records of select
franchises and factoring the `franchiseName` and `positionCode`. Based
on the contingency table of select franchises, the Toronto Maple Leafs
have the most C position players, New York Rangers have the most D, L,
and R position players. It also shows that the New York Rangers and
Edmonton Oilers have significantly less R position players.

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

Numeric summaries are used to summarize quantitative data. The numeric
summaries below include minimum, 1st quartile, median, mean, 3rd
quartile, and maximum.

## Numeric Summary of Select Columns from Skater Records

To create the numeric summaries a function is used to filter the skater
records for the position of interest for a specific franchise. The
columns of interest are: `data.gamesPlayed`, `data.goals`, and
`data.seasons`. A numeric summary is created for each column of data.

``` r
library(dplyr)

SkatersTable <- function(position){
  data <- getSkaterRecords(5) %>% filter(data.positionCode == position) %>% select(data.gamesPlayed, data.goals, data.seasons)
  kable(apply(data, 2, summary), col.names = c("gamesPlayed", "Goals", "Seasons"))
}
```

Here, the L position was entered into the function for the Toronto Maple
Leafs. Some things to point out are the maximum number of goals in this
position for this franchise is 296, on average each player played 116
games, and 2 was the median number of seasons.

``` r
SkatersTable("L")
```

|         | gamesPlayed |     Goals |   Seasons |
| ------- | ----------: | --------: | --------: |
| Min.    |      1.0000 |   0.00000 |  1.000000 |
| 1st Qu. |     10.2500 |   0.00000 |  1.000000 |
| Median  |     54.0000 |   7.00000 |  2.000000 |
| Mean    |    116.6263 |  27.56566 |  2.893939 |
| 3rd Qu. |    164.0000 |  29.00000 |  4.000000 |
| Max.    |    947.0000 | 296.00000 | 14.000000 |

Next, the C position is entered into the function for the same
franchise. Some things to point out are the maximum number of games
played is 1062, the average number of goals was 28, and almost 3 seasons
is the average number of seasons.

``` r
SkatersTable("C")
```

|         | gamesPlayed |     Goals |   Seasons |
| ------- | ----------: | --------: | --------: |
| Min.    |      1.0000 |   0.00000 |  1.000000 |
| 1st Qu. |     15.0000 |   1.00000 |  1.000000 |
| Median  |     52.0000 |   7.00000 |  2.000000 |
| Mean    |    114.0295 |  28.03797 |  2.860759 |
| 3rd Qu. |    145.0000 |  26.00000 |  3.000000 |
| Max.    |   1062.0000 | 420.00000 | 15.000000 |

The D position is entered into the function for the same franchise. Some
things to point out are on average players in this position play around
122 games, 3 is the median number of goals, and 20 is the maximum number
of seasons.

``` r
SkatersTable("D")
```

|         | gamesPlayed |      Goals |   Seasons |
| ------- | ----------: | ---------: | --------: |
| Min.    |      1.0000 |   0.000000 |  1.000000 |
| 1st Qu. |     15.0000 |   0.000000 |  1.000000 |
| Median  |     58.0000 |   3.000000 |  2.000000 |
| Mean    |    122.3322 |   9.681661 |  2.948097 |
| 3rd Qu. |    151.0000 |   9.000000 |  4.000000 |
| Max.    |   1184.0000 | 148.000000 | 20.000000 |

R is the last position to be entered into the function for the same
franchise. Some things to point out are the maximum number of games is
1188, the average number of goals is around 32, and 2 is the median
number of seasons.

``` r
SkatersTable("R")
```

|         | gamesPlayed |     Goals |   Seasons |
| ------- | ----------: | --------: | --------: |
| Min.    |      1.0000 |   0.00000 |  1.000000 |
| 1st Qu. |     17.0000 |   1.00000 |  1.000000 |
| Median  |     70.0000 |   9.00000 |  2.000000 |
| Mean    |    127.0621 |  32.37288 |  3.056497 |
| 3rd Qu. |    172.0000 |  36.00000 |  4.000000 |
| Max.    |   1188.0000 | 332.00000 | 21.000000 |

# Plots

This section covers graphical summaries of the data pulled from the NHL
Franchise.

## Barplot

This is a side-by-side bar plot of the count of inactive and active
players for select franchises. For these select franchises, the New York
Rangers has the largest number of inactive players and all of the
franchises hasve significantly less active players than inactive
players.

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
g + geom_bar(aes(fill = data.activePlayer), position = "dodge") + labs(x = "Franchise Name", fill = "", title = "Inactive and Active Players for Select Franchises") + theme(axis.text.x = element_text(size = 10, angle = 90))
```

![](README_files/figure-gfm/Skater%20bar%20plot-1.png)<!-- -->

## Boxplot

This is a side-by-side boxplot of the losses for select franchises in
the goalie records. All of the franchises have around the same
interquartile ranges and the New York Rangers have some outliers higher
than the other franchises.

``` r
#create dataframe of select franchises from goalie records
GoalieRecords <- rbind(getGoalieRecords(5),getGoalieRecords(10), getGoalieRecords(20), getGoalieRecords(25), getGoalieRecords(15), getGoalieRecords(22))

#create barplot
g <- ggplot(GoalieRecords, aes(x = data.franchiseName, y = data.losses))
g + geom_boxplot() + theme(axis.text.x = element_text(size = 10, angle = 90)) + labs(x = "Franchise Name", y = "Losses", title = "Losses for Select Franchises")
```

![](README_files/figure-gfm/losses%20boxplot-1.png)<!-- -->

## Scatter Plot

``` r
SkaterRecords <- rbind(getSkaterRecords(23), getSkaterRecords(16), getSkaterRecords(3), getSkaterRecords(6))

g <- ggplot(SkaterRecords, aes(x = data.gamesPlayed, y = data.mostGoalsOneSeason))
g + geom_point(aes(color = data.franchiseName)) + labs(x = "Games Played", y = "Most Goals in One Season", color = "Franchise Name", title = "Games Played vs. Most Goals in One Season") + geom_smooth(aes(group = data.franchiseName), method = "lm", color = "black")
```

![](README_files/figure-gfm/scatter%20plot-1.png)<!-- -->
