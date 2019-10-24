---
layout: post
title: Developing a Machine Learning Algorithm to Predict App Store app Ratings
---
Apple's iPhone App Store is stocked with over 2.2 million different apps, which have been downloaded over 130 billion times. In fact, two of those apps (and nearly 20,000 of those downloads) are from apps that I've developed and published! You can check out [ISS Now](https://apps.apple.com/us/app/iss-now-space-station-tracker/id1047052212?ls=1), and [We Tip](https://apps.apple.com/us/app/we-tip-tip-calculator/id1056447103?ls=1).  Because of my interest in the App Store, I thought it would be fun to try to build an algorithm to predict the rating (0-5 stars) of apps based off the app's data!
[Kaggle](https://www.kaggle.com) has a bunch of awesome data sets, one of which contains App Store data on the top 7,200 apps (unfortunately, my apps do not rank this high 😔). The data includes the app's name, price, genre, how many devices are supported, how many screenshots are on the app store, and other pertinent information. The main libraries I'll be using are Pandas, NumPy, Matplotlib, and Sci-Kit Learn. After I organize my data correctly, I'll use a RidgeCV regression to process the data.

### Data Visualization
First, I was curious about what our distribution of genres looked like. Although this didn't necessarily help me with the regression, it's interesting information and could have helped me spot an issue in the data if there was one. Here is a bar graph of the genres of the 7,200 top apps:

![Oops! This didn't load.](/images/genres.png)

People sure like video games!

### Linear Regression
I first started with a linear regression to get my feet wet, remind myself of some older topics, and see what characteristics affect our rating the most. I started out by one-hotting the categorical data, since we'd need to do this anyways. I pulled out the category (game, education, social networking, etc...) and the content rating (4 years and older, 17 years and older, etc...) and put those into their own one-hot table. Then, I created a linear regression model, ran the regression, and sorted out relevant features. Unfortunately, this told me that my data was pretty messy. It looked like relevant features were anything with an R<sup>2</sup> of 0.07 or more... pretty pathetic. Nevertheless, I plotted the relevant features in an SNS pairplot:

![Oops! This didn't load.](/images/sns.png)

From this, I saw a huge skew in my response variable. I went to work trying to log the data and applying different transformation to try to make it more even. This ended up being a huge hassle because logging data with values of 0 (e.g. 0 stars on the App Store) results in negative infinity. After my professor looked at it and determined it would be best to not log the data, I left it at that. 

### Why is my R<sup>2</sup> so darn low!

Because my R<sup>2</sup> was so low, I wanted to plot some of my numerical data to see what was going on. Here's what I saw:

![Oops! This didn't load.](/images/dat1.png)
![Oops! This didn't load.](/images/dat2.png)
![Oops! This didn't load.](/images/dat3.png)

Well, this is certainly some odd looking data! The first graph looks odd because there are plenty of app with few screenshots on the App Store that rate high, while there is also plenty of apps with many screenshots on the App Store that rate very low! So we end up with this odd looking graph of numerical values. The second and third graphs look more normal, but there are a whole lot of possible values on the low end of the X-axis, so this is probably one of the reasons our R^2 is so low. Just for fun, I wanted to see what it would look like to fit a polynomial to the "Number of Languages Supported Graph". Here's what my code produced:

![Oops! This didn't load.](/images/dat4.png)

Interesting for sure!

### Time to run this RidgeCV

The first step was to find what polynomial *degree* fits the *numerical* explanatory data the best. By using a train_test_split with a train size of 75% and a test size of 25%, I was able to generate the graph below:

![Oops! This didn't load.](/images/testtrain.png)

This graph shows that the least amount of error will occur while fitting a degree-two polynomial to the data set. Next, I used a polynomial of degree 2 to populate our dataframe with the original columns, columns squared, and interaction columns. I then scaled the data so columns with large ranges (such as number of ratings) wouldn't overpower the columns with smaller ranges (such as the price). These steps were done using a Pipeline, which ties together steps into a sequence.
Once I had the new, scaled data, I combined the numerical and one-hot data into one DataFramed called "X". Once I had X and y, I was able to simply use the RidgeCV command with possible alphas of [0.00001, 0.1, 1] to create a model. Then, in one elegant line, model.fit(X,y), the model was fit to the data. An intercept and a ton of coefficients were outputted from this process. There were a lot because of the large number of categorical data, as well as the colums that were created in the PolynomialFeature stage.

![Oops! This didn't load.](/images/output.png)

### Conclusion

In the end, I only had a model score of about 0.15. With the data provided, it seems very hard to be able to predict the rating of an app. There are so many aspects of an app that will lead people to rate it differently. The data I had access to is just one part of the story. Nevertheless, it was a super exciting process getting to learn all about RidgeCV, Pandas, and how these algorithms process data!

You can find my repository with all my code [here](https://github.com/RywesTech/AppCrunch).
