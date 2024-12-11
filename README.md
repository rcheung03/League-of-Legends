# League-of-Legends-First-Objective-Statistical-Analysis
League of Legends First Objective Statistical Analysis is a data science project at UCSD that explores the impact of early game objectives, such as dragons and heralds on win rates and match outcomes. The project goes through the data science life cycle to uncover patterns linking first objectives to team success.

By: Ryan Cheung

## Introduction
For this project, I used analyzed League of Legends games using a data set from Oracle's Elixir. This data set encapsulates professional match throughout 2024.

Objectives in League of Legends, are key strategic points on the map that allow teams that secure them to gain an advantage. Some examples of objectives are the Rift Herald and various Dragons that spawn during the game. The Rift Herald allows the team that defeats it to use it against an enemy turret, assisting with map control.

I want to specifically focus on the impact of securing the first objectives in a game. Does gaining control of the first objectives give one team a decisive advantage? Using this data set, I will find out if there is such impact and build a predictive model that gives the winning team based on these objectives.

The original data set contains 116,064 rows and 161 columns. For my analysis I only selected a few of these columns for focus on.

- `firstdragon`: This is a binary column indicating whether a team secured the first dragon

- `firstherald`: This is a binary column indicating whether a team secured the first herald (there is only one herald per match)

- `firstbaron`: This is a binary column indicating whether a team secured the first baron

- `firsttower`: This is a binary column indicating whether a team secured the first tower

- `teamkills`: This column indicates the total number of kills a team obtained during the match

- `gspd`: gspd (gold spent percent difference) is a measure of economic advantage (gold) over the enemy team

# Data Cleaning and Exploratory Data Analysis

## Cleaing
 I drop columns that I do not need and keep only the games that have complete data. The data that is kept will be used for my hypothesis test and prediction model.

 Below is the head of the cleaned dataframe.

|          |   firstdragon |   firstherald |   firstbaron |   result |   elementaldrakes |    gspd  |   firsttower |   teamkills |
|:---------|--------------:|--------------:|-------------:|---------:|------------------:|--------:|-------------:|------------:|
| 190      |           1.0 |           1.0 |          1.0 |        1 |               2.0 |  0.166  |          1.0 |          20 |
| 191      |           0.0 |           0.0 |          0.0 |        0 |               1.0 | -0.166  |          0.0 |           7 |
| 202      |           0.0 |           0.0 |          0.0 |        1 |               2.0 | -0.0045 |          0.0 |          31 |
| 203      |           1.0 |           1.0 |          1.0 |        0 |               3.0 |  0.0045 |          1.0 |          20 |
| 214      |           1.0 |           1.0 |          1.0 |        1 |               2.0 |  0.0858 |          0.0 |          24 |




 ### Univariate Analysis
 
 Here I perform a univariate analysis to see the distribution of dragons taken per game.

 <iframe
  src="assets/drag_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This histogram shows that the most common outcome is obtaining anywhere from 2 to 3 dragons per match. The right-skewed distributions suggests that it is possible, but rare to teams to get 4 or more dragons. One possible reason could be that a team that has secured multiple dragons is ahead enough to win before getting another.


### Bivariate Analysis

Here I performed a bivariate analysis on first baron and result statistics in the dataset.

 <iframe
  src="assets/first_baron_wr.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This pie chart shows the distribution in which teams win after securing the first baron. The visualization helps illustrate how securing the first baron correlates with the game outcome. Here you can see that about 84% of teams that secure the first baron end up winning the game, hilighting the significance of securing the first baron.

### Interesting Aggregates

|   firstbaron |   result |   teamkills |
|-------------:|---------:|------------:|
|            0 |     1607 |       99864 |
|            1 |     6673 |      152791 |

I groupby first baron with the cleaned dataset and calculate the sum of the statistics. Here we can see the difference in wins and team kills based on first baron.

## Assessment of Missingness

### NMAR Analysis

I believe that the `ban` columns are most likely not missing at random (NMAR). The missingness of the value depends on the value itself becuase players on a team can choose whether to utilize their bans. That means that a missing ban could just be a player choosing not to ban.

### Missingness Dependency

I test the missingness of firstdragon dependig on league and result.

**Null Hypothesis**: Distribution of 'league' when 'firstdragon' is missing is the same as the distribution when it's not missing.

**Alternative Hypothesis**: Distribution of 'league' when 'firstdragon' is missing is NOT the same as the distribution when it's not missing.

 <iframe
  src="assets/missingness.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The plot shows the result of a permutation test perfomed to analyze whether the missingness of `firstdragon` depends on the result. The large P-value of 0.99 suggests that `firstdragon`'s missingness does not depend on the game result.

## Hypothesis Testing

**Null Hypothesis**: Null Hypothesis: Win rate with first dragon = 0.5 (50%)

**Alternative Hypothesis**: Alternative Hypothesis: Win rate with first dragon > 0.5 (50%)

**Test Statistic**: Difference between observed win rate and 0.5 (50%)

**Significance Level**: 5%

Here is the histogram with the distribution of the test statistic during the hypothesis test:

<iframe
  src="assets/drag_wr.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Using the resulting P-value, 0.00099, I reject the null hypothesis. This suggests that the odds of winning after securing the first dragon is greater than a coinflip (50/50). Furthermore, the results suggest that a team that secures the first dragon will win 56.77% of the time.

## Framing a Prediction Problem