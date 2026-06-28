# Onboarding & Activation Campaign — Experiment Analysis README

## Business Context

A subscription-based digital product company ran an A/B test on a new onboarding and activation campaign. **Control** users got the existing onboarding experience; **Treatment** users got the new campaign experience. Leadership needs a launch/no-launch decision, backed by evidence on both the primary conversion outcome and the guardrail metrics that could make a conversion win risky (refunds, support load, revenue quality).

**Business problem statement:** Should the new onboarding/activation campaign be rolled out to all users? This decision affects every future signup to the product, and the metric leadership wants to see move is the paid conversion rate. The risks that must be monitored alongside it are refund rate, support ticket volume, and the revenue quality of each conversion — a higher conversion rate that comes with more refunds, more support tickets, or lower-value customers is not an unambiguous win. Before recommending a launch, the evidence required is: a statistically significant lift in the primary metric, confirmation that the lift holds (or can be scoped) across customer segments, and confirmation that no guardrail metric has moved sharply in the wrong direction without explanation.

## Dataset Description

| | |
|---|---|
| Source file | `data/campaign_experiment_data.xlsx` (sheet `experiment_data`, reference sheet `business_context`) |
| Raw rows | 1,408 users |
| Groups | Control (existing onboarding) / Treatment (new campaign) |
| Funnel fields | `visited_landing_page`, `started_trial`, `completed_onboarding`, `converted_to_paid` (all 0/1) |
| Outcome fields | `revenue_30d`, `support_tickets_30d`, `refund_requested`, `days_to_convert`, `engagement_score` |
| Segment fields | `region`, `device_type`, `traffic_source`, `plan_type` |

## North Star Metric Selected

**Paid Conversion Rate.** Full justification in `outputs/recommendation_memo.md` — in short, it's the metric most directly tied to the actual business outcome (paying customers), and every other funnel metric is a means to that end rather than the end itself.

## KPI Tree Summary

`outputs/kpi_tree.png` (also at `screenshots/kpi_tree_preview.png`) breaks Paid Conversion Rate into 4 primary drivers — Landing Page Visit Rate, Trial Start Rate, Onboarding Completion Rate, and Engagement Quality — each with 2 sub-drivers, plus 4 guardrail metrics (Refund Rate, Support Ticket Rate, Revenue Quality, Days to Convert) tracked alongside but never optimized directly.

## Experiment Analysis Approach

Full data quality checks and cleaning logic are in `analysis/experiment_analysis.xlsx` (sheet `data_quality_checks` has the full checklist). Summary of what was checked and how it was handled:

| Check | Result | Action |
|---|---|---|
| Missing values | `device_type` (18), `traffic_source` (24), `engagement_score` (14) | Categorical fields filled `"Unknown"` + flagged; `engagement_score` left blank + flagged and **excluded from averages** (not imputed, to avoid biasing the metric) |
| Group counts | Control 690 / Treatment 710 (after cleaning) | Balanced, no group-size concern |
| Duplicate user IDs | 8 exact duplicate rows | Removed (kept first occurrence); 0 remaining conflicting duplicate IDs |
| Invalid binary values | 0 found across all 5 binary columns | Verified via formula, no action needed |
| Funnel logic consistency | 0 inconsistencies (e.g. converted-but-never-onboarded) | Verified via formula, data is internally consistent |
| Outliers in revenue | 4 outliers among 72 converted users (IQR method, bound = $2,340.63) | Flagged, not removed — reviewed as part of the revenue-quality guardrail rather than discarded |
| Segment distribution across groups | Balanced across region/device/traffic_source/plan_type | No randomization concern |

## Hypothesis Test Summary

Two-proportion z-test on paid conversion rate: Control 3.19% vs. Treatment 7.04%, **z = 3.264, p = 0.00055 (one-tailed, α = 0.05) → reject H0.** The lift is statistically significant. Full notes: `analysis/hypothesis_test_notes.md`. Evidence: `outputs/experiment_summary.xlsx` (`hypothesis_test` sheet), screenshot at `screenshots/hypothesis_test_output.png`.

## Guardrail Metrics Considered

- **Support ticket rate** — 14.78% → 24.79% (highly significant increase, the clearest risk found)
- **Revenue quality (avg revenue per converted user)** — fell ~53%, driven by a shift toward Free-plan conversions
- **Refund rate** — 0.00% → 0.42% (directional concern, not yet statistically significant)
- **Days to convert** — 8.86 → 6.40 days (faster conversion; monitored alongside the support-ticket finding)
- **Engagement score** — 57.03 → 62.94 (significant increase; reassuring evidence the lift is genuine, not just funnel pressure)

Full discussion: `outputs/recommendation_memo.md`.

## Final Recommendation

**Launch only for selected segments** — roll out broadly, but hold the existing experience for Social-sourced traffic (the one segment where Treatment underperforms Control), and treat the rollout as phased while the support-ticket driver is investigated. Not an unconditional full launch, and not "do not launch" — the primary effect is real and significant, but two guardrails (support load, revenue quality) need active management during rollout. Full reasoning: `outputs/recommendation_memo.md`.

## Assumptions and Limitations

- Revenue and engagement figures are 30-day snapshots; longer-term retention/upgrade/churn behavior is unknown.
- Refund rate's statistical insignificance is partly a small-sample issue (3 events) — could look different at scale.
- "Unknown" segment values (filled from missing data) have small sample sizes (6–18 users) and aren't reliable for segment-level conclusions on their own.
- Revenue outliers were flagged, not removed; mean ARPCU is outlier-sensitive, so the median comparison is treated as the more reliable revenue-quality read in the memo.
- IQR-based outlier detection for revenue was computed only within converted users (revenue is structurally 0 for everyone else), not across the full population.

## Screenshots Included

| File | Shows |
|---|---|
| `screenshots/summary_metrics.png` | Control vs Treatment summary table |
| `screenshots/hypothesis_test_output.png` | Two-proportion z-test output and evidence |
| `screenshots/kpi_tree_preview.png` | KPI tree image |

## Repository Structure

```
.
├── README.md
├── data/
│   └── campaign_experiment_data.xlsx
├── analysis/
│   ├── experiment_analysis.xlsx
│   └── hypothesis_test_notes.md
├── outputs/
│   ├── kpi_tree.png
│   ├── experiment_summary.xlsx
│   └── recommendation_memo.md
└── screenshots/
    ├── kpi_tree_preview.png
    ├── summary_metrics.png
    └── hypothesis_test_output.png
```
