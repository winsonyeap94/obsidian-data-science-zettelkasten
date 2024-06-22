---
tags:
  - causal-analytics
creation-date: 2024-05-13 22:39
---
# Frontdoor Adjustment

## Intuition

![[Pasted image 20240513224618.png]]

The high-level intuition for why we can identify the causal effect of $T$ on $Y$ is as follows:
- A mediator like $M$ is very helpful; we can isolate the association that flows through $M$ by focusing our statistical analysis on $M$, and the only association that flows through $M$ is *causal association*.

We will focus our analysis on $M$ using a three-step procedure:
1. Identify the causal effect of $T$ on $M$.$$P(m \mid do(t)) = P(m \mid t)$$
2. Identify the causal effect of $M$ on $Y$.$$P(y \mid do(m)) = \sum_tP(y \mid m, t) P(t)$$
3. Combine the above steps to identify the causal effect of $T$ on $Y$. $$P(y \mid do(t)) = \sum_mP(m \mid do(t))O(y \mid do(m))$$
## Frontdoor Criterion

Definition: A set of variables $M$ satisfies the frontdoor criterion relative to $T$ and $Y$ if the following are true:
1. $M$ completely mediates the effect of $T$ on $Y$ (i.e., all causal paths from $T$ to $Y$ go through $M$).
2. There is no unblocked backdoor path from $T$ to $M$.
3. All backdoor paths from $M$ to $Y$ are blocked by $T$.



---
# References

- [[Casual Inference by Brady Neal#Chapter 5 & 6 Randomised Experiments; Frontdoor Adjustment; *do*-calculus; Graph-Based Identification]]