# Which Movie Genre is the Most Timeless?


> How long does a title stay trending — and when is the optimal moment to promote it?

This project models content trend shelf life using Wikipedia daily pageviews as a public content demand signal, applies survival analysis to quantify decay rates by genre, and outputs data-driven promotion window recommendations for platform schedulers.

---

## Project Summary

Wikipedia pageview counts are a public proxy for content demand: they capture **intent-driven search behavior** (people actively looking up a film) and are freely available for any title on any platform. Unlike internal play counts, they're reproducible by anyone with internet access, which makes them ideal for an analytical prototype.

The core analytical insight is that different content types have fundamentally different shelf-life distributions, not just different average durations. This project serves to suggest that Genre-specific promotion timelines are necessary to avoid wasting budget on content that's already past its peak, or missing the window for content that slowly gains traction.

---
## Data Pipeline

### 1. `collect_movies.py`

Pulls American movies from TMDB across 5 genres (Action, Drama, Sci-Fi, Comedy, Horror) and 3 budget tiers (high/mid/low). Budget thresholds are calibrated per genre using films sampled across popularity bands to reduce selection bias. Randomized sampling within each band ensures the dataset isn't dominated by the most culturally prominent titles. Writes to the `movies`, `genre_budget_thresholds`, and `collection_log` tables in SQLite.

### 2. `collect_pageviews.py`

For every movie in the `movies` table, fetches daily Wikipedia pageviews covering 14 days before release through 210 days after. Tries alternate Wikipedia title formats (e.g. `Title (film)`, `Title (2023 film)`) if the primary title isn't found. Logs failures to `pageview_failures` for manual follow-up. Writes to the `pageviews` table.

### 3. `patch_pageviews.py`

One-off helper for movies that failed during the main collection run. Edit the three variables at the top of the file (`MOVIE_ID`, `WIKI_TITLE`, `RELEASE_DATE`) and run it to insert a single movie's pageviews manually.

---

## Analytical Methods

| Step | Method | Library |
|---|---|---|
| Trend death definition | 7-day rolling avg < 20% of peak for 2+ consecutive weeks | `pandas` |
| Threshold sensitivity | KM curves at 15%, 20%, 25% | `lifelines` |
| Shelf life by genre | Kaplan-Meier survival curves + pairwise log-rank tests | `lifelines` |
| Covariate effects | Cox Proportional Hazards with Schoenfeld residual check | `lifelines` |

---

## Cox Proportional Hazards Model Features

| Feature | Source | Description |
|---|---|---|
| `genre` | TMDB | One-hot encoded; baseline = Action |
| `budget_tier` | Derived from `budget_usd` | Categorized into low/mid/high; one-hot encoded |
| `runtime` | TMDB | Runtime in minutes |
| `is_franchise` | TMDB | 1 if part of a collection, 0 otherwise |
| `release_season` | Derived | Q1/Q2/Q3/Q4 from release date, one-hot encoded |
| `early_velocity` | Wikipedia days 1–7 | OLS slope of first-week pageviews |

---

## Key Findings

- **Drama is the standout genre.** Drama titles have roughly half the weekly hazard rate of action titles (Cox HR = 0.537, p = 0.032), which is the only feature that reaches statistical significance. Pairwise log-rank tests confirm drama's shelf life is significantly longer than action (p = 0.004), comedy (p = 0.021), and horror (p = 0.010).
- **The typical promotion window is 5–7 weeks** Median survival across all genres is 6 weeks. Scheduling teams have a meaningful window to act — the risk is failing to align promotion spend to genre-specific decay curves.
- **First-week buzz does not predict long-term shelf life.** Despite intuitive appeal, `early_velocity` has a near-null hazard ratio (HR ≈ 1.000), meaning launch interest and sustained engagement are driven by different dynamics.
- **Budget, runtime, and most release seasons show no significant effect** on trend duration at this sample size (all p > 0.05).
- **Concordance index = 0.635** the Cox model with our sample size is modestly better than chance for ranking titles by shelf life but not a strong individual predictor. It is directionally informative for genre-level promotion planning.
