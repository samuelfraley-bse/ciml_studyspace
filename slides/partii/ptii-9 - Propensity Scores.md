---
cost: 0.00114
course: General
difficulty: 5
est_minutes: 30
summary: ''
tags: []
type: Notes
---

```json
{
  "summary": "This lecture provides an introduction to Propensity Scores, explaining their role in causal inference to address confounding and assess the positivity assumption, and outlines methods like adjustment, matching, and inverse probability weighting for estimating treatment effects.",
  "topics": ["Propensity Scores", "Causal Inference", "Confounding", "Positivity Assumption", "Treatment Effect Estimation"],
  "doc_type": "Lecture",
  "difficulty": 7,
  "study_time": 45
}
```
---CONTENT---

# Propensity Scores

Master in Data Science Upf - CI & ML - Part I

# Objective

*   Learn a new tool that is widely used in healthcare (and appears in other domains)
*   The main point of this tool is that it lets us assess the positivity assumption

# Introduction

# Main Problem

## TREATMENT A
AAAA
คน
Outcome

## TREATMENT B
คน
คนคนคน
Outcome

We cannot compare the mean effect of both groups

# Fair comparison

## TREATMENT A
AAAA
คน
Outcome

## TREATMENT B
คน
คนคนคน
Outcome

We cannot compare the mean effect of both groups
MATCHING

# Overlap - Positive Assumption

## TREATMENT A
AAAA
คน
Outcome

## TREATMENT B
คนคนคน
Outcome

If there is a type of patient missing in one group, you cannot make the comparison

# Unbalanced groups

## TREATMENT A
AAAA
คน
Outcome

## TREATMENT B
คน
คนคนคน
Outcome

Groups can be unbalanced within variables not affecting the outcome

example: color of their shirt

# Predictive Variables

## TREATMENT A
AAAA
คน
Outcome

## TREATMENT B
คน
คนคนคน
Outcome

There may be relevant variables not related to the treatment assignment
Unrelated Illnesses

# Types of variables

Covariates
Confounding
Covariates

Treatment
Outcome

What if we forget an important confounding factor?

# Comparison Main Problem

## TREATMENT
AAAA
คน

## TREATMENT B
คน
คนคนคน

Which of the confounders is more important?
*   Age
*   Gender
*   Baselines
*   ...

# COMPACTING ALL THE RELEVANT INFORMATION

## COMPARISON
Confounding Variables
*   Age
*   Gender
*   Baselines
*   ....

Calculation --->

## COMPARISON
Propensity Score
Probability of getting the treatment

# Intuitive Diagram

Confounding
Propensity
Treatment
Outcome

# Formalism

# Definition

I(X)=P(D=1|X)

Which is the propensity score in each case?

X=1 (large stones)
*   *   *   *   *   *   *   *
(T=1) Treatment A

X=0 (small stones)
*   *   *   *
(T=0) Treatment B
*   *   *   *   *   *   *   *

# Derivation

Propensity Score: I(X)=P(D=1|X)
It happens that P(x|d,l)=P(x|l)

P(R=1|do(D=d)) = ∑ₓP(R=1|d,x)P(x)
=∑ₓ∑ᵢP(R=1|d,x)P(x|I)P(I)
=∑ₓ∑ᵢP(R=1|d,x,I)P(x|d,I)P(I)
=∑ᵢP(R=1|d,I)P(I)

# Overlapping

# Baseline assessment

BMI_First Visit
Yes -
No -
Alif
20 25 30 35 40 45
sel_var

# Positive Assumption / Overlapping

density
8 -
6 -
4 -
2 -
0 -
0.00 0.25 0.50 0.75 1.00
propensity
Alif
No
Yes

# Algorithms

# Propensity Score Adjustment

P(R=1|do(D=d)) =∑ᵢP(R=1|d,I)P(I)

1) Calculate PS
2) Perform an adjustment using only PS as confounder for both treatments
3) Calculate ATE

# Propensity Score Matching

P(R=1|do(D=d)) =∑ᵢP(R=1|d,I)P(I)

1) Calculate PS
2) For each patient, find similar patients, only taking into account their PS
3) Calculate ATE

# Inverse Probability Weighting

P(R=1|do(D=d)) =∑ₓP(R=1|d,x)P(x) = ∑ₓP(R=1|d,x)P(d|x)P(x)/P(d|x) =
= ∑ₓP(R=1,d,x)/P(d|x)

AVOID IT, unless strictly necessary. Numerically problematic, because you may be dividing near 0

# Practical comments

*   The objective is to maximize accuracy of the ML models, but from the PS perspective, the closer to random, the better
*   Including variables highly predictive of the treatment, but not on the outcome can be problematic for the overlapping: groups will appear separated, but the variable doesn't affect as confounder
*   Calculating PS instead of adjustment can be beneficial if you have many outcomes

# Practical comments

If you use machine learning algorithms for calculating PS or after calculating ATEs, you may suffer from overfitting. Not because of the algorithms themselves, but because you train and predict on the same dataset. In this case it is recommended to use cross-fitting.