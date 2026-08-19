# credit-risk-default-prediction
Credit risk analysis and default prediction using Python, Logistic Regression and Random Forest.
# Credit Risk Analysis & Default Prediction

A Python-based credit risk analysis examining the drivers of
credit card default and comparing Logistic Regression with
Random Forest for Probability of Default prediction.

## Key Results

- Random Forest ROC-AUC: **0.773**
- Random Forest Gini: **0.547**
- Logistic Regression ROC-AUC: **0.733**
- Default rate increases from **~12% to ~70%** as the number
  of late-payment months increases.
- Predicted risk bands successfully separate borrowers from
  **9.3% observed default risk** to **65.9%**.

  ## Repayment History and Default Risk

![Default Rate by Late Payments](images/default_by_late_payments.png)

## Risk Segmentation

![Default Rate by Risk Band](images/default_by_risk_band.png)

## Model Comparison

![Model Comparison](images/model_comparison.png)
