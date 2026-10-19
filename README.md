# Tennis-Match-Analysis

Exploratory data analysis of about two months of professional tennis match data (Feb–Mar 2024), built with Python. The project covers the full workflow: consolidating raw daily snapshots, cleaning duplicated and inconsistent records, and answering 17 analytical questions plus several optional ones about players, serve statistics, match duration and tournaments.

**Tools:** Python, Pandas, NumPy, Matplotlib, Seaborn, Jupyter

---

## Dataset

The raw data is a folder of 60 daily `.zip` files. Each zip contains many `.parquet` files, one per match per table type, named `<table>_<match_id>.parquet` (e.g. `home_team_11998445.parquet`).

| Table | Content |
|---|---|
| `match` | Match-level info (winner, start time, first server, ...) |
| `home_team` / `away_team` | Player profile (height, weight, plays, rank, country, prize, ...) |
| `home_score` / `away_score` | Games per set |
| `time` | Duration of each set (seconds) |
| `period` | Per-period statistics (aces, double faults, ...) |
| `pbp` (GameInfo) | Point-by-point data (~4.9M rows before cleaning) |
| `power` | Break-of-serve flags per game |
| `tournament`, `round`, `venue`, `season`, `odds`, `votes` | Context tables |

Scale after consolidation: ~16.9K raw match IDs, 2,644 unique players, 100 countries.

---

## Data cleaning highlights

Most of the effort went into making the data trustworthy before analysing it:

- **Duplicate snapshots.** A match can appear in several daily snapshots. Full-row `drop_duplicates()` was not enough because dynamic columns (e.g. `user_count`, `current_prize`) differ between snapshots, so records were deduplicated on `match_id`.
- **Incomplete vs. final versions.** For some matches an early snapshot has `winner_code = NaN` while a later one has the result. `keep="last"` keeps the most complete version.
- **Missing vs. outlier.** Matches with no timing data at all would get a duration of 0 under `sum(skipna=True)`. These were treated as missing data, not as very short matches.
- **NaN in comparisons.** `NaN == x` is always `False`; rows with missing `first_to_serve` were wrongly counted as "receiver won" until NaNs were dropped explicitly.
- **Duplicated join keys.** Merging point-level data with a two-rows-per-match player table multiplied counts several times. A one-row-per-match lookup is built before joining.
- **Conflicting names.** 7 `player_id`s have more than one `full_name`; names are mapped to one canonical value per ID and grouping is done on `player_id` only.

---

## Selected findings

| Question | Result |
|---|---|
| Unique players | 2,644 (86% appear as both home and away) |
| Average height | 1.82 m overall; 1.84 m men vs 1.73 m women (≈49% missing) |
| Most wins | Popko, Dmitry — 30 wins in the period |
| Typical number of sets | 2 sets (≈72% of matches), consistent with best-of-3 |
| Average aces per match | 5.39 (median 4, right-skewed) |
| Most titles in one month | 3 titles, shared by six players (Feb–Mar 2024) |
| Games per set | Men ≈ 9.5 vs women ≈ 9.1 |
| Left- vs right-handed | ≈ 88% right-handed, ≈ 12% left-handed (≈57% missing) |
| Height vs ranking | Correlation ≈ 0.1 (very weak) |
| Higher-ranked player wins | ≈ 65% of matches |
| Most successful country | Depends on the metric; France leads on total titles and number of successful players |

All results are descriptive, not causal, and are limited to a two-month window with partly missing player attributes.

---

## Limitations

- Only two months of data; "most wins" or "most titles" reflect activity in that window, not careers.
- Several player attributes are heavily missing (height ≈ 49%, handedness ≈ 57%), so those results cover only players with data.
- Findings are observational and should not be read as causal.

---

### 👤 Contact
* **LinkedIn:** [My LinkedIn Profile](https://www.linkedin.com/in/zahrasadr)
* **Portfolio:** [My GitHub Profile](https://www.github.com/zsadr67)

*Designed by ZahraSadr*
