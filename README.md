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
*"$"* and *","*. This can be simply done with the .replace() method

![remove_special](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/replace_special.png)

2. After this, all salaries can be converted to a numeric data type with the *pd.to_numeric* function, applies to all columns except for the only non-numeric one, the player_names. We use the *dtypes* attribute to confirm this data type transformation.

![to_numeric](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/convert_numeric.png)

##### Player Performance Data

1. It's not surprising that players can play be traded and thus play for multiple teams in one season. For example in the 1999-2000 season, Tariq-Abdul Wahad played for Orlando then was traded to Denver. Players whom played on more than one team per season are listed with an additional row with a Tm designation as TOT or Total.

![tot_team_clean](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/tot_team_clean%20-%20Copy.png)

The TOT row is an summation of that player's stats. However, TOT is not a real team so we will need to grab the name of the player's most recent team.

The function below takes in a single dataframe and returns the record if there is only one row. If there are multiple rows for a player in a single season, it will take the total or TOT and replace the Team(Tm) with the most current team.

![team_fxn](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/team_fxn.png)


2. Some players have an asterisk next to their name, possibly as a designation for other features. We will need to remove this so we can merge with the salary data.

![replace_asterisk](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/replace_asterisk.png)


#### Merge Player Stats and Salary DataFrames

1. First, we use the *loc()* function to extract rows for the 2019-2020 season. We will merge this partitioned dataset with the salary data from HoopsHype. After we removed the aterisk from the player stats, we can merge on player names. We want to merge *left* to keep all the player stats to calculate our Approximate Value metric.

![merge_function](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/merge_dfs.png)

This new dataframe, *merged_df_20*, contains the full stats for each player plus their current and projected salary data for future seasons.

![merged_20](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/merged_20_df.png)


## Data Exploration and Analysis

#### Finding the Approximate Value & Pay-Per-Minute

1. **Approximate Value** - As previously mentioned, I approximate a player's on-court performance with the *Approximate Value (AV)* metric, developed by Dean Oliver. The formula can be found [here](https://www.nbastuffer.com/analytics101/approximate-value/).

2. **Pay per Minute Played** - Simply divide the salary for the year by the actual total of minutes played

![av_metric](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/av_formula.png)

#### Plot correlations

1. The following scatter plot shows a right skewed or positive distribution where most values are clustered around the left tail of the distribution while the right tail of the distribution is longer.

![scatter_plt](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/scatter_plt_20.png)


We can use the *describe()* method over the *$_per_min-19/20* column for further details on the distribution. Over 75% of the data points are less than or equal to $15k, with the mean at $14k.

![describe](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/describe_method.png)

This information indicates the presence of outliers, such as players whose salary per minute is too high given the low amount of minutes played. Scenarios usually include injuries and/or vaccine requirement to play during the NBA bubble season.

Luckily, we can use this data to set a salary per minute threshold to get a better sense of the distribution. The plot below shows the 75th percentile <= $15k per minute.

![scatter_plt_2](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/scatter_plt_20_2.png)

#### Player Clustering

1. Using the mean values, via the *.mean()* method, I can split the data into quadrants relative to the mean value for Approximate Value and Salary Per Minute.

![new_scatter_function](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/new_scatter_fxn.png)

We can do this with the *plt.axvline* and *plt.axhline* methods to draw vertical and horizontal lines, respectively, to identify the quadrants relative to the averages.

Players with above-average AV value and below-average $ per minute are located in the upper left quadrant of the resulting graph below.

![new_scatter](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/new_scatter.png)


2. Use Iplot - Iplot stands for interactive plot. Plotly allows you to generate graphs offline and save them in local machine. The plotly.offline.plot() function creates a standalone HTML that is saved locally and opened inside your web browser. Use plotly.offline.iplot() when working offline in a Jupyter Notebook to display the plot in the notebook. More info [here](https://www.tutorialspoint.com/plotly/plotly_online_and_offline_plotting.htm).

In the Jupyter notebook, you can hover over each point to access the player names with their respective salary per minute and Approximate Value metric. Feel free to dig in!

![iplot_graph](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/iplot_scatter.png)

The players in the upper left quadrant represent a potential opportunity to invest in proven on-court performance with a *below-average* salary relative to the league.

#### Generate a list of free-agents with high AV and below-average $ per minute

1. We will use the original dataset *merged_20_df* to slice and remove any outliers. Essentially, we will use the same formula for the scatter plot but will not plot it.

2. Secondly, I will store the values for on-court performance and salary metrics, and filters the data to only keep above-average performers with below-average pay in per minute.

![high_roi](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/high_roi.png)

![high_roi_filtered](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/high_roi_filtered.png)

3. Lastly, I want to consider only upcoming free-agents. I can do so by filtering salary for 2020/21 season to equal 0.

![filter_agents](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/filter_agents.png)


**Voila!** Below are the top 10 upcoming free agents sorted by Approximate Value. NBA Front office might be interested in these players, given they have the cap space to do so!

![free_agent_list](https://github.com/aclao89/NBA_Salaries_Performance/blob/main/Images/free_agent_list.png)

## Considerations

If you are an avid basketball fan, you should see that all these players serve as quality, rotational players for their respective teams.

The best player on the list, Serge Ibaka, won an NBA Champion Title with the Toronto Raptors back in the 2018-2019 season. Sounds like he might be underpaid for his level of production on the court but we will need more advance stats to make a sound judgement.

We can expect these players to be in the conversation of contract negotiations with their own team or other teams who want a quality, role player with a below-average $ per minute of the league.
