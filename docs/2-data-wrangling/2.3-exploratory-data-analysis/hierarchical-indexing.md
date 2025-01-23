# Hierarchical Indexing

Hierarchical indexing (MultiIndex) allows you to have multiple (two or more) _index levels_ on an axis. It enables "higher dimensional" data in a lower dimensional data structure.

You create a hierarchical index by simply passing a list of arrays to the index argument of a pandas DataFrame or Series.

```python
data = pd.Series(
    np.random.uniform(size=9),
    index=[["a", "a", "a", "b", "b", "c", "c", "d", "d"], [1, 2, 3, 1, 3, 1, 2, 2, 3]],
)

data

data.index
```

You can use _partial indexing_ to select subsets of data:

```python
data["b"]

data["b":"c"]

data.loc[["b", "d"]]
```

You can also select from "inner" level:

```python
data.loc[:, 2]
```

Hierarchical indexing works on both axes.

```python
frame = pd.DataFrame(
    np.arange(12).reshape((4, 3)),
    index=[["a", "a", "b", "b"], [1, 2, 1, 2]],
    columns=[["Ohio", "Ohio", "Colorado"], ["Green", "Red", "Green"]],
)

frame
```

Setting names on the axes work as usual:

```python
frame.index.names = ["key1", "key2"]
frame.columns.names = ["state", "color"]

frame
```

```python
frame.index.nlevels
```

Partial indexing works on columns too:

```python
frame["Ohio"]
```

You may need to rearrange the order of the levels on an axis. The `swaplevel` method will swap the levels in the MultiIndex on a particular axis. The default is to swap the levels on the rows:

```python
frame.swaplevel()

frame.swaplevel(0, 1, axis=1)
```

You can also sort by a single level or subset of levels:

```python
frame.sort_index(level=1)
```

It's common to use one or more columns from a DataFrame as the row index.

```python
frame = pd.DataFrame(
    {
        "a": range(7),
        "b": range(7, 0, -1),
        "c": ["one", "one", "one", "two", "two", "two", "two"],
        "d": [0, 1, 2, 0, 1, 2, 3],
    }
)

frame
```

`set_index` will return a new DataFrame using one or more of its columns as the index.

```python
frame2 = frame.set_index(["c", "d"])

frame2
```

`reset_index` does the opposite of `set_index` and turns the index back into a column.

```python
frame2.reset_index()
```

You can choose to drop the columns when resetting index:

```python
frame2.reset_index(drop=True)
```
