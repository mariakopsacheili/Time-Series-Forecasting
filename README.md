# Time Series Forecasting: Daily Births in 2015
## Project Overview
This project focuses on time series forecasting for the daily number of births in 2015. The objective was to analyze the data, ensure stationarity and build predictive models using ARIMA, SARIMA and Prophet to determine the best approach.

# Data Description
The dataset consists of daily birth counts for the year 2015.
After preprocessing, the dataset was structured with the date as the index and the number of births as the column.
# Exploratory Data Analysis (EDA)
Statistical Summary: Used .describe() to observe key statistics.
Visualization: A violin plot was created to understand distribution and variability.
# Time Series Decomposition: The dataset was decomposed into trend, seasonality, and residuals for better understanding.
# Stationarity Check & Transformation
## Dickey-Fuller Test: Initially, the data was not stationary.
Differencing (.diff()) was applied to remove trends and make the series stationary.
Re-evaluation: After differencing, the Dickey-Fuller test confirmed stationarity.
# Modeling Approaches
## 1. Naive Model (Baseline)
Assumes "What happened yesterday will happen tomorrow."
Used as a benchmark for comparison.
## 2. ARIMA (AutoRegressive Integrated Moving Average)
Chose d = 1 based on the differenced dataset.
ACF and PACF plots were analyzed to select p and q.
Data was split into train (330 rows) and test (34 rows).
Experimented with different (p, d, q) combinations for ARIMA.
Best ARIMA model: (p=13, d=1, q=19).
## 3. SARIMA (Seasonal ARIMA)
Used auto_arima to automate hyperparameter selection:
![image](https://github.com/user-attachments/assets/99cd4419-a101-4108-bfbe-79a163a90308)
Performed diagnostic plotting to validate model performance.
4. Facebook Prophet
Implemented Prophet for a more flexible, trend-aware forecasting approach.
Model Evaluation
Metrics Used:

- Mean Absolute Error (MAE)
- Mean Absolute Percentage Error (MAPE)
# Results
Model	MAE	MAPE
ARIMA (p=13, d=1, q=19)	668	1.51%
SARIMA (auto-selected)	765.6	1.78%
Prophet	714	0.94%
# Conclusion
Since birth counts range between 6,000 and 13,000, MAPE is the most meaningful metric.
Best Model: Prophet (MAPE = 0.94%) — it generalizes better across different birth values.
ARIMA had a lower MAE but a higher MAPE, making it less optimal for relative error evaluation.

