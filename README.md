# Elliot Richardson - Capstone Project

## Table of Contents:
I. [Introduction](#Introduction)

II. [Summary](#Summary-of-findings)

III. [Data used](#Data-being-used)

IV. [Processing](#Cleaning-and-processing)

V. [Modeling](#Modeling-and-analysis)

VI. [Final models](#Final-models)


## Introduction

For my capstone project, I have chosen to make a targeting model for campaigns advocating for defunding the police using survey data from Pew Research Center's American Trends Panel (June 2020) and the UCLA Democracy Fund Voter Study Group (July 2020). Campaigns often purchase "scores" from data science firms that indicate the likelihood that a person will turn out to vote or support a specific political party, but I wanted to more specifically focus on police and prison reform. There are schisms within the Democratic party about how to handle police brutality and incarcerated folks, so hopefully this model will help bring to light where and why those schisms exist and allow movements advocating for defunding the police or abolishing prisons to more efficiently target supporters in their outreach.

I will use regression models to produce support scores between 0-100, as is customary for political targeting models. I'm limiting the features to be largely demographic (i.e. race, age, gender, etc) in order to replicate the information that a campaign might have to work with. Model performance will be evaluated using the R<sup>2</sup> score and the baseline will be the average support score among panel participants. I'd like to outperform the baseline by at least 50%.

## Summary of findings

Based on the models I created, I'm not sure that this is enough information to create a truly helpful targeting model. Both had large RMSEs (0.23 and 0.30) for a target between 0 and 1 so I wouldn't be very confident building an outreach model on these scores. I'd be really interested to see what the RMSEs are for models produced by professional targeting firms! I imagine that the consumer data they have is extremely helpful but it seems exceedingly difficult to predict something as nuanced as this. The shared important features from these two models were age, religion, and education; the models built on the Pew data put more emphasis on political party, while the models of the UCLA dataset put more emphasis on race. However, I'd take these observations with a rather large grain of salt as the models were limited in their accuracy.

## Data being used

[Pew Research Center American Trends Panel](data/pew.txt) (June 2020): 4,708 rows x 159 columns

[UCLA Democracy Fund Voter Study Group](data/ucla.txt) (July 2020): 6,479 rows x 265 columns

Data dictionary for PEW demographic data [here](https://www.pewresearch.org/wp-content/uploads/2018/05/Codebook-and-instructions-for-working-with-ATP-data.pdf)

Data dictionary for PEW opinion data [here](https://drive.google.com/file/d/1eiMsJSFsv04s_oySIIb2oELk71KBSAjA/view?usp=sharing)

Data dictionary for UCLA data [here](https://drive.google.com/file/d/1ScJGMNFG4HvVwFvPGGULRsnz71_sJsZN/view?usp=sharing)


## Cleaning and processing

### [Pew](code/1a_pew_cleaning.ipynb)
For the Pew dataset, I needed to map out the integers in just about every column to their corresponding meaning since they were categorical and not ordinal. I also needed to map values like "999" which meant "Refused" to null values. I dropped columns that had a lot of nulls, except for those that seemed to be the only indicator of something important. For example, I was able to drop a column denoting socioeconomic class because I had another column with income brackets. After dropping those columns and narrowing to the columns I wanted to use (largely demographic info), I dropped the remaining nulls, created dummies, and ended up with 4,325 rows and 50 columns. 

<details open>
<summary>Pew column descriptions</summary>
 <br>

|Column|Type|Description|
|---|---|---|
|defund_support|int64|A value between 0 and 1 (adapted from 1-5 scale in original survey) indicating what the participant believes should happen to funding for the police (1 = Increased a lot, 2 = Increased a little, 3 = Stay about the same, 4 = Decreased a little, 5 = Decreased a lot)|
|lives_in_metro_area|int64|Binary indicator of whether the participant lives in a metropolitan area (0 = Non-metropolitan, 1 = Metropolitan)|
|is_female|int64|Binary indicator of whether the participant is female or male (0 = Male, 1 = Female)|
|education_level|int64|A value between 1 and 6 indicating the participant's educational history (1 = Less than high school, 2 = High school graduate, 3 = Some college but no degree, 4 = Associate's degree, 5 = College graduate / some post-grad, 6 = Post-graduate)|
|is_hispanic|int64|Binary indicator of whether the participant is hispanic (0 = Non-hispanic, 1 = Hispanic)|
|is_citizen|int64|Binary indicator of whether the participant is a citizen (0 = Non-citizen, 1 = Citizen)|
|relig_activity|int64|A value between 1 and 6 indicating how often the participant partakes in religious activity (1 = More than once a week, 2 = Once a week, 3 = Once or twice a month, 4 = A few times a year, 5 = Seldom, 6 = Never)|
|income_bracket|int64|A value between 1 and 9 indicating the participant's household income (1 = Less than $10k, 2 = $10-20k, 3 = $20-30k, 4 = $30-40k, 5 = $40-50k, 6 = $50-75k, 7 = $75-100k, 8 = $100-150k, 9 = More than $150k)|
|internet_access|int64|Binary indicator of whether the participant has internet access from their household (0 = No internet access, 1 = Has household internet access)|
|social_media_user|int64|Binary indicator of whether the participant is a social media user (0 = Doesn't use social media, 1 = Uses social media)|
|region_east_south_central|int64|Binary indicator of whether the participant lives in the East South Central census division from FIPS (0 = Not in census division, 1 = Lives in census division)|
|region_mid_atlantic|int64|Binary indicator of whether the participant lives in the Mid-Atlantic census division from FIPS (0 = Not in census division, 1 = Lives in census division)|
|region_mountain|int64|Binary indicator of whether the participant lives in the Mountain census division from FIPS (0 = Not in census division, 1 = Lives in census division)|
|region_new_england|int64|Binary indicator of whether the participant lives in the New England census division from FIPS (0 = Not in census division, 1 = Lives in census division)|
|region_pacific|int64|Binary indicator of whether the participant lives in the Pacific census division from FIPS (0 = Not in census division, 1 = Lives in census division)|
|region_south_atlantic|int64|Binary indicator of whether the participant lives in the South Atlantic census division from FIPS (0 = Not in census division, 1 = Lives in census division)|
|region_west_north_central|int64|Binary indicator of whether the participant lives in the West North Central census division from FIPS (0 = Not in census division, 1 = Lives in census division)|
|region_west_south_central|int64|Binary indicator of whether the participant lives in the West South Central census division from FIPS (0 = Not in census division, 1 = Lives in census division)|
|age_group_18_29|int64|Binary indicator of whether the participant is between the ages of 18 and 29 (0 = Not in age group, 1 = In age group)|
|age_group_30_49|int64|Binary indicator of whether the participant is between the ages of 30 and 49 (0 = Not in age group, 1 = In age group)|
|age_group_50_64|int64|Binary indicator of whether the participant is between the ages of 50 and 64 (0 = Not in age group, 1 = In age group)|
|age_group_65_plus|int64|Binary indicator of whether the participant is more than 64 years old (0 = Not in age group, 1 = In age group)|
|race_asian|int64|Binary indicator of whether the participant is Asian (0 = Not of this race, 1 = Of this race)|
|race_black|int64|Binary indicator of whether the participant is Black (0 = Not of this race, 1 = Of this race)|
|race_mixed|int64|Binary indicator of whether the participant is of mixed race (0 = Not of this race, 1 = Of this race)|
|race_other|int64|Binary indicator of whether the participant is of an unlisted race (0 = Not of this race, 1 = Of this race)|
|race_white|int64|Binary indicator of whether the participant is white (0 = Not of this race, 1 = Of this race)|
|marital_status_live_w_partner|int64|Binary indicator of whether the participant lives with their life partner (0 = Doesn't live with partner, 1 = Lives with partner)|
|marital_status_married|int64|Binary indicator of whether the participant is married (0 = Not married, 1 = Married)|
|marital_status_separated|int64|Binary indicator of whether the participant is in a separated marriage (0 = Not separated, 1 = Separated)|
|marital_status_single|int64|Binary indicator of whether the participant is single (0 = Not single, 1 = Single)|
|marital_status_widowed|int64|Binary indicator of whether the participant's spouse passed away (0 = Not a widow(er), 1 = A widow(er))|
|religion_agnostic|int64|Binary indicator of whether the participant is religiously agnostic (0 = Not agnostic, 1 = Agnostic)|
|religion_atheist|int64|Binary indicator of whether the participant is an atheist (0 = Not an atheist, 1 = Atheist)|
|religion_buddhist|int64|Binary indicator of whether the participant is Buddhist (0 = Not Buddhist, 1 = Buddhist)|
|religion_catholic|int64|Binary indicator of whether the participant is Catholic (0 = Not Catholic, 1 = Catholic)|
|religion_hindu|int64|Binary indicator of whether the participant is Hindu (0 = Not Hindu, 1 = Hindu)|
|religion_jewish|int64|Binary indicator of whether the participant is Jewish (0 = Not Jewish, 1 = Jewish)|
|religion_mormon|int64|Binary indicator of whether the participant is Mormon (0 = Not Mormon, 1 = Mormon)|
|religion_muslim|int64|Binary indicator of whether the participant is Muslim (0 = Not Muslim, 1 = Muslim)|
|religion_none|int64|Binary indicator of whether the participant does not ascribe to any religious label (0 = Does ascribe to a label, 1 = Doesn't ascribe to a particular label)|
|religion_orthodox|int64|Binary indicator of whether the participant is religiously orthodox (0 = Not orthodox, 1 = Orthodox)|
|religion_other|int64|Binary indicator of whether the participant practices an unlisted faith (0 = Does not practice unlisted faith, 1 = Practices unlisted faith)|
|religion_protestant|int64|Binary indicator of whether the participant is Protestant (0 = Not Protestant, 1 = Protestant)|
|political_party_dem|int64|Binary indicator of whether the participant considers themselves a Democrat (0 = Not a Democrat, 1 = A Democrat)|
|political_party_gop|int64|Binary indicator of whether the participant considers themselves a Republican (0 = Not a Republican, 1 = A Republican)|
|political_party_ind|int64|Binary indicator of whether the participant considers themselves a political independent (0 = Not independent, 1 = Independent)|
|voter_status_not_registered|int64|Binary indicator of whether the participant said they were not registered to vote (0 = Didn't say not registered, 1 = Said not registered)|
|voter_status_prob_registered|int64|Binary indicator of whether the participant said they were probably registered to vote (0 = Didn't say probably registered, 1 = Said probably registered)|
|voter_status_registered|int64|Binary indicator of whether the participant said they were registered to vote (0 = Didn't say registered, 1 = Said registered)|
    
</details>
    
    
### [UCLA](code/1b_ucla_cleaning.ipynb)
For this dataset, the columns were already numerical except for the state and congressional district. There were a few cases where columns contained empty strings, so the entire column was cast as objects but I replaced empty strings with null values and converted all of those back to numerical data types. Then I went through a similar process of dropping columns with too many nulls to be helpful, narrowed to the ones I felt a campaign might realistically have access to, and then dropped the remaining rows that still had null values. After dummying the 'state' column, I ended up with 4,052 rows and 107 columns. 

|Column|Type|Description|
|---|---|---|
|group_favorability_the_police|int64|A value between 0 and 1 (adapted from 1-4 scale in original survey) indicating the participant's opinion of the police; a higher value represents a more supportive opinion (1 = Very unfavorable, 2 = Somewhat unfavorable, 3 = Somewhat favorable, 4 = Very favorable)|
|news_sources_facebook|int64|Binary indicator of whether the participant said they use Facebook or Twitter as news source (0 = Doesn't use this news source, 1 = Uses this news source)|
|news_sources_cnn|int64|Binary indicator of whether the participant said they use CNN as news source (0 = Doesn't use this news source, 1 = Uses this news source)|
|news_sources_msnbc|int64|Binary indicator of whether the participant said they use MSNBC as news source (0 = Doesn't use this news source, 1 = Uses this news source)|
|news_sources_fox|int64|Binary indicator of whether the participant said they use FOX as news source (0 = Doesn't use this news source, 1 = Uses this news source)|
|news_sources_network|int64|Binary indicator of whether the participant said they use ABC, CBS, NBC, or PBS as news source (0 = Doesn't use this news source, 1 = Uses this news source)|
|news_sources_localtv|int64|Binary indicator of whether the participant said they use a local TV station as news source (0 = Doesn't use this news source, 1 = Uses this news source)|
|news_sources_telemundo|int64|Binary indicator of whether the participant said they use Telemundo or Univision as news source (0 = Doesn't use this news source, 1 = Uses this news source)|
|news_sources_npr|int64|Binary indicator of whether the participant said they use NPR as news source (0 = Doesn't use this news source, 1 = Uses this news source)|
|news_sources_amtalk|int64|Binary indicator of whether the participant said they use an AM talk radio station as news source (0 = Doesn't use this news source, 1 = Uses this news source)|
|news_sources_new_york_times|int64|Binary indicator of whether the participant said they use the New York Times as news source (0 = Doesn't use this news source, 1 = Uses this news source)|
|news_sources_local_newspaper|int64|Binary indicator of whether the participant said they use a local newspaper as news source (0 = Doesn't use this news source, 1 = Uses this news source)|
|news_sources_other|int64|Binary indicator of whether the participant said they use an unlisted source as news source (0 = Doesn't use this news source, 1 = Uses this news source)|
|employment_disabled|int64|Binary indicator of whether the participant is permanently disabled and unable to work (0 = Not disabled, 1 = Disabled)|
|employment_employed|int64|Binary indicator of whether the participant is currently employed (0 = Not employed, 1 = Employed)|
|employment_homemaker|int64|Binary indicator of whether the participant is currently a homemaker (0 = Not homemaker, 1 = Homemaker)|
|employment_part_time|int64|Binary indicator of whether the participant is currently working part-time (0 = Not working part-time, 1 = Working part-time)|
|employment_retired|int64|Binary indicator of whether the participant is currently retired (0 = Not retired, 1 = Retired)|
|employment_self_employed|int64|Binary indicator of whether the participant is currently self-employed (0 = Not self-employed, 1 = Self-employed)|
|employment_student|int64|Binary indicator of whether the participant is currently a full-time student (0 = Not student, 1 = Student)|
|employment_unemployed|int64|Binary indicator of whether the participant is currently unemployed (0 = Not unemployed, 1 = Unemployed)|
|foreign_born|int64|Binary indicator of whether the participant was born in the United States (0 = Not born in the U.S., 1 = Born in the U.S.)|
|language_english|int64|Binary indicator of whether the participant speaks English at home (0 = Doesn't speak English at home, 1 = Speaks English at home)|
|language_spanish|int64|Binary indicator of whether the participant speaks Spanish at home (0 = Doesn't speak Spanish at home, 1 = Speaks Spanish at home)|
|religion_evangelical|int64|Binary indicator of whether the participant is Evangelical (0 = Not Evangelical, 1 = Evangelical)|
|religion_agnostic|int64|Binary indicator of whether the participant is religiously agnostic (0 = Not agnostic, 1 = Agnostic)|
|religion_atheist|int64|Binary indicator of whether the participant is an atheist (0 = Not an atheist, 1 = Atheist)|
|religion_buddhist|int64|Binary indicator of whether the participant is Buddhist (0 = Not Buddhist, 1 = Buddhist)|
|religion_catholic|int64|Binary indicator of whether the participant is Catholic (0 = Not Catholic, 1 = Catholic)|
|religion_christian|int64|Binary indicator of whether the participant practices an unlisted form of Christianity (0 = Not Christian, 1 = Christian)|
|religion_hindu|int64|Binary indicator of whether the participant is Hindu (0 = Not Hindu, 1 = Hindu)|
|religion_jewish|int64|Binary indicator of whether the participant is Jewish (0 = Not Jewish, 1 = Jewish)|
|religion_mormon|int64|Binary indicator of whether the participant is Mormon (0 = Not Mormon, 1 = Mormon)|
|religion_muslim|int64|Binary indicator of whether the participant is Muslim (0 = Not Muslim, 1 = Muslim)|
|religion_none|int64|Binary indicator of whether the participant does not ascribe to any religious label (0 = Does ascribe to a label, 1 = Doesn't ascribe to a particular label)|
|religion_orthodox|int64|Binary indicator of whether the participant is religiously orthodox (0 = Not orthodox, 1 = Orthodox)|
|religion_protestant|int64|Binary indicator of whether the participant is Protestant (0 = Not Protestant, 1 = Protestant)|
|orientation_bisexual|int64|Binary indicator of whether the participant identifies as bisexual (0 = Not bisexual, 1 = Bisexual)|
|orientation_gay_man|int64|Binary indicator of whether the participant identifies as a gay man (0 = Not a gay man, 1 = Gay man)|
|orientation_heterosexual|int64|Binary indicator of whether the participant identifies as heterosexual (0 = Not heterosexual, 1 = Heterosexual)|
|orientation_lesbian|int64|Binary indicator of whether the participant identifies as a lesbian (0 = Not lesbian, 1 = Lesbian)|
|orientation_other|int64|Binary indicator of whether the participant identifies as an unlisted sexual/romantic orientation (0 = Not unlisted orientation, 1 = Unlisted orientation)|
|in_union_never|int64|Binary indicator of whether the participant has never been in a union (0 = Has been in a union, 1 = Has never been in union)|
|in_union_yes|int64|Binary indicator of whether the participant is currently in a union (0 = Not in union, 1 = Currently in union)|
|household_gun_owner_yes|int64|Binary indicator of whether the participant is a gun owner (0 = Not a gun owner, 1 = Gun owner)|
|household_gun_owner_no|int64|Binary indicator of whether the participant lives in a gun-free household (0 = Does not live in a gun-free household, 1 = Lives in gun-free household)|
|fc_smallgov|int64|Binary indicator of whether the participant favors a larger government with more services (0 = Favors smaller government, 1 = Favors larger government)|
|age|int64|Integer indicating the participant's age (18 - 93)|
|gender|int64|Binary indicator of whether the participant is female (0 = Male, 1 = Female)|
|region_northeast|int64|Binary indicator of whether the participant lives in the Northeastern region of the U.S. (0 = Doesn't live in region, 1 = Lives in region)|
|region_south|int64|Binary indicator of whether the participant lives in the Southern region of the U.S. (0 = Doesn't live in region, 1 = Lives in region)|
|region_west|int64|Binary indicator of whether the participant lives in the Western region of the U.S. (0 = Doesn't live in region, 1 = Lives in region)|
|race_ethnicity_asian|int64|Binary indicator of whether the participant is Asian (0 = Not Asian, 1 = Asian)|
|race_ethnicity_black|int64|Binary indicator of whether the participant is Black (0 = Not Black, 1 = Black)|
|race_ethnicity_native|int64|Binary indicator of whether the participant is Native (0 = Not Native, 1 = Native)|
|race_ethnicity_pacific_island|int64|Binary indicator of whether the participant is Pacific Islander (0 = Not Pacific Islander, 1 = Pacific Islander)|
|race_ethnicity_white|int64|Binary indicator of whether the participant is white (0 = Not white, 1 = White)|
|household_income|int64|Value between 1 and 24 indicating the participant's household income (1 = Less than $15k, 2 = $15-20k, 3 = $20-25k, 4 = $30-35k, 5 = $35-40k, 6 = $40-45k, 7 = $45-50k, 8 = $50-55k, 9 = $55-60k, 10 = $60-65k, 11 = $65-70k, 12 = $70-75k, 13 = $75-80k, 14 = $80-85k, 15 = $85-90k, 16 = $90-95k, 17 = $95-100k, 18 = $100-125k, 19 = $125-150k, 20 = $150-175k, 21 = $175-200k, 22 = $200-225k, 23 = $225-250k, 24 = More than $250k)|
|education|int64|Description (1 = Less than 4th grade, 2 = Completed middle school, 3 = Some high school, 4 = Completed high school, 5 = Vocational training, 6 = Some college, 7 = Associate degree, 8 = Bachelor's degree, 9 = Some grad school, 10 = Masters degree, 11 = Doctorate degree)|
|state_XX|int64|Binary indicator of whether the participant lives in a state, represented by the abbreviation in the column name (0 = Doesn't live in state, 1 = Lives in state)|


## Modeling and analysis

I went through a very similar modeling process for each dataset. 

1. I tried a bunch of regressor models with default parameters in a rapid fire fashion to see which type seemed best for this type of project. I chose the top 4 performing models from the several that I tried. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a. For the Pew data which I was using to predict support for defunding the police, I chose the following models: linear, lasso, gradient boost, and ada boost with gradient boost as the base estimator. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;b. For the UCLA data which I was using to predict favorability of the police, I used the following: linear, lasso, random forest, and gradient boost. 

2. I used GridSearchCV to determine the best parameters for those four models. Using those parameters, I extracted the coefficients and feature importances from the models to narrow my feature list in an attempt to make the models more generalizable. It helped a bit!

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a. For the Pew dataset, I got my best score yet with the Gradient Boost model: a pretty unimpressive 0.2310.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;b. For the UCLA dataset, I also beat the previous best score but with my lasso model which had a testing score of 0.2746.

3. I wanted to ensemble the models if possible, so I used a voting regressor to combine the wisdom of my top four models. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a. For the Pew dataset, the voting regressor came really close to the best score yet, but the Gradient Booster with narrowed features still won out. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;b. For the UCLA dataset, the voting regressor was the best score yet with a still not very impressive 0.2794.

4. I used a loop to determine if different numbers of features worked best for each type of model and found that the linear and lasso models performed better with more features relative to the other models. I re-ran the models with these ideal cutoffs and got mixed results.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a. For the Pew dataset, I got my best score yet (0.2325 lol) from a Gradient Boost model with 31 features.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;b. For the UCLA dataset, I came close but did not surpass the voting regressor score.

5. I wanted to use those unique cutoffs for each model but still ensemble them to pool their wisdom. However, the voting regressor doesn't allow different features for each of the models it's ensembling, so I decided to manually ensemble the scores from those models with varied features. I used the test scores from the previous step as weights, generated predictions from each model, and then calculated a weighted average for each prediction. In both cases, this manual ensemble outperformed sklearn's VotingRegressor.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a. For the Pew dataset, the Gradient Boost model with 31 features remained the top performing and final model.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;b. For the UCLA dataset, the manual ensemble produced the best score yet of 0.2841. The pooled predictions were calculated using the predictions from linear, lasso, random forest, and gradient boost models with 46, 46, 32, and 18 features respectively. 

## Final Models:

### [Pew](code/2a_modeling_defunding_support.ipynb)

**Top model:** Gradient Boost model with 31 features

**Best testing score:** 0.2325

**Top ten features:**
|Feature|Importance|
|---|---|
|political_party_gop|0.274|
|age_group|0.1076|
|political_party_dem|0.1005|
|education_level|0.0582|
|income_bracket|0.0436|
|religion_atheist|0.0426|
|religion_protestant|0.041|
|religion_agnostic|0.038|
|relig_activity|0.0333|
|religion_catholic|0.0266|


### [UCLA](code/2b_modeling_police_favorability.ipynb)

**Top model:** Manual ensemble of linear, lasso, random forest, and gradient boost models with 46, 46, 32, and 18 features respectively

**Best testing score:** 0.2841

**Top ten features:**
|Feature|Average coefficient|
|---|---|
|age|0.0909|
|race_ethnicity_black|0.0565|
|household_income|0.0349|
|race_ethnicity_white|0.03|
|religion_atheist|0.028|
|fc_smallgov|0.0259|
|news_sources_cnn|0.0235|
|news_sources_msnbc|0.0228|
|household_gun_owner_no|0.0228|
|news_sources_npr|0.0218|
|education|0.0212|
|religion_agnostic|0.013|
|employment_employed|-0.0124|
|religion_none|0.0113|





