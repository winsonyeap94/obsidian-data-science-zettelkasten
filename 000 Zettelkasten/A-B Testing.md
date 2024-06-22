---
tags:
  - ab-testing
creation-date: 2024-05-02 22:40
---
# A/B Testing

**A/B testing**, or **split testing**, originated from the statistical randomised control trials, is one of the most popular ways of businesses to test:
- new UX features
- new versions of a product
- new versions of an algorithm

Typically it involves two groups:
1. **Control Group** (exposed to one version or the *current* version of product)
2. **Experimental Group** (exposed to second or the *new* version of product)

## A/B Testing Process

![[Pasted image 20240502224313.png]]
### Step 1: Hypothesis of A/B Test

First, we need to formally describe the business hypothesis. 

**Business Hypothesis** describes what two products are being compared and what is the desired impact or difference for the business.
- How to "fix" a potential issue in the product
- Solution will influence the KPIs

**Primary Metric** is a way to measure the performance of the product being tested in the A/B test, for the experimental and control groups. It will be used to identify whether there is a *statistically significant difference* between these two groups.
- There should only be a *single primary metric*.
- Answering *Metric Validity Question*

**Metric Validity Question**: If this chosen metric were to increase significantly while everything else stays constant, would we achieve our goal and address this problem?
- Higher revenue?
- Higher engagement?
- More views?

#### Revenue Primary Metric

$$
\text{Conversion Rate} = \frac{\text{Number of Conversions}}{\text{Number of Total Visitors}} \times 100\%
$$
#### Engagement Primary Metric

$$
\text{Click Through Rate (CTR)} = \frac{\text{Number of Clicks}}{\text{Number of Impressions}} \times 100\%
$$

#### Statistical Hypothesis

**Hypothesis Testing** or **Statistical Hypothesis** is a statistical procedure that is used to determine whether there is a significant difference between the observed data and the expected data.
- Used to test the results of an experiment.
- Establish statistical significance.

We put the hypothesis subject to rejection under the Null Hypothesis ($H_0$).
- The hypothesis subject to acceptance is under the Alternative Hypothesis ($H_1$).

### Step 2: A/B Test Design | Power Analysis

Three steps:
1. Power Analysis
2. Minimum Sample Size
3. Test Duration

#### 2.1 Power Analysis

1. Determine **[[Power]]** ($\beta$) of the test.
	1. Power of a statistical test is the probability of correctly rejecting the null hypothesis.
	2. It is the probability of not making a *Type II Error* ($1-\beta$).
		1. $\beta$ : Type II Error
	3. It is common to pick *80%* as the power of the A/B test.
2. Determine **Significance Level** ($\alpha$) of the test.
	1. It is the probability of rejecting the null, while the null is true.
	2. The odds of detecting statistical significance while it is not, is the probability of making a *Type I Error* ($\alpha$).
	3. It is common to pick *5%* as significance level of the test.
3. Determine **Minimum Detectable Effect** ($\delta$) of the test
	1. What is the substantive to the statistical significance for business?
	2. Proxy that relates to smallest effect that would matter in practice
	3. No common level -> depends on the business risk

#### 2.2 Calculating Min Sample Size

To ensure our results are repeatable, robust, and can be generalised to the entire population, we need to *avoid P-hacking*. This is to ensure real statistical significance and to avoid biased results. To do so, we need to collect sufficient observations, thus having to determine a minimum sample size.

This can be done based on the power ($\beta$) and minimum detectable effect ($\delta$) of the test.

There are two methods, depending on the primary metric of our A/B testing:
1. The primary metric is in the form of a **binary variable**.
2. The primary metric is in the form of **proportions** or **averages** (e.g., click-through rate).

##### Primary Metric is in form of Proportion or Averages

$$
\begin{align*}
H_0 : \mu_{con} = \mu_{exp} \\
H_1 : \mu_{con} \neq \mu{exp}
\end{align*}
$$
The minimum sample size is estimated based on the **[[Central Limit Theorem]]**.

Based on CLT, the sample mean is approximated as a Normal distribution. This means that the difference between both sample means would also follow a Normal distribution, as below.
$$
\begin{gather}
\bar{X}_{con} \sim N(\mu_{con}, \sigma_{con}^2) \\ 
\bar{X}_{exp} \sim N(\mu_{exp}, \sigma_{exp}^2) \\ 
\bar{X}_{con} - \bar{X}_{exp} \sim N(\mu_{con} - \mu_{exp}, \frac{\sigma_{con}^2}{N_{con}} + \frac{\sigma_{exp}^2}{N_{exp}})
\end{gather}
$$

Based on this, we can then calculate the minimum sample size ($N$).
$$
N = \frac{(\sigma^2_{con} + \sigma^2_{exp})(z_{(1-\frac{\alpha}{2})} + z_{(1-\beta)})^2}{\sigma^2}
$$
This can often be calculated using online tools.

### Step 3: A/B Test Duration

To determine the duration of test time, a common formula used is:
$$
\text{Duration} = \frac{N}{\text{No. of Visitors Per Day}}
$$
However, we have to <mark style="background: #FFF3A3A6;">be careful of external factors</mark> (e.g., holidays, special events) during the duration, which will affect our test results.

Considerations:
- Too small test duration $\rightarrow$ **Novelty effects**
- Too large test duration $\rightarrow$ **Maturation effects** (e.g., individuals "grow used to" a treatment, effects by external factors)



---
# References

[[Crash Course in A-B Testing with Case Study]]