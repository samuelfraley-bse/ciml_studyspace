---
cost: 0.00071
course: General
difficulty: 5
est_minutes: 30
summary: ''
tags: []
type: Notes
---

---json
{
  "summary": "This exam for 'Causal Inference and Machine Learning: Part I' assesses understanding of panel data analysis, including OLS, Fixed Effects, dynamic models, incidental parameters in logit, and nonparametric regression techniques.",
  "topics": ["Panel Data Econometrics", "Fixed Effects Model", "OLS Estimation", "Nonparametric Regression", "Incidental Parameters Problem"],
  "doc_type": "Exam",
  "difficulty": 7,
  "study_time": 120
}
---
# Causal Inference and Machine Learning: Part I

**INSTRUCTOR: LAURA MAYORAL**

March 25, 2025. Time: 10:00-11:15 am.

Identification Number................................................

## Instructions:

1.  This is the exam corresponding to the first part of the course Causal Inference and Machine Learning (instructor: Laura Mayoral).
2.  No books, notes, calculators, or any other material (including mobile phones) are allowed during the exam.
3.  You have 75 min to complete the exam.
4.  **Please keep your answers short** and write them in the space provided after each question. This space should be in principle enough but in case you need additional space, ask for more paper. Any additional paper you use should contain your ID number.
5.  Use the last sheet in this exam to do your calculations. This page doesn't need to be returned.
6.  The maximum number of points you can get in this exam is 70.

## 1. (30 points)

Suppose you have panel data on 160 cities observed annually from 2005 to 2025. You aim to examine the impact of infrastructure investment (e.g., spending on roads, bridges, transportation systems) on regional economic growth, measured as the annual growth rate of GDP per capita. You suspect each city has time-invariant unobserved characteristics, such as geographical location, proximity to natural resources, historical trade importance, or long-standing institutional effectiveness that can have an impact on growth. You consider the following model:

(1) $Growth_{it} = β_0 + β_1 Infrastructure_{it} + W_{it}γ + d_i + ε_{it}$

where $Infrastructure_{it}$ is the amount spent by city $i$ on infrastructure projects in year $t$, $W_{it}$ are observable time-varying control variables (e.g., unemployment rate, average education level, or government debt) and $d_i$ represents city-specific, time-invariant unobserved characteristics.

(a) Suppose you estimate model (1) by ordinary least squares (OLS) (therefore omitting $d_i$, as it's not observable).
    (i) List the assumptions required for OLS to yield consistent estimates of the infrastructure effect, $β_1$.
    (ii) Under these assumptions, does the resulting OLS estimate have a causal interpretation?
    (iii) Briefly discuss whether these assumptions are likely to hold or fail in this case and how this would affect your answer to (ii).

(b) Consider now that $Infrastructure_{it}$ and $d_i$ can be correlated. Suggest a consistent estimator of $β_1$ and briefly describe one way to compute it. Under the latter assumption, do you expect the resulting estimate to be larger, smaller or very similar to the OLS estimate in point (a) above? Clearly justify your answer.

(c) Under the assumption that $d_i$ and $Infrastructure_{it}$ are uncorrelated, will the FE estimator be consistent? what would be the most efficient estimator you can employ? Describe the logic of a test that allows you to test whether $d_i$ and $Infrastructure_{it}$ are uncorrelated.

## 2. (30 points)

A variable $Y_{it}$ follows the model,

(2) $Y_{it} = β_0 + β_1x_{it} + d_i + αY_{it−1} + ε_{it}$

where $i = 1, . . ., N$ and $t = 1, . . ., T$ denote individuals and time, respectively, $N = 20000$ and $T = 5$. Assume that the usual assumptions needed for the FE estimator hold.

(a) Discuss the problems that might arise if you use the FE estimator to estimate model (2) and suggest a method that can overcome this problem. Would the problem you have described be equally relevant if $T = 2000$? Clearly justify your answer.

(b) You want to estimate the probability that a worker changes jobs, using panel data for 7000 workers over 10 periods. Your binary dependent variable $Y_{it}$ takes the value 1 if worker $i$ changes jobs at time $t$, and 0 otherwise. Briefly explain why a standard logit model with individual fixed effects suffers from the incidental parameters problem.

(c) How does the conditional logit approach address this issue? Clearly describe each of the elements in your answer.

## 3. (10 points)

The following figure presents 3 nonparametric regressions of log wage (Y axis) over years of education (X axis), obtained using the Nadaraya-Watson estimator. The difference between the three lines is the bandwidth (denoted by $h$) employed in their computation. The "default" value of $h$ is chosen using cross validation. The other two lines use half and twice the default $h$. Describe what the role of this parameter is and the tradeoffs involved in its choice. Use your answer to comment on each of the lines in the figure.

Figure: Nonparametric regressions of log wage over years of education.
-   **Y-axis:** Ipoly smooth: natural log of hwage
-   **X-axis:** Ipoly smoothing grid
-   **Legend:**
    *   Default (solid line)
    *   Half default (dashed line)
    *   Twice default (dotted line)
    
*(The figure itself, depicting three distinct lines with varying degrees of smoothness representing the different bandwidths, is omitted in this text-based conversion.)*