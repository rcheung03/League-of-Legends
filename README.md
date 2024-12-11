# League-of-Legends-First-Objective-Statistical-Analysis
League of Legends First Objective Statistical Analysis is a data science project at UCSD that explores the impact of early game objectives, such as dragons and heralds on win rates and match outcomes. The project goes through the data science life cycle to uncover patterns linking first objectives to team success.

By: Ryan Cheung

## Introduction
For this project, I used analyzed League of Legends games using a data set from Oracle's Elixir. This data set encapsulates professional match throughout 2024.

Objectives in League of Legends are key strategic points on the map that allow teams that secure them to gain an advantage. Some examples of objectives are the Rift Herald and various Dragons that spawn during the game. The Rift Herald allows the team that defeats it to use it against an enemy turret, assisting with map control.

I want to specifically focus on the impact of securing the first objectives in a game. Does gaining control of the first objectives give one team a decisive advantage? Using this data set, I will find out if there is such impact and build a predictive model that gives the winning team based on these objectives.

The original data set contains 116,064 rows and 161 columns. For my analysis I only selected a few of these columns for focus on.

- `firstdragon`: This is a binary column indicating whether a team secured the first dragon

- `firstherald`: This is a binary column indicating whether a team secured the first herald (there is only one herald per match)

- `firstbaron`: This is a binary column indicating whether a team secured the first baron

- `firsttower`: This is a binary column indicating whether a team secured the first tower

- `teamkills`: This column indicates the total number of kills a team obtained during the match

- `gspd`: gspd (gold spent percent difference) is a measure of economic advantage (gold) over the enemy team

# Data Cleaning and Exploratory Data Analysis

## Cleaning
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

This histogram shows that the most common outcome is obtaining anywhere from 2 to 3 dragons per match. The right-skewed distributions suggests that it is possible, but rare for teams to get 4 or more dragons. One possible reason could be that a team that has secured multiple dragons is ahead enough to win before getting another.


### Bivariate Analysis

Here I performed a bivariate analysis on `firstbaron` and `result` statistics in the dataset.

 <iframe
  src="assets/first_baron_wr.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This pie chart shows the distribution in which teams win after securing the `firstbaron`. The visualization helps illustrate how securing the `firstbaron` correlates with the game outcome. Here you can see that about 84% of teams that secure the `firstbaron` end up winning the game, hilighting the significance of securing the `firstbaron`.

### Interesting Aggregates

|   firstbaron |   result |   teamkills |
|-------------:|---------:|------------:|
|            0 |     1607 |       99864 |
|            1 |     6673 |      152791 |

I groupby `firstbaron` with the cleaned dataset and calculate the sum of the statistics. Here we can see the difference in `result` and `teamkills` based on `firstbaron`.

## Assessment of Missingness

### NMAR Analysis

I believe that the `ban` columns are most likely not missing at random (NMAR). The missingness of the value depends on the value itself becuase players on a team can choose whether to utilize their bans. That means that a missing ban could just be a player choosing not to ban.

### Missingness Dependency

I test the missingness of `firstdragon` dependig on `league` and `result`.

**Null Hypothesis**: Distribution of 'league' when 'firstdragon' is missing is the same as the distribution when it's not missing.

**Alternative Hypothesis**: Distribution of 'league' when 'firstdragon' is missing is NOT the same as the distribution when it's not missing.

 <iframe
  src="assets/misingness.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The plot shows the result of a permutation test perfomed to analyze whether the missingness of `firstdragon` depends on `league`. The large P-value of 0.99 suggests that `firstdragon`'s missingness does not depend on `league`

## Hypothesis Testing

**Null Hypothesis**: Win rate with first dragon = 0.5 (50%)

**Alternative Hypothesis**: Win rate with first dragon > 0.5 (50%)

**Test Statistic**: Difference between observed win rate and 0.5 (50%)

**Significance Level**: 5%

Here is the histogram with the distribution of the test statistic during the hypothesis test:

<iframe
  src="assets/drag_wr.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Using the resulting p-value, 0.00099, I reject the null hypothesis. This suggests that the odds of winning after securing the first dragon is greater than a coinflip (50/50) and is significant. Furthermore, the results suggest that a team that secures the first dragon will win 56.77% of the time.

## Framing a Prediction Problem

In previous sections, I found that first objectives like baron and dragon can significantly impact the result of the game. Is it possible to predict whether a team will win or lose mainly based on first objectives taken by the team?

To answer this question I will use logistic regression since it is a binary classification problem. The response variable is `result`.

I selected a few relevant features for prediction, including both binary features (such as `firstdragon`, `firstherald`) and numerical features (such as `teamkills` and `gspd`). For preprocessing, I apply StandardScaler to the numerical features, while the binary features are passed through without transformation.

I evaluate the model using accuracy, as it gives a simple and straightforward measure of how many predictions the model got correct. This metric is chosen because of the binary nature of the classification task and the balanced nature of the dataset.

## Baseline Model

For my baseline model, I built a binary classificaiton model using logistic regression. It predicts the outcome of the game given certain features. For **quantitative** features, I used `teamkills` and `GSPD`. These two features were standardized using StandardScaler. For **categorical** features, I used `firstdragon` and `firstherald`. Since they were already in binary form, enconding was not needed.

Both the training accuracy and testing accuracy are about 90%, meaning that the model can predict 90% of the data. This suggests that the model has good performance since it has high accuracy on training and testing sets.

## Final Model

In the final model, I added two more first objectives: `firstbaron` and `firsttower`. They are both categorical. I decided to add `firstbaron` because baron grants major buffs that allow the a team to push towards the enemy base. I added `firsttower` because it allows the team that secures it to have more freedom in the map and extra gold. Both of these seem to impact the result of the game.

The final model is also binary classification model that uses logistic regression. No encoding was done to the new features since they are both categorical. For hyperparameter tuning, I focused on the regularization strength (C) and solver type. I tested C values from 0.01 to 100 and tried different solvers. Using grid search, I found the best C value to be 1.0 and the optimal solver to be 'liblinear'. However, the testing and training accuracy of about 90% shows that the final model did not improve from the baseline model.

## Fairness Analysis


For my fairness analysis, I chose early objectives (`firstdragon`, `firstherald`) vs late objectives (`firstbaron`, `firsttower`). These groups were chosen because early objectives can influence match outcomes, and I want to ensure the model predicts both well.

My evaluation metric is precision, as it measures how well the model identifies winning teams. I used a permutation test to compare precision between the two groups.

**Null Hypothesis**: The model's precision for early and late objectives is the same.

**Alternative Hypothesis**: The precision differs between early and late objectives.

With a significance level of 5% and 10,000 trials, the observed precision difference was 0.0012, and the p-value was 0.87. Since the p-value is above 0.05, I fail to reject the null hypothesis, concluding that the model performs similarly for both groups.