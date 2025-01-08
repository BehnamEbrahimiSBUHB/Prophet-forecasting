Prophet does not have a built-in functionality to automatically treat weekends as holidays, but this effect can be achieved using a few different methods, including:

*   **Conditional Seasonalities:** The most appropriate method for handling weekend effects as a type of recurring 'holiday' is to use conditional seasonalities.
    *   First, create a boolean column in your dataframe that indicates whether a date falls on a weekend (Saturday or Sunday) or a weekday.
    *   Then, when creating the Prophet model, turn off the default weekly seasonality by setting `weekly_seasonality=False`.
    *   Add a custom weekly seasonality using the `add_seasonality` function, specifying a `condition_name` that corresponds to the weekend column.
    *   Add a second custom weekly seasonality using `add_seasonality`, with a `condition_name` corresponding to the weekday column.
    *   Ensure that the future dataframe also has these weekend and weekday boolean columns to allow the model to correctly apply the conditional seasonality.
    *   This approach will allow the model to learn a different weekly seasonal pattern for weekends compared to weekdays.
*   **Holidays:** Weekends could also be treated as recurring holidays by creating a dataframe with weekend dates, however, this method is not as efficient as using conditional seasonalities.
    *   A dataframe would need to be created that includes every weekend date within the dataset.
    *   The `lower_window` and `upper_window` parameters could be used to extend the holiday effect to include Friday evenings or Monday mornings if desired.
    *   The holiday dataframe would be passed to the Prophet constructor using the `holidays` parameter.
    *   Because the weekends are repeating, the model will assume the "holiday" occurs every weekend of the time series, and will apply it accordingly to the future dataframe.
    *  The `holidays_prior_scale` can be used to adjust the influence of the weekend 'holiday' effect.
*   **Additional Regressors:** A similar effect to the 'holiday' method above can be achieved by creating a binary regressor that is 1 on weekends and 0 on weekdays, and passing this to the `add_regressor` function. This requires the regressor values to be known in the future dataframe.
    *  This approach is similar to the holiday method, but might be preferable in some contexts.
*   **Data with Regular Gaps:** If your dataset only contains weekdays, and you intend to predict for weekdays, then you should be careful when creating future dataframes. When generating future dataframes, ensure that you only include the days that have data, otherwise the seasonalities may be poorly estimated for the absent periods.

**Important Considerations:**

*   **Choice of Method**: Conditional seasonality is likely the most appropriate method when modeling a repeating weekend effect because it will learn distinct patterns for weekdays and weekends, whereas the other two methods treat weekends as a simple additive effect.
*   **Model Complexity**: Adding conditional seasonality may increase model complexity, but may also more accurately model the data when weekly patterns are different during weekends compared to weekdays.
*   **Holiday Effects:** The holiday method will cause an additive effect to be applied to weekends, similar to other holidays, which is less flexible than the conditional seasonality method.
*  **Regular Gaps:** If your dataset has regular gaps, such as only weekday data, you should ensure that future predictions respect these gaps to avoid bias in the seasonality.

In summary, while Prophet does not have a direct feature to treat weekends as a built-in holiday, **conditional seasonalities is the recommended approach**. Alternatively, you can use the holidays functionality or add a regressor, although they are not as flexible. Carefully consider how your data is structured and the nuances of any weekly seasonality when choosing the best method to handle weekend effects.
