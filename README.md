# BSE Study Assistant

Course materials + AI study assistant for the BSE master's course in Panel Data, Nonparametric Econometrics, and Causal Inference & ML.

---

## Setup

**1. Clone the repo**
```bash
git clone <repo-url>
cd <repo-name>
```

**2. Open the folder in Claude**

- **Claude Code** (terminal): `claude` inside the folder
- **Cowork** (desktop): open Cowork → select this folder as your workspace

That's it. The assistant loads automatically.

---

## How to use it

Just chat normally. You don't need to use any commands — the assistant detects what you need:

| What you say | What happens |
|---|---|
| "explain propensity scores" | Normal answer, exam-focused |
| "i don't get GMM" / "explain simply" | Plain English, no jargon, real-world analogy |
| "what's the difference between FE and RE" | Side-by-side comparison table |
| "give me bullets on DiD" | Max 8 tight bullets |
| "summarize part one" / "overview of part II" | Full part summary, ~400 words |
| "what did I study last time" | Recap of your previous session |
| "what should I study" / "make me a study plan" | Prioritised plan based on your history |

**Slash commands work too** if you prefer:
`/baby`, `/compare`, `/bullets`, `/summarize`, `/refresh`, `/plan`

---

## Memory

The assistant tracks what you've studied automatically — no setup needed.

- A `study_log.md` file is created locally when you start your first session
- Every question you ask and every command you run is logged silently
- Next time you open the repo, the assistant reads the log and picks up where you left off
- `study_log.md` is gitignored — your study history is yours only, it won't sync to the shared repo

---

## What's in the repo

```
slides/parti/       Part I slides — Panel Data & Nonparametrics (Prof. Mayoral)
slides/partii/      Part II slides — Causal Inference & ML (Prof. Ruiz de Villa)
exams/              Past exam examples
hw/                 Problem sets and solutions
concept_index.md    Full map of every concept → which part → which slide
```
