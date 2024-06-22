---
tags:
  - "#probability"
  - causal-analytics
creation-date: 2024-04-28 21:32
---
# Variance

Consider the variance of a random variable, $W$:
$$
V(W) = \sigma^2 = E[(W - E(W))^2] \text{ in the population}
$$
$$
V(W) = E(W^2) - E(W)^2
$$

In a given sample of data, we can estimate the variance by the following calculation:
$$
\hat{S}^2 = (n-1)^{-1} \sum^n_{i=1}(x_i - \bar{x})^2
$$
where we divide by $n -1$ because we are making a *degree-of-freedom adjustment from estimating the mean*.<mark style="background: #FFF3A3A6;"> But in large samples, this degree-of-freedom adjustment has no practical effect</mark> on the value of $S^2$, where $S^2$ is the average (after a degree-of-freedom correction) over the sum of all squared deviations from the mean.

## Properties of Variance

1. The variance of a line is:
$$
V(aX + b) = a^2\ V(X)
$$
2. Variance of a constant is $0$.
3. Variance of two random variables is equal to:
$$
V(X + Y) = V(X) + V(Y) + 2(E(XY) - E(X)E(Y))
$$
4. If the two variables are independent, then:
$$
E(XY) = E(X)E(Y) \text{ and } V(X+Y) = V(X) + V(Y)
$$
5. If the two variables are dependent, then we have to take [[Covariance]] into account.

---
# References

[[Casual Inference - The Mixtape#Chapter 2 - Probability and Regression Review]]