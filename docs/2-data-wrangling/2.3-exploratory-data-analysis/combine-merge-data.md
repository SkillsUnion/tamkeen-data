# Combining and Merging Datasets

Data can be combined or merged in a number of ways:

- `merge`: connects rows in DataFrames based on one or more keys. Equivalent to database `join` operations.
- `concat`: concatenates or "stacks" together objects along an axis. Equivalent to database `union` operations.
- `combine_first`: instance method enables splicing together overlapping data to fill in missing values in one object with values from another (we already covered this previously).

## `merge`

```python
df1 = pd.DataFrame(
    {
        "key": ["b", "b", "a", "c", "a", "a", "b"],
        "data1": pd.Series(range(7), dtype="Int64"),
    }
)

df2 = pd.DataFrame(
    {"key": ["a", "b", "d"], "data2": pd.Series(range(3), dtype="Int64")}
)
```

```python
df1
```

```python
df2
```

Merging the two dataframes above constitutes a _many-to-one_ join; the data in `df1` has multiple rows labeled `a` and `b`, whereas `df2` has only one row for each value in the key column `key`.

```python
pd.merge(df1, df2)
```

If you did not specify which column(s) to join on, `merge` uses the overlapping column names as the keys. It's a good practice to specify explicitly, though.

```python
pd.merge(df1, df2, on="key")
```

If the column names are different in each object, you can specify them separately:

```python
df3 = pd.DataFrame(
    {
        "lkey": ["b", "b", "a", "c", "a", "a", "b"],
        "data1": pd.Series(range(7), dtype="Int64"),
    }
)

df4 = pd.DataFrame(
    {"rkey": ["a", "b", "d"], "data2": pd.Series(range(3), dtype="Int64")}
)

pd.merge(df3, df4, left_on="lkey", right_on="rkey")
```

The default merge type is `inner` join. You can specify the other options- `left, right, outer` via the `how` parameter.

```python
pd.merge(df1, df2, how="outer")
```

```python
pd.merge(df3, df4, left_on="lkey", right_on="rkey", how="outer")
```

Let's consider a _many-to-many_ join:

```python
df1 = pd.DataFrame(
    {"key": ["b", "b", "a", "c", "a", "b"], "data1": pd.Series(range(6), dtype="Int64")}
)

df2 = pd.DataFrame(
    {"key": ["a", "b", "a", "b", "d"], "data2": pd.Series(range(5), dtype="Int64")}
)
```

```python
df1
```

```python
df2
```

Since there were `three "b"` rows in the left DataFrame and `two` in the right one, there are `six "b"` rows in the result:

```python
pd.merge(df1, df2, how="inner")
```

To merge with multiple keys, pass a list of column names:

```python
left = pd.DataFrame(
    {
        "key1": ["foo", "foo", "bar"],
        "key2": ["one", "two", "one"],
        "lval": pd.Series([1, 2, 3], dtype="Int64"),
    }
)

right = pd.DataFrame(
    {
        "key1": ["foo", "foo", "bar", "bar"],
        "key2": ["one", "one", "one", "two"],
        "rval": pd.Series([4, 5, 6, 7], dtype="Int64"),
    }
)

pd.merge(left, right, on=["key1", "key2"], how="outer")
```

If there are overlapping non-key column names:

```python
pd.merge(left, right, on="key1")
```

You can pass `suffixes` to specify the strings to append to the overlaping names:

```python
pd.merge(left, right, on="key1", suffixes=("_left", "_right"))
```

If the merge key(s) is in the index, you can pass `left_index=True` or `right_index=True` to indicate that the index should be used as the merge key.

```python
left1 = pd.DataFrame(
    {"key": ["a", "b", "a", "a", "b", "c"], "value": pd.Series(range(6), dtype="Int64")}
)

right1 = pd.DataFrame({"group_val": [3.5, 7]}, index=["a", "b"])
```

```python
left1
```

```python
right1
```

```python
pd.merge(left1, right1, left_on="key", right_index=True)
```

DataFrame has a `join` method which performs a left join by default. The join key on the right dataframe has to be the index. The join key on the left dataframe can be an index or a column (by specifying the `on` parameter):

```python
left1.join(right1, on="key")
```

## `concat`

You can join DataFrames along any axis which is referred to as _concatenation_ or _stacking_. This is akin to database `union` operations, in any "direction" (axis).

```python
s1 = pd.Series([0, 1], index=["a", "b"], dtype="Int64")
s2 = pd.Series([2, 3, 4], index=["c", "d", "e"], dtype="Int64")
s3 = pd.Series([5, 6], index=["f", "g"], dtype="Int64")
```

Calling `concat` with these objects in a list glues together the values and indexes:

```python
s1
```

```python
s2
```

```python
s3
```

By default, `concat` works along `axis="index"`, producing another Series. If you pass `axis="columns"`, the result will instead be a DataFrame:

```python
pd.concat([s1, s2, s3])
```

```python
pd.concat([s1, s2, s3], axis="columns")
```

The default behavior of `concat` is union (`outer` join) of the indexes, you can also intersect them by passing `join='inner'`:

```python
s4 = pd.concat([s1, s3])

s4
```

```python
pd.concat([s1, s4], axis="columns")
```

```python
pd.concat([s1, s4], axis="columns", join="inner")
```

When combining Series along axis="columns", pass the `keys` argument for the DataFrame column headers:

```python
pd.concat([s1, s2, s3], axis="columns", keys=["one", "two", "three"])
```

For DataFrames, it will become a hierarchical index instead:

```python
df1 = pd.DataFrame(
    np.arange(6).reshape(3, 2), index=["a", "b", "c"], columns=["one", "two"]
)

df2 = pd.DataFrame(
    5 + np.arange(4).reshape(2, 2), index=["a", "c"], columns=["three", "four"]
)
```

```python
df1
```

```python
df2
```

```python
pd.concat([df1, df2], axis="columns", keys=["level1", "level2"])
```

If the index does not contain any relevant data, and you want to avoid concatenating based on indexes, you can pass the `ignore_index=True` argument, this will assign a new default index:

```python
df1 = pd.DataFrame(np.random.standard_normal((3, 4)), columns=["a", "b", "c", "d"])

df2 = pd.DataFrame(np.random.standard_normal((2, 3)), columns=["b", "d", "a"])

pd.concat([df1, df2], ignore_index=True)
```
