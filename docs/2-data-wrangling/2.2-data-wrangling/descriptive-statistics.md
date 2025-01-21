# Descriptive Statistics

`Series` and `DataFrame` have a number of common mathematical and statistical methods. Most of these fall into the category of _reductions_ or _summary statistics_, methods that extract a single value (like the sum or mean) from a Series or a Series of values from the rows or columns of a DataFrame. They have built-in handling for missing data.

```python
df = pd.DataFrame(
    [[1.4, np.nan], [7.1, -4.5], [np.nan, np.nan], [0.75, -1.3]],
    index=["a", "b", "c", "d"],
    columns=["one", "two"],
)
```

```python
df
```

As always, the default `axis` is `axis=0` for `DataFrame` methods, which means that by default, the reduction takes place over rows.

The method returns a Series containing column sums.

```python
df.sum()
```

```python
# passing axis='columns' or axis=1 sums over the columns instead
df.sum(axis="columns")
```

When an entire row or column contains only missing values, the sum is 0, whereas if any value is NA, it will be skipped. This can be disabled with the `skipna` option.

```python
df.sum(skipna=False)
```

```python
df.sum(axis=1, skipna=False)
```

Some methods like `idxmin` and `idxmax`, return indirect statistics like the index value where the minimum or maximum values are attained:

```python
df.idxmax()
```

```python
df.idxmin()
```

Other methods are accumulations (as opposed to reductions):

```python
df.cumsum()
```

Refer to the [API reference](https://pandas.pydata.org/pandas-docs/stable/reference/frame.html#computations-descriptive-stats) for a list of methods.

`describe` produces multiple summary statistics in one shot:

```python
df.describe()
```

It produces different summary statistics for non-numerical data.

```python
obj = pd.Series(["c", "a", "d", "a", "b", "b", "c", "c"])

obj.describe()
```

`unique` returns a Numpy array of unique values in a `Series`.

```python
obj.unique()
```

You can also use `value_counts` to return a `Series` containing unique values as its index and frequencies as its values, ordered count in descending order.

```python
obj.value_counts()
```

You can also sort the result by index.

```python
obj.value_counts().sort_index()
```
