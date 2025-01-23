# Covariance and Correlation

Covariance and correlation are two mathematical concepts which are commonly used in statistics. They are used to determine the relationship between two variables. The covariance is used to measure the linear relationship between two variables. On the other hand, the correlation is used to measure both the strength and direction of the _linear relationship_ between two variables.

Covariance is a measure of how much two random variables vary together. It's similar to variance, but where variance tells you how a single variable varies, covariance tells you how two variables vary together.

Correlation (coefficient) is a _normalized_ measure of covariance that is easier to understand, as it provides quantitative measurements of the statistical dependence between two random variables. The correlation coefficient is a value that indicates the strength of the relationship between variables. The coefficient can take any values from -1 to 1. The interpretations of the values are:

- **-1**: Perfect negative linear correlation
- **-0.8**: Strong negative linear correlation
- **-0.5**: Moderate negative linear correlation
- **-0.2**: Weak negative linear correlation
- **0**: No linear correlation
- **0.2**: Weak positive linear correlation
- **0.5**: Moderate positive linear correlation
- **0.8**: Strong positive linear correlation
- **1**: Perfect positive linear correlation

## Working with Sample Data

Here, we'll use the tips dataset from seaborn which contains information about restaurant bills and tips.

```python
import seaborn as sns
tips = sns.load_dataset('tips')
numeric_data = tips.select_dtypes(include=['float64', 'int64'])
numeric_data
```

## Computing Correlation and Covariance

Compute the correlation and covariance between the total bill and tip:

```python
numeric_data['total_bill'].cov(numeric_data['tip'])
numeric_data['total_bill'].corr(numeric_data['tip'])
```

You can also get the full (pair-wise) correlation or covariance matrix as a DataFrame:

```python
numeric_data.cov()
numeric_data.corr()
```

You can also compute pair-wise correlations between a DataFrame's columns or rows with another Series or DataFrame. Passing a Series returns a Series with the correlation value computed for each column:

```python
numeric_data.corrwith(numeric_data['tip'])
```

Looking at correlations with tips can help us understand what factors might influence tipping behavior in restaurants.
