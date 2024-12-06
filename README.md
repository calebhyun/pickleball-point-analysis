# Pickleball Point Analysis

Caleb Hyun - ckhyun@umich.edu

Maxwell Cooper - maxwcoop@gmail.com

## Introduction

This dataset is a collection of professional pickleball doubles matches found at pklmart.com. We were able to get 2 main dataframes through the use of the pklshop API. One dataframe contained information about 2500+ rallies across different 50 different pickleball matches. The next dataframe contained information about 22,000 shots from these rallies. We combined the results of these two dataframes into a final dataframe.

### Point Analysis

Through the use of this dataframe, we hope to be able to predict the winner of an individual pickleball point through machine learning models based on metrics found in these datasets. 

After we have created a model, we will analyze the classifiers that made up this model, and attempt to identify the most important features of what makes a winning point for each team. Ultimately, we would like to identify what exactly makes up a win in a doubles match to help improve pickleball performance.

The columns we will utilize are as follows:

      'w_team_id' - this is the column we are predicting, it is binarized to 1 for the serve team 
      wins the point, 0 for the return team wins
      
      'ts_type' - what the third shot type is in the match, this is done by the serving team
      
      'rally_len' - the number of shots completed in this individual rally or point
      
      'srv_switch_ind', 'rtrn_switch_ind', 'srv_team_flipped_ind', 'rtrn_team_flipped_in' - these 
      columns are binarized talking about different methods of stacking in pickleball, 1 for if 
      they are doing that method of stacking, 0 if not
      
      'lob_count_S', 'lob_count_R' - describes how many lobs each team hit in one point, 
      S for Serve, R for Return
      
      'serve_dink_count', 'return_dink_count' - describes how many dinks each team hit 
      in one point
      
      'speedup_count_S', 'speedup_count_R' - describes how many speedups each team hit 
      in one point, S for Serve, R for Return
      
      'first_to_speedup' - describes the first team to speedup the ball in each point, 
      S for Serve, R for Return, or nan for neither team hit a speedup
      
      'shot_type_orig' - describes the type of shot hit, utilized for combining the two dataframes 

## Data Cleaning and Exploratory Data Analysis

The pklshop API allowed us to get 2 main datasets. One was a dataset containing rallies indexed by game it occured in. The next was shots indexed by each rally.

We will now combine these datasets into a final dataset of rallies indexed by game. But this dataset would now contain additional information that we got from the shots dataframe.

The specific metrics we pulled are: Serve and Return team Dink Count, Serve and Return team Speedup count, Serve and Return Team lob count, and which team did a speedup first.

### Data Cleaning
First, we have to create a combined_rallies dataset by appending all of the rallies game by game.

Similarly, we accessed the shots rally by rally, to create our final combined_shots dataset: 

      combined_rallies.columns:
            Index(['rally_id', 'match_id', 'game_id', 'rally_nbr', 'srv_team_id',
             'srv_player_id', 'rtrn_team_id', 'rtrn_player_id', 'ts_player_id',
             'ts_type', 'w_team_id', 'to_ind', 'to_team_id', 'rally_len',
             'srv_switch_ind', 'rtrn_switch_ind', 'srv_team_flipped_ind',
             'rtrn_team_flipped_ind', 'ending_type', 'ending_player_id', 'lob_cnt',
             'dink_cnt', 'maint_dtm', 'maint_app', 'create_dtm', 'create_app'],
            dtype='object')
<iframe
  src="assets/combined_shots_table.md"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Univariate Analysis
<iframe
  src="assets/rally_length_by_end_type.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
<iframe
  src="assets/shot_type_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analysis
<iframe
  src="assets/rally_length_vs_dink_count.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## To DO: insert two interesting bivariate analyses

### Interesting Aggregates
In order to create our model, we wanted to break down the number of shots hit by each team, as we originally only had the number of a shot hit during the point.

We created a final analysis dataframe, where along with other columns from the original dataframe, we used the shots dataframe to count the number of dinks, speedups, and lobs that each team hit, along with who was first to speedup.

### Final DataFrame


| w_team_id | srv_team_id | rally_id | ts_type | srv_switch_ind | rtrn_switch_ind |
|-----------|-------------|----------|---------|----------------|-----------------|
| T1        | T2          | R47      | Drop    | N              | N               |
| T1        | T1          | R49      | Drop    | Y              | N               |
| T2        | T1          | R52      | Drop    | N              | N               |
| T2        | T1          | R1       | Drive   | N              | Y               |
| T2        | T2          | R2       | Drop    | Y              | N               |


| srv_team_flipped_ind   | rtrn_team_flipped_ind   |   rally_len |   serve_dink_count |
|:-----------------------|:------------------------|------------:|-------------------:|
| Y                      | N                       |           7 |                  1 |
| N                      | Y                       |           9 |                  0 |
| Y                      | Y                       |           7 |                  0 |
| N                      | N                       |           5 |                  0 |
| N                      | N                       |          21 |                  4 |


|   return_dink_count |   speedup_count_S |   speedup_count_R |   lob_count_S |   lob_count_R |
|--------------------:|------------------:|------------------:|--------------:|--------------:|
|                   1 |                 0 |                 1 |             0 |             0 |
|                   1 |                 1 |                 0 |             0 |             0 |
|                   0 |                 0 |                 0 |             1 |             0 |
|                   0 |                 0 |                 0 |             0 |             0 |
|                   4 |                 1 |                 0 |             0 |             0 |


| first_to_speedup   | srv_team_won   |
|:-------------------|:---------------|
| R                  | False          |
| S                  | True           |
| nan                | False          |
| nan                | False          |
| S                  | True           |

These are the first 5 rows of our final dataframe. Some columns to note that weren't referenced earlier are 'srv_team_id' and 'rally_id', these columns are just indexes essentially. They helped us match up shot data to be aggregated into the final dataframe. 

This dataframe is what we will be running our models on. We are going to utilize multiple features to predict the outcome of an individual point 

### Imputation
We did not have to impute any values, we ended up not grabbing any rows where there were na values for the rally. The only 'imputation' we did was fill the 'first_to_speedup' column with 'NaN' if neither team hit a speedup shot in the rally.

We didn't fill any missing values because we didn't grab them, because we didn't want to predict on made-up data through imputation. We wanted to predict the outcome of points where we had all necessary data present.

## Framing a Prediction Problem
We are going to predict the results of column 'w_team_id' which is a binary column with 1 for serve team wins, and 0 for return team wins. We are predicting this column because we want to gather what features allow us to actually make the predictions, and what matters the most for the models accuracy. 

This is a binary classification problem, and we are going to utilize the accuracy score to determine how effective our model was. Accuracy will let us understand how accurate our predictions were to the actual result of the point over the span of all of our points. 


## Baseline Model

Features: The baseline model uses rally length, serve team dink count (quantitative), and third shot type (nominal).

Data Transformations:

Numerical: rally_len and serve_dink_count are scaled with StandardScaler to ensure consistent feature scaling. Categorical: ts_type is one-hot encoded with handle_unknown='ignore', enabling binary representation and handling unseen categories.

Modeling Steps:

Train-Test Split: The dataset is split to ensure unbiased performance evaluation on unseen data. Pipeline: A ColumnTransformer scales numerical features and encodes categorical ones, feeding transformed data to a LogisticRegression model. The same transformations are applied to the test set during prediction.

Evaluation:

Performance is assessed using a classification report (precision, recall, F1-score) and a confusion matrix.

Classification Report:
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

## Final Model
Classification Report:
               precision    recall  f1-score   support

       False       0.67      0.80      0.73       316
        True       0.57      0.41      0.48       208

    accuracy                           0.65       524
    Best Cross-Validation Accuracy: 0.5949079959852793
<iframe
  src="assets/final_confusion_matrix.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
## Conclusion
