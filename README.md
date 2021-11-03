# NBA-predictions

## Overview

This is my final capstone project for the 12-week Data Science course at Lighthouse Labs. 

I created a model that predicts the winner of NBA games, primarily focused on a dataset of 2013-2015 NBA season games (data provided via stats.nba.com, basketball-reference.com, as well as Lighthouse Labs). I wanted to create a model that looked solely at the recent performance of each team, that could be applied to any season and get good results. This proved to be a difficult task (as expected; if predicting sports was easy then everyone would be doing it!).

The primary use for this model is sports-betting, with the optimistic goal of calculating betting odds (which I did not achieve in this current iteration).

`data` contains the excel spreadsheets, as well as some saved models
`archive` contains old versions of code (and some of it is quite messy)

### 1. Data Pre-Processing
*Note:* The code for this section can be found in `pre-processing_moving-average.ipynb`

In this section I clean up the data. Some noteworthy adjustments:
- Charlotte changed their name from the Bobcats to the Hornets in 2014
- LA Clippers appeared as Los Angeles Clippers as well as LA CLippers
- I created a moving average for recent team performance (the most recent *5 games*; an arbitrary number choice I made)
- I OneHot encoded all of the HOME teams and AWAY teams for each game, creating a sparse matrix specifying the matchup of each game.
- I applied a MinMax scalar to all of the numeric statistical columns for 2013-2015 game data (on a scale of 0-1)*
  *Note:* The MinMax scalar was applied to 2013-2014 games on the same scale, and 2015 was scaled on its own, separate scale. If this model were to be expanded to multiple years (or decades) I believe a scalar should be applied to each year individually, to account for recent trends in the sport. This would require each scalar to be saved to decode the values.

### 2. Exploratory Data Analysis
*Note:* The code for this section can be found in `exploratory_data_analysis.ipynb`

I started my data analysis by looking at the previous year's (2012) best NBA teams, and looked for statistical outliers that separated them from other teams. I focused primarily on stat columns correlated with the number of WINS each team had (WINS being the target variable for my model).

The most correlated stat seems to be the PLUS_MINUS; the average +/- for that team, that season. Other important stats seemed to be focused primarily around offensive stats (such as shooting percentages). Weaker correlations can be found in the defensive stats (such as REBounds, BLocKs, STeaLs, etc.)

I decided to run PCA on the DataFrame to see if I could reduce the columns down even further, however the results didn't seem to add much clarity to the model* (*from a visual assessment of the PCA components vs. the explained variance of the features).

*It's worth mentioning that the HOME_TEAM wins **53.6%** in this dataset (2013-2015 games specifically).*

I briefly explore the individual stats of the top 100 players from the 2012 season, but I am currently not using this data (yet) in my model, due to time constraints. I think there is a lot of room for improving the model by incorporating player data (such as injuries, recent performance, "superstar value" (i.e. how many players account for the total points per game for the team), etc.)

### 3. Model Predictions
*Note:* There are multiple notebooks for this section, in order of completion and complexity:
- `logistic_regression_v2.ipynb` : Sklearn's Simple Logistic Regression Models (surprisingly accurate)
- `XGBoost_regression.ipynb` : Experimental Regression model that rounds the prediction to try and classify WIN or LOSS, very quick to train
- `random_forest_classifier.ipynb` : Sklearn's RandomForestClassifier models
- `XGBoost_v2.ipynb` : XGBoostRandomForestClassifier models 
- **Most Accurate** `neural_network.ipynb` : Sequential Neural Network which focuses strictly on the 5 game moving average stats
- **Most Accurate** `neural_network_feature_exploration.ipynb` : Sequential Neural Network which uses the team matchup sparse matrix as well

The `logistic_regression_v2.ipynb` and `XGBoost_v2.ipynb` notebooks contain multiple models where I focus on different features, and review the overall performance of each metric, attempting to fine-tune the results.

### 4. Results
The results were somewhat consistent across all of the models, but the general trend is outlined as follows:
- The "ignorant" baseline model (simply calculated based off of the number of HOME_WINS / total games) which predicts HOME_WIN every time would be **54% accurate**
- The baseline model which looks at the MATCHUP matrix (HOME and AWAY teams ONLY) achieved a **63% accuracy** (obvious room for error as teams change their lineup and players move around)
- The moving average model with the MATCHUP matrix improves this accuracy to roughly **63.5-64%** by incorporating 5-game moving average stats for each team
- The revised moving average model with the MATCHUP matrix improves this accuracy to roughly **64-64.5%** by fine-tuning some of the features
- The Neural Network model - which only looks at the revised moving average stats, achieved a **65.2%** accuracy for the entire 2015 season
- The Neural Network model which includes the sparse team matchup matrix achieves a **64.6%** accuracy for the entire 2015 season.

### 5. Next Steps
This was a very fun project but there are so many more things I want to incorporate. As mentioned above, I want to look at the top players for each team, how often each team has played in the last X days, injury reports for each team, coaching changes, the possibilities are endless.

I also wanted to create a website where users could go and select two teams to predict the winner, where the model would send API requests for the teams selected and generate the most recent and up-to-date information to allow for accurate prediction. I was very optimistic about what I could achieve in the ~10 days I had.

### 6. Conclusion
I think it's important to remember (I've had to remind myself this multiple times) that predicting sports games is not an easy task. There are huge upsets every year and these are the games that bring sporting events to life. 
