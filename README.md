# 🎵 Harmony Search Algorithm
### Planning Techniques for Robotics — Faculty of Computer Science and Artificial Intelligence

> A comprehensive study of the Harmony Search (HS) metaheuristic optimization algorithm: theory, implementation, variants, and applications in robotics and engineering.

---

## 📚 Table of Contents

- [Overview](#overview)
- [Background & Motivation](#background--motivation)
- [Algorithm](#algorithm)
  - [Key Parameters](#key-parameters)
  - [How It Works](#how-it-works)
  - [Pseudocode](#pseudocode)
  - [Exploration vs. Exploitation](#exploration-vs-exploitation)
- [Mathematical Formulation](#mathematical-formulation)
- [Improved Variants](#improved-variants)
- [Applications](#applications)
- [Numerical Example](#numerical-example)
- [Comparison with Other Metaheuristics](#comparison-with-other-metaheuristics)
- [Advantages & Limitations](#advantages--limitations)
- [References](#references)

---

## Overview

The **Harmony Search (HS)** algorithm is a nature-inspired metaheuristic optimization technique introduced by **Geem, Kim, and Loganathan in 2001**. It is modeled after the musical improvisation process, where musicians iteratively adjust the pitch of each instrument to achieve a perfect state of harmony.

**Key highlights:**
- Simple concept with few adjustable parameters
- Gradient-free — works on discontinuous, noisy, or multimodal objective functions
- Balances exploration and exploitation naturally
- Widely applied across engineering, robotics, scheduling, and machine learning

---

## Background & Motivation

In music, each musician stores possible pitches in memory and selects one at each step via:
1. **Memory consideration** — recall a previously played note
2. **Pitch adjustment** — slightly tweak a remembered note
3. **Random selection** — choose a completely new note

This same process, mapped to optimization, allows the algorithm to explore the search space efficiently and converge toward optimal or near-optimal solutions.

---

## Algorithm

### Key Parameters

| Parameter | Symbol | Typical Range | Role |
|-----------|--------|---------------|------|
| Harmony Memory Size | HMS | 5 – 50 | Number of candidate solutions stored |
| Harmony Memory Considering Rate | HMCR | 0.7 – 0.99 | Probability of picking from memory vs. random |
| Pitch Adjusting Rate | PAR | 0.1 – 0.5 | Probability of fine-tuning a selected value |
| Bandwidth | BW | problem-specific | Range of pitch adjustment `[-BW, +BW]` |
| Number of Decision Variables | N | problem-specific | Dimensionality of the problem |
| Max Improvisations | NI | problem-specific | Stopping criterion (total iterations) |

---

### How It Works

**Step 1 — Initialize Harmony Memory**

Generate `HMS` random solutions within variable bounds:

```
x_i^j = L_j + rand(0,1) × (U_j - L_j)
```

where `i = 1..HMS`, `j = 1..d`, and `L_j`, `U_j` are the lower and upper bounds.

**Step 2 — Improvise a New Harmony**

For each decision variable `j`:

- **Source Selection:** If `rand ≤ HMCR` → pick a value from Harmony Memory; else → generate randomly within bounds.
- **Pitch Adjustment:** If memory was used and `rand ≤ PAR` → adjust by `x_j ← x_j + rand(-BW, +BW)`.
- **Boundary Clipping:** `x_j^new = max(L_j, min(U_j, x_j^new))`

**Step 3 — Update Harmony Memory**

If the new solution is better than the worst solution in HM:

```
HM ← HM \ {x_worst} ∪ {x_new}
```

**Step 4 — Repeat** until the maximum number of improvisations is reached.

---

### Pseudocode

```
BEGIN
  Define objective function f(x), x = (x1, x2, ..., xd)^T
  Generate initial Harmony Memory (HMS random solutions)
  Define HMCR, PAR, BW, and max iterations

  WHILE t < MaxIterations DO
    FOR each decision variable j DO
      IF rand ≤ HMCR THEN
        x_j^new ← value from Harmony Memory
        IF rand ≤ PAR THEN
          x_j^new ← x_j^new ± rand × BW   // pitch adjustment
        END IF
      ELSE
        x_j^new ← random value in [L_j, U_j] // random selection
      END IF
    END FOR

    IF f(x_new) < f(x_worst) THEN
      Replace x_worst with x_new in HM
    END IF

    t ← t + 1
  END WHILE

  RETURN best solution in HM
END
```

---

### Exploration vs. Exploitation

Harmony Search balances global search and local refinement through three operators:

| Operator | Role | Controlled By |
|----------|------|---------------|
| Random Selection | Exploration — discovers new regions | Low HMCR |
| Memory Consideration | Exploitation — leverages known solutions | High HMCR |
| Pitch Adjustment | Local search — refines promising solutions | PAR, BW |

**Parameter effects summary:**

| Parameter | ↑ Effect | ↓ Effect |
|-----------|----------|----------|
| HMCR ↑ | More exploitation | Less exploration |
| HMCR ↓ | More exploration | Less exploitation |
| PAR ↑ | More local search | — |
| BW ↑ | More exploration | Less exploitation |
| BW ↓ | More exploitation | Less exploration |

---

## Mathematical Formulation

**Harmony Memory Matrix:**

```
     [ x1^1  x2^1  ...  xd^1  | f(x^1)   ]
HM = [ x1^2  x2^2  ...  xd^2  | f(x^2)   ]
     [  ...   ...  ...   ...   |  ...      ]
     [ x1^HMS x2^HMS ... xd^HMS | f(x^HMS) ]
```

**Complete Probability Distribution:**

```
P(x_j^new = v) = HMCR × [(1 - PAR) × 1/HMS + PAR × 1/(2·BW)] + (1 - HMCR) × 1/(U_j - L_j)
```

**Convergence Condition (Markov Chain):**

```
lim_{t→∞} P(x_best^t ∈ S*) = 1

Sufficient condition: 0 < HMCR < 1  and  0 < PAR ≤ 1
```

---

## Improved Variants

| Variant | Key Improvement |
|---------|----------------|
| **IHS** (Improved HS) | Dynamic PAR and BW — improves exploration-exploitation trade-off over time |
| **GHS** (Global-best HS) | Incorporates the global best solution (PSO-inspired) for better guidance |
| **SGHS** (Self-Adaptive Global-best HS) | Automatically adapts parameters based on feedback during optimization |
| **NGHS** (Novel Global HS) | Simplified scheme with added mutation operators |

**IHS Dynamic Parameter Formulas:**

```
bw(t)  = bw_max × exp( ln(bw_min / bw_max) / t_max × t )

PAR(t) = PAR_min + (PAR_max - PAR_min) / t_max × t
```

---

## Applications

**Engineering Design**
Optimal design of truss structures, pipe networks, and mechanical components — minimizing material usage while satisfying structural constraints.

**Robotics & Path Planning**
Finding collision-free, energy-efficient paths for autonomous robots. Optimizing joint angles, trajectories, and motion sequences. Hybrid HSA + Artificial Potential Field (APF) methods address local minima traps.

**Power Systems**
Economic load dispatch, optimal power flow, scheduling of renewable energy sources.

**Water Resources**
Optimization of water distribution networks, reservoir operation, and irrigation scheduling.

**Scheduling**
Job-shop scheduling (JSP), flexible job-shop scheduling (FJSP), vehicle routing, and task allocation.

**Machine Learning**
Feature selection, hyperparameter optimization, and neural network training.

---

## Numerical Example

### Feature Selection Problem

Given a dataset with 4 features `F = {F1, F2, F3, F4}`, each candidate solution is a binary vector:

```
X = [x1, x2, x3, x4],  xi ∈ {0, 1}
```

**Objective function:**

```
Fitness = Accuracy - α × (Nf / 4)
```
where `Nf` = number of selected features, `α = 0.1`.

**Initial Harmony Memory:**

| Solution | Selected Features | Accuracy | Fitness |
|----------|-----------------|----------|---------|
| [0 1 0 1] | 2 | 0.85 | 0.800 |
| [0 1 1 1] | 3 | 0.90 | 0.825 |
| [1 1 1 0] | 3 | 0.88 | 0.805 |
| [1 0 0 1] | 2 | 0.80 | 0.750 |

**New harmony improvised:** `[1 1 0 0]` → after pitch adjustment → `[1 1 1 0]`

**Fitness of new solution:**
```
Fitness = 0.91 - 0.1 × (3/4) = 0.835
```

Since `0.835 > 0.750`, the worst solution is replaced. After convergence:

```
X* = [1 1 1 0]   →  High accuracy with reduced feature count
```

---

## Comparison with Other Metaheuristics

| Metric | Harmony Search | Genetic Algorithm | PSO | ACO |
|--------|---------------|-------------------|-----|-----|
| Inspiration | Musical improvisation | Natural selection | Swarm behavior | Ant foraging |
| Convergence Speed | Moderate | Moderate | Fast | Slow–Moderate |
| Solution Quality | High | High | Medium–High | High (discrete) |
| Computational Complexity | Low | High | Low | High |
| Robustness | High | Medium | Medium | Medium |
| Exploration vs. Exploitation | **Balanced** | Exploration-focused | Exploitation-focused | Balanced |

**Key takeaways:**
- HS is simpler to implement than GA (no chromosome encoding/decoding, fewer operators)
- PSO converges faster in continuous problems but is more prone to local optima
- HS maintains better diversity than PSO through randomization and pitch adjustment
- ACO excels at discrete routing problems; HS is more general-purpose

---

## Advantages & Limitations

**Advantages**
- Simple concept and easy to implement
- Few parameters to tune
- Handles continuous and combinatorial problems
- Gradient-free — applicable to non-differentiable, noisy, or multimodal functions
- Naturally balances exploration and exploitation
- Adaptable across diverse application domains

**Limitations**
- Sensitive to parameter settings (HMCR, PAR, BW)
- Slow convergence on high-dimensional problems
- May get trapped in local optima without adaptive variants

---

## References

1. Z. W. Geem, J. H. Kim, and G. V. Loganathan, "A New Heuristic Optimization Algorithm: Harmony Search," *Simulation*, 2001.
2. X. Z. Gao et al., "Harmony Search Method: Theory and Applications," *Computational Intelligence and Neuroscience*, vol. 2015, pp. 1–10, 2015. https://doi.org/10.1155/2015/258491
3. F. Qin, A. M. Zain, and K.-Q. Zhou, "Harmony Search Algorithm and Related Variants: A Systematic Review," *Swarm and Evolutionary Computation*, vol. 74, p. 101126, Oct. 2022. https://doi.org/10.1016/j.swevo.2022.101126
4. LANL pyHarmonySearch — Python implementation. https://github.com/lanl/pyHarmonySearch
5. Algorithm Afternoon — Harmony Search overview. https://algorithmafternoon.com/physical/harmony_search/

---

*Faculty of Computer Science and Artificial Intelligence — Planning Techniques for Robotics*
