Okay, here's the provided content converted into Markdown, formatted as a Prophet Model Study Guide:

# Prophet Model Study Guide

## Quiz

### Instructions: Answer the following questions in 2-3 sentences each.

*   How can you save a Prophet model in Python, and why is it important not to use pickle?
*   Describe the 'flat' trend option in Prophet and when it might be useful.
*   What should you be mindful of when 'warm-starting' a Prophet model with new data?
*   Explain the purpose of cross-validation in Prophet and what it helps assess.
*   Name three hyperparameters that can be tuned in a Prophet model and describe their impact.
*   How can you treat COVID-19 lockdowns as a one-off event when modelling with Prophet?
*   Explain the difference between additive and multiplicative seasonality, and when multiplicative seasonality might be appropriate.
*   What is a key consideration when using Prophet with sub-daily data, especially if there are regular gaps?
*   How should you handle outliers in a time series when using Prophet?
*   Describe how Prophet handles automatic changepoint detection.

## Quiz Answer Key

*   In Python, Prophet models should be saved using built-in serialization functions to JSON. Pickling is discouraged because the Stan backend may cause issues under certain Python versions.
*   The 'flat' trend forces the growth rate to be constant rather than changing. It's useful when time series have strong seasonality or when relying on exogenous regressors.
*   When warm-starting a model, consider that it may not be effective for large data updates and that the number of changepoints should remain consistent.
*   Cross-validation in Prophet measures forecast error by fitting the model to historical data up to certain cutoff points, helping to assess how well the model generalises to unseen data.
*   Three tunable parameters are: `changepoint_prior_scale` (trend flexibility), `seasonality_prior_scale` (seasonality flexibility), and `holidays_prior_scale` (holiday effect flexibility). Tuning these can influence how closely the model fits to observed data.
*   COVID-19 lockdowns can be treated as one-off holidays by creating a DataFrame with the dates of the lockdowns which have upper windows corresponding to the length of the lockdown. Since future dates are not specified for these, Prophet will treat these as one-off events.
*   Additive seasonality adds a constant effect of seasonality to the trend, while multiplicative seasonality makes the effect of the seasonality proportional to the trend's magnitude. Multiplicative seasonality is appropriate when seasonal fluctuations increase alongside trend increases.
*   When using sub-daily data, only make predictions for time windows that are present in the historical data. If the history contains only parts of the day then forecasting on other parts of the day may be unconstrained.
*   Outliers should be removed from the time series data by setting their values to NA in the history so Prophet does not fit to them and negatively impact the forecast.
*   Prophet automatically detects changepoints by specifying potential changepoints at multiple places and using a sparse prior to limit the number of change-points that are used in the final fit of the model.

## Essay Questions

### Instructions: Answer the following questions in essay format (approx. 500-750 words each). Please note that you should not supply answers to these questions, these are prompts to allow further thought about the content covered in the sources.

*   Discuss the importance of trend modelling in Prophet and analyse the options available for controlling trend flexibility, citing examples from the source materials. How can understanding the potential for trend changes in the future help you develop forecasts?
*   Explain the concept of seasonality within the Prophet model, detailing how you can adjust the parameters of the model to accurately capture seasonal fluctuations (be sure to include conditional seasonalities). Further, illustrate why it is critical to evaluate if seasonality has changed over the course of the historical data and if the model is capturing the changes effectively.
*   Discuss how Prophet handles holidays and special events, both pre-built and custom. How can you use this functionality to accurately model the effects of various external events in your time series data, including potentially irregular shocks like COVID-19 lockdowns?
*   Describe the role of cross-validation and hyperparameter tuning in refining the performance of a Prophet model. Explain the key hyperparameters you might want to tune. In your answer, also include the appropriate range of these parameters as well as whether or not the parameters should be tuned on a log scale or not.
*  Explain the challenges of forecasting time series with shocks and non-daily data using Prophet. How can you use the described features of the model to deal with these challenges?

## Glossary of Key Terms

*   **Additive Seasonality:** A type of seasonality where the seasonal effect is added to the trend to get the forecast, and is constant in magnitude throughout the data.
*   **Changepoints:** Points in a time series where the trend is allowed to change, and are used to represent a shift in trend and are automatically detected by default by the Prophet model.
*   **Changepoint Prior Scale:** A hyperparameter that controls the flexibility of the trend, with higher values allowing more trend changes to occur. It is essentially an L1 regularization penalty on trend changepoints.
*   **Conditional Seasonality:** Seasonality that depends on a particular factor, allowing seasonal patterns to vary based on external conditions or data.
*   **Cross-Validation:** A technique to measure forecast error using historical data by selecting cutoff points in the history and comparing forecasted values to actual values after those cutoff points.
*   **Exogenous Regressor:** An external variable that may influence the target variable, and can be added as a feature in the prophet model.
*   **Flat Trend:** A trend model in Prophet where the growth rate of the trend is forced to be constant.
*   **Fourier Order:** The number of terms in the partial sum used to model seasonality, which determines the frequency changes the seasonality can fit.
*  **Holidays Prior Scale:** A hyperparameter that controls the flexibility of holiday effects, with higher values allowing greater influence from holidays. It is essentially a regularization penalty on the impact of holidays.
*   **Horizon:** The length of time into the future for which forecasts are being made.
*   **Hyperparameter:** A parameter in a model whose value is set before the learning process begins, and is not learned from the data directly.
*   **MAPE (Mean Absolute Percent Error):** A metric used to measure forecast accuracy, calculated as the average of absolute percentage differences between predicted and actual values.
*   **MCMC (Markov Chain Monte Carlo):** A sampling method used to obtain estimates of uncertainty in seasonality by sampling the posterior predictive distribution.
*   **Multiplicative Seasonality:** A type of seasonality where the seasonal effect is multiplied by the trend to get the forecast, and the magnitude of the seasonal effect increases with the magnitude of the trend.
*   **Outlier:** An observation that is significantly different from other data points in a time series, and can negatively impact the fit of the model.
*   **RMSE (Root Mean Squared Error):** A metric used to measure forecast accuracy, calculated as the square root of the average squared differences between predicted and actual values.
*   **Saturating Forecast:** A forecast that approaches a maximum (or minimum) value, typically used in a logistic growth model.
*   **Seasonality Prior Scale:** A hyperparameter that controls the flexibility of seasonality, with higher values allowing seasonality to fit larger fluctuations. It is essentially an L2 regularization penalty on the impact of the seasonality.
*   **Time Series:** A sequence of data points ordered in time, typically at regular intervals.
*   **Trend:** The general direction of the time series data over time, which may be increasing, decreasing, or stable, and can be fit using several functions in Prophet (including linear, logistic growth, or flat).
*   **Warm-Starting:** Using parameters of a previous model to speed up the fitting process of a new model with additional data.
