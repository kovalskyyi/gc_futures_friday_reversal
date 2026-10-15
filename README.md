# Gold Friday Retracement Research

Does Friday tend to reverse the direction established earlier in the week?

This project explores that question using daily OHLC data for COMEX gold futures
(`COMEX:GC1!`). The main finding is that Fridays were more often positive in this
sample, but we did not find evidence that the direction of the preceding four-day
move provided additional information about Friday's direction.

## Initial hypothesis

The initial idea was that Friday might be more likely to reverse than continue
the preceding four-day move. In particular, after a bearish Monday–Thursday
narrative, would Friday be more likely to move upward?

Here, “narrative” simply means the direction of the net price move through
Thursday. It does not require every preceding day to move in the same direction.
The project investigates this relationship rather than developing a trading strategy.

## Data and definitions

- **Dataset:** 3,975 daily observations, covering trading dates from January 4,
  2010 to October 15, 2025.
- **Session dates:** the original timestamps mark evening session openings.
  They are interpreted as UTC and map to 17:00 in Chicago. Dates are shifted to
  the following trading date, so Sunday openings belong to Monday sessions.
  The source CSV does not explicitly declare its timezone.
- **Flat candle:** the single flat candle is retained. Neutral Friday outcomes
  remain in the data but are excluded from reversal-versus-continuation percentages.
- **Friday move:** Friday close minus Friday open. Percentage moves use Friday
  open as the denominator.
- **Reversal:** Friday's move has the opposite sign to the preceding narrative.
  This means an opposite-direction day, not necessarily a full recovery of the
  preceding move.
- **Continuation:** Friday's move has the same nonzero sign as the narrative.

Two definitions of the preceding move are tested:

| Specification | Four-day move | Required sessions |
|---|---|---|
| Main: includes weekend gap | Current Thursday close − previous Friday close | Previous Friday and current Monday, Thursday and Friday |
| Robustness: excludes weekend gap | Current Thursday close − current Monday open | Current Monday, Thursday and Friday |

Tuesday and Wednesday are not required to calculate these endpoint moves.
The previous Friday must be from the immediately preceding calendar week.
Percentage versions divide each move by its respective starting price.

There are **723 eligible weeks** in the main specification and **763** in the
robustness specification. Each contains three neutral Fridays, leaving **720**
and **760** directional outcomes for the frequency comparisons.

## What we tested

1. Reversal and continuation frequencies overall and separately after short
   and long narratives.
2. Two-sided exact 95% confidence intervals for reversal probabilities.
3. A one-sided binomial test of whether reversal after a short narrative is
   more likely than 50%.
4. A two-sided Fisher's exact test comparing reversal frequencies after short
   versus long narratives.
5. A permutation test asking whether narrative direction is associated with
   Friday direction after accounting for the observed Friday upward bias.

Scatter plots also compare the signed four-day percentage move with Friday's
percentage move. They show no obvious linear relationship; correlations are
close to zero. Relationships involving move size or volatility were not tested
formally.

## Results

### Overall reversal frequency is close to 50%

| Specification | Reversal | Continuation |
|---|---:|---:|
| Main | 50.28% | 49.72% |
| Robustness | 50.79% | 49.21% |

### Reversal is more frequent after a short narrative

| Specification | Prior narrative | Reversals / directional weeks | Reversal frequency | Exact 95% CI |
|---|---|---:|---:|---:|
| Main | Short | 188 / 337 | 55.79% | 50.30–61.17% |
| Main | Long | 174 / 383 | 45.43% | 40.37–50.57% |
| Robustness | Short | 205 / 365 | 56.16% | 50.90–61.32% |
| Robustness | Long | 181 / 395 | 45.82% | 40.83–50.88% |

The one-sided binomial test finds evidence that reversal after a short narrative
exceeds 50%: **p = 0.0191** for the main specification and **p = 0.0106** for
robustness.

The difference in reversal frequency between short and long narratives is about
**10.35 percentage points**. Fisher's exact test finds this difference statistically
significant at the 5% level: **p = 0.0058** and **p = 0.0047**, respectively.

### The permutation test does not support an additional narrative effect

The reversal-rate difference needs careful interpretation. A positive Friday is
a **reversal after a short narrative**, but a **continuation after a long narrative**.
Consequently, an overall upward bias in Fridays can produce different reversal
rates even when Friday direction is independent of the preceding narrative.

The permutation test keeps the narratives fixed and randomly shuffles Friday
moves between weeks without replacement. It preserves the actual Friday move
distribution, including its upward bias. We run 10,000 shuffles with a fixed seed.

The statistic is the difference between the positive-Friday rate after a short
narrative and the positive-Friday rate after a long narrative. Success therefore
means **Friday up in both groups**.

| Specification | Friday up after short | Friday up after long | Difference | Two-sided permutation p-value |
|---|---:|---:|---:|---:|
| Main | 55.79% | 54.57% | +1.22 p.p. | 0.7683 |
| Robustness | 56.16% | 54.18% | +1.99 p.p. | 0.6039 |

Neither result is statistically significant. We do not have sufficient evidence
to conclude that narrative direction is associated with Friday direction under
this test. This does not prove that no relationship exists.

## Interpretation and practical relevance

The supported descriptive observation is that **Fridays were more often positive
than negative in this dataset**. Across all available non-neutral Fridays, 427 of
779 were positive: **54.81%**. This full-sample percentage is descriptive; the
binomial tests above concern the short-narrative subgroup.

It is accurate to report the observed 55.79% positive-Friday rate after a short
narrative. However, the permutation test does not support presenting the short
narrative as an additional reason to expect a positive Friday.

For practical use, the finding is therefore a possible general Friday upward
tendency to investigate further, rather than a confirmed conditional retracement
signal. This research does not establish profitability or a trading rule.

The long-term behavior of gold is one possible explanation for the upward bias,
but it was not tested here and does not automatically explain Friday open-to-close
returns.

## Limits of the conclusions

- The binomial and Fisher tests assume independent observations. The binomial
  intervals also assume a constant success probability.
- Global permutation assumes weeks are exchangeable under the null hypothesis;
  it does not preserve serial dependence or changes in market regimes.
- Results are exploratory, with unadjusted p-values, because the same data were
  inspected during EDA. The robustness sample overlaps the main sample and is
  not an independent replication.
- The permutation test evaluates **directional association**, not all possible
  relationships involving return magnitude, volatility or particular subperiods.
- Findings apply to this GC futures dataset and the definitions used here.

## Project files

- [Research notebook](notebooks/friday_retracement_xau.ipynb): data preparation,
  feature engineering, EDA, confidence intervals and statistical tests.
- [Cleaned daily data](dataset/GC%20Daily%20OHLC%20Data_clean.csv).
