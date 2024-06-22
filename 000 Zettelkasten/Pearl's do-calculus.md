---
tags:
  - causal-analytics
creation-date: 2024-05-13 22:52
---
# Pearl's do-calculus

do-calculus gives us tools to **identify any causal quantity that is identifiable**.
$$
P(Y \mid do(T=t), X=x)
$$
where $Y$, $T$, and $X$ are arbitrary sets.

## Theorem - Rules of do-calculus

Given a causal graph $G$, an associated distribution $P$, and disjoint sets of variables $Y$, $T$, $Z$, and $W$, the following rules hold.

Rule 1:
$$
P(y \mid do(t), z, w) = P(y \mid do(t), w) \ \ \ if \ Y \perp\kern-5pt\perp{G_{\bar{T}}} Z\mid T,W
$$
Rule 2:
$$
P(y \mid do(t), do(z), w) = P(y \mid do(t), z, w) \ \ \ if \ Y \perp\kern-5pt\perp{G_{\bar{T},\underline{Z}}} Z\mid T,W
$$
Rule 3:
$$
P(y \mid do(t), do(z), w) = P(y \mid do(t), w) \ \ \ if \ Y \perp\kern-5pt\perp{G_{\bar{T},\bar{Z(W)}}} Z\mid T,W
$$
where $Z(W)$ denotes the set of nodes of $Z$ that aren't ancestors of any node of $W$ in $G_{\bar{T}}$.

---
# References

- [[Casual Inference by Brady Neal#Chapter 5 & 6 Randomised Experiments; Frontdoor Adjustment; *do*-calculus; Graph-Based Identification]]
