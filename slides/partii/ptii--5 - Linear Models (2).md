---
cost: 0.00144
course: General
difficulty: 8
est_minutes: 90
summary: This lecture introduces causal inference concepts using linear models, illustrating
  them with a business pricing problem and exploring causal graph structures like
  confounders, mediators, colliders, and instrumental variables.
tags:
- Causal Inference
- Linear Models
- Confounding
- Mediation
- Instrumental Variables
type: Lecture
---

# Causal Inference & Linear Models

Master in Data Science Upf - CI & ML - Part I

# Land2Hotel pricing problem

## Business problem

*   Land2hotel is a company that already had lots of land and they wanted to invest it on building hotels.
*   For each location, they built a hotel and set a price according to its location.
*   They had lots of success. Now they want to invest the earned money in building new hotels.
*   Using their historical data, they want to understand how placement and price affect the number of customers. In particular, now they can choose the locations for the hotels.

## Exercise

Analyze the data:
*   Distance (D)
*   Price (P)
*   Number of Customers (C)

Create the graph that describes causal relationships among the 3 variables

## Graph

```
           Distance
           /     \
         -3      -10
         /         \
       Price ----> Customers
             -2
```

## Synthetic Data

D has 3 possible values 0, 0.5, 1 sampled randomly

P:= 10 −3D + εp with ερ~ N(0,0.5)

C:= 30 -2P – 10D + εc with εc~ N(0,2)

## Exercise

Analyze the data:
*   Distance (D)
*   Price (P)
*   Number of Customers (C)

Use linear regression to calculate how an increase in price historically has affected the number of customers

## Code

```python
from numpy.random import choice, normal
import statsmodels.formula.api as smf
import random
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
random.seed(1234)

n = 100

distance = choice([0, 0.5, 1], n)
price = 10 - 3*distance + normal(loc=0, scale=.5, size=n)
customers = 30 - 2*price - 10*distance + normal(loc=0, scale=2, size=n)

df = pd.DataFrame({'price': price, 'distance': distance, 'customers': customers})

smf.ols(formula='customers ~ price', data=df).fit().summary()
```

## Linear Model

|            | coef     | std err | t      | P>|t| | [0.025 | 0.975] |
| :--------- | :------- | :------ | :----- | :---- | :------ | :----- |
| Intercept  | -0.6948  | 1.818   | -0.382 | 0.703 | -4.302  | 2.912  |
| **price**  | **1.0036** | **0.209** | **4.812** | **0.000** | **0.590**  | **1.417**  |

```python
sns.regplot(x = 'price', y = 'customers', data=df)
```