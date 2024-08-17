---
tags:
  - graphs
  - synthetic-data-generation
  - neural-networks
creation-date: 2024-07-08 22:39
---
## GraphRNN

GraphRNN is a deep learning technique used for **generating synthetic graphs** (think GANs for graphs).

The idea of GraphRNN is to generate graphs by *sequentially adding nodes and edges*.

![[Pasted image 20240708224141.png]]

### Modelling Graphs as Sequences

![[Pasted image 20240708224226.png]]

The **sequence $S^\pi$** has two levels:
- **Node-level**: Add nodes, one at a time.
	- Note that <mark style="background: #FFF3A3A6;">each node-level step is an edge-level sequence</mark>.
- **Edge-level**: Add edges between existing nodes.

With this, the goal of GraphRNN is to then use RNNs to model these processes.

![[Pasted image 20240708224332.png]]
![[Pasted image 20240708224423.png]]

In essence, we have transformed graph generation problem into a *sequence generation problem*.
- We need to model two processes:
	1. Generate a state for a new node (node-level sequence)
	2. Generate edges for the new node based on its state (edge-level sequence)
- The approach is to use RNNs to model these processes.

### Two-levels of RNN

GraphRNN has a node-level RNN and an edge-level RNN. The relationship between the two RNNs are:
1. Node-level RNN generates the *initial state* for the edge-level RNN.
2. Edge-level RNN then sequentially *predicts if the new node will connect* to each of the previous node. 

![[Pasted image 20240711214807.png]]
- The start-of-sequence (SOS) token is used to initialise the input sequence.
	- SOS is usually a vector with all zeros/ones.
- The end-of-sequence (EOS) token is then used as an extra RNN output.
	- If output EOS=0, RNN will continue generation.
	- If output EOS=1, RNN will stop generation.

#### Towards Edge-level RNN

>[!notes]- **Teacher Forcing** - Replacing input and output by the real sequence
>![[Pasted image 20240711215211.png]]
>![[Pasted image 20240711215219.png]]
>![[Pasted image 20240711215230.png]]
>![[Pasted image 20240711215244.png]]
>![[Pasted image 20240711215252.png]]

#### Putting things together

Steps:
1. **Add a new node** : We run Node RRN for a step, and use its output to initialise Edge RNN.
2. **Add new edges for the new node** : We run Edge RNN to predict if the new node will connect to each of the previous node.
3. **Add another new node** : We use the last hidden state of Edge RNN to run Node RNN for another step.
4. **Stop graph generation** : If Edge RNN outputs EOS at step 1, we know no edges are connected to the new node. We stop the graph generation.

>[!steps]- Step-by-step illustration (Model Training)
>![[Pasted image 20240711215435.png]]
>![[Pasted image 20240711215443.png]]
>![[Pasted image 20240711215456.png]]
>![[Pasted image 20240711215507.png]]
>![[Pasted image 20240711215518.png]]
>![[Pasted image 20240711215529.png]]
>![[Pasted image 20240711215540.png]]
>![[Pasted image 20240711215552.png]]

>[!steps]- Step-by-step illustration (Test)
>![[Pasted image 20240711215629.png]]


---
## References

- [[CS224W - Machine Learning with Graphs#Chapter 12 Deep generative models for graphs]]