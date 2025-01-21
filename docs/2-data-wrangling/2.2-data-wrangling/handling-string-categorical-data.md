# Handling String and Categorical Data

## String Manipulation

Cleaning up a messy dataset for analysis often requires a lot of string manipulation. To complicate matters, a column containing strings will sometimes have missing data:

```python
data = {
    "Dave": "dave@google.com",
    "Steve": "steve@gmail.com",
    "Rob": "rob@gmail.com",
    "Wes": np.nan,
}

data = pd.Series(data)

data
```

String and regular expression methods can be applied (passing a lambda or other function) to each value using `map`, but it will fail on the NA values. To cope with this, Series has array-oriented methods for string operations that skip NA values. These are accessed through Series's `str` attribute.

```python
data.str.contains("gmail")
```

Note that the result of this operation has an `object` dtype.

Pandas has `extension types` that provide for specialized treatment of strings, integers and boolean data. We discussed `StringDType` in the previous lesson. Refer to the [documentation](https://pandas.pydata.org/pandas-docs/stable/user_guide/basics.html#basics-dtypes) for more information.

```python
data_as_string = data.astype("string")

data_as_string
```

```python
data_as_string.str.contains("gmail")
```

You can also slice strings using this syntax:

```python
data_as_string.str[:5]
```

Regular expressions can be used, too, along with any `re` options like `IGNORECASE`:

```python
import re
```

```python
# regex email pattern
pattern = r"([A-Z0-9._%+-]+)@([A-Z0-9.-]+)\.([A-Z]{2,4})"

data.str.findall(pattern, flags=re.IGNORECASE)
```

There are a couple of ways to do vectorized element retrieval. Either use `str.get` or index into the `str` attribute:

```python
matches = data.str.findall(pattern, flags=re.IGNORECASE).str[0]

matches
```

```python
matches.str.get(1)
```

The `extract` method will return the captured groups of a regular expression as a DataFrame:

```python
data.str.extract(pattern, flags=re.IGNORECASE)
```

Refer to the [API reference](https://pandas.pydata.org/pandas-docs/stable/reference/series.html#string-handling) for a list of string methods.

## Categorical Data

Pandas has a `categorical` type for string values with a smaller set of distinct values that are repeated. Consider the following example:

```python
values = pd.Series(["apple", "orange", "apple", "apple"] * 2)

values
```

```python
values.unique()
```

```python
values.value_counts()
```

Many data systems (for data warehousing, statistical computing, or other uses) have developed specialized approaches for representing data with repeated values for more efficient storage and computation. In data warehousing, a best practice is to use so-called dimension tables containing the distinct values and storing the primary observations as `integer keys` referencing the dimension table:

```python
values = pd.Series([0, 1, 0, 0] * 2)

dim = pd.Series(["apple", "orange"])

values
```

```python
dim
```

We can use `take` to restore the original Series of strings:

```python
dim.take(values)
```

This representation as integers is called the _category codes_ or simply _codes_. The array of distinct values can be called the _categories_.

The categorical representation can yield significant performance improvements when you are doing analytics. You can also perform transformations on the categories while leaving the codes unmodified.

Pandas' `categorical` type holds data that uses the integer-based categorical representation or encoding.

```python
df = pd.DataFrame(
    {
        "basket_id": np.arange(N),
        "fruit": fruits,
        "count": rng.integers(3, 15, size=N),
        "weight": rng.uniform(0, 4, size=N),
    },
    columns=["basket_id", "fruit", "count", "weight"],
)

df
```

```python
cat_s = s.astype("category")

cat_s
```

```python
cat_s.cat.codes
```

```python
cat_s.cat.categories
```

Suppose that we know the actual set of categories for this data extends beyond the four values observed in the data. We can use the `set_categories` method to change them:

```python
actual_categories = ["a", "b", "c", "d", "e"]
cat_s2 = cat_s.cat.set_categories(actual_categories)

cat_s2
```

While it appears that the data is unchanged, the new categories will be reflected in operations that use them. For example, `value_counts` respects the categories, if present:

```python
cat_s.value_counts()
```

```python
cat_s2.value_counts()
```

Conversely, you can remove categories not in your data by calling `remove_unused_categories`:

```python
cat_s2.cat.remove_unused_categories()
```

### Computing Indicator / Dummy Variables

Another type of transformation for statistical modeling or machine learning applications is converting a categorical variable into a _dummy_ or _indicator_ matrix. If a column in a DataFrame has `k` distinct values, you would derive a matrix or DataFrame with k columns containing all 1s and 0s.

```python
df = pd.DataFrame({"key": ["b", "b", "a", "c", "a", "b"], "data1": range(6)})

df
```

```python
pd.get_dummies(df["key"])
```

You can also add a prefix to the columns:

```python
dummies = pd.get_dummies(df["key"], prefix="key")

dummies
```

A useful recipe for statistical applications is to combine `get_dummies` with a discretization function like `cut`:

```python
np.random.seed(12345)

values = np.random.uniform(size=10)

values
```

```python
bins = [0, 0.2, 0.4, 0.6, 0.8, 1]

pd.get_dummies(pd.cut(values, bins))
```
