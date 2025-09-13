# Pipeline Final Project in Airflow

## Project Scenario

You are a data engineer at a data analytics consulting company. You have been assigned a project to decongest the national highways by analyzing the road traffic data from different toll plazas. Each highway is operated by a different toll operator with a different IT setup that uses different file formats. Your job is to collect data available in different formats and consolidate it into a single file.

## Objectives

In this assignment, you will develop an Apache Airflow DAG that will:

- Extract data from a CSV file
- Extract data from a TSV file
- Extract data from a fixed-width file
- Transform the data
- Load the transformed data into the staging area

---

### Steps

1. **Start Apache Airflow**  
    Open Apache Airflow in your IDE.

2. **Create Directory Structure**  
    Open a terminal and create a directory structure for the staging area:
    ```
    /home/project/airflow/dags/finalassignment/staging
    ```
    ```bash
    sudo mkdir -p /home/project/airflow/dags/finalassignment/staging
    ```

3. **Set Directory Permissions**  
    Execute the following command to give appropriate permissions:
    ```bash
    sudo chmod -R 777 /home/project/airflow/dags/finalassignment
    ```

4. **Download the Dataset**  
    Download the data set from the source to the destination using the `curl` command:
    ```bash
    sudo curl https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0250EN-SkillsNetwork/labs/Final%20Assignment/tolldata.tgz -o /home/project/airflow/dags/finalassignment/tolldata.tgz
    ```

---

## ETL_toll_data.py

Below is the `ETL_toll_data.py` file containing the required packages for creating a DAG and running the necessary operations listed above:

```python
from datetime import timedelta
from airflow.models import DAG
from airflow.operators.bash_operator import BashOperator 
from airflow.utils.dates import days_ago

default_args={
     'owner':'Yadhunath',
     'start_date':days_ago(0),
     'email':'my@email.com',
     'email_on_failure':True,
     'email_on_retry':True,
     'retries':1,
     'retry_delay':timedelta(minutes=5),
}

dag=DAG(
     'ETL_toll_data',
     schedule_interval=timedelta(days=1),
     default_args=default_args,
     description='Apache Firlow final assignment.',
)

#TASK 1: Unzip the tollgate.tgz file.
unzip_data = BashOperator(
     task_id='unzip_data',
     bash_command='tar -xvzf tolldata.tgz -C /home/project/airflow/dags/finalassignment',
     dag=dag,
)

#TASK 2: extract the fields Rowid, Timestamp, Anonymized Vehicle number, and Vehicle type from the vehicle-data.csv file and save them into csv_data.csv
extract_data_from_csv = BashOperator(
     task_id='extract_data_from_csv',
     bash_command='cut -d "," -f1-4 vehicle-data.csv > csv_data.csv',
     dag=dag,
)

#TASK 3: extract the fields Number of axles, Tollplaza id, and Tollplaza code from the tollplaza-data.tsv file and save it into tsv_data.csv
extract_data_from_tsv = BashOperator(
     task_id='extract_data_from_tsv',
     bash_command='cut -f5-7 --output-delimiter="," tollplaza-data.tsv > tsv_data.csv',
     dag=dag,
)

#TASK 4:extract the fields Type of Payment code, and Vehicle Code from the fixed width file payment-data.txt and save it into fixed_width_data.csv
extract_data_from_fixed_width = BashOperator(
     task_id='extract_data_from_fixed_width',
     bash_command="awk '{print $(NF-1) \",\" $NF}' payment-data.txt > fixed_width_data.csv",
     dag=dag,
)

#TASK 5: Consolidating csv_data.csv, tsv_data.csv, and fixed_width_data.csv to a single extracted_data.csv file.
consolidate_data = BashOperator(
     task_id='consolidate_data',
     bash_command='paste -d"," csv_data.csv tsv_data.csv fixed_width_data.csv > extracted_data.csv',
     dag=dag,
)

#TASK 6: transforming the data from the extracted_data.csv file to capital letters.
transform_data = BashOperator(
     task_id='transform_data',
     bash_command='tr "(a-z)" "(A-Z)" < extracted_data.csv > transformed_data.csv',
     dag=dag,
)

#TASK 7: Define the pipeline order.
unzip_data >> extract_data_from_csv >> extract_data_from_tsv >> extract_data_from_fixed_width >> consolidate_data >> transform_data
 ```

---

## Next Steps

- Copy this file into the correct directory that stores the Airflow DAGs:
  ```bash
  cp ETL_toll_data.py /home/project/airflow/dags/finalassignment
  ```

- You can see the project by running:
  ```bash
  airflow dags list | grep ETL_toll_data
  ```

- To see the listed tasks, run:
  ```bash
  airflow dags tasks list
  ```
