## Exercise: ETL Server Access Log Processing DAG

Create an Apache Airflow DAG named **ETL_Server_Access_Log_Processing** to perform the following ETL steps on a server access log file:

### Log File Fields

- **timestamp**: TIMESTAMP  
- **latitude**: float  
- **longitude**: float  
- **visitorid**: char(37)  
- **accessed_from_mobile**: boolean  
- **browser_code**: int  

### Tasks

1. **Download the File**  
    - Add a task to download `web-server-access-log.txt` from a remote server.

2. **Extract Fields**  
    - Read the downloaded file.
    - Extract the `timestamp` and `visitorid` fields from each record.

3. **Transform Data**  
    - Capitalize the `visitorid` for all records.
    - Store the transformed data in a local variable.

4. **Load Data**  
    - Write the extracted and transformed data into a new file named `capitalized.txt`.

### DAG Requirements

- **Imports Block**: Add necessary Python and Airflow imports.
- **DAG Arguments Block**: Use default arguments for the DAG.
- **DAG Definition Block**: Define the DAG to run daily.
- **Tasks**: Create `extract`, `transform`, and `load` tasks to call the respective Python scripts.
- **Task Pipeline Block**: Set up the task dependencies (pipeline).
- **Submission**: Submit the DAG.
- **Verification**: Verify that the DAG is submitted successfully.

---

## Answer code

```python
#Importing the necessary libraries
from datetime inport timedelta
from airflow.models import DAG
from airflow.operations.python import PythonOperator
from airflow.utils.dates import days_ago

#Files definition
input_file = 'Airflow/web-server-access-log.txt'
extracted_file = 'extract_DAG.txt'
transformed_file = 'transform_DAG.txt'
loaded_file = 'capitalized.txt'


#Function for defining the extract file.
def extract():
    global input_file
    print("Extracting...")
    with open(input_file, 'r') as infile, \ 
    open(extracted_file, 'w') as outfile:
        for line in infile:
            fields = line.split('#')
            if len(fields) >= 4:
                field_1 = fields[0]
                field_4 = fields[3]
                outfile.write(field_1 + "#" + field_4 + "\n")

#function for tranforming the file
def transform():
    global extracted_file, transformed_file
    print("Transforming...")
    with open(extracted_file, 'r') as infile, \
    open(transformed_file, 'w') as outfile:
        for line in infile:
            capitalize = line.upper()
            outfile.write(capitalized + '\n')

#function for loading
def extract():
    global loaded_file, transformed_file
    print("Loading...")
    with open(transformed_file, 'r') as infile, \
    open(loaded_file, 'w') as outfile:
        for line in infile:
            outfile.write(line + '\n')

#function for checking it is in the correct file
global output_file
    print("Inside Check")
    with open(output_file, 'r') as infile:
        for line in infile:
            print(line)

#DAG arguments
default_args = {
    'owner':'Yadhunath',
    'start_date':days_ago(0),
    'email':['y2003@gmail.com'],
    'retries':1,
    'retry_delay':timedelta(days=1)
}

#DAG initialization
dag = DAG(
    'capitalize-visitorid-dag',
    default_args=default_args,
    description = 'DAG_for_capitalizing',
    schedule_interval=timedelta(days=1)
)

#to execute the extract, transform, and load functions
execute_extract = PythonOperator(
    task_id = 'extract',
    python_callable=extract,
    dag=dag,
)

execute_extract = PythonOperator(
    task_id = 'extract',
    python_callable=extract,
    dag=dag,
)

execute_transform = PythonOperator(
    task_id = 'transform',
    python_callable=transform,
    dag=dag,
)

execute_load = PythonOperator(
    task_id = 'load',
    python_callable=load,
    dag=dag,
)

execute_check = PythonOperator(
    task_id = 'check',
    python_callable=check,
    dag=dag,
)

#Task pipeline

execute_extract >> extracr_tranform >> extract_load >> extract_check
```

Copy the DAG file into the dags directory.

```
cp ETL_Server_Access_Log_Processing.py $AIRFLOW_HOME/dags
```

Verify if the DAG is submitted by running the following command.

```
airflow dags list | grep etl-server-logs-dag
```

If the DAG didn't get imported properly, you can check the error using the following command.

```
airflow dags list-import-errors
```