---
carousels:
  - images: 
    - image: /images/nandropping_plots/age.jpg
    - image: /images/nandropping_plots/country.jpg
    - image: /images/nandropping_plots/ethnicity.jpg
    - image: /images/nandropping_plots/gender.jpg
    - image: /images/nandropping_plots/genres.jpg
    - image: /images/nandropping_plots/height.jpg
    - image: /images/nandropping_plots/ratings.jpg
    - image: /images/nandropping_plots/year.jpg
---
<!-- ---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
--- -->

# Introduction

Blabalbla


# Data

## Data merging - Erik
a

## Data cleaning - Rasmus

{% include carousel.html height="50" width="120" unit="%" duration="100" number="1" %}



# Mathematical methods (Not too heavy, mmaybe put in separate page)

First off, we want to define some of the mathematical methods we use in this analysis.

## Binomial test - John

The binomial test calculates the probability of getting a certain number of positives, given a binary probability. This can be used to estimate whether we have reason to believe that two distributions are in fact the same. This is the formula for calculating this probability (AKA the p-value):
![image](images/binom-test.png)\\
*Source: <a href="https://sites.utexas.edu/sos/files/2015/06/binom.png">https://sites.utexas.edu/sos/files/2015/06/binom.png</a>*

With tests like these you typically choose an alpha value as a threshold. If the p-value from the test is below this alpha value, you can discard the null hypothesis, and conclude that the distributions are significantly different.

Source for this section: <a href="https://sites.utexas.edu/sos/guided/inferential/categorical/univariate/binomial/">https://sites.utexas.edu/sos/guided/inferential/categorical/univariate/binomial/</a>

## T-test - Erik

## Clustering - Tejas
We use T-SNE to visually inspect the data for natural clusterings that might also be informative about the chances of being nominated for oscars. T-SNE measures the closeness of pairs of points in higher dimensional spaces and then transforms the points to a lower dimension while trying to preserve this notion of closeness and distance  (i.e. points that were close in higher dimensions should stay close in the lower dimension and vice versa). This allows for easier visualization and handling of high dimensional data, but comes at the cost of interpretability, as we cannot exactly say how the original features contribute individually to the new features in the reduced space.

We also use PCA in a similar fashion. PCA attempts to summarize the information in high dimensional data using a smaller set of uncorrelated variables called Principal Components (PCs) that capture most of the variance in the data. PCs are linear combinations of the original features. This makes PCA better than T-SNE for explainability as we can see how much each feature contributes to each principal component.

(_I am adding my general analysis here coz no designated place as of now, can tie into other parts as needed_)

In the following plot, we see the results of applying T-SNE to generate 2-dimensional embeddings of all the cleaned data of numeric type 
![image](images/T_SNE_all_numerical_features.png)


The Oscar nominees are clearly concentrated in certain regions. This indicates that it should be possible to predict whether someone _will not_ get nominated with reasonable accuracy (if for instance they fall in the regions without any nominees near by). Entries of nominees do, however, overlap with entries that were not nominated, so we cannot yet say if it is possible to reliably predict whether someone _will_ get nominated for an oscar. We find that the results of PCA also support this conclusion - (TODO make colours same across plots..)
![image](images/PCA_scatter_all_features.png)




## Logistic regression - Rasmus




# Analysis - TODO come up with better headers



## What kind of actor should you be?

### Clustering - Tejas

### Nationality - John
How does nationality correlate with being nominated for an Oscar? Let's have a look at the 10 countries with the most Oscar nominations in our cleaned dataset:

![image](images/oscar-nom-per-country.png)

Unsurprisingly, the US is clearly at the top of this list. Here is the same data, with only the US, UK and Others:
![image](images/oscar-nom-per-country-others.png)

As we can see by these plots, the US is in general overrepresented in our dataset with regards to Oscar nominations. This makes sense, as the movie industry is very centered on America. Our most important question here, however, is how does the Oscar nomination frequency change with nationality? Let's see the previous plot again, but this time adjusted for the total number of movies from each of the categories:

![image](images/oscar-nom-per-country-adjusted.png)

To our surprise, there does not seem to be a clear skew in favor of American actors/movies in our dataset! In fact, actors in British movies tend to be nominated more frequently.

This needs to be analyzed more rigorously, and that is where our binomial test enters the picture. We analyze the distribution of American vs. non-American movies in our dataset, with the first category containing all actors in movies where the US is one of the nationalities, or the only one (many movies have multiple nationalities). First off, we find the ratio of American movies in our dataset being nominated. Then we perform the binomial test to see if the ratio of non-American movies being nominated is significantly different from the American ratio. We choose alpha=0.05 as the threshold to discard the null hypothesis for this test.

Again, to our surprise, we find that while the ratio is slightly higher for American movies, they do not have a significantly higher ratio of Oscar nominations that non-American movies, with a p-value of 0.56 (far higher than 0.05). This is likely very affected by our data cleaning process, where we as previously mentioned (TODO: Add reference) remove basically all data from countries like India (removing all of Bollywood from the equation). However, with this result in mind we decide to keep non-American data in our dataset for all the following analysis, as the data cleaning has removed the expected multicolinearity between country and Oscar nomination.

Although the nationalities are quite balanced in our cleaned dataset, it is also interesting to examine what the distribution looks like for the data before cleaning. In the full dataset 0.55% of American movies are nominated, as opposed to 0.064% of non-American movies. We perform the same binomial test for this data, and find that the probability of these two numbers being from the same distribution (the p-value) is a whopping 2.6*10^(-307). Thus we conclude that in general (assuming our full dataset is somewhat representative), actors in American movies have a significantly higher chance of being nominated for an Oscar than non-American movies.

TL;DR: In general, actors in American movies have a higher chance of winning an Oscar, but in our cleaned dataset this skew is gone.

### Career analysis - Erik

### Prediction on actor features - Rasmus




## What kind of films should you star in?

### Genre analysis - Melker

### Prediction on movie features - Melker (plots)/ Rasmus





## Conclusion/recommendations