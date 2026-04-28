# Problem Solution Pipeline


```python
import pandas as pd 
import numpy as np
import os
from pymongo import MongoClient
import logging
from collections import Counter
import matplotlib.pyplot as plt

from sklearn.model_selection import train_test_split
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler
from sklearn.preprocessing import OneHotEncoder
from sklearn.compose import ColumnTransformer
from sklearn.impute import SimpleImputer
from sklearn.linear_model import LogisticRegression
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import classification_report, confusion_matrix, accuracy_score
from sklearn.model_selection import StratifiedKFold
from sklearn.model_selection import GridSearchCV
from sklearn.metrics import roc_auc_score, average_precision_score
from sklearn.model_selection import cross_validate

# import warnings
# warnings.filterwarnings("ignore", category=UserWarning)
```


```python
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')
```

## Data preparation: query MongoDB to put data into a dataframe


```python
# In terminal, set the environment variable for MongoDB URI 

# export MONGO_URI="mongodb+srv://____REPLACEWITHUSERNAME____:____REPLACEWITHPASSWORD____@cluster0.____REPLACEWITHCLUSTER____.mongodb.net/?retryWrites=true&w=majority"
# export MONGO_URI="__YOUR_MONGO_URI_HERE__"
```


```python
# uri = os.getenv("MONGO_URI") # Use environment variable for MongoDB URI
client = MongoClient(uri) # Connect to MongoDB Atlas using the URI
db = client["diabetes_readmission"] # Access the database
collection = db["encounters"] # Access the collection

# Test connection
try:
    client.admin.command('ping')
    print("Connected to MongoDB Atlas successfully")
    logging.info("Connected to MongoDB Atlas successfully")
    
except Exception as e:
    print("Connection failed:", e)
    logging.error(f"Connection failed: {e}")
```

    2026-04-28 09:39:53,337 - INFO - Connected to MongoDB Atlas successfully


    Connected to MongoDB Atlas successfully



```python
# Fetch all documents from the collection
cursor = collection.find() 

# Convert to DataFrame
encounters = pd.DataFrame(list(cursor))

print(f"Loaded {len(encounters)} rows with {len(encounters.columns)} columns")
print(f"Columns: {list(encounters.columns)}")
logging.info(f"Loaded {len(encounters)} rows with {len(encounters.columns)} columns")
```

    2026-04-28 09:40:08,869 - INFO - Loaded 66221 rows with 23 columns


    Loaded 66221 rows with 23 columns
    Columns: ['_id', 'patient_nbr', 'race', 'gender', 'age', 'time_in_hospital', 'num_lab_procedures', 'num_procedures', 'num_medications', 'number_outpatient', 'number_emergency', 'number_inpatient', 'number_diagnoses', 'change', 'diabetesMed', 'admission_type_desc', 'discharge_disposition_desc', 'admission_source_desc', 'readmitted_30day', 'diagnoses', 'num_diagnoses', 'medications', 'num_medications_taken']



```python
print(f"Original DataFrame shape: {encounters.shape}")
```

    Original DataFrame shape: (66221, 23)



```python
# Data Cleaning and Preprocessing to prepare data from MongoDB 

# Transform array fields (diagnoses and medications) since ML models can't handle arrays directly

# 1. 
# Transform diagnoses array
# Create binary columns for top diagnoses to capture specific conditions that may be predictive of readmission risk
# Extract all diagnoses from all documents
all_diagnoses = []
for diag_list in encounters['diagnoses']:
    all_diagnoses.extend(diag_list)

# Get top 15 most common diagnoses
diagnosis_counts = Counter(all_diagnoses)
top_diagnoses = [diag for diag, count in diagnosis_counts.most_common(15)]
print(f"Top 15 diagnoses: {top_diagnoses}")

# Create binary columns for each top diagnosis
for diag in top_diagnoses:
    # Clean column name (remove dots and special characters)
    col_name = f'diag_{diag.replace(".", "_")}'
    encounters[col_name] = encounters['diagnoses'].apply(lambda x: 1 if diag in x else 0)

# Drop the original diagnoses array column
encounters = encounters.drop('diagnoses', axis=1)
logging.info("Transformed diagnoses array into binary columns for top diagnoses")

# 2.
# Transform medications array
# Create binary columns for top medications to capture specific treatments that may be predictive of readmission risk
# Extract all medications from all documents
all_medications = []
for med_list in encounters['medications']:
    all_medications.extend(med_list)

# Get top 10 most common medications
med_counts = Counter(all_medications)
top_medications = [med for med, count in med_counts.most_common(10)]
print(f"Top 10 medications: {top_medications}")

# Create binary columns for each top medication
for med in top_medications:
    # Clean column name (replace hyphens with underscores)
    col_name = f'med_{med.replace("-", "_")}'
    encounters[col_name] = encounters['medications'].apply(lambda x: 1 if med in x else 0)

# Drop the original medications array column
encounters = encounters.drop('medications', axis=1)
logging.info("Transformed medications array into binary columns for top medications")

# Handle missing values in categorical fields by replacing 'Not Mapped', 'Not Available', and 'NULL' with 'Unknown'
encounters['admission_source_desc'] = encounters['admission_source_desc'].replace(['Not Mapped', 'Not Available', 'NULL'], 'Unknown')
encounters['admission_type_desc'] = encounters['admission_type_desc'].replace(['Not Mapped', 'Not Available', 'NULL'], 'Unknown')
logging.info("Replaced 'Not Mapped', 'Not Available', and 'NULL' with 'Unknown' in categorical fields")

# Encode age brackets numerically so that they can be used in ML models. We will use the midpoint of each age bracket as the encoded value.
age_mapping = {
    "[0-10)": 5,
    "[10-20)": 15,
    "[20-30)": 25,
    "[30-40)": 35,
    "[40-50)": 45,
    "[50-60)": 55,
    "[60-70)": 65,
    "[70-80)": 75,
    "[80-90)": 85,
    "[90-100)": 95,
}
encounters["age"] = encounters["age"].map(age_mapping)
logging.info("Encoded age brackets as ordinal midpoint values")
```

    Top 15 diagnoses: ['250', '428', '276', '401', '414', '427', '599', '496', '486', '786', '403', '780', '410', '584', '682']


    2026-04-28 09:40:09,484 - INFO - Transformed diagnoses array into binary columns for top diagnoses


    Top 10 medications: ['insulin', 'metformin', 'glipizide', 'glyburide', 'pioglitazone', 'rosiglitazone', 'glimepiride', 'repaglinide', 'glyburide-metformin', 'nateglinide']


    2026-04-28 09:40:09,875 - INFO - Transformed medications array into binary columns for top medications
    2026-04-28 09:40:09,916 - INFO - Replaced 'Not Mapped', 'Not Available', and 'NULL' with 'Unknown' in categorical fields
    2026-04-28 09:40:09,930 - INFO - Encoded age brackets as ordinal midpoint values



```python
encounters.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>_id</th>
      <th>patient_nbr</th>
      <th>race</th>
      <th>gender</th>
      <th>age</th>
      <th>time_in_hospital</th>
      <th>num_lab_procedures</th>
      <th>num_procedures</th>
      <th>num_medications</th>
      <th>number_outpatient</th>
      <th>...</th>
      <th>med_insulin</th>
      <th>med_metformin</th>
      <th>med_glipizide</th>
      <th>med_glyburide</th>
      <th>med_pioglitazone</th>
      <th>med_rosiglitazone</th>
      <th>med_glimepiride</th>
      <th>med_repaglinide</th>
      <th>med_glyburide_metformin</th>
      <th>med_nateglinide</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>69e2f46489db8ef6f8bdd198</td>
      <td>84259809</td>
      <td>Caucasian</td>
      <td>Male</td>
      <td>65</td>
      <td>4</td>
      <td>70</td>
      <td>1</td>
      <td>21</td>
      <td>0</td>
      <td>...</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>69e2f46489db8ef6f8bdd1c2</td>
      <td>96190083</td>
      <td>Caucasian</td>
      <td>Male</td>
      <td>65</td>
      <td>1</td>
      <td>59</td>
      <td>0</td>
      <td>10</td>
      <td>0</td>
      <td>...</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>69e2f46489db8ef6f8bdd1e0</td>
      <td>2780676</td>
      <td>AfricanAmerican</td>
      <td>Male</td>
      <td>35</td>
      <td>3</td>
      <td>44</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>...</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>69e2f46489db8ef6f8bdd1e9</td>
      <td>99378891</td>
      <td>Caucasian</td>
      <td>Male</td>
      <td>85</td>
      <td>1</td>
      <td>37</td>
      <td>0</td>
      <td>13</td>
      <td>0</td>
      <td>...</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>69e2f46489db8ef6f8bdd1f1</td>
      <td>3168720</td>
      <td>Caucasian</td>
      <td>Female</td>
      <td>65</td>
      <td>7</td>
      <td>42</td>
      <td>3</td>
      <td>10</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 46 columns</p>
</div>



# Solution Analysis: Implement a model


```python
# Solution analysis: Implement a model
# For the analysis, I will implement 2 models and compare their performance: a Random Forest regression model and a Logistic Regression model.
# This will allow us to see if a more complex model like Random Forest provides better predictive performance compared to a simpler logistic model, and then choose the final model based on the evaluation.
# The goal is to predict whether a patient will be readmitted within 30 days based on the various features in the dataset. This will help us understand which factors are most influential in determining readmission risk.

# Define target and features
y = encounters['readmitted_30day'] # Target variable
X = encounters.drop(['_id', 'patient_nbr', 'readmitted_30day', 'discharge_disposition_desc'], axis=1) # Features (take away features that may be directly related to readmission or features that are created AFTER the readmission happens)

# Preprocess the data
# Identify numeric and categorical columns
numeric_features = X.select_dtypes(include=['int64', 'float64']).columns
categorical_features = X.select_dtypes(include=['object']).columns

# Define transformers for numeric and categorical features
numeric_transformer = Pipeline(steps=[('imputer', SimpleImputer(strategy='median')), # Impute missing values with median
                                      ('scaler', StandardScaler())]) # Scale numeric features
categorical_transformer = Pipeline(steps=[('imputer', SimpleImputer(strategy='most_frequent')),
                                          ('onehot', OneHotEncoder(handle_unknown='ignore'))]) # One-hot encode categorical features

# Combine transformers into a preprocessor
preprocessor = ColumnTransformer(transformers=[('num', numeric_transformer, numeric_features),
                                               ('cat', categorical_transformer, categorical_features)])

# Train-test split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=888, stratify=y) # Stratify to maintain class distribution in train and test sets

# Pipeline for Logistic Regression
logistic_pipeline = Pipeline(steps=[('preprocessor', preprocessor),
                                    ('classifier', LogisticRegression(max_iter=1000, random_state=888, class_weight='balanced'))]) # Use class_weight='balanced' to handle class imbalance in the target variable, more patient may not be readmitted within 30 days, so we want to give more weight to the minority class to improve model performance on that class

# Train Logistic Regression model
logistic_pipeline.fit(X_train, y_train)
y_pred_logistic = logistic_pipeline.predict(X_test)
y_prob_logistic = logistic_pipeline.predict_proba(X_test)[:, 1] # Get predicted probabilities for the positive class (readmitted within 30 days)

# Evaluate Logistic Regression model
print("\nLOGISTIC REGRESSION — TEST SET EVALUATION")
print(classification_report(y_test, y_pred_logistic))
print("Confusion Matrix:")
print(confusion_matrix(y_test, y_pred_logistic))
print(f"Accuracy:  {accuracy_score(y_test, y_pred_logistic):.4f}")
print(f"ROC-AUC:   {roc_auc_score(y_test, y_prob_logistic):.4f}")
print(f"PR-AUC:    {average_precision_score(y_test, y_prob_logistic):.4f}")

# Cross validation for Logistic Regression
cv = StratifiedKFold(n_splits=5, shuffle=True, random_state=888)
logistic_cv_scores = cross_validate(logistic_pipeline, X_train, y_train, cv=cv, scoring=["roc_auc", "f1", "recall", "precision", "accuracy"])

print("LOGISTIC REGRESSION — 5-FOLD CROSS-VALIDATION")
print(f"ROC-AUC:    {logistic_cv_scores['test_roc_auc'].mean():.4f}  ")
print(f"F1:         {logistic_cv_scores['test_f1'].mean():.4f}  ")
print(f"Recall:     {logistic_cv_scores['test_recall'].mean():.4f}  ")
print(f"Precision:  {logistic_cv_scores['test_precision'].mean():.4f}  ")
print(f"Accuracy:   {logistic_cv_scores['test_accuracy'].mean():.4f}  ")
logging.info("Completed 5-fold cross-validation for Logistic Regression")



# Pipeline for Random Forest 
rf_pipeline = Pipeline(steps=[('preprocessor', preprocessor),
                                ('classifier', RandomForestClassifier(n_estimators=100, random_state=888, class_weight='balanced'))]) # Use RandomForest Classifier for classification task
# Train Random Forest model
rf_pipeline.fit(X_train, y_train)
y_pred_rf = rf_pipeline.predict(X_test)
y_prob_rf = rf_pipeline.predict_proba(X_test)[:, 1] # Get predicted probabilities for the positive class (readmitted within 30 days)

# Evaluate Random Forest model
print("\nRANDOM FOREST — TEST SET EVALUATION")
print(classification_report(y_test, y_pred_rf))
print("Confusion Matrix:")
print(confusion_matrix(y_test, y_pred_rf))
print(f"Accuracy:  {accuracy_score(y_test, y_pred_rf):.4f}")
print(f"ROC-AUC:   {roc_auc_score(y_test, y_prob_rf):.4f}")
print(f"PR-AUC:    {average_precision_score(y_test, y_prob_rf):.4f}")
logging.info("Trained and evaluated Random Forest model")

# Cross validation for Random Forest
rf_cv_scores = cross_validate(rf_pipeline, X_train, y_train, cv=cv, scoring=["roc_auc", "f1", "recall", "precision", "accuracy"])
print("RANDOM FOREST — 5-FOLD CROSS-VALIDATION")
print(f"ROC-AUC:    {rf_cv_scores['test_roc_auc'].mean():.4f}  ")
print(f"F1:         {rf_cv_scores['test_f1'].mean():.4f}  ")
print(f"Recall:     {rf_cv_scores['test_recall'].mean():.4f}  ")
print(f"Precision:  {rf_cv_scores['test_precision'].mean():.4f}  ")
print(f"Accuracy:   {rf_cv_scores['test_accuracy'].mean():.4f}  ")
logging.info("Completed 5-fold cross-validation for Random Forest")

# Grid search for hyperparameter tuning of Random Forest
param_grid = {
    'classifier__n_estimators': [100, 200],
    'classifier__max_depth': [None, 10, 20],
    'classifier__min_samples_split': [2, 5],
    'classifier__min_samples_leaf': [1, 2],
}
grid_search = GridSearchCV(estimator=rf_pipeline, param_grid=param_grid, cv=cv, scoring='roc_auc', n_jobs=-1)
grid_search.fit(X_train, y_train)
print("Best Hyperparameters for Random Forest:")
print(grid_search.best_params_)

# Evaluate best Random Forest model from grid search
best_rf_model = grid_search.best_estimator_
y_pred_best_rf = best_rf_model.predict(X_test)
y_prob_best_rf = best_rf_model.predict_proba(X_test)[:, 1]
print("\nBEST RANDOM FOREST — TEST SET EVALUATION")
print(classification_report(y_test, y_pred_best_rf))
print("Confusion Matrix:")
print(confusion_matrix(y_test, y_pred_best_rf))
print(f"Accuracy:  {accuracy_score(y_test, y_pred_best_rf):.4f}")
print(f"ROC-AUC:   {roc_auc_score(y_test, y_prob_best_rf):.4f}")
print(f"PR-AUC:    {average_precision_score(y_test, y_prob_best_rf):.4f}")
logging.info("Completed hyperparameter tuning and evaluation of best Random Forest model")
```

    
    LOGISTIC REGRESSION — TEST SET EVALUATION
                  precision    recall  f1-score   support
    
               0       0.89      0.73      0.80     10973
               1       0.29      0.54      0.38      2272
    
        accuracy                           0.70     13245
       macro avg       0.59      0.64      0.59     13245
    weighted avg       0.78      0.70      0.73     13245
    
    Confusion Matrix:
    [[7973 3000]
     [1034 1238]]
    Accuracy:  0.6954
    ROC-AUC:   0.6961
    PR-AUC:    0.3516


    2026-04-28 09:40:14,921 - INFO - Completed 5-fold cross-validation for Logistic Regression


    LOGISTIC REGRESSION — 5-FOLD CROSS-VALIDATION
    ROC-AUC:    0.6922  
    F1:         0.3815  
    Recall:     0.5421  
    Precision:  0.2944  
    Accuracy:   0.6986  


    2026-04-28 09:40:21,949 - INFO - Trained and evaluated Random Forest model


    
    RANDOM FOREST — TEST SET EVALUATION
                  precision    recall  f1-score   support
    
               0       0.84      0.99      0.91     10973
               1       0.66      0.07      0.12      2272
    
        accuracy                           0.83     13245
       macro avg       0.75      0.53      0.52     13245
    weighted avg       0.81      0.83      0.77     13245
    
    Confusion Matrix:
    [[10895    78]
     [ 2118   154]]
    Accuracy:  0.8342
    ROC-AUC:   0.6762
    PR-AUC:    0.3425


    2026-04-28 09:40:51,401 - INFO - Completed 5-fold cross-validation for Random Forest


    RANDOM FOREST — 5-FOLD CROSS-VALIDATION
    ROC-AUC:    0.6748  
    F1:         0.1141  
    Recall:     0.0627  
    Precision:  0.6286  
    Accuracy:   0.8329  
    Best Hyperparameters for Random Forest:
    {'classifier__max_depth': 10, 'classifier__min_samples_leaf': 2, 'classifier__min_samples_split': 5, 'classifier__n_estimators': 200}


    2026-04-28 09:43:27,868 - INFO - Completed hyperparameter tuning and evaluation of best Random Forest model


    
    BEST RANDOM FOREST — TEST SET EVALUATION
                  precision    recall  f1-score   support
    
               0       0.89      0.74      0.81     10973
               1       0.30      0.54      0.39      2272
    
        accuracy                           0.71     13245
       macro avg       0.59      0.64      0.60     13245
    weighted avg       0.79      0.71      0.73     13245
    
    Confusion Matrix:
    [[8112 2861]
     [1035 1237]]
    Accuracy:  0.7059
    ROC-AUC:   0.7010
    PR-AUC:    0.3566


# Analysis rationale

Two models, logistic regression and random forest, were implemented to predict whether a patient will be readmitted to the hospital within 30 days. The logistic regression model was chosen for its simplicity and interpretability, while the random forest model was selected for its ability to capture complex relationships in the data and handle non-linear interactions between features. 

In the process of building the model and preparing the data to go from a document-based database to a relational format, several decisions were made. First, arrays were flattened to create a tabular structure suitable for machine learning algorithms. This involved creating new columns for each element in the arrays, such as medications and diagnoses. In preprocessing the data, missing values were handled using imputation techniques, and categorical variables were encoded using one-hot encoding. Feature selection was performed to identify the most relevant features for the model. `readmitted_30day` was set as the target variable, and features that were directly related to readmission or created after the readmission event were excluded from the feature set to prevent data leakage. 

After preprocessing, the dataset was split into training and testing sets to evaluate the model's performance on unseen data. Both models were trained on the training set, and their predictions were evaluated on the testing set using a classification report that includes precision, recall, and F1-score metrics. These metrics were chosen to provide a comprehensive evaluation of the model's performance, especially in the context of imbalanced classes where accuracy alone may not be sufficient to assess the model's effectiveness. A confusion matrix was also generated to visualize the performance of the models in terms of true positives, true negatives, false positives, and false negatives. ROC-AUC scores were calculated to assess the models' ability to discriminate between the classes, with higher scores indicating better performance. For both models, cross-validation was performed to ensure that the results were robust and not due to overfitting. Grid search was used to tune the hyperparameters of the random forest model, which helped improve its performance by finding the optimal combination of parameters such as the number of trees and maximum depth.

After analyzing the accuracy, ROC-AUC, and Precision-Recall AUC scores, the tuned random forest model was selected primarily based on its superior ROC-AUC, with PR-AUC scores being comparable between the two models. This indicates that it has a better ability to distinguish between patients who will be readmitted and those who will not. Additionally, the Precision-Recall AUC score was comparable, suggesting that it has a better precision in predicting positive cases of readmission, which is more crucial in a medical setting where we want to determine which patients are at risk of readmission. Missing a positive case is more costly than a false alarm, as it could lead to inadequate care for patients who are at risk of readmission. Though the logistic regression has a higher accuracy, it is not the best metric to evaluate the model's performance in this case due to the imbalanced nature of the dataset. Thus for any further interpretation of feature important and visualizations, the random forest model will be used as the final model for predicting hospital readmissions within 30 days.

# Visualization of results


```python
# 2 subplots for roc and feature importance
fig, ax = plt.subplots(1, 2, figsize=(18, 6))
# ROC curve subplot
fpr_logistic, tpr_logistic, _ = roc_curve(y_test, y_prob_logistic)
fpr_rf, tpr_rf, _ = roc_curve(y_test, y_prob_best_rf)
roc_auc_logistic = auc(fpr_logistic, tpr_logistic)
roc_auc_rf = auc(fpr_rf, tpr_rf)
ax[0].plot(fpr_logistic, tpr_logistic, color='orange', label=f'Logistic Regression (AUC = {roc_auc_logistic:.4f})')
ax[0].plot(fpr_rf, tpr_rf, color='green', label=f'Random Forest (AUC = {roc_auc_rf:.4f})')
ax[0].plot([0, 1], [0, 1], color='red', linestyle='--')
ax[0].set_xlim([0.0, 1.0])
ax[0].set_ylim([0.0, 1.05])
ax[0].set_xlabel('False Positive Rate')
ax[0].set_ylabel('True Positive Rate')
ax[0].set_title('ROC Curve Comparison')
ax[0].legend(loc='lower right')

# Feature importance subplot
feature_importances = best_rf_model.named_steps['classifier'].feature_importances_
feature_names = best_rf_model.named_steps['preprocessor'].get_feature_names_out()
feature_importance_df = pd.DataFrame({'feature': feature_names, 'importance': feature_importances})
feature_importance_df = feature_importance_df.sort_values(by='importance', ascending=False).head(20)

colors = plt.cm.coolwarm(np.linspace(0.2, 0.8, len(feature_importance_df)))
bars = ax[1].barh(feature_importance_df['feature'], feature_importance_df['importance'], 
                  color=colors, edgecolor='black', linewidth=0.5, height=0.7)
ax[1].set_xlabel('Feature Importance')
ax[1].set_title('Top 20 Feature Importances from Random Forest')
ax[1].invert_yaxis()
plt.tight_layout()
plt.show()

logging.info("Plotted ROC curves and feature importance for model comparison")
```


    
![png](Images/Problem_Solution_Pipeline_files/Problem_Solution_Pipeline_14_0.png)
    


# Visualization rationale

To visualize the results of the model, an ROC/AUC curve was plotted because it visually shows the trade-off between the true positive rate and false positive rate at various threshold settings, evaluating how well the model distinguishes between classes. Both the logistic regression and random forest models were plotted on the same ROC curve for comparison. The AUC score is a single metric that summarizes the overall performance of the model, so in having both models on the same graph allows for a clear visual comparison of their performance, specifically with the random forest model having a slightly higher AUC compared to the logistic regression model. Additionally, a bar chart was created to show which features had the most influence on the random forest model's predictions for hospital readmissions. The choice of a bar chart for feature importance was made because it allows for easy comparison of the importance scores across different features, making it straightforward to identify the most influential predictors.
