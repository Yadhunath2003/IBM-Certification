# Verifying Data Quality for a Data Warehouse

## Objective

- Check Null values
- Check Duplicate values
- Check Min/Max values
- Check Invalid values
- Generate a report on data quality

---

## 1. Setting up the Staging Area

Download the staging area setup script:

```bash
wget https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/8ZUkKar_boDbhNgMiwGAWg/setupstagingarea.sh
```

Export the Postgres password for your server:

```bash
export PGPASSWORD=<your postgres password>
```

Run the setup script:

```bash
bash setupstagingarea.sh
```

---

## 2. Getting the Test Framework Ready

We will use a Python-based framework to run the data quality tests.

**Step 1: Download the framework**

```bash
wget https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0260EN-SkillsNetwork/labs/Verifying%20Data%20Quality%20for%20a%20Data%20Warehouse/dataqualitychecks.py
wget https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/HB0XK4MDrGwigMmVPmPoeQ/dbconnect.py
wget https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0260EN-SkillsNetwork/labs/Verifying%20Data%20Quality%20for%20a%20Data%20Warehouse/mytests.py
wget https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/saTxV8y9Kt-e8Zxe29M0TA/generate-data-quality-report.py
ls
```

**Step 2: Install the Python driver for PostgreSQL**

```bash
python3 -m pip install psycopg2
```

**Step 3: Update Password in `dbconnect.py`**

```python
import os
import psycopg2
pgpassword = "PGPASSWORD" #Actual password needs to be entered.
conn = None
try:
    conn = psycopg2.connect(
        user = "postgres",
        password = pgpassword,
        host = "postgres",
        port = "5432",
        database = "postgres")
except Exception as e:
    print("Error connecting to data warehouse")
    print(e)
else:
    print("Successfully connected to warehouse")
finally:
    if conn:
        conn.close()
        print("Connection closed")
```

Run the following command to check the connection:

```bash
python3 dbconnect.py
```

Expected output:

```
Successfully connected to warehouse
Connection closed
```

---

## 3. Create a Sample Data Quality Report

Install required Python packages:

```bash
python3 -m pip install pandas tabulate
```

Generate a sample data quality report:

```bash
python3 generate-data-quality-report.py
```

Example code:

```python
# connect to database
pgpassword = "aLSH8U5t4XpebdHzrzetfiHJ"
conn = psycopg2.connect(
        user = "postgres",
        password = pgpassword,
        host = "postgres",
        port = "5432",
        database = "billingDW")

print("Connected to data warehouse")

#Start of data quality checks
results = []
tests = {key:value for key,value in mytests.__dict__.items() if key.startswith('test')}
for testname,test in tests.items():
    test['conn'] = conn
    results.append(run_data_quality_check(**test))

#print(results)
df=pd.DataFrame(results)
df.index+=1
df.columns = ['Test Name', 'Table','Column','Test Passed']
print(tabulate(df,headers='keys',tablefmt='psql'))
#End of data quality checks
conn.close()
print("Disconnected from data warehouse")
```

Sample output:

```bash
Connected to data warehouse
**************************************************
Mon Sep 15 23:50:46 2025
Starting test Check for nulls
Finished test Check for nulls
Test Passed True
Test Parameters
column = monthid
table = DimMonth

Duration :  0.004483938217163086
Mon Sep 15 23:50:46 2025
**************************************************
**************************************************
Mon Sep 15 23:50:46 2025
Starting test Check for min and max
Finished test Check for min and max
Test Passed True
Test Parameters
column = month
table = DimMonth
minimum = 1
maximum = 12

Duration :  0.0016942024230957031
Mon Sep 15 23:50:46 2025
**************************************************
**************************************************
Mon Sep 15 23:50:46 2025
Starting test Check for valid values
{'Individual', 'Company'}
Finished test Check for valid values
Test Passed True
Test Parameters
column = category
table = DimCustomer
valid_values = {'Individual', 'Company'}

Duration :  0.0031561851501464844
Mon Sep 15 23:50:46 2025
**************************************************
**************************************************
Mon Sep 15 23:50:46 2025
Starting test Check for duplicates
Finished test Check for duplicates
Test Passed True
Test Parameters
column = monthid
table = DimMonth

Duration :  0.0021600723266601562
Mon Sep 15 23:50:46 2025
**************************************************
+----+------------------------+-------------+----------+---------------+
|    | Test Name              | Table       | Column   | Test Passed   |
|----+------------------------+-------------+----------+---------------|
|  1 | Check for nulls        | DimMonth    | monthid  | True          |
|  2 | Check for min and max  | DimMonth    | month    | True          |
|  3 | Check for valid values | DimCustomer | category | True          |
|  4 | Check for duplicates   | DimMonth    | monthid  | True          |
+----+------------------------+-------------+----------+---------------+
Disconnected from data warehouse
```

---

## 4. Explore the Data Quality Tests

Open `mytest.py` to see the following tests:

- **check_for_nulls**: Checks for nulls in a column
- **check_for_min_max**: Checks if values in a column are within a min/max range
- **check_for_valid_values**: Checks for invalid values in a column
- **check_for_duplicates**: Checks for duplicates in a column

Each test requires at least 4 parameters:

- `testname`: Human-readable name for reporting
- `test`: The actual test function
- `table`: Table name to test
- `column`: Column name to test

Example test definitions:

```python
from dataqualitychecks import check_for_nulls
from dataqualitychecks import check_for_min_max
from dataqualitychecks import check_for_valid_values
from dataqualitychecks import check_for_duplicates

test1={
    "testname":"Check for nulls",
    "test":check_for_nulls,
    "column": "monthid",
    "table": "DimMonth"
}

test2={
    "testname":"Check for min and max",
    "test":check_for_min_max,
    "column": "month",
    "table": "DimMonth",
    "minimum":1,
    "maximum":12
}

test3={
    "testname":"Check for valid values",
    "test":check_for_valid_values,
    "column": "category",
    "table": "DimCustomer",
    "valid_values":{'Individual','Company'}
}

test4={
    "testname":"Check for duplicates",
    "test":check_for_duplicates,
    "column": "monthid",
    "table": "DimMonth"
}
```

---

## 5. Creating a New Check for Nulls

Add a new test to check for null values in the `year` column:

```python
#Checks Null values in the Year column
test5={
    "testname":"Check for nulls",
    "test":check_for_nulls,
    "column": "year",
    "table": "DimMonth"
}
```

---

## 6. Check for Min and Max Range

Add a test for min/max range in the `year` column:

```python
#Checks min max range values in the Year column
test6={
    "testname":"Check for min max range",
    "test":check_for_min_max,
    "column": "year",
    "table": "DimMonth",
    "minimum": 1,
    "maximum": 4
}
```

---

## 7. Check for Valid or Invalid Entries

Add a test for invalid entries in the `quartername` column of the `DimMonth` table:

```python
#Checks for invalid entries in quatername column
test7={
    "testname":"Check for valid values",
    "test":check_for_valid_values,
    "column": "quartername",
    "table": "DimCustomer",
    "valid_values":{'Q1','Q2', 'Q3', 'Q4'}
}
```

---

## 8. Check for Duplicate Entries

Add a test for duplicate entries in the `customerid` column of the `DimCustomer` table:

```python
#Check for duplicate customerid in DimCustomer Table
test8={
    "testname":"Check for duplicates",
    "test":check_for_duplicates,
    "column": "customerid",
    "table": "DimCustomer"
}
```

Run the script:

```bash
python3 generate-data-quality-report.py
```

Sample output:

```bash
Duration :  0.0017006397247314453
Tue Sep 16 00:11:14 2025
**************************************************
+----+-------------------------+-------------+-------------+---------------+
|    | Test Name               | Table       | Column      | Test Passed   |
|----+-------------------------+-------------+-------------+---------------|
|  1 | Check for nulls         | DimMonth    | monthid     | True          |
|  2 | Check for min and max   | DimMonth    | month       | True          |
|  3 | Check for valid values  | DimCustomer | category    | True          |
|  4 | Check for duplicates    | DimMonth    | monthid     | True          |
|  5 | Check for nulls         | DimMonth    | year        | True          |
|  6 | Check for min max range | DimMonth    | year        | False         |
|  7 | Check for valid values  | DimMonth    | quartername | True          |
|  8 | Check for duplicates    | DimCustomer | customerid  | True          |
+----+-------------------------+-------------+-------------+---------------+
``` 

