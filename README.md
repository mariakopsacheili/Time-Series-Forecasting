# Time Series Forecasting: Daily Births in 2015
## Project Overview
This project focuses on time series forecasting for the daily number of births in 2015. The objective was to analyze the data, ensure stationarity and build predictive models using ARIMA, SARIMA and Prophet to determine the best approach.

# Data Description
The dataset consists of daily birth counts for the year 2015.
After preprocessing, the dataset was structured with the date as the index and the number of births as the column.
# Exploratory Data Analysis (EDA)
- Check for nulls, duplicates (no null or duplicate values were found)
- Statistical Summary: Used .describe() to observe key statistics.
- Transform our series datetime and specify datetime as index.
- Visualization: A violin plot was created to understand distribution and variability.
  ![image](https://github.com/user-attachments/assets/16ac7412-3868-41d5-a0d6-9e206d7e598d)

  - Insights:
    - Births appear fairly stable across months, with some seasonal variation.
    - Summer months (June-August) show slightly higher births, as indicated by the white dots.
    - December has a slightly narrower spread, suggesting more consistent birth counts.

# Time Series Decomposition 
The dataset was decomposed into trend, seasonality, and residuals for better understanding.
In time series analysis, additive and multiplicative models are two common approaches used to decompose a time series into its underlying components. These components typically include:
- Trend: The long-term movement or direction in the data.
- Seasonality: Regular, repeating patterns or cycles (e.g., monthly or yearly patterns).
- Residual (or Noise): The irregular, random fluctuations that cannot be explained by trend or seasonality.

The choice between an additive or multiplicative model depends on how these components interact with each other in the data.

![image](https://github.com/user-attachments/assets/fdd0f9a0-5eba-4f9e-b1a6-8f7f24f5084d)

- If the seasonal fluctuations grow or shrink over time, we use a multiplicative model.
- If the seasonal fluctuations are constant, we use an additive model.

![image](https://github.com/user-attachments/assets/b45f9a8c-4c7c-4daf-8961-d2767a8bcdc3)

![image](https://github.com/user-attachments/assets/fb86e2e3-e5f6-4960-831f-27e9b25dc9b8)
1. Original Data (Top Plot - "births")
- There is strong seasonality with peaks and troughs repeating at regular intervals.
- The overall trend appears to be increasing at certain points and decreasing at others.

2. Trend Component (Second Plot - "Trend")
- The trend line shows an upward movement in the middle of the year, followed by a downward trend towards the end.
- This indicates that births may follow a general increasing and decreasing pattern over time.
- There is no strict stationarity since the trend is not constant.

3. Seasonal Component (Third Plot - "Seasonal")

- The pattern repeats in a consistent and periodic manner.
- Since I used period=12, this suggests that births exhibit a monthly seasonality effect.
- The seasonal fluctuations are roughly stable in amplitude, which is typical in an additive model.

4. Residual Component (Bottom Plot - "Resid")

- Residuals (random noise) should ideally be randomly scattered around zero with no clear pattern.
- Here, the spread of residuals seems wider at certain points, which might suggest some heteroscedasticity (variance changes over time).

Conclusions:
The data seem not stationary due to the clear trend and seasonality.

# Stationarity Check & Transformation
- In order a timeseires to be more easily forecasted, it should be stationary.
- In order a series to be stationary it should not be time dependent and it should hav esteady mean and std.
- Also the Dickey-Fuller Test shows all the necessary values in order to judge whether or not we have a stationary series.
## Dickey-Fuller Test: Initially, the data was not stationary.
- Null Hypothesis (H₀): The time series has a unit root → It is non-stationary (i.e., it has some trend or autocorrelation).
- Alternative Hypothesis (H₁): The time series does not have a unit root → It is stationary (i.e., the statistical properties like mean and variance remain constant over time).

If the p-value is low (< 0.05) and the test statistic is smaller than the critical values, we reject H₀ and conclude that the series is stationary.

![image](https://github.com/user-attachments/assets/40fb9200-7295-4510-b129-e5a2edfd5a94)

![image](https://github.com/user-attachments/assets/0cbe30c7-7de5-4c57-a75b-a08f1d5c911b)

It is observed that we do not have a stationary series.

1) The mean and standard deviation are not constant, meaning the series is non-stationary. 
2) The p-value (0.4829) is much greater than the common significance levels (0.01, 0.05, or 0.10), further confirming that we cannot reject the null hypothesis.
3) The test statistic (-1.6014) is higher than all the critical values (-3.449 at 1%, -2.869 at 5%, and -2.571 at 10%). This means we fail to reject the null hypothesis (H₀: The series has a unit root, meaning it is non-stationary).

- Differencing (.diff()) was applied to remove trends and make the series stationary.
# Re-evaluation:
## Plot again the decomposition to check the diffenced series
![image](https://github.com/user-attachments/assets/d3b4a978-ebc2-4f04-a10a-ead41a634865)

1. Observed Component (First Plot)
- The original series is now more stationary, with fluctuations centered around zero.

2. Trend Component (Second Plot)
- Unlike before, the trend component is much less pronounced and fluctuates around zero.

3. Seasonal Component (Third Plot)
- The seasonality remains strong and unchanged, which makes sense because differencing only removes trends, not seasonal patterns.

4. Residual Component (Fourth Plot)
- Ideally, residuals should look like white noise (randomly scattered points with no pattern).
- My residuals still show some variation and structure, suggesting the model might still capture some patterns.

## Check Stationarity again

![image](https://github.com/user-attachments/assets/9f5f00e9-4697-4c99-bba6-4cc66d9f5871)
![image](https://github.com/user-attachments/assets/33c1c921-2783-4b66-8782-bf2b3893899e)

Time series is now stationary:
- Mean seems stable over time and around zero now. Std seems more stable too.
- Test Statistic: -9.65 (way lower than all critical values)
- p-value: 1.41e-16 (<< 0.05, meaning we reject H0)
  
# Modeling Approaches
## 1. Naive Model (Baseline)
Assumes "What happened yesterday will happen tomorrow."

![image](https://github.com/user-attachments/assets/7ed9ec66-863c-463e-8723-e930bf704f59)

MSE: 9421121.5 , not a good result.

## 2. ARIMA (AutoRegressive Integrated Moving Average)
Chose d = 1 based on the differenced dataset.
ACF and PACF plots were analyzed to select p and q.

![image](https://github.com/user-attachments/assets/e2f2408f-ff99-4567-9955-55744ba8e251)

Data was split into train (330 rows) and test (34 rows).

![image](https://github.com/user-attachments/assets/79fd8b3d-d336-47dd-b428-26e0881a0978)

Experimented with different (p, d, q) combinations for ARIMA.


Best ARIMA model: (p=13, d=1, q=19).
![image](https://github.com/user-attachments/assets/93258cc9-7667-4a20-b1f0-146d0268835e)

## 3. SARIMA (Seasonal ARIMA)
Used auto_arima to automate hyperparameter selection:

![image](https://github.com/user-attachments/assets/bf4e92ea-ed30-4b29-8ff5-36b4b94025f3)
![image](https://github.com/user-attachments/assets/683f57c5-dfe2-4db2-b267-d9b7e790cc0d)


Performed diagnostic plotting to validate model performance.
### SARIMA
![image](https://github.com/user-attachments/assets/2b9f06ad-b09b-4bc6-a98b-0e3dfb1883dc)

### ARIMA
![image](https://github.com/user-attachments/assets/bb4287c7-04f0-43a3-b0ab-737c1ea64243)


## 4. Facebook Prophet
Implemented Prophet for a more flexible, trend-aware forecasting approach.

![image](https://github.com/user-attachments/assets/86ab5418-6c55-4bfb-aa75-a1f7c91e0f16)
![image](https://github.com/user-attachments/assets/e1794d3f-253f-4560-8b81-d418ef8acd0b)



  
# Results

### Model Evaluation
Metrics Used:

- Mean Absolute Error (MAE)
- Mean Absolute Percentage Error (MAPE)
  
| Model                     | MAE  | MAPE  |  
|---------------------------|------|------|  
| **ARIMA (p=13, d=1, q=19)** | 668  | 1.51% |  
| **SARIMA (auto-selected)**  | 765.6 | 1.78% |  
| **Prophet**                | 714  | **0.94%** | 

# Conclusion
- Since birth counts range between 6,000 and 13,000, MAPE is the most meaningful metric.
- Best Model: Prophet (MAPE = 0.94%) — it generalizes better across different birth values.
- ARIMA had a lower MAE but a higher MAPE, making it less optimal for relative error evaluation.

