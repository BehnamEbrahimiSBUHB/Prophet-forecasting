The Prophet model has several parameters that can be tuned to improve accuracy, although some parameters are not typically adjusted.

**Tunable Parameters**
*   **`changepoint_prior_scale`**: This regularization parameter controls the flexibility of the trend, influencing how much the trend changes at changepoints. A smaller value may lead to underfitting, while a larger value can cause overfitting. It is recommended to tune this parameter within a range of [0.001, 0.5] on a log scale.
*   **`seasonality_prior_scale`**: This parameter adjusts the flexibility of seasonal patterns. A higher value allows for larger fluctuations, while a lower value reduces the magnitude of seasonality. It can be tuned within a range of [0.01, 10] on a log scale.
*   **`holidays_prior_scale`**: Similar to `seasonality_prior_scale`, this parameter controls the flexibility of holiday effects. It can be tuned within a range of [0.01, 10].
*   **`seasonality_mode`**: This parameter determines whether seasonality is modeled as 'additive' or 'multiplicative'. Multiplicative seasonality is more appropriate when the magnitude of seasonal fluctuations increases with the overall trend.

**Parameters Not Usually Tuned**
*   **`growth`**: This parameter is usually set to either 'linear' or 'logistic' depending on the known growth trend. A flat growth can also be specified.
*  **`changepoints`**: This parameter is used for manually setting changepoint locations and is usually left as default for automatic changepoint detection.
*   **`n_changepoints`**: The number of automatically placed changepoints is usually sufficient at the default of 25.
*  **`yearly_seasonality`, `weekly_seasonality`, `daily_seasonality`**: These parameters are usually left as 'auto' if there is sufficient data.
*   **`holidays`**: This parameter is used to pass a dataframe of specified holidays that should be tuned using `holidays_prior_scale`.
*   **`mcmc_samples`**: This parameter affects uncertainty but not the main prediction.
*  **`interval_width`**: This parameter adjusts the width of the uncertainty interval, but not the actual prediction.
*   **`uncertainty_samples`**: This parameter adjusts the sample size for uncertainty intervals, primarily affecting the smoothness of uncertainty estimates.
*   **`changepoint_range`**: This is the proportion of the history in which the trend is allowed to change. It defaults to 0.8, and may be tuned in a fully automated setting.

**Additional Parameters**
*  **`scaling`**: This parameter is set to 'absmax' by default. It can be set to 'minmax' to handle datasets with very large values of 'y'.
*  **`fourier_order`**: This parameter determines the number of terms in the partial sum for seasonality estimation.
*  **`add_seasonality`**: This function is used to add custom seasonalities, such as monthly or quarterly, and includes the ability to set the `fourier_order` and prior scale of seasonalities.
*  **`add_regressor`**: This method adds custom regressors, which are not necessarily binary indicators, and can have their own parameters like standardisation and prior scales.

It is important to note that some parameters, such as `changepoint_prior_scale`, `seasonality_prior_scale`, and `holidays_prior_scale`, are regularization parameters, and are often tuned on a log scale. Cross-validation can be used to tune these hyperparameters.
