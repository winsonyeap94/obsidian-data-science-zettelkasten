---
tags:
  - causal-analytics
creation-date: 2024-05-15 22:07
---
# Conditional Average Treatment Effect (CATE)

The key difference between CATE and [[Average Treatment Effect (ATE)]] is that CATE is conditioned on $x$.
$$
\tau(x) \triangleq \mathbb{E}[Y(1) - Y(0) \mid X=x]
$$

Normally, we always assume *unconfoundedness* and *positivity*.
- Given $W$ is a sufficient adjustment set,$$\tau(x) \triangleq \mathbb{E}[Y(1) - Y(0)] = \mathbb{E}_W[ \mathbb{E}[Y \mid T=1, W] - \mathbb{E}[Y \mid T = 0, W] ]$$
- Given $W \cup X$ is a sufficient adjustment set,$$\tau(x) \triangleq \mathbb{E}[Y(1)-Y(0) \mid X=x] = \mathbb{E}_W[ \mathbb{E}[Y \mid T=1, X=x, W] - \mathbb{E}[Y \mid T=0, X=x, W] ]$$
## Techniques of estimating CATE

1. [[Conditional Outcome Modelling (COM)]] (or S-Learners)
2. TARNet
3. [[X-Learner]]
4. Doubly robust methods
5. Matching
6. [[Double Machine Learning]]
7. Causal trees and forests

---
# References

- [[Casual Inference by Brady Neal#Chapter 5 & 6 Randomised Experiments; Frontdoor Adjustment; *do*-calculus; Graph-Based Identification]]