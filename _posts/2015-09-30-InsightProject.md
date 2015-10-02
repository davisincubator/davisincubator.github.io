---
layout: post
title: "Predicting healthcare expenditures for the startup company HealthSherpa"
date: 2015-09-30
author:
  - bethstankevich
---
For the TLDR version, check out my [slides]( https://www.slideshare.net/secret/3VsCdmITn7jGgl "slides").  
Check out my Github for the [code](https://github.com/bstankev/Insight_DataProject "code"). 

For the past 2+ weeks I have worked with the startup company [HealthSherpa](https://www.healthsherpa.com/ "HealthSherpa") to predict healthcare expenditures across a range of services (e.g., ER visits, primary care visits). The long game of this prediction is to link up people and their healthcare expenditures with a healthcare plan that a) provides the services they need, b) helps reduce their out of pocket expenses, and c) empowers them with information to make an informed healthcare decision. HealthSherpa’s major goal is to make navigating the healthcare market tractable for the average person. 

You need to be clairvoyant when choosing a healthcare plan. You must be able to predict whether in the next year you will be visiting the ER often or having major surgery or needing expensive prescription medicine. For most of us, this is impossible to know. 

Currently, HealthSherpa provides customers with a fantastic, simple, and informative breakdown of the features a healthcare plan has. However, the list of plans currently contains all viable plans available in the customer’s area (e.g., 26 plans for me to choose from). 

![hsplan](https://cloud.githubusercontent.com/assets/11904975/10254656/f56de90a-68f9-11e5-9d05-b0993b120752.png)   

> Sample plan information a customer would see when searching for a plan.

HealthSherpa is dedicated to thoughtfully reducing this list. Thus, begins my project. I am providing them with an early peak at the data they have gathered and helping them move forward towards their goal of predicting what healthcare plans a person will need. Specifically, I drilled down into the kinds of features that are predictive of healthcare expenditure and created a model of healthcare expenditures. HealthSherpa will leverage this information to reduce the list of plans they show to customers. 

## **The project before the project.**  
I am new to the healthcare market having been in school or working for the government for the past forever. While I expected there to a lot of information contained in each plan, I idea about the volume of plans I would have to choose from. To understand the volumne of plans available to the average person, I found a publicly available dataset on data.healthcare.gov with data on healthcare plans available across the country. Most people are faced with choosing between 20 and 40 plans (see figure below). Some, especially and perhaps unsurprisingly in Florida, are faced with 100+ choices. That is insane. It illustrates the need for a company like HealthSherpa to help customers wade through the plan space.

![plancount](https://cloud.githubusercontent.com/assets/11904975/10254661/f58283a6-68f9-11e5-8f93-d3de3dbaa8a1.png) 

> Number of counties across the country that offer that many plans. 

## **Starting the project. The data.** 
Some serious legwork was done by HealthSherpa to translate a publicly available dataset from SAS and STATA formats into a CSV file. The data comes from the Medical Expenditure Panel Survey (MEPS). It contains data from 2012 on ~38,000 people across 3 survey dates and encompasses ~1,900 variables. The information collected ranges from demographics to health status to healthcare expenditures. What the dataset does not contain is information on the specific plans people are on nor does it contain zipcode (geospatial) information. Omitting specific location information like a zipcode or county keeps the information people provided private. However, the lack of information makes it difficult to leverage census information or to zoom in on plan availability (remember, I already found plan information broken down by county from the government). 

## **Step 2. Finding informative features. (OR perhaps the most important thing I did. Seriously.)**
The column names from the MEPS data were not in plain English (e.g., ‘RTHLTH53’). They required a close reading of a mound of documentation and searching across multiple survey PDFs. The columns contained, for the most part, strings (e.g., ‘2 Female’ in the 'Sex' column). Machine learning algorithms require numbers, not words. Thus, all of the string columns needed to be altered from ‘2 Female’ to 2. 

I opted for a quasi-manual approach using a random forest regressor to get feature importances (I also tried recursive feature selection and forward selection, but this worked best for me). I set a feature importance cut off value and took all features above that line. 

![featimp](https://cloud.githubusercontent.com/assets/11904975/10254597/ae53d0de-68f9-11e5-9d81-cbacff9c77e2.png) 

> Feature importances for the features ranked the most highly..

## **Features. They tell a powerful story.**
The quasi-manual approach I utilized served a more important purpose. The end goal is for HealthSherpa to ask their customers a few quick questions and then link them up with a plan. Let’s highlight ‘few’ and ‘quick’. No one wants to answer a ton of long-winded questions. Nor does HealthSherpa want to make people answer 40+ questions before getting plan recommendations. Using my own eyes as a first-pass filter was, for now, a good way to understand whether HealthSherpa could easily ask someone for this information. 

While it is obvious that having a preexisting condition like diabetes is highly predictive of the care you might need and what you might spend, it is also obvious that the large majority of people looking for a healthcare plan do not have a preexisting condition. How do you predict their healthcare usage? Simply asking questions about how you feel mentally and physically are, it turns out, important in predicting healthcare spending (see ‘Mental Health’ and ‘Physical Health’ in the feature importance graph above and the graph below). This is easy to do and everyone (including people without health problems) is able to provide an answer. 

![hlth](https://cloud.githubusercontent.com/assets/11904975/10254655/f56dc2a4-68f9-11e5-8cdd-c2bef8a07184.png) 

> Basic point, feel bad -> spend more in healthcare. These are all subjective questions about what you think about your mental and physical state. To be a bit more specific, I'll give two examples. For "mental health" people were asked "what is the state of your mental health?" (answers = excellent, very good, fair, poor, very poor). For "calm level" people were asked "In the last month, how calm or peaceful have you felt?" (answers = all of the time, most of the time, some of the time, little of the time, none of the time). 

These features are not only predictive of expenditures, they also help to uncover populations of peoples that spend similarly, but likely do so for different reasons. Education level and poverty level were both highly important features. Being more educated or more impoverished is correlated with spending more on healthcare (see graphs below). However, these two graphs are unlikely to represent similar groups of people and they are unlikely to require the same healthcare plan. It is essential that these populations can be captured and suggested different plans. 

**Sidenote: This result is the precise reason why I started looking for profiles of the different populations within the dataset (see k-means clustering at the end of the post).** 

![grpdiff](https://cloud.githubusercontent.com/assets/11904975/10254652/f56a5b6e-68f9-11e5-81f9-7742778321b3.png) 

> More highly educated people spend more money on healthcare. More impoverished people spend more money on healthcare.   

![wrkflw](https://cloud.githubusercontent.com/assets/11904975/10254660/f5824d14-68f9-11e5-95fc-897c969b8939.png)  

> This is a very simplified workflow. I did my work in Python (IPython notebooks) and used packages like NumPy, seaborn, matplotlib, pandas, and scikit-learn. I started with data intake, cleaning, imputation of missing data, scaling data, and figuring out what sample weighting might be needed. Next, I used a random forest regressor to determine features that were important and to reduce the number of features used in my ensuing models. Then I used a random forest classifier to predict if a person would spend money on a particular healthcare expenditure (tuned the hyperparameters). Last, I used a gradient boosted regression tree to predict how much money a person would spend (tuned the hyperparameters). 

**Sidenote:** the of interest expenditures that I analyze using this process include office-based (including primary care physician), ER, prescription drugs, home health, and inpatient. However, to constrain the story and the modeling, I’m going to focus primarily on what I found for office-based visits.

## **Moving towards modeling expenditures. Preprocessing.**
![expnd](https://cloud.githubusercontent.com/assets/11904975/10254653/f56a7068-68f9-11e5-91b5-f0d8faa517c9.png)

> Number of people who reported expenditures for each category. 

1. Missing data imputation. A lot of data reduction occurs for some types of expenditure (see above). To maintain a dataset of a reasonable size, I filled in missing feature data with the median response (using scikit-learn’s Imputer function). I settled on the median because the data is often highly skewed between groups in a way that using the mean, which is pulled far from the true center of the data, would not make sense. 

2. Standardization. Much of the data is categorical (ordinal) some is continuous (e.g., Age & BMI). Some models are less robust against features of different scales (e.g., Logistic regression). Having the feature space standardized can help protect against having one feature dominate the prediction. I did this using scikit-learn’s StandardScaler function. 

3. Dummy variable creation. Some of the data is categorical (nominal), which some models will treat as continuous and therefore infer categories that are closer in their number label are actually closer to each other. For example, in the case of marriage status, if Married = 1, Single = 2, Widowed = 3, then some models would infer that Married people are more like Single people than they are like Widowed people, which is entirely not correct and the assigned number labels are meaningless. However, dummy variable creation (which I used scikit-learn’s OneHotEncoder tool to do) vastly increases your feature space (in the above example, 1 Marriage Status feature would become 3 features - Married, Single, Windowed). An increase in feature space can lead to a decrease in model predictiveness. For a logistic regression model, doing dummy variable creation is very important. For a random forest regression model, it is not particularly important. In the course of doing my modeling, I switched to ensemble classifiers like random forests and gradient boosted regression trees and thus abandoned doing dummy variable creation (and immediately improved model performance). 

## **Model – part 1. Will you spend money?**

**Random forest classifier (RFC).**
Why choose RFC?

1. Can deal with categorical and continuous data
2. More robust against overfitting than decision trees
3. Runtime is fast
4. Able to deal with unbalanced and missing data (though I found that unbalanced classes were poorly predicted)
5. Aren’t troubled by noisy features
6. Can deal with non-linearities in the data
7. Non-parametric model 
8. Provide easy to understand results (unlike a neural net)

What to think about: Even though RFCs are more robust to overfitting, it is still an issue. Additionally, it may not be feasible to utilize this model in the real world as using a large number of trees in the model can make the algorithm slow to run.

![rfc](https://cloud.githubusercontent.com/assets/11904975/10254659/f57e49f8-68f9-11e5-8d46-1643b5c6e74c.png) 

> Performance of the random forest classifier. 

What else did I try: started with a very simple logistic regression, but it performed a bit worse and required extra feature manipulation steps. Though I may re-visit it in the future. 

## **Model – part 2. How much money money will you spend?**

**Gradient boosted regression tree (GBRT).**
Why choose GBRT?

1. Can deal with categorical and continuous data
2. More robust against overfitting than both decision trees and random forests
3. Able to deal with unbalanced and missing data (though I found that unbalanced classes were poorly predicted)
4. Aren’t troubled by noisy features
5. Can deal with non-linearities in the data
6. Non-parametric model 
7. Provide easy to understand results (unlike a neural net)

What to think about: A problem inherent to any sort of decision tree is overfitting. Use a deviance plot to show the training and testing error vs. number of trees used to understand the point at which adding more trees results in overfitting. GBRT is generally more robust to overfitting because you have a lot of parameters at your fingertips to control overfitting. 

![gbrt](https://cloud.githubusercontent.com/assets/11904975/10254657/f56f4e3a-68f9-11e5-90af-b9cd4e8fa15d.png)  

> Performance of the gradient boosted regression tree. 

What I tried: I initially started with a multi-class logistic regression, then a random forest, in the end GBRT worked the best.

>**Summary:** A lot of bang for my buck was gained (in order of importance) through thoughtful feature reduction, imputation of missing features, dealing with class imbalances (for categorization only), and using hyperparameter tuning. However, model performance could be improved possibly through additional rounds of feature selection and the addition of more data. 

>**Fun fact:** A negative r-squared means your model is predicting worse than simply predicting the average (a corollary to being at chance in classification). Before feature reduction, this was a reality for me and some predictions. 


## **Pet project.** 
Both highly educated and impoverished people spend more on healthcare. Those two populations, while showing similar trends, likely require different healthcare plans. Understanding the subprofiles that exist in this dataset may provide some added insights about the plans you might suggest to one population compared to another. 

![kmmod](https://cloud.githubusercontent.com/assets/11904975/10254658/f57de2a6-68f9-11e5-84b1-183b65193401.png) 

> Workflow for doing the k-means clustering.  

The 4 cluster k-means model capture two obvious groups (see below).     
1. The youngest population with lowest BMI who felt "good". They spent the least on healthcare.     
2. The oldest population with highest BMI who felt "bad". Thay spent the most on healthcare.    

Then there were two clusters that were similar in age and expenditure. However they were differentiated on poverty, BMI, and how they felt.    
3. Higher BMI, less impoverished, feel "bad".    
4. Lower BMI, more impoverished, feel "good".      
These two groups exhibit how different attributes can be swapped between peopple and result in a similar level of spending. In this case, a high BMI and feeling "bad" were equivalent to being more impoverished.

Moving forward with this, I need to examine if there are groups differences for what they spend their money on. This would be helpful in understanding what type of plan to recommend. For example, does group 3 spend more on prescription drugs and group 4 spend more on ER visits? 

![kmeans](https://cloud.githubusercontent.com/assets/11904975/10254654/f56acfcc-68f9-11e5-88bf-eba22e1a9146.png) 

> A cartoon of the resulting population profiles from the clusters. 
