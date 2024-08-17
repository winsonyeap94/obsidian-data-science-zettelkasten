---
tags:
  - graphs
  - neural-networks
creation-date: 2024-07-11 22:02
---
## Graph Convolutional Policy Network (GCPN)

GCPN combines **graph representation** with **[[Reinforcement Learning|reinforcement learning]]**.

Key components of GCPN:
1. **[[Graph Neural Networks|Graph Neural Network]]** captures the graph structural information.
2. **Reinforcement learning** guides the generation towards desired objectives.
3. **Supervised training** imitates examples in given datasets.

### Overview

![[Pasted image 20240711220601.png]]

1. (a) Insert nodes.
2. (b, c) Use GNN to predict which nodes to connect.
3. (d) Take an action (check chemical validity).
4. (e, f) Compute reward.

### Training GCPN

**Total Reward = Final Reward + Step Reward**
- Final Reward: Optimise *desired properties*
	- At the end, assign positive reward for high desired property.
- Step Reward: Learn to take *valid action*
	- At each step, assign small positive reward for valid action.

There are two parts to training:
1. **Supervised training**
	- Train *policy* by *imitating the action* given by real observed graphs. 
	- Use gradient.
2. **RL training**
	- Train policy to *optimise rewards*.
	- Use standard *policy gradient algorithm*.

![[Pasted image 20240711220820.png]]

### GCPN vs [[GraphRNN]]

Commonality between both algorithms:
1. Generate graphs sequentially
2. Imitate a given dataset

Main differences:
1. GCPN uses **GNN** to predict the generation action.
	- Pros: GNN is more expressive than RNN.
	- Cons: GNN takes *longer time to compute* than RNN.
2. GCPN uses **RL** to direct graph generation to our goals.
	- RL enables goal-directed graph generation.

![[Pasted image 20240711220547.png]]

---
## References

- [[CS224W - Machine Learning with Graphs#Chapter 12 Deep generative models for graphs]]
