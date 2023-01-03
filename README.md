# Relationship between an NBA Player's Salary to Respective Performance
### Finding above average performers with below average salary

![header_picture](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/salary_nba-768x384.jpg)

## Introduction

This project explores the correlation between NBA player salaries and actual on-court performance from both 2019/2020 and 2020/2021 seasons.

The purpose is to identify above average performers with below average salary (in per minute terms) whom are about to free agents in the subsequent off-season. This analysis provides insights and opportunities to add quality rotational players to compete for a NBA Finals run.

For performance, we will use a metric called [Approximate Value (AV)](https://www.nbastuffer.com/analytics101/approximate-value/), developed by Dean Oliver. AV estimates a player's values, making no fine distinctions, but, rather, distinguishing easily between very good seasons, average seasons, and poor seasons.

Bonus: We can also identify which players are overpaid and underpaid based on salary and performance.

## Data Sources

1. [Basketball Reference](https://www.basketball-reference.com/leagues/NBA_2020_totals.html) - NBA Player Stats
2. [HoopsHype](https://hoopshype.com/salaries/players/) - NBA Player Salary

## Tools Used

1. Jupyter Notebook
2. Tableau **(Future work)**

## Scraping Player Stats from 2019/2020 & 2020/2021 Seasons

#### Step 1: Import Python Packages
1. For web scraping purposes, we will need requests and BeautifulSoup. Pandas and Numpy are popular packages for data manipulation and analysis. Matplotlib, plotly, chart_studio, and cufflinks are purposed for charts and visualizations.

![python_packages](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/python_packages.png)


##### Extract the table element from Basketball Reference page
2. We use a *with* statement to open the 2020 Season stored as an html file. We will then parse the page with BeautifulSoup and locate the dataset by pointing to the html table tag *all_totals_stats* via the soup.find() method. Lastly, we have to cast the html table to a string and used the read_html() function to convert into a Pandas DataFrame.

![extract_bballref](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/web_scrape_2020.png)

##### Bonus: Use a *for* loop to extract the last 22 NBA Seasons, 2000 - 2022.
3. We can use the similar *with* statement above but nested in a *for* loop. We added a "Year" column for distinction purposes and further analysis.

![web_scrape_00_22 ](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/web_scrape_00_22.png)

#### Step 2: Data Cleaning

##### Player Salary Data

1. Remove special characters from salary - In order to convert to numeric, we need to remove
"$" and ",". This can be simply done with the .replace() method

![remove_special](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/replace_special.png)

##### Player Performance Data

1. It's not surprising that players can play be traded and thus play for multiple teams in one season. For example in the 1999-2000 season, Tariq-Abdul Wahad played for Orlando then was traded to Denver. Players whom played on more than one team per season are listed with an additional row with a Tm designation as TOT or Total.

![tot_team_clean](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/tot_team_clean%20-%20Copy.png)

The TOT row is an summation of that player's stats. However, TOT is not a real team so we will need to grab the name of the player's most recent team.

The function below takes in a single dataframe and returns the record if there is only one row.

If there are multiple rows for a player in a single season, it will take the total or TOT and replace the Team(Tm) with the most current team.

![team_fxn](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/team_fxn.png)
