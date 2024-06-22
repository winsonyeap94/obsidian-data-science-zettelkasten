---
tags:
  - causal-analytics
  - probability
  - statistics
creation-date: 2024-05-05 17:18
---
# Simpson's Paradox

![[Pasted image 20240505172303.png]]

Simpson's paradox is a statistical phenomenon where an **association between two variables in a population appears, disappears, or reverses when the data is disaggregated** into subgroups. The key features of Simpson's paradox are:

1. There is an apparent association between two variables when the data is aggregated, but this association disappears or reverses when the data is broken down into subgroups.
2. This occurs because there is a **confounding variable** that is <mark style="background: #FFF3A3A6;">unequally distributed across the subgroups</mark>.****** The effect of this confounding variable overwhelms the relationship between the original two variables when the data is aggregated.
3. A classic example is in a university admissions scenario, where women appear to be admitted at a lower rate than men overall, but when broken down by department, women are actually admitted at higher rates in both the natural sciences and social sciences departments. This is because women tended to apply more to the social sciences departments, which had lower admission rates overall.
4. Simpson's paradox illustrates how statistical relationships can be highly dependent on the specific data and variables being analysed. It *cautions against drawing conclusions from aggregated data without considering potential confounding factors*.

In summary, Simpson's paradox demonstrates how an apparent relationship between two variables can be misleading or even reversed when underlying confounding factors are not properly accounted for in the analysis. It is an important statistical concept for understanding the limitations of drawing causal inferences from observational data.

## Example: Mortality Rate Table

![[Pasted image 20240505171914.png]]

When looking at the overall scenario ("Total" column), Treatment A may look better. However, if we break it down by "Condition", we can see Treatment B is actually a better choice.
- This is because Treatment A is **biased due to higher weightage on Mild**, which has a lower mortality rate.


---
# References

[[Casual Inference by Brady Neal#Chapter 1 Motivation - Why You Might Care]]