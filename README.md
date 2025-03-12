# League-of-Legends-data-analysis

## Introduction


## Step 5: Framing a Prediction Problem
Can we accurately predict a team's vision score based solely on their in-game performance statistics?

For our prediction model, we will perform necessary preprocessing steps such as dropping non-informative or metadata columns like gameid and url. This ensures that our model leverages only the relevant in-game statistics.

To address this question, we will frame the problem as a regression task where the vision score is treated as a continuous variable. Our dataset includes the following columns:
assists, result, wardsplaced, wpm, wardskilled, wcpm, kills, controlwardsbought, visionscore, gamelength, more_kills, and more_vision.
Below is the head of DataFrame we are using in this section:

<div style="text-align: center;">
  <table style="margin: 0 auto;">
    <thead>
      <tr>
        <th>index</th>
        <th>assists</th>
        <th>result</th>
        <th>wardsplaced</th>
        <th>wpm</th>
        <th>wardskilled</th>
        <th>wcpm</th>
        <th>kills</th>
        <th>controlwardsbought</th>
        <th>visionscore</th>
        <th>gamelength</th>
        <th>more_vision</th>
        <th>more_kills</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>32650</td>
        <td>60</td>
        <td>True</td>
        <td>151.0</td>
        <td>3.7956</td>
        <td>47.0</td>
        <td>1.1814</td>
        <td>21</td>
        <td>31.0</td>
        <td>304.0</td>
        <td>2387</td>
        <td>False</td>
        <td>True</td>
      </tr>
      <tr>
        <td>32651</td>
        <td>23</td>
        <td>False</td>
        <td>139.0</td>
        <td>3.4939</td>
        <td>57.0</td>
        <td>1.4328</td>
        <td>13</td>
        <td>35.0</td>
        <td>359.0</td>
        <td>2387</td>
        <td>True</td>
        <td>False</td>
      </tr>
      <tr>
        <td>32662</td>
        <td>11</td>
        <td>False</td>
        <td>99.0</td>
        <td>3.3712</td>
        <td>25.0</td>
        <td>0.8513</td>
        <td>9</td>
        <td>23.0</td>
        <td>188.0</td>
        <td>1762</td>
        <td>False</td>
        <td>False</td>
      </tr>
      <tr>
        <td>32663</td>
        <td>53</td>
        <td>True</td>
        <td>117.0</td>
        <td>3.9841</td>
        <td>31.0</td>
        <td>1.0556</td>
        <td>24</td>
        <td>29.0</td>
        <td>254.0</td>
        <td>1762</td>
        <td>True</td>
        <td>True</td>
      </tr>
      <tr>
        <td>32674</td>
        <td>53</td>
        <td>False</td>
        <td>160.0</td>
        <td>3.6309</td>
        <td>45.0</td>
        <td>1.0212</td>
        <td>24</td>
        <td>50.0</td>
        <td>313.0</td>
        <td>2644</td>
        <td>False</td>
        <td>True</td>
      </tr>
    </tbody>
  </table>
</div>


To mitigate overfitting, the data will be split into 75% training and 25% test sets. Our model’s performance will be evaluated using regression metrics such as Mean Absolute Error (MAE), Root Mean Squared Error (RMSE), and the R² score. These metrics will help us understand the predictive accuracy and the variance explained by our model.

At the time of prediction, the only available information will be the in-game performance statistics (e.g., assists, wards placed, ward kills, kills, control wards bought, etc.), allowing the model to generate an estimated vision score. This predictive insight can then be used to further understand a player’s contribution to vision control and overall team strategy.

By addressing this prediction problem, we aim to quantify the impact of in-game performance on vision score, providing a valuable tool for game analysis and strategic planning in League of Legends.

## Step 6: Baseline Model
For the baseline model, we used a linear regression, with the following features wardsplaced, wpm, wardskilled, wcpm, controlwardsbought. My assumption is that the most direct inputs to a vision score are the actions related to placing and managing wards. The features are quantitative. We utilized StandardScaler Transformer to transform them into standard scale, becasue each match has different time length, and therefore the statistics could seem really different without being standardized. 

We also used Polynomial Features to fine a hyperameter that best fit the model. After fitting the model, our R squared score on the training data was 0.9102. Though our accuracy is high the RSME on the training data was 18.3220, which is not very good. Our R squared score on the test data was 0.9078, which means our model has low variance. The RSME on the test data was 22.4480. Our model still has large improvement space, and we will improve it by adding more features and using a random forest regressor, and tuning hyperparameters in the next section because it will capture complex, non-linear interactions without needing to manually generate polynomial features.


## Step 7: Final Model
In our Final model, we shifted from a polynomial linear regression approach to a Random Forest regressor to better capture complex non-linear interactions among our features. Our dataset includes both categorical variables (such as result, more_vision, and more_kills) and quantitative features (like assists, wardsplaced, wpm, wardskilled, wcpm, kills, controlwardsbought, gamelength, among others). We used a preprocessing pipeline where the categorical features were transformed using OneHotEncoder (with the first category dropped) and the numerical features were standardized using StandardScaler. This ensures that differences in match duration and varying scales among features do not skew the model's performance.

To improve model performance, we implemented hyperparameter tuning using GridSearchCV. We set up a grid that explored combinations of three key hyperparameters for the Random Forest regressor: the number of trees (n_estimators), the maximum depth of the trees (max_depth), and the minimum number of samples required to split a node (min_samples_split). The grid search was conducted with 5-fold cross-validation (with shuffling enabled for more robust sampling) and used negative root mean squared error (RMSE) as the scoring metric.

Given the size of our dataset (approximately 100,000 rows), we opted to perform the initial hyperparameter tuning on a smaller subset (50,000 rows). This subset allowed us to efficiently search for the best hyperparameters without the extensive computational time required for the full dataset. On this subset, the grid search identified the best hyperparameters as follows: max_depth of 10, min_samples_split of 5, and n_estimators of 200, resulting in a cross-validated RMSE of around 19.0761 and a test RMSE of approximately 18.9855.

With these promising results from the subset, we applied the tuned hyperparameters to a pipeline re-fitted on the full training data. This approach should leverage the model's ability to capture non-linearities and complex feature interactions, ultimately enhancing the prediction of the team's vision score compared to our baseline model. Using the best parameter the test dataset RMSE was 18.8683, the train dataset R^2 was 0.9438, and the test R^2 was 0.9348


## Step 8: Fairness Analysis
In this section, we are going to assess if our model is fair among different groups. The question we are trying to answer here is: “does my model perform worse on teams that lost than it does for teams that won ?” To answer this question, we performed a permutation test and examined the result of the difference in accuracy between the two groups.

The group X represents the teams that lost, and group Y represents the teams that won. Our evaluation metric is the difference in the R^2 between each group, and the significance level is 0.05.

The followings are our hypothesis:

Null hypothesis: Our model is fair. Its accuracy for teams that lost is same as the accuracy for teams that won.

Alternative hypothesis: Our model is unfair. Its accuracy for teams that lost is NOT the same as the accuracy for teams that won.

Test statistics: The difference in R^2 between the groups.

After performing the permutation test, the result p-value we got is 1.0, which is larger than the 0.05 significance level. Consequently, we fail to reject the null hypothesis. This outcome implies that our model predicts players from both groups with statistically similar accuracy levels. Consequently, our model appears to be fair, exhibiting no discernible bias towards one group over the other based on the specified criteria.

