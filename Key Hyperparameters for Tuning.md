Several hyperparameters can significantly influence the accuracy of a Prophet model, with some having a greater impact than others. It is noted that not all parameters need tuning.

**Key Hyperparameters for Tuning:**
*   **`changepoint_prior_scale`**: This is a critical regularization parameter that controls the flexibility of the trend. It determines how much the trend changes at changepoints.
    *   A smaller value will make the trend less flexible and may lead to underfitting. Variance that should be modeled by trend changes may instead be handled by the noise term.
    *   A larger value makes the trend more flexible, potentially overfitting to noise and even capturing yearly seasonality.
    *   The default value is 0.05. A suitable tuning range is [0.001, 0.5], often on a log scale.
*   **`seasonality_prior_scale`**: This controls the flexibility of the seasonal patterns.
    *   A higher value allows larger seasonal fluctuations.
    *   A lower value shrinks the magnitude of the seasonality.
    *   The default is 10, which applies little regularization. A reasonable tuning range is [0.01, 10], often on a log scale.
*   **`holidays_prior_scale`**: This parameter controls the flexibility of holiday effects.
    *   It can be tuned on a range of [0.01, 10].
    *  The default is 10, applying little regularization.
*   **`seasonality_mode`**: This parameter determines whether seasonality is modeled as 'additive' or 'multiplicative'.
    *   The default is 'additive', where seasonal effects are added to the trend.
    *   'Multiplicative' seasonality is more appropriate when the magnitude of seasonal fluctuations grows with the trend. Multiplicative seasonality models seasonality as a percentage of the trend.
    *   This can be identified by examining the time series, to see if seasonal fluctuations grow with the trend.

**Parameters That May Need Tuning in Specific Cases:**
*   **`changepoint_range`**: This determines the proportion of the history in which the trend is allowed to change.
    *   The default is 0.8, meaning no trend changes are fitted in the last 20% of the time series.
    *   It is suggested that this is not tuned, except perhaps over a large number of time series. A reasonable range would be [0.8, 0.95].

**Parameters Not Usually Tuned:**
*   **`growth`**: This is usually set to 'linear' or 'logistic' depending on the known growth trend.
*   **`changepoints`**: This is used for manually specifying changepoint locations and is usually left as default.
*   **`n_changepoints`**: The number of automatically placed changepoints is usually sufficient at the default of 25.
*   **`yearly_seasonality`, `weekly_seasonality`, `daily_seasonality`**: These are usually left as 'auto' if there is sufficient data.
*   **`holidays`**: This is used to pass a dataframe of specified holidays and is tuned using `holidays_prior_scale`.
*   **`mcmc_samples`**: This affects uncertainty, but not the main prediction.
*   **`interval_width`**: This adjusts the width of the uncertainty interval, but not the actual prediction.
*    **`uncertainty_samples`**: This adjusts the sample size for uncertainty intervals and affects the smoothness of uncertainty estimates.

It is important to note that these are general recommendations, and the specific optimal values for these parameters will depend on the unique characteristics of each time series being modeled. Cross-validation can be used for hyperparameter tuning.
