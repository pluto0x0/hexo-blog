---
title: Z-Test
date: 2024-09-18 18:58:07
img_path: /_posts/
mermaid: true
categories:
- Course Notes
- Statistics
---

## Z-Test

1. **For Population Proportion**:
   You can use the Z test to test hypotheses about population proportions. For example, you can test if the proportion of a certain category in one population differs from that in another.

2. **For Comparing Two Means**:
   You can compare two sample means to determine whether they come from populations with the same mean. For example, the Z test can be used to check if the average height in two groups is the same.

<!-- 3. **For One-Sample Mean Test**:
   You can test whether the mean of a sample differs significantly from a known population mean. -->

### Key Assumptions

- The data should be approximately **normally distributed**, especially for large samples (central limit theorem).
- You should have a sufficiently large **sample size** (typically $n > 30$ for each group).
- You should know the **population variance** or approximate it when the sample size is large.

## Z-Test Formula for Comparing Means

For a Z test comparing two sample means, the formula is:

$$
Z = \frac{(\bar{X_1} - \bar{X_2})}{\sqrt{\frac{\sigma^2_1}{n_1} + \frac{\sigma^2_2}{n_2}}}
$$

Where:

- $\bar{X_1}$ and $\bar{X_2}$ are the sample means,
- $n_1$ and $n_2$ are the sample sizes,
- $\sigma_1$ and $\sigma_2$ are the population standard deviations (or sample standard deviations for large samples).

{% note primary %}

### Example

You want to compare the average heights of male and female students at a school. You take a random sample of 50 male students and 50 female students. The average height of the males in your sample is 175 cm with a standard deviation of 10 cm, and the average height of the females is 165 cm with a standard deviation of 8 cm. You want to test whether the average heights of males and females are the same.

#### Null Hypothesis ($H_0$)

The mean height of males and females is equal, i.e., $\mu_1 = \mu_2$.

#### Test Statistic Formula

$$
Z = \frac{(\bar{X_1} - \bar{X_2})}{\sqrt{\frac{\sigma^2_1}{n_1} + \frac{\sigma^2_2}{n_2}}}
$$

Where:

- $\bar{X_1}$ = mean height of males = 175 cm,
- $\bar{X_2}$ = mean height of females = 165 cm,
- $\sigma_1$ = standard deviation of male heights = 10 cm,
- $\sigma_2$ = standard deviation of female heights = 8 cm,
- $n_1 = n_2 = 50$.

#### Calculation

$$
Z = \frac{175 - 165}{\sqrt{\frac{10^2}{50} + \frac{8^2}{50}}} = \frac{10}{\sqrt{2 + 1.28}} = \frac{10}{\sqrt{3.28}} = \frac{10}{1.811} \approx 5.52
$$

Since the Z-value is quite large, you would reject the null hypothesis and conclude that there is a significant difference between the average heights of male and female students.
{% endnote %}

## Z-Test Formula for Population Proportion

For a Z test involving proportions, the formula is:
$$
Z = \frac{p_1 - p_2}{\sqrt{P(1 - P) \left( \frac{1}{n_1} + \frac{1}{n_2} \right)}}
$$
Where:

- $p_1$ and $p_2$ are the sample proportions,
- $n_1$ and $n_2$ are the sample sizes,
- $P$ is the pooled proportion, calculated as:

$$
P = \frac{x_1 + x_2}{n_1 + n_2}
$$

where $x_1$ and $x_2$ are the number of successes in each sample.

{% note primary %}

### Example

A factory produces light bulbs, and it claims that 95% of its products meet quality standards. You take a random sample of 100 light bulbs and find that 90 of them meet the standards. You want to test whether the actual proportion of quality light bulbs is 95% as the factory claims.

#### Null Hypothesis ($H_0$)

The proportion of quality light bulbs is 95%, i.e., $p = 0.95$.

#### Test Statistic Formula

$$
Z = \frac{\hat{p} - p_0}{\sqrt{\frac{p_0(1 - p_0)}{n}}}
$$

Where:

- $\hat{p}$ = sample proportion = $\frac{90}{100} = 0.90$,
- $p_0$ = population proportion (claimed value) = 0.95,
- $n$ = sample size = 100.

#### Calculation

$$
Z = \frac{0.90 - 0.95}{\sqrt{\frac{0.95(1 - 0.95)}{100}}} = \frac{-0.05}{\sqrt{\frac{0.95 \times 0.05}{100}}} = \frac{-0.05}{0.02179} \approx -2.29
$$

You would then compare this Z-value to the critical value from the Z-distribution to determine whether to reject the null hypothesis. If the Z-value is beyond the critical value for a given significance level (e.g., 1.96 for a 95% confidence level), you reject the null hypothesis.
{% endnote %}

### When to Use a Z Test

- **Large sample sizes** (typically $n > 30$).
- When the population variance is known or can be approximated.
- When you are comparing sample means or proportions and you want to test for significant differences.

If the sample size is small, and the population variance is unknown, a **t-test** is usually more appropriate.
