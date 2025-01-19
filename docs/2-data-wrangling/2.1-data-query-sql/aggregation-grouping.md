# Data Aggregation and Grouping

Data aggregation is the process of summarizing multiple rows of data into a single result. This is useful when you need to analyze data at a higher level, such as calculating totals, averages, or counting records that meet specific criteria.

## Aggregate Functions

SQL provides several built-in aggregate functions that perform calculations on a set of rows:

- **COUNT():** Counts the number of rows or non-null values
- **SUM():** Calculates the total of numeric values
- **AVG():** Calculates the average (mean) of numeric values
- **MIN():** Finds the minimum value
- **MAX():** Finds the maximum value

### Basic Examples

```sql
-- Count total number of tracks
SELECT COUNT(*) as total_tracks
FROM tracks;

-- Calculate average invoice total
SELECT AVG(total) as avg_invoice_total
FROM invoices;

-- Find the longest and shortest tracks
SELECT
    MIN(milliseconds/1000) as shortest_seconds,
    MAX(milliseconds/1000) as longest_seconds
FROM tracks;
```

## GROUP BY Clause

The GROUP BY clause allows you to group rows that have the same values into summary rows. It's commonly used with aggregate functions to perform calculations on each group.

### Syntax and Examples

```sql
-- Count tracks per genre
SELECT
    genres.Name as genre,
    COUNT(*) as track_count
FROM tracks
JOIN genres ON tracks.GenreId = genres.GenreId
GROUP BY genres.Name
ORDER BY track_count DESC;

-- Calculate total sales by country
SELECT
    BillingCountry,
    COUNT(*) as total_orders,
    ROUND(SUM(total), 2) as total_revenue
FROM invoices
GROUP BY BillingCountry
ORDER BY total_revenue DESC;
```

## HAVING Clause

While WHERE filters individual rows before grouping, HAVING filters groups after aggregation. This allows you to set conditions on the aggregate results.

### Examples with HAVING

```sql
-- Find genres with more than 100 tracks
SELECT
    genres.Name as genre,
    COUNT(*) as track_count
FROM tracks
JOIN genres ON tracks.GenreId = genres.GenreId
GROUP BY genres.Name
HAVING track_count > 100
ORDER BY track_count DESC;

-- Find customers who spent over $45
SELECT
    customers.FirstName,
    customers.LastName,
    ROUND(SUM(invoices.total), 2) as total_spent
FROM customers
JOIN invoices ON customers.CustomerId = invoices.CustomerId
GROUP BY customers.CustomerId
HAVING total_spent > 45
ORDER BY total_spent DESC;
```

## Combining WHERE and HAVING

You can use both WHERE and HAVING in the same query. WHERE filters individual rows before grouping, while HAVING filters the groups after aggregation.

```sql
-- Find high-value customers from USA in 2009
SELECT
    customers.FirstName,
    customers.LastName,
    ROUND(SUM(invoices.total), 2) as total_spent
FROM customers
JOIN invoices ON customers.CustomerId = invoices.CustomerId
WHERE
    customers.country = 'USA'
    AND strftime('%Y', invoices.InvoiceDate) = '2009'
GROUP BY customers.CustomerId
HAVING total_spent > 20
ORDER BY total_spent DESC;
```

## Best Practices

1. Always include columns used in GROUP BY in your SELECT statement
2. Use HAVING for conditions on aggregate functions, not WHERE
3. Apply WHERE filters before grouping to improve performance
4. Be careful with NULL values in grouped columns
5. Use meaningful aliases for aggregated columns
