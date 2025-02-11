# How do I win a Pickleball Point?

Caleb Hyun - <a href='mailto:ckhyun@umich.edu'>ckhyun@umich.edu</a>

Maxwell Cooper - <a href='mailto:maxwcoop@gmail.com'>maxwcoop@gmail.com</a>

<a href='https://colab.research.google.com/drive/188fhIAnGwSyy9-qEvqacct2RG3GNOzxK?usp=sharing' target="_blank"> Link to Analysis </a>


## Introduction

We analyzed a dataset of professional pickleball doubles matches found at pklmart.com. We were able to get 2 main dataframes through the pklshop API. One dataframe contained information about 2500+ rallies across different 50 different professional pickleball matches. We also had information about 22,000 shots from these rallies, which we combined into a final dataframe.

### Point Analysis

Our goal was to predict the winner of an individual pickleball point through machine learning models based on metrics found in these datasets. 

After creating the model, we analyzed the classifiers that made up this model, and attempted to identify the most important features of what makes a winning point for each team. Ultimately, we would like to identify what exactly makes up a win in a doubles match to help improve pickleball performance.

We utilized the following columns:

`w_team_id` - this is the column we are predicting, it is binarized to 1 for the serve team 
      wins the point, 0 for the return team wins
      
`ts_type` - what the third shot type is in the match, this is done by the serving team
      
`rally_len` - the number of shots completed in this individual rally or point
      
`srv_switch_ind`, `rtrn_switch_ind`, `srv_team_flipped_ind`, `rtrn_team_flipped_in` - these 
      columns are binarized talking about different methods of stacking in pickleball, 1 for if 
      they are doing that method of stacking, 0 if not
      
`lob_count_S`, `lob_count_R` - describes how many lobs each team hit in one point, 
      S for Serve, R for Return
      
`serve_dink_count`, `return_dink_count` - describes how many dinks each team hit 
      in one point
      
`speedup_count_S`, `speedup_count_R` - describes how many speedups each team hit 
      in one point, S for Serve, R for Return
      
`first_to_speedup` - describes the first team to speedup the ball in each point, 
      S for Serve, R for Return, or nan for neither team hit a speedup
      
`shot_type_orig` - describes the type of shot hit, utilized for combining the two dataframes 

## Data Cleaning and Exploratory Data Analysis

The pklshop API allowed us to get 2 main datasets. One was a dataset containing rallies indexed by game it occured in. The next was shots indexed by each rally.

We combined these datasets into a final dataset of rallies indexed by game. This data also contained additional information that we got from the `shots` dataframe.

The specific metrics we pulled are: Serve and Return team Dink Count, Serve and Return team Speedup count, Serve and Return Team lob count, and which team did a speedup first.

### Data Cleaning
First, we created a combined_rallies dataset by appending all of the rallies game by game.

Similarly, we accessed the shots rally by rally, to create our final combined_shots dataset: 

      combined_rallies.columns:
            Index(['rally_id', 'match_id', 'game_id', 'rally_nbr', 'srv_team_id',
             'srv_player_id', 'rtrn_team_id', 'rtrn_player_id', 'ts_player_id',
             'ts_type', 'w_team_id', 'to_ind', 'to_team_id', 'rally_len',
             'srv_switch_ind', 'rtrn_switch_ind', 'srv_team_flipped_ind',
             'rtrn_team_flipped_ind', 'ending_type', 'ending_player_id', 'lob_cnt',
             'dink_cnt', 'maint_dtm', 'maint_app', 'create_dtm', 'create_app'],
            dtype='object')
| shot_id   | rally_id   |   shot_nbr | shot_type_orig   | shot_type   | player_id   |   entry_ts |   btt_before |   btt_after |   loc_x |   loc_y |   next_loc_x |   next_loc_y |   cst_model_id |   whtb_model_id | maint_dtm                        | maint_app   | create_dtm                       | create_app   |
|:----------|:-----------|-----------:|:-----------------|:------------|:------------|-----------:|-------------:|------------:|--------:|--------:|-------------:|-------------:|---------------:|----------------:|:---------------------------------|:------------|:---------------------------------|:-------------|
| S467      | R47        |          1 | SE               | SE          | P4          |        nan |          nan |         nan |     nan |     nan |          nan |          nan |            nan |             nan | 2022-07-30 21:41:10.918407+00:00 | aspancak    | 2022-04-09 03:19:34.047765+00:00 | postgres     |
| S468      | R47        |          2 | R                | R           | P1          |        nan |          nan |         nan |     nan |     nan |          nan |          nan |            nan |             nan | 2022-07-30 21:41:10.918407+00:00 | aspancak    | 2022-04-09 03:19:34.048099+00:00 | postgres     |
| S469      | R47        |          3 | tsDrp            | tsDrp       | P4          |        nan |          nan |         nan |     nan |     nan |          nan |          nan |            nan |             nan | 2022-07-30 21:41:10.918407+00:00 | aspancak    | 2022-04-09 03:19:34.048423+00:00 | postgres     |
| S470      | R47        |          4 | D                | D           | nan         |        nan |          nan |         nan |     nan |     nan |          nan |          nan |            nan |             nan | 2022-07-30 21:41:10.918407+00:00 | aspancak    | 2022-04-09 03:19:34.048739+00:00 | postgres     |
| S471      | R47        |          5 | D                | D           | nan         |        nan |          nan |         nan |     nan |     nan |          nan |          nan |            nan |             nan | 2022-07-30 21:41:10.918407+00:00 | aspancak    | 2022-04-09 03:19:34.049060+00:00 | postgres     |

### Univariate Analysis

<iframe
  src="assets/rally_length_by_end_type.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This plot is a collection of 4 box and whisker plots. As we can see from the chart, a majority of the unforced errors, and normal errors occur earlier in a point, typically within 5 or 6 shots. The majority of the winners occur after ~9 shots. This causes us to want to look into the rally length as a possible parameter to our eventual model.

We decided to remove rallies that ended due to an `unforced_error`, as these are hard to predict and result from mistakes by the losing team rather than efforts by the opposing team.

<iframe
  src="assets/shot_type_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This chart tells us how many total shots were hit in our shots dataset. Here are the descriptions and analyses of each category from left to right:
      
`O` - other, and a majority of the shots are uncategorized based on this
`D` - dink, this is to be expected because the sport centers around dinks
`SE` - serves 
`R` - returns
`tsDrp` - number of third shot drives
`tsDrv` - number of third shot drops interestingly enough, the third shot drop is more popular than the drive
`SP` - speed ups
`L` - number of lobs, we expect this, as lobs are not very popular 
`E` - ernies, or shots where a player jumps over the kitchen to hit a volley
`tsL` - number of third shot lobs, where this is clearly a last resort third shot
 `A` - ATP's or around the post shots where the ball doesn't travel over the net

Additionally, we might want to analyze some of these shot types, in terms of whether or not the type of third shot weighs a large part of the winner, or we could want to look at which team hit a speed up shot first during the point.

### Bivariate Analysis
<iframe
  src="assets/rally_length_vs_dink_count.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This plot charts the comparison between rally length and dink count, but also includes the type of shot ending it had. From it, we can see that most of the low dink count, low rally length points ended in errors or unforced errors. As points went longer over 20 rallys with under 15 dinks they started ending with winners or another type of ending.

The following are three charts of shot locations that we found to be interesting.

**Third Shot Drop Locations**

<img src="assets/tsDrp_shots.png" alt="Third Shot Drop Locations" width="500">

As we can see from this image, the majority of the third shot drop locations are hit from the baseline. We can assume this means that the returned ball to the serving team was hit hard and deep into the court, forcing the serving team to hit a drop rather than a lob or a drive.

**Third Shot Drive Locations**

<img src="assets/tsDrp_shots.png" alt="Third Shot Drive Locations" width="500">

From this image, we can see that the third shot drives are commonly hit from the baseline as well, but with one major difference from the third shot drop locations being that they were farther up and had a lower precision targeted on the baseline. This is reflected in professional gameplay, where players will opt for a third shot drive when the return ball is hit weaker and closer to the net.

**Lob Locations**

<img src="assets/lob_shots.png" alt="Lob Locations" width="500">

This image demonstrates the lob locations in the matches. We can see that there are three hotspots where lobs are hit. The largest region is the back middle section, where players will be in 'no man's land' between the baseline and the kitchen area. This can force many awkward lobs. It is also where opponents' lobs land, so it could also be the locations of a counter lob, after the team was already lobbed in the shot before.

The next location is the right player's kitchen spot. This is interesting because the right player is hitting more lobs than the last hotspot of the left player's kitchen spot.



### Interesting Aggregates
In order to create our model, we wanted to break down the number of shots hit by each team, as we originally only had the number of a shot hit during the point.

We created a final analysis dataframe, where along with other columns from the original dataframe, we used the shots dataframe to count the number of dinks, speedups, and lobs that each team hit, along with who was first to speedup.

### Final DataFrame

| w_team_id   | srv_team_id   | rally_id   | ts_type   | srv_switch_ind   | rtrn_switch_ind   | srv_team_flipped_ind   | rtrn_team_flipped_ind   |   rally_len |   serve_dink_count |   return_dink_count |   speedup_count_S |   speedup_count_R |   lob_count_S |   lob_count_R | first_to_speedup   | srv_team_won   |
|:------------|:--------------|:-----------|:----------|:-----------------|:------------------|:-----------------------|:------------------------|------------:|-------------------:|--------------------:|------------------:|------------------:|--------------:|--------------:|:-------------------|:---------------|
| T1          | T2            | R47        | Drop      | N                | N                 | Y                      | N                       |           7 |                  1 |                   1 |                 0 |                 1 |             0 |             0 | R                  | False          |
| T1          | T1            | R49        | Drop      | Y                | N                 | N                      | Y                       |           9 |                  0 |                   1 |                 1 |                 0 |             0 |             0 | S                  | True           |
| T2          | T1            | R52        | Drop      | N                | N                 | Y                      | Y                       |           7 |                  0 |                   0 |                 0 |                 0 |             1 |             0 | nan                | False          |
| T2          | T1            | R1         | Drive     | N                | Y                 | N                      | N                       |           5 |                  0 |                   0 |                 0 |                 0 |             0 |             0 | nan                | False          |
| T2          | T2            | R2         | Drop      | Y                | N                 | N                      | N                       |          21 |                  4 |                   4 |                 1 |                 0 |             0 |             0 | S                  | True           |

These are the first 5 rows of our final dataframe. Some columns to note that weren't referenced earlier are `srv_team_id` and `rally_id`: these columns are just indexes. They helped us match up shot data to be aggregated into the final dataframe. 

This dataframe is what we will be running our models on. We are going to utilize multiple features to predict the outcome of an individual point 

### Imputation
We did not have to impute any values, we ended up not grabbing any rows where there were na values for the rally. The only 'imputation' we did was fill the `first_to_speedup` column with 'NaN' if neither team hit a speedup shot in the rally.

We didn't fill any missing values because we didn't grab them, because we didn't want to predict on made-up data through imputation. We wanted to predict the outcome of points where we had all necessary data present.

## Framing a Prediction Problem
We are going to predict the results of column `srv_team_won` which is a binary column with 1 for serve team wins, and 0 for return team wins. We are predicting this column because we want to gather what features allow us to actually make the predictions, and what matters the most for the models accuracy. 

This is a binary classification problem, and we are going to utilize the accuracy score to determine how effective our model was. Accuracy will let us understand how accurate our predictions were to the actual result of the point over the span of all of our points. 

On top of this, since we would like to determine what factors make up a winning point, we excluded any row that has an ending classified as an **'Unforced Error'**. This allows us to get more accurate data for a winning point with either a forced error, winner, or other, not a classified mistake by a player.

During a live match, we wouldn't have the information and features utilized before the point is over, but we aren't trying to predict live odds or who is going to win the current played point. We are instead analyzing previously played points to try to determine their winner, then from there we want to analyze what important features made up the winning teams chances/our prediction.

## Baseline Model

Features: The baseline model uses rally length, serve team dink count (quantitative), and third shot type (nominal).

**Data Transformations:**

Numerical: rally_len and serve_dink_count are scaled with StandardScaler to ensure consistent feature scaling. Categorical: ts_type is one-hot encoded with handle_unknown='ignore', enabling binary representation and handling unseen categories.

**Modeling Steps:**

Train-Test Split: The dataset is split to ensure unbiased performance evaluation on unseen data. 

Pipeline: A ColumnTransformer scales numerical features and encodes categorical ones, feeding transformed data to a LogisticRegression model. The same transformations are applied to the test set during prediction.


**Classification Report:**
      
               precision    recall  f1-score   support
       False       0.61      0.93      0.74       316
        True       0.46      0.09      0.15       208

    accuracy                           0.60       524
 


<iframe
  src="assets/baseline_confusion_matrix.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

**Evaluation:**

While our Baseline model seemed to have reasonable accuracy, we noticed that the model was picking False much too often, likely because of the fact that the return team has an inherent ~10% advantage starting the point. However, even when the model WAS picking True, it was getting it wrong more than 50% of the time. This can help explain why the f1-score is so low for 'True' predictions. We assessed the performance using a classification report (precision, recall, F1-score) and a confusion matrix.

Overall, we don't think this model is incredible because if we had just predicted the return team to win every time, we would end up with a ~55% accuracy. So our model can definitely be improved. We would also like to see more True values predicted as it feels like our model is just predicting mostly false without a lot of reason to it, and that is why our accuracy is marginally higher than 55%.


## Final Model
### Random Forest Classifier

For the final model, we used a **Random Forest Classifier** and improved upon the baseline model by engineering 4 new features and tuning hyperparameters using `GridSearchCV`.

## **Features Addded**

**numerical added:** `dink_count_dif`, `speedup_count_dif`, `lob_count_dif`

**categorical added:** `rally_len_categorical`, `srv_switch_ind`, `rtrn_switch_ind`, `srv_team_flipped_ind`, `rtrn_team_flipped_ind`, `first_to_speedup`

We added variables that relate to stacking: `srv_switch_ind`, `rtrn_switch_ind`, `srv_team_flipped_ind`, `rtrn_team_flipped_ind` as we believed that they might have an impact on the winner of the point. 

We also added `first_to_speedup` as we believed that this might shed light into who was playing more aggressively, ultimately benefitting our models prediction on which team ends up winning.


## **Feature Engineering**
1. **Interaction Features**
   - **`dink_count_dif`:** Difference between serve and return dink counts. This captures the net dominance in dinks. We decided to add this as a feature because we didn't want to overfit to a large number of dinks: we believed that the important metric was the differential between serve and return team dinks.
   - **`speedup_count_dif`:** Difference between serve and return speedups. This reflects aggression levels in rallies. We decided to add the speedup count difference as well for the same reason as the dink count difference, that the difference between the number hit mattered more than just the number hit by each team.
   - **`lob_count_dif`:** Difference between serve and return teams lobs. Same reason here as well, the number of lobs hit difference could fit the model better than the raw number of lobs per team.

2. **Categorical Transformation**
   - **`rally_len_categorical`:** Converted `rally_len` into short, medium, and long categories. Again, we didn't want to overfit too heavily on the numerical rally length (even with scaling) so we  decidde to bin them based on length. So we utilized the 33rd and 66th percentiles as our bin metrics. Under 5 shots, was a short rally, under 11 was a medium rally, and anything over 11 is long. We belived that the difference between a 15 shot rally and a 25 shot rally isn't that large and we could get better results through this categorization. 
   - Binary encoding for features like `srv_switch_ind`.

## **Modeling Algorithm**
- **Random Forest Classifier**: We chose the random forest model because it inherently models the complex, non-linear features better than a logistic regression model. It can also capture interactions between the features better than logistic regression.
- **Hyperparameter Tuning**:
  - **`n_estimators`:** Number of trees in the forest. Tuning this controls the trade-off between model accuracy and training time, as more trees typically improve performance up to a point.
  - **`max_depth`:** Controls tree depth to prevent overfitting. Tuning this ensures that trees are not too deep (overfitting) or too shallow (underfitting), balancing model complexity.
  - **`classifier_min_samples_split`:** Controls the minimum number of samples to split a node in a decision tree. Tuning this prevents overly complex trees by ensuring splits occur only when enough data supports them.

### **Performance Evaluation**
- **Test Set Results**:
  - **Classification Report**: Our Final model improved precision, recall, and F1-scores compared to the baseline model.
  - **Confusion Matrix**: Most significantly, our model reduced the number of false negatives.

### **Comparison with Baseline**
- The final model outperformed the baseline by capturing nuanced interactions through engineered features and Random Forest hyperparameter tuning.
- Additionally, we saw that, while the model still predicted False too much, it predicted True correctly more often than not as opposed to our Baseline model. Our model seems to be very good at predicting whether the return team wins the point, but not as good at measuring whether the serve team wins the point. 


Classification Report:
               
               precision    recall  f1-score   support
       False       0.67      0.80      0.73       316
        True       0.57      0.41      0.48       208

    accuracy                           0.65       524
<iframe
  src="assets/final_confusion_matrix.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Feature Analysis
One of our stated goals was to figure out how we could change our game based on the most important hyperparameters to our model. 


**Feature Importance with Directionality**

<img src="assets/Feature_importance-directional.png" alt="Feature Importance with Directionality" width="800">

**Dink Count Difference Impacts**

<img src="assets/dink_count_dif_chart.png" alt="Dink Count Difference Impacts" width="500">

## Interesting Insights:

Qualifiers - Our model is not perfect by any means, and this was trained on pro matches, so the takeaways may be different than amateur matches.

**Influence of `ts_type`:**
The model predicts a higher probability of the serving team winning when the shot type is 'Drive' compared to 'Drop' or 'Lob.' However, this reflects a correlation observed in the data and does not necessarily imply causation. For example, we saw that drives are generally hit off of shallower returns. Lobs seem to be the least effective third shot.

NaN being high in this graph makes sense, as when a third shot is not hit, it was likely off of an error in the return.

**Influence of `First to Speed Up`:**
Interestingly, the team to speed up first is strongly predicted to win the point, according to our model.

**Influence of `rally_len_categorical`:**
Something that surprised us was that the length of the category didn't seem to affect the prediction that much. We expected that, since the serving team has a small disadvantage going into the point (.45%), as the point goes on that disadvantage would go away when both teams get to the kitchen line. This was what we saw.

**Stacking:**
None of the stacking variables (srv, rtrn switch) seemed to make much of a difference on the model.

## Conclusion
Pickleball point winners are inherently hard to predict, since it is a binary outcome clouded by hundreds of small choices. Our goal was to identify clear, actionable strategies that players can use to improve their chances of winning points by leveraging patterns and tendencies observed in gameplay.

### Strategy 1: 
**Change your priorities as the point progresses.** As the returning team, prioritize aggression during the first six shots of the rally to force opponents into making an error. After this initial phase, and the serving team is at the kitchen line, shift gears between shots 6-12 to allow the rally to return to equilibrium. Once balance is restored, actively seek opportunities for decisive winners or unexpected speedups to catch your opponents off guard.

### Strategy 2: 
**Don't feel forced to dink.** Our model predicted that the team that hit more dinks actually **lost** the point - mix in other types of shots and don’t let your opponents feel comfortable.

### Strategy 3: 
**Don't be afraid to look for speedups.** Taking control of the rally by increasing the pace can break your opponents’ rhythm. Our model found that the first team to speed up generally won - however, pros normally speed up when they receive an attackable ball, so this metric may be correlation rather than causation.

### Strategy 4: 
**Drive when you get a weak return.** Our model predicted that teams that drove on the third ball were more likely to win the point - however, based on the heatmap of where shots are hit, we can see that pros generally hit their drives when the return was more shallow, and dropped when the return was deep.

### Strategy 5: 
**Stack away!**  Our model found that stacking does not have a significant impact on the winner of points. By stacking, you ensure that both partners are playing on their strongest side, which can improve shot quality and court coverage.
