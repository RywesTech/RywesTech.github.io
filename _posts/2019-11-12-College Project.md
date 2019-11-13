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

![Oops! This didn't load.](/images/col-stats.png)

The table columns tell us...
An average precision of 0.89 means that out of all the schools predicted to be for-profit, 89% were correct.
An average recall of 0.87 means that 89% of the for-profit schools were corrected predicted as for-profit
The F1 score takes into account the precision and recall, and is used to measure the overall performace of the model.
A support value of 1112 for the 0's means that there are 1112 correct samples in the not-for-profit class.
A support value of 910 for the 1's means that there are 910 correct samples in the for-profit class.

### I am confusion

It's almost like whoever created the confusion matrix wanted it to be... confusing? Well, they sure succeeded. Our model prints out this confusion matrix:

```
array([[3564,  271],
       [ 334, 2571]])
```

Which corresponds to this order:
```
[[TN, FP],
  [FN, TP]]
```
This confusing confusion matrix tells us that...
3,498 school that were correctly predicted as not-for-profit (TN).
337 schools were incorrectly predicted as for-profit (FP).
285 schools were incorrectly predicted as not-for-profit (FN).
2620 schools were correctly predicted as for-profit (TP).

### Decisions, decisions, decisions (boundaries)

Let's use a logarithmic regression model to plot a decision boundary between instructional_expenditure_per_fte and 5_year_declining_balance. After using the StandardScaler to scale our data, here's what we get:

![Oops! This didn't load.](/images/col-boundary.png)

Well... That's disappointing. With how scattered the data is, it seems like these factors probably aren't the best way to predict if a school is for-profit or not. Let's try some other things.

### More algorithms

Let's try using Naive Bayes, Logistic Regression, Gradient Boosting, and KNN classifiers to model our data. Then, let's plot what it would look like to increase their false positive rates (FPR) and see what kind of true positive rates (TPR) we would get:

![Oops! This didn't load.](/images/col-roc.png)

What you see above is an ROC (Receiver Operating Characteristic) curve. As we increase the FPR, the TPR will naturally increase as well. However, we want a low FPR and a high TPR. Because of this, we want to find the curve with the greatest area under it. Luckily, it's pretty easy to see that our Gradient Boosting algorithm wins here at an accuracy of 98.39%.

### What are the worst schools?

### What factors ensure a college is non-predatory?

### Conclusion
