# Recommendation Memo — Onboarding & Activation Campaign Experiment

## Executive Summary

The new onboarding and activation campaign (Treatment) significantly more than doubled the paid conversion rate compared to the existing experience (Control): **7.04% vs. 3.19%**, a statistically significant lift (p = 0.00055). Every funnel stage above conversion also improved, and engagement scores rose too — so this isn't just funnel manipulation, users genuinely engage more.

But the lift comes with real, measurable costs: the **support ticket rate jumped from 14.78% to 24.79%** (highly significant), and **revenue per converted user fell by more than half** because Treatment disproportionately converts users into the lowest-value (Free) plan tier. Blended revenue per user is roughly flat overall (not a net loss), and refund rate ticked up slightly but isn't yet statistically distinguishable from zero.

**Recommendation: Launch only for selected segments, with required guardrail fixes — not an unconditional full launch.** See Section "Final Recommendation" for the specific scope.

## North Star Metric

**Paid Conversion Rate** — the % of users who became paying customers.

- *Why this is the North Star:* it's the single metric that most directly reflects the business outcome leadership actually cares about (revenue-generating customers), and it's the metric the decision ("launch or not") is explicitly anchored to in the business problem statement.
- *Why other metrics are supporting, not the North Star:* landing page visits, trial starts, and onboarding completion are all necessary *steps toward* conversion, not the outcome itself — a campaign that nails every step but doesn't convert more users into payers hasn't actually achieved the business goal.
- *Connection to business growth:* every additional paid conversion compounds into recurring revenue for a subscription business — this is the lever leadership is actually trying to pull.
- *Risk of optimizing it blindly:* exactly what this experiment surfaced — a campaign can lift conversion by funneling people toward the cheapest plan, converting them faster than they're ready for, or pressuring sign-ups that generate more support burden. That's why this memo evaluates guardrails before recommending a launch, instead of stopping at the North Star result.

## KPI Tree Explanation

See `outputs/kpi_tree.png` / `screenshots/kpi_tree_preview.png`. The tree breaks Paid Conversion Rate into 4 primary drivers (Landing Page Visit Rate, Trial Start Rate, Onboarding Completion Rate, Engagement Quality), each with 2 sub-drivers, plus 4 guardrail metrics (Refund Rate, Support Ticket Rate, Revenue Quality, Days to Convert) that sit outside the optimization path — they're meant to be watched, not optimized directly. This experiment is a real-world illustration of exactly why that guardrail layer exists: the primary metric and its drivers all moved in the right direction, while two guardrails (support tickets, revenue quality) moved in the wrong one at the same time.

## Experiment Result Summary

| Metric | Control | Treatment | Change |
|---|---|---|---|
| Users | 690 | 710 | — |
| Landing page visit rate | 63.62% | 72.39% | +8.8pp, p=0.00022 |
| Trial start rate | 25.07% | 29.01% | +3.9pp, p=0.0485 |
| Onboarding completion rate | 15.65% | 21.13% | +5.5pp, p=0.0041 |
| **Paid conversion rate** | **3.19%** | **7.04%** | **+3.85pp, p=0.00055** |
| Avg revenue per user (blended) | $51.97 | $54.25 | +$2.28, not significant (p=0.91) |
| Avg revenue per converted user | $1,630.10 | $770.41 | -53%, driven by plan mix |
| Refund rate | 0.00% | 0.42% | +0.42pp, not significant (p=0.087) |
| Support ticket rate | 14.78% | 24.79% | +10.0pp, p<0.0001 |
| Avg engagement score | 57.03 | 62.94 | +5.9, p<0.0001 |
| Avg days to convert | 8.86 | 6.40 | -2.46 days, p=0.0083 |

Full detail and segment breakdowns: `outputs/experiment_summary.xlsx`.

## Hypothesis Test Interpretation

A two-proportion z-test on paid conversion rate rejects the null hypothesis (z=3.264, p=0.00055 one-tailed) at α=0.05. The lift is very unlikely to be random noise. Full test notes: `analysis/hypothesis_test_notes.md`; live formulas and evidence: `outputs/experiment_summary.xlsx` (`hypothesis_test` sheet) and `screenshots/hypothesis_test_output.png`.

## Guardrail Analysis

| Guardrail | Control | Treatment | Risk? |
|---|---|---|---|
| **Support ticket rate** | 14.78% | 24.79% | **Yes — significant risk.** A ~68% relative increase in users needing support is a real operational cost, and is consistent across every region (not isolated to one segment). |
| **Revenue quality (avg revenue per converted user)** | $1,630 (mean) / $813 (median) | $770 (mean) / $452 (median) | **Yes — moderate risk.** Treatment converts far more users into the Free plan (68% of its converters vs. 50% for Control), pulling down the value of each conversion. Blended revenue per user is flat, so this isn't a revenue *loss* yet, but it does mean the conversion lift is partly "more, cheaper" rather than "more, equally valuable." |
| **Refund rate** | 0.00% | 0.42% | **Watch, not yet confirmed.** Only 3 refunds in Treatment vs. 0 in Control — directionally concerning but not statistically significant at this sample size (p=0.087). Re-test with a larger sample or longer window before ruling this in or out. |
| **Days to convert** | 8.86 days | 6.40 days | **Likely neutral-to-positive**, but flagged because it moves in the same direction as the support-ticket increase — faster conversions paired with more support tickets could indicate some users convert before fully understanding the product. Worth monitoring alongside the ticket data, not a standalone red flag. |
| **Engagement score** | 57.03 | 62.94 | **Positive signal**, included here because it's the guardrail that argues *against* the "this is just funnel manipulation" interpretation — genuinely higher engagement supports the conversion lift being real. |

## Segment-Level Insight

- **Region:** Conversion lift is positive in every region (North strongest at +5.4pp, West weakest at +1.7pp) — no region shows a decline.
- **Device type:** Lift is positive on Desktop, Mobile, and Tablet alike.
- **Traffic source — the one notable exception:** every channel improves *except* **Social**, where Treatment (6.06%) is actually slightly *below* Control (7.75%). This is the clearest segment-level decline in the dataset.
- **Plan type:** Free-plan conversion lift is by far the largest (+6.2pp) compared to Basic (+0.26pp, essentially flat) and Premium (+3.5pp). This is the direct mechanism behind the revenue-quality guardrail finding above — the campaign is unusually effective at converting price-sensitive users specifically.
- **Support tickets:** the increase is broad-based (+8 to +12pp) across every region — this is not a localized issue that launching selectively would fix on its own.

## Final Recommendation

**Launch only for selected segments — specifically, launch broadly except hold the existing (Control) experience for Social-sourced traffic, and treat the rollout as phased rather than a one-time full switch.**

Rationale: the North Star result is strong and statistically solid, and the engagement-score increase gives confidence it reflects real activation rather than funnel gaming. That supports moving forward rather than holding the campaign back entirely or running it longer with no changes. But two things argue against an unconditional, full launch right now:

1. The Social channel is the one segment where the campaign underperforms the existing experience — there's no reason to roll out a worse experience there.
2. The support ticket increase is large, statistically clear, and broad-based — this is a real cost center that should be staffed/addressed as part of rollout, not discovered after the fact at full scale.

The revenue-quality shift toward Free-plan conversions and the small uptick in refunds are not blockers on their own (blended revenue is flat, refund increase isn't yet significant), but both should be tracked closely through the rollout as leading indicators.

## Risks and Limitations

- **Refund rate sample size is small** (3 events in Treatment) — not enough data yet to confirm or rule out a real increase. This could look very different at scale.
- **Revenue figures are 30-day snapshots.** Whether the Free-plan-heavy conversion mix upgrades over time or churns is unknown from this data and matters a lot for the real revenue verdict.
- **"Unknown" device_type / traffic_source segments** (originally missing data, filled per business rule) have very small sample sizes (6–18 users) and shouldn't be used to draw segment-level conclusions on their own.
- **Revenue outliers** (4 unusually large converted-user revenue values, 3 of them in Control) were flagged but not removed — they meaningfully inflate Control's *mean* ARPCU; the *median* comparison ($813 vs. $452) is the more reliable revenue-quality read and is still directionally consistent with the mean.
- This is a 30-day window experiment — no data on longer-term retention, upgrade, or churn behavior for either group.

## Next Steps

1. Investigate the root cause of the support ticket increase (which step, what users are asking about) before scaling further.
2. Re-run or extend the experiment for the Social channel specifically, or exclude it from rollout pending its own analysis.
3. Track refund rate and Free-plan retention/upgrade behavior over a longer window (60–90 days) as a follow-up checkpoint before declaring full success.
4. If support ticket volume can be brought down to within Control's range, revisit this recommendation for a full, unconditional launch.
