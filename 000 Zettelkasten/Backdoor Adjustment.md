---
tags:
  - causal-analytics
creation-date: 2024-05-05 17:31
---
# Backdoor Adjustment

Backdoor adjustment is the process of **adjusting or controlling for confounders**.

It is used as a way to perform causality analysis on [[Observational Studies]], instead of [[Randomised Control Trials (RCT)]].

If $W$ is a sufficient adjustment set, we have:
$$
\begin{align}
\mathbb{E}[Y(t) \mid W=w] & = \mathbb{E}[Y \mid \text{do}(T=t), W=w] \\
& = \mathbb{E}[Y \mid t, w]
\end{align}
$$
![[Pasted image 20240505174421.png]]

## Example

Let's take an example where we are comparing the effects of two treatments on a disease.

![[Pasted image 20240505174518.png]]

Notice that how with backdoor adjustment, <mark style="background: #FFF3A3A6;">the weights are the same between both treatment effects</mark>. Below is how the adjusted calculation compares against the naïve example where no adjustments are made.

![[Pasted image 20240505174646.png]]


## Blocking Backdoor Paths

![[Pasted image 20240512230955.png]]

### Backdoor criterion and backdoor adjustment

A set of variables $W$ satisfies the backdoor criterion relative to $T$ and $Y$ if the following are true:
1. $W$ blocks all backdoor paths from $T$ to $Y$.
2. $W$ does not contain any descendants of $T$.

Given the modularity assumption and that $W$ satisfies the backdoor criterion, we can identify the causal effect of $T$ on $Y$:
$$
P(y \mid do(t)) = \sum_w P(y \mid t,w) P(w)
$$




---
# References

- [[Casual Inference by Brady Neal#Chapter 1 Motivation - Why You Might Care]]
- [[Casual Inference by Brady Neal#Chapter 4 Backdoor Adjustment; Structural Causal Models]]
