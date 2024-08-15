# ETL-Process-for-BI-project-for-match-stats-from-League-of-Legends-game
A simple ELT process for a League of Legends match data file, implemented in JupyterLab using pandas, with a custom parser for data from a single match.


Data Cleaning and Transformation Process:

1. Anomaly Removal:

- Filter out games that:
  - Have fewer than 10 players.
  - Last less than 5 minutes.
  - Are from non-ranked queues (e.g., ARAM).
  - Are from older versions, which are too few to analyze.
  - Contain null data (13 such cases identified).

2. Column Removal: Drop columns not used in the model, such as gameId, gameType, mapId, gameMode, and participantIdentities.

3. Participant Data Unpacking: Split the participants column, which holds objects containing player statistics, into individual columns. Each player has over 150 attributes.

4. Row Expansion: Transform each row into 10 rows, where each row represents a single player's data in a match.

5. Column Pruning: Retain only relevant columns from the player statistics object, including:
stats.win, stats.kills, stats.deaths, stats.assists, stats.firstBloodKill, stats.visionScore, stats.pentaKills, stats.goldEarned, stats.damageDealtToTurrets, and stats.totalDamageDealtToChampions.

6. Additional Transformations:

- Create a gameDurationDiscrete column from gameDuration with categories: short (<20 min), normal (20-30 min), and long (>30 min).
- Extract gameDurationMinutes from gameDuration.
- Generate a stats.kdaDiscrete column based on the formula (kills + assists) / deaths and categorize as:
  - Low: kdaDiscrete <= 1
  - Medium: 1 < kdaDiscrete < 2
  - High: kdaDiscrete >= 2
- Create a gameCreationMonth column from gameCreation.
- Add an isWeekend column based on the gameCreation date.
- Simplify gameVersion to include only characters up to the second dot.

7. Further Cleanup:

- Remove unnecessary columns such as stats.kills, stats.assists, gameCreation, and gameDuration.
- Drop rows containing null values.

8. Dimensional Modeling:

- Extract relevant columns into separate dimension tables, removing duplicate rows.
- Create foreign keys in the original fact table to link to these dimensions.
