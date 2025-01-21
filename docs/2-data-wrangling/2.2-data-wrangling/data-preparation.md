# Data Preparation

## Handling Outliers

Consider a DataFrame with some normally distributed data:

```python
data = pd.DataFrame(np.random.standard_normal((1000, 4)))

data
```

```python
data.describe()
```

Let's assume that values exceeding 3 in absolute value are outliers (the threshold value can be adjusted accordingly). To find the values in one of the columns exceeding 3 or -3:

```python
col = data[2]

col[col.abs() > 3]
```

To select all rows having a value exceeding 3 or -3, you can use the `any` method on a Boolean DataFrame:

```python
data[(data.abs() > 3).any(axis="columns")]
```

To deal with outliers, you can cap them to a maximum or minimum value. Here `np.sign` returns an array of 1 and -1 depending on the sign of the values:

```python
data[data.abs() > 3] = np.sign(data) * 3
```

```python
data.describe()
```

Another common way to deal with outliers is to drop rows that contain them.

```python
data[(data.abs() < 3).all(axis="columns")]
```

## Permutation and Random Sampling

Permuting (randomly reordering) a Series or the rows in a DataFrame is possible using the `numpy.random.permutation` function. Calling `permutation` with the length of the axis you want to permute produces an array of integers indicating the new ordering:

```python
df = pd.DataFrame(np.arange(5 * 7).reshape((5, 7)))

df
```

```python
sampler = np.random.permutation(5)

sampler
```

You can then use `iloc` indexing or `take` method to reorder your data based on the randomly generated integer array:

```python
df.iloc[sampler]
```

```python
df.take(sampler)
```

Permuting columns work too:

```python
column_sampler = np.random.permutation(df.shape[1])

column_sampler
```

```python
df.take(column_sampler, axis=1)
```

To select a random subset without replacement (the same row cannot appear twice), you can use the `sample` method on Series and DataFrame:

```python
df.sample(n=3)
```

You can also sample with replacement (allowing repeat choices), by passing `replace=True`:

```python
choices = pd.Series([5, 7, -1, 6, 4])

choices.sample(n=10, replace=True)
```
