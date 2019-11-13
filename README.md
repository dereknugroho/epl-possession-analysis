# EPL Possession Analysis

In professional football (soccer), is there a relationship between a team's possession of the ball and their match outcomes?

## Overview

I have analyzed all of the matches played in the [English Premier League (EPL)](https://www.premierleague.com/) from the 2006/07 season to the 2018/19 season.

2006/07 is the earliest season in which possession figures are available on the EPL website.

## Methodology

I defined a match outcome as the number of points earned by a given team in a given match. As per the [official rules](https://www.premierleague.com/premier-league-explained), the outcome of a match is based on the number of goals scored by each team:
```
---------------------------------------------------------------------------------------------------
| GOALS SCORED                                     | OUTCOME                                      |
---------------------------------------------------------------------------------------------------
| Team A scores more goals than Team B             | Team A earns 3 points; Team B earns 0 points |
---------------------------------------------------------------------------------------------------
| Team B scores more goals than Team A             | Team A earns 0 points; Team B earns 3 points |
---------------------------------------------------------------------------------------------------
| Team A and Team B score the same number of goals | Team A earns 1 point;  Team B earns 1 point  |
---------------------------------------------------------------------------------------------------
```

Each of the 20 teams in the English Premier League begin each season with zero points. The team that accumulates the most points by the end of the season is the champion. (There are no playoffs.)

For each team, I have plotted the following:
- Independent variable: The given team's average ball possession percentage per match (across every match played in every season)
- Dependent variable: The given team's average points earned per match (across every match played in every season)

Each data point represents a single team.

I generated regression curves using three separate tools: Python, R, and Google Sheets. The estimated regression coefficients were consistent across all three tools.

## Scraping the data

If you _don't_ want to scrape the data, skip to the next section. The data is already contained in the following file:
```
epl_data.csv
```

If you _do_ want to scrape the data, you will need to have [Selenium WebDriver](https://www.seleniumhq.org) for Python installed. 

The script below will create a csv file containing data on each match played.
```
scrape.py
```

Data is populated by fetching specific values from each match played in the English Premier League ([example](https://www.premierleague.com/match/38687)) and appending it to the csv file.

As a result of explicit waits, this script will take at least 7.5 hours to finish executing. During this period of time, there will be a repeating interval of approximately 6 seconds during which a single Chrome instance will launch and then quit. The duration of the interval (and thus the total time required to finish executing the entire script) may be longer depending on your hardware and the strength of your internet connectivity.

scrape.py is not going to stop running until one of the following events occur:
  1. The script has successfully completed execution
  2. The script is manually interrupted during execution
  3. The script is automatically interrupted due to persistent network errors

**In order to ensure data purity, the csv file will always be deleted and re-created each time the script is executed. It is therefore advised to avoid interrupting scrape.py during execution.**

Once you are ready, run the following command in the cloned repo:
```
$> python3 scrape.py
```

This is going to create a csv file named epl_data.csv that contains the data to be analyzed. These will be the column labels:
```
--------------------------------------------------------------
| Column Label | Meaning                                     |
--------------------------------------------------------------
| name_home    | The name of the home team                   |
--------------------------------------------------------------
| pp_home      | The possession percentage for the home team |
--------------------------------------------------------------
| gs_home      | The goals scored by the home team           |
--------------------------------------------------------------
| pe_home      | The points earned by the home team          |
--------------------------------------------------------------
| name_away    | The name of the away team                   |
--------------------------------------------------------------
| pp_away      | The possession percentage for the away team |
--------------------------------------------------------------
| gs_away      | The goals scored by the away team           |
--------------------------------------------------------------
| pe_away      | The points earned by the away team          |
--------------------------------------------------------------
```

## Analyzing the data

In order to view the regression curve generated by Python, run this command:
```
$> python3 generate_visuals.py
```

...or open this file:
```
img/regression_curve_python.png
```

In order to view the regression curve generated by R, use [RStudio](https://rstudio.com) to run this R script:
```
generate_visuals.R
```

...or open this file:
```
img/regression_curve_R.png
```

In order to view the Google Sheets calculations, open this file:
```
calculations_GS.xlsx
```

In order to view the regression curve generated by Google Sheets, open this file:
```
img/regression_curve_GS.png
```

## Things to note

- It is possible that at some point between the 2006/07 season and the 2018/19 season, the English Premier League changed its methodology for calculating possession. If there was a change in methodology, the change in input would be unlikely to have a statistically significant impact. Additionally, if there _was_ a change, I wasn't able to find an announcement on their website. You can calculate the approximate possession percentage for a team in a given match as follows:
  ```
  Possession percentage for Team A = Passes completed by Team A / (Passes completed by Team A + Passes completed by Team B)
  ```

- There are 20 teams that play in a given season. Each data point represents a single team. However, there are 39 total data points. This is because the English Premier League relegates the 3 teams with the fewest points down to the [2nd tier](https://www.efl.com) for the following season. The 3 teams from the 2nd tier that earned the most points in the 2nd tier are promoted up to the English Premier League to replace the 3 teams that were relegated. Some teams have never been relegated. Some teams have been relegated but promoted the following season. See [here](https://www.premierleague.com/premier-league-explained) for the official rules.

- If you want to play around with the raw data in a database, import this file to [sqlite3](https://www.sqlite.org/index.html) (recommended):
  ```
  sqlite_epl_data.db
  ```

## To do

- [x] Analyze on a fully-aggregated basis (one plot, 39 data points)
- [ ] Analyze on a per-season basis (13 plots, 20 data points per plot)
- [ ] Analyze on a per-team basis (39 plots, variable number of data points per plot)
