# Recommendation Memo — Onboarding & Activation Campaign Experiment

**To:** Leadership Team
**From:** Business Analytics
**Re:** Should the new onboarding/activation campaign be launched to all users?

---

## Executive Summary

The new campaign (Treatment) produced a statistically significant, large improvement in Paid Conversion Rate — more than double the rate of the existing experience (7.04% vs. 3.19%, p = 0.00055). However, this win comes with real costs: support ticket volume rose sharply, a small but new refund rate appeared (0% → 0.42%), revenue per converted user fell, and one major segment (Social traffic source) actually performed *worse* under the new campaign. The recommendation is therefore **not an unconditional full launch**, but a **segmented launch with guardrail monitoring** — see Final Recommendation below.

## Business Problem Summary

**Decision to be made:** Whether to launch the new onboarding/activation campaign (Treatment) to 100% of users, reject it, launch it only to specific user segments, or continue testing.
**Who this impacts:** All new users going through onboarding, the support team (ticket volume), finance (refund exposure and revenue quality), and the product/growth team (resourcing for the new campaign).
**Metric that should improve:** Paid Conversion Rate (the North Star metric for this experiment).
**Risks that must be monitored:** Refund rate, support ticket rate, revenue quality (average revenue per converted user), and segment-level performance — an improvement in the average can hide a regression in a specific, valuable segment.
**Evidence required before recommending:** A statistically significant lift in the North Star metric *and* an absence of material guardrail deterioration, *and* confirmation the lift holds (or is at least not negative) across key user segments.

## North Star Metric

**Paid Conversion Rate** — see `analysis/hypothesis_test_notes.md` for the full justification. In short: it is the only metric in the funnel that represents the actual business outcome (a paying customer), rather than an intermediate step. Landing page visits, trial starts, and onboarding completion are supporting metrics because they are necessary but not sufficient — a campaign can improve all of them without producing a single additional paying customer. The risk of optimizing Paid Conversion Rate blindly is exactly what this memo addresses: a team could chase conversion lift while quietly degrading revenue quality, increasing support burden, or hurting specific segments — which is why guardrails are mandatory, not optional, in this analysis.

## KPI Tree Explanation

(Full diagram: `outputs/kpi_tree.png`)

The tree breaks Paid Conversion Rate into three primary drivers:
1. **Funnel Reach** (Landing Page Visit Rate, Trial Start Rate) — are enough people entering the funnel?
2. **Activation Quality** (Onboarding Completion Rate, Average Engagement Score) — are the people who enter actually engaging well?
3. **Conversion Speed & Quality** (Average Days to Convert, Average Revenue per Converted User) — when people do convert, is it happening efficiently and with good revenue quality?

Three guardrails sit alongside the tree rather than underneath it, because they aren't drivers of conversion — they're checks that the conversion improvement isn't bought at an unacceptable cost: **Refund Rate**, **Support Ticket Rate**, and **Segment-Level Conversion Decline**.

## Experiment Result Summary

(Full table: `outputs/experiment_summary.xlsx`, screenshot: `screenshots/summary_metrics.png`)

| Metric | Control | Treatment | Lift |
|---|---|---|---|
| Landing Page Visit Rate | 63.62% | 72.39% | +13.8% relative |
| Trial Start Rate | 25.07% | 29.01% | +15.7% relative |
| Onboarding Completion Rate | 15.65% | 21.13% | +35.0% relative |
| **Paid Conversion Rate** | **3.19%** | **7.04%** | **+120.9% relative** |
| Average Revenue Per User | $51.97 | $54.25 | +4.4% relative |
| Average Days to Convert | 8.86 days | 6.40 days | -27.8% (faster) |

Every funnel-stage metric improved under Treatment, and the improvement compounds at each stage (the biggest relative jump is at Onboarding Completion, +35%), which is consistent with the campaign genuinely improving the onboarding experience rather than just shifting which users happen to convert.

## Hypothesis Test Interpretation

Full detail: `analysis/hypothesis_test_notes.md`, evidence: `screenshots/hypothesis_test_output.png`.

A one-tailed two-proportion z-test on Paid Conversion Rate produced Z = 3.264, p = 0.00055 — far below the α = 0.05 threshold. We reject the null hypothesis: the conversion improvement is very unlikely to be due to chance. The 95% confidence interval for the difference (1.56pp to 6.15pp) does not cross zero, reinforcing the same conclusion.

## Guardrail Analysis

**1. Refund Rate** — Control: 0.00%, Treatment: 0.42% (3 users). This is a new risk that didn't exist before (Control had zero refunds), and the difference is statistically significant (p = 0.044 on a one-tailed proportions test). In absolute terms it is still small (3 of 710 users), but it should not be ignored — it is the only metric where Treatment introduces a behavior (refund requests) that simply did not occur under Control.

**2. Support Ticket Rate** — Control: 14.78%, Treatment: 24.79% (+67.7% relative, p < 0.0001). This is the most material guardrail concern. Average tickets per user rose from 0.22 to 0.37. A near-70% increase in support burden is a real operating cost that needs to be weighed against the conversion gain — if the new campaign is creating confusion or friction that drives users to support rather than smoothly through onboarding, that cost scales with launch volume.

**3. Revenue Quality (Average Revenue per Converted User)** — Control: $1,630 (median $813), Treatment: $770 (median $452). At face value this looks like a serious decline (-52.7%), but it is partly driven by 3 very large outlier customers who happened to land in the Control group (one user alone generated $8,610). Even after accounting for this with the median (a more robust measure), Treatment's converted users still generate meaningfully less revenue per head ($452 vs. $813). This is a genuine, if smaller, guardrail concern: the campaign is converting more users, but a larger share of new conversions may be lower-value customers.

**4. Engagement Score** — Control: 57.0, Treatment: 62.9 (+10.4%, p < 0.0001). This guardrail moved favorably, not adversely — it supports the conversion result rather than undercutting it.

**Verdict: two of the three primary guardrails (support ticket rate, and to a lesser extent revenue quality) show real, statistically meaningful deterioration.** Refund rate is a smaller but real new risk. None of these are large enough on their own to reject the treatment outright, but together they argue against a "flip it on for everyone, no monitoring" launch.

## Segment-Level Insight

(Full breakdown: `analysis/experiment_analysis.xlsx` and `outputs/experiment_summary.xlsx`, Segment_Breakdown sheet)

Conversion lift is **not uniform across segments**, and one segment shows an outright decline:

- **Social traffic source: Treatment performs WORSE than Control** — Control 7.75% conversion vs. Treatment 6.06% (a decline of -1.7pp, -21.8% relative). This is the single most important segment-level finding: averaged across all users, the campaign looks like a clear win, but it is actively underperforming for users acquired via Social channels.
- **Basic plan users see almost no benefit** — Control 3.62% vs. Treatment 3.88% (+0.26pp, only +7.2% relative lift) — dramatically smaller than the Free plan's +204% relative lift or Premium's +127%.
- **West region shows the smallest regional lift** — +1.7pp (+50.5% relative) vs. +5.5pp (+155%) in North, the strongest region.
- Device type, most regions, and most traffic sources (Email, Organic, Paid Search, Referral) all show strong, consistent positive lift, which is reassuring — the Social and Basic-plan results look like genuine segment-specific effects rather than the overall result being fragile.

## Final Recommendation

### → Launch only for selected segments, with guardrail monitoring

Specifically:
1. **Launch the new campaign to all traffic sources except Social**, where the data shows a real decline rather than just a smaller gain. Investigate why Social users respond differently before considering a Social-specific rollout.
2. **Launch to Free and Premium plan users**, where the lift is large and consistent. **Hold off on Basic plan users** until a follow-up test or root-cause analysis explains the near-zero lift there.
3. **Put Support Ticket Rate and Refund Rate on a monitoring dashboard before any expanded rollout**, with a pre-agreed threshold (e.g., pause expansion if support ticket rate exceeds 30% or refund rate exceeds 1%) given how much both guardrails moved in this experiment.
4. **Do not treat this as a single binary launch decision** — phase the rollout by segment and re-evaluate guardrails at each phase.

This is not a "Launch" or "Do not launch" answer in the simple sense, because the data itself is not uniform — the conversion win is real and large, but so are the guardrail costs and the segment-level exception, and a partial, monitored launch is the recommendation best supported by the evidence.

## Risks and Limitations

- The Average Revenue per Converted User comparison is heavily influenced by a small number of high-value outliers in the Control group (n=22 converters in Control is a small base); this metric should be re-evaluated once more conversions accumulate.
- Days-to-convert and revenue-per-converted-user are both based on very small converter samples (22 Control, 50 Treatment) relative to the full dataset (1,400 users), so these specific findings carry wider uncertainty than the main conversion-rate result.
- The dataset covers signups from January–May 2025 only; seasonal effects on campaign performance (e.g., back-to-school, holiday signup behavior) cannot be assessed from this window.
- Support ticket content/reason is not available in this dataset, so we can observe *that* tickets increased but not definitively *why* — this should be a top priority for follow-up before a full rollout.
- 8 exact duplicate records were removed from the raw data during cleaning (see README); this is a small fraction (0.6%) of the dataset and does not materially change any of the conclusions, but is disclosed for transparency.

## Next Steps

1. Pull support ticket *reason* data (if available) for Treatment users to diagnose the cause of the support ticket increase before scaling further.
2. Run a targeted, Social-channel-only follow-up test to understand why that segment responds negatively to the new campaign.
3. Investigate the Basic plan segment specifically — interview or survey a sample of Basic users who went through Treatment but didn't convert, to identify friction points unique to that plan tier.
4. Re-run the revenue-per-converted-user comparison once segment-restricted launch data accumulates a larger converter sample, to reduce sensitivity to outliers.

