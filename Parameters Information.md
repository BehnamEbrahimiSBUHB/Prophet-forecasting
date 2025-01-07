When tuning hyperparameters in a Prophet model, it's important to understand which parameters are most impactful and how they affect the model's fit and predictive accuracy. Here's a detailed overview of key parameters, considerations, and tips for hyperparameter tuning:

**Parameters to Tune:**

*   **`changepoint_prior_scale`**: This is arguably the most impactful parameter. It controls the flexibility of the trend, influencing how much the trend changes at detected changepoints.
    *   A **lower** value makes the trend **less** flexible, potentially leading to **underfitting**, where variance that should be modeled with trend changes ends up in the noise term.
    *   A **higher** value makes the trend **more** flexible, potentially leading to **overfitting**, where the trend might capture yearly seasonality.
    *   The default value is 0.05.
    *   A suitable tuning range is often **[0.001, 0.5]** on a **log scale**. This is because it is a regularization parameter that effectively acts like a lasso penalty.
    *   **Tip**: If the trend appears to be underfit, increase this value, and if it overfits, decrease it.

*   **`seasonality_prior_scale`**: This parameter controls the flexibility of seasonal patterns.
    *   A **higher** value allows for **larger fluctuations** in seasonality, whereas a **lower** value shrinks the magnitude of the seasonality.
    *   The default value is 10, which applies basically no regularization.
    *   A reasonable tuning range is **[0.01, 10]**, often on a **log scale**.
    *   **Tip:** If the seasonality seems too small, increase this value, and if it is overfit, decrease it.

*   **`holidays_prior_scale`**: This controls the flexibility of holiday effects.
    *   Similar to `seasonality_prior_scale`, it defaults to 10.0, applying little regularization.
    *   It can be tuned on a range of **[0.01, 10]**.
     *   A smaller value can be used to smooth these effects.

*   **`seasonality_mode`**: This parameter determines whether seasonality is modelled as an additive or multiplicative component.
    *   The default is **`additive`**, meaning the seasonal effect is added to the trend.
    *   **`Multiplicative`** seasonality is more appropriate for time series where the magnitude of seasonal fluctuations increases with the overall trend. For example, air passenger numbers tend to have seasonal peaks that grow along with the overall trend.
    *   This parameter is best identified by looking at the time series and seeing if the magnitude of seasonal fluctuations grows with the magnitude of the time series.

**Parameters That Might Be Tuned (with caution):**

*   **`changepoint_range`**: This parameter determines the proportion of the history where trend changes are allowed.
    *   The default is 0.8, meaning changepoints are only placed in the first 80% of the time series.
    *   It's generally conservative to avoid overfitting to trend changes at the very end of the time series, where there isn't sufficient data to fit it well.
    *   It is difficult to tune this parameter effectively with cross-validation over cutoffs, so it should probably only be tuned over a large number of time series. In that setting, a range of **[0.8, 0.95]** may be reasonable.

**Parameters Not Usually Tuned:**

*   **`growth`**: Options are `linear` or `logistic`. This is typically set based on the known trend of the data.
*   **`changepoints`**: For manually specifying changepoint locations, usually left as the default (automatic).
*   **`n_changepoints`**: The number of automatically placed changepoints, usually sufficient at the default of 25. Focus on tuning `changepoint_prior_scale` instead.
*   **`yearly_seasonality`, `weekly_seasonality`, `daily_seasonality`**: These are usually left as `'auto'` if there is sufficient data. If there is more than a year of data it is likely better to leave yearly seasonality on and reduce its effects with the  `seasonality_prior_scale`.
*   **`holidays`**: This is used to pass a dataframe of specified holidays and should be tuned using  `holidays_prior_scale`.
*    **`mcmc_samples`**: This parameter affects the uncertainty but not the main prediction. It also increases the computation time considerably, and may be unnecessary for some applications.
*   **`interval_width`**: Adjusts the width of the uncertainty interval but not the actual prediction.
*   **`uncertainty_samples`**: Adjusts the sample size for uncertainty intervals, affecting the smoothness of uncertainty estimates, but not the prediction.

**Cross-Validation for Tuning**

*   Prophet's `cross_validation` function simulates historical forecasts by making predictions with varying cutoff dates. This is key for evaluating hyperparameter performance.
    *   You can specify the forecast horizon (`horizon`), the initial training period (`initial`), and the time between cutoffs (`period`).
    *   The output is a dataframe with actual values (`y`) and out-of-sample predictions (`yhat`).
    *   Custom cutoffs can be provided as a list of dates.
*  The `performance_metrics` utility calculates error measures like MSE, RMSE, MAE, and MAPE.
    *   These metrics are computed over rolling windows and can be visualised using `plot_cross_validation_metric`.
    *    Custom performance metrics can be registered.
*   Hyperparameter tuning can be done by evaluating parameters with cross-validation, often using a parameter grid. It is possible to parallelize the evaluation of parameters.
*  **Tip:**  Different performance metrics may be appropriate for different use cases.

**Additional Tips:**

*   **Log Scale:** Parameters like `changepoint_prior_scale` and `seasonality_prior_scale` are often tuned on a log scale, because they represent regularization penalties.
*   **Outlier Handling:** Outliers should be removed from the training data by setting their values to `NA` or `None`. Prophet will still predict values for removed outliers if their dates are included in the future dataframe.
*   **Warm Starting:** When updating a model with new data, warm starting can speed up the process. This involves using the model parameters from a previously trained model as the initial parameters for a new fit. This is achieved using `warm_start_params` and the `init` parameter when refitting the Prophet model. However, warm starting may not be beneficial if there are large changes in the data.
*   **Scaling:** If the target variable (`y`) has very large values, using `scaling='minmax'` in the Prophet constructor can prevent the scaled data from being compressed into a small range, potentially improving the fit.
*   **Data Gaps**: If your data has regular gaps, make predictions only for periods with data, to avoid bias in seasonality.
*   **Monthly Data**: For monthly data, make monthly forecasts and consider using binary regressors like "is\_jan", "is\_feb", etc., with `yearly_seasonality=False` to avoid overfitting the seasonality.
*   **Visualisation:** Use the `.plot()` and `.plot_components()` methods to visualise the forecast and its components, including interactive plots with Plotly (Python) or dygraphs (R).
*   **Debugging:** Use `.preprocess()` to see the inputs passed to the Stan routine and `.calculate_init_params()` to see how model parameters are initialised.
*   **Multiplicative Seasonality**: If the magnitude of seasonal fluctuations increases with the overall trend, use `seasonality_mode='multiplicative'`.

**Important Considerations:**

*   **Prophet is no longer under active development**. The documentation suggests using alternative libraries for state-of-the-art forecasting accuracy.
*   **Model Refitting:** Prophet models must be refit when new data becomes available. They are designed to be fit only once.

By carefully considering these parameters and using cross-validation for evaluation, you can significantly improve the accuracy and reliability of your Prophet models.
