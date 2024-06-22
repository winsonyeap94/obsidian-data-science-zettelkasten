---
tags: 
creation-date: 2024-04-28 21:39
---
# Covariance

Covariance measures the **amount of linear dependence between two random variables**. The definition of covariance is:
$$
C(X, Y) = E(XY) - E(X)E(Y)
$$

The combined variance between two dependent variables then become:
$$
V(X+Y) = V(X) + V(Y) + 2C(X, Y)
$$
While it is tempting to say that a zero covariance means that two random variables are unrelated, that is incorrect. They could have a *nonlinear relationship*.


## Covariance between two linear functions

The covariance between two linear functions is:
$$
C(a_1 + b_1 X, a_2 + b_2 Y) = b_1 b_2 C(X, y)
$$
The two constants, $a_1$ and $a_2$, zero out because their mean is themselves and so the difference equals $0$.

## Interpreting magnitude of the covariance

Interpreting the magnitude of the covariance can be tricky. For that, we are better served by looking at [[Correlation]].
$$
\text{Corr}(X, Y) = \text{Cov}(W, Z) = \frac{C(X, Y)}{\sqrt{V(X)V(Y)}}
$$
$$
W = \frac{X - E(X)}{\sqrt{V(X)}}
$$
$$
Z = \frac{Y - E(Y)}{\sqrt{V(Y)}}
$$

---
# References

[[Casual Inference - The Mixtape#Chapter 2 - Probability and Regression Review]]