# Reshaping and Pivoting Data

Reshaping or pivoting dataframes refers to the process of changing the layout of a dataframe. This is useful when you want to change the granularity of your data or when you want to convert a _wide_ dataframe into a _long_ dataframe or vice versa.

## Reshaping

```python
data = pd.DataFrame(
    np.arange(6).reshape((2, 3)),
    index=pd.Index(["Ohio", "Colorado"], name="state"),
    columns=pd.Index(["one", "two", "three"], name="number"),
)

data
```

The `stack` method pivots the columns into rows, producing a Series with a MultiIndex.

```python
result = data.stack()

result
```

From a hierarchically indexed Series, you can rearrange the data back into a DataFrame with `unstack` , which pivots rows into columns.

By default, the innermost level is unstacked (same with stack).

```python
result.unstack()
```

You can unstack a different level by passing a level number or name:

```python
result.unstack(level=0)

# or just stating the name of the level
result.unstack(level="state")
```

When you unstack in a DataFrame, the level unstacked becomes the lowest level:

```python
df = pd.DataFrame(
    {"left": result, "right": result + 5},
    columns=pd.Index(["left", "right"], name="side"),
)

df
```

## Pivoting between "Wide" and "Long" Format

Long format and wide format are two common ways of organizing data in the context of databases, spreadsheets, or data analysis. They refer to the arrangement of data rows and columns.

### Long format

Each row typically represents a single observation or entry, and each column contains variables or attributes related to that observation. This format is also known as "tidy data" or "normalized data."

Example:

| Year | Country | Population |
| ---- | ------- | ---------- |
| 2019 | SG      | 5.7        |
| 2019 | MY      | 31.5       |
| 2019 | TH      | 69.8       |
| 2020 | SG      | 5.7        |
| 2020 | MY      | 32.7       |
| 2020 | TH      | 69.8       |

Advantages:

- It is easier to handle and analyze structured data with different attributes.
- Efficient storage for sparse data, as it avoids repeating column headers.

### Wide format

Each row contains multiple observations or entries, and each column contains variables or attributes related to that observation.

Example:

| Year | SG  | MY   | TH   |
| ---- | --- | ---- | ---- |
| 2019 | 5.7 | 31.5 | 69.8 |
| 2020 | 5.7 | 32.7 | 69.8 |

Advantages:

- Easier to read and understand when the number of variables is limited.
- Suitable for simple summary statistics and basic analyses.

```python
price_reindex
```

We can "pivot" a table from a "wide" format to a "long" format using the `melt` function.

The `date` column is the group indicator, while the other columns are data values. We need to indicate the group indicator(s):

```python
melted = pd.melt(price_reindex, id_vars="Date")

melted
```

Using `pivot`, we can reshape back to the original layout:

```python
reshaped = melted.pivot(index="Date", columns="variable", values="value")

reshaped
```
