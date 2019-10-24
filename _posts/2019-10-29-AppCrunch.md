---
layout: post
title: Developing a Machine Learning Algorithm to Predict App Store app Ratings
---
Apple's iPhone app store is stocked with over 2.2 million differnt apps, which have been downloaded over 130 billion times. In fact, two of those apps (and nearly 20,000 of those downloads) are from apps that I've developed and published! You can check out [ISS Now](https://apps.apple.com/us/app/iss-now-space-station-tracker/id1047052212?ls=1), and [We Tip](https://apps.apple.com/us/app/we-tip-tip-calculator/id1056447103?ls=1).  Because of my interest in apps and the App Store, I thought it would be fun to try to build an algorith to process this data!
[Kaggle](https://www.kaggle.com) has a bunch of awesome data sets, one of which contains App Store data on the top 7200 apps (unfortuantly, my apps do not rank this high 😔). The data includes the app's name, price, genre, how many devices are supported, how many screen shots are on the app store, and other pertinent information. The main libraries I'll be using are Pandas, NumPy, Matplotlib, and Sci-Kit Learn. After I organize my data correctly, I'll use a RidgeCV regression to process the data.

### Data Visulization
First, I was curious about what our distribution of generes looked like. Although this didn't necessairly help me with the regression, it's intersting information and could have helped me spot an issue in the data if there was one. Here is a bar graph of the genres of the 7200 top apps:
![Oops! This didn't load.](/images/genres.png)

### Linear Regression
I first started with a linear regression to get my feet wet, remind myself of some older topics, and see what chsarachteristics effect our rating the most. I started out by one-hotting the categorical data, since we'd need to do this anyways. I pulled out the category (game, education, social networking, etc...) and the content rating (4 years and older, 17 years and older, etc...) and put those into their own one-hot table. Then, I created a linear regression model, ran the regression, and sorted out relevant features. Unfortunatly, this told me that my data was pretty messy. It looked like relevant features were anything with a R<sup>2</sup> of 0.07 or more... pretty pathetic. Nevertheless, I plotted the relevant features in an SNS pairplot:

![Oops! This didn't load.](/images/sns.png)

From this, I saw a huge skew in my response variable. I went to work trying to log the data and applying different transformation to try to make it more even. This ended up being a huge hastle because logging data with values of 0 (e.g. 0 stars on the App Store) results in negative infinity. After my professor looked at it and determined it would be best to not log the data, I left it at that. 

### Why is my R<sup>2</sup> so darn low!
