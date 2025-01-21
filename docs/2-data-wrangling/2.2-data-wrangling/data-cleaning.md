# Data Cleaning

## Handling Missing Data

Missing data is common in most data analysis applications. One of the goals in designing pandas was to make working with missing data as painless as possible. For example, all of the descriptive statistics on pandas objects exclude missing data by default.

The `isna` method gives us a Boolean Series with `True` where values are null:

```python
float_data = pd.Series([1.2, -3.5, np.nan, 0])

float_data
```

```python
float_data.isna()
```

In statistics applications, NA data may either be data that does not exist or that exists but was not observed (through problems with data collection, for example). When cleaning up data for analysis, it is often important to do analysis on the missing data itself to identify data collection problems or potential biases in the data caused by missing data.

The built-in Python `None` value is also treated as NA:

```python
string_data = pd.Series(["aardvark", np.nan, None, "avocado"])

string_data
```

```python
string_data.isna()
```

You can use `dropna` to filter out missing data. On a Series, it returns the Series with only the non-null data and index values:

```python
data = pd.Series([1, np.nan, 3.5, np.nan, 7])

data
```

```python
data.dropna()
```

```python
# which is equivalent to this

data[data.notna()]
```

With DataFrame objects, things are a bit more complex. You may want to drop rows or columns which are all NA or just those containing any NAs. `dropna` by default drops any row containing a missing value:

```python
data = pd.DataFrame(
    [
        [1.0, 6.5, 3.0],
        [1.0, np.nan, np.nan],
        [np.nan, np.nan, np.nan],
        [np.nan, 6.5, 3.0],
    ]
)

data
```

```python
data.dropna()
```

Passing `how='all'` will only drop rows that are all NA:

```python
data.dropna(how="all")
```

To drop columns in the same way, pass `axis=1` or `axis='columns'`:

```python
# first let's add a column of all NaNs

data[4] = np.nan

data
```

```python
data.dropna(axis="columns", how="all")
```

Suppose you want to keep only rows containing at most a certain number of missing observations. You can indicate this with the `thresh` argument:

```python
df = pd.DataFrame(np.random.standard_normal((7, 3)))
# set some missing values
df.iloc[:4, 1] = np.nan
df.iloc[:2, 2] = np.nan

df
```

```python
df.dropna()
```

Rather than filtering out missing data, you may want to fill in the "holes" in any number of ways. Calling `fillna` with a constant replaces missing values with that value:

```python
data = pd.Series([1.0, np.nan, 3.5, np.nan, 7])

data
```

```python
data.fillna(data.mean())
```

```python
df = pd.DataFrame(np.random.standard_normal((6, 3)))

df.iloc[[2, 4, 5], 1] = np.nan
df.iloc[4:, 2] = np.nan
df.iloc[3:5, 0] = np.nan

df
```

## Handling Duplicates

Duplicate rows may be found in a DataFrame for any number of reasons. Here is an example:

```python
data = pd.DataFrame({"k1": ["one", "two"] * 3 + ["two"], "k2": [1, 1, 2, 3, 3, 4, 4]})

data
```

The dataframe method `duplicated` returns a boolean Series indicating whether each row is a duplicate or not:

```python
data.duplicated()
```

`drop_duplicates` returns a DataFrame where the duplicated array is `False`:

```python
data.drop_duplicates()
```

You can specify any subset of columns to detect duplicates. Suppose we had an additional column of values and wanted to filter duplicates only based on the 'k1' column:

```python
data["v1"] = range(7)

data
```

```python
data.drop_duplicates(subset=["k1"])
```

`duplicate` and `drop_duplicates` by default keep the first observed value combination. Passing `keep='last'` will return the last one:

```python
data.drop_duplicates(subset=["k1", "k2"], keep="last")
```
