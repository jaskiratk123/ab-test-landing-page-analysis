# A/B Test Analysis: Landing Page Conversion Experiment

A statistical analysis of a 21-day landing page redesign experiment on a real e-commerce dataset, including data quality investigation, hypothesis testing, time-segmented analysis, and a Product Manager-facing decision memo.

---

## The Question

A product team redesigned the landing page and ran a randomized A/B test against the existing version. They needed an answer:
Should we ship the new page, or not?

## The Answer

No. Don't ship.
But more interestingly, the data isn't telling us the new page is bad. It's telling us the experiment wasn't long enough to know either way.

---

## What I Found

The headline number: across approximately 290,000 users over 21 days, the new landing page converted at 11.88% versus 12.04% for the old page. That's a -0.16 percentage point difference, with p = 0.19, well above any threshold for statistical significance. The 95% confidence interval for the difference runs from -0.39pp to +0.08pp, crossing zero. We genuinely cannot rule out that the true effect is no effect.

But the daily trajectory tells a more interesting story than the aggregate number does.

When I looked at the rolling-average conversion rate day by day, the two groups crossed somewhere around day 11 to 13. Treatment was slightly winning in the first half of the experiment, then control overtook it. By the end, control was 0.28pp ahead, still not statistically significant (p = 0.10), but approaching the threshold and trending the wrong direction for the new page.

This crossover pattern is consistent with two interpretations:

- Novelty effect on treatment. The new page attracted initial attention because it was different, then performance regressed as users habituated.
- Learning effect on control. Returning users gradually became more efficient with the familiar page.

Both stories fit the data. A 21-day experiment isn't long enough to distinguish them.

## What This Means for the Business

The decision memo recommends not shipping the current treatment, but with a specific reason. Not "the test failed" but "the test ended while the trends were still moving." The right next step isn't another launch decision. It's a properly designed re-test:

- Minimum 6 weeks duration to let novelty and learning dynamics stabilize
- Cohort-based analysis separating new from returning users
- Pre-registered analysis plan to avoid being misled by mid-experiment swings
- Explicit guardrails on engagement and bounce rate, not just conversion

---

## The Data Quality Investigation

Before any statistical test, I checked whether the experiment data was actually clean. Two issues turned up:

About 1.3% of users had their group assignment mismatched with the landing page they were shown. A user labeled "control" actually saw the new page, or vice versa. This is consistent with a caching or session bug somewhere in the experiment instrumentation. Left in, it would have biased every group-level metric toward the page users actually saw rather than the page they were assigned. All 3,893 contaminated records were removed before analysis.

I also found one duplicate user record, which violates the independence assumption underlying the z-test.

After cleanup, the dataset went from 294,478 to 290,584 unique users. The remaining control/treatment split came out 50/50, suggesting the underlying randomization was working. The contamination happened downstream of the bucketing.

This step is the difference between a defensible test and a misleading one. It's the kind of pre-flight check that's standard at companies running A/B tests at scale but often gets skipped in junior analyses.

---

## Methodology

| Step | Approach |
|------|----------|
| Data quality | Assignment integrity check, duplicate removal |
| Primary test | Two-proportion z-test (two-sided, α = 0.05) |
| Effect estimation | 95% confidence interval for the difference in proportions |
| Heterogeneity check | Segment analysis by experiment phase (early vs late half) |
| Integrity verification | Daily volume + assignment split stability across 21 days |
| Visualization | Daily conversion with 3-day rolling average |
| Communication | PM-facing decision memo with TL;DR, findings, caveats, next steps |

## Tools

Python, pandas, numpy, scipy, statsmodels, matplotlib

## Dataset

A/B Testing dataset by Zhang Luyuan on Kaggle: https://www.kaggle.com/datasets/zhangluyuan/ab-testing

294,478 user records with group assignment, landing page shown, and conversion outcome.

---

## What This Project Demonstrates

Beyond the technical skills, this project is structured to show:

- **Statistical thinking in business context.** The decision memo is written for a Product Manager, not for a statistics textbook.
- **Skepticism applied to your own data.** The contamination check distinguishes analysts who can be trusted with messy real data from analysts who run tests on whatever they're handed.
- **Comfort with null results.** A "fail to reject" outcome is information. Treating it as the actual finding rather than burying it or chasing significance through subgroup analysis is the senior signal.
- **Heterogeneity awareness.** The time-segmented analysis reveals more than the aggregate metric does. Real analysts look beyond the headline.
- **Methodological honesty.** The notebook includes a "what I'd add in a v2" section calling out methodological gaps: formal sample ratio mismatch test, cohort separation, daily peeking analysis. Real analysts know their limits.

---

## About

Built by Jaskirat Kaur as part of transitioning from QA engineering into data analytics.

I spent the past year and a half at Quark Software Inc. analyzing test execution data, building automation that reduced regression cycles by approximately 50%, and learning that the part of QA work I cared most about was the analytical pattern-finding, not the test writing. This portfolio is the bridge from that work into a dedicated analyst role.

Connect: https://www.linkedin.com/in/jaskirat-kaur23
