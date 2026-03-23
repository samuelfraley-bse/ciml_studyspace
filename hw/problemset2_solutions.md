---
cost: 0.00685
course: General
difficulty: 8
est_minutes: 180
summary: This document provides detailed solutions to a problem set covering advanced
  econometrics topics including fixed effects estimation in linear and nonlinear models,
  kernel density estimation, binned scatter plots, Nadaraya-Watson local constant
  regression, local linear regression, and Robinson's partially linear model estimator.
tags:
- Fixed Effects Models
- Kernel Density Estimation
- Nonparametric Regression
- Bias-Variance Trade-off
- Partially Linear Models
type: Assignment
---

# Problem Set 2 Solutions
Causal Inference and Machine Learning

Instructor: Laura Mayoral
Teaching Assistant: Lucia Sauer

Winter 2026

## Problem 1
### Part (a)

**Question**
Describe how you could estimate a FE model and discuss whether you would obtain consistent estimators and what would be the disadvantages of following this approach.

**Solution**
**Linear probability model:**
$Y_{it} = X_{it}\beta + A_i + \epsilon_{it}$

where:
*   $Y_{it}$ is a binary variable indicating whether individual $i$ is unemployed at time $t$,
*   $X_{it}$ represents time-varying regressors (age, education, etc.),
*   $a_i$ captures individual fixed effects, accounting for unobserved heterogeneity that is time-invariant (e.g., intrinsic ability, long-term preferences for work, grounds on which they might be discriminated against),
*   $\epsilon_{it}$ is the error term.

**Estimation options:**
*   **Within transformation:** Subtract individual-specific means for each variable from both sides.
*   **First-differences:** subtract previous period's values for each individual
*   **Dummy-variables:** one dummy for each individual (minus 1)

**Consistency of estimators:** estimates of $\beta$ should be consistent: FE estimation eliminates omitted variable bias caused by time-invariant unobserved heterogeneity ($a_i$), leading to consistent $\beta$ assuming there are no other confounders. But LPM assume homoskedastic errors, but this is not the case for a binary outcome, so need to specify robust or cluster-robust errors when specifying estimation model parameters, and estimations of $Y$ may also not fall within the [0, 1] range.

### Part (b)

**Question**
Explain why considering a nonlinear model can be useful in this case. Assuming you want to estimate a model with FE, explain the incidental parameter problem.

**Solution**
**Why nonlinear models are useful:**
*   Linear Probability Model (LPM) can predict probabilities outside [0, 1] which is unrealistic, while nonlinear models like logit and probit apply a sigmoid transformation, ensuring predicted probabilities remain within valid bounds.
*   LPM assumes a constant marginal effect of regressors, which is often incorrect (e.g., the effect of an additional year of education on unemployment might not be the same at different levels of education). A logit/probit model allows for diminishing marginal effects, which better reflects reality.

**Incidental parameter problem:**
*   If we estimate a logit or probit model with MLE, we must estimate both $\beta$ (common parameters) and $a_i$ (individual-specific parameters).
*   **Problem:** We cannot get rid of the $a_i$'s (which we could in the linear case with de-meaning) as there is no straightforward equivalent transformation in the non-linear case. Therefore the number of parameters grows to infinity with $N$. This is what creates the inconsistency if $T$ (in our case 5) is considered to be fixed: the information grows as $N$ tends to infinity, but so does the number of parameters.

### Part (c)

**Question**
Explain what a sufficient statistic is and how it can help overcome the incidental parameter problem. What type of model could you estimate following this approach?

**Solution**
**Sufficient statistic:** A statistic $S(Y)$ is sufficient for a parameter $\theta$ if the conditional distribution of $Y$ given $S(Y)$ does not depend on $\theta$. Intuitively, $S(Y)$ summarizes all information in the data about $\theta$—once we know $S(Y)$, the remaining data is uninformative.

**Overcoming the incidental parameter problem:** In fixed-effects nonlinear models, if a sufficient statistic $S_i$ exists for $a_i$, we can condition on it to obtain a likelihood that no longer depends on the fixed effects. Maximizing this *conditional likelihood* yields consistent estimates of $\beta$ without estimating the $a_i$'s.

**Application:** This approach works for **fixed-effects logit models** (also called conditional logit), where:
$P(Y_{it} = 1 | X_{it}, a_i) = \frac{\exp(X_{it}\beta + a_i)}{1 + \exp(X_{it}\beta + \alpha_i)}$ (1)
The sufficient statistic for $a_i$ is $S_i = \sum_{t=1}^T Y_{it}$ (the individual's total number of successes). By conditioning on $S_i$, the $a_i$'s drop out of the likelihood, solving the incidental parameter problem.
**Note:** This approach requires binary outcomes and does *not* work for probit models (no sufficient statistic exists).

### Part (d)

**Question**
Use the KEANE dataset (Wooldridge) and construct a model to explain the probability of being employed (employ). Discuss whether the inclusion of individual effects can help to avoid the OVB. Employ a nonlinear model with individual effects to compute the estimates, justify your choice and provide an interpretation of the coefficients.

**Solution**
**The model employed:**
$employ_{it} = \beta_1 educ_{it} + \beta_2 expwc_{it} + \beta_3 expbc_{it} + \beta_4 exp_ser_{it} + \beta_5 expersq_{it} + \beta_6 black_i + a_i + \epsilon_{it}$
where $employ_{it}$ is a binary indicator (1 = employed), and $a_i$ represents individual fixed effects (omitted in pooled models). Several variables are excluded to avoid perfect collinearity or endogeneity. `wage` and `lwage` are mechanically zero when unemployed, making them perfectly correlated with the outcome. Similarly, `wrkmfg` (working in manufacturing), `choice`, `enroll`, and `status` all mechanically determine employment status. `exper` is excluded as it is perfectly collinear with the sum of experience variables (`expwc`, `expbc`, `expser`). Finally, `attrit` and `numyrs` are data quality indicators rather than substantive regressors.

**Linear probability model with fixed effects:**
```stata
xtreg employ educ expwc expbc expser i.black expersq, fe vce(cluster id)
note: 1.black omitted because of collinearity.
```
**Fixed-effects (within) regression**
Group variable: id                         Number of obs = 12,723
                                           Number of groups =  1,881
R-squared:                                 Obs per group:
      Within = 0.1840                            min =       1
      Between = 0.0032                           avg =     6.8
      Overall = 0.0327                           max =       7

corr(u_i, Xb) = -0.6179                    F(5, 1880)  =  327.84
                                           Prob > F    =  0.0000
----------------------------------------------------------------
         employ | Robust
        | Coefficient  std. err.   t    P>|t|  [95% conf. interval]
----------------------------------------------------------------
           educ |  .1694137   .0058749  28.84  0.000   .1578917   .1809358
          expwc |  .1447253   .0079737  18.15  0.000   .1290871   .1603635
          expbc |  .1419629   .0072293  19.64  0.000   .1277847   .1561411
         expser |  .1061742   .010495   10.12  0.000   .0855913   .1267572
        1.black |         0  (omitted)
        expersq | -.0175894   .000956  -18.40  0.000  -.0194643  -.0157145
          _cons | -1.609391   .0700492  -22.98  0.000  -1.746773  -1.472009
----------------------------------------------------------------
        sigma_u | .4393007
        sigma_e | .3762185
            rho | .57689212 (fraction of variance due to u_i)
----------------------------------------------------------------
```
*   The FE specification removes time-invariant unobservables (e.g., ability, discrimination), eliminating a major source of OVB. Note that `black` is omitted due to de-meaning.
*   `Education` has a positive effect on employment probability, suggesting that additional schooling improves labor market outcomes.
*   The `experience` variables capture sector-specific human capital. If coefficients are positive, they indicate that work experience increases employability.
*   **Limitation:** Predicted probabilities may fall outside [0, 1], and the model assumes constant marginal effects.

## Problem 5
**Conditional fixed-effects logit model:**
```stata
xtlogit employ educ expwc expbc expser i.black expersq, fe nolog
xtlogit, or
```
**Conditional fixed-effects logistic regression**
Group variable: id                         Number of obs = 9,672
                                           Number of groups = 1,408
                                           Obs per group:
                                                 min =     2
                                                 avg =   6.9
                                                 max =     7
Log likelihood = -2830.1776                LR chi2(5)  = 2324.43
                                           Prob > chi2 =  0.0000
----------------------------------------------------------------
         employ | Odds Ratio  Std. Err.   z    P>|z|  [95% conf. interval]
----------------------------------------------------------------
           educ |   9.216824   .8090868  25.30  0.000   7.759964  10.9472
          expwc |   1.533308   .0967118   6.78  0.000   1.355004  1.735074
          expbc |   1.865924   .1005152  11.58  0.000   1.678961  2.073706
         expser |   1.431063   .1001786   5.12  0.000   1.24759   1.641517
        1.black |          1  (omitted)
        expersq |   .9151197   .0081995  -9.90  0.000   .8991892   .9313323
----------------------------------------------------------------
```
*   **Education (OR = 9.22):** Each additional year of education multiplies the odds of being employed by 9.22, or equivalently, increases the odds by 822%. This large effect suggests education is a powerful determinant of employment, controlling for individual fixed effects and experience.
*   **Experience variables:** Work experience in each sector (white-collar, blue-collar, service) increases employment odds (ORs between 1.43–1.87), with blue-collar experience showing the strongest effect. The negative coefficient on `expersq` (OR = 0.92 < 1) suggests diminishing returns to experience.
*   **Advantages over LPM:** Predicted probabilities are bounded in [0, 1], and the model allows for non-constant marginal effects (education may matter more at low baseline probabilities).
*   The conditional likelihood eliminates $a_i$ without estimating $N$ parameters, solving the incidental parameter problem.

## Problem 2
### Part (1)

**Question**
Write down the formula for the histogram estimator $f_{HIST}(x_0)$ centered at $x_0$ with bin width $2h$. Explain the role of $h$ in this expression.

**Solution**
$f_{HIST}(x_0) = \frac{1}{2Nh} \sum_{i=1}^N \mathbf{1}(x_0 - h < x_i < x_0 + h)$
which can equivalently be written as:
$f_{HIST}(x_0) = \frac{1}{N} \sum_{i=1}^N \frac{1}{2h} \mathbf{1}\left(\left|\frac{x_i - x_0}{h}\right| < 1\right)$

**Interpretation of the formula:**
*   The indicator function $\mathbf{1}(\cdot)$ counts the number of observations falling within the interval $(x_0 - h, x_0 + h)$.
*   Dividing by $N$ converts the count into a relative frequency.
*   Dividing by $2h$ (the bin width) ensures the estimator integrates to 1, making it a proper density.

**Role of $h$:** The parameter $h$ is the half-width of each bin (so the full bin width is $2h$). It controls the degree of smoothing:
*   **Large $h$**: wide bins, each containing many observations $\Rightarrow$ smooth but potentially biased estimate (fine details of the density are lost).
*   **Small $h$**: narrow bins, each containing few observations $\Rightarrow$ noisy but potentially less biased estimate (captures local features but with high variance).

### Part (2)

**Question**
Explain intuitively what happens to the histogram estimate as (i) $h \rightarrow 0$ for fixed $N$, (ii) $h \rightarrow \infty$ for fixed $N$, and (iii) $N \rightarrow \infty$ for fixed $h$.

**Solution**
**(i):** As the bin width shrinks to zero, each bin will contain at most one observation. The histogram becomes a collection of isolated spikes at each data point (and zero everywhere else). This is maximally noisy—the estimate is only informative about the location of individual sample points, not about the underlying density. The variance is enormous, while the bias is small.
**(ii):** As the bin width grows without bound, eventually all $N$ observations fall into a single bin. The histogram becomes a flat rectangle spread over the entire range of the data—a completely uninformative estimate. The variance is zero (only one bin), but the bias is enormous because all local structure in the density is erased.
**(iii):** As the sample size grows, the relative frequency in each bin converges (by the law of large numbers) to the true probability of falling in that bin. So the histogram converges to a step-function approximation of the true density $f$, where each step has height $Pr(x_0-h < X < x_0+h)/(2h) \approx f(x_0)$. However, because $h$ is fixed, the histogram remains a step function and never becomes smooth—there is a residual approximation bias that does not vanish. For the histogram to be consistent, we need both $N \rightarrow \infty$ and $h \rightarrow 0$ (but slowly enough that $Nh \rightarrow \infty$).

### Part (3)

**Question**
Using the `nlsw88` dataset, construct histograms of the variable `wage` using 10, 20, and 50 bins. Comment on how the shape of the estimated density changes. Which number of bins seems most informative and why?

**Solution**
The plots for 10, 20, and 50 bins for `Hourly wage` show:
*   **10 bins:** Very smooth but overly coarse. The right skew is visible, but important details are lost (low variance, high bias).
*   **20 bins:** Good balance. The skewness and main concentration are clear without excessive noise.
*   **50 bins:** Too noisy and spiky. Many bins have few observations, so much of the variation reflects sampling noise rather than real structure.

**Conclusion:** Around 20 bins is most informative for this dataset ($N \approx 2,246$). Stata's default rule ($\sqrt{N} \approx 47$) suggests nearly 50 bins, which appears excessive here. This illustrates the bias-variance trade-off: fewer bins oversmooth, while too many amplify noise.

## Problem 3
### Part (1)

**Question**
The kernel density estimator is given by
$\hat{f}(x_0) = \frac{1}{Nh} \sum_{i=1}^N K\left(\frac{x_i - x_0}{h}\right)$
Explain the role of $K(\cdot)$ and $h$. How does this estimator relate to the histogram?

**Solution**
**Role of $K(\cdot)$:** The kernel function $K(\cdot)$ is a weighting function that determines how much each observation $x_i$ contributes to the density estimate at the point $x_0$. It assigns weights based on the distance between $x_i$ and $x_0$: observations close to $x_0$ receive high weight, and those far away receive low (or zero) weight. The kernel must be a symmetric, unimodal density function that integrates to 1.

**Role of $h$:** The bandwidth $h$ is a smoothing parameter that controls the width of the kernel—i.e., how far from $x_0$ the estimator looks when computing the density. It plays an analogous role to the bin width in a histogram:
*   **Small $h$**: the kernel is narrow, so only observations very close to $x_0$ contribute $\Rightarrow$ less bias, more variance.
*   **Large $h$**: the kernel is wide, so many observations contribute $\Rightarrow$ more bias, less variance.

**Relation to the histogram:** The histogram is a special case of the kernel density estimator where the kernel is the uniform (rectangular) density:
$K(z) = \frac{1}{2} \mathbf{1}(|z| < 1)$
With this kernel, the kernel density estimator becomes exactly the histogram formula. The key difference is that the histogram evaluates this at fixed, equally-spaced bin centers, whereas the kernel density estimator evaluates it at every point $x_0$, creating a "running" or "sliding" histogram. Moreover, by using a smooth kernel (e.g., Gaussian, Epanechnikov), the resulting estimate inherits the smoothness of $K$, overcoming the step-function limitation of histograms.

### Part (2)

**Question**
State at least three properties that a kernel function $K(\cdot)$ must satisfy. Give two examples of commonly used kernels and write down their functional forms.

**Solution**
A kernel function $K(\cdot)$ must satisfy:
1.  **Symmetry:** $K(z) = K(-z)$ for all $z$. The kernel is symmetric around zero.
2.  **Integration to unity:** $\int_{-\infty}^{\infty} K(z) dz = 1$. This ensures $\hat{f}$ is a proper density.
3.  **Zero first moment:** $\int_{-\infty}^{\infty} zK(z) dz = 0$. This follows from symmetry and ensures that the estimator does not systematically shift the density.
4.  **Finite second moment:** $\int_{-\infty}^{\infty} z^2K(z) dz = \kappa < \infty$. This constant $\kappa$ appears in the bias formula.
5.  **Boundedness/tail behavior:** Either $K(z) = 0$ for $|z| \ge z_0$ (compact support), or $K(z) \rightarrow 0$ as $|z| \rightarrow \infty$.

**Two commonly used kernels:**
*   **Epanechnikov kernel:**
    $K(z) = \frac{3}{4}(1 - z^2)\cdot \mathbf{1}(|z| < 1)$ (2)
    This is the optimal kernel in the sense of minimizing MISE. It has compact support (observations outside $|z| < 1$ receive zero weight).
*   **Gaussian kernel:**
    $K(z) = \frac{1}{\sqrt{2\pi}} \exp\left(-\frac{z^2}{2}\right)$ (3)
    This is the standard normal density. It does not have compact support (all observations receive some weight), but the weights decay exponentially with distance.

### Part (3)

**Question**
Explain why the kernel density estimator can be seen as a "smoothed histogram." In particular, explain why using the uniform kernel $K(z) = \frac{1}{2}\mathbf{1}(|z| < 1)$ produces a "running histogram" rather than a standard histogram.

**Solution**
A standard histogram divides the real line into fixed, non-overlapping bins and counts the fraction of observations in each bin. All evaluation points within the same bin share the same density estimate, which creates the characteristic step function shape.
The kernel density estimator with a uniform kernel uses the same counting-and-averaging idea, but with one crucial difference: **the bin is centered at the evaluation point $x_0$ and moves as $x_0$ changes.** For each new evaluation point, we count the observations within $(x_0 - h, x_0 + h)$ and compute their relative frequency. As $x_0$ slides continuously across the support, the count changes smoothly (one observation enters or leaves at a time), producing a continuous (though not smooth) function rather than a step function. When we replace the uniform kernel with a smooth kernel (e.g., Gaussian or Epanechnikov), we additionally assign non-constant weights to the observations within the window: observations closer to $x_0$ receive higher weight than those near the boundary. This produces an estimate that inherits the smoothness of the kernel function itself. Hence, the kernel density estimator is literally a smoothed version of the running histogram.

### Part (4)

**Question**
Consider the Gaussian kernel, $K(z) = (2\pi)^{-1/2} \exp(-z^2/2)$, and the Epanechnikov kernel, $K(z) = \frac{3}{4}(1 - z^2)\mathbf{1}(|z| < 1)$. Which observations receive nonzero weight in each case? What are the practical implications of this difference?

**Solution**
**Epanechnikov kernel:** Only observations within a distance $h$ from the evaluation point $x_0$ receive nonzero weight. Formally, $K\left(\frac{x_i - x_0}{h}\right) > 0$ if and only if $|x_i - x_0| < h$. Observations outside this window receive exactly zero weight. This means the estimate at $x_0$ is determined entirely by observations in the interval $(x_0 - h, x_0 + h)$.

**Gaussian kernel:** All observations receive nonzero weight, regardless of their distance from $x_0$. This is because the Gaussian density is positive everywhere: $\exp(-z^2/2) > 0$ for all $z$. However, the weights decay exponentially with distance, so observations far from $x_0$ receive negligible (but technically nonzero) weight.

**Practical implications:**
*   **Computational cost:** The Epanechnikov kernel is cheaper to compute because only a subset of observations contribute at each evaluation point. With the Gaussian kernel, all $N$ observations must be included (though in practice, observations beyond $3h$-$4h$ contribute negligibly and can be truncated).
*   **Smoothness of the estimate:** The Gaussian kernel produces an infinitely differentiable density estimate (since the Gaussian itself is infinitely differentiable). The Epanechnikov kernel produces an estimate that is differentiable but only up to a limited order (since the kernel itself has kinks at $z = \pm 1$).
*   **Practical importance:** Despite these differences, after choosing the optimal bandwidth, the MISE varies very little across kernels. The choice of $h$ matters far more than the choice of $K$.

## Problem 4
### Part (1)

**Question**
Define the Mean Squared Error (MSE) and the Mean Integrated Squared Error (MISE) of a kernel density estimator. Explain why MISE is a more useful criterion than MSE for evaluating the global performance of a density estimator.

**Solution**
**Mean Squared Error (MSE):** The MSE of the kernel density estimator at a specific point $x_0$ is:
$MSE[\hat{f}(x_0)] = E [(\hat{f}(x_0) - f(x_0))^2] = \underbrace{Var[\hat{f}(x_0)]}_{\text{variance}} + \underbrace{Bias[\hat{f}(x_0)]^2}_{\text{squared bias}}$ (4)
This measures how far the estimator is from the truth at a single point $x_0$, accounting for both the variability of the estimator (variance) and its systematic deviation from the truth (bias).

**Mean Integrated Squared Error (MISE):** The MISE is the integral of the MSE over all values of $x$:
$MISE(h) = \int MSE[\hat{f}(x_0)]dx_0 = \int E [(\hat{f}(x_0) - f(x_0))^2] dx_0$ (5)

**Why MISE is more useful:** The MSE is a local criterion—it tells us how well the estimator performs at a single point. But we typically want to estimate the entire density function, not just its value at one point. The MISE is a global criterion that aggregates performance across the whole support. When we choose the bandwidth $h$ to minimize MISE, we obtain a single value of $h$ that provides the best overall trade-off between bias and variance across all points simultaneously. This is what makes MISE the standard criterion for bandwidth selection.

### Part (2)

**Question**
The optimal bandwidth satisfies $h^* = O(N^{-1/5})$. Explain intuitively why the optimal bandwidth shrinks as $N$ grows.

**Solution**
As the sample size $N$ grows, we accumulate more and more data points. This means that even in a small neighborhood around any point $x_0$, there will be many observations—the data becomes denser everywhere.
With more observations available locally, we can afford to use a narrower window (smaller $h$) without running into high variance problems. A smaller $h$ reduces bias (because we are averaging over a narrower range where $f$ is approximately constant), and the large $N$ compensates by keeping the variance under control (because even a narrow window still contains many observations).
Formally, the optimal bandwidth balances:
*   Squared bias $\approx C_1 \cdot h^4$ (decreases as $h$ shrinks), and
*   Variance $\approx C_2/(Nh)$ (increases as $h$ shrinks).

Setting the derivative of their sum to zero and solving for $h$ gives $h^* \propto N^{-1/5}$. The exponent $-1/5$ reflects the specific way bias ($\sim h^4$) and variance ($\sim (Nh)^{-1}$) trade off in density estimation.

### Part (3)

**Question**
Using the `nlsw88` data, estimate the kernel density of the variable `wage` using the Epanechnikov kernel with three bandwidths: the Stata default, half the default, and twice the default. Plot all three estimates on the same graph. Comment on the bias-variance trade-off visible in your plot.

**Solution**
The plot shows `Kernel density of wage, three bandwidths` for 'Default', 'Half default', and 'Twice default' bandwidths.
*   **Default bandwidth:** Produces a reasonably smooth estimate. The `wage` distribution appears right-skewed with a single mode.
*   **Half default (smaller $h$):** The estimate is more wiggly and reveals more local features—possibly secondary bumps or sharper peaks. However, some of this additional detail may be noise rather than genuine features of the distribution. This illustrates low bias but high variance.
*   **Twice default (larger $h$):** The estimate is very smooth, almost washing out interesting features. The skewness may appear less pronounced. This illustrates low variance but high bias—the estimate is oversmoothed.

### Part (4)

**Question**
Using the plug-in bandwidth estimator, estimate the density of this variable using three different kernels. Discuss the differences, if any.

**Solution**
The plot shows `Density of wage: Three Kernels (Plug-in bandwidth)` for Epanechnikov, Gaussian, and Uniform kernels.
Using the plug-in bandwidth, the estimated densities are very similar across the Gaussian, Epanechnikov, and Uniform kernels. Differences are minor and mainly visible in the tails and peak smoothness. This confirms that the choice of bandwidth has a much larger impact on the density estimate than the specific kernel function. Kernel choice affects efficiency slightly, but in practice the results are nearly identical.

### Part (5)

**Question**
On the same graph (or a new one), overlay a normal density with the same mean and standard deviation as `wage`. Does the `wage` distribution look normal? What features of the distribution does the kernel density estimator reveal that a parametric normal assumption would miss?

**Solution**
The plot shows `Kernel density estimate` (Epanechnikov, bandwidth = 0.7609) and `Normal density` (with same mean and SD as wage).
The kernel density estimate reveals several features that a parametric normal assumption would miss:
1.  **Right skewness:** The `wage` distribution has a long right tail (some workers earn much more than average), whereas the normal distribution is symmetric.
2.  **Possible multimodality or bumps:** The kernel estimate may show slight bumps or shoulders that a unimodal normal cannot capture.
3.  **Bounded support:** Wages are non-negative, but the normal density assigns positive probability to negative values. The kernel estimate respects the data's support more naturally.
4.  **Heavier right tail:** The kernel estimate likely shows more mass in the upper tail than the normal.

**Conclusion:** The `wage` distribution does not look normal. This is a common finding in labor economics—wage distributions are typically right-skewed, and log wages are often closer to normal. This example illustrates a key use of nonparametric density estimation: checking whether parametric assumptions are reasonable.

## Problem 5
### Part (1)

**Question**
Explain the steps involved in constructing a binned scatter plot. Why are binned scatter plots more informative than standard scatter plots when the sample size is large?

**Solution**
1.  **Bin the x-variable:** Divide the range of the x-variable into $J$ equal-sized bins (quantile bins). For example, with $J = 20$, each bin contains approximately $N/20$ observations.
2.  **Compute bin means:** Within each bin, compute the mean of the x-variable and the mean of the y-variable.
3.  **Plot the means:** Create a scatter plot where each point represents one bin, with coordinates $(\bar{x}_j, \bar{y}_j)$.

**Why more informative for large samples?**
When $N$ is large (thousands or millions of observations), a standard scatter plot becomes a dense cloud of overlapping points, making it nearly impossible to discern any relationship between $x$ and $y$. The binned scatter plot solves this by:
*   **Reducing visual clutter:** Instead of $N$ points, we plot only $J$ (typically 20-50) points.
*   **Averaging out noise:** Within each bin, individual-level noise is averaged away, revealing the underlying conditional mean $E[y|x]$.
*   **Providing a visual nonparametric estimate:** The binned scatter is essentially a simple nonparametric estimator of $E[y|x]$, making the shape of the relationship immediately visible.

### Part (2)

**Question**
The Stata command `binscatter` allows you to control for additional variables using the Frisch-Waugh-Lovell theorem. Explain how this works. Under what assumption on the conditional expectation is this approach valid? What can go wrong if this assumption is violated?

**Solution**
The `binscatter` command uses the Frisch-Waugh-Lovell (FWL) Theorem. The procedure is:
1.  Regress $y$ on the control variables $z$ (by OLS). Save the residuals $\tilde{y}_i = y_i - \hat{y}_i$.
2.  Regress $x$ on the control variables $z$ (by OLS). Save the residuals $\tilde{x}_i = x_i - \hat{x}_i$.
3.  Construct the binned scatter plot of $\tilde{y}$ against $\tilde{x}$.

**Underlying assumption:** This approach is justified by the FWL theorem, which is a result about linear regression. Therefore, the residualization procedure is only valid when the conditional expectation $E[y|x, z]$ is linear in both $x$ and $z$:
$E[y|x, z] = \alpha + \beta x + \gamma'z$ (6)
If the true conditional expectation is nonlinear (e.g., $E[y|x, z] = \beta x + \lambda(z)$ with $\lambda$ nonlinear), the linear residualization does not properly remove the effect of $z$. The resulting binned scatter plot of $y$ against $x$ can be distorted, potentially showing a relationship that is an artifact of the incorrect linear adjustment rather than the true partial relationship.

### Part (3)

**Question**
Using the `nlsw88` dataset:
(a) Create a scatter plot of `wage` against `tenure`. Comment on what you can learn from it.
(b) Create a binned scatter plot of `wage` against `tenure` using 20 quantile bins. Compare it with the scatter plot.
(c) Now create the binned scatter plot controlling for `age`. Does the relationship change? Interpret.
(d) Create the binned scatter plot separately by `race` (or `union status`). What do you observe?

**Solution**
**(a):** The standard scatter plot (`Scatter: wage vs tenure`) shows a dense cloud of points. With $N \approx 2,246$ observations, it is difficult to discern a clear pattern, though there may be a vague positive trend.

**(b):** The binned scatter plot (`Binscatter: wage vs tenure`) is dramatically more informative. We can see a clear positive relationship between tenure and wages: workers with more years on the job earn higher wages. The relationship appears approximately linear over most of the support.

**(c):** After controlling for `age`, the relationship between tenure and wages (`Binscatter: controlling for age`) remains essentially unchanged. Both the slope and overall pattern are very similar to the unconditional binscatter, suggesting that age does not meaningfully confound the tenure-wage relationship in this sample. Tenure appears to have an independent association with wages.

**(d):** The `Binscatter: by union status` plot shows both union and nonunion workers exhibit a positive relationship between tenure and wages. Union workers earn higher wages at each tenure level, indicating a level shift consistent with a union wage premium. However, the nonunion line is slightly steeper, suggesting that wage growth with tenure is somewhat stronger for nonunion workers, even though unions raise wage levels overall.

### Part (4)

**Question**
Cattaneo et al. (2024) propose an improved approach to binned scatter plots. Briefly describe two advantages of their approach over the traditional `binscatter` command.

**Solution**
1.  The traditional `binscatter` controls for covariates by linear residualization (FWL), which is only valid under linearity. The Cattaneo et al. approach is set in the framework of a partially linear model, and provides methods that correctly handle nonlinear relationships. This means the resulting binned scatter plot is a valid visual representation even when the effect of the controls is nonlinear.
2.  The traditional `binscatter` command provides only point estimates (the bin means) with no measure of statistical uncertainty. Cattaneo et al. provide valid confidence bands for the conditional mean function, so you can assess whether apparent patterns are statistically significant or could be due to sampling variability.
3.  The method provides a principled, data-driven way to select the number of bins, rather than relying on an ad hoc choice by the researcher.

## Problem 6
Consider the model $y_i = m(x_i) + \epsilon_i$, where $E[\epsilon|x] = 0$ and $m(\cdot)$ is unknown.

### Part (1)

**Question**
Write down the Nadaraya-Watson (NW) kernel regression estimator $\hat{m}(x_0)$. Explain how it can be interpreted as a local weighted average.

**Solution**
The Nadaraya-Watson (NW) kernel regression estimator of $m(x_0) = E[y|x = x_0]$ is:
$\hat{m}(x_0) = \frac{\sum_{i=1}^N K\left(\frac{x_i - x_0}{h}\right) y_i}{\sum_{i=1}^N K\left(\frac{x_i - x_0}{h}\right)}$
This can be written as a weighted average:
$\hat{m}(x_0) = \sum_{i=1}^N W(x_i, x_0, h)y_i$, where $W(x_i, x_0, h) = \frac{K\left(\frac{x_i - x_0}{h}\right)}{\sum_{j=1}^N K\left(\frac{x_j - x_0}{h}\right)}$

**Interpretation:** The NW estimator is a locally weighted average of the $y_i$'s. The weights $W(x_i, x_0, h)$:
*   Are non-negative and sum to 1 (so this is a proper weighted average).
*   Are larger for observations where $x_i$ is close to $x_0$ and smaller (or zero) for observations far away.
*   Depend on the bandwidth $h$: smaller $h$ concentrates the weights more tightly around $x_0$.

The intuition is simple: to estimate the conditional mean at $x_0$, we average the $y$-values of nearby observations, giving more influence to those whose $x$-values are closest to $x_0$. Note also that the denominator $\sum_j K\left(\frac{x_j - x_0}{h}\right) = Nh\hat{f}(x_0)$ is proportional to the kernel density estimator evaluated at $x_0$.

### Part (2)

**Question**
The NW estimator can also be obtained as the solution to a weighted least squares problem. Write down this optimization problem explicitly. Hint: think of fitting a constant locally.

**Solution**
The NW estimator can be obtained as the solution to the following optimization problem:
$\hat{m}(x_0) = \arg \min_{m_0} \sum_{i=1}^N K\left(\frac{x_i - x_0}{h}\right) (y_i - m_0)^2$
This is a weighted least squares problem where we fit a constant $m_0$ to the data, using kernel weights. Taking the first-order condition:
$\frac{\partial}{\partial m_0} \sum_{i=1}^N K\left(\frac{x_i - x_0}{h}\right) (y_i - m_0)^2 = -2 \sum_{i=1}^N K\left(\frac{x_i - x_0}{h}\right) (y_i - m_0) = 0$
Solving for $m_0$:
$m_0 \sum_{i=1}^N K\left(\frac{x_i - x_0}{h}\right) = \sum_{i=1}^N K\left(\frac{x_i - x_0}{h}\right) y_i$
$\hat{m}(x_0) = m_0 = \frac{\sum_{i=1}^N K\left(\frac{x_i - x_0}{h}\right) y_i}{\sum_{i=1}^N K\left(\frac{x_i - x_0}{h}\right)}$
which is exactly the NW estimator. This is why the NW estimator is also called the **local constant estimator**: it fits a constant (horizontal line) locally around each $x_0$.

### Part (3)

**Question**
Explain the role of the bandwidth $h$ in the NW estimator. What happens when $h$ is very small? Very large? Relate this to the bias-variance trade-off.

**Solution**
The bandwidth $h$ determines the size of the local neighborhood around $x_0$ that is used to compute the weighted average. Its role mirrors the bandwidth in kernel density estimation:
**Very small $h$**: Only observations very close to $x_0$ receive nonnegligible weight. In the extreme, if $h$ is so small that only one observation is within the window, $\hat{m}(x_0) \approx y_i$ for the single nearest observation. The estimate interpolates the data, producing a very wiggly curve that passes near every data point. This means low bias (the estimate closely tracks the true function locally) but high variance (the estimate is driven by individual noisy observations). This is **overfitting**.
**Very large $h$**: All observations receive similar weight, regardless of distance. In the extreme ($h \rightarrow \infty$), the estimate converges to the global sample mean $\bar{y}$, a flat horizontal line. This means high bias (any interesting structure in $m(x)$ is erased) but low variance (averaging over many observations). This is **underfitting**.
**Bias-variance trade-off**: The optimal $h$ balances these two forces. Including observations where $x_i \ne x_0$ introduces bias (because $E[y_i|x_i] = m(x_i) \ne m(x_0)$ in general), but reduces variance (because more data is used). The optimal bandwidth is $h^* = O(N^{-1/5})$, just as in density estimation.

### Part (4)

**Question**
Describe the cross-validation procedure for selecting $h$ in kernel regression. Why is it important to leave observations out when computing the cross-validation criterion? What problem does this prevent?

**Solution**
**Cross-validation (leave-one-out) for bandwidth selection:**
1.  **For each observation $i$:** Compute the NW estimate at $x_i$ using all observations except $i$:
    $\hat{m}_{-i}(x_i; h) = \frac{\sum_{j \ne i} K\left(\frac{x_j - x_i}{h}\right) y_j}{\sum_{j \ne i} K\left(\frac{x_j - x_i}{h}\right)}$ (7)
2.  **Compute the cross-validation criterion:**
    $CV(h) = \sum_{i=1}^N (y_i - \hat{m}_{-i}(x_i; h))^2 \pi(x_i)$ (8)
    where $\pi(x_i)$ are optional weights to downweight observations at the endpoints (where estimates can be unreliable).
3.  **Evaluate over a grid:** Compute $CV(h)$ for a range of values of $h$.
4.  **Select optimal $h$:** Choose $\hat{h}_{CV} = \arg \min_h CV(h)$.

**Why leave one observation out?** Nonparametric methods are extremely flexible. If we evaluated the fit using the same data used to construct the estimator (in-sample fit), then choosing a very small $h$ would give a nearly perfect fit (each $\hat{m}(x_i)$ would be very close to $y_i$), yielding a very small in-sample MSE. But this perfect fit is just overfitting: the estimator is fitting the noise in the data, not just the signal. Out-of-sample, such an estimator would perform very poorly.
By leaving observation $i$ out when predicting $y_i$, cross-validation simulates out-of-sample prediction. A bandwidth that is too small will overfit the training data but predict poorly for the held-out observation. This prevents overfitting and selects an $h$ that balances fit and generalizability.

### Part (5)

**Question**
Using the `nlsw88` data, estimate the NW regression of `wage` on `tenure` using Stata's `lpoly` command (with `degree(0)`). Plot the estimate with confidence intervals. Experiment with at least two different bandwidths and comment on how the estimate changes.

**Solution**
The plots show `Local linear: wage vs tenure` for 'Default bandwidth', 'bw = 2', and 'bw = 5'.
As the bandwidth increases, the variance of the estimator decreases, which is reflected in progressively narrower confidence intervals and a smoother fitted regression curve. With smaller bandwidths, the estimated function displays more local fluctuations and wider confidence bands, capturing short-run variation but at the cost of higher variability. In contrast, larger bandwidths produce a much smoother and nearly linear relationship between tenure and wages, with substantially tighter confidence intervals, as more observations are averaged at each point. However, this increased smoothness may introduce bias by oversmoothing and potentially masking genuine nonlinear features of the underlying relationship. Overall, the results clearly illustrate the bias-variance trade-off inherent in nonparametric regression.

## Problem 7
### Part (1)

**Question**
Write down the local linear regression estimator as the solution to a weighted least squares problem. How does it differ from the NW estimator?

**Solution**
The local linear regression estimator of $m(x_0)$ is obtained by solving:
$(\hat{a}_0, \hat{a}_1) = \arg \min_{a_0, a_1} \sum_{i=1}^N K\left(\frac{x_i - x_0}{h}\right) (y_i - a_0 - a_1(x_i - x_0))^2$
The estimate of the conditional mean at $x_0$ is $\hat{m}(x_0) = \hat{a}_0$, and the estimate of the derivative is $\hat{m}'(x_0) = \hat{a}_1$.

**Comparison with the NW estimator:** The NW estimator solves the same problem but with only a constant $m_0$ (i.e., $a_1 = 0$ is imposed):
$\hat{m}_{NW}(x_0) = \arg \min_{m_0} \sum_{i=1}^N K\left(\frac{x_i - x_0}{h}\right) (y_i - m_0)^2$
The key difference is that NW fits a horizontal line (constant) locally, while local linear fits a sloped line. This extra flexibility allows local linear to better approximate the true $m(x)$ when it has a nonzero slope, especially near the boundaries of the data.

### Part (2)

**Question**
Name two advantages of local linear regression over the NW (local constant) estimator.

**Solution**
**Advantage 1: Better behavior at the boundaries (reduced boundary bias).**
The NW estimator suffers from significant bias at the endpoints of the data. Near the boundary, the kernel window extends beyond the support of the data on one side, so the weighted average is computed using an asymmetric set of observations. For a local constant fit, this creates a systematic pull toward the interior of the data, flattening the estimate at the edges.
The local linear estimator partially overcomes this because a linear function can extrapolate beyond the data without the same systematic bias. The slope term $a_1(x_i - x_0)$ allows the fit to tilt appropriately even with an asymmetric set of observations.

**Advantage 2: Direct estimation of the derivative $m'(x_0)$.**
The local linear estimator simultaneously produces estimates of both $m(x_0)$ (the constant term $\hat{a}_0$) and $m'(x_0)$ (the slope $\hat{a}_1$). The derivative is the marginal effect of $x$ on $y$, which is often the quantity of primary interest in applied work. With the NW estimator, obtaining the derivative requires additional (more complex) techniques, such as taking numerical derivatives of the estimated curve.

### Part (3)

**Question**
The local linear estimator provides a direct estimate of $m'(x_0)$, the derivative of the conditional mean. Explain how. Why is this useful in applied work?

**Solution**
In the local linear framework, we model the conditional mean locally as:
$m(x) \approx a_0 + a_1(x - x_0)$ for $x$ near $x_0$
This is a first-order Taylor expansion of $m(x)$ around $x_0$:
$m(x) = m(x_0) + m'(x_0)(x - x_0) + O((x - x_0)^2)$
By comparison, $a_0 = m(x_0)$ and $a_1 = m'(x_0)$.
Therefore, the estimated slope coefficient $\hat{a}_1$ from the local weighted least squares regression is a direct estimate of the derivative $m'(x_0)$, i.e., the marginal effect of $x$ on $y$ at the point $x_0$.

**Why is this useful?** In applied work, we often want to know: *By how much does $y$ change when $x$ increases by one unit?* The answer is $m'(x_0)$, and local linear regression gives us this for free at every evaluation point. We can then:
*   Plot $m'(x_0)$ as a function of $x_0$ to see how the marginal effect varies.
*   Compute the average marginal effect: $\frac{1}{N} \sum_i m'(x_i)$.
*   Test whether the marginal effect is constant (i.e., test linearity of $m$).
Stata's `npregress` command reports average marginal effects by default.

### Part (4)

**Question**
Using the `nlsw88` data:
(a) Estimate a local linear regression of `wage` on `tenure` using Stata's `npregress kernel` command (the default is local linear). Report the estimated average marginal effect of tenure on wages.
(b) Plot the estimated conditional mean function using `npgraph`.
(c) Compare your local linear estimate with: (a) the NW estimate from Problem 6, and (b) a simple OLS regression. Discuss the differences, paying special attention to the behavior at the endpoints.

**Solution**
**(a)** We estimate a local linear regression of `wage` on `tenure` using `npregress kernel`. The estimated average marginal effect of tenure on wages is
$\frac{\partial E[wage|tenure]}{\partial tenure} = 0.186$,
indicating that an additional year of tenure increases hourly wages by approximately $0.19 on average. This effect is obtained as the average of the local derivatives across observations.

**(b)** The `Local linear: wage vs tenure` plot shows a smooth, increasing relationship between tenure and wages, consistent with a positive return to job tenure. The function is approximately linear over most of the support, with some curvature near the boundaries where fewer observations are available.

**(c)** The `Comparison of estimation methods` plot shows NW (local constant), Local linear, and OLS estimates.
The NW (local constant) and local linear estimators track each other very closely over most of the tenure range, indicating a smooth and nearly linear increase in wages with tenure in the interior of the data. This suggests limited nonlinear structure in the conditional mean. However, at high tenure levels near the boundary, both nonparametric estimators become unstable, with the local linear estimator exhibiting particularly large fluctuations. This reflects boundary problems and the scarcity of observations at extreme tenure values. In contrast, the OLS regression imposes a global linear relationship and remains smooth at the endpoints, though it cannot capture any local curvature. Overall, while all three methods deliver very similar average marginal effects, the nonparametric estimators highlight potential boundary issues that are masked by the parametric OLS specification.

## Problem 8
Consider the partially linear model:
$y_i = x_i'\beta + \lambda(z_i) + u_i$, $E[u_i | x_i, z_i] = 0$,
where $\lambda(\cdot)$ is an unknown function.

### Part (1)

**Question**
Explain why OLS estimation of $\beta$ in the equation $y_i = x_i'\beta + (\lambda(z_i) + u_i)$ would be in general inconsistent (i.e., why we cannot simply ignore $\lambda(z_i)$ or treat it as part of the error term).

**Solution**
In the partially linear model the function $\lambda(z_i)$ is generally correlated with $x_i$. If one estimates the regression
$y_i = x_i'\beta + \epsilon_i$,
where $\epsilon_i = \lambda(z_i) + u_i$, the OLS exogeneity condition fails because
$E[\epsilon_i | x_i] = E[\lambda(z_i) | x_i] \ne 0$
in general whenever $x_i$ and $z_i$ are correlated. Thus, $\lambda(z_i)$ acts as an omitted variable that is systematically related to the regressors. This violates the orthogonality condition required for OLS consistency and leads to omitted variable bias. Therefore, OLS estimation of $\beta$ is generally inconsistent unless $\lambda(z_i)$ is constant or independent of $x_i$.

### Part (2)

**Question**
Derive Robinson's difference estimator step by step:
(a) Take conditional expectations of both sides of the model, conditioning on $z$. What equation do you obtain?
(b) Subtract the equation from (i) from the original model. Write down the resulting equation.
(c) Explain how to estimate $\beta$ from this differenced equation.

**Solution**
**(a) Conditioning both sides on $z_i$:**
$E[y_i|z_i] = E[x_i'|z_i]\beta + \lambda(z_i) + \underbrace{E[u_i|z_i]}_{=0}$
So we obtain:
$E[y_i|z_i] = E[x_i'|z_i]\beta + \lambda(z_i)$

**(b)**
$y_i - E[y_i|z_i] = (x_i - E[x_i|z_i])'\beta + u_i$
The nonparametric component $\lambda(z_i)$ has been completely eliminated by differencing! We are left with a linear equation in $\beta$ where the regressors are $\tilde{x}_i = x_i - E[x_i|z_i]$ and the dependent variable is $\tilde{y}_i = y_i - E[y_i|z_i]$.

**(c) In practice, $E[y_i|z_i]$ and $E[x_i|z_i]$ are unknown.** We replace them with nonparametric (kernel regression) estimates $\hat{m}_y(z_i)$ and $\hat{m}_x(z_i)$:
$\tilde{y}_i = y_i - \hat{m}_y(z_i)$, $\tilde{x}_i = x_i - \hat{m}_x(z_i)$
Then, estimate $\beta$ by OLS of $\tilde{y}_i$ on $\tilde{x}_i$:
$\hat{\beta}_{PL} = \left(\sum_{i=1}^N \tilde{x}_i \tilde{x}_i'\right)^{-1} \sum_{i=1}^N \tilde{x}_i \tilde{y}_i$

### Part (3)

**Question**
Robinson's estimator of $\beta$ is $\sqrt{N}$-consistent. Explain why this is remarkable—why doesn't the nonparametric estimation of the conditional expectations slow down the convergence rate?

**Solution**
Robinson's estimator achieves $\sqrt{N}$-consistency because the nonparametric components enter only as nuisance functions that are differenced out before estimating $\beta$. After partialling out $E[y|z]$ and $E[x|z]$, the remaining regression is linear with an error term that is orthogonal to the regressors. The estimation error from the nonparametric first stage is of smaller order and does not affect the first-order asymptotic distribution, so $\beta$ converges at the parametric rate despite using nonparametric methods.

### Part (4)

**Question**
Describe how to estimate the nonparametric component $\lambda(z)$ once $\beta$ has been obtained.

**Solution**
Once we have $\hat{\beta}_{PL}$, we can estimate $\lambda(z)$ using the relationship:
$\lambda(z) = E[y|z] - E[x'|z]\beta$
The estimator is:
$\hat{\lambda}(z) = \hat{m}_y(z) - \hat{m}_x(z)'\hat{\beta}_{PL}$
where $\hat{m}_y(z)$ and $\hat{m}_x(z)$ are the kernel regression estimates from Step 1 of the Robinson procedure.
**Note:** The nonparametric component $\hat{\lambda}(z)$ converges at the slower nonparametric rate ($\sqrt{Nh}$, not $\sqrt{N}$), unlike $\hat{\beta}$. This is unavoidable—estimating an entire function is inherently harder than estimating a finite-dimensional parameter.

## Problem 9
Using the `nlsw88` dataset, consider the following partially linear model:
$wage_i = \beta_1 \cdot union_i + \beta_2 \cdot married_i + \lambda(tenure_i) + u_i$.
Here, `union` and `married` enter the model linearly (as dummy variables), while the effect of `tenure` on wages is left unspecified.

### Part (1)

**Question**
Before estimating the partially linear model, run a standard OLS regression of `wage` on `union`, `married`, and `tenure` (entering linearly). Report the estimated coefficients.

**Solution**
Linear model is estimated as follows:
$wage_i = \beta_0 + \beta_1 union_i + \beta_2 married_i + \beta_3 tenure_i + u_i$
```stata
reg wage union married tenure, robust
```
**Linear regression**
Number of obs =      1,868
F(3, 1864)    =     62.27
Prob > F      =     0.0000
R-squared     =     0.0854
Root MSE      =     3.9898
----------------------------------------------------------------
         wage | Robust
        | Coefficient  std. err.   t    P>|t|  [95% conf. interval]
----------------------------------------------------------------
        union |  1.120149   .2200396   5.09  0.000   .6885993   1.551699
      married | -.2669653   .2015122  -1.32  0.185  -.6621786   .128248
       tenure |  .1842085   .0165432  11.14  0.000   .1517634   .2166535
        _cons |  6.273233   .2080868  30.15  0.000   5.865125   6.68134
----------------------------------------------------------------
```
*   $\hat{\beta}_1$: 1.12, statistically significant (p < 0.001). Union workers earn about $1.12/hour more on average.
*   $\hat{\beta}_2$: -0.27, not statistically significant (p = 0.185).
*   $\hat{\beta}_3$: 0.18, statistically significant (p < 0.001). Each additional year of `tenure` increases wages by about $0.18/hour.

### Part (2)

**Question**
Now estimate the partially linear model using Robinson's estimator. Report the estimated coefficients on `union` and `married`. Compare them with the OLS estimates from part (a). Are the differences large? Interpret.

**Solution**
Now, the model is estimated as follows:
$wage_i = \beta_1 union_i + \beta_2 married_i + \lambda(tenure_i) + u_i$
The parametric coefficients $\hat{\beta}_1$ and $\hat{\beta}_2$ are similar to the OLS estimates, which suggests that the linearity assumption for `tenure` is not severely biasing the parametric components.
```stata
semipar wage union married, nonpar(tenure) robust ci
```
**semipar**
Number of obs =      1868
R-squared     =    0.0148
Adj R-squared =    0.0138
Root MSE      =    3.9721
----------------------------------------------------------------
         wage | Coefficient  Std. Err.   t    P>|t|  [95% Conf. Interval]
----------------------------------------------------------------
        union |   1.091552   .2228357   4.90  0.000   .6545187   1.528586
      married |  -.2726239   .2000104  -1.36  0.173  -.6648915   .1196436
----------------------------------------------------------------
```

### Part (3)

**Question**
The `semipar` command also produces a plot of the estimated $\hat{\lambda}(tenure)$. Describe the shape of this function. Does the effect of tenure on wages appear to be linear? If not, how would you characterize the nonlinearity?

**Solution**
The plot (`Partially linear model` plot showing `wage` vs `tenure`) of $\hat{\lambda}(tenure)$ shows the estimated nonparametric effect of tenure on wages, after controlling linearly for union status and marital status.
The function is increasing, indicating that wages rise with tenure. The relationship appears approximately linear over most of the range (0-15 years), with a slight flattening at very high tenure levels (beyond 20 years), suggesting possible diminishing returns. The confidence bands widen at the extremes due to sparse data. Overall, the linear specification used in OLS appears reasonable for this data.

### Part (4)

**Question**
Based on parts (a)-(c), discuss whether the linearity assumption for `tenure` in the OLS model seems reasonable. What are the practical implications of your findings?

**Solution**
The linearity assumption for `tenure` appears reasonable:
*   The parametric coefficients (`union`, `married`) are nearly identical in OLS and semi-parametric models.
*   The nonparametric function $\hat{\lambda}(tenure)$ is approximately linear over the main data range.
*   Minor flattening at high tenure suggests some diminishing returns, but this affects only a small portion of observations.

### Part (5)

**Question**
Bonus. Re-estimate the model using `ttl_exp` (total work experience) instead of `tenure` as the nonparametric variable. Does the nonparametric component look different? Comment.

**Solution**
**The model with total experience as the nonparametric component:**
$wage_i = \beta_1 union_i + \beta_2 married_i + \lambda(ttl\_exp_i) + u_i$
```stata
semipar wage union married, nonpar(ttl_exp) robust ci
```
**semipar**
Number of obs =      1878
R-squared     =    0.0215
Adj R-squared =    0.0204
Root MSE      =    3.8160
----------------------------------------------------------------
         wage | Coefficient  Std. Err.   t    P>|t|  [95% Conf. Interval]
----------------------------------------------------------------
        union |   1.301071   .2128636   6.11  0.000   .8835966   1.718545
      married |  -.1541987   .1944246  -0.79  0.428  -.53551    .2271126
----------------------------------------------------------------
```
Compared to the `tenure` model, the `union` coefficient increases from 1.09 to 1.30, the `married` coefficient becomes insignificant, and model fit improves ($R^2 = 0.0215$ vs 0.0148).
The plot (`Partially linear model (experience)`) of $\hat{\lambda}(ttl\_exp)$ shows stronger concavity than `tenure`—wages rise steeply in early career years but flatten after 15-20 years, exhibiting diminishing returns consistent with human capital theory and making the semiparametric approach more valuable here than for `tenure`.