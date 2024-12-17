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
a


# Mathematical methods (Not too havy, mmaybe put in separate page)

## Binomial test - John

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

![image](images/oscar-nom-per-country.png)
![image](images/oscar-nom-per-country.png)

### Career analysis - Erik

### Prediction on actor features - Rasmus




## What kind of films should you star in?

### Genre analysis - Melker

### Prediction on movie features - Melker (plots)/ Rasmus





## Conclusion/recommendations