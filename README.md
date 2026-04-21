# DS4320_Project2: xxx

### Executive Summary

xxx

---
# Project Details

|   | Details |
|---|---|
| Name | Jessica Ni |
| NetID | dkh8my |
| DOI | [![DOI_fill innnnn](fill innnnnn) |
| Press Release | [Turning Hospital Data Into Fewer Return Visits for Patients](https://github.com/hzheni/DS4320_Project2/blob/main/Press_Release.md) |
| Pipeline | [Problem Solution Pipeline Code_ change link](Problem_Solution_Pipeline.ipynb) <br> [Problem Solution Pipeline Markdown](Problem_Solution_Pipeline.md) |
| License | [MIT LICENSE](./LICENSE) |

---

## Problem Definition
### General and Specific Problem
* **General Problem:** Predicting hospital readmission risk
* **Specific Problem:** Using encounter-level documents from a MongoDB database, predict whether a patient will be readmitted within 30 days of hospital discharge and identify the most influential clinical and demographic factors contributing to that risk.

### Rationale
The general problem of predicting hospital readmission risk is broad and not directly actionable without spelling out factors such as the time frame for readmission and the specific factors to analyze. By refining the problem to focus on predicting 30-day readmission, we can create a more precise and measurable outcome, as 30-day-post-discharge readmission is a commonly used benchmark in the healthcare domain. Additionally, by identifying the most influential clinical and demographic factors, we can ensure that the project is not only predictive but also interpretable. This is crucial for healthcare applications where understanding the reasons behind a prediction allows healthcare providers to take informed actions to mitigate the risk of readmission for patients. Overall, the refined problem statement allows us to develop targeted models specifically for 30 day readmission and the factors that contribute to it.

### Motivation
Patients and healthcare providers alike are motivated by the shared objective of improving health outcomes and avoiding unnecessary hospital readmissions. For patients, readmissions can be a source of stress, financial burden, and potential health risks. For healthcare providers, readmissions can reflect on the quality of care provided and can lead to increased costs and resource strain. By developing a predictive model to identify patients at high risk of readmission and understanding the key factors contributing to that risk, both parties can benefit from knowledge that can improve patient care by targeting features that are most influential in readmission risk. The overall goal is to be more efficient in providing care to patients, so that they can avoid readmissions and have better health outcomes.

### Press Release Headline and Link
[**Turning Hospital Data Into Fewer Return Visits for Patients**](https://github.com/hzheni/DS4320_Project2/blob/main/Press_Release.md)

## Domain Exposition
### Terminology

| Term | Definition |
|------|------------|
| Readmission | The event of a patient being admitted to the hospital again within a certain time frame after being discharged. |
| 30-day readmission | A specific type of readmission that occurs within 30 days of hospital discharge, primarily driven by the CMS Hospital Readmissions Reduction Program (HRRP) |
| Risk Standardized Readmission Rate (RSRR) | A metric used to evaluate hospital performance based on the rate of readmissions, adjusted for patient risk factors. |
| Clinical factors | Medical conditions, treatments, or health indicators that may influence a patient's health outcomes. |
| Discharge | The process of a patient leaving the hospital after receiving care. |
| Predictive model | A statistical or machine learning model that is used to predict an outcome based on input data. |
| Binary classification | A type of predictive modeling where the outcome variable has two possible classes, such as "readmitted" or "not readmitted". |

### Domain Explanation
This project lives in the domain of healthcare and data science. It focuses on using patient hospital stay data to predict the risk of hospital readmission, which uses data science techniques to analyze a healthcare problem. On the healthcare side, hospital readmissions are a significant concern for both patients and healthcare providers, as they can lead to increased costs, resource strain, and negative health outcomes. On the data science side, this project involves building predictive models using clinical and demographic data to identify patterns and factors that contribute to readmission risk. The intersection of these two domains allows us to apply data driven approaches to healthcare challenges. 

### Background Readings 
[Link to OneDrive folder of PDFs](https://myuva-my.sharepoint.com/:f:/g/personal/dkh8my_virginia_edu/IgDsGNghX3VLSo1WyxsPeE11AccMwpuq5oUh_v18fhy-W4U?e=cK6Xf8)

### Background Readings Table

| Title | Description | Direct Link | Link to File in Folder |
|-------|-------------|-------------|------------------------|
| CMS.gov Hospital Readmissions Reduction Program | Directly from the Centers for Medicare and Medicaid Services, it explains the federal Hospital Readmissions Reduction Program (HRRP), which penalizes hospitals for excessive 30-day readmission rates and establishes readmission as a key quality-of-care metric in the U.S. healthcare system. | [CMS.gov Hospital Readmissions Reduction Program](https://www.cms.gov/medicare/quality/value-based-programs/hospital-readmissions) | [CMS.gov Hospital Readmissions Reduction Program](https://myuva-my.sharepoint.com/:b:/g/personal/dkh8my_virginia_edu/IQBZWHdnTNCaQbgAHtRXz6wpAVFhRO_OzhlaNNpRL7DGjsc?e=ctN5Nv) |
| From Data to Decisions - Leveraging Predictive Analytics to Transform for Hospital Readmissions | Provides a broad overview that discusses what predictive analytics is in healthcare, why readmissions matters, and how predictive analytics is a targeted intervention to take action in primary care. | [From Data to Decisions - Leveraging Predictive Analytics to Transform for Hospital Readmissions](https://www.mghihp.edu/news-and-more/opinions/data-analytics/data-decisions-leveraging-predictive-analytics-transform-hospital-readmissions) | [From Data to Decisions - Leveraging Predictive Analytics to Transform for Hospital Readmissions](https://myuva-my.sharepoint.com/:b:/g/personal/dkh8my_virginia_edu/IQC124yyovNaTbz64HzhV5ImARPSaRj_V_u2vF-9CN4WnXI?e=1zLWDj) | 
| Predictive machine learning model for 30-day hospital readmissions in a tertiary healthcare setting | Presents a machine learning-based approach (Logistic Regression, Random Forest, and LightGBM) to predicting 30-day hospital readmissions using clinical and demographic variables for a 200-bed community hospital in Argentina. It overall looks to improve readmission risk prediction and inform targeted healthcare interventions. | [Predictive machine learning model for 30-day hospital readmissions in a tertiary healthcare setting](https://academic.oup.com/bioinformaticsadvances/article/5/1/vbaf121/8145567) | [Predictive machine learning model for 30-day hospital readmissions in a tertiary healthcare setting](https://myuva-my.sharepoint.com/:b:/g/personal/dkh8my_virginia_edu/IQAVumqwtxThS6f4St0nnZQiARQRbuo31uThSI7ZtjcSnA4?e=9ytxEt) | 
| Systematic Review and Meta-Analysis of the Financial Impact of 30-Day Readmissions for Selected Medical Conditions | Reviews multiple studies to quantify the financial burden of 30-day hospital readmissions, emphasizing the economic impact on healthcare systems and the importance of reducing preventable readmissions. | [Systematic Review and Meta-Analysis of the Financial Impact of 30-Day Readmissions for Selected Medical Conditions](https://pmc.ncbi.nlm.nih.gov/articles/PMC11011876/pdf/healthcare-12-00750.pdf) | [Systematic Review and Meta-Analysis of the Financial Impact of 30-Day Readmissions for Selected Medical Conditions](https://myuva-my.sharepoint.com/:b:/g/personal/dkh8my_virginia_edu/IQAJnFPMXnQtTLGLdym6Jq7YARpEXIWFA4vm_ZhXuVJQ4ME?e=2yCyMs) |
| The Top 5 Causes of Hospital Readmissions – and How to Prevent Them - Upfront Healthcare | Blog-style post that outlines common clinical and social causes of hospital readmissions and discusses practical strategies for prevention. | [The Top 5 Causes of Hospital Readmissions – and How to Prevent Them](https://upfronthealthcare.com/resources/cost-reduction/the-top-5-causes-of-hospital-readmissions-and-how-to-prevent-them/) | [The Top 5 Causes of Hospital Readmissions – and How to Prevent Them](https://myuva-my.sharepoint.com/:b:/g/personal/dkh8my_virginia_edu/IQDHNy1A9VI3SInpwL4UHvToARSpEpDnXax97Mq8SZNzRVc?e=x4crQL) | 

## Data Creation
### Data Acquisition
The main dataset used for this project is the "Diabetes 130-US Hospitals for Years 1999-2008" dataset, representing 10 years of clinical care at 130 US hospitals and integrated delivery networks. This is a publicly available and de-identified dataset containing hospital records of patients diagnosed with diabetes, who underwent laboratory and had medications. For this project, the data was downloaded in CSV format from the UC Irvine Machine Learning Repository. The downloaded dataset contained two files: "diabetic_data.csv" which contains the main patient data, and "IDS_mapping.csv" which contains mappings for IDs used in the main dataset. The data was preprocessed and transformed to unstructured format before being loaded into MongoDB.

Edit these links to actual link
[Link to California Fire Perimeters (all)](https://data.ca.gov/dataset/california-fire-perimeters-all)

[Link to CAL FIRE Administrative Units](https://data.ca.gov/dataset/cal-fire-administrative-units)

### Code
| File Name              | Description           | Link           |
| ---------------------- | --------------------- | -------------- |
| load_and_prepare_data.ipynb | This notebook contains the pipeline to load the raw data, clean and transform it, transform it into documents, and load it into MongoDB. | [GitHub Link](https://github.com/hzheni/DS4320_Project2/blob/main/load_and_prepare_data.ipynb) | 

### Bias Identification
Several sources of bias are present in the data collection process for the diabetes hospital readmission dataset. Sampling bias was introduced through inclusion criteria that limited encounters to hospital stays between 1-14 days, excluding very short or extended hospitalizations that may represent different risk profiles. Temporal bias is significant because the data spans 1999-2008, predating modern diabetes treatments and current programs relating to hospital readmissions, such as the Centers for Medicare & Medicaid Services' Hospital Readmissions Reduction Program. This means the data may not reflect current healthcare practices or readmission patterns influenced by policy changes. There may be selection bias if certain hospitals or patient groups were overrepresented or underrepresented in the dataset, leading to skewed results and biased conclusions when analyzing the data. Additionally, there may be measurement bias if there were inconsistencies in how data was recorded across different hospitals or over time. These biases limit the generalizability of any predictive model trained on this data, and findings should be interpreted as historical associations rather than current clinical predictions.

### Bias Mitigation
For sampling bias, the analysis should explicitly acknowledge that findings only generalize to hospital stays of 1-14 days. For temporal bias, the model should be framed as a historical analysis rather than a current clinical tool, and any claims about readmission risk factors should note that treatment protocols have evolved since 2008. One mitigation approach is to focus on stable clinical factors (number of diagnoses, medication count) rather than time-sensitive treatments. Additionally, if more recent data is available, it can be used to validate findings from the older dataset. For demographic and socioeconomic bias, stratified analysis by race and payer code (where available) can quantify whether model performance differs across subgroups. Quantifying uncertainty for numerical features such as number of diagnoses or medications by computing standard deviations or confidence intervals can express the range of expected values and provide a measure of reliability for model predictions and any analysis. Overall, transparency about the dataset's limitations and careful interpretation of results can help mitigate the impact of these biases on conclusions drawn from the analysis. 

### Rationale for Critical Decisions
When creating the dataset and preparing the data for the document model, there were several critical decisions related to data selection, transformation, and organization that could introduce or mitigate uncertainty. The decision to filter out patients readmitted after 30 days (the ">30" category) was a judgment call that reduces uncertainty by creating a clean binary classification problem aligned with the clinical benchmark of 30-day readmission. But this may introduce uncertainty by excluding a meaningful group of patients who were readmitted after 30-day window, potentially losing signal about later readmissions that may share similar risk factors. Next, each document was decided to be modeled as an encounter rather than a patient, due to the specific question of predicting whether a patient will be readmitted within 30 days of hospital discharge. There were also decisions made about which features to include in the documents. Several columns, such as weight, were dropped due to a high number of missing values. Columns that may lead to data leakage (discharge_disposition_id, admission_source_id) were also dropped to mitigate the risk of the model learning from information that would not be available at the time of prediction. Overall, being transparent about these decisions and their potential impacts on the analysis helps to contextualize the results and manage uncertainty effectively.

## Metadata

### Implicit Schema
The diabetes hospital readmission dataset is structured where each document represents exactly one hospital admission (encounter) for a diabetic patient. The schema follows an implicit structure where related attributes are logically grouped. Core patient and encounter attributes such as demographic information (race, gender, age) and hospital utilization metrics (time in hospital, number of procedures, medications, and visits) are stored as top-level fields for efficient querying. Clinical information such as diagnoses and medications are stored as arrays to accommodate varying lengths across encounters. Categorical identifiers (e.g., admission type, discharge disposition, and admission source) are stored as descriptive text rather than numeric codes to improve interpretability. This approach allows for a consistent organization of data across documents while accommodating the variability in clinical records, such as differing numbers of diagnoses or medications across encounters. The schema is designed to balance the need for structured fields and key attributes, while maintaining flexibility inherent to the document model.

### Data Summary
| Property | Value |
|:---|:---|
| Database Name | `diabetes_readmission` |
| Collection Name | `encounters` |
| Total Documents | 66,221 |
| Date Range of Data | 1999-2008 |
| Document Model Type | Encounter-centric (one document per hospital admission) |
| Target Variable | `readmitted_30day` (binary: 1 if readmitted within 30 days, 0 if not) |
| Numerical Features | `time_in_hospital`, `num_lab_procedures`, `num_procedures`, `num_medications`, `number_outpatient`, `number_emergency`, `number_inpatient`, `num_diagnoses` |
| Categorical Features | `race`, `gender`, `age`, `admission_type_desc`, `discharge_disposition_desc`, `admission_source_desc`, `change`, `diabetesMed` |
| Array Features | `diagnoses` (array of ICD-9 codes), `medications` (array of diabetes medication names) |

### Data Dictionary

| Name                       | Data Type        | Description                                 | Example              |
| -------------------------- | ---------------- | ------------------------------------------- | -------------------- |
| encounter_id               | Integer          | Unique identifier for hospital encounter    | 1               |
| patient_nbr                | Integer          | Unique identifier for patient               | 8222157              |
| race                       | String           | Patient race category                       | "Caucasian"          |
| gender                     | String           | Patient gender                              | "Female"             |
| age                        | String           | Age range bucket                            | "[0-10)"             |
| time_in_hospital           | Integer          | Days spent in hospital                      | 1                    |
| num_lab_procedures         | Integer          | Number of lab tests performed               | 41                   |
| num_procedures             | Integer          | Number of procedures performed              | 0                    |
| num_medications            | Integer          | Number of medications prescribed            | 1                    |
| number_outpatient          | Integer          | Outpatient visits in past year              | 0                    |
| number_emergency           | Integer          | Emergency visits in past year               | 0                    |
| number_inpatient           | Integer          | Inpatient visits in past year               | 0                    |
| num_diagnoses              | Integer          | Total diagnoses recorded                    | 1                    |
| diagnoses                  | Array of Strings | ICD diagnosis codes                         | [0: "648", 1: "250", 2: "V27"]  |
| medications                | Array of Strings | Medications administered                    | [0: "glipizide"]     |
| change                     | String           | Indicates change in medications             | "No"                 |
| diabetesMed                | String           | Indicates if diabetes medication prescribed | "No"                 |
| admission_type_desc        | String           | Type of admission                           | "Emergency"          |
| discharge_disposition_desc | String           | Discharge outcome                           | "Discharged to home" |
| admission_source_desc      | String           | Source of admission                         | "Physician Referral" |
| readmitted_30day           | Integer (Binary) | Readmission within 30 days                  | 0                    |

### Data Dictionary Uncertainty Quantification

| Feature | Measurement Precision | Missing Rate | Uncertainty Source | Uncertainty Quantification |
|:---|:---|:---|:---|:---|
| `time_in_hospital` | ±0.5 days (recorded as integer days) | 0% | Discrete rounding; actual stay may include partial days. | ±0.5 days per measurement |
| `num_lab_procedures` | ±1 procedure (count data) | 0% | Potential under-counting if labs performed at outside facilities | ±2 procedures |
| `num_procedures` | ±1 procedure (count data) | 0% | Procedure coding varies by hospital; some procedures may be bundled. Right-skewed as <br> many patients have few procedures | ±1 procedure |
| `num_medications` | ±1 medication (count data) | 0% | Medications may be inconsistently recorded; only diabetes medications captured in array | ±2 medications |
| `number_outpatient` | ±1 visit (count data) | 0% | Relies on complete capture within Cerner network; visits to outside facilities missing | ±3 visits (high uncertainty) |
| `number_emergency` | ±1 visit (count data) | 0% | Same limitation as outpatient; ER visits at non-Cerner hospitals not recorded | ±2 visits |
| `number_inpatient` | ±1 admission (count data) | 0% | Prior admissions to non-Cerner hospitals are not captured in the data | ±1 admission |
| `num_diagnoses` | ±0 (derived from `diagnoses` array) | 0% | Original data limited to 3 diagnosis codes; actual diagnoses may be more | Under-counting of 2-5 additional diagnoses |
| `num_medications_taken` | ±0 (derived from `medications` array) | 0% | Only diabetes-related medications captured; other medications excluded | Systematic under-counting of non-diabetes medications |
| `patient_nbr` | N/A (identifier) | 0% | No uncertainty; serves as linkage key only, not a predictive feature | N/A |
