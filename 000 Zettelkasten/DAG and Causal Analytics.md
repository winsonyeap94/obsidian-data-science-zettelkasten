---
tags:
  - dag
  - graphs
  - "#causal-analytics"
creation-date: 2024-04-28 22:54
---
# DAG and Causal Analytics

[[Directed Acyclic Graph (DAG)]]s explain causality in terms of *counterfactuals*.
- That is, a causal effect is defined as a comparison between two states of the word -- one state that actually happened when some intervention took on some value and another state that didn't happen (the "counterfactual") under some other intervention.
- Think of DAG as like a graphical representation of chain and causal effects. The causal effects are themselves based on some *underlying, unobserved structured process*, one an economist might call the equilibrium values of a system of behavioural equations, which are themselves nothing more than a _model_ of the world.

Causal effects can happen in two ways:
1. **Direct** : e.g., $D \rightarrow Y$ 
2. **Mediated by a third variable** : e.g., $D \rightarrow X \rightarrow Y$

## A simple DAG

### Scenario 1: Observed Confounder ($X$)

![[Pasted image 20240428230019.png]]

There are two main paths in this example above:
1. **Direct path** from $D$ to $Y$, which represents a causal effect. That path is represented by $D \rightarrow Y$.
2. **Backdoor path**, represented by $D \leftarrow X \rightarrow Y$. This is **not a causal effect**. Rather, it is a process that creates *[[spurious]] correlations* between $D$ and $Y$ that are driven solely by fluctuations in the $X$ random variable.

Just as not controlling for a variable like that in a regression creates omitted variable bias, <mark style="background: #FFF3A3A6;">leaving a backdoor open creates bias</mark>.
- We therefore call $X$ a *confounder* because it jointly determines $D$ and $Y$, and so confounds our ability to discern the effect of $D$ on $Y$ in naive comparisons.
- Think of the backdoor path like this: Sometimes when $D$ takes on different values, $Y$ takes on different values because $𝐷$ causes $𝑌$. But sometimes $𝐷$ and $𝑌$ take on different values because $𝑋$ takes on different values, and that bit of the correlation between $𝐷$ and $𝑌$ is purely [[spurious]]. The existence of two causal pathways is contained within the correlation between $𝐷$ and $𝑌$.

### Scenario 2: Unobserved Confounder ($U$)

Let’s look at a second DAG, which is subtly different from the first. In the previous example, $𝑋$ was observed. We know it was observed because the direct edges from $𝑋$ to $𝐷$ and $𝑌$ were solid lines. But sometimes there exists a confounder that is unobserved, and when there is, we represent its direct edges with **dashed lines**. 

![[Pasted image 20240428230726.png]]

### Realistic Example

 A classical question in labour economics is whether college education increases earnings. According to the Becker human capital model ([Becker 1994](https://mixtape.scunning.com/references#ref-Becker1994)), education increases one’s marginal product, and since workers are paid their marginal product in competitive markets, education also increases their earnings. But college education is not random; it is optimally chosen given an individual’s subjective preferences and resource constraints.

![[Pasted image 20240428231106.png]]

- $D$ : Treatment (college education)
- $Y$ : Outcome of interest (earnings)
- $PE$ : Parental education
- $I$ : Family income
- $B$ : Unobserved background factors (e.g., genetics, family environment, mental ability)

Now that we have a DAG, what do we do? We can list out all direct and indirect paths between $D$ and $Y$. Once we have those, we then have a better sense of where our problems are. So:

1. $D \rightarrow Y$ (the causal effect of education on earnings)
2. $D \leftarrow I \rightarrow Y$ (backdoor path 1)
3. $D \leftarrow PE \rightarrow I \rightarrow Y$ (backdoor path 2)
4. $D \leftarrow B \rightarrow PE \rightarrow I \rightarrow Y$ (backdoor path 3)

## Colliding

![[Pasted image 20240428231613.png]]

Just like last time, there are two ways to get from $𝐷$ to $𝑌$. You can get from $𝐷$ to $𝑌$ using the direct (causal) path, $D \rightarrow Y$, or you can use the backdoor path, $𝐷 \rightarrow X \leftarrow Y$. However, this time the 𝑋 has two arrows pointing to it, not away from it.

When two variables cause a third variable along some path, we call that third variable a “**collider**”. 
- Put differently, $𝑋$ is a collider along this backdoor path because $𝐷$ and the causal effects of $𝑌$ collide at $𝑋$. 
- Colliders are special in part because when they appear along a backdoor path, that backdoor path is *closed* simply because of their presence. Colliders, when they are left alone, always close a specific backdoor path.

## Backdoor Criterion

When <mark style="background: #FFF3A3A6;">all backdoor paths have been closed</mark>, we say that you have come up with a research design that satisfies the **backdoor criterion**. And if you have satisfied the backdoor criterion, then you have in effect isolated some causal effect. 

But let’s formalize this: a set of variables $𝑋$ satisfies the backdoor criterion in a DAG **if and only if $𝑋$ blocks every path between confounders that contain an arrow from $𝐷$ to $𝑌$**. 

Let’s review our original DAG involving parental education, background and earnings.
![[Pasted image 20240428232348.png]]
The minimally sufficient conditioning strategy necessary to achieve the backdoor criterion is the control for $𝐼$, because $𝐼$ appeared as a non-collider along every backdoor path. It might literally be no simpler than to run the following regression:
$$
Y_i = \alpha + \delta D_i + \beta I_i + \epsilon_i
$$
By simply conditioning on $𝐼$, your estimated $\hat{\delta}$ takes on a causal interpretation.




---
# References

[[Casual Inference - The Mixtape#Chapter 3 - Directed Acyclic Graphs]]
