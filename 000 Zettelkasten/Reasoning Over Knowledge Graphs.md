---
tags:
  - graphs
  - knowledge-graphs
creation-date: 2024-06-24 22:00
---
## Reasoning Over Knowledge Graphs

*"Can we do multi-hop reasoning, i.e., answer complex queries, on an incomplete, massive KG?"*

The goal of reasoning over knowledge graphs is to **answer multi-hop queries**, which can be of:
1. One-hop queries,
2. Path queries, or
3. Conjunctive queries

![[Pasted image 20240624220153.png]]

Example KG: Biomedicine
![[Pasted image 20240624220449.png]]

| Query Types         | Examples (Query)                                                                                                                       |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| One-hop Queries     | What adverse effect is caused by Fulvestrant?<br>*(e: Fulvestrant, (r: Causes))*                                                       |
| Path Queries        | What protein is associated with the adverse event caused by Fulvestrant?<br>*(e: Fulvestrant, (r: Causes, r: Assoc))*                  |
| Conjunctive Queries | What is the drug that treats breast cancer and caused headache?<br>*((e: BreastCancer, (r: TreatedBy)), (e: Migraine, (r: CausedBy)))* |

---
## Types of Queries

### (1) Predictive One-hop Queries

We can formulate [[Knowledge Graph Completion Tasks|KG completion]] problems as answering one-hop queries.

![[Pasted image 20240624220630.png]]

### (2) Path Queries

![[Pasted image 20240624220759.png]]

We can generalise one-hop queries to path queries by *adding more relations on the path*.

An $n$-hop path query $q$ can be represented by:
$$
q = (v_a, (r_1, ..., r_n))
$$
- $v_a$ : "anchor" entity
- Let answers to $q$ in graph $G$ be denoted by $[[q]]_G$

>[!example]- Example
>Question: "What proteins are *associated* with adverse events *caused* by *Fulvestrant*?"
>- $v_a$ : e: Fulvestrant
>- $(r_1, r_2)$ : (r: Causes, r: Assoc)
>- Query: (e: Fulvestrant, (r: Causes, r:Assoc))
>
>![[Pasted image 20240624220935.png]]
>![[Pasted image 20240624221121.png]]
>![[Pasted image 20240624221107.png]]

>[!warning] A challenge with answering queries is that KGs are **incomplete** and **unknown**, i.e., many relations between entities are missing or are incomplete (e.g., lack of knowledge, enumerating all the facts take non-trivial time and cost).
>Can we first do KG completion and then traverse the completed KG?
>- Unfortunately **no**, because the "completed" KG is a *dense graph* (most $(h,r,t)$ triples (edge on KG) will have some non-zero probability).
>- The time complexity of traversing a dense KG is exponential as a function of the path length $L: O(d_{max}^L)$.
>
>A solution to help overcome the problem of incomplete graphs is by using **[[Predictive Queries]]**.

### (3) Conjunctive Queries

![[Pasted image 20240624223112.png]]

The idea to solve conjunctive queries is to find the **intersection** between the different sets of queries to get the final set of answers.

![[Pasted image 20240624223301.png]]

>[!warning] Similar to Path Queries, Conjunctive Queries also face the same problem where KGs are incomplete and unknown. 
>However, the problem is different from Path Queries due to the following:
>1. Each intermediate node represents a set of entities; how do we represent it?
>2. How do we define the intersection operation in the latent space?
>
>A solution to help solve the problem for conjunctive queries is by using [[Box Embeddings]].



---
## References

- [[CS224W - Machine Learning with Graphs#Chapter 9 Reasoning over knowledge graphs]]