# Data

The shot data file (shots_2024_25.csv) is not stored in this repo because of its size, and the .gitignore file excludes CSV files in this folder. Follow the steps below to recreate it.

## Source

Shot-level data from NBA Stats, pulled with the [nba_api](https://github.com/swar/nba_api) package using the ShotChartDetail endpoint.

- Season: 2024-25
- Season type: Regular Season
- Context measure: FGA (all field-goal attempts, so both makes and misses are included)

## How to recreate it

The pulling code is in the first section of notebooks/shot_analysis.ipynb. It requests each of the 30 teams separately, waits a couple of seconds between requests, combines the results, and saves the file as data/shots_2024_25.csv.

I pulled by team because requesting the whole league in one call silently cut off the data (it returned exactly 102,400 rows, covering only 577 of 1,230 games).

When you read the file back in, read the GAME_ID column as text so the leading zeros are kept:

```
shots_all = pd.read_csv('../data/shots_2024_25.csv', dtype={'GAME_ID': str})
```

## What the final dataset looks like

- 219,527 shots (one row per field-goal attempt)
- 1,230 games, 566 players, 30 teams
- Dates from 2024-10-22 to 2025-04-13
- 24 columns

## Columns used in the analysis

| Column | Meaning |
|---|---|
| GAME_ID, GAME_DATE | Game identifier and date |
| PLAYER_ID, PLAYER_NAME | Player (grouped by ID, since players can be traded mid-season) |
| TEAM_NAME, PERIOD | Team and quarter |
| SHOT_TYPE | 2PT Field Goal or 3PT Field Goal |
| SHOT_ZONE_BASIC | Court zone (Restricted Area, In The Paint (Non-RA), Mid-Range, Left Corner 3, Right Corner 3, Above the Break 3, Backcourt) |
| SHOT_DISTANCE | Shot distance in feet |
| LOC_X, LOC_Y | Court coordinates of the shot |
| SHOT_MADE_FLAG | 1 if the shot was made, 0 if missed |

## Columns I added

| Column | Meaning |
|---|---|
| SHOT_VALUE | 2 or 3, based on SHOT_TYPE |
| POINTS | SHOT_VALUE if the shot was made, otherwise 0 |

## Notes

- nba_api is community-maintained and not an official NBA product, so the endpoint could change or return different data in the future.