# Pickleball Point Analysis

Caleb Hyun - ckhyun@umich.edu

Maxwell Cooper - maxwcoop@gmail.com

## Introduction

This dataset is a collection of professional pickleball matches found at pklmart.com. We were able to get 2 main dataframes through the use of the pklshop API. One dataframe contained information about 2500+ rallies across different 50 different pickleball matches. The next dataframe contained information about 22,000 shots from these rallies. We combined the results of these two dataframes into a final dataframe.

### Point Analysis

Through the use of this dataframe, we hope to be able to predict the winner of an individual pickleball point through machine learning models based on metrics found in these datasets. 

After we have created a model, we will analyze the classifiers that made up this model, and attempt to identify the most important features of what makes a winning point for each team. 

The columns we will utilize are as follows:

      **'w_team_id'** - this is the column we are predicting, it is binarized to 1 for the serve team wins the point, 0 for the return team wins
      
      **'ts_type'** - what the third shot type is in the match, this is done by the serving team
      
      **'rally_len'** - the number of shots completed in this individual rally or point
      
      **'srv_switch_ind'**, 'rtrn_switch_ind', 'srv_team_flipped_ind', 'rtrn_team_flipped_in' - these columns are binarized talking about different methods of stacking in pickleball, 1 for if they are doing that method of stacking, 0 if not
      
      **'lob_count_S', 'lob_count_R'** - describes how many lobs each team hit in one point, S for Serve, R for Return
      
      **'serve_dink_count', 'return_dink_count'** - describes how many dinks each team hit in one point
      
      **'speedup_count_S', 'speedup_count_R'** - describes how many speedups each team hit in one point, S for Serve, R for Return
      
      **'first_to_speedup'** - describes the first team to speedup the ball in each point, S for Serve, R for Return, or nan for neither team hit a speedup
      
      **'shot_type_orig'** - describes the type of shot hit, utilized for combining the two dataframes 

## Data Cleaning and Exploratory Data Analysis

## Framing a Prediction Problem

## Baseline Model

## Final Model
