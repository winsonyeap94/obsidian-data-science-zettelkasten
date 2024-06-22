---
tags:
  - probability
  - causal-analytics
creation-date: 2024-04-28 20:56
---
# Independent Events

We define independent events two ways:

1. **Logical independence**
	- For instance, two events occur but there is no reason to believe that the two events affect each other.
	- When it is assumed that they *do* affect each other, this is a logical fallacy called *post hoc ergo propter hoc*, which is Latin for "after this, there fore because of this". This fallacy recognises that the temporal ordering of events is not sufficient to be able to say that the first thing caused the second.

2. **Statistical independence**
	- Two events, $A$ and $B$, are independent if and only if:
		$\text{Pr}(A\ |\ B) = \text{Pr}(A)$
	- Example: Sampling with replacement, Rolling of dice


## Joint Probabilities

For independent events, to calculate joint probabilities, we multiply the marginal probabilities:
$$
\text{Pr}(A, B) = \text{Pr}(A) \ \text{Pr}(B)
$$




---
# References
[[Casual Inference - The Mixtape#Chapter 2 - Probability and Regression Review]]