Readme:

Overview:

In this practical application, the goal is to compare the performance of the classifiers we encountered in this section, namely K Nearest Neighbor, Logistic Regression, Decision Trees, and Support Vector Machines. 
I will utilize a dataset related to marketing bank products over the telephone.

Data information:
Our dataset comes from the UCI Machine Learning repository link. The data is from a Portugese banking institution and is a collection of the results of multiple marketing campaigns. 
The data link is as follows:
https://archive.ics.uci.edu/dataset/222/bank+marketing.
The dataset is for 105754 campaigns - telemarketing phone calls to sell long term deposits. 
The data is from May 2008 to June 2013, in total of 52944 phone contacts. 
The results are either the client choose to buy the long term desposit- 'yes' or not buy 'no'.¶

Data cleaning:
1. Check to find there is no NULL values.
2. Some columns with cateogory type have "unknown" category.  In this exercise, I removed the rows with "unknown" 
3.   Removed "duration" column: last contact duration, in seconds (numeric). Important note: this attribute highly affects the output target
 (e.g., if duration=0 then y='no'). Yet, the duration is not known before a call is performed. 
Also, after the end of the call y is obviously known. 
Thus, this input should only be included for benchmark purposes and should be discarded if the intention is to have a realistic predictive model.

Understand the task:
The business objective is to predict if the client would accept the long term CD offer.  
So the data is split to features (X) and y( accept  (Y) and reject (N))

Engineering the features:
The feature columns have both object types and numerical types.  So I used make_column_transformer to transform the original data:
1.  For object type, use OneHot Encoder to change to numerical
2. For numberical type, use StandardScaler() to scale the data
For the client outcome:  I used Label encoder to map the 'no' to '0' and 'yes' to 1.
After the transformation, need to add back the original column names.


Train/ test split:
I used test size = 0.2 and random_state = 42


Baseline Model:
I used Dummy Classifier to establish the baseline.  The baseline accuracy is 0.87 for both train and test.  Visualize with ROC curve.  There is no discrimination for this dummy model.  The curve is a straightline.

Simple Model comparison:
I compared the LogisticRegression model, KNN, Decision Tree and SVM. Also used Seaborn to plot the confusion matrix and visualize with ROC curves for each model with mat
 The summary table is as follows:

              train score	test score	   average fit time
model			
Logistic 	0.89	0.89	0.1856
KNN	0.90	0.87	0.0063
Decision 	0.99	0.82	0.1045
SVC	0.90	0.89	51.6925


By comparing fit time and score, I think Logistic model is the best choice,which has the highest test score and reasonable fit time.  By comparing the ROC curves, it matches the score conclusions
 SVC' s test score is the same as Logistic Regressor.  But it has much longer fit time. From the confusion matrix, the SVC and Logistic model are similar.

Optimizing the Model 
1. Use Sequential Feature Selection to refine the feature selection.  
The SFS is very time consuming and requires a lot computing power.  I limited the SFS 'tol' parameter to 0.01 and CV =3, hoping to shorten the time.
I think SVC is still too long and I have to give up on SVC's search.

a. For Logistic Regression, I used tol = 0.00001 and tol = 0.01, it looks like the features selcted are different.  For the tol = 0.00001, there are 12 features selected.
But for tol =0.01, there are only 1 feature selected.  Time spent is about 6 s.
######
Selected feature names with tol = 0.00001: Index(['job_entrepreneur', 'job_housemaid', 'education_basic.9y',
       'education_high.school', 'contact_telephone', 'month_jul', 'month_mar',
       'day_of_week_fri', 'poutcome_failure', 'poutcome_success', 'campaign',
       'nr.employed'],
      dtype='object')
Selected feature names with tol = 0.01: Index(['poutcome_success'], dtype='object')
####
The "gender" feature isn't important, because it isn't in the selected columns.  The feature cannot change the accuracy even by 0.00001.

b. For Knn + SFS
The feature selected is " PDAY" ,w hich is dfferent from Linear Regression. But the SFS time is very long: 266.67S

c. For DecisionTree+SFS
The features selected are 'poutcome_success', 'nr.employed'.   'poutcome_success' is the same as Logistic model.  But there is one more feature selected. Reasonable search time : 1.71 s

d. SVC+SFS
The search time is too long for my laptop.  I used the previously decision tree features selected instead.


2. Refit the data with features selected and optimize with hyperparameters.

a.  For logistic model, build the GridSearchCV with the parameters below

param_grid = {
    'penalty': ['l1', 'l2'],
    'C': [0.01, 0.1, 1, 10],
    'solver': ['liblinear'], 
  }

b. For Knn, build the GridSearchCV with the parameters below

param_grid = {
    'n_neighbors': [3, 5, 7, 9],
    'weights': ['uniform', 'distance'],
    'metric': ['euclidean', 'manhattan']
}

c. For decision tree,build the GridSearchCV with the parameters below
param_grid = {
    'criterion': ['gini', 'entropy'],
    'splitter': ['best', 'random'],
    'max_depth': [None, 10, 20, 30],
    'min_samples_split': [2, 10, 20],
    'min_samples_leaf': [1, 5, 10],
    'max_leaf_nodes': [None, 10, 20, 30],
    'min_impurity_decrease': [0.0, 0.01, 0.1],
    'ccp_alpha': [0.0, 0.01, 0.1]
}

d.  For SVC, build the GridSearchCV with the parameters below
svc_param_grid = {'C': [0.1, 1, 10, 100]}

Results after optimization
	train scor test score	SFS search time
model			
Logistic	0.89	0.88	6.02
KNN	0.89	0.88	266.67
Decision	0.89	0.88	1.71
SVC	0.89	0.88	>600

Summary:

After optimization, looks like the test scores are about the same for all models.  But the feature selection time are long.
Comparing to simple model, I don't see any advantages to do the feature engineering to select specific features.

If we use simple LinearRegression model, the results are good and with reasonable computing time.