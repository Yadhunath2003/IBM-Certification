# Setting up a Staging Area

## Objectives

In this section, you will:

- Set up a staging server for a data warehouse
- Create the schema to store the data
- Load the data into the tables
- Run a sample query

---

## 1. Creating a Database

Use the `createdb` command of PostgreSQL to create a new database from the terminal.

First, export your PostgreSQL password (replace `<your_password>` with your actual password):

```bash
export PGPASSWORD=<your_password>
```

Then, create a database named `billingDW`:

```bash
createdb -h postgres -U postgres -p 5432 billingDW
```

*Tip: If running locally, use `-h localhost` instead of `-h postgres`.*

---

## 2. Creating the Data Warehouse Schema

### Step 1: Download the Schema Files

Download and extract the schema files:

```bash
wget https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DB0260EN-SkillsNetwork/labs/Setting%20up%20a%20staging%20area/billing-datawarehouse.tgz
tar -xvzf billing-datawarehouse.tgz
ls *.sql
```

You should see 4 `.sql` files listed.

### Step 2: Create the Schema

Run the following command to create the schema in the `billingDW` database:

```bash
psql -h postgres -U postgres -p 5432 billingDW < star-schema.sql
```

Expected output:

```
-- BEGIN
-- CREATE TABLE
-- CREATE TABLE
-- CREATE TABLE
-- ALTER TABLE
-- ALTER TABLE
-- COMMIT
```

---

## 3. Load Data into Dimension Tables

### Step 1: Load Data into `DimCustomer` Table

```bash
psql -h postgres -U postgres -p 5432 billingDW < DimCustomer.sql
```

### Step 2: Load Data into `DimMonth` Table

```bash
psql -h postgres -U postgres -p 5432 billingDW < DimMonth.sql
```

---

## 4. Load Data into Fact Tables

Load data into the `FactBilling` table:

```bash
psql -h postgres -U postgres -p 5432 billingDW < FactBilling.sql
```

---

## 5. Run a Sample Query

Check the number of rows in all tables:

```bash
psql -h postgres -U postgres -p 5432 billingDW < verify.sql
```

Sample output:

```
"Checking row in DimMonth Table"
count
------
132 
(1 row)

"Checking row in DimCustomer Table"
count
------
1000 
(1 row)

"Checking row in FactBilling Table"
count
------
132000 
(1 row)
```

---

## Exercise

1. **Create a database named `practice`:**

    ```bash
    createdb -h postgres -U postgres -p 5432 practice
    ```

2. **Create the schema in the `practice` database:**

    ```bash
    psql -h postgres -U postgres -p 5432 practice < star-schema.sql
    ```

3. **Load data into all tables:**

    ```bash
    psql -h postgres -U postgres -p 5432 practice < DimMonth.sql
    psql -h postgres -U postgres -p 5432 practice < DimCustomer.sql
    psql -h postgres -U postgres -p 5432 practice < FactBilling.sql
    ```

4. **Verify the data:**

    ```bash
    psql -h postgres -U postgres -p 5432 practice < verify.sql
    ```
