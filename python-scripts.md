Here's a Python code example demonstrating key features of the Prophet forecasting library, drawing on information from the sources, including how to handle various data characteristics, tune parameters, and use custom settings:

```python
import pandas as pd
from prophet import Prophet
from prophet.plot import plot_plotly, plot_components_plotly
from prophet.make_holidays import make_holidays_df
import numpy as np

# 1. Data Preparation
# Example time series data (replace with your data)
data = {
    'ds': pd.to_datetime(['2023-01-01', '2023-01-02', '2023-01-03', '2023-01-04', '2023-01-05',
                           '2023-01-08', '2023-01-09','2023-01-10', '2023-01-11', '2023-01-12',
                           '2023-02-01','2023-02-02','2023-02-03','2023-02-04','2023-02-05',
                           '2023-02-08','2023-02-09','2023-02-10','2023-02-11','2023-02-12']),
    'y':,
    'extra_regressor':
}

df = pd.DataFrame(data)

# Sub-daily data example
sub_daily_data = {
    'ds': pd.to_datetime(['2023-01-01 00:00:00', '2023-01-01 01:00:00', '2023-01-01 02:00:00',
                           '2023-01-01 03:00:00', '2023-01-01 04:00:00', '2023-01-01 05:00:00']),
    'y':
}
df_sub_daily = pd.DataFrame(sub_daily_data)

# Monthly data example
monthly_data = {
    'ds': pd.to_datetime(['2023-01-01', '2023-02-01', '2023-03-01', '2023-04-01', '2023-05-01']),
    'y':
}
df_monthly = pd.DataFrame(monthly_data)

#Data with regular gaps example
gaps_data = {
    'ds' : pd.to_datetime(['2023-01-02', '2023-01-03', '2023-01-04','2023-01-05', '2023-01-09','2023-01-10', '2023-01-11','2023-01-12']),
    'y' :
}
df_gaps = pd.DataFrame(gaps_data)

# 2. Handling Holidays
# Create a holiday dataframe
holiday_data = {
    'ds': pd.to_datetime(['2023-01-01', '2023-01-10', '2023-02-01']),
    'holiday': ['New Year', 'Special Day', 'Another Day']
}
holidays = pd.DataFrame(holiday_data)

# Country-specific holidays (US)
#In Python, most holidays are computed deterministically and so are available for any date range; a warning will be raised if dates fall outside the range supported by that country.
#In R, holiday dates are computed for 1995 through 2044 and stored in the package as data-raw/generated_holidays.csv.
#If a wider date range is needed, this script can be used to replace that file with a different date range: https://github.com/facebook/prophet/blob/main/python/scripts/generate_holidays_file.py.
#Note that holiday dataframes for subdivisions can also be created.
nsw_holidays = make_holidays_df(
    year_list=[2019 + i for i in range(10)],
    country='AU',
    province='NSW'
)


# 3. Model Instantiation and Parameter Tuning
# Default model (linear growth, additive seasonality)
model = Prophet(holidays=holidays)

#Model with multiplicative seasonality
model_multiplicative_seasonality = Prophet(seasonality_mode='multiplicative')

# Model with custom parameters (logistic growth)
model_tuned = Prophet(
    growth='logistic', #default is linear
    changepoint_prior_scale=0.03, #tune between [0.001, 0.5] on a log scale
    seasonality_prior_scale=0.05, #tune between [0.01, 10] on a log scale
    holidays_prior_scale=1, #tune between [0.01, 10]
    #These parameters don't usually need to be tuned but are shown here for completeness
    #yearly_seasonality='auto', #auto if there is enough data
    #weekly_seasonality='auto', #auto if there is enough data
    #daily_seasonality='auto', #auto if there is enough data
    #changepoints=None, #use if manually setting changepoints
    #n_changepoints=25, #usually sufficient
    #mcmc_samples=0, #does not affect main prediction
    #interval_width=0.8, #adjust width of uncertainty interval
    #uncertainty_samples=1000 #adjust sample size for uncertainty intervals
    )

# Model with flat growth
model_flat_trend = Prophet(growth='flat')  #For time series with strong seasonality rather than trend changes

# 4. Custom Seasonality
#Note that by default prophet will automatically fit weekly and yearly seasonalities if the time series is more than two cycles long and daily seasonality for sub-daily time series
model_tuned.add_seasonality(name='monthly', period=30.5, fourier_order=5) #add custom seasonality
model_tuned.add_seasonality(name='quarterly', period = 91.25, fourier_order = 8, mode='additive') #custom seasonality that overrides multiplicative default
model_multiplicative_seasonality.add_seasonality(name='monthly', period=30.5, fourier_order=5, mode='multiplicative')  # multiplicative seasonality

# Conditional seasonality (e.g. different weekly seasonality pre- and post-COVID, not used in this example)
def is_nfl_season(ds):
    date = pd.to_datetime(ds)
    if (date.month > 8 or date.month < 2) and date.weekday() == 6:
        return True
    else:
        return False
#Model with conditional seasonality - note we create the conditional column
df['on_season'] = df['ds'].apply(is_nfl_season)
df['off_season'] = ~df['on_season']
model_conditional_seasonality = Prophet(weekly_seasonality=False) #first turn off the default weekly_seasonality
model_conditional_seasonality.add_seasonality(name='weekly_on_season', period=7, fourier_order=3, condition_name='on_season')
model_conditional_seasonality.add_seasonality(name='weekly_off_season', period=7, fourier_order=3, condition_name='off_season')


# 5. Adding Regressors
#Note that regressors must be added prior to model fitting
model.add_regressor('extra_regressor') #add additional regressor
model_tuned.add_regressor('extra_regressor', mode='additive') #specify mode for regressor

# 6. Handling Data with Gaps
# Ensure future predictions respect the gaps
#In this example we will just predict using all the available data but you can filter the data used for prediction


# 7. Logistic Growth
# Example with logistic growth - add a capacity
df['cap'] = 30  # Specify the carrying capacity
model_logistic = Prophet(growth='logistic')

# 8. Outlier Handling
# Setting outlier values to NA/None. Note that Prophet will still predict values for these dates if they are included in the future dataframe.
# In this example we will not remove any outliers
#df.loc[(df['ds'] > '2023-01-03') & (df['ds'] < '2023-01-05'), 'y'] = None


# 9. Model Fitting
#Fit the various models
model.fit(df)
model_multiplicative_seasonality.fit(df)
model_tuned.fit(df)
model_flat_trend.fit(df)
model_logistic.fit(df)
model_conditional_seasonality.fit(df)


# 10. Prediction
#Make future dataframes
future = model.make_future_dataframe(periods=30) #Make future dataframe with 30 future periods
future_sub_daily = model.make_future_dataframe(periods=30, freq='H') #make future dataframe with 30 future periods with hourly frequency for sub-daily data
future_monthly = model.make_future_dataframe(periods = 12, freq='MS') #make future dataframe for monthly data
future_gaps = model.make_future_dataframe(periods = 30)
future_conditional_seasonality = model_conditional_seasonality.make_future_dataframe(periods=30)

# Add capacity for logistic growth
future['cap'] = 30

#Ensure that regressor columns exist for future dataframe
future['extra_regressor'] = np.linspace(6, 12, num=len(future)) #note that regressors must be known for the future
future_conditional_seasonality['on_season'] = future_conditional_seasonality['ds'].apply(is_nfl_season)
future_conditional_seasonality['off_season'] = ~future_conditional_seasonality['on_season']

# Make predictions
forecast = model.predict(future) #predict future values using the basic model
forecast_multiplicative_seasonality = model_multiplicative_seasonality.predict(future) # predict using multiplicative seasonality
forecast_tuned = model_tuned.predict(future) #predict using tuned model
forecast_flat_trend = model_flat_trend.predict(future)
forecast_logistic = model_logistic.predict(future)
forecast_conditional_seasonality = model_conditional_seasonality.predict(future_conditional_seasonality)
forecast_sub_daily = model.predict(future_sub_daily)
forecast_monthly = model.predict(future_monthly)
forecast_gaps = model.predict(future_gaps)

# 11. Visualisation
# Interactive plots
#You need to have installed plotly for these
plot_plotly(model, forecast) #plot the basic model
plot_components_plotly(model, forecast) #plot the components of the basic model

#Visualise components for the other models
model_tuned.plot_components(forecast_tuned)
model_multiplicative_seasonality.plot_components(forecast_multiplicative_seasonality)
model_flat_trend.plot_components(forecast_flat_trend)
model_logistic.plot_components(forecast_logistic)
model_conditional_seasonality.plot_components(forecast_conditional_seasonality)
model.plot_components(forecast)

# 12. Model Persistence
# Serialize the model to JSON for portability. Do not use pickle
from prophet.serialize import model_to_json, model_from_json
with open('serialized_model.json', 'w') as fout:
    fout.write(model_to_json(model))

with open('serialized_model.json', 'r') as fin:
    loaded_model = model_from_json(fin.read())

# 13. Model Updating using Warm Starting
#Example of warm-starting with previous model parameters
def warm_start_params(m):
    res = {}
    for pname in ['k', 'm', 'sigma_obs']:
        if m.mcmc_samples == 0:
            res[pname] = m.params[pname]
        else:
            res[pname] = np.mean(m.params[pname])
    for pname in ['delta', 'beta']:
        if m.mcmc_samples == 0:
            res[pname] = m.params[pname]
        else:
            res[pname] = np.mean(m.params[pname], axis=0)
    return res
#Refit the model using warm start parameters
new_model = Prophet().fit(df, init=warm_start_params(model)) #warm-starting can speed up model refitting

# 14. Cross-Validation
# Example of cross-validation
from prophet.diagnostics import cross_validation
from prophet.diagnostics import performance_metrics, plot_cross_validation_metric
#Note that we cannot automatically determine the number of cross-validation parameters, but can set them using parameters
cv_results = cross_validation(model, horizon='7 days', initial='30 days', period='3 days', parallel='processes') #cross-validation
df_performance = performance_metrics(cv_results) #error metrics
plot_cross_validation_metric(cv_results, metric='rmse') #visualise error metrics

# 15. Scaling
# minmax scaling (use when the data has very large y values)
m_minmax_scaling = Prophet(scaling='minmax') #min max scaling
m_minmax_scaling = m_minmax_scaling.fit(df)

# 16. Inspecting Transformed Data
#For debugging you can use these methods
transformed_inputs = model.preprocess() #returns transformed inputs passed to stan
initial_params = model.calculate_init_params() #shows how model parameters are initialised
print(transformed_inputs)
print(initial_params)


#17. Coefficients for extra regressors
from prophet.utilities import regressor_coefficients
beta_coefficients = regressor_coefficients(model)
print(beta_coefficients)


# 18. Extracting Uncertainty Intervals
#By default, Prophet returns uncertainty intervals based on trend and observation noise.
#To get uncertainty in seasonality you need to specify mcmc_samples (which defaults to 0)
m_uncertainty = Prophet(mcmc_samples = 300).fit(df)
forecast_uncertainty = m_uncertainty.predict(future) # uncertainty is obtained by using the predictive_samples function
m_uncertainty.plot_components(forecast_uncertainty)

# 19. Setting Custom Trend
#To use a custom trend function, you need to download the source code, modify the trend function and install the local version.
#See the documentation for details.


# 20. Important Considerations
#Prophet is not under active development, and alternative libraries may give state of the art forecasting accuracy
#Consider using: statsforecast, hierarchicalforecast, neuralforecast, NeuralProphet

print("Code execution complete")
```

**Key Points in the Code:**

*   **Data Handling:** The code shows how to prepare data, including sub-daily, monthly data and data with regular gaps.
*   **Holiday Effects:** It demonstrates how to incorporate custom holidays and country-specific holidays.
*   **Parameter Tuning:** Examples of tuning `changepoint_prior_scale`, `seasonality_prior_scale`, and `holidays_prior_scale` are provided.
*   **Custom Seasonality:** The code shows how to add custom monthly and quarterly seasonalities and multiplicative seasonality, as well as seasonalities conditional on other factors.
*    **Additional Regressors:** The code illustrates how to add additional regressors, including specifying if they are additive or multiplicative.
*   **Logistic Growth:** The example includes how to use a logistic growth model with a carrying capacity.
*   **Outlier Handling:** The code has a commented out example of setting outliers to NA/None.
*   **Model Persistence:** The code shows how to save and load Prophet models using JSON serialization.
*  **Warm Starting:** The code demonstrates how to speed up model fitting using warm starting.
*   **Cross-Validation:** The code shows how to set up cross-validation and calculate performance metrics.
*   **Scaling**: The code shows how to scale the data using 'minmax' scaling.
*   **Inspecting Data**: The code shows how to use `.preprocess()` and `.calculate_init_params()` for debugging.
*   **Uncertainty Intervals**: The code shows how to obtain uncertainty intervals, including uncertainty in seasonality.
*   **Custom Trend**: The code comments on how a custom trend can be set, although this is not fully implemented in the example.
*   **Important Considerations:** The code makes a note that Prophet is not under active development, and lists alternative libraries.

**Important Notes:**
*   You will need to install Prophet and pandas (and optionally plotly) to run this code: `pip install prophet pandas plotly`.
*   The example data is for demonstration purposes and should be replaced with your own time series data.
*  The `warm_start_params` function shows one way to get the parameters of a fitted model, although for this to be valid you must be refitting the model with the same settings for `n_changepoints`, seasonality features and `mcmc` sampling.

This comprehensive example should help you understand how to use many of the key features of the Prophet library in your own forecasting tasks.
