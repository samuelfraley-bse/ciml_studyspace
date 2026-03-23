---
cost: 0.00943
course: General
difficulty: 7
est_minutes: 240
summary: This handout introduces nonparametric local regression and semiparametric
  estimation techniques, covering various methods like Kernel regression, local linear
  regression, k-nearest neighbors, Lowess, and advanced visualization tools like binned
  scatter plots, along with their statistical properties and Stata implementation.
tags:
- Nonparametric Regression
- Kernel Estimation
- Bandwidth Selection
- Semiparametric Models
- Stata Implementation
type: Lecture
---

# Handout 4: Nonparametric Local Regression and Semiparametric estimation.

Master in Data Science for Decision Making
Barcelona School of Economics

Laura Mayoral
IAE and BSE
Barcelona, Winter 2026

## 1. Introduction

*   **Goal:** estimate the relationship between y and x without imposing a functional form.
*   → the same, in more technical words:
    **Nonparametric estimation of the conditional expectation.**
*   The conditional expectation of Y conditional on X (a univariate variable) at X = x0:
    $E[Y |X = x_0] = m(x_0)$
    $m(.)$ is not specified.

*   In this lecture we will develop nonparametric regression techniques
*   We will start by considering that X is a scalar variable (recall the curse of dimensionality)
*   These nonparametric methods are **local averaging methods:** estimates are obtained by cutting the data into ever smaller slices as $N \rightarrow \infty$ and estimating local behavior within each slice.
*   In a nutshell: for each point $x_0$ those estimators are **weighted** (typically, kernel weights) **local** (=in a neighbor of $x_0$) averages of values of $y$

## An introductory example

*   Consider the relationship between hours worked per day and hourly wage (simulated data)
*   We run an OLS regression and get a very positive relationship

```
reg y x

Source | SS | df | MS | Number of obs = 200
-------+----------+----+----------+-------------+----------
Model | 15.6516595 | 1 | 15.6516595 | F(1, 198) = 39.00
Residual | 79.4533954 | 198 | .401279775 | Prob > F = 0.0000
-------+----------+----+----------+ R-squared = 0.1646
Total | 95.1050549 | 199 | .477914849 | Adj R-squared = 0.1604
                                   | Root MSE = .63347

------------------------------------------------------------------
y | Coefficient Std. err. t P>|t| [95% conf. interval]
----+-------------------------------------------------------------
x | .1885303 .0301873 6.25 0.000 .1290004 .2480602
_cons | .7854619 .2283455 3.44 0.001 .3351607 1.235763
------------------------------------------------------------------
```

*   However, plot the data: highly nonlinear relationship

(Image of a scatterplot with a linear regression line overlaid, showing a non-linear relationship where the line doesn't fit well. X-axis "wage", Y-axis "hours per day" (implied, by context of the example). Data points scatter around a curve that first increases and then decreases.)

*   We could estimate two lines, one for the increasing part of the relationship and one for the decreasing one:

(Image of a scatterplot with two linear regression lines, one for the increasing part and one for the decreasing part, creating a piecewise linear fit.)

*   Or we could even consider more regression lines, (i.e., a smaller ”bandwidth”)

(Image of a scatterplot with multiple shorter linear regression segments, providing a more flexible fit than the two-line example.)

*   The previous methods will work if we know the breaking points (we’re imposing them when running the OLS regressions).
*   Nonparametric methods share a similar spirit: they are local averaging methods.
*   No need to impose any breaking points as in this example!
*   estimates are obtained by cutting the data into ever smaller slices as $N \rightarrow \infty$ and estimating local behavior within each slice.

*   Parametric versus Nonparametric methods:
*   Asymptotic properties are quite different
*   Lower convergence rates: because of local averages (less than less than N observations in estimating each slice)
*   In simplest cases still asymptotically normally distributed;
*   Due to lower convergence rates, biases appear
*   Things become in general a bit “uglier” and properties are a bit ”less nice” than in parametric estimation, so be a bit patient!

### 1.2. Some simple visualization tools

**Scatterplot**

*   Before we begin with the complicated stuff, let’s always look at the data first!
*   Consider this example: The relation between tenure on the job and hourly wage.
*   DATA (example STATA: `sysuse nlsw88`)
*   Simplest visualization tool: `scatterplot`

(Image of a scatterplot showing "Hourly wage" on the Y-axis and "Job tenure (years)" on the X-axis. Many data points are clustered at lower tenure values, making the relationship unclear.)

*   What can you say about the relationship between wage and tenure by looking at this graph?
*   Not much!
*   scatterplots are not very useful for large data sets

### A better way of plotting the data: binned scatter plots

*   If a lot of data points: scatter plots are not very useful (clouds of millions of points! impossible to see anything)
*   **Binned scatters:** very useful visualization tools, particularly for large datasets
*   Compare the scatter and the binned scatter plot (on same data)

(Two images side-by-side: The left image is the same dense scatterplot of "Hourly wage" vs "Job tenure (years)" as before. The right image is a binned scatterplot of "wage" vs "tenure", showing far fewer points, each representing the mean of a bin, with a clear positive linear trend visible.)

*   The second graph is much more informative that the first one about the shape of the conditional expectation.
*   From the second graph, you can easily see that
    *   There’s a positive relationship between tenure and wage
    *   This relationship seems to be pretty linear
*   What’s the magic?
*   Binned scatter plots are visual, simple, nonparametric estimators of the conditional expectation.

*   How they work (from STATA help)
    **STATA command: Binscatter**
    *   groups the x-axis variable into equal-sized bins (number of bins to be determined by you, default empirical ventiles)
    *   computes the mean of the x-axis and y-axis variables within each bin (median is also an option)
    *   then creates a scatterplot of these data points.
    *   The result is a non-parametric visualization of the conditional expectation function.

*   Let’s see this graphically
    **What is a binned scatter plot?**
    **Step 1:** Start with a familiar scatter plot

    (Image of a scatterplot with Y-axis "y" and X-axis "x", showing a U-shaped pattern of grey data points.)

    **Step 2:** Partition the support of X into bins

    (Image same as Step 1, but with vertical dashed lines added, dividing the X-axis into several equal-width bins.)

    **Step 3:** Find the average Y in each bin

    (Image same as Step 2, but with blue dots added. Each blue dot represents the average Y-value within its respective X-bin.)

    **Step 4:** Plot only bin means

    (Image with only the blue dots from Step 3, without the original grey scatter points or the bin lines. It clearly shows the U-shaped trend with discrete points.)

    **Step 5:** Add a polynomial fit to raw data

    (Image with the blue dots from Step 4, and a grey linear regression line drawn through the data. An arrow points to the line with text "using raw data" implying this fit comes from the original scatter data.)

*   Typical Example: Chetty, Friedman and Rockoff (2014, AER)
    a) College Attendance at Age 20

    (Image of a scatterplot with "Percent in College at Age 20" on the Y-axis and "Normalized Teacher Value Added (RV_t)" on the X-axis. Blue dots show binned data, with a red linear regression line overlaid. A coefficient and standard error are displayed: "Coef. = 0.82% (0.07)". The dots mostly follow a positive linear trend.)

    **Note:** n = 4,170,905 with # of bins J = 20

*   **Additional options of the binscatter command Plot the data conditioning by different values of other variable**
    1.  You can plot the data for values of other variable.
        For instance, by race
        `binscatter wage tenure, by(race) nq(50)`

        (Image of a binned scatterplot with "wage" on the Y-axis and "tenure" on the X-axis. There are two sets of binned points and regression lines: one for "race=White" (blue) and one for "race=Black" (red), showing different relationships between wage and tenure for the two groups.)

*   **Control for other variables**
    2.  You can control for other variables that you might think are relevant.
        Control for age.
        `binscatter wage tenure, control(age) nq(50)`

        (Image of a binned scatterplot with "wage" on the Y-axis and "tenure" on the X-axis, showing binned points and a regression line. This plot is adjusted for age, and the linear trend is visibly clearer.)

*   **How does binscatter deal with control variables?**
    Method inspired by a famous theorem in regression analysis:
    **The Frisch-Waugh-Lowell Theorem**
*   Binscatter residualizes the x-variable and y-variables on the specified controls before binning and plotting.
*   That is,
    *   `regress y/z, save residuals, e1, add mean of y to e1, obtain e'_1`
    *   `regress x/z, sav residuals, e2, add mean of x to e2, obtain e'_2`
    *   `Plot e'_1 on e'_2`
*   This is in fact trickier than it looks and not so ”safe”: only valid if conditional expectation is linear

*   **An improved approach to binned scatterplots: Cattaneo et al., 2024**
*   A very recent paper improves on traditional methodology (published May 2024!): [Cattaneo et al (2024)](https://www.aeaweb.org/articles?id=10.1257/aer.20210086)
*   The “traditional” approach of residualizing first the data only justified when the conditional expectation is linear.
*   Otherwise: don’t do it!
*   This paper also provides:
    *   Ways of doing inference
    *   Optimal binning selection

*   We’ll go back to this paper when we study semiparametric methods (partially linear model)
*   But you can install the stata package to play with it in the meantime:
    **STATA package: `binsreg`**

*   **STATA code to generate this example:**
    *   Load data in stata memory:
        `sysuse nlsw88`
        `keep if inrange(age,35,44) & inrange(race,1,2)`
        `keep if inrange(age,35,44) & inrange(race,1,2)`
        `scatter wage tenure, graphregion(color(white)) lwidth(thick)`
        `binscatter wage tenure, nq(50)`
        `binscatter wage tenure, control(age) nq(50)`
        `binscatter wage tenure,by(race) nq(50)`

### Takeaways

*   Always start by plotting your data
*   Binned scatterplots are very useful tools, particularly when there are a lot of data points
*   Visual and quick estimator of the conditional expectation
*   Quite flexible stata command, allows to eliminate impact of other variables (linearly) (but notice the limitations of this, only valid under linearity!)
*   But binscatter is not enough! (no inference, a bit too crude...)
*   New binned scatter technique: Cattaneo et al, (2024) –to be reviewed soon.

### Overview of the handout

*   The remaining of this handout: Different approaches to carry out nonparametric regression.
*   Different methods: Kernel local (constant) regression; Local linear/polynomial regression; Lowess, . . .
*   Intuition is simple, technical stuff becomes complicated
*   We will look at
    1) Intuition;
    2) implementation;
    3) differences across the methods; etc
    4) stata tips (more on this in the TA session);

### Roadmap of this handout

1.  Introduction: Nonparametric Local Regression;
    1.2. Some simple visualization tools
2.  Local Weighted Averages
3.  Kernel Local Regression: implementation, properties
4.  Local Linear Regression
5.  K-Nearest Neighbor
6.  Lowess
7.  Semiparametric regression

## 2. A bit of intuition: Local Weighted Averages

*   **Model:**
    $y_i = m(x_i) + \epsilon_i, \quad i = 1, \dots, N, \quad \epsilon_i \overset{iid}{\sim} (0, \sigma^2_\epsilon).$ (1)
    and $E(\epsilon|x) = 0$.
*   Under these assumptions, the conditional expectation is
    $m(x_0) = E(y|x = x_0).$ (2)
*   **Problem:**
    $m(.)$ unspecified $\rightarrow$ use nonparametric methods to estimate it at a point $x_0$

### A bit of intuition about local average estimators

*   Suppose that at $x_0$, there are multiple observations on $y$, say $N_0$ observations.
*   A simple estimator for $m(x_0)$ is the sample average of these $N_0$ values of $y$.
    $\hat{m}(x_0) = \sum_{i=1}^{N_0} w_i y_i$
    where $w_i = 1/N_0$ if $x = x_0$ and 0 otherwise.
*   Notice that (for fixed $x_0$):
    $\bar{m}(x_0) \sim \left(m(x_0), \frac{\sigma^2}{N_0}\right),$ (3)
*   Why? it is the average of $N_0$ observations that are i.i.d with mean $m(x_0)$ and variance $\sigma^2_\epsilon$.

*   The estimator $\bar{m}(x_0)$ is unbiased but not consistent (in general)
*   Why? Consistency requires $N_0 \rightarrow \infty$ as $N \rightarrow \infty$, so that $V[\bar{m}(x_0)] \rightarrow 0$.
*   But $N_0$ can be really small, particularly for continuous variables! (most likely, just one observation of y)

*   Then:
*   The **Problem** of this approach: not enough observations to average ($N_0$ can be too small, it can even be 1 for continuous variables even with a huge sample!)
*   A **Solution**: consider averages of $y$ when $x$ is close to $x_0$, (in addition to when $x$ exactly equals $x_0$).

*   **Local weighted average estimator:**
*   a weighted average of the dependent variable in a neighborhood of $x_0$.
    $\widehat{m}(x_0) = \sum_{i=1}^{N} w(x_i, x_0, h) y_i$
    where the weights $w(x_i, x_0, h)$ sum to 1 and vary with :
    *   the sample values of the regressors, $x_i$
    *   the evaluation point $x_0$
    *   the value of $h$, i.e., the length of the window around $x_0$

### Note: The OLS estimator has a “similar” structure

*   This estimator is not “that different” from those you’ve used in the past!
*   Recall that the OLS estimator is also a weighted average of $y_i$, since some algebra yields
    $\hat{m}_{OLS}(x_0) = \sum_{i=1}^{N} \left(\frac{1}{N} + \frac{(x_0 - \bar{x})(x_i - \bar{x})}{\sum_j (x_j - \bar{x})^2}\right) y_i$
*   The OLS weights are different though:
    *   Local regression uses weights that are decreasing as $x_i$ gets far away from $x_0$ (if, for example, $x_i > x_0 > \bar{x}$)
    *   OLS weights don’t verify this, in fact, weights can even increase with increasing distance from $x_0$

### Back to the local weighted average estimator

*   **h:** bandwidth parameter. Smaller values of h $\rightarrow$ smaller window $\rightarrow$ more weight being placed on those observations with $x_i$ close to $x_0$.
*   **2h:** window width
*   The most common weight functions are:
    1.  Kernel weights
    2.  Lowess
    3.  k-nearest neighbors
*   Modus operandi: compute $\widehat{m}(x_0)$ at a variety of points of $x_0$ to obtain a regression curve.

## 3. Kernel regression: Nadaraya-Watson (NW) estimator

*   Recall the **Model:**
    $y_i = m(x_i) + \epsilon_i, \quad i = 1, \dots, N,$ (4)
    $E(\epsilon|x) = 0,$
    $E(\epsilon^2|x) = \sigma^2(x)$
    .
*   Recall the **Goal:** Estimate of $m(x_0)$,
    $m(x_0) = E(y|x = x_0).$ (5)

*   Let’s now analyze the case where we use **Kernel weights**
*   **Kernel regression** is a weighted average estimator using kernel weights.
*   Consider again the local weighted average estimator, where we compute the average of the y’s in an interval of length 2h around $x_0$
    $\widehat{m}(x_0) = \frac{\sum_{i=1}^{N} \mathbf{1}(|\frac{x_i - x_0}{h}| < 1) y_i}{\sum_{i=1}^{N} \mathbf{1}(|\frac{x_i - x_0}{h}| < 1)}$
    *   The numerator: sums the y’s in the interval $(x_0 \pm h)$
    *   The denominator: gives the total number of y’s that have been summed in the numerator

*   Thus: the previous expression is an average of the y’s with **equal weights** (weights are relative frequency of y in the window)
*   Consider instead **Kernel weights**
*   Why?
    *   non-constant weights
    *   give more weight to observations close to $x_0$
*   **Kernel Regression Estimator**
    $\widehat{m}(x_0) = \frac{\sum_{i=1}^{N} K\left(\frac{x_i - x_0}{h}\right) y_i}{\sum_{i=1}^{N} K\left(\frac{x_i - x_0}{h}\right)}$
    (also called Nadaraya-Watson estimator)
*   similar Kernels as before: Gaussian, Epanechnikov, etc.

### Example: Nadaraya-Watson estimator for the hours worked/wage problem

(stata defaults for h, kernel... –we’ll learn about them)
`lpoly y x , ci msize(small) graphregion(color(white))`

(Image of a scatterplot with "hours per day" on the Y-axis and "wage" on the X-axis. A local polynomial smooth curve (red line) is overlaid, along with a 95% CI band (grey shaded area). The curve shows an initial increase and then a decrease, fitting the nonlinear pattern of the data. The legend indicates `kernel = epanechnikov, degree = 0, bandwidth = .18, pwidth = .27`.)

### Implementation of the NW estimator

#### 1. Kernel choice

*   Kernel choice: $MISE(h^*)$ is minimized by the Epanichnikov Kernel (as before)
*   but small differences across kernels for optimal $h^*$
*   **Choice of bandwidth is much more important than choice of kernel**

### Implementation of the NW estimator, II

#### 2. Bandwidth choice

*   **Optimal bandwidth:** recall the tradeoff between bias&variance in the choice of h.
*   Optimal bandwidth: trades off **bias** (minimized with small bandwidth) and **variance** (minimized with large bandwidth)
*   Recall the trade-off:
    *   Incorporating values of $y_i$ for which $x_i \ne x_0$ into the weighted average introduces bias, since $E[y_i|x_i] = m(x_i) \ne m(x_0)$ for $x_i \ne x_0$.
    *   However, using these additional points reduces the variance of the estimator, since we are averaging over more data.

*   The optimal bandwidth balances the trade-off between increased bias and decreased variance, using squared error loss.
*   Variance=$O((Nh)^{-1})$; bias=$O(h^2)$
*   Theory just says that the optimal bandwidth (=the one that minimizes MISE) for kernel regression is $O(N^{-0.2})$ (but this is useless for choosing h in applications). Why this value: makes the squared bias and the variance of the same order of magnitude.
*   In practice: plug-in estimator of the optimal h using MISE(h) is complicated now (estimation of the plug-in estimation requires estimation of $m''(x)$, second derivative of conditional expectation which is difficult to estimate).
*   Alternative: **Cross-validation**, computationally intensive, but easier to implement

### Choosing the bandwidth: Cross-validation

*   Cross-validation is a popular technique for many prediction problems
*   Cross-validation, in general:
    *   Construct prediction models that perform well **out of sample**
*   Simple idea:
    *   we split the data in two sets: **training set** and **validation set**
    *   Use the data in the training set to construct the estimator.
    *   Using this estimator, predict the “out of sample” observations, i.e., the obs. in the “validation set”, calculate the error.
    *   Choose the estimator with best out of sample performance

*   Why leaving some observations out?
*   **Avoid overfitting:**
    *   an estimator that is very good for the in-sample data but can perform badly for non-seen observations
    *   why is that? because in a dataset there’s always noise. If we perfectly fit that data, we fit both the “signal” (what really matters in the data) AND the noise, something that is pure random variation.
    *   Since the noise changes in every realization of the data, a model that fits very well a dataset can perform badly out of sample

*   Cross validation, in particular:
*   Goal: use cross-validation to choose a value of h that yields a good estimate $m(x)$
*   Idea
    *   For each observation i, compute an estimator $m_i$ using cross validation i.e., using a “training sample” to compute the estimator
    *   ...and a validation sample only used to compute out of sample prediction error
    *   Then, choose h that yields smallest MSE.

*   How it works (a bit simplified):
    1.  For each i, define the training sample as all the observations except obs. i; validation sample: observation i
    2.  The estimator leaving i out is given by
        $\hat{m}_{-i}(h, x_i) = \frac{\sum_{j \ne i} w_{j,h} y_j}{\sum_{j \ne i} w_{j,h}}$
    3.  Compute CV(h) (very similar to the MSE(h))
        $CV(h) = \sum_{i=1}^{n} (y_i - \hat{m}_{-i}(x_i))^2 \pi(x_i),$ (6)
    *   $\pi(x_i)$: weights introduced to potentially downweight the end points, to prevent those points to receive too much attention (local weighted estimates can be quite highly biased at the end points)

*   4. $h^*_{cv}$ is chosen as the value that minimizes the CV(h)
*   5. In practice CV(h) is computed over a range of values of h. Choose the value of h that makes it smallest.
*   Properties of $\hat{h}_{cv}$: converges to $h^*$ (optimal h), but slowly ($\approx$ low convergence rate)

(Image of a plot showing "5-CV Error" on the Y-axis and "Smoothing Bandwidth" on the X-axis. The plot shows a U-shaped curve, indicating that the cross-validation error first decreases and then increases as bandwidth increases, with a clear minimum point.)

### Takeaways

*   In Kernel regression, cross-validation tends to perform better than the plug-in estimator
*   Logic of Cross-validation: choose the h that minimizes the (out of sample) mean prediction error
*   Why leaving one observation out at a time?
    *   nonparametric methods are very flexible, and if we consider the whole sample, we can get an almost “perfect fit”
    *   $\Rightarrow$ Overfitting!

## Statistical Properties of Kernel regression estimators

### 1. The Kernel regression estimator is consistent

*   $\tilde{m}_0$ is consistent if some conditions on h and Nh hold
*   Recall: these conditions are needed for developing the theory; not informative to choose the value of h in practice
*   The estimator is consistent provided:
    *   h $\rightarrow$ 0: i.e., substantial weight is given only to $x_i$ very close to $x_0$.
    *   AND
    *   Nh $\rightarrow \infty$: i.e., there’s “many” $x_i$ close to $x_0$ as $n \rightarrow \infty$, so that many observations are used in forming the weighted average.

### 2. The Kernel regression estimator is biased in finite samples

*   It can be shown that
    $\widehat{m}(x_0) = m(x_0) + O(h^2)$
*   Asymptotically, the bias tends to zero under the assumptions above (i.e. if h tends to zero)
*   However, the bias can be substantial in finite samples
*   Particularly, at the end points (where few observations exist)
*   When considering confidence intervals, the estimate is centered in the true value of m plus the bias!

### 3. The Kernel regression estimator is asymptotically normal

*   Rate of converge: $\sqrt{Nh}$: smaller than the usual $\sqrt{N}$
*   Asymptotic distribution (notice the bias!)
    $\sqrt{Nh}(\widehat{m}(x_0) - m(x_0) - b(x_0)) \rightarrow N\left(0, \frac{\sigma^2_\epsilon}{f(x_0)} \int K(z)^2 dz\right)$ (7)
*   Notice that $f(x_0)$ appears in the denominator
*   This implies that the variance term in is larger for small $f(x_0)$, i.e., when there’re few ’x’s in the neighborhood of $x_0$, which makes sense

### Constructing Confidence Intervals

*   Estimates of $m(x_0)$ typically are provided with CI
*   How can we compute them?
    1.  Use the asymptotic distribution above ignoring the bias. Then:
        $m(x_0) \in \widehat{m}(x_0) \pm 1.96 \sqrt{\frac{1}{Nh} \frac{\hat{\sigma}^2_\epsilon}{\hat{f}(x_0)} \int K(z)^2 dz}$
*   But two problems
    *   **Problem 1** Convergence to the normal distribution is slow (recall the lower convergence rates)
    *   **Problem 2** Forgetting the bias means that the CI are not centered correctly!

### Solutions.

*   **Problem 1.** Don’t use the asymptotic distribution, instead use bootstrap (i.e., a method that approximates the finite sample distribution)
*   **Problem 2.** Reduce the bias:
    a) Undersmoothing
    b) using higher order Kernels (Fourth-order, Gaussian Fourth-order quartic): the bias when these kernels are employed are $O(h^4)$
    c) Use alternative methods that are less biased: Local polynomial regression, Lowess . . . (smaller bias)
*   For instance, you can use bootstrad AND undersmoothing

### Trimming

*   Recall the definition of the NW estimator:
    $\widehat{m}(x_0) = \frac{\sum_{i=1}^{N} K\left(\frac{x_i - x_0}{h}\right) y_i}{\sum_{i=1}^{N} K\left(\frac{x_i - x_0}{h}\right)}$
*   Notice that the denominator is $\hat{f}(x_0)$, the kernel density estimator.
*   **Problem** : For some $x_i$, $f(x_i)$ can be very small (i.e., values that are unlikely). Since the estimate of the density appears in the denominator, this can lead to a very large value (in abs. value) of $\widehat{m}(x_i)$.
*   Such problems are most likely to occur in the tails of the distribution.

*   **Trimming:** eliminates or greatly downweights all points with $f(x_i) < b$, say, where $b \rightarrow 0$ as $N \rightarrow \infty$.
*   For nonparametric estimation one can just focus on estimation of $m(x_i)$ for more central values of $x_i$,
*   However, the semiparametric methods of Section 9.7 can entail computation of $m(x_i)$ at all values of $x_i$, in which case trimming is typically employed.

### Example

*   DATA: PSID Individual Level Final Release 1993 data, ([www.isr.umich](http://www.isr.umich) then choose Data Center )
*   Relation between years of completed education and (log of) wages
*   Females in their 30’s
*   Data from Cameron and Trivedi

*   OLS regression:
*   highly significant role of education;

```
regress lnhwage educatn

Source | SS | df | MS | Number of obs = 177
-------+----------+----+----------+-------------+----------
Model | 15.189945 | 1 | 15.189945 | F(1, 175) = 25.19
Residual | 105.519895 | 175 | .602970827 | Prob > F = 0.0000
-------+----------+----+----------+ R-squared = 0.1258
Total | 120.70984 | 176 | .685851362 | Adj R-squared = 0.1208
                                   | Root MSE = .77651

------------------------------------------------------------------
lnhwage | Coefficient Std. err. t P>|t| [95% conf. interval]
--------+---------------------------------------------------------
educatn | .1833945 .0206 5.02 0.000 .0627381 .144051
_cons | .8966776 .2657917 3.37 0.001 .3721077 1.421247
------------------------------------------------------------------
```

*   interpretation : marginal effect
*   an increase in one year of education increases by 10% hourly wage.

*   **But...is the linearity assumption reasonable?**
*   Let’s plot the data (scatter plot)
    `twoway scatter lnhwage educatn, graphregion(color(white))`

(Image of a scatterplot with "natural log of hwage" on the Y-axis and "years of completed schooling 1992" on the X-axis. The data points show some scatter but suggest a non-linear, possibly concave, relationship.)

*   **Binned scatter plot**
    **STATA: binscatter command**
    `binscatter lnhwage educatn, nq(20)`
    `binscatter lnhwage educatn, nq(20) line(qfit)`
    (first graph imposes a linear fit on the data, second is more flexible, allows for a quadratic one)

(Two images side-by-side. Both show binned scatterplots of "lnhwage" vs "educatn". The left plot has a linear fit. The right plot has a quadratic fit (a curve), which appears to follow the trend of the binned data more closely, suggesting a non-linear relationship between education and log wage.)

### Nonparametric regression in STATA

**The `lpoly` and `npregress` commands**

*   STATA has several commands to do nonparametric regression: `lpoly`, `npregress` (the latter has more options)
*   `lpoly`: Kernel-weighted local or polynomial smoothing
    *   Less options than `npregress`
    *   Very easy to use

*   `npregress kernel`:
    *   From Stata 15 onwards: a new command, `npregress`
    *   Determines bandwidth by cross-validation whereas `lpoly` uses plug-in value
    *   Evaluates at each $x_i$ value (whereas `lpoly` default is to evaluate at 50 equally spaced values)
    *   For local linear, computes partial effects.
    *   Can use `margins` and `marginsplot` for plots and average partial effects.
    *   Can deal with more than one regressor.
    *   we’ll see an example in a few slides

### Example

`lpoly lnhwage educatn, ci`
(STATA default values, default is degree 0 –constant–; plug in estimator)

(Image of a scatterplot with "natural log of hwage" on the Y-axis and "years of completed schooling 1992" on the X-axis. A local polynomial smooth curve (dashed line) is overlaid along with a 95% CI band (grey shaded area) and the original scatter points. The curve shows an increasing but concave relationship. The legend indicates `kernel = epanechnikov, degree = 0, bandwidth = .85, pwidth = 1.28`.)

*   Try different values for the bandwidth

(Image of a plot showing "lpoly smooth: natural log of hwage" on the Y-axis and "lpoly smoothing grid" on the X-axis. Three different local polynomial smooth lines are shown: "Default", "Half default", and "Twice default", illustrating how bandwidth affects the smoothness and fit of the curve.)

### Takeaways

*   First nonparametric regression method: Kernel local regression
*   In a nutshell: local averages of the dependent variable, y
*   Choice of bandwidth is key
*   Use cross-validation to select h
*   Choice of kernel is less important, optimal kernel: Epanechnikov
*   STATA commands: npregress, lpoly
*   Asymptotic properties: consistent, asymptotically normal
*   Lower convergence rates

*   A few problems to be aware about:
*   When computing confidence intervals: take into account bias reduction techniques
    *   If asymptotic distribution is employed: undersmoothing, higher order kernels
    *   Use bootstrap
*   Open problem: How to compute marginal effects?

## 4. Other methods: Local Linear Regression

*   The Nadaraya–Watson estimator can be seen as a particular case of a wider class of nonparametric estimators, the so-called local polynomial estimators.
*   The Nadaraya–Watson estimator is a **local constant** estimator because it assumes that $m(x)$ equals a constant in the local neighborhood of $x_0$.
*   Now: let $m(x)$ be **linear** in the neighborhood of $x_0$,
    $m(x) = a_0 + b_0(x - x_0)$ in the neighborhood of $x_0$
    .

### Implementation of this idea

1) Notice that the kernel regression estimator (previous estimator) $m(x_0)$ can be obtained as
$\widehat{m}(x_0) = \operatorname{argmin}_{m_0} \sum_i W\left(\frac{x_i - x_0}{h}\right)(y_i - m_0)^2$
where the weights are the NW weights:
$W\left(\frac{x_i - x_0}{h}\right) = K\left(\frac{x_i - x_0}{h}\right) / \sum_{j=1}^{N} K\left(\frac{x_i - x_0}{h}\right)$
Why? remember that $m(x_0)$ is a constant and $e_i = y_i - m_0$. Then, this is similar as weighted least squares, $e_i = y_i - m_0$.

2) Consider now $m_0 = a_0 + a_1(x_i - x_0)$. Obtain the local linear estimator as:
$\widehat{m}(x_0) = \operatorname{argmin}_{a_0, a_1} \sum_i W\left(\frac{x_i - x_0}{h}\right)(y_i - a_0 - a_1(x_i - x_0))^2$
Then, the estimate of m in a neighborhood of $x_0$ is given by
$\hat{m}(x) = \hat{a}_0 + \hat{a}_1(x - x_0)$
*   Same idea: this is (local) weighted least squares regression, where the weights are kernel weights
*   Interpretation:
    *   The constant $a_0$ is the conditional mean at $x_0$.
    *   The slope parameter, $a_1$: is the derivative of the mean function with respect to x.

3) More generally, we can consider a local polynomial estimator of degree p
$\operatorname{argmin}_{a_0, a_1} \sum_i W\left(\frac{x_i - x_0}{h}\right)(y_i - a_0 - a_1(x_i - x_0) \cdot \dots - a_p(x_i - x_0)^p)^2$

### Some advantages over NW

*   **Higher accuracy:** Local linear regression estimators use a more flexible model that allows for a more accurate fit to the data, especially in regions where the data may be changing rapidly. Better behavior at end points (always problematic because of low density of data points).
*   **Easy computation of derivatives:** (very useful for interpreting results)
*   Cons: A bit more costly computationally than NW

### Example

*   Consider again the education/wage example: we will estimate a local linear regression
*   STATA: Can be estimated using `lpoly` or `npregress`
    `lpoly lnhwage educatn,degree(1).`
    Or `npregress kernel lnhwage educatn` –several options available!–
*   Let’s look at the latter
*   `npregress` command - default is local linear
*   The output reports averages of the mean function and the effects of the mean function.
*   An average effect may be either 1) an average marginal effect, for continuous covariates or 2) the mean of contrasts for discrete covariates.

### npregress kernel lnhwage educatn

```
npregress reports averages $\hat{\alpha} = \frac{1}{N}\sum_{i=1}^N \widehat{\alpha}(x_i)$ and $\hat{\beta} = \frac{1}{N}\sum_{i=1}^N \widehat{\beta}(x_i)$

Bandwidth

           Mean   Effect
educatn  2.94261  4.00482

Local-linear regression               Number of obs =      177
Kernel   : epanechnikov                 E(kernel obs) =      177
Bandwidth: cross validation             R-squared     =    0.1943

lnhwage | Estimate
--------+----------
Mean    | 2.223502
--------+----------
Effect  |
educatn | .1492393

Note: Effect estimates are averages of derivatives.
Note: You may compute standard errors using vce(bootstrap) or reps().

Versus OLS $\hat{\alpha} = 0.897$ and $\hat{\beta} = 0.10$
```

*   First table: bandwidth employed
*   Notice that different bandwidths are employed for mean effect and for the derivative effect
*   Second table: averages of the means point and for the effects (derivative)
*   Notice that by default standard errors do not appear, you can get them though by explicitly asking for them.

### npgraph:

(Image of a scatterplot of "natural log of hwage" vs "years of completed schooling 1992", with a local-linear estimate curve (red line) overlaid. The curve shows an increasing, slightly concave relationship, fitting the data points. The legend indicates "Local-linear estimates kernel = epanechnikov bandwidth = 2.94261".)

### Obtain bootstrap standard errors and confidence intervals for these values

`npregress kernel lnhwage educatn, vce(bootstrap, seed(10101) reps(50))`

```
Get bootstrap standard errors

. npregress with bootstrap standard errors
. npregress kernel lnhwage educatn, vce(bootstrap, seed(10101) reps(50))
(running npregress on estimation sample)

Bootstrap replications (50)
----+---1---+---2---+---3---+---4---+---5
..................................................    50

Bandwidth

           Mean   Effect
educatn  2.94261  4.00482

Local-linear regression               Number of obs =      177
Kernel   : epanechnikov                 E(kernel obs) =      177
Bandwidth: cross validation             R-squared     =    0.1943

lnhwage | Observed    Bootstrap
        | estimate  Std. Err.    z    P>|z|  [95% Conf. Interval]
--------+---------------------------------------------------------
Mean    |
lnhwage | 2.223502  .0635099   35.01  0.000   2.121183   2.325821
--------+---------------------------------------------------------
Effect  |
educatn | .1492393  .0242175    6.16  0.000   .114171    .1941928

Note: Effect estimates are averages of derivatives.

Versus OLS se($\hat{\alpha}$) = 0.302 and se($\hat{\beta}$) = 0.023.
```

### Plot the graph: Estimated value of $m(x_0)$, the conditional mean of log wage at $x_0$

`margins, at(educatn = (1(1)16)) vce(bootstrap, seed(10101) reps(50))`
`marginsplot, legend(off) scale(1.1) /// addplot(scatter lnhwage educatn if lnhwage<50000, msize(tiny))`

(Image of a scatterplot of "Mean function" (Y-axis) against "years of completed schooling 1992" (X-axis). The plot shows blue dots representing adjusted predictions, with vertical error bars indicating 95% confidence intervals. The curve itself is increasing and slightly concave, fitting the general pattern of the data and its confidence bands.)

### Partial effects of changing education

`margins, at(educatn = (10(1)16)) contrast(atcontrast(ar)) /// vce(bootstrap, seed(10101) reps(50))`
`marginsplot, legend(off)`

(Image of a plot showing "Contrasts of mean function" (Y-axis) against "years of completed schooling 1992" (X-axis) from 11 to 16. The plot displays points connected by lines, with vertical error bars indicating 95% confidence intervals. This visualizes the change in the mean function at different levels of education.)

### Stata code

```stata
npregress kernel lnhwage educatn
npregress kernel lnhwage educatn, vce(bootstrap, seed(10101) reps(50))
margins, at(educatn = (10(1)16)) vce(bootstrap, seed(10101) reps(50))
marginsplot, legend(off) scale(1.1) /// addplot(scatter lnhwage educatn if lnhwage<50000, msize(tiny))
graph export nonparametricfig11.wmf, replace
margins, at(educatn = (10(1)16)) contrast(atcontrast(ar)) /// vce(bootstrap, seed(10101) reps(50))
marginsplot, legend(off)
graph export nonparametricfig13.wmf, replace
```

## 5. Nearest Neighbor Estimator

*   **Simple idea:** The k-nearest neighbor estimator is the weighted average of the y values for the k observations of $x_i$ closest to $x_0$.
*   Define $N_k(x_0)$: the set of k observations of $x_i$ closest to $x_0$.
*   Then:
    $m_{KNN}(x_0) = \frac{1}{k} \sum_i \mathbf{1}(x_i \in N_k(x_0)) y_i$
*   This estimator is
    *   a kernel estimator with uniform weights
    *   except that the **bandwidth is variable.**
*   Here the bandwidth $h_0$ at $x_0$ equals the distance between $x_0$ and the furthest of the k nearest neighbors, and more formally $h_0 = k/(2Nf(x_0))$.

*   Pros: a simple rule for variable bandwidth selection.
*   It is computationally faster to use a symmetrized version that uses the k/2 nearest neighbors to the left and a similar number to the right

## 6. Lowess

*   **Lowess:** locally weighted scatterplot smoothing estimator
*   A variant of local polynomial estimation (kernel)
*   Computational Differences:
    *   uses a **variable bandwidth** $h_{0,k}$ determined by the distance from $x_0$ to its kth nearest neighbor;
    *   tricubic kernel
    *   Robust against outliers: **downweights observations with large residuals** $e_i = y_i - m(x_i)$, which requires passing through the data N times.

*   Lowess has some advantages with respect to local lineal regression:
    *   More robust against outliers
    *   But computationally more expensive
    *   See Fan and Gijbels (1996, p. 24). for additional details Lowess is attractive compared to kernel regression as it uses a variable

### Example

*   Comparison of local constant, local linear and lowess: wage and years of education
*   To compute lowess: (`lowess lnhwage educ, clstyle(p3)`), `scale(1.1)`
    ///

(Image of a plot showing "lnhwage" (Y-axis) against "educatn" (X-axis). Three different smoothing curves are overlaid: "Kernel" (solid), "Local linear" (dashed), and "lowess" (dotted). The plot visually compares how these different methods fit the data, with Lowess often showing a more flexible or wiggly fit.)

### Multivariate Kernel Regression:

*   Conceptually, multivariate kernel regression is identical to univariate one
    $\widehat{m}(x_0) = \sum_{i=1}^{N} W(x_i, x_0, h) y_i$
    where $x$ is a $k \times 1$ vector, $W(x_i, x_0, h) = K((x_i - x_0)/h) / \sum_i K((x_i - x_0)/h)$ and $K(.)$ is a multivariate kernel
*   Often, the multivariate kernel is just the product of univariate kernels
*   If this is the case, divide by standard deviation so that all variables have similar scale
*   Use cross validation to choose a common bandwidth $h^*$

*   Important: convergence rates decreases (**curse of dimensionality**)
*   Before: $\sqrt{Nh}$,
*   Now: $\sqrt{Nh^k}$, where k is the number of covariates

### Takeaways

*   So far: Kernel-based methods to visualize/estimate conditional expectation in a flexible way
*   Methods based on local averages of the dependent variable
*   Several methods: local Kernel, local polynomial, Lowess, nearest neighbor . . .
*   Methods differ in bandwidth used, weights used, etc.
*   Not huge differences, but Lowess and local polynomial behave better at end points.
*   These methods can handle multivariate regression, but rates of convergence decrease, so performance deteriorates as the number of regressors increases.

## Semiparametric Methods

### 1. Introduction

*   Previous slides: regression models without any structure.
*   This gives a lot of flexibility but it also has some limitations :
    *   Sometimes, theory may place some structure on the data. We might want to incorporate this information in the model
    *   We can only include in the analysis a relative small set of variables (curse of dimensionality)
    *   ...but incorporating many variables might be needed to avoid endogeneity of regressors
*   This lecture: Semiparametric methods

### Semiparametric models: examples (from Cameron&Trivedi)

*   Many semiparametric models and many methods to estimate them. This is only a short intro to these methods.

**Table 9.2. Semiparametric Models: Leading Examples**

| Name | Model | Parametric | Nonparametric |
| :------------------ | :-------------------------------- | :--------- | :------------ |
| Partially linear | $E[y|\mathbf{x}, \mathbf{z}] = \mathbf{x}'\beta + \lambda(\mathbf{z})$ | $\beta$ | $\lambda(\cdot)$ |
| Single index | $E[y|\mathbf{x}] = g(\mathbf{x}'\beta)$ | $\beta$ | $g(\cdot)$ |
| Generalized partial linear | $E[y|\mathbf{x}, \mathbf{z}] = g(\mathbf{x}'\beta + \lambda(\mathbf{z}))$ | $\beta$ | $g(\cdot), \lambda(\cdot)$ |
| Generalized additive | $E[y|\mathbf{x}] = c + \sum_{j=1}^{M} g_j(\mathbf{x}_j)$ | $-$ | $g_j(\cdot)$ |
| Partial additive | $E[y|\mathbf{x}, \mathbf{z}] = \mathbf{x}'\beta + c + \sum_{j=1}^{M} g_j(\mathbf{z}_j)$ | $\beta$ | $g_j(\cdot)$ |
| Projection pursuit | $E[y|\mathbf{x}] = \sum_{j=1}^{M} g_j(\mathbf{x}'_j\beta_j)$ | $\beta_j$ | $g_j(\cdot)$ |
| Heteroskedastic linear | $E[y|\mathbf{x}] = \mathbf{x}'\beta$; $V[y|\mathbf{x}] = \sigma^2(\mathbf{x})$ | $\beta$ | $\sigma^2(\cdot)$ |

are identified. For example, see the discussion of single-index models. In addition to estimation of $\beta$, interest also lies in the marginal effects such as $\partial E[y|\mathbf{x}, \mathbf{z}]/\partial \mathbf{x}$.

### Roadmap

1.  Partially Linear Models
2.  Single Index Models
3.  Summary, other models exist . . .

## Partially Linear Model

*   **Partially Linear Model:** conditional mean is a linear regression function plus an unspecified nonlinear component.
    $E[y|x, z] = x\beta + \lambda(z) \quad \lambda(.) \text{ unspecified.}$
*   **Model** to be estimated:
    $y = x\beta + \lambda(z) + u, \quad E(u|x, z) = 0.$ (8)
*   **Estimation Method: Robinson Difference Estimator**
*   We will obtain estimates for $\beta$ and for $\lambda$ in two steps
    *   **Step 1:** get rid of $\lambda(z)$ and estimate $\beta$ (only)
    *   **Step 2:** Use the estimates of $\beta$ in a model that will allow us to obtain an estimate for $\lambda(.)$

### Robinson Difference Estimator

*   **Step 1:** A) get rid of $\lambda(.)$, B) estimate $\beta$
*   A) get rid of lambda:
    *   Take conditional expectations (conditioning by z) on both sides of Model 8 (and notice that $E(u|z) = 0$):
        $E[y|z] = E[x|z]'\beta + \lambda(z)$ (9)
    *   Subtract the two equations –eq (8) and eq (9)– and obtain the model:
        $y - E[y|z] = (x - E[x|z])'\beta + u$ (10)
    *   The conditional moments $E[y|z]$ and $E[x|z]$ are unknown $\Rightarrow$ Replace them by non parametric estimators $\widehat{m}_{y_i}$ and $\widehat{m}_{x_i}$

*   **Robinson’s difference estimator:**
*   **Step 1:** B) estimate $\beta$ in the model
    $y - \widehat{m}_{y_i} = (x - \widehat{m}_{x_i})'\beta + u$ (11)
*   The resulting estimator of $\beta$ is consistent and A.N (assuming u is i.i.d.):
    $\sqrt{N}(\widehat{\beta}_{PL} - \beta) \overset{d}{\rightarrow} N\left(0, \sigma^2 \left(\operatorname{plim} \frac{1}{N} \sum_{i=1}^{N} (x_i - E[x_i|z_i])(x_i - E(x_i|z_i))' \right)^{-1}\right)$

*   Notes:
    1.  Cost of non-specifying $\lambda$? higher variance (efficiency loss) [But no loss if $E(x|z)$ is linear!]
    2.  The distribution assumes homokedasticity (u is i.i.d). Use Eicker-White standard errors to make it robust to heteroskedasticity
    3.  To estimate the variance: replace $(x_i - E[x_i|z_i])$ by $(x_i - \widehat{m}_{x_i})$
    4.  How to compute $\widehat{m}_{x_i}$ and $\widehat{m}_{y_i}$?
        *   Robinson: Kernel estimates with convergence no slower than $N^{-1/4}$.

*   **Step 2:** Estimate $\lambda$
*   Recall that $\lambda(z) = E(y|z) - E(x|z)'\beta$.
*   Estimate $\lambda(z)$ as
    $\widehat{\lambda}(z) = \widehat{m}_{y_i} - \widehat{m}'_{x_i} \beta$

### Summarizing: Robinson difference estimator

*   **Model:** $E[y_i|x_i, z_i] = x_i'\beta + \lambda(z_i)$, unspeficied $\lambda(\cdot)$
*   **Steps:**
    1.  Kernel regress y on z and get residual $y - \widehat{y}$.
    2.  Kernel regress x on z and get residual $x - \widehat{x}$.
    3.  OLS regress $y - \widehat{y}$ on $x - \widehat{x}$, get $\widehat{\beta}$
    4.  Combine the estimates in 1) 2) and 3) to get $\widehat{\lambda}(z) = \widehat{m}_{y_i} - \widehat{m}'_{x_i} \widehat{\beta}$

### An example

*   Same data as before: now, wage on marital status and education.
*   Let’s look first at the OLS:

```
regress lnhwage educatn married, vce(robust) noheader

------------------------------------------------------------------
lnhwage | Coefficient Std. err. t P>|t| [95% conf. interval]
--------+---------------------------------------------------------
educatn | .1009005 .0219979 4.59 0.000 .0574834 .1443176
married | .4198385 .1545864 2.72 0.007 .1147326 .7249443
_cons | .6149712 .3219871 1.91 0.058 -.0205319 1.250474
------------------------------------------------------------------
```

*   **Robison’s estimator:**
*   **STATA command: `semipar`**
*   married enters linearly, we allow education to enter nonparametrically
    `semipar lnhwage married, nonpar(educatn) robust ci title("Partial linear")`
*   with robust standard errors, confidence intervals...
*   Output has two parts: 1) the parametric component

```
. semipar lnhwage married, nonpar(educatn) robust ci title("Partial linear")

                                         Number of obs =     177
                                         R-squared     =  0.0442
                                         Adj R-squared =  0.0388
                                         Root MSE      =  0.7134

------------------------------------------------------------------
lnhwage | Coefficient Std. err. t P>|t| [95% conf. interval]
--------+---------------------------------------------------------
married | .357994   .146041   2.45  0.015   .069777   .646211
------------------------------------------------------------------
```

*   . . . and the non-parametric component: Plot of $\lambda(z)$ against z where z is education

(Image of a scatterplot showing estimated values for a non-parametric component (Y-axis) against education (X-axis). The blue line represents the estimated $\lambda(z)$ with a shaded grey area for confidence intervals. The curve shows a non-linear relationship for education's effect on log wage, increasing and then possibly flattening or slightly decreasing.)

### Trimming

*   **Trimming:** we can apply trimming to estimate the non-parametric component.
*   In kernel estimation: estimates are not good in areas of the support of z with low density values
*   Why? low density=few values to compute the local average
*   Trimming consists of excluding data points for which the density $f(z) < b$, for some positive value b
*   the command `semipar` allows for the introduction of trimming (default is no trimming)

### Summarizing

*   Partially linear model: additive model with a parametric part and a non-parametric one
*   Estimation: Robinson’s two step estimator
*   Stata: `semipar`
*   Advantages of this method:
    *   The model allows ”any” form of the unknown
    *   $\widehat{\beta}$ is $\sqrt{n}$-consistent

### Summarizing II

*   These methods have been recently revisited in the machine learning literature (really cutting edge at the moment)
*   **Double machine learning**, see Chernozhukov et al (AER, 2017)
*   In a nutshell: same idea but estimate the conditional expectations needed in the procedure above using machine learning, instead of kernel regression
*   Several advantages, in particular, avoid the curse of dimensionality
*   If interested, check out [this link](https://mixtape.csss.washington.edu/double_ml.html) for an easy introduction to the topic.

### Back to binned scatter plots

*   Recall that binned scatter plots are very popular and very useful visualization tools of the conditional expectation
*   STATA Binscatter command (see handout 3), very popular but...
*   problematic as well (for instance, controlling for additional variables).
*   A recent paper improves considerably on this: On binscatter, Cataneo et al. (2024). STATA: `binsreg`

*   Main features of the new binned scatter plots:
*   Framework: partially linear model.
    $y_i = \mu(x_i) + w'_i\gamma + e_i$
*   we’re interested on the shape of the relationship between x and y, controlling for additional variables w.
    *   it provides ways of controlling (correctly!) for additional variables
    *   Optimal choice of the number of bins (i.e., number of quantiles of x plotted)
    *   Uncertainty quantification: confidence bands on the binscatter!
*   Bottom line: in your applications, use the **new `binsreg` command!**

## Single Index Models

*   **Model:**
    $E[y_i|x_i] = g(x_i'\beta)$
    where $g(\cdot)$ is not specified.
*   Many standard nonlinear (parametric) models such as logit, probit, and Tobit are of single-index form. (In these cases $g(.)$ is known)
*   But we can also estimate this model leaving $g(.)$ unspecified and estimate it non-parametrically.

*   Advantages:
*   Advantage 1: generalizes the linear regression model (which assumes $g(.)$ is the identity function)
*   Advantage 2: the curse of dimensionality is avoided as there is only one nonparametric dimension

### Interpretation: marginal effects

*   For single-index models the effect on the conditional mean of a change in the jth regressor using calculus methods is
    $\frac{\partial E[y|x]}{\partial x_j} = g'(x'\beta)\beta_j,$ (12)
    where $g'(z) = \frac{\partial g(z)}{\partial z}.$
*   Then, relative effects of changes in regressors are given by the ratio of the coefficients since
    $\frac{\partial E[y|x]/\partial x_j}{\partial E[y|x]/\partial x_k} = \frac{\beta_j}{\beta_k},$ (13)
    because the common factor $g'(x'\beta)$ cancels.
*   Thus, if $\beta_j$ is two times $\beta_k$, then a one-unit change in $x_j$ has twice the effect as a one-unit change in $x_k$.

### Estimation with unspecified g(.)

*   Identification: $\beta$ can only be identified up to location and scale
*   That is, we estimate $a + b\beta_i$
*   This is still useful to compute relative marginal effects!
*   **Ichimura semiparametric least squares:** choose $\beta$ and $g(\cdot)$ that minimizes
    $\operatorname{argmin}_{\beta} \sum_{i=1}^{N} w(x_i) f(y_i - \hat{g}(x_i'\beta))^2,$
    where $\hat{g}(.)$ is the leave-one-out NW estimator and $w(\cdot)$ is a trimming function that drops outlying x values.
*   $\beta$ can only be estimated up to scale in this model,
*   but still useful as ratio of coefficients equals ratio of marginal effects in a single-index models.

### Example

*   Same data as before. Now: wages on hours worked and education
*   **STATA command: `sls`** (Semiparametric Least Squares from Ichimura, 1993)
*   Install it first:
    `ssc install sls`
*   Run both ols and sls and compare results

```
. sls lnhwage hours educatn, trim(1.99)
initial:  SSq(b) =  120.10723
alternative:  SSq(b) =  120.1062
rescale:  SSq(b) =  98.292016
SLS 0:  SSq(b) =  98.292016
SLS 1:  SSq(b) =  98.195246
SLS 2:  SSq(b) =  98.007811
SLS 3:  SSq(b) =  98.007526
SLS 4:  SSq(b) =  98.007526
pilot bandwidth
1052.001873
SLS 0:  SSq(b) =  99.252078  (not concave)
SLS 1:  SSq(b) =  97.285143
SLS 2:  SSq(b) =  97.202952
SLS 3:  SSq(b) =  97.201992
SLS 4:  SSq(b) =  97.201988

------------------------------------------------------------------
lnhwage | Coefficient Std. err. t P>|t| [95% conf. interval]
--------+---------------------------------------------------------
hours   | .0001365 .0000839 1.63 0.106 -.0000292 .0003022
educatn | .1071543 .0206339 5.19 0.000 .0664293 .1478793
_cons   | .6437424 .3068995 2.10 0.037 .0380175 1.249467
------------------------------------------------------------------

                                         Number of obs =     177
                                         root MSE      =  0.741056

------------------------------------------------------------------
lnhwage | Coefficient Std. err. z P>|z| [95% conf. interval]
--------+---------------------------------------------------------
Index   |
educatn | 1048.102  275.9987  3.80  0.000   507.1545  1589.05
hours   | 1 (offset)
------------------------------------------------------------------
```

*   Interpretation:
*   one more year of education has the same effect on log hourly wage as working 1048 more hours a year!
*   Compared to OLS, 0.1071453/0.0001365 = 785.

*   This graph plots the predicted conditional expectation versus $x'\beta$ (highly nonlinear)

(Image of a plot showing "y and yhat" (Y-axis) against "Index" (X-axis). The curve, formed by connected blue dots, represents the predicted conditional expectation, which is highly non-linear, showing significant fluctuations. The "Index" on the X-axis is a linear combination of covariates, $x'\beta$.)

### Index models, summary

*   Generalizes the linear regression model (which assumes $g(.)$ constant)
*   Gain over parametric models: more flexible
*   Gain over fully non-parametric: only one nonparametric dimension (avoid curse of dimensionality).
*   We only identified the parameters up to location and scale but
*   The ratio of the coefficients provides the relative marginal effects

### Takeaways

*   Semiparametric methods aim to overcome some of the limitations of fully parametric and fully nonparametric methods
*   Flexible, yet tractable (many variables can be included)
*   Literature is very large
*   Here, we’ve only presented a short introduction.
*   See [this document](https://www.stata.com/manuals/npsemipar.pdf) to learn about other semiparametric methods STATA can handle