Debug a Fab transfer yield delta issue

Haiying Yu

#### Executive summary
This project is to debug a fab transfer yield delta issue.  Fab B shows ~0.7% lower yield (higher Bin 11 failure ~0.7%) than the mother Fab A.
Fab A and Fab B FT yield data and failure bin data are compared.
Exploration of the data shows some missing values in PCM (process control monitor) data and a lot of information columns which aren't PCM data.
PCA with optimized number of components (WAT parameters) are used for feature analysis.
Ridge and Lasso regression models are used with Pipeline, GridSearch CV to optimize the hyper-parameters of the models
Top 10 WAT parameters which contributed to the BIN 11  fallouts are derived from the optimized Ridge and Lasso model.


#### Rationale
Yield is important for the cost of goods.  Higher yield means lower cost. In addition, yield is also an indicator of the quality.  Higher yield means higher quality.
In a fab transfer project, one of the success criteria for a fab transfer is to ensure the yield is comparable between the mother fab and new fab.

#### Research Question
What caused the higher yield loss in Fab B.
What are the top 10 WAT parameters contributing to this yield delta. WAT is a good indicator for fab process.

#### Data Sources
Fab A and B  wafer WAT and Final test yield / BIN data from my company. 

#### Methodology
Feaure engineering by using regression models.
Due to the large numbers of WAT paramters, use PCA to reduce the number of features. Optimize the PCA components to achieve 95% explained variance.
Compared 5 components of PCA with Ridge vs 5 components of PCA with Lasso model by using Pipeline.
Then optimize both models by using GridSearchCV on both Ridge / Lasso hyperparemeters and PCA components 

#### Results
Ridge model shows lower MSE than Lasso model.
Summarized the top 10 WAT parameters from Ridge models as follows:


Feature	Coefficient
Feature	Coefficient
Rs_HRI	0.022375
Rs_PODRPO2	0.020197
Cmim40X50	0.019557
Iof_PTM9A	-0.019451
Rs_PPORPO2	0.018970
Iof_P45L	-0.017601
Rs_NODRPO2	0.016797
Rs_NPORPO2	0.016650
Rs_HRIserp	0.013830
Rs_NW_STI	0.013069

Feature	Coefficient
Rs_PODRPO2	0.012936
Iof_PTM9A	-0.010889
Cmim40X50	0.010483
Rc_TV1	-0.010032
Rs_NODRPO2	0.009208
Rs_HRI	0.008754
Iof_P45	-0.008469
Rs_NW_STI	0.008355
Rs_NPORPO2	0.008239
Rs_PPORPO2	0.007339

Lasso model top 10 features match with Ridge Model top 10 well (the sequences are different and only 2 parameters are different). 
I think the WAT parameters are highly correlated with each others. So it is better to use Ridge model.

Hold out validation of the test MSE shows Lasso = 0.043 and Ridge = 0.027  Ridge is also better than Lasso.

#### Next steps
Futher debug reveals that FT pass / fail yield and bin failure rate might not be the best way to debug this issue, because the FT test instability was found due to ringing issues.
Final test (FT) was changed from pass / fail to POR circuit trip point parametric data collection.
Based on the new trip point data, redo the analysis using random forest regression and Ridge model with GridSearchCV + feature permutation to find the most relevant WAT parameters.

#### Outline of project
https://github.com/Haiying-Yu/AI-ML-Class.git
File name " PCM-FabB-Yield.ipynb

##### Contact and Further Information
Haiying Yu
Haiyingy@gmail.com