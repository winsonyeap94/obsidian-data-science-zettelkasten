---
tags:
  - graphs
creation-date: 2024-06-02 22:22
---
# Node Classification

## Overview

![[Pasted image 20240602230131.png]]

Question:
- Given a network with labels on some nodes, how do we assign labels to all other nodes in the network?

Example:
- In a network, some nodes are fraudsters and some other nodes are fully trusted. How do you find the other fraudsters and trustworthy nodes?

This is called **semi-supervised node classification**.
- **Message passing** is an alternative framework that can be used to help solve this problem.

Intuition:
- **Correlations** exist in networks (i.e., similar nodes are connected).
- Key concept is **collective classification** (i.e., idea of assigning labels to all nodes in a network together.)

Techniques:
1. Relational classification
2. Iterative classification
3. Belief propagation

>[!example]- Applications of Node Classification
>1. Document classification
>2. Part-of-speech (POS) tagging
>3. Link prediction
>4. Optical character recognition (OCR)
>5. Image/3D data segmentation
>6. Entity resolution in sensor networks
>7. Spam and fraud detection

---
## Correlations Exist in Networks

- Individual behaviours are correlated in the network.
	- Correlation: Nearby nodes have the *same class*.
- There are two main types of dependencies that lead to correlation:
	1. **Homophily**: Individual characteristics $\rightarrow$ Social connections
	2. **Influence**: Social connections $\rightarrow$ Individual characteristics

### (1) Homophily

Homophily is the tendency of individuals to associate and bond **with similar others**.
- *"Birds of a feather flock together"*
- Example: Researchers who focus on the same research area are more likely to establish a connection (e.g., meeting at conferences, interacting at academic talks, etc.).

![[Pasted image 20240602225935.png]]

### (2) Influence

**Social connections can influence the individual** characteristics of a person.
- Example: I recommend my musical preferences to my friends, until one of them grows to like my same favourite genres.

---
## Collective Classification

- Intuition: Simultaneous classification of interlinked nodes using correlations.
- This is a *probabilistic framework*.
- Applies **Markov Assumption** - The label $Y_v$ of one node $v$ depends on the labels of its neighbours $N_v$.
$$
P(Y_v) = P(Y_v \mid N_v)
$$

Collective classification involves 3 steps:

| No  | Step                                                                 | Description                                                                                                                                                                              |
| --- | -------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | **Local Classifier**<br>- Assign initial labels                      | - Predicts label based on node attributes/features<br>- Standard classification task<br>- Does not use network information                                                               |
| 2   | **Relational Classifier**<br>- Capture correlations between nodes    | - Learns a classifier to label one node based on the labels and/or attributes of its neighbours<br>- This is where network information is used                                           |
| 3   | **Collective Inference**<br>- Propagate correlations through network | - Apply relational classifier to each node iteratively<br>- Iterate until the inconsistency between neighbouring labels is minimised<br>- Network structure affects the final prediction |
Problem Setting:
- How to predict the labels $Y_v$ for the unlabelled nodes $v$?
- Each node $v$ has a feature vector $f_v$
- Labels for some nodes are given
- Task: Find $P(Y_v)$ given all features and the network.

### Loopy Belief Propagation

- Belief Propagation is a dynamic programming approach to **answering probability queries in a graph** (e.g., probability of node $v$ belonging to class 1).
- Iterative process in which neighbour nodes "talk" to each other, **passing messages**.
	- *"I (node $v$) believe you (node $u$) belong to class 1 with likelihood..."*
- When consensus is reached, we calculate the final belief.

Advantages:
1. Easy to program and parallelise
2. General; can apply to any graph model with any form of potentials
	- Potential can be higher order (e.g., $\psi(Y_i, Y_j, Y_k, Y_v, ...)$)

Challenges:
1. Convergence is not guaranteed (when to stop), especially if there's many closed loops.

#### Message Passing

- Task: Count the number of nodes in a graph
- Condition: Each node can only interact (pass messages) with its neighbours 

![[Pasted image 20240604210154.png]]

Algorithm:
1. Define an ordering of nodes (that results in a path)
2. Edge directions are according to order of nodes
	1. Edge direction defines the *order* of message passing
3. For node $i$ from 1 to $N$:
	1. Compute the message from node $i$ to $i+1$ (number of nodes counted so far)
	2. Pass the message from node $i$ to $i+1$

![[Pasted image 20240604210402.png]]

#### Generalising to a Tree

- We can perform message passing not only on a path graph, but also on a tree-structured graph.

![[Pasted image 20240604210457.png]]
#### Notation

- **Label-label potential matrix $\psi$**
	- Dependency between a node and its neighbour.
	- $\psi(Y_i, Y_j)$ is proportional to the probability of a node $j$ being in class $Y_j$ given that it has neighbour $i$ in class $Y_i$.
- **Prior belief $\phi$**
	- $\phi(Y_i)$ is proportional to the probability of node $i$ being in class $Y_i$.
- $m_{i \rightarrow j}(Y_j)$
	- $i$'s message
	- Estimate of $j$ being in class $Y_j$.
- $L$ 
	- Set of all classes / labels.

#### Algorithm

![[Pasted image 20240604210933.png]]
![[Pasted image 20240604211151.png]]




---
## Relational Classification and Iterative Classification

### Probabilistic Relational Classifier

>[!info] This technique does not involve using the node attributes. It only relies on the node labels.

Basic Idea:
- Class probability $Y_v$ of node $v$ is a weighted average of class probabilities of its neighbours.
- For **labelled nodes** $v$, initialise label $Y_v$ with ground-truth label $Y_v^*$.
- For **unlabelled nodes**, initialise $Y_v = 0.5$.
- **Update** all nodes in a *random order* until convergence or until maximum number of iterations is reached.
$$
P(Y_v = c) = \frac{1}{\sum_{(v, u) \in E}A_{v,u}} \sum_{(v,u)\in E}A_{v,u} P(Y_u=c)
$$
	where $u$ is the neighbour of node $v$, and $P(Y_v=c)$ is the probability of node $v$ having label $c$.
- If edges have strength/weight information, $A_{v,u}$ can be the *edge weight* between $v$ and $u$.

Challenges:
1. Convergence is not guaranteed
2. Model cannot use node feature information

![[Pasted image 20240603221446.png]]
![[Pasted image 20240603221934.png]]

### Iterative Classification

Main Idea:
- Classify node $v$ based on its **attributes $f_v$ as well as labels $z_v$** of neighbour set $N_v$.

**Approach: Train two classifiers**
1. $\phi_1(f_v)$ : Predict node label based on node feature vector $f_v$.
2. $\phi_2(f_v, z_v)$ : Predict label based on node feature vector $f_v$ and summary $z_v$ of labels of $v$'s neighbours.

How do we compute the summary $z_v$ of labels of $v$'s neighbours $N_v$?
- Ideas: $z_v$ = vector
	- Histogram of the number (or fraction) of each label in $N_v$
	- Most common label in $N_v$
	- Number of different labels in $N_v$

Steps:
1. **Phase 1: Classify based on node attributes alone**
	- On a *training set*, train classifiers $\phi_1(fv)$ and $\phi_2(f_v, z_v)$.
2. **Phase 2: Iterate till convergence**
	- On a *test set*, set labels $Y_v$ based on the classifier $\phi_1$, compute $z_v$ and predict the labels with $\phi_2$.
	- Repeat for each node $v$.
		- Update $z_v$ based on $Y_u$ for all $u \in N_v$.
		- Update $Y_v$ based on the new $z_v$ ($\phi_2$).
	- Iterate until class labels stabilise or max number of iterations is reached.

>[!warning] Convergence is not guaranteed.

![[Pasted image 20240603223011.png]]
>[!notes]- Step-by-step example
>![[Pasted image 20240604205128.png]]
>![[Pasted image 20240604205307.png]]
>![[Pasted image 20240604205353.png]]
>![[Pasted image 20240604205414.png]]
>![[Pasted image 20240604205446.png]]
>
 





---
# References

- [[CS224W - Machine Learning with Graphs#Chapter 2b Massage Passing & Node Classification]]
