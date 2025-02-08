# Advanced Airflow

In this lesson, we will dive deeper into Airflow features and best practices.

## Variables and Connections

Airflow allows you to manage variables and connections to external systems.

### Variables

Variables are key-value pairs that can be used to store configuration or runtime information.

```python
from airflow.models import Variable

# Set a variable
Variable.set("my_variable", "my_value")

# Get a variable
my_variable = Variable.get("my_variable")
```

### Connections

Connections store information required to connect to external systems.

```python

from airflow.hooks.base_hook import BaseHook

# Get a connection
my_conn = BaseHook.get_connection("my_connection_id")

# Use the connection
print(my_conn.host)
print(my_conn.login)
```

## Scheduling and Triggers

Airflow provides flexible scheduling options and triggers to manage DAG execution.

### Scheduling

You can schedule DAGs using cron expressions or preset options. We covered this in the previous lesson.

```python
dag = DAG('my_dag', default_args=default_args, schedule_interval='@daily')
```

### Triggers

Triggers allow you to start DAGs based on external events. This can be useful for chaining DAGs together or starting a DAG when a specific condition is met.

#### Example: Triggering a DAG from Another DAG

In this example, we will create two DAGs. The first DAG will trigger the second DAG upon completion.

**DAG 1:**

```python
from airflow import DAG
from airflow.operators.dummy_operator import DummyOperator
from airflow.operators.trigger_dagrun import TriggerDagRunOperator
from datetime import datetime

default_args = {
    'owner': 'airflow',
    'start_date': datetime(2023, 1, 1),
    'retries': 1,
}

dag1 = DAG('trigger_dag_example', default_args=default_args, schedule_interval='@daily')

start = DummyOperator(task_id='start', dag=dag1)

trigger = TriggerDagRunOperator(
    task_id='trigger_dag',
    trigger_dag_id='target_dag',
    dag=dag1,
)

end = DummyOperator(task_id='end', dag=dag1)

start >> trigger >> end
```

**DAG 2:**

```python
from airflow import DAG
from airflow.operators.dummy_operator import DummyOperator
from datetime import datetime

default_args = {
    'owner': 'airflow',
    'start_date': datetime(2023, 1, 1),
    'retries': 1,
}

dag2 = DAG('target_dag', default_args=default_args, schedule_interval=None)

start = DummyOperator(task_id='start', dag=dag2)
end = DummyOperator(task_id='end', dag=dag2)

start >> end
```

## Sensors and Hooks

Sensors and hooks are used to interact with external systems and wait for specific conditions.

### Sensors

Sensors wait for a certain condition to be met before proceeding. They are useful for tasks that depend on external events, such as the arrival of a file or the completion of a process.

#### Example: Waiting for a File to Arrive

In this example, we will create a DAG that waits for a file to arrive in a specific directory before proceeding.

```python
from airflow import DAG
from airflow.sensors.filesystem import FileSensor
from airflow.operators.dummy_operator import DummyOperator
from datetime import datetime

default_args = {
    'owner': 'airflow',
    'start_date': datetime(2023, 1, 1),
    'retries': 1,
}

dag = DAG('file_sensor_example', default_args=default_args, schedule_interval='@daily')

start = DummyOperator(task_id='start', dag=dag)

file_sensor = FileSensor(
    task_id='file_sensor',
    filepath='/path/to/file',
    poke_interval=10,
    timeout=600,
    dag=dag,
)

end = DummyOperator(task_id='end', dag=dag)

start >> file_sensor >> end
```

### Hooks

Hooks provide a way to interact with external systems. They are used to connect to databases, APIs, and other services.

#### Example: Fetching Data from an API

In this example, we will create a DAG that fetches data from an API using an HTTP hook.

```python
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from airflow.hooks.http_hook import HttpHook
from datetime import datetime

def fetch_data():
    http_hook = HttpHook(http_conn_id='my_http_connection')
    response = http_hook.run('GET', '/endpoint')
    data = response.json()
    # Process the data
    print(data)

default_args = {
    'owner': 'airflow',
    'start_date': datetime(2023, 1, 1),
    'retries': 1,
}

dag = DAG('http_hook_example', default_args=default_args, schedule_interval='@daily')

fetch_data_task = PythonOperator(
    task_id='fetch_data',
    python_callable=fetch_data,
    dag=dag,
)

fetch_data_task
```

## Airflow CLI and UI

Airflow provides a command-line interface (CLI) and a web-based user interface (UI) for managing workflows.

### CLI

The CLI allows you to perform various operations on Airflow.

```bash
# List all DAGs
airflow dags list

# Trigger a DAG
airflow dags trigger my_dag

# Pause a DAG
airflow dags pause my_dag

# Unpause a DAG
airflow dags unpause my_dag
```

### UI

The UI provides a graphical interface to monitor and manage DAGs.

- **DAGs**: View and manage DAGs.
- **Graph View**: Visualize task dependencies.
- **Tree View**: View task instances and their status.
- **Variables**: Manage variables.
- **Connections**: Manage connections.
- **Plugins**: Manage plugins.
- **Admin**: Manage users and roles.
- **Docs**: Access Airflow documentation.
