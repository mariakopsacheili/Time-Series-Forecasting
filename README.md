# Time Series Forecasting: Daily Births in 2015
## Project Overview
This project focuses on time series forecasting for the daily number of births in 2015. The objective was to analyze the data, ensure stationarity and build predictive models using ARIMA and SARIMA  to determine the best approach.

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

### statsmodels.tsa.seasonal.seasonal_decompose
- The requirement that "x must contain 2 complete cycles" means that my time series data should include at least two full periods of the seasonal component I am trying to analyze.
- For weekly seasonality, I have 52 weeks in a year, which is more than 2 complete cycles.
- For monthly seasonality, I have 12 months, which is also more than 2 complete cycles.
- I will check weekly.
  
  ![image](https://github.com/user-attachments/assets/d4d1cdc0-65bb-4784-9f29-8a208a60854c)

  
1. Original Data (Top Plot - "births")
- x: date, y: number of births
- The overall trend appears to be increasing at certain points and decreasing at others.

2. Trend Component (Second Plot - "Trend")
- The trend line shows an upward movement in the middle of the year, followed by a downward trend towards the end.
- This indicates that births may follow a general increasing and decreasing pattern over time.
- There is no strict stationarity since the trend is not constant.

3. Seasonal Component (Third Plot - "Seasonal")

- Since I used period=52, this suggests that births might exhibit a weekly seasonality effect.


4. Residual Component (Bottom Plot - "Resid")
- In time series decomposition, residuals represent the portion of the time series that remains after removing the trend and seasonal components.
- Residuals (random noise) should ideally be randomly scattered around zero with no clear pattern.


# Stationarity Check & Transformation
- In order a timeseires to be more easily forecasted, it should be stationary.
- In order a series to be stationary it should not be time dependent and it should hav esteady mean and std.
- Also the Dickey-Fuller Test shows all the necessary values in order to judge whether or not we have a stationary series.
## Dickey-Fuller Test: Initially, the data was not stationary.
- Null Hypothesis (H₀): The time series has a unit root → It is non-stationary (i.e., it has some trend or autocorrelation).
- Alternative Hypothesis (H₁): The time series does not have a unit root → It is stationary (i.e., the statistical properties like mean and variance remain constant over time).

If the p-value is low (< 0.05) and the test statistic is smaller than the critical values, we reject H₀ and conclude that the series is stationary.


![image](https://github.com/user-attachments/assets/0cbe30c7-7de5-4c57-a75b-a08f1d5c911b)

It is observed that we do not have a stationary series.

1) The p-value (0.4829) is much greater than the common significance levels (0.01, 0.05, or 0.10), further confirming that we cannot reject the null hypothesis.
2) The test statistic (-1.6014) is higher than all the critical values (-3.449 at 1%, -2.869 at 5%, and -2.571 at 10%). This means we fail to reject the null hypothesis (H₀: The series has a unit root, meaning it is non-stationary).

- Differencing (.diff()) was applied to remove trends and make the series stationary.
# Re-evaluation:
## Plot again the decomposition to check the diffenced series
![image](https://github.com/user-attachments/assets/51e9bbb4-0c24-4131-ad98-a6c5feb23a6e)


1. Observed Component (First Plot)
- The original series is now more stationary, with fluctuations centered around zero.

2. Trend Component (Second Plot)
- There is not a specific trend.

3. Seasonal Component (Third Plot)
-  Seasonality still is seems to exists.

5. Residual Component (Fourth Plot)
- Ideally, residuals should look like white noise (randomly scattered points with no pattern).
- My residuals still show some variation and structure, suggesting the model might still capture some patterns.

## Check Stationarity again


![image](https://github.com/user-attachments/assets/33c1c921-2783-4b66-8782-bf2b3893899e)

Time series is now stationary:
- Test Statistic: -9.65 (way lower than all critical values)
- p-value: 1.41e-16 (<< 0.05, meaning we reject H0)
  
# Modeling Approaches
## 1. Naive Model (Baseline)
Assumes "What happened yesterday will happen tomorrow."

![image](https://github.com/user-attachments/assets/7ed9ec66-863c-463e-8723-e930bf704f59)

MSE: 9421121.5 , not a good result.

## 2. ARIMA (AutoRegressive Integrated Moving Average)
Chose d = 1 based on the differenced dataset, this will transform the data to stationary.
ACF and PACF plots were analyzed to select p and q.

![image](https://github.com/user-attachments/assets/2e0071a8-8f72-4c54-b064-cc714b98be36)


Original Data (not stationary) was split into train (330 rows) and test (35 rows).

![image](https://github.com/user-attachments/assets/d5e23f97-adf1-4372-a13e-420e63775bae)


Experimented with different (p, d, q) combinations for ARIMA.
1. ![image](https://github.com/user-attachments/assets/4964bc7b-1b40-4ef1-8f24-eac01f4efc87)
2. ![image](https://github.com/user-attachments/assets/4597f6c6-e421-49a7-8dc4-f149469a3282)
3. ![image](https://github.com/user-attachments/assets/59b541a1-51be-456c-aa81-5c2ffd41874e)


Best ARIMA model: (p=13, d=1, q=19).
![image](https://github.com/user-attachments/assets/a7ff0304-c380-4080-881d-8bad0497d6ec)


## 3. SARIMA (Seasonal ARIMA)
Used auto_arima to automate hyperparameter selection:

![image](https://github.com/user-attachments/assets/b51fb949-f84d-456c-b66a-cf69b598b205)

  
# Results

### Model Evaluation
Metrics Used:

- Mean Absolute Error (MAE)

  
| Model                     | MAE  | 
|---------------------------|------|
| **ARIMA (p=13, d=1, q=19)** | 668  |
| **SARIMA (auto-selected)**  | 5433.03 |


# Conclusion
Model  - ARIMA (p =13, d=1, q=19) has the lowest MAE (948.76), so is the best choise.

