Here's a step-by-step guide to using the Prophet forecasting library, drawing from the provided sources:

**1. Installation**

*   **Choose your API:** Prophet has both R and Python implementations.
*   **R Installation:** Use `install.packages("prophet")`. R tools are required on Windows. You can optionally use `cmdstanr` as an experimental backend by setting the `R_STAN_BACKEND` environment variable to `CMDSTANR`.
*  **Python Installation:** Use `pip install prophet`.  Python 2 is not supported; the minimum version is 3.7.

**2. Data Preparation**

*   **Input Dataframe:** Prophet requires a dataframe with two columns: `ds` (datestamp) and `y` (numeric measurement to be forecasted).
    *   The `ds` column should be in a format that Pandas can understand. For dates, use YYYY-MM-DD; for timestamps, use YYYY-MM-DD HH:MM:SS.
*   **Sub-daily data:** Use the format YYYY-MM-DD HH:MM:SS for timestamps.
*  **Missing Data:**  Outliers are best handled by removing them by setting their values to NA or None in the history. Prophet will still predict values for removed outliers if their dates are included in the future dataframe.
*   **Monthly Data:** For monthly data, make monthly forecasts, and consider using binary regressors like `is_jan`, `is_feb`, etc. with `yearly_seasonality=False` to avoid overfitting.
*   **Data with gaps:** If your data has regular gaps, ensure your predictions only cover periods with data to avoid bias in seasonality.

**3. Model Fitting**

*   **Instantiate the Model:** Create a `Prophet` object, passing in any desired settings to the constructor.
*   **Fit the Model:** Call the `fit()` method of the Prophet object, passing in your historical dataframe.

**4. Making Predictions**

*   **Create a Future Dataframe:** Use `make_future_dataframe()` to create a dataframe with the dates you wish to predict. This function automatically includes historical dates for in-sample fits, unless manually excluded.
    *   Specify the number of periods to forecast into the future using the `periods` argument.
    *   For monthly data, use `freq='MS'`.
    *   For sub-daily data, use a frequency like `freq = 'H'` for hourly data.
*   **Generate Predictions:**  Call the `predict()` method of your Prophet object, passing in the future dataframe. This will return a dataframe with the predicted values (`yhat`) alongside uncertainty intervals (`yhat_lower` and `yhat_upper`) and component contributions.

**5. Visualisation**

*   **Plot Forecasts:** Use the `plot()` method to visualise the forecast.
*   **Plot Components:** Use the `plot_components()` method to visualise the trend, seasonality, and holiday components.
*   **Interactive Plots:** Use `plot_plotly` and `plot_components_plotly` (Python) for interactive plots. In R, use `dyplot.prophet()` for interactive plots using `dygraphs`.
    *   Note that you must install the necessary packages, such as `plotly` and `ipywidgets` for the Python interactive plots.

**6. Trend Modelling**

*   **Default Trend:** Prophet automatically detects trend changepoints using a piecewise linear trend by default.
*   **Changepoints:** The number of potential changepoints is set by `n_changepoints` but the trend is better tuned by adjusting the regularization using `changepoint_prior_scale`.
    *   A lower `changepoint_prior_scale` makes the trend less flexible; a higher value makes it more flexible, potentially overfitting. A suitable tuning range is \[0.001, 0.5], often on a log scale.
*   **Custom Trends:** To use a custom trend, download the source code, modify the trend function, and install your local version of the library.
*   **Trend Types:** The `growth` parameter can be set to 'linear' or 'logistic', the latter being suitable if there's a known saturation point.
*   **Flat Trend:**  Set `growth = 'flat'` to use a flat trend. This may be appropriate if there are strong exogenous regressors.
*   **Manual Changepoints:** Use the `changepoints` parameter to manually specify changepoint locations.

**7. Seasonality**

*   **Automatic Seasonality:** Prophet automatically fits weekly and yearly seasonality if the time series has more than two cycles. Daily seasonality is fit to sub-daily data.
*   **Fourier Series:** Seasonality is modeled using a partial Fourier sum. The `fourier_order` parameter determines how quickly the seasonality can change. The default `fourier_order` is 3 for weekly and 10 for yearly seasonality.
*   **Custom Seasonality:** Use `add_seasonality()` to add custom seasonalities (e.g. monthly, quarterly, hourly). The period must be specified in days.
*   **Multiplicative Seasonality:** The default is additive seasonality. Use `seasonality_mode='multiplicative'` if seasonal magnitude grows with the trend.  Multiplicative and additive extra regressors will be shown on separate panels in the components plot.
*    **Conditional Seasonality:** Add conditional seasonality by adding a `condition_name` to the `add_seasonality` method. This allows you to create different seasonal patterns based on other columns in the dataframe.
    *   For example, you can add boolean columns to flag "pre-covid" and "post-covid" periods to model changes in weekly seasonality.

**8. Holiday and Event Handling**

*   **Holiday Dataframes:** Provide holiday data to the model via a DataFrame with a `holiday` column and a `ds` (date) column.
*   **Windows:** Use `lower_window` and `upper_window` to specify a range of days for a holiday effect.
*   **One-Off Holidays:** If holidays are specified that do not repeat, they will not be included in the forecast.
*   **Country Holidays:** Use `add_country_holidays()` for built-in country-specific holidays.
*    **Subdivision Holidays (Python):** Use `make_holidays_df` for generating holiday dataframes for subdivisions such as states.
*  **Holiday Prior Scale:** Use the `holidays_prior_scale` parameter to control the flexibility of holiday effects, with a default of 10.

**9. Additional Regressors**

*   **Add Regressors:** Use the `add_regressor()` method to add custom regressors.
*   **Additive or Multiplicative:** Regressors can be specified as additive or multiplicative, and will show up on separate panels in the components plot.
*  **Coefficient Extraction:** Extract the beta coefficients for the regressors using `regressor_coefficients`.

**10. Model Evaluation and Tuning**

*   **Cross-Validation:** Use the `cross_validation()` function for automated cross-validation across historical cutoffs.
    *   Specify the forecast horizon (`horizon`), initial training period (`initial`), and spacing between cutoff dates (`period`).
    *   The default is to use cutoffs every half of the horizon.
    *  Supply a list of dates to the `cutoffs` keyword for custom validation cutoffs.
*   **Performance Metrics:** The `performance_metrics()` function computes MSE, RMSE, MAE, MAPE, MDAPE, and coverage on the output of `cross_validation()`.
*  **Custom Performance Metrics:** Register custom performance metrics using the `@register_performance_metric` decorator.
*   **Hyperparameter Tuning:** Use cross-validation to tune hyperparameters, particularly `changepoint_prior_scale` and `seasonality_prior_scale` using a parameter grid.
    *   `seasonality_prior_scale` controls the flexibility of the seasonality.  A lower value shrinks the magnitude of the seasonality.  The default of 10 applies basically no regularization.
    *  A reasonable tuning range would be \[0.01, 10], often on a log scale.
*   **Parameters Not to Tune:**  The documentation notes that some parameters likely should not be tuned, including `growth`, `changepoints`, `n_changepoints`, `yearly_seasonality`, `weekly_seasonality`, `daily_seasonality`, `holidays`, `mcmc_samples`, `interval_width`, and `uncertainty_samples`.
*    **Parallelization:** Use `parallel="processes"` for single machine use, or `parallel="dask"` for distributed computation of cross-validation.

**11. Uncertainty Intervals**

*   **Posterior Predictive Distribution:** Uncertainty intervals are computed as quantiles of the posterior predictive distribution estimated via Monte Carlo sampling.
*   `interval_width`: Sets the width of the uncertainty intervals (default 0.8 for 80% intervals).  Changing this does not change the forecast `yhat`.
*  `uncertainty_samples`:  Sets the number of Monte Carlo samples to estimate the predictive interval. Increasing it reduces the Monte Carlo error, smoothing out the uncertainty intervals. It only affects the uncertainty intervals and does not affect the forecast.
*  **Impact of Flexibility:** Increased trend flexibility from `changepoint_prior_scale` increases forecast uncertainty.
*  **Raw Samples:** Access raw posterior samples using `m.predictive_samples(future)` (Python) or `predictive_samples(m, future)` (R).
*  **Seasonality Uncertainty**: To get uncertainty in seasonality you must use full Bayesian sampling via the `mcmc_samples` parameter.

**12. Scaling**

*   **Default Scaling:**  Before model fitting, Prophet scales `y` by dividing by the maximum value in the history.
*  **MinMax Scaling:**  For datasets with very large `y` values, use `scaling='minmax'` in the Prophet constructor to prevent the `y` values from being compressed into a very small range.

**13. Model Updating**

*   **Refitting:**  Prophet models must be refit when new data becomes available.
*  **Warm-starting:**  You can potentially speed this process up by using 'warm-starting'. This involves using the model parameters from a previously trained model as the initial parameters for a new fit on the updated data. Retrieve parameters from a trained model with `warm_start_params` and setting the `init` parameter when refitting.

**14. Debugging**
*  **.preprocess()**: Returns the transformed inputs passed to the stan routine.
*  **.calculate_init_params()**:  Shows how the model parameters will be initialised.

**15. Handling Shocks**
*   **One-off events:** Treat COVID-19 lockdowns or similar events as one-off holidays to prevent them from affecting the overall trend.
*  **Conditional Seasonalities**: Account for changes in behaviour by using conditional seasonalities to model, for example, pre- and post-COVID weekly patterns differently.
*   **Outlier removal**: Remove outlier data from the training period, or discard older training data.

**16. Limitations**
*  **No Active Development:** Prophet is no longer under active development and alternative libraries are recommended for better forecasting accuracy.

This comprehensive guide should provide you with a detailed understanding of how to use Prophet, as described in the sources. Remember that Prophet is no longer under active development, and there are other more up-to-date libraries available.
