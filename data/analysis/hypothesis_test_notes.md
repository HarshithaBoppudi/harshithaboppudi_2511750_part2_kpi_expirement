# Hypothesis Test Notes

## Metric Being Tested

**Paid Conversion Rate** (`converted_to_paid`) — the proportion of signed-up users who become paying customers. This is the North Star metric for the experiment (see `outputs/recommendation_memo.md` for the full justification).

## Reason for Choosing This Metric

The business decision in question — whether to launch the new onboarding/activation campaign to all users — is ultimately a revenue-generation decision. Landing page visits, trial starts, and onboarding completion are all useful diagnostic steps, but they are intermediate funnel steps; a campaign could improve all of them and still fail to move the metric leadership actually cares about (paying customers). Paid Conversion Rate is the only metric in the funnel that directly represents the business outcome the campaign was built to drive.

## Hypotheses

- **Null hypothesis (H₀):** The treatment (new campaign) does **not** increase the paid conversion rate relative to control. Formally, p_treatment ≤ p_control.
- **Alternate hypothesis (H₁):** The treatment **increases** the paid conversion rate relative to control. Formally, p_treatment > p_control.

## Test Type

**One-tailed**, two-proportion z-test.

We use a one-tailed test because the business question is directional: leadership only cares whether the treatment is an *improvement* (to decide launch vs. no-launch). We are not trying to detect a two-directional difference; a treatment that performs worse than control leads to the same decision ("do not launch") as a treatment that performs no differently, so the one-tailed framing matches the actual decision being made.

## Significance Level

α = 0.05 (standard threshold for business experimentation decisions).

## Test Inputs

| | Control | Treatment |
|---|---|---|
| Users (n) | 690 | 710 |
| Conversions | 22 | 50 |
| Conversion rate | 3.19% | 7.04% |

(Figures are post-cleaning: 8 exact duplicate rows were removed from the raw 1,408-row dataset before this calculation — see `README.md` Section "Data Cleaning Approach" for details.)

## Test Output

- **Z-statistic:** 3.264
- **P-value (one-tailed):** 0.00055
- **95% confidence interval for the difference (Treatment − Control):** [1.56pp, 6.15pp]
- **Relative lift:** +120.9%

Full evidence captured in `screenshots/hypothesis_test_output.png`.

## Decision Rule

Reject H₀ if p-value < α (0.05); otherwise fail to reject H₀.

## Interpretation Logic

Since p = 0.00055 is far below α = 0.05, we **reject the null hypothesis**. There is strong statistical evidence that the treatment group's paid conversion rate is genuinely higher than control's, and the 95% confidence interval (1.56pp to 6.15pp) does not cross zero, reinforcing that this is unlikely to be due to random chance. The effect size is also large in practical terms — conversion more than doubled (relative lift +120.9%) — so this is both statistically and practically significant.

**However, this result alone is not sufficient grounds for a launch decision.** A primary-metric win must be checked against guardrail metrics before recommending a full launch — see `outputs/recommendation_memo.md` for the guardrail analysis (refund rate, support ticket rate, and segment-level performance) that materially shapes the final recommendation. Notably, the average revenue per *converted* user is lower in Treatment (driven partly by a few high-value outlier customers landing in the Control group), and support ticket rates rose substantially in Treatment — both of which are addressed in the guardrail section of the memo.

## Connection to the Business Decision

This test directly answers the first of five required business questions: "should the treatment be launched?" A statistically significant improvement in the North Star metric is a necessary condition for a "Launch" recommendation, but per Task 8 of the assignment brief, it is not sufficient on its own — the guardrail metrics evaluated separately determine whether the recommendation is an unconditional launch, a segmented launch, or continued testing.

