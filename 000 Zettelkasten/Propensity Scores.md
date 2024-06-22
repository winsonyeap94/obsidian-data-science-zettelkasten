---
tags:
  - causal-analytics
creation-date: 2024-04-30 21:27
---
# Propensity Scores

$$
\text{Propensity Score} : p(T = 1 \mid x)
$$
Propensity scores are used in-conjunction with [[Covariate Adjustment]] as a tool to improve the accuracy of the [[Average Treatment Effect (ATE)]] estimation.

The basic idea of propensity scores is to **turn an observational study into a pseudo-randomised trial by re-weighting samples**.
- Samples are re-weighted by the *inverse propensity score* of the treatment they received.

![[Pasted image 20240430212945.png]]


## Algorithm

1. Use any machine learning method to estimate $\hat{p}(T = t \mid x)$.
2. $$\hat{ATE}=\frac{1}{n}\sum_{i\ s.t.\ t_i = 1}\frac{y_i}{\hat{p}(t_i=1\mid x)} - \frac{1}{n}\sum_{i\ s.t.\ t_i=0}\frac{y_i}{\hat{p}(t_i=0\mid x)}$$
## Problems with inverse propensity weighting (IPW)

1. Need to estimate the propensity score (this is a problem in all propensity score methods).
2. If there's not much *overlap*, propensity scores become non-informative and easily mis-calibrated.
3. Weighting by inverse can create large variance and *large errors for small propensity scores*.
	1. Exacerbated when more than two treatments.



---
# References

- [[MIT 6.S897 Machine Learning for Healthcare, Spring 2019#Causal Inference, Part 2]]
- [[Casual Inference by Brady Neal#Chapter 5 & 6 Randomised Experiments; Frontdoor Adjustment; *do*-calculus; Graph-Based Identification]]