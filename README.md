# Credit Risk Analysis & Default Prediction

This project analyzes **credit card default risk** using Python and machine learning.

The objective is to identify the main factors associated with default, engineer meaningful credit-risk features, estimate **Probability of Default (PD)**, and compare the predictive performance of **Logistic Regression** and **Random Forest**.

The analysis uses the **UCI Default of Credit Card Clients dataset**, containing 30,000 credit card customers.

---

## Key Results

* **Random Forest ROC-AUC:** 0.773
* **Random Forest Gini:** 0.547
* **Logistic Regression ROC-AUC:** 0.733
* **Logistic Regression Gini:** 0.465
* Default rates increased from approximately **12% to 70%** as the number of months with payment delays increased.
* The final risk segmentation separated customers from **9.3% observed default risk** in the Low Risk group to **65.9%** in the Very High Risk group.
* Historical repayment behavior emerged as the strongest source of predictive information.

---

m## Dataset

The analysis uses the **Default of Credit Card Clients** dataset from the **UCI Machine Learning Repository**.

The dataset contains information on **30,000 credit card clients in Taiwan** and includes:

* Credit limits
* Demographic characteristics
* Repayment status
* Monthly bill statements
* Previous payment amounts
* Default payment status

The data covers repayment and billing information from **April to September 2005**, with the target variable indicating whether a client defaulted on payment in the following month.

**Source:** UCI Machine Learning Repository — Default of Credit Card Clients
https://archive.ics.uci.edu/dataset/350/default+of+credit+card+clients

The dataset was originally contributed by **I-Cheng Yeh**.

### Target Variable

* `0` — No Default
* `1` — Default

## Exploratory Data Analysis

The overall default rate in the dataset is approximately **22%**.

Age showed some variation in default rates, but repayment behavior displayed a substantially stronger relationship with future default.

Customers experiencing recent or repeated payment delays were considerably more likely to default.

---

## Repayment History and Default Risk

To summarize repayment behavior, I engineered a new feature:

`num_late_payments`

This represents the number of months in which a customer recorded a payment delay.

The relationship with default was particularly strong:

| Months with Payment Delays | Approx. Default Rate |
| -------------------------: | -------------------: |
|                          0 |                  12% |
|                          1 |                  30% |
|                          2 |                  39% |
|                          3 |                  51% |
|                          4 |                  57% |
|                          5 |                  57% |
|                          6 |                  70% |

![Default Rate by Late Payments](Default%20rate%20by%20late%20payments.png)

The results indicate a clear relationship between repeated repayment difficulties and subsequent default.

---

## Logistic Regression

Logistic Regression was used as the baseline **Probability of Default model**.

### Model Performance

| Metric    | Score |
| --------- | ----: |
| Accuracy  | 0.810 |
| Precision | 0.659 |
| Recall    | 0.289 |
| F1 Score  | 0.402 |
| ROC-AUC   | 0.733 |
| Gini      | 0.465 |

Although the model achieved 81% accuracy, accuracy alone is potentially misleading because only around 22% of customers defaulted.

At the standard 0.50 classification threshold, the model correctly identified only **28.9% of actual defaults**.

---

## Classification Threshold Analysis

Different classification thresholds were therefore evaluated.

The highest F1 score among the tested thresholds occurred around **0.25**:

| Metric    | Score |
| --------- | ----: |
| Precision | 0.493 |
| Recall    | 0.540 |
| F1        | 0.515 |

Reducing the classification threshold substantially increased the proportion of actual defaulters identified by the model.

In a real lending environment, the appropriate threshold would depend on the relative financial cost of **missing a future default** versus **incorrectly flagging a lower-risk borrower**.

---

## Risk Segmentation

Predicted probabilities were converted into four borrower risk groups.

| Risk Band | Customers | Actual Default Rate | Average Predicted PD |
| --------- | --------: | ------------------: | -------------------: |
| Low       |       927 |                9.3% |                 7.8% |
| Medium    |     3,620 |               14.5% |                14.7% |
| High      |       870 |               38.2% |                36.7% |
| Very High |       583 |               65.9% |                67.8% |

![Actual Default by Risk Band](Actual%20Default%20by%20risk%20band.png)

The observed default rate increases consistently across the four predicted risk categories:

**9.3% → 14.5% → 38.2% → 65.9%**

This suggests that the model successfully separates relatively low-risk borrowers from substantially higher-risk borrowers.

### Predicted vs Actual Default Rates

![Predicted vs Actual Default Rate](Predicted%20vs%20Actual%20Default%20rate.png)

Predicted probabilities were also reasonably close to observed default frequencies within the four broad risk bands.

---

## Feature Engineering Experiment

To evaluate the value of the engineered repayment feature, three Logistic Regression specifications were compared.

| Model                  | ROC-AUC |  Gini |
| ---------------------- | ------: | ----: |
| Raw Repayment Features |   0.701 | 0.403 |
| Engineered Feature     |   0.729 | 0.457 |
| Combined               |   0.733 | 0.465 |

The engineered `num_late_payments` feature captured a large amount of the predictive information contained in the original repayment variables.

Using the engineered feature increased ROC-AUC from **0.701 to 0.729**, while adding the original repayment variables produced a smaller additional improvement to **0.733**.

---

## Random Forest

Random Forest was introduced as a nonlinear challenger model.

### Model Comparison

| Metric    | Logistic Regression | Random Forest |
| --------- | ------------------: | ------------: |
| Accuracy  |               0.810 |     **0.819** |
| Precision |               0.659 |     **0.665** |
| Recall    |               0.289 |     **0.367** |
| F1 Score  |               0.402 |     **0.473** |
| ROC-AUC   |               0.733 |     **0.773** |
| Gini      |               0.465 |     **0.547** |

![Model Comparison](Model%20Comparison.png)

Random Forest outperformed Logistic Regression across all reported evaluation metrics.

The largest improvements were observed in **recall, ROC-AUC, and Gini**, indicating better identification and ranking of higher-risk borrowers.

---

## Feature Importance

Random Forest feature importance reinforced the findings from the exploratory analysis.

![Random Forest Feature Importance](Random%20Forest.png)

The strongest predictors were:

1. `PAY_0` — most recent repayment status
2. `num_late_payments` — engineered count of months with payment delays
3. `PAY_2`
4. `PAY_3`

Repayment behavior therefore represents the dominant source of predictive information in the model.

---

## Key Findings

1. **Repayment history is the strongest predictor of default.** Customers with repeated payment delays exhibited substantially higher observed default rates.

2. **Feature engineering improved predictive performance.** The `num_late_payments` variable provided a simple and interpretable summary of repayment behavior.

3. **Classification thresholds matter.** Lowering the Logistic Regression threshold improved recall considerably, illustrating the trade-off between identifying risky borrowers and incorrectly flagging non-defaulters.

4. **Risk segmentation produced clearly differentiated groups.** Observed default rates increased from 9.3% among Low Risk customers to 65.9% among Very High Risk customers.

5. **Random Forest provided the strongest predictive performance**, achieving ROC-AUC of **0.773** and Gini of **0.547**.

---

## Technologies Used

* Python
* Pandas
* NumPy
* Matplotlib
* Scikit-learn
* Jupyter Notebook

---

## Repository Contents

`Credit Risk.ipynb` — Complete Python analysis and modelling workflow

`UCI_Credit_Card.csv` — Dataset used for the analysis

PNG files — Key visualizations from the analysis

---

## Limitations

This project is intended as an educational application of credit-risk analytics and machine learning.

The dataset is historical and represents a specific credit card portfolio, so model performance should not be assumed to generalize to other borrowers, markets, or lending environments.

The risk bands are analytical categories rather than regulatory credit ratings, and feature importance or statistical associations should not be interpreted as causal relationships.

---
## Installation

Clone the repository and install the required Python packages:

```bash
pip install -r requirements.txt

## Conclusion

This project demonstrates an end-to-end credit-risk workflow covering:

**Data Exploration → Feature Engineering → Probability of Default Modelling → Threshold Analysis → Risk Segmentation → Model Comparison**

The results show that historical repayment behavior provides the strongest signal of future default, while Random Forest improves predictive performance relative to the Logistic Regression baseline.
