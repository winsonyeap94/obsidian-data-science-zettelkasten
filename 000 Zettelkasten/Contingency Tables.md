---
tags:
  - "#probability"
  - causal-analytics
creation-date: 2024-04-28 21:17
---
# Contingency Tables

- In statistics, a contingency table (also known as a *cross tabulation* or *crosstab*) is **a type of table in a matrix format that displays the multivariate frequency distribution of the variables**.
- Also sometimes called **two-way tables**.


Example of a two-way contingency table:

|       Event Label       |  Coach is not rehired ($\sim B$)  |     Coach is hired ($B$)     |           Total           |
|:-----------------------:|:---------------------------------:|:----------------------------:|:-------------------------:|
|     ($A$) Bowl game     |   $\text{Pr}(A, \sim B) = 0.1$    |   $\text{Pr}(A, B) = 0.5$    |   $\text{Pr}(A) = 0.6$    |
| ($\sim A$) No bowl game | $\text{Pr}(\sim A, \sim B) = 0.1$ | $\text{Pr}(\sim A, B) = 0.3$ | $\text{Pr}(\sim A) = 0.4$ |
|        **Total**        |     $\text{Pr}(\sim B) = 0.2$     |     $\text{Pr}(B) = 0.8$     |           $1.0$           |

Based on this example, if we want to calculate conditional probability of $B$ given $A$, then it is:
$$
\text{Pr}(B\ |\ A) = \frac{\text{Pr}(A, B)}{\text{Pr}(A)} = \frac{0.5}{0.6} = 0.83
$$

This is often also related to the [[Bayes' Theorem]].


---
# References

[[Casual Inference - The Mixtape#Chapter 2 - Probability and Regression Review]]