# Pipeline Orchestration

## Airflow Architecture Overview

Apache Airflow is an open-source tool to programmatically author, schedule, and monitor workflows. It consists of several components:

- **Scheduler**: Responsible for scheduling jobs, triggering task instances, and monitoring the DAGs.
- **Executor**: Determines how tasks are executed (e.g., locally, with Celery, Kubernetes).
- **Worker**: Executes the tasks defined in the DAG.
- **Web Server**: Provides a user interface to monitor and manage DAGs.
- **Metadata Database**: Stores the state of the DAGs and tasks.

## DAGs and Operators

A Directed Acyclic Graph (DAG) is a collection of all the tasks you want to run, organized in a way that reflects their relationships and dependencies. Operators determine what actually gets done by a task.

### Example DAG

```python
from airflow import DAG
from airflow.operators.dummy_operator import DummyOperator
from airflow.operators.python_operator import PythonOperator
from datetime import datetime

def print_hello():
    return 'Hello world!'

default_args = {
    'owner': 'airflow',
    'start_date': datetime(2023, 1, 1),
    'retries': 1,
}

dag = DAG('hello_world', default_args=default_args, schedule_interval='@daily')

start = DummyOperator(task_id='start', dag=dag)
hello_task = PythonOperator(task_id='hello_task', python_callable=print_hello, dag=dag)
end = DummyOperator(task_id='end', dag=dag)

start >> hello_task >> end
```

### Types of Operators

Airflow provides several types of operators to perform different tasks:

- **DummyOperator**: Used for creating placeholders in the DAG.
- **PythonOperator**: Executes a Python function.
- **BashOperator**: Executes a bash command.
- **EmailOperator**: Sends an email.
- **SqlOperator**: Executes an SQL command.
- **HttpOperator**: Makes an HTTP request.
- **Sensor**: Waits for a certain condition to be met before proceeding.

### Example of Different Operators

```python
from airflow.operators.bash_operator import BashOperator
from airflow.operators.email_operator import EmailOperator

bash_task = BashOperator(
    task_id='bash_task',
    bash_command='echo "This is a bash command"',
    dag=dag,
)

email_task = EmailOperator(
    task_id='email_task',
    to='example@example.com',
    subject='Airflow Alert',
    html_content='<p>Your task has completed.</p>',
    dag=dag,
)

start >> bash_task >> email_task >> end
```

## Tasks and Dependencies

Tasks are the building blocks of a DAG. They represent a single unit of work within the workflow. Dependencies define the order in which tasks are executed.

### Example of Task Dependencies

In the example above, the `start` task must run before `bash_task`, and `bash_task` must run before `email_task`, and `email_task` must run before `end`. This is defined using the `>>` operator.

```python
start >> bash_task >> email_task >> end
```

This ensures that the tasks are executed in the correct order.

### More Complex Dependencies

You can also define more complex dependencies using the `set_downstream` and `set_upstream` methods.

```python
start.set_downstream(bash_task)
bash_task.set_upstream(start)
bash_task.set_downstream(email_task)
email_task.set_upstream(bash_task)
email_task.set_downstream(end)
end.set_upstream(email_task)
```

This achieves the same result as the `>>` operator but can be useful for more complex workflows.

## Schedule Interval Options

The `schedule_interval` parameter in a DAG defines how often the DAG should run. It can be set using cron expressions or predefined presets.

### Cron Expressions

Cron expressions allow you to specify the exact schedule for your DAG.

- `0 0 * * *`: Run daily at midnight.
- `0 12 * * *`: Run daily at noon.
- `0 0 * * 0`: Run weekly at midnight on Sunday.

### Preset Options

Airflow also provides several preset options for common schedules:

- `@once`: Run once.
- `@hourly`: Run every hour.
- `@daily`: Run every day at midnight.
- `@weekly`: Run every week at midnight on Sunday.
- `@monthly`: Run every month at midnight on the first day of the month.

### Example of Schedule Interval

```python
dag = DAG('hello_world', default_args=default_args, schedule_interval='@daily')
```

This sets the DAG to run daily at midnight.

## Practical Real-World Example DAG

Let's create a practical example of a DAG that performs the following tasks:

1. Downloads data from an API.
2. Processes the data.
3. Saves the processed data to a database.
4. Sends an email notification upon completion.

```python
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from airflow.operators.email_operator import EmailOperator
from datetime import datetime
import requests
import pandas as pd
import sqlite3

def download_data():
    url = 'https://api.example.com/data'
    response = requests.get(url)
    data = response.json()
    with open('/tmp/data.json', 'w') as f:
        json.dump(data, f)

def process_data():
    with open('/tmp/data.json', 'r') as f:
        data = json.load(f)
    df = pd.DataFrame(data)
    df['processed'] = True
    df.to_csv('/tmp/processed_data.csv', index=False)

def save_to_db():
    df = pd.read_csv('/tmp/processed_data.csv')
    conn = sqlite3.connect('/tmp/data.db')
    df.to_sql('data', conn, if_exists='replace', index=False)
    conn.close()

default_args = {
    'owner': 'airflow',
    'start_date': datetime(2023, 1, 1),
    'retries': 1,
}

dag = DAG('real_world_example', default_args=default_args, schedule_interval='@daily')

download_task = PythonOperator(task_id='download_data', python_callable=download_data, dag=dag)
process_task = PythonOperator(task_id='process_data', python_callable=process_data, dag=dag)
save_task = PythonOperator(task_id='save_to_db', python_callable=save_to_db, dag=dag)
email_task = EmailOperator(
    task_id='send_email',
    to='example@example.com',
    subject='Data Pipeline Completed',
    html_content='<p>The data pipeline has completed successfully.</p>',
    dag=dag,
)

download_task >> process_task >> save_task >> email_task
```
