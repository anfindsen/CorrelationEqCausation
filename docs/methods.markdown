---
layout: page
title: Methods
permalink: /methods/
---



Here are some of the mathematical methods and concepts we use in this analysis:

<h3 id="correlation" style="font-weight:bold;">Correlation</h3>
No correlation implies that there is no causation. Hence this makes for a good starting point of our analysis.

<h3 id="kolmogorov-smirnov" style="font-weight:bold;">Kolmogorov-Smirnov Test</h3>
For different single dimensional, continuous empirical distributions, the KS test tells us if they come from the same underlying distribution. 
Hence we can use it to see if for instance the underlying distributions of ratings of Oscar nominated and non-Oscar nominated movies is the same.

<h3 id="binomial-test" style="font-weight:bold;">Binomial test</h3>

The binomial test calculates the probability of getting a certain number of positives, given a binary probability. This can be used to estimate whether we have reason to believe that two distributions are in fact the same. This is the formula for calculating this probability (AKA the p-value):

![image](images/binom-test.png)

*Source: <a href="https://sites.utexas.edu/sos/files/2015/06/binom.png">https://sites.utexas.edu/sos/files/2015/06/binom.png</a>*

With tests like these you typically choose an alpha value as a threshold. If the p-value from the test is below this alpha value, you can discard the null hypothesis, and conclude that the distributions are significantly different.

Source for this section: <a href="https://sites.utexas.edu/sos/guided/inferential/categorical/univariate/binomial/">https://sites.utexas.edu/sos/guided/inferential/categorical/univariate/binomial/</a>



<h3 id="t-test" style="font-weight:bold;">T-test</h3>

A T-test is a statistical test used to determine if there is a significant difference between the means of two independent distribution. 

The first step of the T-test is stating the null hypothesis and the alternative hypothesis. The null hypothesis is that the means of the samples are similar while the alternative hypothesis is that the mean are different.

After stating the hypothesis you assume the null hypothesis is true and calculate the T-statistic and corresponding P-value. The P-value is the probability of obtaining the result under the null hypothesis. If the P-value is low enough we can therefore safely reject the null hypothesis and assume the alternative hypothesis to be true. To determine which P-values are low enough we set a threshold at 0.05 meaning we reject the null hypothesis if the probability of obtaining the result and it being true is less than 5%.


<h3 id="clustering" style="font-weight:bold;">Clustering</h3>

We use **T-SNE** to visually inspect the data for natural clusters that might also be informative regarding the chances of being nominated for Oscars. T-SNE measures the closeness of pairs of points in higher dimensional spaces and then transforms the points to a lower dimension while trying to preserve this notion of closeness and distance  (i.e. points that were close in higher dimensions should stay close in the lower dimension and vice versa). This allows for easier visualization and handling of high dimensional data, but comes at the cost of interpretability, as we cannot exactly say how the original features contribute individually to the new features in the reduced space.

We also use **PCA** in a similar fashion. PCA attempts to summarize the information in high dimensional data using a smaller set of uncorrelated variables called Principal Components (PCs) that capture most of the variance in the data. PCs are linear combinations of the original features. This makes PCA better than T-SNE for explainability as we can see how much each feature contributes to each principal component.


<h3 id="logistic-regression" style="font-weight:bold;">Logistic regression</h3>

Logistic regression is a method for binary classification. It works by essentially mapping linear regression output into a value between 0 and 1, i.e. the probability of belonging to either class. Computing linear regression takes 3 steps:

1. Calculate linear regression
z = w_0 + w_1 * x_1 + w_2 * x_2 + ... + w_n * x_n
2. Pass the output through the sigmoid function to get a probability
p = 1 / (1 + exp(-z))
3. Use a (freely chosen) threshold for classification
if p >= threshold, predict 1, else 0

Finding the correct weights w_0, w_1, ..., w_n can be done through gradient descent or least squared method.

A useful side product of this method is finding what features have higher weights when predicting a target. Higher weights indicate the importance of that feature towards the target.


