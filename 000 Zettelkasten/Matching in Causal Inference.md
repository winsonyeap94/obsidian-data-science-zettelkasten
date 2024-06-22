---
tags:
  - causal-analytics
  - nearest-neighbours
creation-date: 2024-04-30 21:16
---
# Matching in Causal Inference

Matching is another technique that can be used to estimate the [[Average Treatment Effect (ATE)]] during causal inference.
- The idea is to find each unit's long-lost counterfactual identical twin, then check up on his outcome.
- For example, we can match each record to the *nearest neighbour from the opposite group*.

![[Pasted image 20240430211835.png]]

## 1-NN Matching

Steps:
1. Let $d(\cdot , \cdot)$ be a distance metric between $x$'s.
2. For each $i$, define $j(i) = \text{argmin}_{j\ s.t.\ t_j \ne t_i} d(x_j, x_i)$ , where $j(i)$ is the nearest counterfactual neighbour of $i$.
3. If $t_i = 1$, then unit $i$ is treated: $$\hat{CATE}(x_i) = y_i - y_{j(i)}$$
4. If $t_i = 0$, then unit $i$ is control: $$\hat{CATE}(x_i) = y_{j(i)} - y_{i}$$
5. Based on the above, we can then derive:
$$
\begin{equation}
\begin{split}
\hat{CATE}(x_i) = (2t_i - 1)(y_i - y_{j(i)}) \\
ATE = \frac{1}{n} \sum^n_{i=1} \hat{CATE}(x_i)
\end{split}
\end{equation}
$$

## Advantages

1. Interpretable, especially in small-sample regime. 
2. Non-parametric

## Disadvantages

1. Heavily reliant on the underlying distance metric.
2. Could be misled by features which don't affect the outcome.


---
# References

[[MIT 6.S897 Machine Learning for Healthcare, Spring 2019#Causal Inference, Part 2]]
