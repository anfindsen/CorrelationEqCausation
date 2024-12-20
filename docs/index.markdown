
<!-- ---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
--- -->

# **Introduction**

The Academy awards are famous for being the most coveted awards in the film industry, and it is the dream of many aspiring actors to raise the figurine and be inducted in to the club of elite actors and household names. In order to win an Oscar the most important step is to get nominated, and in this article we attempt to understand which factors are the most important, and at the end give some data-driven advice to actors that are starting out. 

# **Data**
For the analysis we use the CMU dataset for retrieving metadata about movies, characters and their corresponding actors. Furthermore, we combine this dataset with an IMDb dataset containing ratings for the movies and a table containing Oscar nomination. For a more detailed rundown of our data preprocessing and cleaning please visit our data tab found <a href="data/">here</a>.

After cleaning and processing the data the final dataset has the following characteristics. 

|  | Number of datapoints       | Number of nominated datapoints    |
|-----------------------------------------|---------------------|--------------------|
| Total datapoints              | 23812       | 519    |
| Different movies              | 5987       | 394    |
| Different actors                         | 2959               | 284               |

# **Analysis**

You might want to get up to date with some <a href="methods/">mathematical methods</a> we use in the analysis.

## **Patterns in the data**
Before getting into the nitty gritty of actor and movie details, let's analyze all the data we have to get a feel for how it varies and how actor and movie features can correlate with Oscar nominations.

We only use these methods on numeric data.


In the following plot, we see the results of applying T-SNE to generate 2-dimensional embeddings of all the cleaned data of numeric type.
![image](images/T_SNE_all_numerical_features.png)


The Oscar nominees are clearly concentrated in certain regions. This indicates that it should be possible to predict whether someone _will not_ get nominated with reasonable accuracy (if for instance they fall in the regions without any nominees near by). Entries of nominees do, however, overlap with entries that were not nominated, so we cannot yet say if it is possible to reliably predict whether someone _will_ get nominated for an Oscar. We find that the results of PCA also support this conclusion.
<div style="display: flex; gap: 1px; align-items: center;">
  <img src="images/PCA_scatter_all_features.png" alt="pca" width="49%" />
  <img src="images/PCA_elbow_all.png" alt="pca elbow" width="49%" />
</div>

The elbow plot below shows us how much of the total variance in the data is explained by each PC (called explained variance ratio - EVR). This allows us to gauge how 'important' each PC is (if a PC does not explain much about the data, it is not important).

This heatmap shows how individual features affect principal components, _scaled by the explained variance ratio_. This allows for easier interpretation compared the previous heatmap as values in different columns can be compared directly to judge how much variance is explained by them.
![image](images/PCA_heatmap_all_weighted.png)

We will see more of these soon.

## **What kind of actor should you be?**
Can one optimize to increase their chances of winning an Oscar?

### **Clustering**
We start answering the question of whether it is possible to optimize the odds of winning an Oscar by looking at the data. Different clustering methods applied to different subsets of actor personal features all reveal the same thing - there are things to avoid to ensure you actually stand a chance of winning.

**Clustering on all personal numeric features of actors**

This is the result of T-SNE on _all_ features pertaining to the actors themselves - `age`, `gender`, (one hot encoded) `ethnicity`, `height` and also the augmented features capturing experience - `number_of_movies_starred_in`, `average_rating_previous_movies` and `average_box_office_revenue_previous_movies`.
![image](images/T_SNE_actor_personal_features.png)


*T-sne normalized=True, perplexity=50*

It can be seen that there are multiple clusters, with some having more nominated actors. We suspect this could have to do with clusters corresponding to ethnicities

**Effect on clusters of dropping ethnicities**

On removing ethnicities, we get the following plots from PCA and T-SNE respectively:

<div style="display: flex; gap: 1px; align-items: center;">
  <img src="images/T_SNE_actor_personal_wo_ethnicity.png" alt="pca personal" width="49%" />
  <img src="images/PCA_actor_personal_wo_ethnicity.png" alt="pca persoal wo ethnicity" width="49%" />
</div>

Interestingly, T-SNE showed the Oscar winners and nominees scattered throughout the plots quite uniformly for all the hyperparameter settings we tried (perplexities from 5 to 45 in steps of 10, with and without normalization of features).
PCA still clearly shows the nominees cluster to be a well defined subset of all actors and the Oscar winners cluster to be a subset of the nominees cluster.
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

**Effect on clusters of dropping actor experience on clusters**

To gauge the importance of the augmented features on experience, let's try clustering without them and compare the results. We drop the three augmented features and use the top 10 ethnicities by frequency, one-hot-encoded, along with age, gender and height.
<div style="display: flex; gap: 1px; align-items: center;">
  <img src="images/PCA_scatter_personal_no_augmented.png"  width="49%" />
  <img src="images/T_SNE_personal_no_augmented.png" width="49%" />
</div>

We still see regions to avoid being in if we want to win an Oscar, but this is a bit harder now.


Now that we have a feel for how the features vary, lets dive in and rigorously evaluate the impact of every personal feature.

### **Nationality**
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

### **Career analysis**
In this section we are intersted in finding out more about what characterizes the career of Oscar nominated actors. Furthermore, we explore how an actor's career is impacted after being nominated for their first Oscar. 

For this analysis we split the career of an Actor into three parts: Before being nominated for their first Oscar, at the moment they were nominated for their first Oscar and the career after their first nomination. For actors who have never received or have yet to receive an Oscar nomination we observe their career as one phase. Moreover, for the first part we are only concerned about actors who have been active in the 21st centuary. The reason for this is that our dataset starts at 1928 and for the analysis to make sense we need to observe the actors from the start of their career. 

For our first analysis let's find out how many movies actors star in before being nominated for an Oscar and compare this to the number of movies starred in for actors who have never or yet to receive a nomination. 

![image](images/movies_starred_in.png)

Observing the plots it appears that when actors receive their first Oscar nomination when they have starred in more movies than what most actors have starred in over their career. The average number of movies starred in before receiving an Oscar nomination is 15.13 while in our dataset non-nominated actors have starred in an average of 7.67 movies over their entire career.

However, this analysis is a slightly flawed as the non-nominated sample could contain young actors who are just starting out and are thus weighing down the average. Let's further analyse this potential flaw by plotting the age distribution in both samples.

![image](images/age.png)

The average age of the nominated group is 41.26 and 40.17 for the non-nominated group. However, observing the distribution we observe that the youngest nominated actor is 19 years old while the youngest actor in the non-nominated group is 4 years old. This could explain why the average number of movies starred is lower in the non-nominated group. Therefore, to improve our analysis we filter the non-nominated group to only contain actors who are 19 years old or older. This will give each actor in the sample the same oppurtunity to star in the same number of movies.

![image](images/movies_starred_in_2.png)

In the new sample the average number of movies starred in is 7.92. Let's further investigate if the difference in averages is significant. To do this we utilise and T-test and calculate a P-value of 1.27e-27. This P-value falls below the threshold and we thus reject the null hypothesis being that the averages between the samples would be the same and accept the alternative hypothesis that they are different.  

From this we make the conclusion that actors need to star in around 15 movies on average before being nominated for their first Oscar. In addition, we conclude that one of the reasons why some actors may not have been nominated for an Oscar is due to not having starred in enough movies yet or throught out their career.

With the conclusion that actors need to star in more movies in order to receive Oscar nominations we continue our analysis by analysing if the "success" of the movies you starred in prior to being nominated matters. For the analysis we assume that the success of a movie is determined by the box office revenue and the IMDb rating. 

![image](images/revenue_rating_pre_non.png)

Observing the plot above our initial conclusion is that actors going on to be nominated for an Oscar seem to generally star in higher rated movies prior to their first nomination, but not more comercially sucessful ones. We will further investigate this hypothesis by conducting T-tests with respect to the revenue and rating seperately.

| Metric                                  | Non-Nominated       | Pre-Nominated      | P-Value            |
|-----------------------------------------|---------------------|--------------------|--------------------|
| Average box office revenue              | 86,090,801.55       | 84,983,818.47      | 0.8203             |
| Average rating                          | 5.58                | 6.52               | 1.79e-37           |

From the table above we conclude that actors who go on to be Oscar nominated star in more crically acclaimed movies than actors who do not end up being nominated. However, the comercial success of movies starred in seem to not matter for receiving Oscar nomination as the P-value is 0.82 and thus we accept the null hypothesis of the samples means being equal. A reason for this may be that some genres of movies appeal to the critics but not to the masses.


#### **Actor Network**
<iframe src="{{ 'images/network_analysis.html' | relative_url }}" width="80%" height="480px" frameborder="0"></iframe>

What makes a great movie is seldom a single performance, it is often an ensemble of great performances enhancing each other. Hence we take a deep dive into the actor's relationships with other actors. We define a relationship as having performed in the same movie as another actor, i.e. actors will be nodes and shared performances between actors are edges in a graph. It is however not useful for plotting the graph of all actors as it has over 23 000 nodes. We instead count the number of relationships for nominated and not nominated actors which turn out to be 72.8 and 37.2 respectively. Nominated actors have this number of relationships while on average having starred in 19 movies while not nominated actors have on average starred in just 6 movies. From this, we can conclude that nominated actors before or after their nominations star in more movies with more actors. 
<iframe src="{{ 'images/network_graph_no_ticks.html' | relative_url }}" width="80%" height="480px" frameborder="0"></iframe>

Additionally, it appears that all nominated actors have a relationship to atleast one other Oscar nominated actor. This enforces the hypothesis that great performances are not stand-alone but rather enforced by other performances. 

### **Modelling on actor features**
We explore the possibility to predict whether a person is nominated for an Oscar for a movie. We try using linear regression and decision trees for this. A challenge is the unbalanced state of the data, with there being 45 times more actors who were not nominated. To account for this we apply both over- and undersampling techniques.

#### **Modelling on personal features**
Firstly, we try, if possible, to predict being nomintated purely from personal features such as gender, height, ethnicity and age. If that were possible it would indicate that there is a possibility for a bias for or against certain people, not connected to their skill in acting, which would be bad.

However, both the logistic regression and decision tree proves incapable of doing so, even with rebalancing the data the models achieved a maximum F1 score of 0.05. This indicates that we cannot simply tell who will get nominated by what they look like.


#### **Modelling using all features**
Predicting with all of the features we have leads to more promising results. While logistic regression still does not perform, using a decision tree with undersampling leads to our best-performing model (in terms of F1-score) with:
* Accuracy 0.953
* Precision 0.196
* Recall 0.4375
* F1-score 0.271

This result, while better, is not sufficient for reliably predicting Oscar nominations. It gets really high accuracy, but that is only because most actors are not nominated, and the model generally correctly predicts this. The model would correctly classify 43.75% of Oscar nominations, but only 19.6% of predicted nominations would be correct. While this is an interesting number (Considering the large amount of actors who are not nominated, definitely better than random!) it is not a great prediction.

Extracting the highest feature importance gives us the table:

| Feature | Importance |
|---|---|
| average_rating	 | 0.106306 |
| year | 0.095321 |
| actor_age | 0.081904 |
This indicates that there is no one feature widely used to make this prediction.

## **What kind of films should you star in?**

### **Genre analysis**
<iframe src="{{ 'images/genres_wo_nominations.html' | relative_url }}" width="80%" height="480px" frameborder="0"></iframe>

There are clearly genres worth avoiding for oscar aspiring actors. For instance Animation, Absurdims, Dystopia and Alien film to name a few. 
On the otherhand, there are also genres that have many actor nominations, e.g. Drama, Period, Romantic drama and Biography. However, they have many nominations partly because they have many movies. Hence, we can look at the genres with the highest share of Oscar winners. 


<iframe src="{{ 'images/genres_by_share2.html' | relative_url }}" width="80%" height="480px" frameborder="0"></iframe>
The genre with the highest share of Oscar nominated performances is New Hollywood with 27% of performances being nominated. Coming second is Biographical film with only 8% of performances being nominated. With a time machine, travelling back to the 1960s-1980s would be the most optimal for an actor winning an Oscar. Hence we will need to look at more recent successful genres. 
<iframe src="{{ 'images/genres_by_share_new.html' | relative_url }}" width="80%" height="480px" frameborder="0"></iframe>

In recent years, the most popular genres at the Oscars has not had as high success rate as the most popular genres in earlier years. Up top is Period piece, Romantic drama and Crime Thriller. 

In summary, there are genres to avoid, there has been significant historic oppurtunity in choosing genere, 


### **Modelling on movie features**

Let's look at if it is possible to predict if a movie will be nominated for an Oscar from popular opinion. To do this we split the movies into two: movies, where at least one actor/actress was nominated, and movies where there weren't any.

From looking at the line plots depicting movie average rating and number of votes relation to being nominated, a general trend upwards can be seen. However, even at the level of the highest ratings, so few movies get nominated, and we can't say with high confidence, that a well- or often-reviewed movie will get nominated
<div style="display: flex; gap: 1px; align-items: center;">
  <img src="images/avg_rating_nominated.png" alt="Average Rating" width="49%" />
  <img src="images/nr_votes_nominated.png" alt="Number of Votes" width="49%" />
</div>

Logistic regression is not very performant on this. The best f1-score we reach is 0.14, with recall 1.0 and accuracy 0.1, meaning the model generally only predicts that movies will be nominated, which is not a good model.

<iframe src="{{ 'images/review_dist_new_title.html' | relative_url }}" width="80%" height="480px" frameborder="0"></iframe>


The average rating a movie receives is indicative of several important factors to take into consideration, e.g.: movie quality, audience tastes, specific audience, the era of release, and actor popularity. The overarching issue is that when actors receive oscar nominations, it can affect all the factors contributing to different review scores. Looking at the review distributions for movies with nominated and not nominated actors we can only draw one conclusion with certainty, that films with nominated actors receive significantly better reviews than movies with not nominated actors. This is also verified through the Kolmogorov-Smirnov test, which gets an infinitesimally small p-value.  







## **Conclusion/recommendations**