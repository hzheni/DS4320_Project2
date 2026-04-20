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
| Press Release | [fill in title and change link](https://github.com/hzheni/DS4320_Project1/blob/main/Press_Release.md) |
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
[Link to OneDrive folder of PDFs](https-------EDIT THIS!)

### Background Readings Table

EDIT BELOW ONEDRIVE LINKS!
| Title | Description | Direct Link | Link to File in Folder |
|-------|-------------|-------------|------------------------|
| CMS.gov Hospital Readmissions Reduction Program | Directly from the Centers for Medicare and Medicaid Services, it explains the federal Hospital Readmissions Reduction Program (HRRP), which penalizes hospitals for excessive 30-day readmission rates and establishes readmission as a key quality-of-care metric in the U.S. healthcare system. | [CMS.gov Hospital Readmissions Reduction Program](https://www.cms.gov/medicare/quality/value-based-programs/hospital-readmissions) | [CMS.gov Hospital Readmissions Reduction Program](https://github.com/hzheni/DS4320_Project2/blob/main/Background%20Readings/CMS.gov%20Hospital%20Readmissions%20Reduction%20Program.pdf) |
| From Data to Decisions - Leveraging Predictive Analytics to Transform for Hospital Readmissions | Provides a broad overview that discusses what predictive analytics is in healthcare, why readmissions matters, and how predictive analytics is a targeted intervention to take action in primary care. | [From Data to Decisions - Leveraging Predictive Analytics to Transform for Hospital Readmissions](https://www.mghihp.edu/news-and-more/opinions/data-analytics/data-decisions-leveraging-predictive-analytics-transform-hospital-readmissions) | [From Data to Decisions - Leveraging Predictive Analytics to Transform for Hospital Readmissions](https://github.com/hzheni/DS4320_Project2/blob/main/Background%20Readings/From%20Data%20to%20Decisions%20-%20Leveraging%20Predictive%20Analytics%20to%20Transform%20for%20Hospital%20Readmissions.pdf) | 
| Predictive machine learning model for 30-day hospital readmissions in a tertiary healthcare setting | Presents a machine learning-based approach (Logistic Regression, Random Forest, and LightGBM) to predicting 30-day hospital readmissions using clinical and demographic variables for a 200-bed community hospital in Argentina. It overall looks to improve readmission risk prediction and inform targeted healthcare interventions. | [Predictive machine learning model for 30-day hospital readmissions in a tertiary healthcare setting](https://academic.oup.com/bioinformaticsadvances/article/5/1/vbaf121/8145567) | [Predictive machine learning model for 30-day hospital readmissions in a tertiary healthcare setting](https://github.com/hzheni/DS4320_Project2/blob/main/Background%20Readings/Predictive%20machine%20learning%20model%20for%2030-day%20hospital%20readmissions%20in%20a%20tertiary%20healthcare%20setting.pdf) | 
| Systematic Review and Meta-Analysis of the Financial Impact of 30-Day Readmissions for Selected Medical Conditions | Reviews multiple studies to quantify the financial burden of 30-day hospital readmissions, emphasizing the economic impact on healthcare systems and the importance of reducing preventable readmissions. | [Systematic Review and Meta-Analysis of the Financial Impact of 30-Day Readmissions for Selected Medical Conditions](https://pmc.ncbi.nlm.nih.gov/articles/PMC11011876/pdf/healthcare-12-00750.pdf) | [Systematic Review and Meta-Analysis of the Financial Impact of 30-Day Readmissions for Selected Medical Conditions](https://github.com/hzheni/DS4320_Project2/blob/main/Background%20Readings/Systematic%20Review%20and%20Meta-Analysis%20of%20the%20Financial%20Impact%20of%2030-Day%20Readmissions%20for%20Selected%20Medical%20Conditions.pdf) |
| The Top 5 Causes of Hospital Readmissions – and How to Prevent Them - Upfront Healthcare | Blog-style post that outlines common clinical and social causes of hospital readmissions and discusses practical strategies for prevention. | [The Top 5 Causes of Hospital Readmissions – and How to Prevent Them](https://upfronthealthcare.com/resources/cost-reduction/the-top-5-causes-of-hospital-readmissions-and-how-to-prevent-them/) | [The Top 5 Causes of Hospital Readmissions – and How to Prevent Them](https://github.com/hzheni/DS4320_Project2/blob/main/Background%20Readings/The%20Top%205%20Causes%20of%20Hospital%20Readmissions%20–%20and%20How%20to%20Prevent%20Them%20-%20Upfront%20Healthcare.pdf) | 
