# Introduction to SQL and Basic Queries

SQL (Structured Query Language) is the standard language for managing and manipulating relational databases. It allows users to interact with databases by performing operations such as data retrieval, insertion, updating, and deletion. This lesson covers the basic SQL syntax and conventions, including SELECT statements, filtering data with WHERE, and using operators for comparisons.

## Basic SQL Syntax and Conventions

- SQL keywords are written in UPPERCASE (convention)
  - However, SQL is case-insensitive for most databases, so lowercase is also valid
- Statements end with semicolon (;)
- Spaces and line breaks don't affect query execution, but improve readability
- String values use single quotes ('text')
- Comments use double dash (--)

```sql
-- This is a comment
SELECT FirstName    -- Column name
FROM employees     -- Table name
WHERE Title = 'IT Staff';  -- Condition
```

## Using SELECT to Retrieve Columns

The SELECT statement retrieves data from tables:

```sql
-- Select specific columns
SELECT FirstName, LastName, Country
FROM customers;

-- Select all columns using asterisk
SELECT *
FROM tracks;

-- Select with calculated values
SELECT
    Name,
    Milliseconds/1000 AS seconds,
    UnitPrice
FROM tracks;
```

## Understanding FROM Clause

FROM specifies which table(s) to query:

```sql
-- Single table
SELECT Name, Composer
FROM tracks;

-- Multiple tables with relationship
SELECT tracks.Name, albums.Title
FROM tracks, albums
WHERE tracks.AlbumId = albums.AlbumId;
```

## Column Aliasing with AS

Use AS to rename columns in results:

```sql
SELECT
    FirstName AS given_name,
    LastName AS family_name,
    Email AS contact
FROM employees;

-- Complex aliasing
SELECT
    t.Name AS track_name,
    a.Title AS album_title,
    (t.UnitPrice * 100) AS price_cents
FROM tracks t, albums a
WHERE t.AlbumId = a.AlbumId;
```

## Selecting Distinct Values

DISTINCT removes duplicates from results:

```sql
-- Find unique countries
SELECT DISTINCT Country
FROM customers;

-- Multiple column distinct
SELECT DISTINCT City, Country
FROM customers;

-- Distinct composers
SELECT DISTINCT Composer
FROM tracks
WHERE Composer IS NOT NULL;
```

## Using WHERE for Filtering

Filter results using WHERE clause:

```sql
-- Single condition
SELECT Name, Composer
FROM tracks
WHERE GenreId = 1;

-- Multiple conditions using AND
SELECT Name, UnitPrice
FROM tracks
WHERE GenreId = 1
AND UnitPrice > 0.99;

-- Multiple conditions using OR
SELECT FirstName, LastName, Country
FROM customers
WHERE Country = 'Brazil'
OR Country = 'Argentina';
```

We'll learn more about logical operators to combine conditions in the next lesson.

### Basic Operators

Examples using comparison operators:

```sql
-- Equal to (=)
SELECT Name, UnitPrice
FROM tracks
WHERE UnitPrice = 0.99;

-- Greater than (>)
SELECT Name, Milliseconds
FROM tracks
WHERE Milliseconds > 300000;

-- Greater than or equal to (>=)
SELECT Name, Bytes
FROM tracks
WHERE Bytes >= 5000000;

-- Less than or equal to (<=)
SELECT Name, Bytes
FROM tracks
WHERE Bytes <= 5000000;

-- Less than (<)
SELECT Name, Bytes
FROM tracks
WHERE Bytes < 5000000;

-- Not equal (<>)
SELECT FirstName, Country
FROM customers
WHERE Country <> 'USA';
```
