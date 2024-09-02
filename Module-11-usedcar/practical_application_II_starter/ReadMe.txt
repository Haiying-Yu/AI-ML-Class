Readme:

Background:
In this assignment, we need to evaluate what drives the price of  used cars. The data are from Kaggle.
The provided dataset reduced the 3M cars to 426K cars to ensure speed of processing. 
The goal is to understand what factors make a car more or less expensive. 
As a result of the analysis, I will provide clear recommendations a used car dealership -- as to what consumers value in a used car.

CRISP-DM FrameWork

1. The first step is the business understanding.
Base on region/price/year/manufacturer/model/condition/cylinders/fule/odometer/title status/VIN/drive/size/type/paint_condition/state features provided to predict used car pricing
Select the approriate model to minimize prediction errors.

2. Data Understanding
## load data to a dataframe to check the data information
## check the columns and datatype with info()
## check statistics with describe()
## check what the data look like by using head()

There are total 18 columns and 426888 entries with both numerical data and object - category data.
There are missing data with NaN as well.

3. Data Preparation
## Data cleaning by dropping all NaN data
 # check to see if any null values in any columns by using isnull().sum().  Get zero NaN after droping NaN
# use nunique() function to check all object columns to see how many unique categories for each column.  The following is the list
region            392
state              51
manufacturer       41
paint_color        12
fuel                5
model            5139
condition           6
size                4
cylinders           8
title_status        6

# Drop ID and VIN, because these will not affect pricing. 
# Replace cylinder column cyliner with empty space and leave only the quantities of cyclinders
# convert cylinder column from object type to numeric type.
# Check cleaned car dataframe with head()

Data preprosessing:
## Use make_column_transformer to convert all objects using OneHotEncoder, then standardize the numerical data with standardscaler
## Use fit_transform to transform the car dataframe to transformed_car dataframe
## prepare the feature dataset as X_car by dropping "price" and use " price" as the y_car target in the model

4. Modeling
## Split the cleaned X_car and y_car with train_test_split to train and test

Model - 1: 
# Build a pipeline with PCA to reduce the features to 5 and Ridge Model with default alpha value
# checked the MSE of train and test data
### Checked the explained variance ratio of each principal component and cumulative explained variance to see how well the PCA components represent the original data variance.
## From the explained variance, it looks like it is only 36% of the original data with PCA=5, whihc is low

Model -2
#Add in GridSearchCV with Alpha from 0-4 with 5 steps to see which alpha gives the best Ridge model
# Best Alpha is 4.0.
##  But there is no difference from the MSE with Alpha = 1.  I think the Alpha range might not be the best range. 
## But due to the computation power, I cannot do more alpha.

Model-3
# Use Lasso model with PCA =5
# Get higher MSE of test data

Model -4
# Increase PCA to 100 and plot the cumulative explained variance.  
# In order to achieve >80%, then we need 77 components of PCA.  Ideally we would like to achieve > 95%, but my computation power doesn't allow me to go further
# Settle down at Alpha = 1 and PCA = 100.  This isn't the optimal setting. Ideally, would like to achieve >95% and do a search on Alpha.
## If my computation power is enough, I would like to do GridSearchCV on  Ridge Alpha with PCA explained variance > 95% .  
## With this model, I got lowest MSE I can achieve in the four models (0.83)
## Used the model 4 as the best model for now.  Converted back the model coefficients (PCA and Ridge) back to the original data coefficients.
## The method is to extract the PCA coefficients from the pipeline by using  .named_steps.components_ 
## Then extract the ridge model coefficients from te pipeline by using .named_steps.coef_
## use np.dot to transform back to the original feature coefficient
# Sort the DataFrame by the absolute value of the coefficients for top 10 most inflential factors

5.  Validation

Checked all 4 models test MSEs.  The lowest is PCA = 100 and Alpha =1.  This is simple validation, which is good for large set of data.
## If my computation power is enough, I would like to do GridSearchCV on  Ridge Alpha with PCA explained variance > 95% .  The model isn't fully optimized.

## By increasing the PCA components from 5 to 100 and with Ridge model, the MSE of test was reduced from 0.87 to 0.84.
Further increasing PCA could have better fit. In addition, further optimizing Alpha could have better fit. 
But due to my computer computing power, cannot do Alpha search with PCA = 100. It takes too long. Evaluted the top 10 features which contributed to the model the best with PCA = 100 and Alpha =1

## Redisplay the top 10 most influential factors from model 4 

6. Deployment

summarize per model findings and report to the customer

Top 10 features impacing the pricing
With >80% explained variance simulation, the top 10 features which impact the pricing are as followings

Feature	Coefficient
fuel_diesel	0.586952
transmission_other	-0.546643
state_ok	-0.467597
odometer	-0.411145
transmission_manual	0.361876
type_convertible	0.360137
type_wagon	-0.340713
region_tulsa	-0.331704
fuel_gas	-0.32409
state_ak	0.270128
###

From the original coefficient analysis, the positive impact features are as follows:

Diesel_Fuel, manual transmission, Convertible type,state_ak. The importance are in sequence of the descriptions. The price is positively correlated with the features. To achieve higher pricing, then stock the inventory with the above 4 features.

The negative impact features are other transmission, state_ok, odometer, wagon type, tulsa region,fule_gas. The importance are in sequence of the descriptions. he price is negatively correlated with the features. To achieve higher pricing, then don't stock the inventory with the above features.





