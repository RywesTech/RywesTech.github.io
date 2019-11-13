---
layout: post
title: Using Machine Learning to Predict Wether a University is For-Profit or Not
---

Recently, for-profit colleges and universities have run into some pretty big troubles. Corruption and and poor practices have ravished the landscape, and cast a bad light onto these schools. As a high-school senior looking at a number of different universities to spend the next 4 years of my life, this is something that greatly interests me. So if I was given all the details about a university, but not wether they were for-profit-or not for profit, would I be able to predict if this school was legit or not?

That's exactly what we'll use machine learning to answer.

### Load up the data
First, we needed to load in the data. Our data is from the [US Department of Education](https://www.ed.gov/) and uses the [College Scorecard Dataset](https://collegescorecard.ed.gov/data/). Our incredibly kind professor downloaded the data and cleaned it for us. Once loading the data into our project, we decided to only keep 33 of the columns as these were the most important ones.

Here's a peek at what the head our our data looks like:
![Oops! This didn't load.](/images/col-head.png)

### Howdy, neighbor
To kick off this data prediction, let's use what we've been learning in class recently: K Nearest Neighbors (KNN). This is an algorithm that looks at neighboring data points uses this to determine a classification. When using GridSearchCV(), we generally get an output of 8 as our optimal neighbor count. However, this number changes each time the program is run as the Test/Train data sets are split randomly. Here's an example of the output of my code:

`The optimal number of nearest neighbors for KNN is 8, and it is 89.51533135509396% accurate.`

If we print out a classification report of our model, here is what we get:

*precision    recall  f1-score   support*
0               0.92      0.90      0.91      1143
1               0.87      0.90      0.88       879

accuracy                            0.90      2022
macro avg       0.89      0.90      0.89      2022
weighted avg    0.90      0.90      0.90      2022

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
