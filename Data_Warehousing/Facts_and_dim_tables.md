# Designing a Data Warehouse for a Cloud Service Provider

The cloud service provider has provided billing data in the CSV file `cloud-billing-dataset.csv`, containing billing records for the past decade.

---

## 1. Study the Schema

Below are the field-wise details of the billing data:

| Field Name   | Details                                                                 |
|--------------|-------------------------------------------------------------------------|
| customerid   | ID of the customer                                                      |
| category     | Category of the customer (e.g., Individual or Company)                  |
| country      | Country of the customer                                                 |
| industry     | Domain/industry the customer belongs to (e.g., Legal, Engineering)      |
| month        | Billed month (YYYY-MM), e.g., 2009-01 refers to January 2009            |
| billedamount | Amount charged for that month in USD                                    |

**Queries to Support:**
- Average billing per customer
- Billing by country
- Top 10 customers
- Top 10 countries
- Billing by industry
- Billing by category
- Billing by year
- Billing by month
- Billing by quarter
- Average billing per industry per month
- Average billing per industry per quarter
- Average billing per country per quarter
- Average billing per country per industry per quarter

---

## 2. Fact Table Design

The fact table captures the core transactional data.

| Field Name   | Details                                                                 |
|--------------|-------------------------------------------------------------------------|
| billid       | Primary key - Unique identifier for every bill                          |
| customerid   | Foreign key - ID of the customer                                        |
| monthid      | Foreign key - ID of the month (resolves billed month info)              |
| billedamount | Amount charged for that month in USD                                    |

---

## 3. Dimension Table Design

### Customer Dimension

| Field Name   | Details                                                                 |
|--------------|-------------------------------------------------------------------------|
| customerid   | Primary key - ID of the customer                                        |
| category     | Category of the customer (e.g., Individual or Company)                  |
| country      | Country of the customer                                                 |
| industry     | Domain/industry the customer belongs to (e.g., Legal, Engineering)      |

### Date/Month Dimension

| Field Name   | Details                                                                 |
|--------------|-------------------------------------------------------------------------|
| monthid      | Primary key - ID of the month                                           |
| year         | Year derived from the month field (e.g., 2010)                          |
| month        | Month number derived from the month field (e.g., 1, 2, 3)               |
| monthname    | Month name derived from the month field (e.g., March)                   |
| quarter      | Quarter number derived from the month field (e.g., 1, 2, 3, 4)          |
| quartername  | Quarter name derived from the month field (e.g., Q1, Q2, Q3, Q4)        |

---

## 4. Star Schema Overview

| Table Name   | Type       | Details                                                                 |
|--------------|------------|-------------------------------------------------------------------------|
| FactBilling  | Fact       | Contains billing amount and foreign keys to customer and month data     |
| DimCustomer  | Dimension  | Contains all information related to the customer                        |
| DimMonth     | Dimension  | Contains all information related to the month of billing                |

**Table Structures:**

**FactBilling**
```
|-- rowid integer (primary key)
|-- customerid integer (connects to DimCustomer)
|-- monthid integer (foreign key from DimMonth)
|-- billedamount integer
```

**DimCustomer**
```
|-- customerid integer (primary key)
|-- category char
|-- country char
|-- industry char
```

**DimMonth**
```
|-- monthid integer (primary key)
|-- year integer
|-- month integer
|-- monthname char
|-- quarter integer
|-- quartername char
```

---

## 5. Creating the Schema in PostgreSQL

1. **Set PostgreSQL Password:**
    ```bash
    export PGPASSWORD=<your_password>
    ```

2. **Create the Database:**
    ```bash
    createdb -h postgres -U postgres -p 5432 billingDW
    ```

    - `-h`: Hostname (`postgres`)
    - `-U`: Username (`postgres`)
    - `-p`: Port (`5432`)

    Example output:
    ```
    createdb -h localhost -U postgres -p 5432 billingDW
    ```

3. **Download the Schema SQL File:**
    ```bash
    wget https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0260EN-SkillsNetwork/labs/Working%20with%20Facts%20and%20Dimension%20Tables/star-schema.sql
    ```

4. **Create the Schema:**
    ```bash
    psql -h postgres -U postgres -p 5432 billingDW < star-schema.sql
    ```

    Example output:
    ```
    -- BEGIN
    -- CREATE TABLE
    -- CREATE TABLE
    -- CREATE TABLE
    -- ALTER TABLE
    -- ALTER TABLE
    -- COMMIT
    ```