# Data Aggregation

Data aggregation is the process of grouping data together and performing calculations on them. It is equivalent to the `GROUP BY` clause in SQL.

```python
df = pd.DataFrame(
    {
        "key1": ["a", "a", None, "b", "b", "a", None],
        "key2": pd.Series([1, 2, 1, 2, 1, None, 1], dtype="Int64"),
        "data1": np.random.standard_normal(7),
        "data2": np.random.standard_normal(7),
    }
)

df
```

If you want to compute the mean for each unique value in `key1`:

```python
df.groupby("key1").mean()
```

It does not make sense to compute the mean for `key2` since it is a categorical variable and also serves as a key.

We can select the numeric columns to compute the mean for (after the `groupby` method):

```python
df.groupby("key1")[["data1", "data2"]].mean()
```

Note that the following also works, since the returned result is a DataFrame, however it is less efficient as the selection/subset happens after the computation.

```python
df.groupby("key1").mean()[["data1", "data2"]]
```

You can group by more than 1 column. There is a useful GroupBy method `size` which returns a Series containing group sizes.

```python
df.groupby(["key1", "key2"]).size()
```

You can also group by other `Series`/`array`/`list` with the same length:

```python
states = np.array(["OH", "CA", "CA", "OH", "OH", "CA", "OH"])
years = [2005, 2005, 2006, 2005, 2006, 2005, 2006]

df["data1"].groupby([states, years]).mean()
```

For built-in aggregation methods in pandas, refer to the [documentation](https://pandas.pydata.org/docs/user_guide/groupby.html#built-in-aggregation-methods).

Apply the function on `day` and `time` group.

Create a function that selects the bottom five `tip_pct` values.

Then apply it on `smoker` group.

## Transform

You can also transform your data using the `transform` method. It is similar to `apply` but imposes more restrictions on the type of function you can use. The function must:

- Produce a scalar value to be broadcast to the shape of the group chunk, or
- Return an object that is the same shape as the group chunk
- Not mutate its input

```python
df = pd.DataFrame({"key": ["a", "b", "c"] * 4, "value": np.arange(12.0)})
df

g = df.groupby("key")["value"]
g.mean()
```

`transform` produce a Series of the same shape as `df['value']` but with values replaced by the average grouped by `key`.

We can pass a function or function name (for built-in aggregation) to `transform`:

```python
g.transform(lambda g: g.mean())
g.transform("mean")

def times_two(group):
    return group * 2

g.transform(times_two)
```

A common transformation in data analytics / science is _standardization_ or _standard scaling_. This is where we transform the data to have a mean of 0 and a standard deviation of 1. It is also known as _z-score normalization_.

The formula for standard scaling is:

$$
z = \frac{x - \mu}{\sigma}
$$

where $x$ is the value, $\mu$ is the mean, and $\sigma$ is the standard deviation.

We can achieve this using `transform`:

```python
def normalize(x):
    return (x - x.mean()) / x.std()

g.transform(normalize)
```

or the following works too:

```python
standardized = (df["value"] - g.transform("mean")) / g.transform("std")
standardized
```

## Pivot Tables and Cross-Tabulation

Pivot table is a data summarization tool that is used in the context of data processing. Pivot tables are used to summarize, sort, reorganize, group, count, total or average data. It allows its users to transform columns into rows and rows into columns. It allows grouping by any data field.

In pandas, you can use the `pivot_table` method which is made possible through the `groupby` and `reshape` operations utilizing hierarchical indexing. In addition, `pivot_table` can add partial totals, also known as _margins_.

The default aggregation for `pivot_table` is mean.

```python
tips.pivot_table(
    index=[["day", "smoker"], values=["size", "tip", "tip_pct", "total_bill"]]
)
```

You can put `smoker` in the table columns and `time` and `day` in the rows:

```python
tips.pivot_table(index=[["time", "day"], columns="smoker", values=["tip_pct", "size"]])
```

Add partial totals by passing `margins=True`:

```python
tips.pivot_table(
    index=[["time", "day"], columns="smoker", values=["tip_pct", "size"], margins=True]
)
```

To use other aggregation functions, pass it to the `aggfunc` keyword:

```python
tips.pivot_table(
    index=[["time", "smoker"], columns="day", values="tip_pct", aggfunc=len, margins=True]
)
```

Use `fill_value` to fill missing values:

```python
tips.pivot_table(
    index=[["time", "smoker"],
    columns="day",
    values="tip_pct",
    aggfunc=len,
    margins=True,
    fill_value=0,
)
```

A _cross-tabulation_ or _crosstab_ is a special case of pivot table that computes group frequencies (counts):

```python
pd.crosstab(index=[tips["time"], tips["day"]], columns=tips["smoker"], margins=True)
```
