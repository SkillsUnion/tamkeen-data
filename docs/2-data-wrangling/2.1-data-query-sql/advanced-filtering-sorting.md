# Advanced Filtering and Sorting

Learn how to filter and sort data effectively using SQL's powerful querying capabilities.

## Complex WHERE Conditions

### Using AND, OR, NOT

Combine multiple conditions using logical operators:

```sql
-- AND: Both conditions must be true
SELECT FirstName, LastName, Country
FROM customers
WHERE Country = 'USA' AND State = 'CA';

-- OR: Either condition can be true
SELECT FirstName, LastName, Country
FROM customers
WHERE Country = 'USA' OR Country = 'Canada';

-- NOT: Negates a condition
SELECT FirstName, LastName, Country
FROM customers
WHERE NOT Country = 'USA';
```

### Truth Table for Logical Operators

| A   | B   | A AND B | A OR B | NOT A |
| --- | --- | ------- | ------ | ----- |
| T   | T   | T       | T      | F     |
| T   | F   | F       | T      | F     |
| F   | T   | F       | T      | T     |
| F   | F   | F       | F      | T     |

### Using IN and BETWEEN

```sql
-- IN: Match against a list of values
SELECT TrackId, Name, UnitPrice
FROM tracks
WHERE GenreId IN (1, 2, 3);

-- BETWEEN: Match values in a range (inclusive)
SELECT TrackId, Name, Milliseconds
FROM tracks
WHERE Milliseconds BETWEEN 200000 AND 300000;
```

## Pattern Matching with LIKE

Use wildcards to match patterns:

- `%` matches any sequence of characters
- `_` matches any single character

```sql
-- Find tracks starting with 'Love'
SELECT Name
FROM tracks
WHERE Name LIKE 'Love%';

-- Find tracks with 'love' anywhere in the name
SELECT Name
FROM tracks
WHERE Name LIKE '%love%';

-- Find tracks with exactly 5 characters
SELECT Name
FROM tracks
WHERE Name LIKE '_____';
```

## Working with NULL Values

NULL represents missing or unknown data. Use IS NULL or IS NOT NULL to check for NULL values:

```sql
-- Find the highest-ranking employees
SELECT FirstName, LastName, Title
FROM employees
WHERE ReportsTo IS NULL;

-- Find customers with a company name
SELECT FirstName, LastName, Company
FROM customers
WHERE Company IS NOT NULL;
```

## Sorting Results with ORDER BY

### Single Column Sorting

```sql
-- Sort by last name ascending (default)
SELECT FirstName, LastName
FROM customers
ORDER BY LastName;

-- Sort by total descending
SELECT InvoiceId, Total
FROM invoices
ORDER BY Total DESC;
```

### Multiple Column Sorting

Sort by multiple columns with different directions:

```sql
-- Sort by country, then by last name
SELECT FirstName, LastName, Country
FROM customers
ORDER BY Country ASC, LastName DESC;
```

## Key Points to Remember

- Use parentheses to group complex conditions with AND, OR, NOT
- BETWEEN is inclusive of the boundary values
- LIKE is case-sensitive in most databases
- NULL requires special operators (IS NULL, IS NOT NULL)
- ORDER BY can sort multiple columns in different directions
