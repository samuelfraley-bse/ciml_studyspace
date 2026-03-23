---
cost: 0.00135
course: General
difficulty: 7
est_minutes: 60
summary: This presentation introduces Double Machine Learning as a tool to estimate
  average treatment effects (ATEs) by combining machine learning with residualization
  techniques and cross-fitting to achieve consistent and unbiased estimates.
tags:
- Double Machine Learning
- Causal Inference
- Average Treatment Effect (ATE)
- Frisch-Waugh-Lovell Theorem
- Cross-Fitting
type: Lecture
---

# Double Machine Learning

Master in Data Science Upf - CI & ML - Part I

# Introduction

# Objective
*   Learn a new tool to estimate ATEs based on machine learning

# Reminder - Machine Learning Fitting
Given data (x₁, y₁), Find function that minimizes empirical risk

minᵢΣ (yᵢ - f(xᵢ))²

If we had infinite data:

min∫ (y - f(x))² dP(x, y)

Which has the following solution

**f*(x) = E[Y | X = x]**

And also for classification problems using the accuracy loss

**f*(x) = P(Y | X = x)**

# Function decomposition
Any function can be decomposed

Y = E[Y|X] + ε

With E[ε|X] = 0, just writing

Y = E[Y|X] + (Y- E[Y|X])

# Linear Models & the lovely Frisch-Waugh-Lovell Theorem

# Introductory example

Introductory example
Y=θD+vX+U
D=μX+V

```
  X
 / \
D --- Y
```

# Two Options
We want to estimate θ

1.  Y~D+X
2.  Y~X, D~X, Y_residual = Y-Ŷ (and with D)
    and finally
    Y_residual ~ D_residual

```
  X
 / \
D --- Y
```

# Why could this possibly work?
D=μX+V
Y=θD+vX+U=(θμ+v)X+θV+U

So,
D-D̂ ≈V
Y-Ŷ ≈θV+U

```
  X
 / \
D --- Y
```

# Which one works better?
```R
n <- 100
x <- runif(n)
d <- -3*x + rnorm(n)
y <- -2*x + 5*d + rnorm(n)
df <- data.frame(x, d, y)
```

# Which one works better?
```R
> lm(y ~ x + d ) %>% coefficients
(Intercept)          x          d
 -0.1869549 -1.4615147  5.1036584

> res_y <- y - predict(lm(y~x, df), df)
> res_d <- d - predict(lm(d~x, df), df)
> lm(res_y ~ res_d) %>% coefficients
(Intercept)      res_d
4.761029e-15 5.103658e+00
```

# Double Machine Learning

# General decomposition
We can assume that
Y=g(X,D)+U
with E[U|X,D]=0

D=m(X)+V
with E[V|X]=0

```
  X
 / \
D --- Y
```

# Working assumption
We will work with the partially linear case
Y=θD+g(X)+U
with E[U|X,D]=0

D=m(X)+V
with E[V|X]=0

Objective: estimate θ

```
  X
 / \
D --- Y
```

# Comments
*   In linear regression Y~X+D gives the result
*   But g(X) not directly estimable in general: if you regress Y~X
    Y=θD+g(X)+U=θm(X)+g(X)+θV+U,
    where E[Y|X]=l(x)=θm(X)+g(X)≠g(X)
*   You can obtain awesome estimates of θm(X)+g(X) together (using machine learning), but horrible estimates individually θ,m(X),g(X) (think multicollinearity)

# Naive estimation
Start θ at random Alternate:
Y-θD~X to obtain ĝ
Y-ĝ(X)~D to obtain θ

```
[Chart showing "Simulation" and "N(0,1)" distributions, likely comparing the naive estimate to a normal distribution.]
```

# Reference
"Double/Debiased Machine Learning for Treatment and Structural Parameters" by Victor Chernozhukovy, Denis Chetverikovz, Mert Demirery Esther Dufloy, Christian Hansenx, Whitney Neweyy, James Robins
*   Highly technical
*   Very general: general case Y=g(X,D)+U, ATT and IV

# Main question
Can we apply the same technique in this scenario, with nonlinear g and m estimated using any accurate machine learning technique?

Y=θD+g(X)+U, E[U|X,D]=0
D=m(X)+V, E[V|X]=0

The answer is yes

```
  X
 / \
D --- Y
```

# Why does it work?
You have
Y=θD+g(X)+U
D=m(X)+V

If you proceed like
Y-E[Y|X]=θ(D-E[D|X])+U,

since
E[Y|X]=θE[D|X]+g(X)

# Double ML Algo
1.  Train ML model Y~X and calculate residuals
2.  Train ML model D~X and calculate residuals
3.  Run linear regression Y_residuals ~ D_residuals

# Double Machine Learning is n-root consistent
σ⁻¹√Ν(θ̂₀ - θ₀) → N(0, 1),

# Cross Fitting

# Machine learning overfitting
```
[Two graphs showing fitted curves to data points. The left one appears overfit, capturing noise, while the right one shows a smoother, likely better fit.]
```

# Still overfitting
Even if our algorithm does not overfit (from the point of view of machine learning), in double machine learning we will make predictions on the same set we used for training, and this creates a bias

```
[A graph showing a fitted curve to data points, similar to the overfit one from the previous slide.]
```

# Like k-fold cross validation
Used for l̂(x), m̂(x)
Used for θ

```
[A horizontal line segment divided into two parts, illustrating a data split for different purposes.]
```

# 2 Fold Cross-Fitting Algo
Split data set D in two subsets D₁, D₂

1.  Train ML model Y~X on D₁
2.  Train ML model D~X on D₁
3.  Make predictions using the above models on D₂ and calculate residuals
    Y_residuals and D_residuals
4.  Run linear regression Y_residuals ~ D_residuals to obtain an estimate θ₁
5.  Repeat the same process switching roles between D₁ and D₂, calculate the mean of the two outcomes