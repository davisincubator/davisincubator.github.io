---
layout: post
title: "Predicting healthcare expenditures for the startup company HealthSherpa"
date: 2015-09-30
author:
  - bethstankevich
---
For the TLDR version, check out my [slides]( "slides").  
Check out my Github for the [code](https://github.com/bstankev/Insight_DataProject "code"). 

For the past 2+ weeks I have worked with the startup company [HealthSherpa](https://www.healthsherpa.com/ "HealthSherpa") to predict healthcare expenditures across a range of services (e.g., ER visits, primary care visits). The long game of this prediction is to link up people and their healthcare expenditures with a healthcare plan that a) provides the plan and services they need, b) helps reduce their out of pocket expenses, and c) empowers them with information to make an informed healthcare decision. HealthSherpa’s major goal is to make navigating the healthcare market tractable for the average person. 

You need to be clairvoyant when choosing a healthcare plan. You must be able to predict whether in the next year you will be visiting the ER often or having major surgery or needing expensive prescription medicine. For most of us, this is impossible to know. 

Currently, HealthSherpa provides customers with a fantastic, simple, and informative breakdown of the features a healthcare plan has. However, the list of plans a customer has to choose from is all viable plans available in the customer’s area (e.g., 26 plans for me to choose from). 

![HealthSherpaPlan](https://github.com/bstankev/insight_img/blob/master/HSplan.png) 


HealthSherpa is dedicated to thoughtfully reducing this list. Thus, begins my project. I am providing them with an early peak at the data they have gathered and helping them move forward towards their goal of predicting what healthcare plans a person will need. Specifically, I drilled down into the kinds of features that are predictive of healthcare expenditure and created a model of healthcare expenditures. HealthSherpa will leverage this information to reduce the list of plans they show to customers. 

## **The project before the project.**  
I am new to the healthcare market having been in school or working for the government for the past forever. I already understood that there was a lot of information contained in each plan, but I had zero clue as to the volume of plans available. I found a publicly available dataset on data.healthcare.gov with data on healthcare plans available across the country. Most people are faced with choosing between 20 and 40 plans. Some, especially and perhaps unsurprisingly in Florida, are faced with 100+ choices. That is insane. It illustrates the need for a company like HealthSherpa to wade through the plan space to make personal suggestions for its customers. 

![PlanCount](https://github.com/bstankev/insight_img/blob/master/planCount.png)

## **Starting the project. The data.** 
Some serious legwork was done by HealthSherpa to translate a publicly available dataset that was in a SAS or STATA format into a .csv file. The data comes from the Medical Expenditure Panel Survey (MEPS). It contains data from 2012 on ~38,000 people across 3 survey dates and encompasses ~1,900 variables. The information collected ranges from demographics to health status to healthcare expenditures. What the dataset does not contain is information on the specific plans people are on nor does it contain zipcode (geospatial) information. Omitting specific location information like a zipcode or county keeps the information people provided private. However, the lack of information makes it difficult to leverage census information or to zoom in on plan availability (remember, I already had plan information broken down by county from the government). 

## **Step 2. Finding informative features. (OR perhaps the most important thing I did. Seriously.)**
The columns names from the MEPS data were not in plain English (e.g., ‘RTHLTH53’). They required a close reading of a mound of documentation and searching across multiple survey PDFs. The columns contained, for the most part, strings (e.g., ‘2 Female’). Machine learning algorithms require numbers, not words. All of the string columns needed to be altered from ‘2 Female’ to 2. 

I opted for a quasi-manual approach using a random forest regressor and inspecting the resulting feature importances (I also tried recursive feature selection and forward selection). I set a feature importance cut off value and took all features above that line. 

<img src="https://github.com/bstankev/insight_img/blob/master/featImp.png" width="400" height="200" />

## **Features. They tell a powerful story.**
The quasi-manual approach I utilized served a more important purpose. The end goal is for HealthSherpa to ask their customers a few quick questions and then link them up with a plan. Let’s highlight ‘few’ and ‘quick’. No one wants to answer a ton of long-winded questions. Nor does HealthSherpa want to make people answer 40+ questions before getting plan recommendations. Using my own eyes as a first-pass filter was a powerful way to understand whether HealthSherpa could easily ask someone for this information. While it is obvious that having a preexisting condition like diabetes is highly predictive of the care you might need, it is also obvious that the large majority of people looking for a healthcare plan do not have a preexisting condition. How do you predict their healthcare usage? Simply asking questions about how you feel mentally and physically are important in predicting healthcare spending (see ‘Mental Health’ and ‘Physical Health’ in the feature importance graph above and the right graph below). This is easy to do and everyone (including people without health problems) is able to provide an answer. 

<img src="https://github.com/bstankev/insight_img/blob/master/hlth.png" width="400" height="300" />

Rooting around in the features uncovered two different populations of people that had similar trends. Education level and poverty level were both highly important features. Being more educated or more impoverished is correlated with spending more on healthcare (see graphs below). However, these two groups are unlikely to represent similar groups of people and are unlikely to require the same healthcare plan. It is essential that these populations can be captured and suggested different plans. This result is the precise reason why I started looking for profiles of the different populations within the dataset (see k-means clustering at the end of the post). 

<img src="https://github.com/bstankev/insight_img/blob/master/grpDiff.png" width="400" height="200" />

<img src="https://github.com/bstankev/insight_img/blob/master/wrkflw.png" width="500" height="200" />

**Sidenote:** the of interest expenditures that I analyze using this process include office-based (including primary care physician), ER, prescription drugs, home health, and inpatient. However, to constrain the story and the modeling, I’m going to focus primarily on what I found for office-based visits. 

## **Moving towards modeling expenditures. Preprocessing.**
<img src="https://github.com/bstankev/insight_img/blob/master/expnd.png" width="400" height="300" />

1) Missing data imputation. A lot of data reduction occurs for some types of expenditure. To maintain a dataset of a reasonable size, I filled in missing feature data with the median response (using scikit-learn’s Imputer function). I settled on the median because the data is often highly skewed between groups in a way that using the mean, which is pulled far from the true center of the data, would not make sense. 

2) Standardization. Much of the data is categorical (ordinal) some is continuous (e.g., Age, BMI). Some models are less robust against features of different scales (e.g., Logistic regression). Having the feature space standardized can help protect against having one feature dominate the prediction. I did this using scikit-learn’s StandardScaler function. 

3) Dummy variable creation. Some of the data is categorical (nominal), which some models will treat as continuous and therefore infer categories that are closer in their number label are actually closer to each other. For example, in the case of marriage status, if Married = 1, Single = 2, Widowed = 3, then some models would infer that Married people are more like Single people than they are like Widowed people, which is entirely not correct and the assigned number labels are meaningless. However, dummy variable creation (which I used scikit-learn’s OneHotEncoder tool to do) vastly increases your feature space (in the above example, 1 marriage status feature would become 3). An increase in feature space can lead to a decrease in model predictiveness. For a logistic regression model, doing dummy variable creation is very important. For a random forest regression model, it is not particularly important. In the course of doing my modeling, I switched to ensemble classifiers like gradient boosted regression trees and thus abandoned doing dummy variable creation (and immediately improved model performance). 

## **Model – part 1. Will you spend money?**

**Random forest classifier (RFC).**
Why choose RFC?

1) Can deal with categorical and continuous data
2) More robust against overfitting than decision trees
3) Runtime is fast
4) Able to deal with unbalanced and missing data (though I found that unbalanced classes were poorly predicted)
5) Aren’t troubled by noisy features
6) Can deal with non-linearities in the data
7) Non-parametric model 
8) Provide easy to understand results (unlike a neural net)

What to think about: Even though RFCs are more robust to overfitting, it is still an issue. Additionally, it may not be feasible to utilize this model in real as using a large number of trees in the model can make the algorithm slow to run.

<img src="https://github.com/bstankev/insight_img/blob/master/rfc.png" width="600" height="150" />

What else did I try: started with a very simple logistic regression, but it performed a bit worse and required extra feature manipulation steps. 

## **Model – part 2. How much money money will you spend?**

**Gradient boosted regression tree (GBRT).**
Why choose GBRT?

1) Can deal with categorical and continuous data
2) More robust against overfitting than both decision trees and random forests
3) Able to deal with unbalanced and missing data (though I found that unbalanced classes were poorly predicted)
4) Aren’t troubled by noisy features
5) Can deal with non-linearities in the data
6) Non-parametric model 
7) Provide easy to understand results (unlike a neural net)

What to think about: A problem inherent to any sort of decision tree is overfitting. Use a deviance plot to show the training and testing error vs. number of trees used to understand the point at which adding more trees results in overfitting. GBRT is generally more robust to overfitting because you have a lot of parameters at your fingertips to control overfitting. 

<img src="https://github.com/bstankev/insight_img/blob/master/gbrt.png" width="400" height="200" />

What I tried: I initially started with a logistic regression followed by random forest, in the end GBRT worked the best.

>**Summary:** A lot of bang for my buck was gained (in order of importance) through thoughtful feature reduction, imputation of missing features, dealing with class imbalances (for categorization only), and using hyperparameter tuning. However, model performance could be improved possibly through additional rounds of feature selection and the addition of more data. 

>**Fun fact:** A negative r-squared means your model is predicting worse than simply predicting the average (a corollary to being at chance in classification). Before feature reduction, this was a reality for me and some predictions. 


## **Pet project.** 
Both highly educated and impoverished people spend more on healthcare. Those two populations, while showing similar trends, likely require different healthcare plans. Understanding the profiles that exist in this data may provide some added insight about the plans you might suggest to one population compared to another. 

<img src="https://github.com/bstankev/insight_img/blob/master/kmMod.png" width="400" height="300" />

<img src="https://github.com/bstankev/insight_img/blob/master/kmeans.png" width="400" height="300" />
