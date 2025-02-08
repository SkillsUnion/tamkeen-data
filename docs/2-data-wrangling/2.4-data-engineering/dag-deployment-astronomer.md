# Building a Local Data Pipeline with Astro CLI and SQLite

## Introduction

This lesson guides you through building a local data pipeline using the Astro CLI and SQLite. We'll cover setting up your environment, creating an ETL pipeline, and testing/debugging it.

## Prerequisites

- [Astro CLI](https://docs.astronomer.io/astro/install-cli) installed
- Basic knowledge of Python and SQL
- Docker installed

## 1. Introduction to Astro CLI

The Astro CLI is a command-line tool that simplifies the process of developing, testing, and deploying Airflow DAGs. It allows you to manage Airflow environments locally and in the cloud.

### Installing Astro CLI

Follow the instructions on the [Astro CLI installation page](https://docs.astronomer.io/astro/install-cli) to install the CLI for your operating system.

### Initializing an Astro Project

To start a new project, run:

```bash
astro dev init my_astro_project
cd my_astro_project
```

This creates a new directory `my_astro_project` with the necessary files for an Astro project, including a `Dockerfile`, `dags` folder, and `requirements.txt`.

## 2. Setting up a Local Airflow Environment

The Astro CLI uses Docker to create a local Airflow environment.

### Starting the Airflow Environment

To start the environment, run:

```bash
astro dev start
```

This command builds the Docker image and starts the Airflow webserver, scheduler, and worker. You can access the Airflow UI at `http://localhost:8080`. The default username is `airflow` and the password is `airflow`.

### Stopping the Airflow Environment

To stop the environment, run:

```bash
astro dev stop
```

## 3. Building an ETL Pipeline with SQLite

We'll build an ETL pipeline that extracts data from a CSV file, transforms it, and loads it into a SQLite database.

### Dataset: Sample Sales Data

For this example, we'll use a sample sales data CSV file. Let's create a `sales_data.csv` file with the following content:

```csv
order_id,customer_id,order_date,product_id,quantity,price
1,101,2023-01-01,A1,2,10.00
2,102,2023-01-02,B2,1,20.00
3,101,2023-01-03,A1,3,10.00
4,103,2023-01-04,C3,1,30.00
5,102,2023-01-05,B2,2,20.00
```

Save this file in the `include/` directory of your Astro project.

### Creating the DAG

Create a new DAG file named `sales_pipeline.py` in the `dags/` directory:

```python
# filepath: dags/sales_pipeline.py
from airflow import DAG
from airflow.operators.python import PythonOperator
from airflow.providers.sqlite.operators.sqlite import SqliteOperator
from airflow.providers.sqlite.hooks.sqlite import SqliteHook
from datetime import datetime
import csv

# Define constants
CSV_FILEPATH = "include/sales_data.csv"
SQLITE_FILEPATH = "include/sales.db"
TABLE_NAME = "sales"

# Define default arguments for the DAG
default_args = {
    'owner': 'airflow',
    'start_date': datetime(2023, 1, 1),
    'provide_context': True,
}

# Define a function to create the SQLite table
def create_table():
    sqlite_hook = SqliteHook(sqlite_conn_id='sqlite_default', database=SQLITE_FILEPATH)
    query = f"""
        CREATE TABLE IF NOT EXISTS {TABLE_NAME} (
            order_id INTEGER PRIMARY KEY,
            customer_id INTEGER,
            order_date TEXT,
            product_id TEXT,
            quantity INTEGER,
            price REAL
        );
    """
    sqlite_hook.execute(query)
    sqlite_hook.close_conn()

# Define a function to extract data from the CSV file and load it into SQLite
def load_data():
    sqlite_hook = SqliteHook(sqlite_conn_id='sqlite_default', database=SQLITE_FILEPATH)
    with open(CSV_FILEPATH, 'r') as file:
        reader = csv.DictReader(file)
        data = list(reader)

    values = [tuple(row.values()) for row in data]
    placeholders = ', '.join(['?' for _ in data[0]])
    query = f"INSERT INTO {TABLE_NAME} VALUES ({placeholders})"
    sqlite_hook.insert_rows(table=TABLE_NAME, rows=values, target_fields=list(data[0].keys()))
    sqlite_hook.close_conn()

# Define the DAG
with DAG('sales_pipeline',
         default_args=default_args,
         schedule_interval=None,  # Set to None for manual triggering
         catchup=False) as dag:

    # Define the task to create the table
    create_table_task = PythonOperator(
        task_id='create_table',
        python_callable=create_table,
        dag=dag,
    )

    # Define the task to load data
    load_data_task = PythonOperator(
        task_id='load_data',
        python_callable=load_data,
        dag=dag,
    )

    # Define the task dependencies
    create_table_task >> load_data_task
```

### Adding SQLite Provider

Add the SQLite provider to your `requirements.txt` file:

```text
apache-airflow-providers-sqlite
```

Then, run `astro dev restart` to update the environment with the new dependency.

## 4. Testing and Debugging Your Pipeline

### Running the DAG

1.  Start the Astro environment: `astro dev start`
2.  Open the Airflow UI at `http://localhost:8080`.
3.  Find the `sales_pipeline` DAG and trigger it manually.
4.  Monitor the task logs to ensure the pipeline runs successfully.

### Debugging

If the DAG fails, check the task logs for error messages. Common issues include:

- Incorrect file paths
- Missing dependencies
- SQL syntax errors

You can use the Airflow UI to view the DAG's graph and task details, which can help you identify the source of the problem.

### Verifying the Data

After the DAG runs successfully, you can verify the data in the SQLite database using a SQLite client or by querying the database directly from Python.

```python
import sqlite3

# Connect to the SQLite database
conn = sqlite3.connect('include/sales.db')
cursor = conn.cursor()

# Execute a query to fetch data from the sales table
cursor.execute("SELECT * FROM sales")
rows = cursor.fetchall()

# Print the data
for row in rows:
    print(row)

# Close the connection
conn.close()
```
