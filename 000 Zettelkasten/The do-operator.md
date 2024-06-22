---
tags:
  - causal-analytics
creation-date: 2024-05-12 22:55
---
# The do-operator

The do-operator, denoted as $do(T=t)$, is a fundamental concept in causal inference and represents an **intervention or external manipulation of a treatment** $T$ to a specific value $t$. It allows us to reason about the effects of such interventions on other variables in a causal system. 

## Notation and Terminology

Interventional distributions:
$$
P(Y(t)=y) \triangleq P(Y=y \mid do(T=t)) \triangleq P(y \mid do(t))
$$

## Conditioning vs Intervening

![[Pasted image 20240512230022.png]]

In causal analytics, there is a difference between conditioning and intervening. 
- Conditioning on $T=t$ -- Only means we are restricting our focus to the *subset of the population* to those who received treatment $t$.
- Intervention -- Take the *whole population* and give everyone treatment $t$. 

Interventions are represented using the *do*-operator $do(T=t)$.

With this, the [[Average Treatment Effect (ATE)]] is then represented as:
$$
ATE = \mathbb{E}[Y \mid do(T=1)] - \mathbb{E}[Y \mid do(T=0)]
$$



---
# References

- [[Casual Inference by Brady Neal#Chapter 4 Backdoor Adjustment; Structural Causal Models]]
