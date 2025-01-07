1. How can I implement a custom trend function in Prophet?
Prophet offers built-in trend functions like piecewise linear, piecewise logistic growth, and flat. To use a custom trend, you need to download the source code from the Prophet GitHub repository. Then, modify the trend function within a local branch and install that modified version of the library. Examples of this process can be found in various pull requests demonstrating how to implement a step function trend, or another new trend. This involves changing the core model implementation.

2. How can I update a Prophet model with new data?
Prophet models are designed to be fit only once. When new data becomes available, a new model needs to be fitted, which can be computationally fast enough in most scenarios. However, you can potentially speed this process up using 'warm-starting'. This involves using the model parameters from a previously trained model as the initial parameters for a new fit on the updated data. This is achieved by retrieving parameters from a trained model with warm_start_params then setting the init parameter when refitting the Prophet model with the new data.

3. What is 'minmax scaling' and when should I use it?
Prophet scales the target variable (y) before model fitting by dividing it by the maximum value in the history. This scaling can cause problems if the target variable contains very large values, which may compress the scaled data to a very small range (e.g., [0.9999...-1.0]). This can lead to a poor model fit. To address this, you can set the scaling parameter in the Prophet constructor to 'minmax' rather than the default 'absmax', which allows for better scaling on data with extremely large values.

4. How can I use cross-validation to evaluate a Prophet model's performance?
Prophet offers a cross_validation function which simulates historical forecasts by making predictions with varying cutoff dates. You can specify the forecast horizon (horizon), the initial training period (initial), and the time between cutoffs (period). The output is a dataframe containing actual values (y) and out-of-sample predictions (yhat) for each prediction date and cutoff. Custom cutoffs can also be provided as a list of dates. This allows evaluation by calculating error measures like Mean Squared Error (MSE), Root Mean Squared Error (RMSE), Mean Absolute Error (MAE) and Mean Absolute Percentage Error (MAPE). The performance_metrics utility calculates these metrics over rolling windows and can be visualised using plot_cross_validation_metric, allowing for observation of how error changes as the prediction horizon extends. Parallelisation can be used to speed up the cross validation process by setting parallel argument.

5. How can I handle unusual events, like COVID-19 lockdowns, that may affect my time series data?
To prevent sudden dips or spikes due to one-off events like lockdowns from unduly influencing the trend component of a Prophet model, you can treat those specific periods as holidays. You can define a DataFrame with columns for the start dates of the events (ds), and specify the lower_window and upper_window, indicating the duration of each event in days from the start date. By defining these periods as one-off holidays, you prevent the model from anticipating similar events in the future.

6. When should I use multiplicative seasonality instead of the default additive seasonality?
Prophet's default seasonality is additive, meaning the seasonal effect is added to the trend. However, for time series where the magnitude of seasonal fluctuations increases with the overall trend, multiplicative seasonality is more appropriate. An example is air passenger numbers, where seasonal peaks grow alongside the trend. You can set the seasonality_mode argument in the Prophet constructor to 'multiplicative' to model seasonality as a percentage of the trend.

7. How does Prophet handle non-daily data, such as monthly or sub-daily data?
Prophet can handle non-daily data:

Sub-daily data: Time series with timestamps should be formatted as 'YYYY-MM-DD HH:MM:SS'. Prophet automatically fits daily seasonality to sub-daily data.
Monthly data: Avoid daily forecasts when fitting on monthly data, as the model, being continuous time, will struggle with the unidentifiable data between monthly observations. To avoid issues with within-month overfit, use monthly forecasts or consider modelling yearly seasonality with a set of binary extra regressors (is_jan, is_feb etc). When using extra regressors set yearly_seasonality to False.
Regular gaps: If the data has regular gaps (e.g., only weekday data), ensure future predictions also respect these gaps as the seasonality may not be well estimated for the absent periods.
Aggregated data: With aggregated data, like weekly or monthly data, be aware that holiday effects might need to be shifted to correspond with the dates in the dataset. If holidays do not fall within the date represented by the time series they may be captured by the yearly seasonality, and only holidays that vary throughout the time series would be worth including as holidays.
8. What are some key parameters I can tune to improve the accuracy of a Prophet model?
Prophet has several tunable parameters, but not all of them need adjustments. Parameters to Tune:

changepoint_prior_scale: This is a regularization parameter for the trend, which influences how much the trend changes at changepoints. Too small and the model will underfit. Too large, and it will overfit (perhaps even capturing yearly seasonality). Tune within a range like [0.001, 0.5] on a log scale.
seasonality_prior_scale: This controls the flexibility of seasonal patterns. It can be adjusted from [0.01, 10] on a log scale. A higher value allows for larger fluctuations, a lower value shrinks the seasonality magnitude.
holidays_prior_scale: Similar to seasonality_prior_scale this controls the flexibility of holiday effects and can be tuned on a range of [0.01, 10].
seasonality_mode: Set to 'additive' or 'multiplicative' based on whether the seasonality effect is added or is a proportion of the trend. Parameters Not Usually Tuned:
growth: Usually either 'linear' or 'logistic' depending on the known growth trend.
changepoints: For manually setting changepoints, usually left as default.
n_changepoints: The number of automatically placed changepoints is usually sufficient at the default of 25.
yearly_seasonality, weekly_seasonality, daily_seasonality: These are usually left as 'auto' if there is enough data.
holidays: This is used to pass a dataframe of specified holidays that should be tuned using holidays_prior_scale.
mcmc_samples: Affects uncertainty but not the main prediction.
interval_width: Adjusts the width of the uncertainty interval, not the actual prediction.
uncertainty_samples: Adjusts the sample size for uncertainty intervals, which primarily affects the smoothness of uncertainty estimates.
