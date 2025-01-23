# Handling DateTime Data

## Date Time Data

Pandas is oriented towards working with arrays of dates, whether used as an axis index or a column.

The `to_datetime` method parses many different kinds of date representations:

```python
dates = ["2011-07-06 12:00:00", "2011-08-06 00:00:00"]

pd.to_datetime(dates)
```

It uses `NaT` (Not a Time) as null values for datetime data.

```python
idx = pd.to_datetime(dates + [None])
idx
```

```python
pd.isna(idx)
```

Standard Python uses the `datetime` module to handle date and time data. Pandas has a `Timestamp` object that is similar to the `datetime` object. Pandas also has a `Timedelta` object that is similar to the `timedelta` object.

If you use `datetime` objects as index to a Series or DataFrame, Pandas will automatically convert them to `DatetimeIndex` objects.

```python
from datetime import datetime

dates = [
    datetime(2011, 1, 2),
    datetime(2011, 1, 5),
    datetime(2011, 1, 7),
    datetime(2011, 1, 8),
    datetime(2011, 1, 10),
    datetime(2011, 1, 12),
]

ts = pd.Series(np.random.standard_normal(6), index=dates)
ts
```

```python
ts.index
```

Like other Series, arithmetic operations between differently indexed time series automatically align on the dates:

```python
# [::2] selects every second element
ts + ts[::2]
```

`DatetimeIndex` is an array of `Timestamp` objects.

```python
ts.index[0]
```

You can index by passing a `datetime`, `Timestamp` or `string` that is interpretable as a date:

```python
ts[datetime(2011, 1, 7)]
ts[pd.Timestamp("2011-01-07")]
ts["2011-01-07"]
```

You can even specify the year or year-month strings:

```python
# date_range generate an array of dates
longer_ts = pd.Series(
    np.random.standard_normal(1000), index=pd.date_range("2000-01-01", periods=1000)
)
longer_ts
```

```python
longer_ts["2001"]
```

```python
longer_ts["2001-05"]
```

## Creating Sample Stock Price Data

First, let's create some sample stock price data to work with:

```python
# Create business dates for 100 days
dates = pd.date_range(start='2023-01-01', periods=100, freq='B')

# Create random walk prices for each stock
np.random.seed(42)
initial_prices = {
    'AAPL': 150.0,
    'GOOG': 2800.0,
    'IBM': 130.0,
    'MSFT': 240.0
}

price_data = {}
for stock, start_price in initial_prices.items():
    # Generate random daily returns
    returns = np.random.normal(loc=0.0001, scale=0.02, size=100)
    # Calculate prices using cumulative returns
    prices = start_price * (1 + returns).cumprod()
    price_data[stock] = prices

# Create DataFrame
price = pd.DataFrame(price_data, index=dates)
```

## Resampling Time Series

As you can see from above, the dates are on business days, if you want to change the frequency to calendar days (known as resampling):

```python
price_resampled = price.resample("D").asfreq()
price_resampled.head(10)
```

If you want to fill the na values with the most recent value, you can use the `.ffill()` method.

```python
price_resampled = price.resample("D").ffill()
price_resampled.head(10)
```

If you want to resample to a lower frequency (e.g. monthly) you need to provide an aggregation method:

```python
price_resampled = price.resample("MS").mean()
price_resampled.head()
```

For more resampling frequencies options, please refer to the official [documentation](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#offset-aliases)

## Window Functions

You can apply functions evaluated over a sliding window using the `rolling` method.

For example, to compute the 90-day moving average for Apple price:

```python
price["AAPL"].rolling(30).mean()
```

By default, rolling functions require all of the values in the window to be non-NA. This behavior can be changed to account for missing data and, especially at the beginning of the time series.

```python
price["AAPL"].rolling(30, min_periods=3).mean()
```
