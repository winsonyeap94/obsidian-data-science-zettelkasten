---
tags:
  - graphs
  - embeddings
creation-date: 2024-06-02 14:31
---
# PageRank

PageRank is a **Link Analysis** approach to compute the *importance of nodes in a graph*.

**Idea: Links as votes**
- A page is more important if it has more links.
	- Consideration: In-coming links? Out-going links?
- Think of in-links as votes.
	- However, are all in-links equal? *Links from important pages count more*. This becomes a recursive question.

## PageRank: The "Flow" Model

![[Pasted image 20240602171249.png|200]]

**A "vote" from an important page is worth more:**
- Each link's vote is proportional to the *importance* of its source page.
- If page $i$ with importance $r_i$ has $d_i$ out-links, each link gets $r_i / d_i$ votes.
- Page $j$'s own importance $r_j$ is the *sum of the votes on its in-links*.

Based on this concept, we then define "rank" $r_j$ for node $j$ as
$$
r_j = \sum_{i \rightarrow j} \frac{r_i}{d_i}
$$
where $d_i$ is the out-degree of node $i$.

>[!example]- Web graph example in 1839
>![[Pasted image 20240602171517.png|250]]

---
## How do we solve the equation? 

### Matrix Formulation

![[Pasted image 20240602171757.png|200]]
**Stochastic Adjacency Matrix $M$**
- Let page $j$ have $d_j$ out-links
- If $j \rightarrow i$, then $M_{ij} = \frac{1}{d_j}$
	- $M$ is a column stochastic matrix (meaning that the columns sum to 1).

**Rank vector $r$** (an entry per page)
- $r_i$ is the importance score of page $i$
- $\sum_i r_i$ = 1

The flow equations can be written then as
$$
r = M \cdot r \ \ \ \ \ \ \ \ \ \ r_j = \sum_{i \rightarrow j } \frac{r_i}{d_i} \text{ (from rank concept above)}
$$
>[!example]- Example: Flow Equations & M
>![[Pasted image 20240602172031.png]]

### Connection to Random Walk - The Stationary Distribution

![[Pasted image 20240602172335.png|200]]

- Image a random web surfer:
	- At any time $t$, surfer is on some page $i$
	- At time $t+1$, the surfer follows an out-link from $i$ uniformly at random
	- Ends up on some page $j$ linked from $i$
	- Process repeats indefinitely

- Let:
	- $p(t)$ ... vector whose $i$-th coordinate is the probability that the surfer is at page $i$ at time $t$.
	- Hence, $p(t)$ is a probability distribution over pages.

- Where is the surfer at time $t+1$?
	- Follow a link uniformly at random:
	  $p(t+1) = M \cdot p(t)$
	- Suppose the random walk reaches a state
	  $p(t+1) = M \cdot p(t) = p(t)$
	  then $p(t)$ is **stationary distribution** of a random walk.

- Our original rank vector $r$ satisfies $r = M \cdot r$.
	- So, $r$ is a stationary distribution for the random walk.

### Eigenvector Formulation

>[!tip]- Recap on [[Eigenvectors and Eigenvalues]] for graphs:
>![[Pasted image 20240602173547.png]]

We can look at the flow equation similar to how a eigenvector equation is structured:
  $1 \cdot r = M \cdot r$

So, the **rank vector** $r$ is an **eigenvector** of the stochastic stochastic adjacency matrix $M$ (with eigenvalue 1).
- Starting from any vector $u$, the limit $M(M(... M(M u)))$ is the *long-term distribution* of the surfers.
	- **PageRank** = Limiting distribution = **principal eigenvector** of $M$
	- Note: If $r$ is the limit of the product $MM ... M u$, then $r$ satisfies the flow equation $1 \cdot r = M \cdot r$
	- So $r$ is the **principal eigenvector** of $M$ with eigenvalue 1

With this, we can now efficiently solve for $r$. This method is called the **Power Iteration**.

### Power Iteration

Power iteration is a simple iterative scheme to help solve the PageRank equation.
- Given a web graph with $N$ nodes, where the nodes are pages and edges are hyperlinks,
	- Initialise: $r^0= [1/N, ..., 1/N]^T$
	- Iterate: $r^{(t+1)} = M \cdot r^t$
	- Stop when $|r^{(t+1)} - r^t|_1 < \epsilon$
		- We can use any other vector norm, in the example above we use the L1 norm
- About 50 iterations is sufficient to estimate the limiting solution.

![[Pasted image 20240602214744.png]]

---
## Problems with PageRank

However, there are two problems with PageRank:
1. Some pages are **dead ends** (i.e., have no out-links)
	- Such pages cause importance to "leak out"
2. **Spider traps** (all out-links are within the group)
	- Eventually spider traps absorb all importance

### (1) Dead Ends

Dead-ends are a problem because this causes the matrix to be not column stochastic (columns do not sum to 1), so our initial assumptions are not met.

![[Pasted image 20240602215021.png]]

The solution to the dead-end problem is similar to the spider trap, where we allow the dead-end node to "teleport" to other nodes.
- The adjacency matrix is adjusted so that dead-end nodes **follow random teleport links with total probability 1.0** from dead-ends.

![[Pasted image 20240602215807.png]]

### (2) Spider Traps
![[Pasted image 20240602214953.png]]

Solution for spider traps: At each time step, the random surfer has two options:
1. With probability $\beta$, follow a link at random
2. With probability $1-\beta$, **jump to a random page ("teleport")**
Common values for $\beta$ are in the range 0.8 to 0.9

With this, the surfer will teleport out of the spider trap within a few time steps.

![[Pasted image 20240602215212.png]]

### Google's solution of Random Teleports

At each step, random surfer has two options
1. With probability $\beta$, follow a link at random.
2. With probability $1 - \beta$, jump to some random page.
In practice, $\beta = 0.8, 0.9$ (make 5 steps on average to jump)

**Google's PageRank equation:**
$$
r_j = \sum_{i \rightarrow j} \beta \frac{r_i}{d_i} + (1-\beta)\frac{1}{N}
$$
This formulation assumes that $M$ has no dead-ends. We can either pre-process matrix $M$ to remove all dead-ends or explicitly follow random teleport links with probability 1.0 from dead-ends.

**The Google Matrix G** (Matrix format)
$$
G = \beta M + (1-\beta)\Big[\frac{1}{N}\Big]_{N \times N}
$$

---
## Personalised PageRank

Personalised PageRank is a modified version of PageRank, whereby the node *does not have a uniform probability of teleporting to any node in the network*. Instead, it **ranks proximity of nodes to the teleport nodes ($S$)**. 
- This means that it <mark style="background: #FFF3A3A6;">only teleports to a subset of nodes</mark> $S$.
- What this also means is that it can help us address the problem of **proximity in graphs**, i.e., the question "which nodes in the graph are close/most related to another node?"
- This is achieved via **Random Walk with Restarts**, which allows nodes to teleport back to the starting node: $S = \{Q\}$.

### Random Walks with Restarts

Idea:
- Every node has some importance.
- Importance gets evenly split among all edges and pushed to the neighbours.

Given a set of `QUERY_NODES`, we simulate a random walk:
1. Make a step to a random neighbour and record the visit (visit count).
2. With probability `ALPHA`, restart the walk at one of the `QUERY_NODES`.
3. The nodes with the highest visit count have highest proximity to the `QUERY_NODES`.

![[Pasted image 20240602221525.png]]


![[Pasted image 20240602221721.png]]





---
# References

- [[CS224W - Machine Learning with Graphs#Chapter 2 Node Embeddings]]