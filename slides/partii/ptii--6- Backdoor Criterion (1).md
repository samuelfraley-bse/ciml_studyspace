---
cost: 0.00194
course: General
difficulty: 5
est_minutes: 30
summary: ''
tags: []
type: Notes
---

```json
{
  "summary": "This lecture introduces the Backdoor Criterion for causal inference, detailing graph elements, d-separation, and its application in identifying causal effects and selecting appropriate control variables, including its use in linear models.",
  "topics": [
    "Causal Inference",
    "Backdoor Criterion",
    "d-separation",
    "Directed Acyclic Graphs (DAGs)",
    "Confounding"
  ],
  "doc_type": "Lecture",
  "difficulty": 7,
  "study_time": 60
}
```
---

# Backdoor Criterion

Master in Data Science Upf - CI & ML - Part I

---

# What about large graphs?

![Causal Graph Example](https://f000.backblazeb2.com/file/prompt-assets/What_about_large_graphs_graph.png)

---

# Graph Elements

---

# Conditioning: a reminder

In data, conditioning Y| X=x
means selecting the cases X=x

| X | Y      |
|---|--------|
| 5 | 0.0056 |
| 7 | -2.001 |
| X | 1.8    |
| ... | ...    |
| 8 | 0.003  |
| X | 0.9    |
| ... | ...    |
<br>
<br>
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Arrow](https://f000.backblazeb2.com/file/prompt-assets/Arrow.png)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
| X | Y   |
|---|-----|
| X | 1.8 |
| X | 0.9 |
| X | ... |

In model
X:=εx
Y:=f(X, εy)

Conditioning means
Y| X=x:=f(x, εy)

---

# Chains

P(X, Y, Z) = P(Z|Y) P(Y|X) P(X)

In general:
*   P(Z|Y)≠P(Z)
*   P(Y|X)≠P(Y)
*   P(Z|X)≠P(Z)

Always:
*   P(Z|X, Y)=P(Z|Y)

![Chain Graph](https://f000.backblazeb2.com/file/prompt-assets/Chains_Graph.png)

---

# Fork

P(X, Y, Z) = P(Z|X) P(Y|X) P(X)

In general:
*   P(X|Y)≠P(X)
*   P(X|Z)≠P(X)
*   P(Z|Y)≠P(Z)

Always:
*   P(Z|Y, X)=P(Z|X)

![Fork Graph](https://f000.backblazeb2.com/file/prompt-assets/Fork_Graph.png)

---

# Colliders

P(X, Y, Z) = P(Z| X, Y) P(Y) P(X)

In general:
*   P(X|Z)≠P(X)
*   P(Y|Z)≠P(Y)
*   P(X|Y,Z)≠P(X|Z)

Always:
*   P(X|Y)=P(X)

![Colliders Graph](https://f000.backblazeb2.com/file/prompt-assets/Colliders_Graph.png)

---

# Exercise

List all pairs of variables that are
independent conditional on {R, P}

![Exercise Graph 1](https://f000.backblazeb2.com/file/prompt-assets/Exercise_Graph_1.png)

---

# Exercise

List all pairs of variables that are
independent conditional on {R, V}

![Exercise Graph 2](https://f000.backblazeb2.com/file/prompt-assets/Exercise_Graph_2.png)

---

# d - separation

---

# Conditional Independencies Example

*   Pick number Z and two small perturbations δ₁, δ₂
*   Take X₁:= Z + δ₁ and X₂:= Z + δ₂
*   X₁ and X₂ are highly dependent
*   But known Z, they are independent

![Conditional Independencies Example Diagram](https://f000.backblazeb2.com/file/prompt-assets/Conditional_Independencies_Example_Diagram.png)

X₁ and X₂ are conditionally independent on Z: X₁ ⊥ X₂ | Z

---

# d-separation

d - separation is a way to describe independencies and conditional
independencies just from the graph

---

# Possible (undirected) paths

Between X and Y can only happen

*   X → ... → Y
*   X → ... → Z ← ... ← Y
*   X ← ... ← Z → ... → Y

---

# Blocking & d-separation

An undirected path p between X and Y is blocked by a set of variables
Z if one of the following happens

*   Contains A→B→C or A←B→C with B in Z
*   Any collider A→B←C (or descendants of B) are not in Z

Z d-separate X and Y if blocks any path p from X to Y

---

# Example

![d-separation Example Graph](https://f000.backblazeb2.com/file/prompt-assets/d-separation_Example_Graph.png)

Between Y and Z
*   ∅ d-separates Y and Z
*   U d-connects Y and Z

---

# Exercise

Find all sets that
d-separate Y and Z

![Exercise Graph 3](https://f000.backblazeb2.com/file/prompt-assets/Exercise_Graph_3.png)

---

# Exercise

Find all sets that
d-separate T and R

![Exercise Graph 4](https://f000.backblazeb2.com/file/prompt-assets/Exercise_Graph_4.png)

---

# D-separation & independence

**Theorem:**
If Z d-separates X from Y, then for any distribution P compatible with
the graph,

X ⊥ Y | Z

---

# Backdoor criterion

---

# Adjustment Formula

Pdo(X:=x)(y) = Σz P(y|z, x) p(z)

![Adjustment Formula Graph](https://f000.backblazeb2.com/file/prompt-assets/Adjustment_Formula_Graph.png)

---

# What to do?

![What to do Graph](https://f000.backblazeb2.com/file/prompt-assets/What_to_do_Graph.png)

---

# In general, when can we use adjustment formula?

![Adjustment Formula Question Graph](https://f000.backblazeb2.com/file/prompt-assets/Adjustment_Formula_Question_Graph.png)

---

# Backdoor criterion

Denote G\X the graph without arrows leaving X. If a set of
variables Z
*   Z d-separates X and Y in G\X
*   Contains no descendant of X

Then, we can identify Pdo(X:=x)(y), and it can be calculated

Pdo(X:=x)(y) = Σz P(y|z, x) p(z)

---

# In general, when can we use adjustment formula?

![Adjustment Formula Question Graph 2](https://f000.backblazeb2.com/file/prompt-assets/Adjustment_Formula_Question_Graph_2.png)

---

# Exercise

Find admissible variables
for applying backdoor
criterion

![Exercise Graph 5](https://f000.backblazeb2.com/file/prompt-assets/Exercise_Graph_5.png)

---

# Backdoor conclusions

*   We can analyze our problem even including variables without collected data!
*   We can analyze our problem before having data at hand!
*   We can determine which variables should we need to make causal inferences

---

# Exercise

Find all admissible controls

![Exercise Graph 6](https://f000.backblazeb2.com/file/prompt-assets/Exercise_Graph_6.png)

---

# Exercise

Good, bad or neutral?

![Control Variable Exercise](https://f000.backblazeb2.com/file/prompt-assets/Control_Variable_Exercise.png)

Determine the type of control variable

---

# Good, bad and neutral controls for estimating X -> Y

![Good Bad Neutral Controls](https://f000.backblazeb2.com/file/prompt-assets/Good_Bad_Neutral_Controls.png)

"A Crash Course in Good and Bad Controls" by Cinelli, Forney and Pearl

---

# Software

- Dagitty (http://www.dagitty.net/dags.html#)
- Causal Fusion (https://causalfusion.net/)

---

# Backdoor & Linear Models

---

# Definitions

*   G\X is the graph without leaving arrows from X.
*   G\αXY is the graph with edge X → Y removed
*   βYX.Z denotes the coefficient of X when performing linear regression Y~X+Z

---

# Direct Effect

Let α be the coefficient X→Y. If a set of variables Z
*   Z d-separates G\αXY
*   Contains no descendant of X

Then, the direct effect (αXY) can be calculated as

αXY ~ βYX.Z

---

# Total Effect

If a set of variables Z
*   Z d-separates G\X
*   Contains no descendant of X

Then, the total effect
E[Y|do(X=x0 + 1)] - E[Y|do(X=x0 + 1)]

can be calculated as

Total effect ~ βYX.Z

---

# Example

![Linear Model Example Graph](https://f000.backblazeb2.com/file/prompt-assets/Linear_Model_Example_Graph.png)

*   b=αXY ~ βYX.{Z,M}
*   b + ac ~ βYX.M