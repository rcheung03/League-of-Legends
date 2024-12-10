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