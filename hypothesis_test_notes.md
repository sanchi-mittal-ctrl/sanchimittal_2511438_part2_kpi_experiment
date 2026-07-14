# Hypothesis Test Notes

## Metric Being Tested

**Paid conversion rate** — the proportion of users who became a paying customer (`converted_to_paid = 1`) out of all users assigned to a group.

This is the experiment's North Star metric (see `README.md` / `outputs/recommendation_memo.md` for the full justification). It is tested first and given the most weight because it's the metric the business decision ("should we launch this campaign?") is actually anchored to — the funnel metrics above it (landing page visits, trial starts, onboarding completion) are *supporting* signals, not the decision metric itself.

## Hypotheses

- **H0 (null):** The paid conversion rate of the Treatment group is equal to the paid conversion rate of the Control group. `p_treatment = p_control`
- **H1 (alternate):** The paid conversion rate of the Treatment group is **greater than** the Control group. `p_treatment > p_control`

## One-Tailed or Two-Tailed?

**One-tailed (right-tailed).** The business question is specifically "does the new campaign *improve* conversion enough to justify launching it" — not simply "is it different." A one-tailed test gives more statistical power to detect an improvement, which is the only direction that would change the launch decision in this metric's favor.

*Trade-off worth noting:* a one-tailed test on the North Star metric alone would not, by itself, formally flag if Treatment were significantly *worse*. That risk is why this analysis doesn't stop at the North Star test — every guardrail metric below is checked independently for movement in the harmful direction, regardless of what the North Star test says.

## Significance Level

**α = 0.05** — the conventional threshold for product/growth experiments of this kind, balancing the risk of a false positive (launching a campaign that doesn't actually help) against being so conservative that a real improvement gets missed.

## Test Used

**Two-proportion z-test.** `converted_to_paid` is a binary outcome (0/1) measured on two independent, non-overlapping groups (Control vs. Treatment) with large sample sizes in each (n=690 and n=710) — this is exactly the condition the two-proportion z-test is designed for. (A chi-square test of independence on the same 2x2 table would give an equivalent result.)

## Test Inputs

| | Control | Treatment |
|---|---|---|
| Users (n) | 690 | 710 |
| Conversions (x) | 22 | 50 |
| Conversion rate (p) | 3.19% | 7.04% |

## Test Output

| Statistic | Value |
|---|---|
| Pooled proportion | 0.0514 |
| Standard error | 0.01181 |
| **Z-statistic** | **3.264** |
| **P-value (one-tailed)** | **0.00055** |
| Absolute lift | +3.85 percentage points |
| Relative lift | +120.9% |
| 95% CI on the difference | [+1.56pp, +6.15pp] |

Live formulas for this calculation are in `outputs/experiment_summary.xlsx`, sheet `hypothesis_test`. A screenshot of that sheet is saved at `screenshots/hypothesis_test_output.png`.

## Decision Rule

If p-value < α (0.05) → reject H0 → the improvement is statistically significant.
If p-value ≥ α (0.05) → fail to reject H0 → not enough evidence of a real improvement.

**Result: p = 0.00055 < 0.05 → Reject H0.**

## Business Interpretation

The Treatment group's paid conversion rate (7.04%) is significantly higher than Control's (3.19%) — this is very unlikely to be due to random chance alone (only about a 1-in-1,800 chance of seeing a gap this large if the campaign truly had no effect). On the North Star metric in isolation, the new campaign works.

**However — this result alone is not sufficient to recommend launch.** The same lift shows up consistently across every major funnel stage (landing page visits, trial starts, onboarding completion all improved with their own statistically significant p-values, see `outputs/experiment_summary.xlsx`), and engagement score is also significantly higher in Treatment (62.94 vs. 57.03, p<0.0001) — which is reassuring, since it suggests the campaign is driving genuinely more engaged users rather than just inflating the conversion number through funnel pressure.

At the same time, the guardrail analysis (Task 8 / `outputs/recommendation_memo.md`) surfaces real costs sitting alongside this lift — most notably a large, statistically significant increase in the support ticket rate, and a decline in average revenue per converted user driven by a shift toward lower-value plan tiers. The conversion-rate test answers "did it move the metric we asked it to move" — it does not, by itself, answer "should we launch." That call is made in the recommendation memo by weighing this result against the guardrails together.
