The `lower_window` and `upper_window` parameters in Prophet are used when modelling holiday effects, including one-off events or recurring holidays. These parameters specify a range of days before and after a holiday that should be included in the holiday effect.

*   **`lower_window`**: This parameter specifies the number of days *before* the holiday date to include in the holiday effect. A negative value means that the effect of the holiday will be extended to days before the actual date of the holiday. For instance, if `lower_window` is set to -1, the day before the holiday will be included.

*  **`upper_window`**: This parameter specifies the number of days *after* the holiday date to include in the holiday effect.  A positive value means that the effect of the holiday will be extended to days after the actual date of the holiday. For instance, if `upper_window` is set to 1, the day after the holiday will be included.

The holiday effect is inclusive of the upper bound, meaning the last day of the window is included in the holiday effect.

**Practical Uses**

*   **Extending Holiday Effects:** `lower_window` and `upper_window` are useful for capturing the full impact of holidays or events, which may not be limited to a single day. For example, if you want to include the effect of Christmas Eve in addition to Christmas Day, you could set `lower_window` to -1 and `upper_window` to 0. Similarly, for Black Friday following Thanksgiving, you might set `lower_window` to 0 and `upper_window` to 1.

*   **Modelling Event Spillovers**: These parameters can be used to model the impact of an event that has effects before or after the event itself.

**Implementation**

*   When providing holiday data to Prophet, you create a DataFrame that includes a `holiday` column and a `ds` (date) column. You can also include `lower_window` and `upper_window` columns to specify the number of days before and after the holiday to include in the holiday effect.

*   For one-off events, if no future dates for the holiday are provided, Prophet assumes the holiday will not repeat in the future, and therefore it will not be included in future predictions.

*   If a holiday repeats every year then it will be included in future predictions.

**Example**

To model the effect of Peyton Manning's playoff appearances with a 1-day window after each game, the following DataFrame could be used:

```python
playoffs = pd.DataFrame({
    'holiday': 'playoff',
    'ds': pd.to_datetime(['2008-01-13', '2009-01-03', '2010-01-16',
                            '2010-01-24', '2010-02-07', '2011-01-08',
                            '2013-01-12', '2014-01-12', '2014-01-19',
                            '2014-02-02', '2015-01-11', '2016-01-17',
                            '2016-01-24', '2016-02-07']),
    'lower_window': 0,
    'upper_window': 1,
})
```

In this example, the `lower_window` is set to 0, so the playoff effect starts on the day of the game, and the `upper_window` is set to 1, so the effect continues for one day after the game.
