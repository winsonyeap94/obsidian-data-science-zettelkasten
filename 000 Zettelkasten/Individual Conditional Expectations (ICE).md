---
tags:
  - explainable-ai
creation-date: 2024-08-18 12:09
---
## Individual Conditional Expectations (ICE)

![[Pasted image 20240818121041.png]]

Individual Conditional Expectation (ICE) plots *display **one line per instance** that shows how the instance’s prediction changes when a feature changes*.
- The [[Partial Dependence Plot (PDP)]] for the average effect of a feature is a global method because it does not focus on specific instances, but on an overall average. The equivalent to a PDP for individual data instances is called individual conditional expectation (ICE) plot.

### Centred ICE Plot

There is a problem with ICE plots: Sometimes it can be hard to tell whether the ICE curves differ between individuals because they start at different predictions. 
- A simple solution is to **centre the curves at a certain point** in the feature and **display only the difference in the prediction** to this point. The resulting plot is called **centred ICE plot (c-ICE)**. 
- Anchoring the curves at the lower end of the feature is a good choice. 

The new curves are defined as:
$$
\hat{f}_{cent}^{(i)} = \hat{f}^{(i)} - \mathbf{1} \hat{f}\Big(x^a, x_C^{(i)}\Big)
$$
- $\mathbf{1}$ : Vector of 1's with the appropriate number of dimensions
- $\hat{f}$ : Fitted model
- $x^a$ : Anchor point

![[Pasted image 20240818121337.png]]
![[Pasted image 20240818121343.png]]


---
### Advantages

1. Individual conditional expectation curves are **even more intuitive to understand** than partial dependence plots. One line represents the predictions for one instance if we vary the feature of interest.
2. Unlike partial dependence plots, ICE curves can **uncover heterogeneous relationships**.

### Disadvantages

1. ICE curves **can only display one feature** meaningfully, because two features would require the drawing of several overlaying surfaces and you would not see anything in the plot.
2. ICE curves suffer from the same problem as PDPs: If the feature of interest is correlated with the other features, then **some points in the lines might be invalid data points** according to the joint feature distribution.
3. If many ICE curves are drawn, the **plot can become overcrowded** and you will not see anything. The solution: Either add some transparency to the lines or draw only a sample of the lines.


---
## References

- [[Interpretable Machine Learning]]
- [9.1 Individual Conditional Expectation (ICE) | Interpretable Machine Learning (christophm.github.io)](https://christophm.github.io/interpretable-ml-book/ice.html)