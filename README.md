# Gora Liquidation Prediction Project

## About The Project
This project is aimed at predicting the Total Liquidation to Total Borrow Ratio within the Gora Lending dataset. The goal of this analysis is to assess the likelihood of account defaults, thus aiding lending platforms in managing risks and fostering a healthier DeFi ecosystem by preempting potential defaults.

## Project Steps Overview

### 1. Importing Libraries and Datasets
Primary Python libraries were imported to handle and explore the Gora Lending Data dataset. While Giza libraries weren't initially necessary, they were included for potential future use.

### 2. Data Cleaning
The dataset was examined for missing or duplicate data points. Fortunately, it was clean and required no imputation which streamlined the preprocessing steps.

### 3. Baseline Model Creation
Five regression models (Random Forest, Gradient Boosting, Ridge Regression, SVM, and Linear Regression) were created as benchmarks to guide further analysis and potential data improvement through enrichment or compression. The Mean Squared Error (MSE) and Root Mean Squared Error (RMSE) results are presented below:

![image](https://github.com/John-Embate/Gora-Liquidation-Prediction/assets/72902619/72d9b913-b7ed-4480-963f-ccb963bdc1d1)

The R-squared scores for each model are also included:

![image](https://github.com/John-Embate/Gora-Liquidation-Prediction/assets/72902619/f68477c5-cbed-492e-93ca-2fe33caa9ffc)

### 4. Data Exploration and Visualization
This section analyzed the distribution and presence of outliers in each variable. Feature importance from the baseline Random Forest model was used to prioritize the top 15% of variables for exploration using KDE plots and box plots (further details available in the project's Jupyter Notebook). The following are the KDE plot and box plot of the top 15% variables based on feature importance of our baseline Random Forest Model:
![image](https://github.com/John-Embate/Gora-Liquidation-Prediction/assets/72902619/6ff82242-3d40-470c-9788-a8e28672fb86)
![image](https://github.com/John-Embate/Gora-Liquidation-Prediction/assets/72902619/69546563-b723-4027-b356-9a9705e5297a)
![image](https://github.com/John-Embate/Gora-Liquidation-Prediction/assets/72902619/a8f1f598-f10f-4ce2-9872-61b5566fa5d6)
![image](https://github.com/John-Embate/Gora-Liquidation-Prediction/assets/72902619/375fda86-c610-4255-8501-91a62ef291ec)
![image](https://github.com/John-Embate/Gora-Liquidation-Prediction/assets/72902619/7b10a5f5-2ea7-4d79-ad84-f6ab4391fe9a)

The visualizations revealed a significant number of outliers, impacting data distribution. To address this, data entries exceeding a 10% outlier threshold were removed. This approach aimed to balance data integrity and size, ultimately reducing the dataset from 783k to 506k entries.

### 5. Data Preprocessing
This involved two main sub-steps:
   - **Manual Preprocessing**: Unnecessary variables, such as wallet addresses, were removed. The token_borrow_mode feature was transformed to maintain its value for the top eight tokens (based on value counts) and converted to "Others" for the remaining entries. This was done to facilitate one-hot encoding. Datetime variables were converted to their appropriate data types, with date and time components extracted. These original features were then dropped before cyclical feature transformation of the extracted hour data.
   - **Top 15 Features**: The feature importance scores from the baseline Random Forest model are illustrated in a graph. 
![image](https://github.com/John-Embate/Gora-Liquidation-Prediction/assets/72902619/b9caa084-c347-4ee7-b779-af4718f7c104)
The table below highlights the top 15% most important features and their corresponding importance scores.
![image](https://github.com/John-Embate/Gora-Liquidation-Prediction/assets/72902619/d5b8680d-d4d6-48fd-bec9-57d181e63b84)


### 6. Train-Validation-Test Data Split
The data was split into training, validation, and testing sets. The training data was further divided into `stack_train` and `stack_val` sets for creating additional baseline models whose predictions would be used in the final model. A pipeline was implemented to standardize non-top 15% features and compress the results using PCA (to retain 95% of the original data's variability). A column transformer was then created, utilizing the pipeline for data transformation and a one-hot encoder for the `token_borrow_mode` feature.

The results of the baseline model training and their corresponding scores on the training, validation, and testing datasets are provided below:
![image](https://github.com/John-Embate/Gora-Liquidation-Prediction/assets/72902619/ee1cbc24-6a96-4501-af83-599b62bc27a4)
![image](https://github.com/John-Embate/Gora-Liquidation-Prediction/assets/72902619/11cb82b4-1cac-4a33-85b7-3348c1d06d58)

The R-squared scores for each model are also included:
![image](https://github.com/John-Embate/Gora-Liquidation-Prediction/assets/72902619/b563670d-5238-4234-bb0e-b63bd76c68ae)

Due to its poor performance and high computational cost for training and prediction, the SVM model was excluded from final model training.

### 7. Final Model Training
For the final model training, the `X_stack_val` dataset was augmented by incorporating predictions from the previously trained Random Forest, Gradient Boosting, and KNN Regressor models. An XGBoost Regressor and another Random Forest Regressor were then trained and compared for final model selection. The Random Forest Regressor ultimately yielded superior performance.

### 8. Validation and Hyperparameter Tuning
Randomized Search CV was employed for hyperparameter tuning of the final model, as Grid Search CV would be computationally expensive.

### 9. Validation and Test Scoring
The model's performance was evaluated using the validation and test datasets. Similar results on both sets indicate that the model likely did not overfit.

### 10. Saving Models and Transformation Pipelines
The final model and transformation pipelines were exported for future use and potential deployment. They were then re-imported to verify proper scoring and ensure no issues arose during the export/import process.

### 10. Predicting Unlabeled Test Data
An unlabeled test dataset from Gora's evaluation data was used for prediction. Manual preprocessing was performed before applying the transformation pipelines. Predictions were generated and exported in CSV and NPY formats.
