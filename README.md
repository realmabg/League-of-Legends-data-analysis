# League of Legends Vision Score Data Analysis

Authors: Adrian Kong & Borngreat Omoma-Edosa

## Introduction

League of Legends is a popular multiplayer online battle arena (MOBA) game developed by Riot Games. Vision control plays a crucial role in securing strategic advantages, allowing teams to track enemy movements, contest objectives, and avoid ambushes. The game's vision score quantifies a team's overall contribution to vision through ward placement, clearing enemy wards, and revealing unseen areas of the map.

Our dataset, developed by Oracle’s Elixir, contains 1,013,856 rows of match data from professional League of Legends esports games through 2025. This dataset includes a wide range of statistics, including individual player performance, team-level metrics, and match outcomes. While vision score is often assumed to be a key factor in winning games, its relationship with performance may not be so straightforward. If teams consistently have higher vision scores when losing, it could indicate that vision control is more of a reactionary tool rather than a predictor of success.

This project explores the question: How much does a team's vision score reflect its overall performance?

Understanding this relationship can help players and analysts assess whether vision score is a reliable indicator of game state ogrear a symptom of a team trying to recover from a disadvantage. If teams place and clear more wards when they are losing, it may suggest that vision control is a byproduct of needing to catch up rather than a direct contributor to winning.

The key columns relevant to this analysis include:

`gameid`: Unique identifier for each game.
`side`: Whether the team played on the blue or red side.
`assists`: The number of assists a team accumulated.
`result`: Whether the team won (1) or lost (0) the game.
`wardsplaced`: The total number of wards a team placed during the game.
`wpm` (Wards per Minute): The rate at which a team placed wards.
`wardskilled`: The number of enemy wards cleared.
`wcpm` (Wards Cleared per Minute): The rate at which a team cleared enemy wards.
`kills`: The total number of kills a team secured.
`controlwardsbought`: The number of control wards purchased.
`visionscore`: A score reflecting a team’s vision contribution, considering wards placed, cleared, and revealed.
`vspm` (Vision Score per Minute): Vision score adjusted for game length.
`position`: The role of the player (Top, Jungle, Mid, Bot, Support), or denoting if a row correlated to a team's statistics in a game.
`gamelength`: The duration of the match.
`year`: The year in which the game took place.
`url`: A link to the source page for the game’s statistics.
`league`: The professional league in which the game occurred.
`datacompleteness`: A boolean value indicating whether all data points for a match are available


## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

We are trying to draw insight from a team's vision score, so we chose to keep the following relevant columns: `gameid`, `side`, `assists`, `result` `wardsplaced`, `wpm`, `wardskilled`, `wcpm`, `kills`, `controlwardsbought`, `visionscore`, `vspm`, `position`, `gamelength`, `year`, `url`, `league`, and `datacompleteness`.

For each game that was played, there were 12 rows: 10 rows for each player (5 players on one team) as well as a 2 rows for each team. We kept only the two rows for each team per game, as denoted by `["position"]=="team"` .

We also had to ensure that we only included valid games, as some were started but had to be restarted. As such, we excluded any games were the column `visionscore` was were missing or was 0. 0 is not a realistic vision score as at least one person on a team will place a ward in a useful area in a full game.

There were also two new columns that we made ourselves: `more_vision` and `more_kills`. These were made by looking at each game and comparing the `visionscore` and `kills` between the two team's rows. If team A had higher `kills` than team B, then team A would have True in the `more_kills` column and team B would have False, and vice versa. We repeated this process for the `more_vision`, using the teams' `visionscore` as the metric instead.

Along with this, we also made `result` a boolean value, with a 1 (win) becoming True and 0 (a loss) being false.

### Univariate Analysis

We performed univariate analysis on both `kills` and `visionscore`.

<iframe
  src="assets/univariate_graph_team_kills.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This histogram shows that the distribution of each team's kills is roughly normal, with a slight right skew. This means that while most teams tend to have a similar number of kills, there are a few outliers with higher kill counts. The right skew could mean that a small number of teams are getting significantly higher kills, which is common when a team is completely winning or stronger in a game.

We also did univariate analysis on `visionscore`.

<iframe
  src="assets/univariate_graph_visionscore.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Similarly, this histogram shows that the distribution of each team's vision score is roughly normal, with a slight right skew. Again, this means that most teams have a similar vision score with a few higher value outliers. This could come from when teams have an advantage in their game, allowing them to have more time and area to place their wards for better vision.

### Bivariate Analysis

One proposed reason for having higher vision score was because a winning team has more time and area, as stated above. We tested this by performing bivariate analysis on the `more_vision` and `result` statistics in the dataset to see what proportion of teams with a higher `visionscore` than their opponents won their games.

<iframe
  src="assets/bivariate_result_vision.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The plot shows that around 78% of the time, the team that has a higher `visionscore` won their game. This implies that having a higher `visionscore` has some sort of positive impact and competitive edge that can lead to a winning advantage. 

### Interesting aggregates



| more_vision   |     assists |   result |   wardsplaced |    wpm |   wardskilled |    wcpm |
|:--------------|------------:|---------:|--------------:|-------:|--------------:|--------:|
| False         | 1.82566e+06 |    17850 |   7.28174e+06 | 223902 |   3.11974e+06 | 94863.8 |
| True          | 2.63055e+06 |    51782 |   6.97768e+06 | 215222 |   3.11964e+06 | 95477   |

| more_vision   |            kills |   controlwardsbought |   visionscore |   vspm |   more_kills |
|:--------------|-----------------:|---------------------:|--------------:|-------:|-------------:|
| False         | 812218           |          2.73374e+06 |   1.6193e+07  | 496395 |        16095 |
| True          |      1.15419e+06 |          2.55877e+06 |   1.70621e+07 | 525778 |        47357 |


We used `more_vision` to groupby the dataset (As a reminder, True means the team had a higher vision score than their opponent). While we see that the team with a higher vision score did end up winning more and having more kills, what is interesting is that they actually placed less wards and killed less wards. One possible reason for this may be because a losing team felt that they had to keep placing wards in order to get back into game and they were able to find more wards due to their opponents placing wards closer to their base.


## Assignment of Missingness

### NMAR Analysis

We think that the column `url` is Not Missing at Random (NMAR). There are many reasons why a game will not have a url linked to their game; there is not infrastructure for it, the league is not popular enough, etc. 

### Missingness Dependency

We are going to test if the `url` being missing depends on other columns. The two columns that we chose are `visionscore` and `year`. 

First, we did a permutation test on `url` and `year`

Null Hypothesis: The distribution of `year` when `url` is missing is the same as the distribution of `year` when `url` is not missing.

Alternative Hypothesis: The distribution of `year` when `url` is missing is NOT same as the distribution of `year` when `url` is not missing.

To test this, we chose a significance level of 0.05 and used Total Variation Distance (TVD) as our test statistic.


|   year |   url_missing = False |   url_missing = True |
|-------:|----------------------:|---------------------:|
|   2017 |               0.05355 |              0       |
|   2018 |               0.13197 |              0.0002  |
|   2019 |               0.16778 |              0.00318 |
|   2020 |               0.23576 |              0       |
|   2021 |               0.27451 |              0.00478 |
|   2022 |               0.05453 |              0.34432 |
|   2023 |               0.04427 |              0.30901 |
|   2024 |               0.03455 |              0.28155 |
|   2025 |               0.00307 |              0.05695 |


Here is a bar graph for easier observations:

<iframe
  src="assets/observed_bar_graph.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Our observed statistic using TVD is 0.855. After we performed our permutation tests, we found the p-value to be 0.

Below is the empirical distribution of the TVD for the test.

<iframe
  src="assets/url_year_graph.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Here is a zoom in on the graph of the TVDs we found in our permutations.

<iframe
  src="assets/zoomed_in_url_year_graph.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Since the p-value is less than the 0.05 significance level, we reject the null hypothesis. We conclude that the missingness of `url` depends on the `year` column.


Next, we did a permutation test on `url` and the `more_vision` column we created earlier.


Null Hypothesis: The distribution of `more_vision` when `url` is missing is the same as the distribution of `more_vision` when `url` is not missing.

Alternative Hypothesis: The distribution of `more_vision` when `url` is missing is NOT same as the distribution of `more_vision` when `url` is not missing.

Again, we chose a significance level of 0.05 and used Total Variation Distance (TVD) as our test statistic.


| more_vision   |   url_missing = False |   url_missing = True |
|:--------------|----------------------:|---------------------:|
| False         |              0.525808 |             0.524212 |
| True          |              0.474192 |             0.475788 |


<iframe
  src="assets/observed_bar_graph2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Our observed statistic using TVD is 0.0016. After we performed our permutation tests, we found the p-value to be 0.545

Below is the empirical distribution of the TVD for the test.

<iframe
  src="assets/url_more_vision_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
Since the p-value is bigger than the 0.05 significance level, we fail to reject the null hypothesis. We conclude that the missingness of `url` does not depend on the `year` column.

## Hypothesis Testing

In this hypothesis test, we look to see if there is a significant difference in the distribution of kills for a team with a higher vision score and the team that has the lower vision score. This is important because we want to see if having a higher vision score results in some sort of strategic and competitive advantage for a team in a League of Legends match, and the ultimate advantage results in a win.

Null Hypothesis: The distribution of kills for a team with the higher vision score in a game is the same as the team that has the lower vision score.

Alternate Hypothesis: The distribution of kills for the team with the higher vision score is NOT the same as the team that has the lower vision score.

We are going to use absolute mean difference between the kills in teams with the higher vision score and kills in teams with a lower vision score as the test statistic. We will also use a significance level of 0.05.

Our observed test statistic was 0.415.

Here is a histogram containing the distribution of our test statistics along with the observed statistic:

<iframe
  src="assets/hypothesis_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Here is a zoom in on the graph of the absolute difference in means we found in our permutations:

<iframe
  src="assets/hypothesis_distribution_zoom_in.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

After we performed our permutation tests, we found the p-value to be 0.

Since the p-value is smaller than the 0.05 significance level, we reject the null hypothesis. There is statistical evidence to suggest that the distribution of kills for the team with the higher vision score is NOT the same as the team with the lower vision score. This finding tekks us to consider that having a higher vision score than the other team can result in an advantage in a League of Legends game, and ultimately winning.

## Framing a Prediction Problem
Can we accurately predict a team's vision score based solely on their in-game performance statistics?

For our prediction model, we will perform necessary preprocessing steps such as dropping non-informative or metadata columns like gameid and url. This ensures that our model leverages only the relevant in-game statistics.

To address this question, we will frame the problem as a regression task where the vision score is treated as a continuous variable. Our dataset includes the following columns:
assists, result, wardsplaced, wpm, wardskilled, wcpm, kills, controlwardsbought, visionscore, gamelength, more_kills, and more_vision.
Below is the head of DataFrame we are using in this section:
cBought = controlwardsbought
wPlaced = wardskilled
wSkilled = wardsplaced
mVision = more_vision
mKills = more_kills

<div style="text-align: center;">
  <table style="margin: 0 auto; border-collapse: collapse;">
    <thead>
      <tr>
        <th style="border: 1px solid #ccc; padding: 4px;">index</th>
        <th style="border: 1px solid #ccc; padding: 4px;">assists</th>
        <th style="border: 1px solid #ccc; padding: 4px;">result</th>
        <th style="border: 1px solid #ccc; padding: 4px;">wPlaced</th>
        <th style="border: 1px solid #ccc; padding: 4px;">wpm</th>
        <th style="border: 1px solid #ccc; padding: 4px;">wSkilled</th>
        <th style="border: 1px solid #ccc; padding: 4px;">wcpm</th>
        <th style="border: 1px solid #ccc; padding: 4px;">kills</th>
        <th style="border: 1px solid #ccc; padding: 4px;">cBought</th>
        <th style="border: 1px solid #ccc; padding: 4px;">visionscore</th>
        <th style="border: 1px solid #ccc; padding: 4px;">gamelength</th>
        <th style="border: 1px solid #ccc; padding: 4px;">mVision</th>
        <th style="border: 1px solid #ccc; padding: 4px;">mKills</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #ccc; padding: 4px;">32650</td>
        <td style="border: 1px solid #ccc; padding: 4px;">60</td>
        <td style="border: 1px solid #ccc; padding: 4px;">True</td>
        <td style="border: 1px solid #ccc; padding: 4px;">151.0</td>
        <td style="border: 1px solid #ccc; padding: 4px;">3.7956</td>
        <td style="border: 1px solid #ccc; padding: 4px;">47.0</td>
        <td style="border: 1px solid #ccc; padding: 4px;">1.1814</td>
        <td style="border: 1px solid #ccc; padding: 4px;">21</td>
        <td style="border: 1px solid #ccc; padding: 4px;">31.0</td>
        <td style="border: 1px solid #ccc; padding: 4px;">304.0</td>
        <td style="border: 1px solid #ccc; padding: 4px;">2387</td>
        <td style="border: 1px solid #ccc; padding: 4px;">False</td>
        <td style="border: 1px solid #ccc; padding: 4px;">True</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc; padding: 4px;">32651</td>
        <td style="border: 1px solid #ccc; padding: 4px;">23</td>
        <td style="border: 1px solid #ccc; padding: 4px;">False</td>
        <td style="border: 1px solid #ccc; padding: 4px;">139.0</td>
        <td style="border: 1px solid #ccc; padding: 4px;">3.4939</td>
        <td style="border: 1px solid #ccc; padding: 4px;">57.0</td>
        <td style="border: 1px solid #ccc; padding: 4px;">1.4328</td>
        <td style="border: 1px solid #ccc; padding: 4px;">13</td>
        <td style="border: 1px solid #ccc; padding: 4px;">35.0</td>
        <td style="border: 1px solid #ccc; padding: 4px;">359.0</td>
        <td style="border: 1px solid #ccc; padding: 4px;">2387</td>
        <td style="border: 1px solid #ccc; padding: 4px;">True</td>
        <td style="border: 1px solid #ccc; padding: 4px;">False</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc; padding: 4px;">32662</td>
        <td style="border: 1px solid #ccc; padding: 4px;">11</td>
        <td style="border: 1px solid #ccc; padding: 4px;">False</td>
        <td style="border: 1px solid #ccc; padding: 4px;">99.0</td>
        <td style="border: 1px solid #ccc; padding: 4px;">3.3712</td>
        <td style="border: 1px solid #ccc; padding: 4px;">25.0</td>
        <td style="border: 1px solid #ccc; padding: 4px;">0.8513</td>
        <td style="border: 1px solid #ccc; padding: 4px;">9</td>
        <td style="border: 1px solid #ccc; padding: 4px;">23.0</td>
        <td style="border: 1px solid #ccc; padding: 4px;">188.0</td>
        <td style="border: 1px solid #ccc; padding: 4px;">1762</td>
        <td style="border: 1px solid #ccc; padding: 4px;">False</td>
        <td style="border: 1px solid #ccc; padding: 4px;">False</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc; padding: 4px;">32663</td>
        <td style="border: 1px solid #ccc; padding: 4px;">53</td>
        <td style="border: 1px solid #ccc; padding: 4px;">True</td>
        <td style="border: 1px solid #ccc; padding: 4px;">117.0</td>
        <td style="border: 1px solid #ccc; padding: 4px;">3.9841</td>
        <td style="border: 1px solid #ccc; padding: 4px;">31.0</td>
        <td style="border: 1px solid #ccc; padding: 4px;">1.0556</td>
        <td style="border: 1px solid #ccc; padding: 4px;">24</td>
        <td style="border: 1px solid #ccc; padding: 4px;">29.0</td>
        <td style="border: 1px solid #ccc; padding: 4px;">254.0</td>
        <td style="border: 1px solid #ccc; padding: 4px;">1762</td>
        <td style="border: 1px solid #ccc; padding: 4px;">True</td>
        <td style="border: 1px solid #ccc; padding: 4px;">True</td>
      </tr>
      <tr>
        <td style="border: 1px solid #ccc; padding: 4px;">32674</td>
        <td style="border: 1px solid #ccc; padding: 4px;">53</td>
        <td style="border: 1px solid #ccc; padding: 4px;">False</td>
        <td style="border: 1px solid #ccc; padding: 4px;">160.0</td>
        <td style="border: 1px solid #ccc; padding: 4px;">3.6309</td>
        <td style="border: 1px solid #ccc; padding: 4px;">45.0</td>
        <td style="border: 1px solid #ccc; padding: 4px;">1.0212</td>
        <td style="border: 1px solid #ccc; padding: 4px;">24</td>
        <td style="border: 1px solid #ccc; padding: 4px;">50.0</td>
        <td style="border: 1px solid #ccc; padding: 4px;">313.0</td>
        <td style="border: 1px solid #ccc; padding: 4px;">2644</td>
        <td style="border: 1px solid #ccc; padding: 4px;">False</td>
        <td style="border: 1px solid #ccc; padding: 4px;">True</td>
      </tr>
    </tbody>
  </table>
</div>



To mitigate overfitting, the data will be split into 75% training and 25% test sets. Our model’s performance will be evaluated using regression metrics such as Mean Absolute Error (MAE), Root Mean Squared Error (RMSE), and the R² score. These metrics will help us understand the predictive accuracy and the variance explained by our model.

At the time of prediction, the only available information will be the in-game performance statistics (e.g., assists, wards placed, ward kills, kills, control wards bought, etc.), allowing the model to generate an estimated vision score. This predictive insight can then be used to further understand a player’s contribution to vision control and overall team strategy.

By addressing this prediction problem, we aim to quantify the impact of in-game performance on vision score, providing a valuable tool for game analysis and strategic planning in League of Legends.

## Baseline Model
For the baseline model, we used a linear regression, with the following features wardsplaced, wpm, wardskilled, wcpm, controlwardsbought. My assumption is that the most direct inputs to a vision score are the actions related to placing and managing wards. The features are quantitative. We utilized StandardScaler Transformer to transform them into standard scale, becasue each match has different time length, and therefore the statistics could seem really different without being standardized. 

We also used Polynomial Features to fine a hyperameter that best fit the model. After fitting the model, our R squared score on the training data was 0.9102. Though our accuracy is high the RSME on the training data was 18.3220, which is not very good. Our R squared score on the test data was 0.9078, which means our model has low variance. The RSME on the test data was 22.4480. Our model still has large improvement space, and we will improve it by adding more features and using a random forest regressor, and tuning hyperparameters in the next section because it will capture complex, non-linear interactions without needing to manually generate polynomial features.


## Final Model
In our Final model, we shifted from a polynomial linear regression approach to a Random Forest regressor to better capture complex non-linear interactions among our features. Our dataset includes both categorical variables (such as result, more_vision, and more_kills) and quantitative features (like assists, wardsplaced, wpm, wardskilled, wcpm, kills, controlwardsbought, gamelength, among others). We used a preprocessing pipeline where the categorical features were transformed using OneHotEncoder (with the first category dropped) and the numerical features were standardized using StandardScaler. This ensures that differences in match duration and varying scales among features do not skew the model's performance.

To improve model performance, we implemented hyperparameter tuning using GridSearchCV. We set up a grid that explored combinations of three key hyperparameters for the Random Forest regressor: the number of trees (n_estimators), the maximum depth of the trees (max_depth), and the minimum number of samples required to split a node (min_samples_split). The grid search was conducted with 5-fold cross-validation (with shuffling enabled for more robust sampling) and used negative root mean squared error (RMSE) as the scoring metric.

Given the size of our dataset (approximately 100,000 rows), we opted to perform the initial hyperparameter tuning on a smaller subset (50,000 rows). This subset allowed us to efficiently search for the best hyperparameters without the extensive computational time required for the full dataset. On this subset, the grid search identified the best hyperparameters as follows: max_depth of 10, min_samples_split of 5, and n_estimators of 200, resulting in a cross-validated RMSE of around 19.0761 and a test RMSE of approximately 18.9855.

With these promising results from the subset, we applied the tuned hyperparameters to a pipeline re-fitted on the full training data. This approach should leverage the model's ability to capture non-linearities and complex feature interactions, ultimately enhancing the prediction of the team's vision score compared to our baseline model. Using the best parameter the test dataset RMSE was 18.8683, the train dataset R^2 was 0.9438, and the test R^2 was 0.9348


## Fairness Analysis
In this section, we are going to assess if our model is fair among different groups. The question we are trying to answer here is: “does my model perform worse on teams that lost than it does for teams that won ?” To answer this question, we performed a permutation test and examined the result of the difference in accuracy between the two groups.

The group X represents the teams that lost, and group Y represents the teams that won. Our evaluation metric is the difference in the R^2 between each group, and the significance level is 0.05.

The followings are our hypothesis:

Null hypothesis: Our model is fair. Its accuracy for teams that lost is same as the accuracy for teams that won.

Alternative hypothesis: Our model is unfair. Its accuracy for teams that lost is NOT the same as the accuracy for teams that won.

Test statistics: The difference in R^2 between the groups.

After performing the permutation test, the result p-value we got is 1.0, which is larger than the 0.05 significance level. Consequently, we fail to reject the null hypothesis. This outcome implies that our model predicts players from both groups with statistically similar accuracy levels. Consequently, our model appears to be fair, exhibiting no discernible bias towards one group over the other based on the specified criteria.

