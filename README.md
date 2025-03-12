# League-of-Legends-data-analysis

## Introduction


## Step 5: Framing a Prediction Problem
Can we accurately predict a team's vision score based solely on their in-game performance statistics?

For our prediction model, we will perform necessary preprocessing steps such as dropping non-informative or metadata columns like gameid and url. This ensures that our model leverages only the relevant in-game statistics.

To address this question, we will frame the problem as a regression task where the vision score is treated as a continuous variable. Our dataset includes the following columns:
assists, result, wardsplaced, wpm, wardskilled, wcpm, kills, controlwardsbought, visionscore, gamelength, more_kills, and more_vision

To mitigate overfitting, the data will be split into 75% training and 25% test sets. Our model’s performance will be evaluated using regression metrics such as Mean Absolute Error (MAE), Root Mean Squared Error (RMSE), and the R² score. These metrics will help us understand the predictive accuracy and the variance explained by our model.

At the time of prediction, the only available information will be the in-game performance statistics (e.g., assists, wards placed, ward kills, kills, control wards bought, etc.), allowing the model to generate an estimated vision score. This predictive insight can then be used to further understand a player’s contribution to vision control and overall team strategy.

By addressing this prediction problem, we aim to quantify the impact of in-game performance on vision score, providing a valuable tool for game analysis and strategic planning in League of Legends.

