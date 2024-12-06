# Pickleball Point Analysis

Caleb Hyun - ckhyun@umich.edu

Maxwell Cooper - maxwcoop@gmail.com

## Introduction

This dataset is a collection of professional pickleball matches found at pklmart.com. We were able to get 2 main dataframes through the use of the pklshop API. One dataframe contained information about 2500+ rallies across different 50 different pickleball matches. The next dataframe contained information about 22,000 shots from these rallies. We combined the results of these two dataframes into a final dataframe.

### Point Analysis

Through the use of this dataframe, we hope to be able to predict the winner of an individual pickleball point through machine learning models based on metrics found in these datasets. 

After we have created a model, we will analyze the classifiers that made up this model, and attempt to identify the most important features of what makes a winning point for each team. 

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
### Bivariate Analysis
<iframe
  src="assets/rally_length_vs_dink_count.html"
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
### Interesting Aggregates

### Imputation

## Framing a Prediction Problem

## Baseline Model

## Final Model
