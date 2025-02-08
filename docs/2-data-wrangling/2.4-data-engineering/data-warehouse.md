# Data Warehouse Architecture

A data warehouse is a central repository of integrated data from one or more disparate sources. They store current and historical data in one single place that are used for creating analytical reports for workers throughout the enterprise.

## Schema Design Patterns

Schema design patterns define how data is organized within a data warehouse. Common patterns include:

- **Star Schema:** A simple schema with a central fact table surrounded by dimension tables.
- **Snowflake Schema:** An extension of the star schema where dimension tables are further normalized into multiple related tables.
- **Data Vault:** A detail-oriented, historical tracking and uniquely linked set of normalized tables that support one or more functional areas of business.

### Star Schema Example

```sql
-- Example of creating a star schema using SQL

-- Dimension Table: Product
CREATE TABLE Product (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(50),
    category VARCHAR(50)
);

-- Dimension Table: Customer
CREATE TABLE Customer (
    customer_id INT PRIMARY KEY,
    customer_name VARCHAR(50),
    location VARCHAR(50)
);

-- Dimension Table: Date
CREATE TABLE Date (
    date_id INT PRIMARY KEY,
    date DATE
);

-- Fact Table: Sales
CREATE TABLE Sales (
    sale_id INT PRIMARY KEY,
    product_id INT,
    customer_id INT,
    date_id INT,
    quantity INT,
    price DECIMAL(10, 2),
    FOREIGN KEY (product_id) REFERENCES Product(product_id),
    FOREIGN KEY (customer_id) REFERENCES Customer(customer_id),
    FOREIGN KEY (date_id) REFERENCES Date(date_id)
);
```

## Dimensional Modeling

Dimensional modeling is a data warehouse design technique that optimizes databases for analytical processing. It involves identifying facts (measurements) and dimensions (context).

- **Facts:** Numerical data that can be aggregated (e.g., sales amount, quantity).
- **Dimensions:** Attributes that provide context to the facts (e.g., product, customer, date).

## Slowly Changing Dimensions (SCD)

SCDs handle changes to dimension attributes over time. Common types include:

- **Type 0: Retain Original:** Dimension attributes never change.
- **Type 1: Overwrite:** Dimension attributes are updated with new values, losing historical data.
- **Type 2: Add New Row:** A new row is added to the dimension table with the updated attributes and effective dates.
- **Type 3: Add New Column:** A new column is added to the dimension table to store the updated attribute.

### SCD Type Examples

#### Type 0: Retain Original

```sql
-- Example: A table where the original values are always retained
CREATE TABLE Product (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(50) -- Name never changes
);
```

#### Type 1: Overwrite

```sql
-- Example: Updating customer's location directly (losing history)
CREATE TABLE Customer (
    customer_id INT PRIMARY KEY,
    customer_name VARCHAR(50),
    location VARCHAR(50)
);

-- Update statement
UPDATE Customer
SET location = 'Chicago'
WHERE customer_id = 201;
```

#### Type 2: Add New Row

```sql
-- Example: Adding a new row to track changes in customer location
CREATE TABLE Customer (
    customer_id INT,
    customer_name VARCHAR(50),
    location VARCHAR(50),
    start_date DATE,
    end_date DATE,
    is_current BOOLEAN,
    PRIMARY KEY (customer_id, start_date)
);

-- Insert initial data
INSERT INTO Customer (customer_id, customer_name, location, start_date, end_date, is_current)
VALUES (201, 'Alice', 'New York', '2023-01-01', NULL, TRUE);

-- Update the existing record
UPDATE Customer
SET end_date = '2023-12-31', is_current = FALSE
WHERE customer_id = 201 AND is_current = TRUE;

-- Insert the new record
INSERT INTO Customer (customer_id, customer_name, location, start_date, end_date, is_current)
VALUES (201, 'Alice', 'Chicago', '2024-01-01', NULL, TRUE);
```

#### Type 3: Add New Column

```sql
-- Example: Adding a new column to track the previous location
CREATE TABLE Customer (
    customer_id INT PRIMARY KEY,
    customer_name VARCHAR(50),
    location VARCHAR(50),
    previous_location VARCHAR(50)
);

-- Update statement
UPDATE Customer
SET previous_location = location, location = 'Chicago'
WHERE customer_id = 201;
```

## Partitioning Strategies

Partitioning divides a large table into smaller, more manageable parts. This improves query performance and simplifies data management.

- **Horizontal Partitioning:** Dividing a table into rows based on a specific column (e.g., date).
- **Vertical Partitioning:** Dividing a table into columns, storing frequently accessed columns together.

### Horizontal Partitioning Example

```sql
-- Example: Horizontal partitioning by year

-- Create the base table
CREATE TABLE Sales (
    sale_id INT PRIMARY KEY,
    product_id INT,
    sale_date DATE,
    quantity INT,
    price DECIMAL(10, 2)
);

-- Create partition for 2023
CREATE TABLE Sales_2023 PARTITION OF Sales
FOR VALUES FROM ('2023-01-01') TO ('2023-12-31');

-- Create partition for 2024
CREATE TABLE Sales_2024 PARTITION OF Sales
FOR VALUES FROM ('2024-01-01') TO ('2024-12-31');
```
