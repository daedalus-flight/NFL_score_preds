## Predicting NFL Game Scores to Estimate Potential Wagering Opportunities

---

### Problem Statement

*State lawmakers around the country may look to legalize sports betting as a way to raise revenue. Commercial casinos generated over $10 billion in state and local gaming tax revenue last year, and that doesn’t include sales and income tax tied to casino gaming. State governments have gotten addicted to the lucrative tax revenue from casinos; legalizing sports betting is the next logical way to get a little more juice out of the same orange. - Forbes Magazine July 1, 2020*

As someone who is optimistic about participating in the long term prospects of the sports gambling industry, an essential first step in understanding the industry better is to examine the factors that are most important to oddsmakers in determining point spreads and over/under lines for NFL games.

---
### Executive Summary


Using a multi-layered, "top-down", approach towards predicting the scores of NFL games, the prediction models did fairly well considering the task. Classification models are more suited to determining a "winning" event but my goal is to lay a strong foundation for future iterations where I can compare these predictive scores to the point spreads and over/under lines set by oddsmakers and, later on, add more granular features such as individual player stats and weather conditions. In the curent version, the models predict target team scores by using the following features:

* team's total points scored vs total points allowed data (ps/pa)
* ps/pa, offensive yards gained, offensive yards allowed. (Passing and Rushing Yards)

##### Some Quick Definitions:

__Point Spread:__ the projected amount by which the favored team (favorite) is expected to beat its opponent (underdog). The point spread serves as an equalizer that sets odds of both teams winning to 50%.  (eg. typical point spread would be NE Patriots (-11) vs. NY Jets)
__Over/Under:__ the projected sum total of both teams’ scores. 

__Example:__ NY Jets 13  at NE Patriots 53 | Jets (-11) Over/Under = 52
- The patriots won by 40 (>11) so your bet would win if you bet on the Patriots to win.
- The sum total was 66 so you would win if you bet the over



---
### Data

Data consists of 2 pared down data sets from Kaggle using data from 2010 to 2016.  
- Intitial player stats data was over 1 million lines. Performed groupby's and data checks to confirm accuracy of stats and non-nulls.
- Scores data provided target team and opponent team game date, location, betting odds and weather and was used to map appropriate stats for modeling - ie. opponent team mapped to target team.


#### Data Dictionary

 |Column|Datatype|Description|
 |---|---|---|
 |id               |object |Unqiue id (target team and game date)
 |schedule_date    |object |Game Date
 |schedule_season  |int64  |Season Year
 |schedule_week    |int64  |Season Week
 |target_team      |object |Team score to be predicted
 |score            |float64|Target team score
 |opponent_score   |float64|Opponent team score
 |opponent_team    |object |Opponent team
 |win_margin       |float64|Target team point margin
 |passing_yards    |int64  |Target team passing yards
 |rushing_yards    |int64  |Target team rushing yards
 |opp_passing_yards|int64  |Opponent team passing yards
 |opp_rushing_yards|int64  |Opponent team
 |home_team        |object |Game location Team
 |team_favorite_id |object |Team favored to Win
 |spread_favorite  |float64|Point spread amount
 |over_under_line  |float64|Sum total of both teams line
 |game_number      |int64  |Target team's game # of season
 |home_yes         |int64  |Was the target team home (binary)
 |pf_7mva          |float64|Target team 7-week moving average points scored
 |pa_7mva          |float64|Opponent team 7-week moving average points scored
 |margin_7mva      |float64|Target team 7-week moving average points scored
 |pyards_7mva      |float64|Home Team passing yards 7-week moving average points scored
 |ryards_7mva      |float64|Home Team rushing yards 7-week moving average points scored
 |opp_pyards_7mva  |float64|Opponent team passing yards 7-week moving average points scored
 |opp_ryards_7mva  |float64|Opponent team rushing yards 7-week moving average points scored
 
 
 ### Conclusions and Takeaways
 ---
 
 
AdaBoost (MAE: 5.28, Test Score: 0.63) and Sequential ANN (MAE: 3.00, Test Score: 0.80) performed very well considering the task. GradientBoost returned perfect scores but must be further evaluated as "too good to be true" until proper justified as there were 'learning rate' and 'n_estimator' balancing acts to be considered.

The project was a success in that it established a firm base for creating a more complicated and revealing model framework.

#### Next Steps

- Add more intricate data sets including individual player stats, possibly through an Elo ranking system, along with weather.
- Enhancing and working with multidimensional models: time series with exogenous factors time series(Arima, Facebook Prophet), Bayesian and RNN.
- Back Testing
- Applying the process to less followed sports to take advantage of alpha opportunities.


---
#### References

https://www.kaggle.com/tobycrabtree/nfl-scores-and-betting-data?select=spreadspoke_scores.csv

https://www.kaggle.com/zynicide/nfl-football-player-stats

---
File workflow

- 1.nfl_scores_main_w_dist (EDA: distribution plots)
in [origin file]: nfl = pd.read_csv('./data/spreadspoke_110820.csv', index_col = "schedule_date", parse_dates = True)
out: nfl_ha.to_csv('./data/nfl_scores_agg.csv’)

- 2.nfl_first_merge (two inputs files)
in: pstats = pd.read_csv('./data/player_stats_2005.csv', index_col = "date", parse_dates = True)
in: nfl_scores = pd.read_csv('./data/nfl_scores_agg.csv', index_col=0)
out: nfl_agg_agg.to_csv('./data/nfl_merged_all.csv’)

- 3.nfl_opp_yard_map
in: y_map = pd.read_csv('./data/nfl_merged_all.csv', index_col = 0)
out: nfl_fin.to_csv('./data/nfl_fin.csv', index = False)

- 4.nfl_model_setup
in: nfl_ma = pd.read_csv('./data/nfl_fin.csv’)
nfl7mva.to_csv('./data/nfl_7mva.csv’) 

- 5.nfl_models_fin
in: nfl = pd.read_csv('./data/nfl_7mva.csv', index_col = 'schedule_date')