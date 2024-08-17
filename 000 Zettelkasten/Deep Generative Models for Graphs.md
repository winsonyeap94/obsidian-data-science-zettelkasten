---
tags:
  - graphs
  - synthetic-data-generation
creation-date: 2024-07-07 21:48
---
## Deep Generative Models for Graphs

![[Pasted image 20240708222203.png]]

The purpose of graph generative models is to help generate realistic graphs, which have a broad range of applications:
1. **Insights** - We can understand the formulation of graphs.
2. **Predictions** - We can predict how the graph will further evolve.
3. **Simulations** - We can use the same process to general novel graph instances.
4. **Anomaly detection** - We can decide if a graph is normal / abnormal.

### Graph Generation Tasks

There are two main categories of graph generation tasks:
1. Task 1: **Realistic graph generation**
	- Generate graphs that are similar to a given set of graphs.
2. Task 2: **Goal-directed graph generation**
	- Generate graphs that optimise given objectives / constraints.
	- e.g., drug molecule generation / optimisation.

### Graph Generative Models

![[Pasted image 20240708222708.png]]

Given a set of graphs sampled from $p_{data}(G)$, the goal of graph generative models is to *learn the distribution $p_{model}(G)$ and then to sample from the distribution itself*.

Setup:
- Assume that we want to learn a generative model from a set of data points (i.e., graphs) $\{x_i\}$.
	- $p_{data}(x)$ is the *data distribution*, which is never known to us, but we have sampled $x_i \sim p_{data}(x)$.
	- $p_{model}(x; \theta)$ is the *model*, parameterised by $\theta$, that we use to approximate $p_{data}(x)$.

Goal:
1. Make $p_{model}(x; \theta)$ close to $p_{data}(x)$ **(Density estimation)**
2. Make sure we can sample from $p_{model}(x;\theta)$ **(Sampling)**
	- We need to generate examples (graphs) from $p_{model}(x;\theta)$.

#### (1) Density Estimation

Key Principle: **Maximum Likelihood** - The fundamental approach to modelling distributions.
$$
\theta^* = \underset{\theta}{\arg\max}\  \mathbb{E}_{x\sim p_{data}} \log p_{model}(x\mid\theta)
$$
We want to find parameters $\theta^*$, such that for observed data points $x_i\sim p_{data}$ maximises $\sum_i \log p_{model}(x_i; \theta^*)$ among all possible choices of $\theta$.

#### (2) Sampling

Goal: To sample from a complex distribution.

The most common approach:
1. Sample from a simple noise distribution
   $z_i \sim N(0, 1)$
2. **Transform the noise** $z_i$ via $f(\cdot)$
   $x_i = f(z_i ; \theta)$
   Then, $x_i$ follows a complex distribution.

$f(\cdot)$ is often designed as a *deep neural network*.

---
## Deep Generative Models

The types of deep generative models are often **auto-regressive models**.
- $p_{model}(x; \theta)$ is used for *both* density estimation and sampling.
	- Other models such as Variational Auto Encoders (VAEs), Generative Adversarial Nets (GANs) have $\ge 2$ models, each playing one of the roles.
- Idea: **Chain Rule** $\rightarrow$ Joint distribution is a product of conditional distributions.$$p_{model}(x; \theta) = \prod^n_{t=1}p_{model}(x_t\mid x_1, ..., x_{t-1}; \theta)$$
	- $x_t$ is the $t$-th action (add node, add edge).

### Algorithms

- [[GraphRNN]]
- [[Graph Convolutional Policy Network (GCPN)]]

---
## Scaling Up and Evaluating Graph Generation

### Issue: Tractability

An issue faced when scaling-up graph generation is **tractability**.
- *Any node can connect to any prior node*, and this means that we end up with too many steps for edge generation.
	- Need to generate full adjacency matrix.
	- Complex, too-long edge dependencies.

![[Pasted image 20240711215804.png]]

The solution is to apply **Breadth-First Search (BFS) node ordering**.
- Benefits:
	1. Reduce possible node orderings $\rightarrow$ From $O(n!)$ to number of distinct BFS orderings.
	2. Reduce steps for edge generation $\rightarrow$ Reduce number of previous nodes to look at.

![[Pasted image 20240711215842.png]]
![[Pasted image 20240711215940.png]]

### Evaluating Generated Graphs

![[Pasted image 20240711220014.png]]

![[Pasted image 20240711220020.png]]
![[Pasted image 20240711220030.png]]

---
## Application - Drug Discovery

Question: Can we learn a model that can generate *valid* and *realistic* molecules with **optimised property scores**?
![[Pasted image 20240711220040.png]]

This is a goal-directed graph generation problem, where we want to generate graphs that:
1. **Optimise a given objective** (High scores)
	- e.g., drug-likeness
2. **Obey underlying rules** (Valid)
	- e.g., chemical validity rules
3. **Are learned from examples** (Realistic)
	- Imitating a molecule graph dataset



---
## References

- [[CS224W - Machine Learning with Graphs#Chapter 12 Deep generative models for graphs]]