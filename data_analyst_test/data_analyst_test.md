# Data Analyst Test Solution

Note:

* You will evaluated on this test considering you have spent about 3 hours on the test
* There are three tasks in the test in total

## Task 1

Please review and, where necessary, make corrections to the following query :

```SQL
WITH daily_data AS (
    SELECT game
    ,player_segment
    , date_diff('day', first_purchase_date, event_date) AS days_since_conversion
    , COUNT(1) AS players
    FROM profile
    LEFT JOIN daily ON daily.player_id = profile.player_id
    WHERE first_purchase_date IS NULL
        AND date_diff('day', first_purchase_date, event_date) >= 0
    GROUP BY 1, 2
) 
SELECT *
    , ROUND(players * 1.0 / MAX(players) OVER (PARTITION BY player_segment) ,3) AS retention
FROM daily_data
```

## Response 1

I'm assuming that the date_diff sintxis is correct, but it might change depending on the SQL language. In particular in several cases would be DAY instead of 'day' for the units. Also some changes of format or parenthesis have been added, even if they are not mandatory just for easier understanding when reading the code.

```SQL
WITH daily_data AS (
    SELECT profile.game
    , player_segment
    , date_diff('day', first_purchase_date, event_date) AS days_since_conversion
    , COUNT(1) AS players
    FROM profile
    LEFT JOIN daily
        ON daily.player_id = profile.player_id AND daily.game = profile.game
    WHERE first_purchase_date IS NOT NULL
        AND date_diff('day', first_purchase_date, event_date) >= 0
    GROUP BY 1, 2
) 
SELECT game
    , player_segment
    , days_since_conversion
    , ROUND(players * 1.0 / (MAX(players) OVER (PARTITION BY game, player_segment)) , 3) AS retention
FROM daily_data
ORDER BY 1, 2, 3
```

## Task 2

You can find data for 4 games in soft launch phase

1. How would you comment on the daily conversion rates of the games above from the given data?
2. What are the differences between games A and C? How would you comment on these differences?
3. What are the differences between games B and C? How would you comment on these differences?
4. How would you comment on game D in general? What are some possible improvement points?
5. Our goal is to maximize our lifetime profits. Keeping this in mind, which game would you choose to launch? You can assume that the costs are identical across these titles. Please make sure to show your calculation and reasoning. If the data is not enough to make a decision, feel free to list any data you would need to make a comment.
   1. Daily ARPU -> Average Revenue per User (at a daily level per Daily Active User)
   2. Daily ARPPU -> Average Revenue per Paying User

## Response 2

1. Daily ARPU = Revenue/DAU, Daily ARPPU = Revenue/Paying Users. Conversion = ARPU/ARPPU.

   * A. 2.5%
   * B. 5%
   * C. 2%
   * D. 12%

Conversion of game A and C are closer to the standard in Puzzle, conversion B is good and from a well performing Puzzle game. Conversion D is really strong, could be from a different genre.
2. Game A is slightly better than C. D1 retention is better for C, but after that Game A improves in comparison. Game A has a better late retention (D30) that can also influence the higher ARPDAU, ARPPU and better daily conversion.
3. Game B is clearly better than game C in monetisation and retention. However it has a problem of retention in D30, there is a drastical drop in comparison to the D7 retention specially when looking at game C. These problems in retention could be related to lack of new content for older players or a too tight economy for older players.
4. Game D has a similar issue to game B in the sense of having really strong KPIs but a really poor D30 retention. Considering how high the conversion is, it is quite likely that the game becomes too difficult without purchases for players on D30 and probably earlier (at least after D7, but we don't know when the issue starts). THe recommendation here would be to balance the economy for players in the late game and see if that brings improvements in retention for the long term. As a second option lack of content could be revised.
5. In principle, I would be leaned to launch game B looking at how healthy the KPIs are. However, there is not enough information to make the decision. For example we could make the assumption that ARPDAU and conversion distribute uniformly across different days, but that's probably incorrect. On top of that we don't know the amount of players in each one of these games, scale can have a huge impact on the KPIs. To understand better which game to launch we would need at least:

* Total amount of players and total revenue per game
* ARPU and ARPPU by retention day
* Some additional points like D14 and D60, ideally until D90. With these we are in condition to fit a curve for the expected revenues also in future periods and proyect it to 180 or 360 days.

## Task 3

In the zip folder, you can find an excel named datafile with some fictitious game DAU data.
Your task is to give a 5-minute presentation (1-2 slider) on any interesting stories you find in the data. The audience for the presentation will be other game analysts.

## Response 3

Solutions can be found in [data_analyst_test notebook](./data_analyst_test/../data_analysis_test.ipynb).
