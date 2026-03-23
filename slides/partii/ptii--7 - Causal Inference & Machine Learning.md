---
cost: 0.00149
course: General
difficulty: 6
est_minutes: 45
summary: This lecture explores the relationship between Causal Inference and Machine
  Learning, highlighting their distinct objectives, how ML can be applied within causal
  frameworks like the adjustment formula, and the fundamental limitations of ML regarding
  distribution shifts and actions affecting system dynamics.
tags:
- Causal Inference
- Machine Learning
- Prediction vs Understanding
- Conditional Expectation
- Adjustment Formula
- Invariant Prediction
type: Lecture
---

# Causal Inference & Machine Learning Relationship

Master in Data Science Upf - CI & ML - Part I

## Objectives

*   See how we can use machine learning for the adjustment formula
*   Understand some limitations of machine learning
*   See how causal inference can help machine learning

## Differences between machine learning & causal inference

## Prediction vs Understanding

**[Image: Diagram showing an arrow from "Age, Comorbidities" to "ICU", with a microscopic image of the COVID-19 virus below, and a small CDC logo.]**

## Prediction vs Understanding

**MACHINE LEARNING**
Objective: Predict who is going to ICU
Decision: patient prioritization
Model: whatever predicts the best

**CAUSAL INFERENCE**
Objective: Understand which factors lead to ICU
Decision: Public health policies
Model: explainable model

## What do we actually estimate when we train a machine learning model?

## Simplest prediction ever

Imagine we have numbers a₁, ..., a_n. We want to predict them with a single number. Which is the best number for the task?

## Simplest prediction ever

Imagine we have numbers a₁, ..., a_n. We want to predict them with a single number. Which is the best number for the task?

What does it mean to have a good prediction?
$\min_a \sum (a_i - a)^2$

## Simplest prediction ever

What means to have a good prediction?
$\min_a \sum (a_i - a)^2$

Solution, the mean:
$m = 1/n \sum a_i$

## Simplest prediction ever

For any a:
$1/n \sum(a_i – a)^2 = 1/n \sum(a_i – m + m – a)^2 =$
$= (m – a)^2 + 1/n \sum(a_i – m)^2 + 2(m-a)/n \sum(a_i – m)=$
$>= 1/n \sum(a_i – m)^2$

## Conditioning: a reminder

In data, conditioning Y|X=x means selecting the cases X=x

**[Image: Two tables. The left table shows columns X and Y with several rows. Three rows are highlighted, showing X='x' and specific Y values (1.8, 0.9). An arrow points to the right table, which shows only the highlighted rows, effectively filtering by X='x'.]**

| X | Y      |
|---|--------|
| 5 | 0.0056 |
| 7 | -2.001 |
| x | 1.8    |
| ... | ...    |
| 8 | 0.003  |
| x | 0.9    |
| ... | ...    |

$\rightarrow$

| X | Y   |
|---|-----|
| x | 1.8 |
| x | 0.9 |
| x | ... |

## Conditional Expectation: a reminder

Y|X=x is a new random variable. As such, you can calculate its expectation
$E[Y | X = x]$

**[Image: Two scatter plots. The left plot shows a vertical distribution of 'y' values (orange dots) around a central black dot representing the mean of Y. The right plot shows two distinct vertical distributions of 'y' values (red dots on left, blue dots on right), each centered around its own black dot, representing the mean of Y for different conditions (X=x1 and X=x2).]**

*   **Left Plot:**
    Unconditional Y,
    with its mean

*   **Right Plot:**
    Variables Y|X=x₁ and Y|X=x₂,
    with their mean

## Conditional Expectation: a reminder

For binary variable Y,
$E[Y | X = x] = P(Y | X = x)$

## Machine Learning Fitting

Given data $(x_i, y_i)$, Find function that minimizes empirical risk
$\min_f \sum (y_i - f(x_i))^2$

If we had infinite data:
$\min_f \int (y - f(x))^2 dP(x, y)$

Which has the following solution
$f^*(x) = E[Y | X = x]$

And also for classification problems using the accuracy loss
$f^*(x) = P(Y | X = x)$

## Adjustment formula algorithms

## Adjustment formula

**[Image: Causal Diagram showing C influencing T and O, and T influencing O. Arrows: C -> T, C -> O, T -> O.]**

```
    C
   / \
  T   O
   \ /
    O
```
(Correction from OCR: C -> T, T -> O, C -> O is implied by the structure.)

For any variables T, C, O
$P^{T=t}(O=1) = \sum_c P(O = 1 | C = c, T = t) P(C = c)$

If we have many confounders P(O| C, T) can be substituted by any classification machine learning algorithm (trained to maximize accuracy).

## S Algorithm

1- Use data $\{(t_i, c_i, y_i)\}$, to train classifier
$f(t, c)$

2- Evaluate
$ATE \sim 1/n \sum f(1, c_i) - f(0, c_i)$

**[Image: Table showing columns T, C, Y with example data.]**

| T   | C   | Y   |
|-----|-----|-----|
| $t_1$ | $c_1$ | $y_1$ |
| ... | ... | ... |
| $t_n$ | $c_n$ | $y_n$ |

References
Meta-learners for Estimating Heterogeneous Treatment Effects using Machine Learning
https://matheusfacure.github.io/python-causality-handbook/21-Meta-Learners.html

## T Algorithm

S has problems to distinguish $f(1, c_i) - f(0, c_i)$

1- Use data $\{(1, c_i, y_i)\}$, to train classifier
$f_1(c)$

Use data $\{(0, c_i, y_i)\}$, to train classifier
$f_0(c)$

2- Evaluate
$ATE \sim 1/n \sum f_1(c_i) - f_0(c_i)$

**[Image: Table showing columns T, C, Y with example data.]**

| T   | C   | Y   |
|-----|-----|-----|
| $t_1$ | $c_1$ | $y_1$ |
| ... | ... | ... |
| $t_n$ | $c_n$ | $y_n$ |

## Limitations of machine learning

## Main assumption in Machine Learning

Data is iid: the distribution when predicting is the same as the distribution in the training.

**[Image: A road sign with two arrows pointing in opposite directions. One arrow says "PAST" and the other says "FUTURE". The background is a sunset.]**

## Invariant Prediction

Implication: You can predict as far as the distribution doesn't change

**[Image: Causal diagram showing "Animal Image" leading to two factors: "Background" and "Animal Classification".]**

```
       Animal
       Image
      /     \
Background   Animal
             Classification
```

*   Machine Learning models may exploit spurious correlations to make accurate predictions
*   Cannot be detected by cross validation
*   Problem will appear when we have different distribution of features

## Invariant Prediction

**OLD DATASET**
Dogs appear in green backgrounds. ML algorithm classifies based on background

**[Image: A photo of a small dog sitting on green grass. Another photo of a blue fish.]**

**NEW IMAGES**
Background changes. Prediction may fail.

**[Image: A photo of a dog jumping in the air against a clear blue sky.]**

## Main assumption in Machine Learning

Implication: You can predict as far your actions doesn't affect the dynamics of your system!

*   Beginning of Covid, models predicted exponential growth of contagions.
*   Concerning prediction leads to decide confinement
*   Initial predictive model under confinement is not valid anymore.

## Conclusions

## Conclusions

*   ML methods can be used for the adjustment formula
*   ML targets conditional expectations
*   ML works as far as the future behaves as the past
*   CI can help to find invariant predictors