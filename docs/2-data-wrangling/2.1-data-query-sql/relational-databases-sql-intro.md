# Introduction to Relational Databases and SQL

Relational databases are a type of database that stores and retrieves data using the relational model, based on the concept of associating tables via shared columns. This model ensures data integrity and efficiency in managing large datasets.

SQL (Structured Query Language) is the standard language for interacting with relational databases. It allows users to perform various operations such as data retrieval, manipulation, definition, and control.

[![Relational Databases and SQL](https://img.youtube.com/vi/zsjvFFKOm3c/0.jpg)](https://www.youtube.com/watch?v=zsjvFFKOm3c)

## Key Concepts

- **Tables:** Data is organized into tables, which are essentially two-dimensional structures with rows and columns. Each table represents a specific entity or concept, such as "Customers," "Products," or "Orders."

- **Rows (Records):** Each row in a table represents a single instance of the entity. For example, a row in the "Customers" table would represent a single customer.

- **Columns (Attributes):** Columns define the characteristics or attributes of the entity. In the "Customers" table, columns might include "CustomerID," "FirstName," "LastName," "Address," and "City."

- **Primary Key:** A unique identifier for each row in a table. This ensures that each record is distinct and prevents duplicate entries. It's typically a single column, but can also be a composite key (multiple columns). Examples include `CustomerID` or a combined `LastName` and `FirstName` if needed to guarantee uniqueness.

- **Foreign Key:** A column in one table that refers to the primary key of another table. This establishes a relationship between the tables. For example, an "Orders" table might have a `CustomerID` foreign key, referencing the `CustomerID` primary key in the "Customers" table. This links orders to specific customers.

- **Relationships:** The connections between tables established via foreign keys. Common relationship types include:

  - **One-to-one:** One record in a table relates to only one record in another table.
  - **One-to-many:** One record in a table relates to multiple records in another table (e.g., one customer can have many orders).
  - **Many-to-many:** Multiple records in one table relate to multiple records in another table (e.g., many products can be in many orders). This typically requires a junction table.

- **Data Integrity:** The accuracy and consistency of data. The relational model enforces data integrity through constraints such as primary keys, foreign keys, and data types.

## Data Types

Data types specify the kind of values that can be stored in a column of a table. Choosing the right data type is important for efficiency, data integrity, and accuracy. Here are some common SQL data types:

### Numeric Data Types

- **INT (INTEGER):** Whole numbers (e.g., -10, 0, 5, 100). Usually stored using 4 bytes, with variations like `SMALLINT` (2 bytes) or `BIGINT` (8 bytes) depending on the required range of values.

- **DECIMAL(p, s) or NUMERIC(p, s):** Exact fixed-point numbers where `p` is the precision (total number of digits) and `s` is the scale (number of digits after the decimal point). For example, `DECIMAL(5, 2)` can store numbers like 123.45. Suitable for monetary values or exact calculations.

- **FLOAT(n):** Approximate floating-point numbers with a specified precision `n`. `FLOAT` is often stored using 4 or 8 bytes.

- **REAL:** Similar to float but generally with a lower precision. Also uses 4 bytes.

- **DOUBLE PRECISION:** A double-precision floating-point number (usually 8 bytes) offering a larger range and precision compared to float.

### Character/String Data Types

- **CHAR(n):** Fixed-length character strings of length `n`. If a value is shorter than `n`, it will be padded with spaces.

- **VARCHAR(n):** Variable-length character strings with a maximum length of `n`. Only uses the amount of space needed for the value being stored, plus a small overhead.

- **TEXT:** Variable-length strings that can store very large amounts of text. Often used for comments, articles, and other long texts.

### Date and Time Data Types

- **DATE:** Stores only the date (year, month, day).

- **TIME:** Stores only the time (hour, minute, second).

- **TIMESTAMP:** Stores date and time information, including the time zone (or timestamp with time zone).

- **DATETIME:** Stores date and time information, but often without specific time zone awareness.

## Boolean Data Type

- **BOOLEAN:** Stores boolean values (TRUE or FALSE).

### Other Data Types

- **BLOB (Binary Large Object):** Used to store large binary data such as images, audio, or video.
- **JSON:** Stores data in a JSON (JavaScript Object Notation) format.
- **ENUM:** Defines a column with a fixed set of possible string values.

## Connecting to a Database

Several popular RDBMS exist, including MySQL, PostgreSQL, Oracle Database, Microsoft SQL Server, and SQLite. These systems provide tools and functionalities to create, manage, and query relational databases.

In this lesson, we'll focus on using SQLite, a lightweight and self-contained database engine that doesn't require a separate server process. SQLite databases are stored in a single file, making them easy to share and transport. We'll use the Chinook sample database, which contains tables for a fictional music store.

### Chinook Database Schema

![Chinook Database Schema](./assets/sqlite-sample-database-color.jpg)

The Chinook sample database contains 11 tables, as follows:

- `employees` table stores employee data such as id, last name, first name, etc. It also has a field named ReportsTo to specify who reports to whom.
- `customers` table stores customer data.
- `invoices` & `invoice_items` tables: these two tables store invoice data. The `invoices` table stores invoice header data and the `invoice_items` table stores the invoice line items data.
- `artists` table stores artist data. It is a simple table that contains the id and name.
- `albums` table stores data about a list of tracks. Each album belongs to one artist, but an artist may have multiple albums.
- `media_types` table stores media types such as MPEG audio and AAC audio files.
- `genres` table stores music types such as rock, jazz, metal, etc.
- `tracks` table stores the data of songs. Each track belongs to one album.
- `playlists` & `playlist_track` tables: `playlists` table stores data about playlists. Each playlist contains a list of tracks. Each track may belong to multiple playlists. The relationship between the `playlists` and `tracks` tables is many-to-many. The `playlist_track` table is used to reflect this relationship.

### Download and Connect to the Chinook Database

1. Download the Chinook database file ([chinook.db](https://www.sqlitetutorial.net/wp-content/uploads/2018/03/chinook.zip)) and extract it to a convenient location on your computer.
2. To connect to the Chinook database using DBeaver:

   - Open DBeaver
   - Click "New Database Connection" (plug icon)
   - Select "SQLite" and click "Next"
   - Click "Open..." beside the Path and locate your downloaded chinook.db file
   - Click "Test Connection..." to verify
     - You might need to download the SQLite driver if it's not already installed on the first connection attempt
     - You will see a message "Connected" if the connection is successful
   - Click "Finish" to create the connection
   - The database will appear in the Database Navigator panel
   - Click on the database to expand and view the tables

3. To create and run SQL queries:
   - Right-click `chinook.db` and select "SQL Editor" → "New SQL Script"
   - The query editor will open with a blank script
   - Write your SQL query in the editor (see next lesson)
   - Click the "Execute SQL Script" button (green play icon) or press Ctrl+Enter (Cmd+Enter on Mac) to run the query
   - Results will appear in the panel below the query editor
