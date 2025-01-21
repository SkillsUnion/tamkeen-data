# Data Transformation

A common step in data cleaning is transforming data based on the values in an array, Series, or column in a DataFrame.

```python
data = pd.DataFrame(
    {
        "food": [
            "bacon",
            "pulled pork",
            "bacon",
            "pastrami",
            "corned beef",
            "bacon",
            "pastrami",
            "honey ham",
            "nova lox",
        ],
        "ounces": [4, 3, 12, 6, 7.5, 8, 3, 5, 6],
    }
)

data
```

Suppose you want to add a column indicating the animal type that each food came from. Let's map the food type to the animal type by creating a dictionary:

```python
meat_to_animal = {
    "bacon": "pig",
    "pulled pork": "pig",
    "pastrami": "cow",
    "corned beef": "cow",
    "honey ham": "pig",
    "nova lox": "salmon",
}
```

The `map` method on a Series accepts a function or dict-like object containing a mapping.

```python
data["animal"] = data["food"].map(meat_to_animal)

data
```

Passing a function works too:

```python
def get_animal(x):
    return meat_to_animal[x]


data["food"].map(get_animal)
```

`replace` provides a more generic way to do element-wise replacement:

```python
data = pd.Series([1, -999, 2, -999, -1000, 3])

data
```

The `-999` values might be sentinel values for missing data. To replace these with NA values:

```python
data.replace(-999, np.nan)
```

You can also replace multiple values at once by passing a list:

```python
data.replace([-999, -1000], np.nan)
```

To use a different replacement for each value, pass a list of substitutes or a dict:

```python
data.replace([-999, -1000], [np.nan, 0])
```

```python
data.replace({-999: np.nan, -1000: 0})
```

```python
df = pd.DataFrame(np.random.standard_normal((6, 3)))

df.iloc[2:, 1] = -999
df.iloc[4:, 2] = 999

df
```

Replace -999 with NaN and 999 with 0.

## Renaming Axis Indexes

Axis labels (index or columns) can be similarly transformed by a function or mapping of some form to produce new, differently labeled objects.

```python
data = pd.DataFrame(
    np.arange(12).reshape((3, 4)),
    index=["Ohio", "Colorado", "New York"],
    columns=["one", "two", "three", "four"],
)
```

```python
data
```

You can use the `map` method like a Series.

```python
def transform(x):
    return x[:4].upper()


data.index.map(transform)
```

Assigning back to the index will modify the DataFrame in-place:

```python
data.index = data.index.map(transform)
```

```python
data
```

You can also use the `rename` method which accepts a function or dict-like object providing a mapping.

```python
data.rename(index=str.title, columns=str.upper)
```

```python
data.rename(index={"OHIO": "INDIANA"}, columns={"three": 3})
```
