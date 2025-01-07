Okay, here is a detailed timeline of the main events and a cast of characters based on the provided sources relating to the Prophet forecasting library:

Timeline of Main Events & Features Introduced
This timeline is organised by feature introduction and then key examples of how the library is used. The date of specific examples will be less meaningful than the logic flow.

Core Functionality & Initial Release (Timeframe implied but not specifically stated):

Core Model: Prophet is released with the ability to decompose time series data into trend, seasonality, and holiday components.
Trend Modeling: Prophet implements a piecewise linear trend by default. It can also model with a piecewise logistic trend.
Seasonality Modeling: The library includes built-in support for yearly, weekly, and daily seasonality, using Fourier series for approximation.
Holiday Effects: Functionality is implemented to incorporate the effects of holidays or other special events. Users must provide a dataframe with holiday dates.
Regression: Implements an interface for adding other data streams as "regressors."
Basic Forecasting: Core functionalities for making future forecasts using make_future_dataframe and predict. Basic plotting capabilities are available with plot and plot_components.
R and Python APIs: Prophet is released with both an R and Python API for model creation, fitting and forecasting.
Model Saving: Functionality to save models using saveRDS and readRDS in R and serialization to JSON in Python.
Key Examples & Use Cases (Timeframe implied but not specifically stated):

Peyton Manning Example: The library is demonstrated with a time series of Peyton Manning's page views, showing examples of basic model use, including holiday implementation with playoff and Super Bowl dates.
Air Passengers Example: This demonstrates the need for multiplicative seasonality, where seasonal fluctuations grow with the trend, showcasing a scenario where the default additive seasonality is inappropriate.
Yosemite Temperatures Example: This shows use cases for sub-daily data with 5-minute resolution, highlighting Prophet's ability to handle high-frequency time series and automatically fit daily seasonality.
Feature Enhancements and Specific Problem Solutions (Chronological Order implied, some overlap):

Sub-Daily Data Support: Prophet gains support for sub-daily data, requiring timestamps in YYYY-MM-DD HH:MM:SS format. Daily seasonality is automatically included when using this.
Data with Regular Gaps: Guidance is provided on handling time series data with regular gaps (e.g., only observations during specific hours or only weekdays).
Monthly Data: Prophet gets the capability to handle monthly data, with a caution to use monthly forecasts for monthly data. It also outlines usage of extra binary regressors for modeling seasonality in monthly data
Multiplicative Seasonality: seasonality_mode is introduced as a parameter allowing the user to choose between additive and multiplicative seasonality. Extra regressors can be specified to be additive or multiplicative also.
Trend Changepoints: Automatic changepoint detection is introduced. Parameters n_changepoints and changepoint_prior_scale are made available for influencing trend flexibility.
Trend Flexibility: The changepoint_prior_scale parameter is introduced and described for adjusting the flexibility of the trend model, with advice for both under and over-fitting situations.
Specifying Changepoints: Users are given the option to manually specify changepoint locations via a changepoints parameter.
Custom Seasonalities: Support for adding custom seasonalities (e.g., monthly, quarterly) is implemented via add_seasonality, including the ability to set the fourier order and prior scale of seasonalities.
Seasonalities Depending on Other Factors: Support is added to use "condition_name" so that seasonalities can vary depending on other time series.
Prior Scales: Methods to adjust prior scales for seasonality and holidays to smooth or constrain their impact on the forecast are introduced, including seasonality_prior_scale and holidays_prior_scale.
Additional Regressors: The add_regressor method allows users to add custom regressors, not necessarily binary indicators and with their own parameters like standardisation and prior scales.
Coefficient Extraction: Ability to extract beta coefficients for extra regressors is added, using regressor_coefficients.
Uncertainty Intervals: interval_width is added for customising the width of the uncertainty intervals.
Outlier Handling: Guidance is provided for handling outliers by setting their values to NA/None.
Model Updating: The documentation explains that Prophet models must be refit when new data becomes available and provides methods for 'warm-starting' a model from previously fitted parameters.
Custom Trends: Guidance is given on how to implement custom trends, beyond the built-in piecewise linear, piecewise logistic growth and flat options.
Cross Validation: Functionality for automatic cross validation across a range of historical cutoffs is added using cross_validation and associated diagnostics. Error metrics can be calculated with performance_metrics and visualised using plot_cross_validation_metric.
Parallelisation: Support is added for parallel cross validation via parallel parameter.
Hyperparameter Tuning: Cross validation becomes the basis for tuning the models hyperparameters.
MinMax Scaling (v1.1.5): scaling='minmax' is introduced to handle situations where data has large 'y' values that compress into a very small range.
Inspecting Transformed Data (v1.1.5): Methods .preprocess() and .calculate_init_params() are made available for debugging and inspecting how data is transformed and initialised before passing to Stan.
Practical Examples of How to Adapt the Model (Timeframe implied but not specifically stated):

COVID-19 Lockdowns as Holidays: A case study demonstrates how to treat COVID-19 lockdowns as one-off holidays to prevent them from influencing the overall trend. A practical example uses a pedestrian activity time series with multiple lockdowns as an example
Adjusting Weekly Seasonality Post-COVID: A method to adapt weekly seasonality before and after a significant event like COVID-19 by adding conditional seasonalities is outlined.
Flat Growth: A flat growth trend is introduced and its use in situations with strong exogenous regressors is outlined, particularly in cases where the target variable is driven by other metrics.
Further Development and Recommended Libraries (Timeframe implied by references):

No Further Model Development: The documentation explicitly states that there are no plans to further develop the underlying Prophet model. It recommends alternative libraries for state-of-the-art forecasting.
Cast of Characters (Key People / Roles Implied)
While the documentation doesn't explicitly name individuals, we can infer the roles and 'characters' involved:

The Prophet Developers (Implied): The individuals/team responsible for creating, maintaining, and evolving the Prophet library. They are the driving force behind its features, bug fixes, and documentation. They have actively encouraged community contribution, but have also decided to discontinue further development of the core model.
The R Community: R users of the Prophet library, who are able to use the prophet function and its other associated helper functions.
The Python Community: Python users of the Prophet library, who use the Prophet class and its associated methods.
The Users of Prophet (Implied): The individuals who utilise the Prophet library for their time series forecasting needs. They interact with the software, test out the features, and contribute to its use cases.
The Stan Developers (Implied): The developers of the Stan probabilistic programming language, which serves as the back-end for Prophet's modelling. Users interact with Stan indirectly through Prophet
The Dask Developers (Implied): The developers of the Dask library, whose parallel computing capabilities are leveraged in Prophet's cross-validation functionality.
The Holidays Library Maintainers (Implied): The individuals who contribute to and maintain the holidays package in Python. This is a key package required for many holiday calculations.
The cmdstanr Developers (Implied): The developers of the cmdstanr Stan backend, who users can engage with as an alternative to rstan.
Peyton Manning (Historical Figure): Referenced as the subject of an example time series data set, used to demonstrate various features of Prophet.
This timeline and cast of characters provide a good overview of the features, development, and key concepts surrounding the Prophet forecasting library.
