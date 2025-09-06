# DAG in Apache Airflow using BashOperator

Let's create a DAG that runs daily, extracts user information from the `/etc/passwd` file, transforms it, and loads it into a file.

This DAG will have two tasks: `extract` (extracts fields from `/etc/passwd`) and `transform_and_load` (transforms and loads data into a file).

## Example DAG Code

```python
# import the libraries
from datetime import timedelta
# The DAG object; we'll need this to instantiate a DAG
from airflow.models import DAG
# Operators; you need this to write tasks!
from airflow.operators.bash_operator import BashOperator
# This makes scheduling easy
from airflow.utils.dates import days_ago

# defining DAG arguments
default_args = {
    'owner': 'your_name_here',
    'start_date': days_ago(0),
    'email': ['your_email_here'],
    'retries': 1,
    'retry_delay': timedelta(minutes=5),
}

# defining the DAG
dag = DAG(
    'my-first-dag',
    default_args=default_args,
    description='My first DAG',
    schedule_interval=timedelta(days=1),
)

# define the tasks
extract = BashOperator(
    task_id='extract',
    bash_command='cut -d":" -f1,3,6 /etc/passwd > /home/project/airflow/dags/extracted-data.txt',
    dag=dag,
)

transform_and_load = BashOperator(
    task_id='transform',
    bash_command='tr ":" "," < /home/project/airflow/dags/extracted-data.txt > /home/project/airflow/dags/transformed-data.csv',
    dag=dag,
)

# task pipeline
extract >> transform_and_load
```

Create a new file by choosing **File → New File** and naming it `my_first_dag.py`. Then, copy the code above and paste it into `my_first_dag.py`.

## Submit a DAG

Submitting a DAG is as simple as copying the DAG Python file into the `dags` folder in the `AIRFLOW_HOME` directory.

Airflow searches for Python source files within the specified `DAGS_FOLDER`. The location of `DAGS_FOLDER` can be found in the `airflow.cfg` file, where it is configured as `/home/project/airflow/dags`.

Airflow will load the Python source files from this location, process each file, execute its contents, and subsequently load any DAG objects present in the file.

Therefore, when submitting a DAG, it is essential to position it within this directory structure. Alternatively, the `AIRFLOW_HOME` directory, representing the structure `/home/project/airflow`, can also be used for DAG submission.

Open a terminal and run the following commands to set the `AIRFLOW_HOME`:

```bash
export AIRFLOW_HOME=/home/project/airflow
echo $AIRFLOW_HOME
```

Run the command below to submit the DAG that was created in the previous exercise:

```bash
cp my_first_dag.py $AIRFLOW_HOME/dags
```

## Verify DAG Submission

List all existing DAGs:

```bash
airflow dags list
```

Check specifically for your DAG:

```bash
airflow dags list | grep "my-first-dag"
```

You should see your DAG name in the output.

To list all tasks in `my-first-dag`:

```bash
airflow tasks list my-first-dag
```

You should see two tasks: `extract` and `transform`.

---

## Steps Summary

### 1. Create the DAG File

- Open your code editor.
- Create a new file named `my_first_dag.py`.
- Copy and paste the provided Python code into this file.

### 2. Set the AIRFLOW_HOME Environment Variable

```bash
export AIRFLOW_HOME=/home/project/airflow
echo $AIRFLOW_HOME
```

### 3. Submit the DAG

Copy your DAG file to the Airflow DAGs folder:

```bash
cp my_first_dag.py $AIRFLOW_HOME/dags
```

### 4. Verify DAG Submission

List all DAGs to confirm your DAG is present:

```bash
airflow dags list
```

Check specifically for your DAG:

```bash
airflow dags list | grep "my-first-dag"
```

You should see your DAG name in the output.

Run the command below to list out all the tasks in my-first-dag.

```
airflow tasks list my-first-dag
```

You should see 2 tasks in the output.