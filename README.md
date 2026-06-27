# KPI Framework, Experiment Analysis & Decision Recommendation — Part 2

## 1. Business Context

A subscription-based digital product company tested a new onboarding and activation campaign (Treatment) against its existing onboarding experience (Control), with users randomly split into the two groups. The objective: improve user conversion and early engagement. Leadership needs a data-backed decision on whether to launch the new campaign to all users, reject it, launch it to selected segments only, or continue testing.

**Decision to be made:** Launch / Do not launch / Launch for selected segments / Continue testing.
**Who it impacts:** New users going through onboarding, the support team, finance, and the growth/product team.
**Metric that should improve:** Paid Conversion Rate.
**Risks to monitor:** Refund rate, support ticket rate, revenue quality, and segment-level performance.
**Evidence required:** A statistically significant North Star lift that holds up against guardrail checks and segment-level scrutiny.

## 2. Dataset Description

- **File:** `data/campaign_experiment_data.xlsx`, sheet `experiment_data` (1,408 raw rows, 16 columns), plus a `business_context` sheet describing the experiment and every field.
- **Groups:** Control (existing onboarding) vs. Treatment (new campaign), randomly assigned.
- **Signup window:** 1 Jan 2025 – 31 May 2025.
- **Key fields:** signup date, experiment group, region/device/traffic source/plan type, funnel flags (visited landing page, started trial, completed onboarding, converted to paid), revenue (30-day), support tickets, refund flag, days to convert, engagement score.

## 3. Data Cleaning Approach

Documented in full in `analysis/experiment_analysis.xlsx` (`Data_Quality_Checks` sheet). Summary:

| Check | Finding | Handling |
|---|---|---|
| Missing values | `device_type` (18 nulls), `traffic_source` (24 nulls), `engagement_score` (14 nulls), `days_to_convert` (1,336 nulls — expected, since it's only populated for users who converted) | Left as null; excluded only from the specific averages/breakdowns that use that field |
| Duplicate user IDs | 8 exact full-row duplicate records found | Dropped (kept first occurrence) — cleaned dataset = 1,400 unique users |
| Group counts (post-cleaning) | Control: 690, Treatment: 710 | Balanced enough for valid comparison; no rebalancing needed |
| Invalid binary values | All 5 binary fields (`visited_landing_page`, `started_trial`, `completed_onboarding`, `converted_to_paid`, `refund_requested`) contain only 0/1 | No invalid values found — no correction needed |
| Revenue outliers | 4 converted users flagged by the IQR rule (>$2,340.63); 3 of the most extreme (up to $8,610) sit in the Control group | Retained as genuine high-value customers (not data errors), but called out explicitly since they skew Control's mean revenue per converted user upward — median is reported alongside mean throughout |
| Segment distribution across groups | Region, device type, traffic source, and plan type are all reasonably balanced between Control and Treatment | No adjustment needed; balance confirms the random split worked as intended |

## 4. North Star Metric Selected

**Paid Conversion Rate.** It is the only funnel metric that represents the actual business outcome (a paying customer) rather than an intermediate step. Full reasoning, including why other metrics are supporting (not North Star) metrics and what could go wrong if this metric were optimized blindly, is in `outputs/recommendation_memo.md`.

## 5. KPI Tree Summary

See `outputs/kpi_tree.png` / `screenshots/kpi_tree_preview.png`. Paid Conversion Rate breaks into 3 primary drivers (Funnel Reach, Activation Quality, Conversion Speed & Quality), each with 2 sub-drivers, plus 3 guardrail metrics (Refund Rate, Support Ticket Rate, Segment-Level Conversion Decline) that sit alongside the tree as checks rather than drivers.

## 6. Experiment Analysis Approach

1. Cleaned the raw dataset (see Section 3) in `analysis/experiment_analysis.xlsx`.
2. Built `outputs/experiment_summary.xlsx` with all 11 required Control vs. Treatment metrics, computed via live Excel formulas (`COUNTIFS`/`AVERAGEIFS`/`SUMIFS`) referencing the cleaned data — not hardcoded values — so the workbook recalculates if the underlying data changes.
3. Broke down 3 segment dimensions (Region, Device Type, Plan Type) by group on the `Segment_Breakdown` sheet of the same workbook (a 4th, Traffic Source, is analyzed separately in the memo since it surfaced the most important finding).
4. Ran a formal hypothesis test on the North Star metric (Section 7).
5. Evaluated guardrails and segment-level results before writing the final recommendation.

## 7. Hypothesis Test Summary

One-tailed two-proportion z-test on Paid Conversion Rate: **Z = 3.264, p = 0.00055** — reject H₀. Treatment's conversion rate (7.04%) is significantly higher than Control's (3.19%), a +120.9% relative lift, 95% CI for the difference [1.56pp, 6.15pp]. Full notes: `analysis/hypothesis_test_notes.md`. Evidence: `screenshots/hypothesis_test_output.png`.

## 8. Guardrail Metrics Considered

1. **Refund Rate** — Control 0.00% → Treatment 0.42% (statistically significant new risk, small in absolute terms).
2. **Support Ticket Rate** — Control 14.78% → Treatment 24.79% (+67.7% relative, the most material guardrail concern).
3. **Revenue Quality (Avg. Revenue per Converted User)** — Control $1,630 → Treatment $770 (partly outlier-driven, but the gap persists using medians too).
4. **Engagement Score** (bonus guardrail, moved favorably) — Control 57.0 → Treatment 62.9.

Full analysis and verdict: `outputs/recommendation_memo.md`.

## 9. Final Recommendation

**Launch only for selected segments, with guardrail monitoring** — not an unconditional full launch. Specifically: launch to all traffic sources except Social (which shows a real conversion decline under Treatment), launch to Free and Premium plan users (strong, consistent lift), hold off on Basic plan users (negligible lift) pending further investigation, and monitor Support Ticket Rate and Refund Rate against pre-agreed thresholds before any expanded rollout. Full reasoning: `outputs/recommendation_memo.md`.

## 10. Assumptions and Limitations

- 8 exact duplicate records (0.6% of raw data) were removed during cleaning.
- Average Revenue per Converted User is based on a small converter sample (22 Control, 50 Treatment) and is sensitive to a few high-value outliers in Control.
- The dataset spans only 5 months (Jan–May 2025), so seasonal effects cannot be assessed.
- Support ticket *reason* is not captured in the data, so the cause of the support ticket increase under Treatment is not yet diagnosed — flagged as a next step.
- `device_type`, `traffic_source`, and `engagement_score` have minor missing values, excluded only from the specific calculations using those fields.

## 11. Screenshots Included

| File | Shows |
|---|---|
| `screenshots/summary_metrics.png` | Control vs Treatment summary table across all 11 required metrics |
| `screenshots/hypothesis_test_output.png` | Full two-proportion z-test inputs, output, decision rule, and conclusion |
| `screenshots/kpi_tree_preview.png` | The KPI tree image |


