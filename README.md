# Drama Movies Outlast the Competition: How Genre Shapes the Shelf Life of Public Interest in Film

> How long does a title stay trending — and when is the optimal moment to promote it?

This project models content trend shelf life using Wikipedia daily pageviews as a public content demand signal, applies survival analysis to quantify decay rates by genre, and outputs data-driven promotion window recommendations for platform schedulers.

---

## Project Summary

Wikipedia pageview counts are a public proxy for content demand: they capture **intent-driven search behavior** (people actively looking up a film) and are freely available for any title on any platform. Unlike internal play counts, they're reproducible by anyone with internet access, which makes them ideal for an analytical prototype.

The core analytical insight is that different content types have fundamentally different shelf-life distributions, not just different average durations. This project serves to suggest that Genre-specific promotion timelines are necessary to avoid wasting budget on content that's already past its peak, or missing the window for content that slowly gains traction.

---
## Dataset Sourcing

TMDB API — A community-built, openly editable database (not a government or academic source), reliable for commercially released American films, with known limitations around budget accuracy and genre simplification.

Wikimedia Pageviews API — From the Wikimedia Foundation (a 501(c)(3) non-profit), with emphasis on its transparency, reproducibility, academic credibility, and lack of commercials. Some generally accepted challenges include bot filtering, English-language skew, and the gap between curiosity and actual viewing behavior.

Dataset construction challenges — Mainly sample size limitations and selection criteria biases
---

## Data Analysis

This section is extensively covered in the Python notebook found in the repository (This includes visualizations as well).

---

## Ending Summary, Ethical Concerns, and Reporting Process

### Ethical Concerns

**Genre stereotyping and cultural hierarchy.** A finding like "drama lasts longer" risks reinforcing longstanding cultural hierarchies that position drama as a "serious" or "prestige" genre and treat action, horror, and comedy as disposable entertainment. This is not what the data shows. The analysis measures *Wikipedia search interest*, not artistic merit, cultural value, or audience satisfaction. A horror film that trends intensely for three weeks and then fades may have delivered exactly the experience its audience wanted.

**Misrepresenting audience behavior.** Wikipedia pageviews are a measure of curiosity, not consumption. Equating search interest with viewership could mislead decision-makers into under-investing in genres whose audiences engage heavily but search less on Wikipedia.

**Population and language bias.** Wikipedia's readership skews toward English-speaking, higher-income, internet-connected populations. This means the "public interest" we are measuring is not universal public interest — it is predominantly the interest of a specific demographic slice. Films that resonate strongly with non-English-speaking audiences, older demographics, or communities with lower internet access may appear to trend less in this data, not because they are less popular, but because the measurement instrument does not capture their attention.

### What Additional Reporting Would Be Needed

1. **Validation against actual viewership data.** The most critical issue is that Wikipedia pageviews are a proxy, not a direct measure of movie watching. A responsible story would either obtain or reference actual streaming or box office data to confirm whether Wikipedia attention patterns track real consumption.

2. **Expanded and diversified sample.** A journalistically rigorous version of this analysis would expand the dataset beyond 143 American theatrical releases to include international films, direct-to-streaming releases, documentaries, and animated features. It would also extend the observation window beyond 20 weeks to capture the full lifecycle of slow-burn titles.

3. **Reproducibility and transparency.** All code, data, and methodology in this project are open and reproducible, so anyone can query the same APIs and verify the results. A published story should link to the repository and invite scrutiny, which is the journalistic equivalent of showing your work.

---

## Key Findings

- **Drama is the standout genre.** Drama titles have roughly half the weekly hazard rate of action titles (Cox HR = 0.537, p = 0.032), which is the only feature that reaches statistical significance. Pairwise log-rank tests confirm drama's shelf life is significantly longer than action (p = 0.004), comedy (p = 0.021), and horror (p = 0.010).
- **The typical promotion window is 5–7 weeks** Median survival across all genres is 6 weeks. Scheduling teams have a meaningful window to act — the risk is failing to align promotion spend to genre-specific decay curves.
- **First-week buzz does not predict long-term shelf life.** Despite intuitive appeal, `early_velocity` has a near-null hazard ratio (HR ≈ 1.000), meaning launch interest and sustained engagement are driven by different dynamics.
- **Budget, runtime, and most release seasons show no significant effect** on trend duration at this sample size (all p > 0.05).
- **Concordance index = 0.635** the Cox model with our sample size is modestly better than chance for ranking titles by shelf life but not a strong individual predictor. It is directionally informative for genre-level promotion planning.
