# CDC-Seasonal-Vaccination-Forecast-Model


The goal of this project is to create Machine learning models with the purpose of forecasting vaccination rate in various metrics within the United States.  This repository is intended for anyone to use provided they can download and install the necessary Python packages.



### **Installation**

All of these files require: Pandas 3.1.1 and up, Numpy 2.5.4 and up, xgboost 3.4.2 (Optional for the XGboost comparison file) and up, Scikit-learn 1.6.1 and up, CatBoost 1.2.10. These libraries can easily be added via -pip install where necessary.



### **Usage**

The goal of these files is to predict the vaccination rate (as a %), of the seasonal influenza vaccine, of a given location using Machine Learning models. These models were trained on previous years' data which has been provided by the CDC. The idea is that, with the model, a user could forecast which location has the lowest vaccination rate and boost it prior to the date of prediction through advertising and community outreach. Currently, the only model available in this repository is the "CDC County CatBoost Model' file which encompasses the CatBoost Model. The file does contain a functional model but it also includes general information about the model such as error, RMSE, and feature importance which is information not necessary for the average user. Additionally, the file ONLY forecasts the 3 counties with the lowest vaccination rates. In time, I hope to create more files which either request a county from a user and provide the vaccination rate, or a state from a user. Additionally, within each file, there are directions to follow as instructions.
While the link is provided in each file, the link to CDC data used for training the model can also be found here:

https://data.cdc.gov/Flu-Vaccinations/Influenza-Vaccination-Coverage-for-All-Ages-6-Mont/vh55-3he6/about_data


### **Data**

The data itself is provided by the CDC and contains the following fields:Vaccine, Geography Type, Geography, FIPS, Season/Survey Year, Month, Dimension, Dimension Type, Estimate (%), 95% CI (%), Sample Size. 'Vaccine' is the the name of the vaccine the data is recording and will be filtered out to "Seasonal Influenza" as that is the vaccine of interest. 'Geography Type' is the classification of geography and will be set to "State/Local Area" or "County" depending on the file. 'Geography' is the name of the county or state in question. FIPS is a unique identifier the CDC assigns to locations and is concated with county to yield a unique county field because there are duplicate county names. 'Season/Survey Year' is the Year the estimate takes place. 'Month' is a numerical value 1-12 assigned to the corresponding month. For County, the value is only 1 meaning all the estimates took place in January. Dimension is the demographic of the sample size measured to yield the estimate. There is some location data here which is filtered out. Dimension Type is the age group of the sample size. Estimate is the midpoint between the upper and lower bracket of the 95% Confidence Interval which is a range of values calculated from sample data that is likely to capture an unknown population parameter, in this case, the estimate of the vaccine. Lastly, the Sample size field is the number of people who were questioned.



### **Methods**
Firstly, In order to forecast a future value based on year, it is necessary to conduct a time series forecast model. Additionally, because the Geography field has high cardinality (>250 unique values used in a categorical field), the options for **optimal** model selection become limited. After cleaning the data, the fields left are Geography, FIPS, Season/Survey Year, and Estimate (%). The geography fields are further concatenated into a unique "county" field. Lastly, because this is a supervised time series forecast, the dataset needs to be ordered by increasing year and a lag field needs to be added to track the change in values between years.

Given that it is necessary to conduct a time series analysis on high cardinality categorical data, an appropriate model must be selected. Because this is a time series analysis, the model needs to undergo supervised learning. The goal of this model is to predict a continuous number from data which means it needs to be a regression based model. Additionally, the dataset itself is relatively small with a number of data points less than 50,000. With this in mind, the optimal models for this purpose are: LightGBM, CatBoost, Generalized Linear Mixed Model (GLMM), and Neural Networks. LightGBM and Neural Networks perform optimally with larger datasets (>100000 and 1m+ data points respectively). Because CatBoost uses target encoding with time-ordered permutation smoothing to deal with high cardinality, CatBoost is selected (GLMM gravitates towards mean due to random effects shrinkage).

CatBoost is a	model which uses sequential symmetric decision trees with target statistics. The parameters for the model are thus: 1000 iterations to ensure enough iterations are conducted, a small learning rate of 0.03 to ensure the iteration are somewhat meaningful but not too meaningful per iteration, a depth of 6 to ensure each tree does excessively branch, RMSE and MAE to govern how wrong and right the model is and lastly CPU as the core processing unit as opposed to GPU.

The model is trained on all but the last year of data (the first year is lost due to shift not existing for that year) and is tested on the last year. After testing the model, it is found that the model runs 221 iterations prior to reaching its best iteration and stops conducting more iterations to prevent over fitting to the training data. Additionally, the RMSE and MAE are found to be 3.59 and 2.86 respectively while the test r2 is found to be 0.67.

RMSE and MAE values vary depending on the number being measured. Considering that the metric evaluated is a percentage which ranges from a value between 0-100, a Root Mean Square Error of 3.59 and a Mean Absolute Error of 2.86 are relatively small, especially considering the number of data points per county. The r2 value ranges from 0 to 1 where a value of 1 accounts for all variability on data(IE perfect prediction). While an r2 of 0.67 can seem low, when considering that the value measured is related to Social Sciences/Psychology/Marketing, 0.67 is quite a good value to have for an r2(a good r2 for those categories is widely considered to lie between 0.3 to 0.5).

This model can also be evaluated by comparing the RMSE value with other less optimal models. The models compared to the CatBoost model are: Scikit-Learn's Linear Regression Model, Lasso Regression Model, and Support Vector Machine Regression Model. The RMSE values of those models are 3.78, 3.61, and 4.52 respectively. Comparing RMSE values shows that the CatBoost RMSE value of 3.59 is lowest, meaning it is the model with the lowest error IE the best model. The CatBoost RMSE can also be compared to another ensemble tree model with built in regularization, XGBoost. In comparing those RMSE values, a value of 3.78 is found for the XGBoost model which is higher still than the CatBoost model.

In short, it appear that the CatBoost model has low error and yields the lowest error when compared to other models.



### **Contributing**

While this is a personal project, the hope is that this model can be used by others to try and boost vaccination rate in the US. For contribution, assistance with bug reporting and model accuracy would be nice. If there are better parameters for any of the models that will lower the error or if there are any errors or bugs when running any of the files, reach out to me, the creator via email at yousufaqeel7@gmail.com; The next steps for this project are:

*Creating a model for State vaccination rates
*Creating an export for both those models so that they can be used in any file anyone so chooses

If there's anything else that should be known, feel free to reach out at yousufaqeel7@gmail.com.



### **License**

While there are no specific licenses that are required for these files besides the ones necessary for any compiler needed to run the files, the data used to train the models is from the CDC and thus is subject to their terms and conditions.



### **Support/Contact**

If there are any questions about any of the files in this repository or any errors in any of the files or any requests for improvements, reach out to me directly at yousufaqeel7@gmail.com.



### **Acknowledgements/ Credits**

I would like to thank the CDC for providing data necessary for these files to exist and I would like for these files to be used for the betterment of society because it is my belief that everyone should be vaccinated where possible.
