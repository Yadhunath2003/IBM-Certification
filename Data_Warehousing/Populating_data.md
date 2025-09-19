# Populating Data in Data Warehouses

## Objectives

- Create production-related databases and tables in a PostgreSQL instance.
- Populate the production data warehouse by loading the tables from scripts.

---

## 1. Create the Database

- Open the pgAdmin GUI and click on the **Servers** tab on the left side of the page. You will be prompted to enter a password.
- In the left tree-view, right-click on **Databases > Create > Database**.
- In the **Database** box, type `Production` as the name for your new database, and then click **Save**. Proceed to Task B.

---

## 2. Create the Tables

- Create a `star-schema.sql` file to define the "DimCustomer", "DimMonth", and "FactBilling" tables.

```sql
CREATE TABLE "DimCustomer"
(
    customerid integer NOT NULL PRIMARY KEY,
    category varchar(10) NOT NULL,
    country char NOT NULL,
    industry char NOT NULL
);

CREATE TABLE "DimMonth"
(
    monthid integer NOT NULL PRIMARY KEY,
    year integer NOT NULL,
    month integer NOT NULL,
    monthname char NOT NULL,
    quarter integer NOT NULL,
    quartername char NOT NULL
);

CREATE TABLE "FactBilling"
(
    billid integer NOT NULL PRIMARY KEY,
    monthid integer NOT NULL,
    customerid integer NOT NULL,
    billedamount bigint NOT NULL
);
```

- In the **Production** database, go to the **Query Tool** and click on **Open File**. A new page called **Select File** will pop up.
- Click on the three dots and choose **Upload**.
- Drag and drop the `star-schema.sql` file into the blank page. Once the file is successfully loaded, click on the **X** icon on the right-hand side.
- Open the file and click on the **Run** option to execute the `star-schema.sql` file.
- Right-click on the **Production** database and select **Refresh** from the dropdown.
- After refreshing, the three tables (`DimCustomer`, `DimMonth`, `FactBilling`) will appear under **Databases > Production > Schemas > Public > Tables**.

---

## 3. Loading the Tables

- Create a `DimCustomer.sql` file to insert values into the `DimCustomer` table.

```sql
INSERT INTO "DimCustomer"
(
    customerid, category, country, industry 
)
VALUES
(
    1, 'Individual', 'Indonesia', 'Engineering',
    2, 'Company', 'India', 'Mechanical',
    3, 'Individual', 'US', 'Electrical'
);
```

- Open the **Query Tool**, click **Open File**, and choose the **Upload** option.
- Drag and drop the `DimCustomer.sql` file into the blank page. Once the file is successfully loaded, open it and click on the **Run** option to execute the `DimCustomer.sql` file.
- Repeat the same process for the other tables: `DimMonth` and `FactBilling`.

- Run the following command in the PostgreSQL Tool to verify the number of entries in the `DimMonth` table:

```sql
select count(*) from public."DimMonth";
```

---

## Exercise Problems

### Problem 1: Count Rows in `FactBilling`

Find the count of rows in the `FactBilling` table:

```sql
select count(*) from public."FactBilling";
```

### Problem 2: Create a Materialized View

Create a materialized view named `avg_customer_bill` with fields `customerid` and `averagebillamount`:

```sql
CREATE MATERIALIZED VIEW avg_customer_bill (customerid, averagebillamount) AS
(select customerid, avg(billedamount)
from public."FactBilling"
group by customerid
);
```

### Problem 3: Refresh the Materialized View

Refresh the newly created materialized view:

```sql
REFRESH MATERIALIZED VIEW avg_customer_bill;
```

### Problem 4: Create Another Materialized View

Create a materialized view named `avg_bill_11000` with fields `customerid` whose average billing is more than 11000:

```sql
CREATE MATERIALIZED VIEW avg_bill_11000 (customerid, averagebillover11k) AS
(select customerid, avg(billedamount) AS avgOver11000
from public."FactBilling"
where avgOver11000 > 11000
group by customerid
);
```

### Problem 5: Query Customers with Average Billing > 11000

Using the newly created materialized view, find the customers whose average billing is more than 11000:

```sql
select * from avg_customer_bill where averagebillamount > 11000;
``` 