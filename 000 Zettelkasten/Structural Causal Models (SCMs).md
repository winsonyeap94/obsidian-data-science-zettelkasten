---
tags:
  - causal-analytics
creation-date: 2024-05-12 23:15
---
# Structural Causal Models (SCMs)

SCMs are a <mark style="background: #FFF3A3A6;">framework for representing and reasoning about causal relationships</mark> in complex systems.

An SCM consists of two main components:
1. A DAG representing the causal relationships between variables, with nodes denoting variables and edges denoting direct causal influences.
2. A set of structural equations that specify how each variable is functionally determined by its direct causes and an exogenous nose term.


![[Pasted image 20240512231812.png]]

- $U$ is some *unobserved random variable*. 
	- This is analogous to the *randomness* that we would see by sampling units (individuals); it denotes all the relevant (noisy) background conditions that determine $B$.
	- In causal graphs, the noise variables are often implicit, rather than explicitly drawn.

![[Pasted image 20240512231847.png]]
![[Pasted image 20240512231915.png]]

- **Endogenous variables** - The variables whose causal mechanisms we are modelling; the variables which have parents in the causal graph.
- **Exogenous variables** - Variables who do not have any parents in the causal graph; these variables are *external* to our model in a sense that we do not choose to model their causes.

If the causal graph contains no cycles (i.e., is a DAG), and the noise variables $U$ are independent, then the causal model is *Markovian*; the distribution $P$ is Markov with respect to the causal graph.
- If the causal graph doesn't contain cycles but the noise terms are *dependent*, then the model is *semi-Markovian*.
- The graphs of *non-Markovian* models contain cycles.


---
# References

- [[Casual Inference by Brady Neal#Chapter 4 Backdoor Adjustment; Structural Causal Models]]