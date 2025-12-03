# Equity in Post-HCT Survival Predictions

## Project Description
This project addresses the **Equity in Post-HCT Survival Predictions** competition hosted on Kaggle. The primary objective is to develop accurate survival prediction models for patients undergoing Hematopoietic Cell Transplantation (HCT), a critical therapy for blood cancers. 

The main challenge of this task is ensuring that the models generalize well across diverse populations. This is why the goal of this project is not to maximize global accuracy but to optimize the **Stratified Concordance Index (C-Index)**. This metric ensures that the model's ability to rank patient risk is consistent across all racial and ethnic sub-populations.

## Objectives
The specific objectives of this project were:
1.  **Bias Mitigation:** To develop preprocessing and modeling strategies that address the imbalance in minority groups.
2.  **Model Optimization:** To train and tune multiple survival analysis models, including linear, tree-based, and deep learning approaches.
3.  **Performance Maximization:** To construct an ensemble model to maximize the Stratified C-Index, aiming to exceed the random baseline of 0.5.

## Methodology

### 1. Exploratory Data Analysis (EDA)
The EDA identified missing data, analysed the ditribution of data and identified outliers.

Interesting findings that would affect future results is a contradiction between biological risk factors and observed survival rates:
*   **Biological Observation:** Minority groups displayed higher HLA mismatch rates, typically indicating higher transplant risk.
*   **Statistical Observation:** Certain minority groups exhibited lower event rates (higher survival) in the training set compared to the majority group.
This discrepancy led to initial models demonstrating bias toward the majority class base rate, necessitating specific weighting strategies.

*Note: Minority/majority in this case do not indicate an imbalance in data, but is used in a social sense, eg. Native American is considered a minority group)*

### 2. Data Preprocessing
*   **Handling Missing Data:** Missing values were handled by median imputation for continuous variables. For Caterigorical variables missing values and values such as "Not done", "Missing" etc where grouped and replaced with the value "Unknown".
*   **Feature Engineering:** Interaction terms were created to capture clinical non-linearities, such as the interaction between patient age and treatment intensity.
*   **Formatting:** Categorical variables were one-hot encoded, and binary flags were standardized.

### 3. Modeling
Three distinct modeling approaches were implemented to capture different aspects of the survival function and address specific data challenges:

*   **Logistic Regression:** A baseline to establish a performance benchmark. Was used in the ensemble to take advantage of it's statistical knowledge.

*   **DeepSurv :** Was chosen to capture non-linear relationships. Adapts the traditional Cox Proportional Hazards model. Instead of a linear function, it uses a deep neural network to output a risk score, allowing the model to learn complex patterns from the data while still handling patients who did not experience an event during the study.

*   **Weighted XGBoost:** Exploratory analysis revealed that the model was biased toward one racial group, leading to poor ranking for other groups. A standard model would ignore these failure cases as statistical noise. Sample weights were applied during training. Patients from groups who experienced an adverse event were assigned higher weights. This forced the model to pay more attention to these specific cases, correcting the bias where the model was previously overly optimistic about the survival rate of certain groups.

### 4. Ensembling
The final risk scores were generated using a weighted average ensemble of the three models to balance generalization (DeepSurv/Logistic Regression) with specific bias correction (XGBoost).

## Results
The models were evaluated using the Stratified Concordance Index (C-Index) on a hold-out validation set.

| Model | Validation C-Index |
| :--- | :--- |
| **Random Baseline** | 0.500 |
| **XGBoost (Weighted)** | 0.637 |
| **Logistic Regression** | 0.648 |
| **DeepSurv** | 0.659 |
| **Final Ensemble** | **0.661** |

**Leaderboard Performance:**
The final ensemble achieved a C-score of **0.66**, placing the solution at approximately rank **~2,600 out of 3,327** participants. For context, the top solution on the leaderboard achieved a score of 0.69.

## References & Resources
*   **Competition:** [Kaggle: Equity in Post-HCT Survival Predictions](https://www.kaggle.com/competitions/equity-post-HCT-survival-predictions)
*   **DeepSurv:** Katzman, J. L., et al. "DeepSurv: personalized treatment recommender system using a Cox proportional hazards deep neural network." *BMC medical research methodology* 18.1 (2018).
*   **XGBoost:** Chen, T., & Guestrin, C. "XGBoost: A Scalable Tree Boosting System." *KDD* (2016).

## Prerequisites
The project requires the following Python libraries:
*   `numpy`
*   `pandas`
*   `matplotlib`
*   `seaborn`
*   `scikit-learn`
*   `lifelines`
*   `xgboost`
*   `torch` (PyTorch)
*   `optuna`

## Data: 
The dataset is not included in this repository due to size. Download `train.csv` from the [Kaggle Competition Data Page](https://www.kaggle.com/competitions/equity-post-HCT-survival-predictions/data). 

*Note: Only training data was used as test data labels are withheld.*
