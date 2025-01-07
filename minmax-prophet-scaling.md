In the context of Prophet, a "large value" for the target variable `y` is relative and is not explicitly defined by a specific numerical threshold in the sources. Instead, the concept of a large value is tied to its potential to cause issues with the default scaling process within Prophet.

Here's a breakdown of how "large values" are problematic and how to identify them:

*   **Default Scaling Issues**: Prophet's default scaling method divides the target variable `y` by its maximum value in the historical data before model fitting.
*   **Compression of Scaled Data:** When the dataset contains extremely large values, this default scaling can compress the scaled `y` values into a very small range. For example, the scaled values might fall within a narrow interval like [0.9999... - 1.0].
*   **Impact on Model Fit**: This compression can lead to a poor model fit, as the model struggles to discern meaningful patterns in such a restricted range.

**How to Identify the Need for Minmax Scaling:**

*   **Look for Extreme Values:** If your target variable `y` has values that are significantly larger than most of the other values, this could indicate a need for `minmax` scaling.
*   **Check the Range of Scaled Data:** If the model is fitting poorly, you can inspect the scaled data using the `.preprocess()` method to see how the raw data has been transformed before being passed to the Stan fitting routine. If the scaled `y` values are compressed into a very small range, this indicates that `minmax` scaling might be appropriate.
*   **Consider the Nature of Your Data**: If your data represents something that is known to have very large values - for example, financial data, large infrastructure costs or large-scale web traffic - it might be worth considering the need for `minmax` scaling.
*  **Observe Model Performance**: If the model fit is poor or the forecast seems unreasonable, then one thing to check is whether the default scaling is appropriate for your data. If your target variable `y` has a wide range of values, and if the model is struggling to fit the data, consider using `scaling = 'minmax'`.
*   **Example Data**: The documentation uses an example dataset called `data3.csv` for demonstrating `minmax` scaling. This dataset is described as having "very large y values". This implies that if your data is similar to `data3.csv` in terms of value ranges, `minmax` scaling might be appropriate for you too.

**When to Apply Minmax Scaling:**

*   **When Default Scaling Fails:** Use `scaling = 'minmax'` in the Prophet constructor when the default scaling method (`absmax`) results in compressed, scaled `y` values.
*   **As a Precaution:** If you know your data has potentially large values, applying `minmax` scaling can be a good initial step to ensure a proper model fit.
* **In the Model Initialization**: The scaling parameter is specified when you create an instance of the Prophet model. For example, you would use `m = Prophet(scaling='minmax')` in Python.

In summary, a "large value" in Prophet is not a specific number, but rather a value that, when combined with the default scaling, leads to a compressed range of scaled target variable values, which in turn compromises the model fit. The decision to use `minmax` scaling should be driven by the characteristics of your data and your evaluation of the model fit.
