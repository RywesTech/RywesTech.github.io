---
layout: post
title: Using Machine Learning to Predict Wether a University is For-Profit or Not
---

Recently, for-profit colleges and universities have run into some pretty big troubles. Corruption and and poor practices have ravished the landscape, and cast a bad light onto these schools. As a high-school senior looking at a number of different universities where I could spend the next 4 years of my life, this is something that greatly interests me. So if I was given all the details about a university, but not wether they were for-profit or not-for-profit, would I be able to predict if this school was legit or not?

That's exactly what we'll use machine learning to answer.

### Load up the data
First, we needed to load in the data. Our data is from the [US Department of Education](https://www.ed.gov/) and uses the [College Scorecard Dataset](https://collegescorecard.ed.gov/data/). Our incredibly kind professor downloaded the data and cleaned it for us. Once loading the data into our project, we decided to only keep 33 of the columns as these were the most important ones.

Here's a peek at what the head our our data looks like:
![Oops! This didn't load.](/images/col-head.png)

### Howdy, neighbor
To kick off this data prediction, let's use what we've been learning in class recently: K Nearest Neighbors (KNN). This is an algorithm that looks at neighboring data points and uses this to determine a classification. When using GridSearchCV(), we generally get an output of 8 as our optimal neighbor count. However, this number changes each time the program is run as the test and train data sets are split randomly. Here's an example of the output of my code:

`The optimal number of nearest neighbors for KNN is 8, and it is 89.51533135509396% accurate.`

If we print out a classification report of our model, here is what we get:

![Oops! This didn't load.](/images/col-stats.png)

The table columns tell us that...
<ul>
<li>An average precision of 0.89 means that out of all the schools predicted to be for-profit, 89% were correct.</li>
<li>An average recall of 0.87 means that 89% of the for-profit schools were corrected predicted as for-profit.</li>
<li>The F1 score takes into account the precision and recall, and is used to measure the overall performace of the model.</li>
<li>A support value of 1112 for the 0's means that there are 1112 correct samples in the not-for-profit class.</li>
<li>A support value of 910 for the 1's means that there are 910 correct samples in the for-profit class.</li>
</ul>

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

First, let's choose a few factors (columns) that make up wether a school is "good" or not. For me, I chose: wether or not a school is under investigation (bad), diversity (good), students with loans (bad), accreditation (good), and for-profit (bad). With these in mind, let's reuse some code from the RollerCoaster project to determine the worst schools. First, my code defines some setpoints to determine wether an aspect is good or bad. It then uses a basic scaling algorithm to scale all the columns while rating them between 0 and 100. 0 if they are the worst in the column relative to the other schools, 100 if they are the best in the column relative to the other schools, or somewhere in the middle. The code does this for each of the 5 aspects, then averages the score.

```python
# this is basically what we use to scale the data
def mapFromTo(x,a,b,c,d):
   y=(x-a)/(b-a)*(d-c)+c
   return y

# set the mins and maxes (100 if that side is good, 0 if that side is bad. E.g. max diversity is good, min diversity is bad)
investigation_max = 0; investigation_min = 100
diversity_max = 100; diversity_min = 0
loans_max = 0; loans_min = 100
accred_max = 100; accred_min = 0
profit_max = 0; profit_min = 100;
```

We end up with these schools (and scores out of 100):

<ol>
  <li>Milan Institute (21.8 out of 100)</li>
  <li>Laird Institute of Spa Therapy (22.2 out of 100)</li>
  <li>Gary Manuel Aveda Institute (22.4 out of 100)</li>
  <li>Bella Capelli Academy (22.6 out of 100)</li>
  <li>Everest Institute (23 out of 100)</li>
</ol>

This list in generalized to chains of schools, but if we look at individual schools then the Milan Institutes actually make up 10 of the 15 worst schools. Thankfully, they are not on my list for applications this year. (On a side note, their [website](https://milaninstitute.edu/) looks like it's from the early 2000's and contains a wide array of *borderline funny* stock images. My algorithm must've worked well.) Anyways, on a happier note, it looks like Atlanta Metropolitan State College, Los Angeles Southwest College, and CUNY Medgar Evers College are the top colleges with scores of 90.2, 90.6, and 92.0, respectively.

### What factors ensure a college is non-predatory?

<ul>
<li>**"Students of color have been hit heaviest by Ashford’s deceptive tactics, a trend that holds true across the for-profit college industry." (Harvard Review)**</li>
<li>**"Just when you thought the student loan crisis couldn't get any worse..." (BigThink)**</li>
<li>**"Licensing and accreditation are the bare-minimum credentials a school should have." (NerdWallet)***</li>
</ul>

Above are just a few of the main quotes I found while I was researching predatory schools. It seems that diversity, loans, and accreditation seem to have the largest impact on wether a school is performing a predatory practice or not.

Sources and other good links:
<https://blog.harvardlawreview.org/for-profit-schools-predatory-practices-and-students-of-color-a-mission-to-enroll-rather-than-educate/>
<https://bigthink.com/politics-current-affairs/predatory-student-loans?rebelltitem=3#rebelltitem3>
<https://www.nerdwallet.com/blog/loans/student-loans/college-choice/>
<https://predatorystudentlending.org/>

### Conclusion

In trying to predict wether a school is for-profit or not, we can tell that the Gradient Boosting algorithm works the best for our data set from the shape of our ROC curve. If fact, if we put in the data for Stanford University, we can see that it outputs the correct answer of not-for-profit.
By using a custom rating algorithm which takes into account wether or not a school is under investigation (bad), diverse (good), has many students with loans (bad), accredited (good), and for-profit (bad), we can estimate the worst and best schools. Milan Institute was by far the worst school (you can read about the disaster [here](https://www.visaliatimesdelta.com/story/news/2019/07/23/milan-institute-struggles-make-payroll-while-employees-scramble/1751222001/)), and CUNY Medgar Evers College in New York seemed to be the best. It is also worth looking into what makes a school a Predatory school, which you can read about in the links above.
I hope you enjoyed reading about these schools!
