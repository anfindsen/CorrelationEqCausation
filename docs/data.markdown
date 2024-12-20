---
layout: page
title: Data Preparation
permalink: /data/
carousels:
  - images: 
    - image: nandropping_plots/age.jpg
    - image: nandropping_plots/country.jpg
    - image: nandropping_plots/ethnicity.jpg
    - image: nandropping_plots/gender.jpg
    - image: nandropping_plots/genres.jpg
    - image: nandropping_plots/height.jpg
    - image: nandropping_plots/ratings.jpg
    - image: nandropping_plots/year.jpg
---

### **Data merging**
For the following analysis we used the CMU dataset containing tables of movies and of characters. The movies and characters have a one-to-many 
relationship allowing us to connect them. Furthermore, the character table contains a column specifying the actor playing the character. This dataset is supplemented with IMDb dataset to find the ratings for the movies and an dataset containing Oscar nomination.

Further we introduce three augmented features: Number of movies starred in, average rating of previous movies, and average box office revenue of previous movies.   

The Oscars are comprised of many different cateogries. However, in the coming analysis we are interested in the following three categories for both men and women: actor/actress, actor/actress in a leading role, and actor/actress in a supporting role. 

### **Data cleaning**

Before looking at the results of our analysis, we need to discuss a limitation of ours: overlap of clean data. As we use data from 3 different sources, the overlap of movies and actors accounts for a fragment of the total data. However, as we want to have equality in the analysis of our features, we need to include the same datapoints for all parts of our analysis. Thus we removed all datapoints with unknown values from our study. Of course this can change the distribution of the data.

On the carousel below you can observe the distribution changes in relevant features before and after dropping. The left plot shows the distribution of all data points and the right the distribution of datapoints that were nominated.

<div align="center" style="font-size: 1.3em;">
Distribution of features before and after data cleaning
</div>

{% include carousel.html height="50" width="120" unit="%" duration="100" number="1" %}


The distribution of the data changed more drastically for all data than for oscar-nominated data. For example, in the ethnicity feature, Indian used to be the most common known ethnicity. However, after cleaning the data it was the second least common value. The number of oscar-nominated Indian actors, however, was not reduced.

We also conducted the Kolmogorov-Smirnov test for all continuous features. With p-value 0.05 and using all data points, changes in distribution were significant for all continuous features (IMDb ratings, height, age, year). However, using nominated only the change in year distribution was significant. (Previous sentence not very clear imo)
