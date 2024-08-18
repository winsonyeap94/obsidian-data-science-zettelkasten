---
tags:
  - explainable-ai
creation-date: 2024-08-18 11:53
---
## Prototypes and Criticisms

![[Pasted image 20240818115424.png]]

A **prototype** is a data instance that is *representative of all the data*. A **criticism** is a data instance that is *not well represented by the set of prototypes*. 
- The purpose of criticisms is to provide insights together with prototypes, especially for data points which the prototypes do not represent well. 
- Prototypes and criticisms can be used independently from a machine learning model to describe the data, but they can also be used to create an interpretable model or to make a black box model interpretable.
- Prototypes and criticisms are <mark style="background: #FFF3A3A6;">always actual instances from the data</mark>.

There are many approaches to find prototypes in the data. One of these is **k-medoids**, a clustering algorithm related to the k-means algorithm. 
- Any clustering algorithm that returns actual data points as cluster centres would qualify for selecting prototypes. But most of these methods find only prototypes, but no criticisms. 
- This chapter presents **MMD-critic** by Kim et al. (2016)[46](https://christophm.github.io/interpretable-ml-book/proto.html#fn46), an approach that combines prototypes and criticisms in a single framework.
	- MMD-critic *compares the distribution of the data and the distribution of the selected prototypes*. MMD-critic selects prototypes that minimize the discrepancy between the two distributions. 
	- Data points in areas with high density are good prototypes, especially when points are selected from different “data clusters”. Data points from regions that are not well explained by the prototypes are selected as criticisms.

### Theory

The MMD-critic procedure on a high-level can be summarized briefly:
1. *Select the number of prototypes and criticisms* you want to find.
2. Find prototypes with greedy search. Prototypes are selected so that the distribution of the prototypes is close to the data distribution.
3. Find criticisms with greedy search. Points are selected as criticisms where the distribution of prototypes differs from the distribution of the data.

We need a couple of ingredients to find prototypes and criticisms for a dataset with MMD-critic:
1. **Kernel function** - to estimate *data densities*
	- A kernel is a function that weighs two data points according to their proximity.
	- Example: radial basis kernel function
1. **Maximum mean discrepancy (MMD)** - to measure *how different two distributions are* so that we can determine whether the distribution of the prototypes we select is close to the data distribution.
2. **Witness function** - to tell us *how different two distributions are at a **particular data point***.
	- With the witness function, we can select *criticisms*.
3. **Greedy search** - as a search strategy for good prototypes and criticisms.


![[Pasted image 20240818120043.png]]
> The squared maximum mean discrepancy measure (MMD2) for a dataset with two features and different selections of prototypes.


$$
MMD^2 = \frac{1}{m^2}\sum^m_{i,j=1}k(z_i, z_j) - \frac{2}{mn}\sum^{m,n}_{i,j=1}k(z_i, x_j) + \frac{1}{n^2} \sum^n_{i,j=1}k(x_i, x_j)
$$
We combine the MMD2 measure, the kernel and greedy search in an algorithm for finding prototypes:
1. Start with an empty list of prototypes.
2. While the number of prototypes is below the chosen number m:
	1. For each point in the dataset, check how much MMD2 is reduced when the point is added to the list of prototypes. Add the data point that minimizes the MMD2 to the list.
3. Return the list of prototypes.

The remaining ingredient for finding criticisms is the witness function, which tells us how much two density estimates differ at a particular point. It can be estimated using:
$$
witness(x)=\frac{1}{n}\sum^n_{i=1}k(x,x_i) - \frac{1}{m}\sum^m_{j=1}k(x,z_j)
$$
The witness function allows us to explicitly search for data instances that are not well represented by the prototypes. Criticisms are points with high absolute value in the witness function.

![[Pasted image 20240818120407.png]]
> Evaluations of the witness function at different points.

#### How can MMD-critic be used for interpretable machine learning?

MMD-critic can add interpretability in three ways: 
1. By helping to better understand the data distribution
2. By building an interpretable model
3. By making a black box model interpretable

As a third option, we can use MMD-critic to make any machine learning model globally explainable by examining prototypes and criticisms along with their model predictions. The procedure is as follows:
1. Find prototypes and criticisms with MMD-critic.
2. Train a machine learning model as usual.
3. Predict outcomes for the prototypes and criticisms with the machine learning model.
4. **Analyse the predictions: In which cases was the algorithm wrong?** Now you have a number of examples that represent the data well and help you to find the weaknesses of the machine learning model.

---
### Advantages

1. MMD-critic works with density estimates of the data. This **works with any type of data and any type of machine learning model**.
2. The algorithm is **easy to implement**.
3. MMD-critic is very flexible in the way it is used to increase interpretability. It can be used to understand complex data distributions. It can be used to build an interpretable machine learning model. Or it can shed light on the decision making of a black box machine learning model.
4. **Finding criticisms is independent of the selection process of the prototypes**. But it makes sense to select prototypes according to MMD-critic, because then both prototypes and criticisms are created using the same method of comparing prototypes and data densities.

### Disadvantages

1. While, mathematically, prototypes and criticisms are defined differently, their **distinction is based on a cut-off value** (the number of prototypes). Suppose you choose a too low number of prototypes to cover the data distribution. The criticisms would end up in the areas that are not that well explained. But if you were to add more prototypes they would also end up in the same areas.
2. You have to **choose the number of prototypes and criticisms**. As much as this can be nice-to-have, it is also a disadvantage. How many prototypes and criticisms do we actually need? The more the better? The less the better?
3.  **How do we select a kernel and its scaling parameter?** Again, when we use MMD-critic as a nearest prototype classifier, we can tune the kernel parameters. For the unsupervised use cases of MMD-critic, however, it is unclear.
4. It takes all the features as input, **disregarding the fact that some features might not be relevant** for predicting the outcome of interest.


---
## References

- [[Interpretable Machine Learning]]
- [8.7 Prototypes and Criticisms | Interpretable Machine Learning (christophm.github.io)](https://christophm.github.io/interpretable-ml-book/proto.html)
