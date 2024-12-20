---
carousels:
  - images: 
    - image: images/nandropping_plots/age.jpg
    - image: images/nandropping_plots/country.jpg
    - image: images/nandropping_plots/ethnicity.jpg
    - image: images/nandropping_plots/gender.jpg
    - image: images/nandropping_plots/genres.jpg
    - image: images/nandropping_plots/height.jpg
    - image: images/nandropping_plots/ratings.jpg
    - image: images/nandropping_plots/year.jpg
---
<!-- ---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
--- -->

# **Introduction**

Blabalbla


# **Data**

## **Data merging** - Erik
a

## **Data cleaning** - Rasmus

Before looking at the results of our analysis, we need to discuss a limitation of ours: overlap of clean data. As we use data from 3 different sources, the overlap of movies and actors accounts for a fragment of the total data. However, as we want to have equality in the analysis of our features, we need to include the same datapoints for all parts of our analysis. Thus we removed all datapoints with unknown values from our study. Of course this can change the distribution of the data.

On the carousel below you can observe the distribution changes in relevant features before and after dropping. The left plot shows the distribution of all datapoints and the right the distribution of datapoints that were nominated.

<div align="center" style="font-size: 1.3em;">
Distribution of features before and after data cleaning
</div>

{% include carousel.html height="50" width="120" unit="%" duration="100" number="1" %}


The distribution of the data changed more drastically for all data than for oscar-nominated data. For example, in the ethnicity feature, indian used to be the most common known ethnicity. However, after cleaning the data it was the second least common value. The number of oscar-nominated indian actors did not reduce however.

We also conducted the Kolmogorov-Smirnov test for all continuous features. With p-value 0.05 and using all data points, changes in distribution were significant for all continuous features (IMDb ratings, height, age, year). However, using nominated only the change in year distribution was significant. (Previous sentence not very clear imo)

# **Analysis** - TODO come up with better headers

Before getting started with the analysis, you might want to get up to date with some <a href="methods/">mathematical methods</a> we used.

## **High level analysis** - Clustering
Before looking into the nitty gritty of actor and movie details, let's analyze all the data we have to get a feel for how it varies and how actor and movie features can correlate with Oscar nominations.

We only use these methods on numeric data.


In the following plot, we see the results of applying T-SNE to generate 2-dimensional embeddings of all the cleaned data of numeric type.
![image](images/T_SNE_all_numerical_features.png)


The Oscar nominees are clearly concentrated in certain regions. This indicates that it should be possible to predict whether someone _will not_ get nominated with reasonable accuracy (if for instance they fall in the regions without any nominees near by). Entries of nominees do, however, overlap with entries that were not nominated, so we cannot yet say if it is possible to reliably predict whether someone _will_ get nominated for an oscar. We find that the results of PCA also support this conclusion.
<div style="display: flex; gap: 1px; align-items: center;">
  <img src="images/PCA_scatter_all_features.png" alt="pca" width="49%" />
  <img src="images/PCA_elbow_all.png" alt="pca elbow" width="49%" />
</div>

The elbow plot below shows us how much of the total variance in the data is explained by each PC (called explained variance ratio - EVR). This allows us to gauge how 'important' each PC is (if a PC does not explain much about the data, it is not important).

This heatmap shows how individual features affect principal components, _scaled by the explained variance ratio_. This allows for easier interpretation compared the previous heatmap as values in different columns can be compared directly to judge how much variance is explained by them.
![image](images/PCA_heatmap_all_weighted.png)

We will see more of these soon.

## **What kind of actor should you be?**
Can one optimize to increase their chances of winning an oscar?

### **Clustering** - Tejas
We start answering the question of whether it is possible to optimize the odds of winning an oscar by looking at the data. Different clustering methods applied to different subsets of actor personal features all reveal the same thing - there are things to avoid to ensure you actually stand a chance of winning.

**Clustering on all personal numeric features of actors**

This is the result of T-SNE on _all_ features pertaining to the actors themselves - `age`, `gender`, (one hot encoded) `ethnicity`, `height` and also the augmented features capturing experience - `number_of_movies_starred_in`, `average_rating_previous_movies` and `average_box_office_revenue_previous_movies`.
![image](images/T_SNE_actor_personal_features.png)


*T-sne normalized=True, perplexity=50*

It can be seen that there are multiple clusters, with some having more nominated actors. We suspect this could have to do with clusters corresponding to ethnicities

**Effect of dropping ethnicities on clusters**

On removing ethnicities, we get the following plots from PCA and T-SNE respectively:

<div style="display: flex; gap: 1px; align-items: center;">
  <img src="images/T_SNE_actor_personal_wo_ethnicity.png" alt="pca personal" width="49%" />
  <img src="images/PCA_actor_personal_wo_ethnicity.png" alt="pca persoal wo ethnicity" width="49%" />
</div>

Interestingly, T-SNE showed the oscar winners and nominees scattered throughout the plots quite uniformly for all the hyperparameter settings we tried (perplexities from 5 to 45 in steps of 10, with and without normalization of features).
PCA still clearly shows the nominees cluster to be a well defined subset of all actors and the oscar winners cluster to be a subset of the nominees cluster.
Thus ethnicity could be an important feature to explain variance in the data.

The heatmap below shows the contributions of different features to different Principal Components, weighted by the amount of variance that each PC explains in the data - called explained variance ratio (EVR) (so for example, all values for PC1 are weighted by the EVR for PC1, and all those for PC5 are weighted by the EVR for PC5, allowing for direct comparison of the overall importance of each feature).
<p align="center">
  <img src="images/PCA_heatmap_actor_no_eth_weighted.png" width="80%" />
</p>
We see large values (absolute) for all the features, indicating that they are quite independant. This is also confirmed by an elbow plot of the explained-variance-ratios, with the last PC explaining 10% of the variance in the data.
(TODO set scale to show how flat it is better)

<p align="center">
  <img src="images/PCA_actor_personal_wo_eth_elbow.png"  width="45%" style="margin: auto" />
</p>

**Effect of dropping actor experience on clusters**

To gauge the importance of the augmented features on experience, let's try clustering without them and compare the results. We drop the three augmented features and use the top 10 ethnicities by frequency, one-hot-encoded, along with age, gender and height.
<div style="display: flex; gap: 1px; align-items: center;">
  <img src="images/PCA_scatter_personal_no_augmented.png"  width="49%" />
  <img src="images/T_SNE_personal_no_augmented.png" width="49%" />
</div>

We still see regions to avoid being in if we want to win an oscar, but this is a bit harder now.


Now that we have a feel for how the features vary, lets dive in and rigorously evaluate the impact of every personal feature.

### **Nationality** - John
How does nationality correlate with being nominated for an Oscar? Let's have a look at the 10 countries with the most Oscar nominations in our cleaned dataset:

![image](images/oscar-nom-per-country.png)

Unsurprisingly, the US is clearly at the top of this list. Here is the same data, with only the US, UK and Others:
![image](images/oscar-nom-per-country-others.png)

As we can see by these plots, the US is in general overrepresented in our dataset with regards to Oscar nominations. This makes sense, as the movie industry is very centered on America. Our most important question here, however, is how does the Oscar nomination frequency change with nationality? Let's see the previous plot again, but this time adjusted for the total number of movies from each of the categories:

![image](images/oscar-nom-per-country-adjusted.png)

To our surprise, there does not seem to be a clear skew in favor of American actors/movies in our dataset! In fact, actors in British movies tend to be nominated more frequently.

This needs to be analyzed more rigorously, and that is where our binomial test enters the picture. We analyze the distribution of American vs. non-American movies in our dataset, with the first category containing all actors in movies where the US is one of the nationalities, or the only one (many movies have multiple nationalities). First off, we find the ratio of American movies in our dataset being nominated. Then we perform the binomial test to see if the ratio of non-American movies being nominated is significantly different from the American ratio. We choose alpha=0.05 as the threshold to discard the null hypothesis for this test.

Again, to our surprise, we find that while the ratio is slightly higher for American movies, they do not have a significantly higher ratio of Oscar nominations that non-American movies, with a p-value of 0.56 (far higher than 0.05). This is likely very affected by our data cleaning process, where we as previously mentioned (TODO: Add reference) remove a lot of data from countries like India (removing much of Bollywood from the equation). However, with this result in mind we decide to keep non-American data in our dataset for all the following analysis, as the data cleaning has removed the expected multicolinearity between country and Oscar nomination.

Although the nationalities are quite balanced in our cleaned dataset, it is also interesting to examine what the distribution looks like for the data before cleaning. In the full dataset 0.55% of American movies are nominated, as opposed to 0.064% of non-American movies. We perform the same binomial test for this data, and find that the probability of these two numbers being from the same distribution (the p-value) is a whopping 2.6*10^(-307). Thus we conclude that in general (assuming our full dataset is somewhat representative), actors in American movies have a significantly higher chance of being nominated for an Oscar than non-American movies.

TL;DR: In general, actors in American movies have a higher chance of winning an Oscar, but in our cleaned dataset this skew is gone.

### **Career analysis** - Erik


#### **Actor Network** - Melker
What makes a great movie is seldom a single performance, it often an ensemble of great performances enhancing each other. Hence we make a deep dive into the actor relationships with other actors. We define a relationship as having performed in the same movie as another actor, i.e. actors will be nodes and shared performances between actors are edges in a graph. It is however not useful plotting the graph of all actors as it has over 23 000 nodes. We instead count the number of relationships for nominated and not nominated actors which turn out to 72.8 and 37.2 respectively. Nominated actors have this number of relationships while on average having stared in 8.9 movies while not nominated actors have on average starred in 7.9 movies. From this we see three options: 1. Nominated actors perofmr in movies with more actors, creatingmore relationships per movie starred in. 2. Nominated actors are in more popular movies where more actors are registered and hence in the dataset. 3. TBD

Additionally, it appears that all nominated actors have a relationship to atleas another oscar nominated actor. This enforces the hypothesis that great performances are not stand-alone but rather enforced by other performances. 
### **Prediction on actor features** - Rasmus
We explored the possibility to predict whether a person is nominated for an oscar for a movie. We tried using linear regression and decision trees for this. A challenge is the unbalanced state of the data, with there being 45 times more actors who were not nominated. To account for this we applied both over- and undersampling techniques.

#### **Predicting from personal features**
Firstly, we tried if it is possible to predict being nomintated purely from personal features such as gender, height, ethnicity and age. If that were possible it would indicate that there is a possibility for a bias for or against certain people, not connected to their skill in acting, which would be bad.

However, both the logistic regression and decision tree proved incapable of doing so, even with rebalancing the data the models achieved a maximum F1 score of 0.05. This indicates that we cannot simply tell who will get nominated by how they look like.


#### **Predicting with all features**
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

## **What kind of films should you star in?**

### **Genre analysis** - Melker
<iframe src="{{ 'images/genres_wo_nominations.html' | relative_url }}" width="100%" height="600px" frameborder="0"></iframe>
### **Prediction on movie features** - Melker (plots)/ Rasmus

Let's look at if it is possible to predict if a movie will be nominated for an oscar from popular opinion. To do this we split the movies into two: movies, where at least one actor/actress was nominated, and movies where there weren't any.

From looking at the lineplots depicting movie average rating and number of votes relation to being nominated, a general trend upwards can be seen. However, even at the level of the highest ratings, so few movies get nominated, and we can't say with high confidence, that a well- or often-reviewed movie will get nominated
<div style="display: flex; gap: 1px; align-items: center;">
  <img src="images/avg_rating_nominated.png" alt="Average Rating" width="49%" />
  <img src="images/nr_votes_nominated.png" alt="Number of Votes" width="49%" />
</div>

Logistic regression was not very performant on this. The best f1-score was reached was with 0.14, with recall 1.0 and accuracy 0.1, meaning the model generally only predicted that movies will be nominated, which is not a good model.






## **Conclusion/recommendations**