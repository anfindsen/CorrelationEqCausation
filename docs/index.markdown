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

Before looking at the results of our analysis, we need to discuss a limitation of ours: overlap of clean data. As we use data from 3 different sources, the overlap of movies and actors accounts for a fragment of the total data. However, as we want to have equality in the analysis of our features, we need to include the same datapoints for all parts of our analysis. Thus we removed all datapoints with unknown values from our study. Of course this can change the distribution of the data.

On the carousel below you can observe the distribution changes in relevant features before and after dropping. The left plot shows the distribution of all datapoints and the right the distribution of datapoints that were nominated.

<div align="center" style="font-size: 1.3em;">
Distribution of features before and after data cleaning
</div>

{% include carousel.html height="50" width="120" unit="%" duration="100" number="1" %}

The distribution of all data changed more drastically for all data than for oscar-nominated data. For example, in the ethnicity feature, indian used to be the most common known ethnicity. However, after cleaning the data it was the second least common value. The number of oscar-nominated indian actors did not reduce however.

We also conducted the Kolmogorov-Smirnov test for all continuous features. With p-value 0.05 and using all data points, changes in distribution were significant for all continuous features (IMDb ratings, height, age, year). However, using nominated only the change in year distribution was significant.


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

Logistic regression is a method for binary classification. It works by essentially mapping linear regression output into a value between 0 and 1 i.e. a probability to belong to either class. So computing linear regression takes 3 steps:
1. Calculate linear regression
z = w_0 + w_1 * x_1 + w_2 * x_2 + ... + w_n * x_n
2. Pass the output through the sigmoid to get a probability
p = 1 / (1 + exp(-z))
3. Use a (freely chosen) threshold for classification
if p >= threshold, predict 1, else 0

Finding the correct weights w_0, w_1, ..., w_n can be done through gradient descent or least squared method.

A useful side product of this method is finding what features have higher weights when predicting a target. Higher weights indicate the importance of that feature towards the target.


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
We explored the possibility to predict whether a person is nominated for an oscar for a movie. We used tried using linear regression and decision trees for this. A challenge is the unbalanced state of the data, with there being 45 times more actors who were not nominated. To account for this we applied both over- and undersampling techniques.

#### Predicting from personal features
Firstly, we tried if it is possible to predict being nomintated purely from personal features such as gender, height, ethnicity and age. If that were possible it would indicate that there is a possibility for a bias for or against certain people, not connected to their skill in acting, which would be bad.

However, both the logistic regression and decision tree proved incapable of doing so, even with rebalancing the data the models achieved a maximum F1 score of 0.05. This indicates that we cannot simply tell who will get nominated by how they look like.


#### Predicting with all features
Predicting with all of the features we have led to more promising results. While logistic regression still did not perform, using a decisiontree with undersampling led to our best-performing model (in therms of F1-score) with:
* Accuracy 0.953
* Precision 0.196
* Recall 0.4375
* F1-score 0.271

While these numbers do not jump off the screen, this model could be used with relative accuracy to predict what actor or actress will next be nominated for an oscar. This specific model would catch 43.75% of the movies before they get nominated, but would only be right 20% of the time.

Extracting the highest feature importance gives us the table:

| Feature | Importance |
|---|---|
| average_rating	 | 0.106306 |
| year | 0.095321 |
| actor_age | 0.081904 |
This indicates that there is no one feature widely used to make this prediction.

## What kind of films should you star in?

### Genre analysis - Melker

### Prediction on movie features - Melker (plots)/ Rasmus

Let's look at if it is possible to predict if a movie will be nominated for an oscar from popular opinion. To do this we split the movies into two: movies, where at least one actor/actress was nominated, and movies where there weren't any.

From looking at the lineplots depicting movie average rating and number of votes relation to being nominated, a general trend upwards can be seen. However, even at the level of the highest ratings, so few movies get nominated, and we can't say with high confidence, that a well- or often-reviewed movie will get nominated
<div style="display: flex; gap: 1px; align-items: center;">
  <img src="images/avg_rating_nominated.png" alt="Average Rating" width="49%" />
  <img src="images/nr_votes_nominated.png" alt="Number of Votes" width="49%" />
</div>

Logistic regression was not very performant on this. The best f1-score was reached was with 0.14, with recall 1.0 and accuracy 0.1, meaning the model generally only predicted that movies will be nominated, which is not a good model.






## Conclusion/recommendations