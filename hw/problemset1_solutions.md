---
cost: 0.00592
course: General
difficulty: 8
est_minutes: 180
summary: This document presents detailed solutions to a problem set on causal inference
  and machine learning, focusing on panel data models, strict exogeneity, fixed and
  random effects, Hausman tests, and dynamic panel data estimation using Arellano-Bond
  GMM.
tags:
- Panel Data Models
- Strict Exogeneity
- Fixed Effects
- Random Effects
- Arellano-Bond GMM
type: Assignment
---

# Problem Set 1 Solutions
Causal Inference and Machine Learning
Instructor: Laura Mayoral
Teaching Assistant: Lucia Sauer
Winter 2026

# Problem 1

## Part (a)

**Question**
Describe the meaning of the strict exogeneity assumption and why it's needed in the estimation of fixed and random effects models.

**Solution**

**Strict Exogeneity:** The key condition is $E(U_{it}|X_{i1}, ..., X_{iT}) = 0$, meaning the conditional expectation of the error term given past, contemporaneous, and future values of the regressors is zero.

**Comparison with Traditional Exogeneity:**
*   **Traditional:** $X_{it}$ and $u_{it}$ are contemporaneously uncorrelated
*   **Strict:** All past, present, and future values ($X_{i1}, ..., X_{iT}$) are uncorrelated with $u_{it}$

**Why Strict Exogeneity is Needed:**
Panel data requires allowing for serial correlation within individuals (though not across individuals). Since observations within a unit are related over time, we cannot assume i.i.d. errors as in cross-sectional analysis. This necessitates strengthening the exogeneity assumption.

**Application to Estimators:**
*   **Fixed Effects (FE):** The within transformation subtracts means of $X_{it}$ and $u_{it}$. Without strict exogeneity, transformed regressors would correlate with transformed errors, causing bias.
*   **Random Effects (RE):** The GLS transformation exploits serial correlation in the composite error ($c_i + U_{it}$). Strict exogeneity ensures feasible GLS consistency.

## Part (b)

**Question**
Consider a distributed lag model explaining sales in firm $i$ as a function of its R&D investments:
$sales_{it} = \beta_1 RD_{it} + \beta_2 RD_{it-1} + c_i + U_{it}, t = 1,...,T$
Discuss whether it's reasonable to expect that the strict exogeneity assumption will hold.

**Solution**

Strict exogeneity is **unlikely to hold** in this context.

**Reason:** Future R&D investments likely depend on past sales. If a negative shock $U_{it}$ decreases sales at time $t$, firms may adjust R&D investments at $t+1$ (either increasing to recover or decreasing due to budget constraints). This creates correlation between $u_{it}$ and $RD_{it+1}$, violating strict exogeneity.

**Example:** A poor quarter ($u_{it} < 0$) reduces sales, leading management to cut R&D spending next year. Then $Cov(u_{it}, RD_{it+1}) \neq 0$, violating $E(u_{it}|RD_{i1}, ..., RD_{iT}) = 0$.

# Problem 2

Consider the unobserved effects model:
$Y_{it} = X_{it}\beta + c_i + u_{it}, t = 1,...,T$

with assumptions:
(i) $E[U_{it}|X_i, c_i] = 0, t = 1, . . . ,T$
(ii) $E[c_i|X_i] = E[c_i] = 0$
(iii) $E[u_{it}^2|X_i, c_i] = \sigma_u^2 I_T$ and $Var(c_i|X_i) = Var(c_i) = \sigma_c^2$

## Part (a)

**Question**
Describe the meaning of assumptions (i), (ii), and (iii). Under these assumptions, does the OLS estimator have a causal interpretation?

**Solution**

**Assumption Meanings:**
*   **(i) Strict Exogeneity:** Idiosyncratic errors have zero mean and are uncorrelated with regressors and fixed effects at all time periods. Ensures no endogeneity from $U_{it}$.
*   **(ii) Random Effects (Mean Independence):** Individual-specific effects are independent of regressors. If $c_i$ is left in the error, no omitted variable bias.
*   **(iii) Homoskedasticity and No Serial Correlation:** $Var(u_{it})$ is constant across $i$ and $t$ with no autocorrelation. $Var(c_i)$ is constant across units.

**Causal Interpretation:** Yes. Under (i) and (ii), $X_{it}$ is exogenous and uncorrelated with $c_i$, so OLS provides causal estimates of $\beta$.

## Part (b)

**Question**
Under assumptions (i)-(iii), what's the most efficient way of estimating model (1)? Add any other assumption you might need.

**Solution**

The most efficient estimator is **Random Effects GLS (RE-GLS)**.

**Additional Assumptions:**
*   $rank(E[X'\Omega X]) = K$ where $\Omega$ is the variance-covariance matrix of composite errors
*   Linearity of the underlying relationship

## Part (c)

**Question**
Now assume that assumptions (ii) and (iii) might not hold. Under assumption (i), does the OLS estimator have a causal interpretation?

**Solution**

No. Neither OLS nor RE would have a causal interpretation.
If (ii) fails, $c_i$ is correlated with $X_{it}$, violating the OLS identification condition since $X_{it}$ correlates with the composite error ($u_{it} + c_i$). The fixed effect $c_i$ becomes an omitted/confounding variable.

## Part (d)

**Question**
Describe how you would estimate model (1) if assumption (ii) fails. Does this estimator have a causal interpretation under that assumption?

**Solution**

Use a **Fixed Effects (FE) estimator**, which removes individual-specific effects via demeaning (within transformation) or first differencing.

**Causal Interpretation:** Yes. Under assumption (i) alone, transformed regressors are uncorrelated with the error term, making FE consistent. Only within-group variation in $X_{it}$ is used to estimate $\beta$, eliminating omitted variable bias from $c_i$.

## Part (e)

**Question**
Describe how you would estimate the standard error of the estimator you suggested in (d) if (ii) and (iii) fail.

**Solution**

Use the **FE estimator with cluster-robust standard errors**, clustering at the unit level. This accounts for heteroskedasticity and serial correlation within units when assumptions (ii) and (iii) fail.

# Problem 3 (Wooldridge 10.1)

## Question
Consider a model for new capital investment in a particular industry where cross-section observations are at the county level with $T$ years of data:
$log(invest_{it}) = \theta_t + Z_{it}\gamma + \delta_1 tax_{it} + \delta_2 disaster_{it} + c_i + u_{it}$

## Part (a)

**Question**
Why is allowing for aggregate time effects in the equation important?

**Solution**

Time effects $\theta_t$ capture shocks or trends affecting all counties, preventing misattribution to tax changes and improving model fit. Examples include:
*   Macroeconomic conditions (recessions, interest rates, inflation)
*   Federal policies (tax reforms, stimulus packages)
*   General trends (technological progress, demographic shifts)

## Part (b)

**Question**
What kinds of variables are captured in $c_i$?

**Solution**

$c_i$ represents county-specific, time-invariant unobserved heterogeneity, but it should be noted that some may actually be observable and included in $Z_{it}$:
*   **Economic structure:** Industrial composition, infrastructure quality, access to finance
*   **Geographic/demographic:** Location, climate, population density, education levels
*   **Institutional:** Governance quality, corruption, zoning laws, regulatory burden
*   **Historical patterns:** Investment legacy, path dependency, agglomeration effects

## Part (c)

**Question**
Interpreting the equation in a causal fashion, what sign does economic reasoning suggest for $\delta_1$?

**Solution**

**Negative sign.** Lower marginal tax rates should increase investment, as money not spent on taxes can be allocated to capital investments.

## Part (d)

**Question**
Explain in detail how you would estimate this model; be specific about the assumptions you are making.

**Solution**

Use a **Fixed Effects (FE) model** to control for omitted variable bias from unobserved county characteristics potentially correlated with tax rates, disasters, or other regressors.
**Key Assumption:** Strict exogeneity — regressors are uncorrelated with idiosyncratic error terms $u_{it}$ across all time periods.
**Difficult-to-measure variables captured by $c_i$:** Institutional quality (corruption, business-friendliness), infrastructure suitability.

## Part (e)

**Question**
Discuss whether strict exogeneity is reasonable for the two variables $tax_{it}$ and $disaster_{it}$; assume that neither has a lagged effect on capital investment.

**Solution**

Strict exogeneity requires: $E[u_{it}|tax_i, disaster_i, z_i, c_i] = 0$.

**Disasters:** More plausibly exogenous as random events. Even disaster-prone counties (fault lines, hurricane belts) should be captured by $c_i$ or $z_{it}$.
**Potential violations:**
*   **Measurement issues:** If disaster severity is subjectively reported, local economic conditions might influence whether an event is classified as a "major" disaster, creating correlation with $u_{it}$.
*   **Government response:** Targeted aid or tax relief following disasters could introduce endogeneity if the disaster variable indirectly captures these policy responses.

**Taxes:** Less likely to satisfy strict exogeneity.
**Potential violations:**
*   **Reverse causality:** Policymakers may adjust tax rates in response to observed investment trends. Counties with high investment growth might lower taxes to attract additional capital, while those experiencing declining investment might raise taxes to maintain revenue. This creates correlation between current shocks $u_{it}$ and future tax rates $tax_{i,t+1}$.
*   **Anticipation effects:** Firms may adjust current investment based on expected future tax changes (e.g., anticipated policy shifts following local elections or budget announcements). If shocks affecting current investment also signal future policy changes, strict exogeneity fails.

**Conclusion:** Strict exogeneity is more defensible for disasters than for tax rates, though neither is beyond question.

# Problem 4 (Wooldridge 10.7)

## Question
Use two terms of data in GPA.RAW to estimate an unobserved effects model. Drop cumgpa (violates strict exogeneity). Data: 366 student-athletes, two semesters each. Key variable: in-season effect on GPA.

## Part (a)

**Question**
Estimate the model by random effects, and interpret the coefficient on the in-season variable.

**Solution**

**Interpretation:** Being in-season decreases GPA by 0.044 points. However, this effect is not statistically significant.

```
xtreg trmgpa spring crsgpa frstsem season sat verbmath hsperc hssize black female, re

Random-effects GLS regression               Number of obs     =        732
Group variable: id                          Number of groups  =        366

R-squared:                                  Obs per group:
    Within = 0.2067                             min =          2
    Between = 0.5390                            avg =        2.0
    Overall = 0.4785                            max =          2

corr(u_i, X) = 0 (assumed)                  Wald chi2(10)     =     512.77
                                            Prob > chi2       =     0.0000
--------------------------------------------------------------------------
trmgpa | Coefficient  Std. err.    z    P>|z|   [95% conf. interval]
-------+------------------------------------------------------------------
spring |  -.0606536   .0371605  -1.63   0.103  -.1334868     .0121797
crsgpa |   1.082365   .0930877  11.63   0.000   .8999166     1.264814
frstsem |   .0029948   .0599542   0.05   0.960  -.1145132     .1205028
 season |  -.0440992   .0392381  -1.12   0.261  -.1210044     .032806
    sat |   .0017052   .0001771   9.63   0.000   .0013582     .0020523
verbmath |   -.15752    .16351   -0.96   0.335  -.4779937     .1629538
 hsperc |  -.0084622   .0012426  -6.81   0.000  -.0108977    -.0060268
 hssize |  -.0000775   .0001248  -0.62   0.534  -.000322      .000167
  black |  -.2348189   .0681573  -3.45   0.001  -.3684048    -.1012331
 female |   .358153    .0612948   5.84   0.000   .2380173     .4782886
  _cons |  -1.73492   .3566599  -4.86   0.000  -2.43396    -1.035879
--------------------------------------------------------------------------
sigma_u |  .37185442
sigma_e |  .40882825
    rho |  .4527451     (fraction of variance due to u_i)
--------------------------------------------------------------------------
```

## Part (b)

**Question**
Estimate the model by fixed effects; informally compare the estimates to the RE estimates, in particular the in-season effect.

**Solution**

Time-invariant variables (gender, ethnicity, SAT) are dropped in FE.
**FE interpretation:** For individuals switching seasons, GPA decreases by 0.057 points on average (within-individual effect only).

```
xtreg trmgpa spring crsgpa frstsem season sat verbmath hsperc hssize black female, fe

Fixed-effects (within) regression              Number of obs     =        732
Group variable: id                             Number of groups  =        366

R-squared:                                     Obs per group:
    Within = 0.2069                                min =          2
    Between = 0.0333                               avg =        2.0
    Overall = 0.0613                               max =          2

corr(u_i, Xb) = -0.0893                        F(4, 362)         =      23.61
                                               Prob > F          =     0.0000
--------------------------------------------------------------------------
trmgpa | Coefficient  Std. err.    t    P>|t|   [95% conf. interval]
-------+------------------------------------------------------------------
spring |  -.0657817   .0391404  -1.68   0.094  -.1427528     .0111895
crsgpa |   1.140688   .1186538   9.61   0.000   .9073505     1.374025
frstsem |   .0128523   .0688364   0.19   0.852  -.1225172     .1482218
 season |  -.0566454   .0414748  -1.37   0.173  -.1382072     .0249165
    sat |         0  (omitted)
verbmath |         0  (omitted)
 hsperc |         0  (omitted)
 hssize |         0  (omitted)
  black |         0  (omitted)
 female |         0  (omitted)
  _cons |  -.7708055   .3305004  -2.33   0.020  -1.420747    -.1208636
--------------------------------------------------------------------------
sigma_u |  .67913296
sigma_e |  .40882825
    rho |  .73400603     (fraction of variance due to u_i)
--------------------------------------------------------------------------
F test that all u_i=0: F(365, 362) = 5.40         Prob > F = 0.0000
```

## Part (c)

**Question**
Construct the non-robust Hausman test comparing RE and FE. Include all time-varying variables except the term dummy.

**Solution**

Hausman test: $\chi^2(4) = 1.81, p = 0.77$.
**Result:** Fail to reject null hypothesis. RE estimator is consistent and efficient; FE not required.
**Caveat:** This test relies on stringent assumptions (homoskedasticity). Hausman tests are rarely used in practice, with researchers favoring FE models for robustness.

# Problem 5

## Part (a)

**Question**
Describe the logic of the Hausman test to discriminate between random and fixed effects.

**Solution**

The Hausman test checks whether unobserved effects correlate with regressors.

**Hypotheses:**
*   $H_0: Cov(X_{it}, C_i) = 0$ — RE is consistent and efficient (preferred)
*   $H_1: Cov(X_{it}, C_i) \neq 0$ — RE is inconsistent; FE is consistent (preferred)

**Test Statistic:**
$H = (\hat{\beta}_{FE} - \hat{\beta}_{RE})' [Var(\hat{\beta}_{FE}) – Var(\hat{\beta}_{RE})]^{-1} (\hat{\beta}_{FE} - \hat{\beta}_{RE}) \sim \chi^2_K$

**Decision:**
*   Small $H$ (high p-value) $\Rightarrow$ Fail to reject $H_0 \Rightarrow$ Use RE
*   Large $H$ (low p-value) $\Rightarrow$ Reject $H_0 \Rightarrow$ Use FE

**Intuition:** Large differences between FE and RE coefficients suggest the RE assumption is violated. If the difference is small, both models provide similar estimates and RE should be preferred due to its efficiency.

## Part (b)

**Question**
Describe the limitations of the non-robust version you computed in Problem 3.

**Solution**

*   Critical values become invalid under heteroskedasticity (when RE is not efficient)
*   Since homoskedasticity is highly restrictive, the non-robust Hausman test is often unreliable

## Part (c)

**Question**
Assess whether it's more reasonable in general to use FE versus RE models and why. Also, describe the potential problems of using FE estimators.

**Solution**

**General Recommendation:** FE is typically safer.
**Reason:** Unobserved effects are often correlated with regressors, making FE necessary to avoid omitted variable bias. Controlling for all stable characteristics allows causal inference.

**FE Limitations:**
*   **Ignores between-group variation:** Less efficient (larger standard errors) than RE
*   **Cannot estimate time-invariant effects:** Variables constant over time are dropped (e.g., education's effect on wages if education doesn't change)
*   **Computational cost:** Expensive with high-dimensional fixed effects (e.g., firm-year interactions)
*   **Amplifies measurement error:** FE eliminates "useful variation," leaving "noisy variation" relatively larger

**Summary:** Use FE as default; RE as robustness check.

# Problem 6 (Wooldridge 10.12)

## Question
Data in WAGEPAN.RAW: 545 men working every year 1980-1987. Consider the wage equation.
$log(wage)_{it} = \theta_t + \beta_1 educ_i + \beta_2 black_i + \beta_3 hispan_i + \beta_4 exper_{it} + \beta_5 exper^2_{it} + \beta_6 married_{it} + \beta_7 union_{it} + c_i + u_{it}$
Education does not change over time.

## Part (a)

**Question**
Estimate by pooled OLS. Are usual OLS standard errors reliable if $c_i$ is uncorrelated with explanatory variables? Explain. Compute appropriate standard errors.

**Solution**

No. Even if $c_i$ is uncorrelated with regressors, OLS standard errors are unreliable due to:
*   Within-unit serial correlation over time
*   Heteroskedasticity across individuals

**Solution:** Use cluster-robust standard errors (clustered by individual).

```
reg lwage educ black hisp exper expersq union, vce(cluster nr)

Linear regression                               Number of obs     =      4,360
                                                F(6, 544)         =      66.44
                                                Prob > F          =     0.0000
                                                R-squared         =     0.1778
                                                Root MSE          =     .48328

------------------------------------------------------------------------------
             |   Robust
       lwage | Coefficient  std. err.    t    P>|t|   [95% conf. interval]
-------------+----------------------------------------------------------------
        educ |   .1025897   .0092867  11.05   0.000   .0843474     .1208319
       black |  -.1690408   .0492897  -3.43   0.001  -.2658623    -.0722193
       hisp |   .0138846   .0398399   0.35   0.728  -.0643742     .0921435
       exper |   .1008117   .012106    8.33   0.000   .0770315     .1245919
     expersq |  -.0032369   .0008535  -3.79   0.000  -.0049134    -.0015603
       union |   .1864264   .0277349   6.72   0.000   .1319457     .2409071
       _cons |  -.0796806   .1205804  -0.66   0.509  -.3165409     .1571796
------------------------------------------------------------------------------
```

## Part (b)

**Question**
Estimate by random effects. Compare with pooled OLS estimates.

**Solution**

*   The coefficient for education remains the same.
*   All signs unchanged; magnitudes slightly different.
*   Hispanic effect remains statistically insignificant.

```
xtreg lwage educ black hisp exper expersq married union, re vce(cluster nr)

Random-effects GLS regression               Number of obs     =      4,360
Group variable: nr                          Number of groups  =        545

R-squared:                                  Obs per group:
    Within = 0.1774                             min =          8
    Between = 0.1837                            avg =        8.0
    Overall = 0.1808                            max =          8

corr(u_i, X) = 0 (assumed)                  Wald chi2(7)      =     577.54
                                            Prob > chi2       =     0.0000

------------------------------------------------------------------------------
             |   Robust
       lwage | Coefficient  std. err.    z    P>|z|   [95% conf. interval]
-------------+----------------------------------------------------------------
        educ |   .1012246   .0088902  11.39   0.000   .0838001     .1186492
       black |  -.1441307   .0503425  -2.86   0.004  -.2428002    -.0454612
       hisp |   .0201511   .0399299   0.50   0.614  -.0581102     .0984123
       exper |   .1121195   .0105475  10.63   0.000   .0914468     .1327921
     expersq |  -.0040689   .0006747  -6.03   0.000  -.0053913    -.0027465
     married |   .0627951   .0189972   3.31   0.001   .0255614     .1000289
       union |   .1073788   .0209015   5.14   0.000   .0664127     .148345
       _cons |  -.1074642   .1151611  -0.93   0.351  -.3331758     .1182474
------------------------------------------------------------------------------
sigma_u |  .32456727
sigma_e |  .35125535
    rho |  .46057171     (fraction of variance due to u_i)
------------------------------------------------------------------------------
```

## Part (c)

**Question**
Estimate by fixed effects. Why is $exper_{it}$ redundant even though it changes over time? What happens to the marriage and union premiums compared to RE?

**Solution**

**Why $exper_{it}$ is redundant:** All men work all years, so experience increases by 1 each year for everyone—no meaningful variation.
**Marriage and union premiums:** Coefficients are smaller in FE than RE, suggesting higher RE premiums are partly due to unobserved heterogeneity (controlled for in FE).

```
xtreg lwage educ black hisp exper expersq married union, fe vce(cluster nr)

Fixed-effects (within) regression              Number of obs     =      4,360
Group variable: nr                             Number of groups  =        545

R-squared:                                     Obs per group:
    Within = 0.1780                                min =          8
    Between = 0.0005                               avg =        8.0
    Overall = 0.0638                               max =          8

corr(u_i, Xb) = -0.1139                        F(4, 544)         =     107.94
                                               Prob > F          =     0.0000

------------------------------------------------------------------------------
             |   Robust
       lwage | Coefficient  std. err.    t    P>|t|   [95% conf. interval]
-------------+----------------------------------------------------------------
        educ |         0  (omitted)
       black |         0  (omitted)
       hisp |         0  (omitted)
       exper |   .1168467   .010713   10.91   0.000   .0958028     .1378906
     expersq |  -.0043009   .0006861  -6.27   0.000  -.0056486    -.0029532
     married |   .0453033   .0210041   2.16   0.031   .0040442     .0865625
       union |   .0820871   .0228266   3.60   0.000   .037248      .1269262
       _cons |   1.06488    .0366294  29.07   0.000   .9929274     1.136832
------------------------------------------------------------------------------
sigma_u |  .40005389
sigma_e |  .35125535
    rho |  .56467848     (fraction of variance due to u_i)
------------------------------------------------------------------------------
```

## Part (d)

**Question**
Add interactions d81·educ, d82·educ, ..., d87·educ and estimate by FE. Has the return to education increased over time?

**Solution**

Coefficients progress from negative to positive and increase almost continuously. However, p-values are too large to reject the null hypothesis of no effect.

```
xtreg lwage married union c.educ##i.year, fe vce(cluster nr)
note: educ omitted because of collinearity.

Fixed-effects (within) regression              Number of obs     =      4,360
Group variable: nr                             Number of groups  =        545

R-squared:                                     Obs per group:
    Within = 0.1708                                min =          8
    Between = 0.1900                               avg =        8.0
    Overall = 0.1325                               max =          8

corr(u_i, Xb) = 0.0991                         F(16, 544)        =      28.23
                                               Prob > F          =     0.0000
------------------------------------------------------------------------------
             |   Robust
       lwage | Coefficient  std. err.    t    P>|t|   [95% conf. interval]
-------------+----------------------------------------------------------------
     married |   .0548205   .0212086   2.58   0.010   .0131596     .0964814
       union |   .0829785   .0230356   3.60   0.000   .0377288     .1282282
        educ |         0  (omitted)
        year |
        1981 |  -.0224159   .1443845  -0.16   0.877  -.3060352     .2612035
        1982 |  -.0057613   .1390377  -0.04   0.967  -.2788779     .2673553
        1983 |   .0104296   .154036    0.07   0.946  -.2921486     .3130079
        1984 |   .0843743   .1586968   0.53   0.595  -.2273593     .3961078
        1985 |   .0497251   .1572949   0.32   0.752  -.2592548     .3587049
        1986 |   .0656064   .1705619   0.38   0.701  -.2694342     .400647
        1987 |   .0904447   .1566013   0.58   0.564  -.2171727     .3980621
             |
   year#c.educ |
        1981 |   .0115854   .0121988   0.95   0.343  -.0123771     .0355479
        1982 |   .0147905   .0117953   1.25   0.210  -.0083793     .0379603
        1983 |   .0171182   .013086    1.31   0.191  -.0085871     .0428236
        1984 |   .0165839   .0138441   1.20   0.231  -.0106105     .0437783
        1985 |   .0237086   .0135902   1.74   0.082  -.0029871     .0504042
        1986 |   .0274123   .0147316   1.86   0.063  -.0015255     .05635
        1987 |   .0304332   .0134996   2.25   0.025   .0039155     .0569509
             |
       _cons |   1.362459   .0203386  66.99   0.000   1.322507     1.40241
------------------------------------------------------------------------------
sigma_u |  .37264192
sigma_e |  .35335714
    rho |  .52654437     (fraction of variance due to u_i)
------------------------------------------------------------------------------
```
The joint Wald test is not statistically significant $F(7,544) = 0.88, p = 0.521$, indicating no evidence of time-varying returns to education.

## Part (e)

**Question**
Return to the original model in part (c). Add a lead of the union variable, $union_{i,t+1}$, and estimate by FE (note: you lose 1987 data). Is $union_{i,t+1}$ significant? What does this say about strict exogeneity of union membership?

**Solution**

The coefficient on $union_{i,t+1}$ is statistically significant at the 5% level.
**Implication:** Future union membership systematically correlates with current wages, violating strict exogeneity.
**Possible mechanism:** Workers with higher wages are more likely to join unions in the future, suggesting reverse causality or anticipation effects.

```
xtreg lwage educ exper expersq married union union_lead, fe vce(cluster nr)

Fixed-effects (within) regression              Number of obs     =      3,815
Group variable: nr                             Number of groups  =        545

R-squared:                                     Obs per group:
    Within = 0.1458                                min =          7
    Between = 0.0067                               avg =        7.0
    Overall = 0.0582                               max =          7

corr(u_i, Xb) = -0.1037                        F(5, 544)         =      61.61
                                               Prob > F          =     0.0000
------------------------------------------------------------------------------
             |   Robust
       lwage | Coefficient  std. err.    t    P>|t|   [95% conf. interval]
-------------+----------------------------------------------------------------
        educ |         0  (omitted)
       exper |   .1213332   .0123665   9.81   0.000   .0970413     .145625
     expersq |  -.0049551   .0008547  -5.80   0.000  -.006634    -.0032762
     married |   .0435952   .0234958   1.86   0.064  -.0025584     .0897488
       union |   .075699    .0236597   3.20   001   .0292235     .1221745
  union_lead |   .0514698   .0227382   2.26   0.024   .0068044     .0961353
       _cons |   1.052117   .0396707  26.52   0.000   .9741904     1.130043
------------------------------------------------------------------------------
sigma_u |  .40174034
sigma_e |  .35745464
    rho |  .55813464     (fraction of variance due to u_i)
------------------------------------------------------------------------------
```

# Problem 7

## Part (a)

**Question**
In the context of dynamic panel data model estimation, what is the Nickell bias? Explain clearly why the problem arises and whether it affects short and/or large panels similarly. Explain how the Arellano-Bond estimator overcomes the problem.

**Solution**

**Nickell Bias (Dynamic Panel Bias):** Arises when combining fixed effects and lags of the dependent variable in a linear regression:
$Y_{it} = \alpha Y_{it-1} + X_{it}\beta + \mu_i + \epsilon_{it}$
The bias affects the coefficient $\alpha$ of the lagged dependent variable.

**Why the Problem Arises:**
When demeaning (within transformation), we subtract individual means:
$\tilde{Y}_{it} = \alpha \tilde{Y}_{it-1} + \tilde{X}_{it}\beta + \tilde{\epsilon}_{it}$
where tilde denotes deviation from individual means. The transformed dependent variable $\tilde{Y}_{it}$ contains all values of $Y_i$ (needed to compute the average), and the transformed error $\tilde{\epsilon}_{it}$ also depends on all values of $\epsilon_i$. Consequently, $\tilde{Y}_{it-1}$ is correlated with $\tilde{\epsilon}_{it}$, creating Nickell bias (when $T$ is assumed to be fixed).

**Effect on Panel Types:**
*   **Short panels (small $T$):** Severe bias of order $O(1/T)$
*   **Large panels (large $T$):** Bias diminishes as $T \rightarrow \infty$

**Arellano-Bond Solution:**
1.  **First-difference** to remove fixed effects: $\Delta Y_{it} = \alpha \Delta Y_{it-1} + \Delta X_{it}\beta + \Delta \epsilon_{it}$
2.  **Problem:** $\Delta Y_{it-1}$ still endogenous (correlated with $\Delta \epsilon_{it}$)
3.  **Use past levels as instruments:** $Y_{it-2}$ is correlated with $Y_{it-1}$ but uncorrelated with $\epsilon_{it}$ or $\epsilon_{it-1}$, making it a valid instrument.
4.  **Apply GMM** to combine multiple moment conditions efficiently.

## Part (b)

**Question**
Consider the original data in the Arellano and Bond paper (STATA: webuse abdata). Unbalanced panel of 140 UK firms, 1976-1984. Model employment $n$ using partial adjustment model with two lags. Estimate by OLS and within estimator. Compare the coefficient of the first lag and discuss the direction of bias.

**Solution**

The pooled OLS and random effects regressions yield a coefficient for the first lag (nL1) just over 1.0, while the fixed effects estimate is closer to 0.7.

```
reg n nL1 nL2 w wL1 k kL1 kL2 ys ysL1 ysL2 cap yr*
xtreg n nL1 nL2 w wL1 k kL1 kL2 ys ysL1 ysL2 cap yr*, re
xtreg n nL1 nL2 w wL1 k kL1 kL2 ys ysL1 ysL2 cap yr*, fe

Comparison of Panel Data Estimators
--------------------------------------------------------------------------------
Variable |   OLS_ab        RE_ab        FE_ab
---------+----------------------------------------------------------------------
     nL1 | 1.0370986***  1.0370986***  .73365614***
     nL2 |-.07035277*   -.07035277*   -.1395947***
       w |-.51970221***  -.51970221***  -.55916893***
     wL1 | .47175589***  .47175589***  .31845945***
       k | .34781036***  .34781036***  .38127208***
     kL1 |-.20030792***  -.20030792***  -.07874491*
     kL2 |-.11492062***  -.11492062***  -.0272623
      ys | .44871524***  .44871524***  .46789006***
    ysL1 |-.7614316***  -.7614316***  -.62680673***
    ysL2 | .2836768*    .2836768*     .05586949
     cap | .00183582*   -.00183582*    .00351535
  yr1976 | (omitted)     (omitted)     (omitted)
  yr1977 | (omitted)     (omitted)     (omitted)
  yr1978 | (omitted)     -.01589887    .01396398
  yr1979 | .01583007     -.0000688     .01861122
  yr1980 | .02342943     .00753056     .02444529
  yr1981 |-.01933861    -.03523748    -.01210953
  yr1982 | .01370475     -.02960361    -.02094094
  yr1983 | .00775164     -.00814723    -.01553745
  yr1984 | .01589887     (omitted)     (omitted)
   _cons | .31696965     .33286852     1.7594715***
---------+----------------------------------------------------------------------
       N |      751           751           751
      r2 | .9944317                 .79749471
     r2_w|              .76966011     .79749471
     r2_b|              .99902012     .97983588
     r2_o|              .9944317     .97463524
--------------------------------------------------------------------------------
Legend: * p<0.05; ** p<0.01; *** p<0.001
```

**OLS (Upward Bias):**
*   Does not account for unobserved individual-specific effects ($\mu_i$).
*   Variation actually explained by fixed effects is incorrectly attributed to the lagged dependent variable.
*   Since the lag is also influenced by the same unaccounted fixed effects, coefficient is inflated.

**Fixed Effects (Downward Bias):**
*   Fixed effects eliminated by demeaning, but creates correlation between transformed lagged term and error.
*   Nickell (1981) shows that when the estimated coefficient is positive (here 0.7), the bias is invariably negative.

**Conclusion:** OLS overestimates while FE underestimates the true coefficient.

## Part (c)

**Question**
Apply the AB estimator using xtabond2. Discuss the output in detail, the difference between "GMM" and "IV" style instruments, and the meaning of different options.

**Solution**

**Arellano-Bond Estimation Results:**
The AB estimate for the first lag coefficient is $\hat{\alpha}_{AB} = 0.818$, falling between OLS ($\approx 1.037$) and FE ($\approx 0.734$) as expected.

```
xtabond2 n nL1 nL2 w wL1 k kL1 kL2 ys ysL1 ysL2 yr*, ///
gmm(L.(n w k)) ///
iv(L(0/2).ys yr*) ///
nolevel robust small

Dynamic panel-data estimation, one-step difference GMM

Group variable: id                         Number of obs     =        611
Time variable: year                        Number of groups  =        140
Number of instruments = 90                 Obs per group: min =          4
F(15, 140)      =   90.98                                  avg =       4.36
Prob > F        =    0.000                                 max =          6

------------------------------------------------------------------------------
             |   Robust
           n | Coefficient  std. err.    t    P>|t|   [95% conf. interval]
-------------+----------------------------------------------------------------
         nL1 |   .8179867   .0859779   9.51   0.000   .6480038     .9879695
         nL2 |  -.1122756   .0502376  -2.23   0.027  -.211598    -.0129532
           w |  -.6816685   .1425842  -4.78   0.000  -.9635652    -.3997718
         wL1 |   .6557083   .2023722   3.24   0.001   .2556076     1.055809
           k |   .3525689   .1218022   2.89   0.004   .1117594     .5933784
         kL1 |  -.1536626   .0862946  -1.78   0.077  -.3242716     .0169464
         kL2 |   .0304529   .0321362  -0.95   0.345  -.0939879     .033082
          ys |   .6509498   .1895859   3.43   0.001   .2761283     1.025771
        ysL1 |  -.9162028   .2639328  -3.47   0.001  -1.438012    -.3943934
        ysL2 |   .2786584   .1855324   1.50   0.135  -.0881491     .645466
      yr1978 |  -.0113271   .0092007  -1.23   0.220  -.0295174     .0068632
      yr1980 |   .0150417   .0139742   1.08   0.284  -.0125861     .0426696
      yr1981 |  -.0249536   .0261443  -0.95   0.341  -.0766422     .026735
      yr1982 |  -.046388    .0279219  -1.66   0.099  -.1015911     .008815
      yr1983 |  -.0421715   .0332151  -1.27   0.206  -.1078396     .0234965
      yr1984 |  -.0352258   .0351853  -1.00   0.318  -.104789     .0343375
------------------------------------------------------------------------------
Instruments for first differences equation
Standard
   D.(ys L.ys L2.ys yr1976 yr1977 yr1978 yr1979 yr1980 yr1981 yr1982 yr1983
     yr1984)
GMM-type (missing=0, separate instruments for each period unless collapsed)
   L(1/8).(L.n L.w L.k)
------------------------------------------------------------------------------
Arellano-Bond test for AR(1) in first differences: z = -5.39  Pr > z = 0.000
Arellano-Bond test for AR(2) in first differences: z = -0.78  Pr > z = 0.436
------------------------------------------------------------------------------
Sargan test of overid. restrictions: chi2(74) = 120.62 Prob > chi2 = 0.001
    (Not robust, but not weakened by many instruments.)
Hansen test of overid. restrictions: chi2(74) = 73.72  Prob > chi2 = 0.487
    (Robust, but weakened by many instruments.)
------------------------------------------------------------------------------
Difference-in-Hansen tests of exogeneity of instrument subsets:
iv(ys L.ys L2.ys yr1976 yr1977 yr1978 yr1979 yr1980 yr1981 yr1982 yr1983
  yr1984)
  Hansen test excluding group: chi2(65) = 56.99 Prob > chi2 = 0.750
  Difference (null H = exogenous): chi2(9) = 16.72 Prob > chi2 = 0.053
------------------------------------------------------------------------------
```

**Command Options Explained:**
*   `gmm(L.(n w k))`: GMM-style instruments for lags of employment, wages, and capital (potentially endogenous variables)
*   `iv(L(0/2).ys yr*))`: IV-style instruments for output lags 0-2 and year dummies (assumed strictly exogenous)
*   `nolevel`: First-difference GMM (not system GMM)
*   `robust`: Heteroskedasticity-robust standard errors
*   `small`: Small-sample corrections (t-tests instead of z-tests)

**GMM-style vs IV-style Instruments:**
*   **GMM-style:** Creates separate instruments for each period. For $Z_t$, uses $(Z_{t-2}, Z_{t-3}, ...)$ as instruments for $\Delta Z_t$. Exploits maximum information but generates many instruments (here: 90 total).
*   **IV-style:** Uses same instruments across all periods (standard IV approach). More parsimonious. Appropriate for strictly exogenous variables.

**Coefficient Interpretation:**
*   **nL1 = 0.818:** Strong employment persistence (82% of previous period's employment carries forward)
*   **nL2 = -0.112:** Negative second lag suggests partial mean reversion
*   **w = -0.682:** Current wages negatively affect employment (labor cost effect)
*   **wL1 = 0.656:** Past wages positively associated (adjustment lags)
*   **k = 0.353:** Current capital complements labor
*   **ys = 0.651:** Sector output increases employment (demand effect)

**Diagnostic Tests:**

| Test      | Statistic             | p-value | Interpretation       |
| :-------- | :-------------------- | :------ | :------------------- |
| AR(1)     | z = -5.39             | 0.000   | ✓ Reject (expected)  |
| AR(2)     | z = -0.78             | 0.436   | ✓ Fail to reject (valid) |
| Hansen    | $\chi^2(74)$ = 73.72  | 0.487   | ✓ Fail to reject (valid) |

*   **AR(1) rejection:** Expected—first-differenced errors are serially correlated by construction.
*   **AR(2) non-rejection:** No second-order autocorrelation in levels, confirming instrument validity.
*   **Hansen test:** Overidentifying restrictions satisfied (p = 0.487), instruments are valid.
**Note on Sargan Test:** The Sargan test (p = 0.001) rejects validity, but this test is **not robust** to heteroskedasticity. We rely on the robust Hansen test instead, which confirms instrument validity.

## Part (d)

**Question**
Compare the value of the coefficient of the first lag you obtained in (c). This value is between OLS and within. Discuss why these values can be interpreted as lower and upper bounds.

**Solution**

**Bounds interpretation:**
*   **OLS (upper bound):** Suffers from upward bias because $c_i$ is omitted and positively correlated with both $n_{it-1}$ and $n_{it}$. This creates spurious positive correlation.
*   **Within/FE (lower bound):** Suffers from Nickell bias, creating downward bias through mechanical negative correlation in the within transformation.
*   **True $\rho$:** Should lie between these biased estimates.

The AB estimate $\hat{\rho}_{AB} \approx 0.817$ falls in the expected range (0.734, 1.037), providing confidence in the estimator's validity. If AB were outside this range, it would suggest specification problems or invalid instruments.

**Theoretical justification:**
$plim(\hat{\rho}_{FE}) < \rho < plim(\hat{\rho}_{OLS})$
This holds under standard assumptions when the true data-generating process includes persistent unobserved heterogeneity.