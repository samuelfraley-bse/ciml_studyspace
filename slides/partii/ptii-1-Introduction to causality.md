---
cost: 0.00157
course: General
difficulty: 5
est_minutes: 30
summary: ''
tags: []
type: Notes
---

```json
{
  "summary": "This lecture introduces the core concepts of causal inference, differentiating it from correlation, exploring the use and limitations of experimental data (A/B tests/RCTs), and delving into methods for causal inference with observational data and the challenge of confounders.",
  "topics": ["Causal Inference", "A/B Testing", "Observational Data", "Spurious Correlation", "Confounders"],
  "doc_type": "Lecture",
  "difficulty": 5,
  "study_time": 45
}
```
---CONTENT---

# Causal Inference & Machine Learning

## Master in Data Science Upf - CI & ML - Part II

## Aleix Ruiz de Villa

*   Data Scientist and Ph.D. in mathematics
*   Previously: Head of Data Science at LaVanguardia, Lidl - SCRM, and Onna.
*   Currently: Data Science consultant
*   Writing "Causal Inference for Data Science" Manning
*   Founder Barcelona Data Science & Machine Learning Meetup
*   Youtube channel @funwithdags

![Aleix Ruiz de Villa and his book cover](image-2.png)

## About this course - Objectives

![Brain activity levels meme: Passing the exam, Having good grades, Understanding what you are doing, Applying it in real cases](image-3.png)

*   Passing the exam doesn't guarantee that you have digested the information
*   Try to understand the concepts in class

*imgflip.com*

## About this course

**Delivery**
2 in-class graded exercises (15% of total qualification)
Exam (70% of total qualification)

## About this course - Objectives

**Objectives**

*   Learning how to address unobserved confounding by modeling reality with graphs
*   Learning causal ML: how to use machine learning for estimating causal effects

The difficulty of this course is not about "doing" something, but "knowing what to do" on each case

## References

**Causal Inference and AB testing - BcnAnalytics:**
[https://www.youtube.com/watch?v=rQKPrry6g0g](https://www.youtube.com/watch?v=rQKPrry6g0g)

**Introduction to Causal Inference - XEurope:**
[https://www.youtube.com/watch?v=E3E13PDn7K8&t=89s](https://www.youtube.com/watch?v=E3E13PDn7K8&t=89s)

**"Causal Inference for Data Science" A.Ruiz de Villa, Manning 2024 (3 copies at upf library)**
Code: [https://github.com/aleixrvr/CausalInference4DataScience](https://github.com/aleixrvr/CausalInference4DataScience)
Quick questions: [https://livebook.manning.com/book/causal-inference-for-data-science/](https://livebook.manning.com/book/causal-inference-for-data-science/)

**"Causal Inference in Statistics: a Primer" J.Pearl, M.Glymour, N.P. Jewell. Wiley 2016**

**"Elements of Causal Inference" Jonas Peters, Dominik Janzing and Bernhard Schölkopf**
([https://mitp-content-server.mit.edu/books/content/sectbyfn?collid=books_pres_0&id=11283&fn=11283.pdf](https://mitp-content-server.mit.edu/books/content/sectbyfn?collid=books_pres_0&id=11283&fn=11283.pdf))

# Experimental Data

## Is a new webpage better?

Can we say the new webpage has been a success based on this graph?

![Graph showing number of visits for a new vs. old webpage version over time. The old version declines, while the new version, introduced at day 0, shows an upward trend.](image-8.png)

## Is a new webpage better?

Can we say the new webpage has been a success based on this graph?

What other factors may influence its dynamics?

![Graph showing number of visits for a new vs. old webpage version over time. The old version declines, while the new version, introduced at day 0, shows an upward trend.](image-9.png)

## Solution: A/B tests (or Randomized Controlled Trials)

![Diagram illustrating A/B testing with two monitors showing different webpage versions (A and B) to two different groups of users (red and green).](image-10.png)

## Limitations of RCT

1.  Too expensive
2.  Many tests at the same time
3.  Not ethical
4.  Infeasible

## Exercise

How different policies affect covid to different countries?

![Tweet by Adriana Castelli showing a table of countries and their COVID-19 policies (Schools, Restaurants, Sporting events, Mass gatherings, Travel restrictions, Domestic lockdown) with 'X' marking applied policies. UK is noted as the control group.](image-12.png)

If you wanted to do a RCT, how would you do it?

## Successful analysis without experimental data

John Snow - Cholera
19th Century

![Historic map by John Snow showing cholera cases clustered around the Broad Street pump.](image-13.png)

## Question

**Cholera**

How one should perform an RCT in this case?

## References

**Causal Inference and AB testing - BcnAnalytics:**
[https://www.youtube.com/watch?v=rQKPrry6g0g](https://www.youtube.com/watch?v=rQKPrry6g0g)

**Introduction to Causal Inference - XEurope:**
[https://www.youtube.com/watch?v=E3E13PDn7K8&t=89s](https://www.youtube.com/watch?v=E3E13PDn7K8&t=89s)

# Observational Data

![Comic strip: "I used to think correlation implied causation." "Then I took a statistics class. Now I don't." "Sounds like the class helped." "Well, maybe."](image-17.png)

## What does causation mean in this context?

![Scatter plot showing correlation between countries' annual per capita chocolate consumption and the number of Nobel laureates per 10 million population.](image-18.png)

*Figure 1. Correlation between Countries' Annual Per Capita Chocolate Consumption and the Number of Nobel Laureates per 10 Million Population.*

## Spurious correlations: common causes

US spending on science, space, and technology
correlates with
Suicides by hanging, strangulation and suffocation
Correlation: 99.79% (r=0.99789126)

![Graph showing US spending on science and suicides by hanging, strangulation, and suffocation tracking almost identically from 1999 to 2009.](image-19.png)

Data sources: U.S. Office of Management and Budget and Centers for Disease Control & Prevention
*tylervigen.com*

## Spurious correlations

Check out Tyler Vigen's Spurious Correlations
[https://www.tylervigen.com/spurious-correlations](https://www.tylervigen.com/spurious-correlations)

## Does living the countryside makes you live shorter?

![Screenshot of a Vox article titled "Want to live longer, even if you're poor? Then move to a big city in California."](image-21.png)

[https://www.vox.com/2016/4/13/11420230/life-expectancy-income](https://www.vox.com/2016/4/13/11420230/life-expectancy-income)

## Exercise

-   How does the place you live affect your life expectancy?

## Exercise

-   How does the place you live affect your life expectancy?
-   Which other factors affect both at the same time?

## CONFOUNDERS

-   How does the place (P) you live affect your life expectancy (LE)?
-   Which other factors affect both at the same time?

![Diagram showing 'Socio-economic factors' influencing both 'P' (Place) and 'LE' (Life Expectancy). 'P' also directly influences 'LE'.](image-24.png)

# Conclusions

## Conclusions

*   Correlation is not causation
*   The clearest way to measure causation is through A/B testing or RCTs
*   We need causal inference whenever we have observational data

# References

## References

*   "Causal Inference in Statistics: A Primer" Pearl, Glymour and Newell
*   "Causal Inference for Data Science" Ruiz de Villa

**Others**

*   [https://matheusfacure.github.io/python-causality-handbook/landing-page.html](https://matheusfacure.github.io/python-causality-handbook/landing-page.html)
*   "The Effect: An Introduction to Research Design and Causality" Nick Huntington-Klein
*   "Causal Inference: The Mixtape" Scott Cunningham