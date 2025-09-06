# ETL Server Access Log Processing DAG

This exercise guides you through creating an Apache Airflow DAG named `ETL_Server_Access_Log_Processing.py` to process a server access log file.

## Steps

### 1. Imports Block

Import the necessary Airflow modules and Python libraries.

### 2. DAG Arguments Block

Define the default arguments for the DAG. You can use the default settings.

### 3. DAG Definition Block

Create the DAG definition. The DAG should run **daily**.

### 4. Download Task

Create a task to download the server access log file from the following URL:

```
https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Build%20a%20DAG%20using%20Airflow/web-server-access-log.txt
```

### 5. Extract Task

The server access log file contains these fields:

- `timestamp` - TIMESTAMP
- `latitude` - float
- `longitude` - float
- `visitorid` - char(37)
- `accessed_from_mobile` - boolean
- `browser_code` - int

The extract task must extract the fields **timestamp** and **visitorid**.

### 6. Transform Task

The transform task must **capitalize** the `visitorid`.

### 7. Load Task

The load task must **compress** the extracted and transformed data.

### 8. Task Pipeline Block

Schedule the tasks in the following order:

1. download
2. extract
3. transform
4. load

### 9. Submit the DAG

Submit the DAG.

### 10. Verification

Verify if the DAG is submitted successfully.


# Answer Code

```python
#import the important libraries
from datetime import timedelta
from airflow.models import DAG
from airflow.operators.bash_operator import BashOperator
from airflow.utils.dates import days_ago

#Defining the DAG argumnets
default_args = {
    'owner:': 'Yadhunath',
    'start_date': days_ago(0),
    'retries': 1,
    'retry_delay':timedelta(minutes=5),
}

#DAG initialization
dag = DAG(
    'ETL_Server_Access_Log_Processing',
    default_args=default_args,
    description='Capitalizing the VisitorID using Bash Operator',
    schedule_interval=timedelta(days=1),
)

#define download
download = BashOperator(
    task_id='download',
    bash_command='curl "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Apache%20Airflow/Build%20a%20DAG%20using%20Airflow/web-server-access-log.txt" -o web-server-access-log.txt',
    dag=dag,
)

#define the tasks
extract = BashOperator(
    task_id='extract',
    bash_command='cut -d"#" -f1,4 web-server-access-log.txt > /home/project/airflow/dags/extracted-data.txt',
    dag=dag
)

transform = BashOperator(
    task_id='transform',
    bash_command='tr "(a-z)" "(A-Z)" < /home/project/airflow/dags/extracted-data.txt > /home/project/airflow/dags/tranformed-data.txt'
    dag=dag
)

load = BashOperator(
    task_id='load',
    bash_command='zip log.zip capitalized.txt '
    dag=dag
)

#task pipelined
download >> extract >> transform >> load
```

Submit the DAG by running the following command.

```
 cp  ETL_Server_Access_Log_Processing.py $AIRFLOW_HOME/dags
```
Verify if the DAG is submitted on the Web UI or the CLI using the below command.

```
airflow dags list
```