# Querying the Data Warehouse (Cubes, Rollups, Grouping Sets, and Materialized Views)

## Objectives

- Grouping sets
- Rollup
- Cube
- Materialized views

The **ROLLUP** operator is used to create subtotals and grand totals for a set of columns. The summarized totals are created based on the columns passed to the ROLLUP operator.

The **CUBE** operator produces subtotals and grand totals. In addition, it produces subtotals and grand totals for every permutation of the columns provided to the CUBE operator.

---

### 1. Write a Query Using Grouping Sets

Create a grouping set for three columns labeled `year`, `category`, and the sum of `billedamount`.

```sql
select year, category, sum(billedamount) as totalbilledamount
from "FactBilling"
left join "DimCustomer"
on "FactBilling".customerid = "DimCustomer".customerid
left join "DimMonth"
on "FactBilling".monthid="DimMonth".monthid
group by grouping sets(year, category);
```

---

### 2. Write a Query Using RollUp

Create a rollup using the three columns `year`, `category`, and the sum of `billedamount`.

```sql
select year, category, sum(billedamount) as totalbilledamount
from "FactBilling"
left join "DimCustomer"
on "FactBilling".customerid = "DimCustomer".customerid
left join "DimMonth"
on "FactBilling".monthid="DimMonth".monthid
group by rollup(year, category)
order by year, category;
```

---

### 3. Write a Query Using Cube

Create a cube using the three columns labeled `year`, `category`, and the sum of `billedamount`.

```sql
select year, category, sum(billedamount) as totalbilledamount
from "FactBilling"
left join "DimCustomer"
on "FactBilling".customerid = "DimCustomer".customerid
left join "DimMonth"
on "FactBilling".monthid="DimMonth".monthid
group by cube(year, category)
order by year, category;
```

---

### 4. Create a Materialized Query Table

#### Step 1: Create the Materialized Views

Execute the SQL statement below to create a materialized view named `countrystats`.

```sql
CREATE MATERIALIZED VIEW countrystats (country, year, totalbilledamount) AS
(select country, year, sum(billedamount)
from "FactBilling"
left join "DimCustomer"
on "FactBilling".customerid = "DimCustomer".customerid
left join "DimMonth"
on "FactBilling".monthid="DimMonth".monthid
group by country, year);
```

Another SQL query to achieve similar functionality of the materialized view above, obtaining the `year`, `quartername`, and the sum of `billedamount` grouped by `year` and `quartername`:

```sql
select year, quartername, sum(billedamount) as totalbilledamount
from "FactBilling"
left join "DimCustomer"
on "FactBilling".customerid = "DimCustomer".customerid
left join "DimMonth"
on "FactBilling".monthid="DimMonth".monthid
group by grouping sets(year, quartername);
```

#### Step 2: Populate/Refresh Data into the Materialized Views

Execute the SQL statement below to populate the materialized view `countrystats`.

```sql
REFRESH MATERIALIZED VIEW countrystats;
```

The command above populates the materialized view with relevant data.

#### Step 3: Query the Materialized Views

Once a materialized view is refreshed, you can query it.

Execute the SQL statement below to query the materialized view `countrystats`.

```sql
select * from countrystats;
```

---

## Exercises

### Problem 1: Create a Rollup for the Columns `year`, `quartername`, and the Sum of `billedamount`

```sql
SELECT country, category, SUM(billedamount) AS totalbilledamount
FROM "FactBilling"
LEFT JOIN "DimCustomer"
ON "FactBilling".customerid = "DimCustomer".customerid
LEFT JOIN "DimMonth"
ON "FactBilling".monthid = "DimMonth".monthid
GROUP BY ROLLUP(country, category)
ORDER BY country, category;
```

---

### Problem 2: Create a Cube for the Columns `year`, `country`, `category`, and the Sum of `billedamount`

```sql
SELECT year, category, country, SUM(billedamount) AS totalbilledamount
FROM "FactBilling"
LEFT JOIN "DimCustomer"
ON "FactBilling".customerid = "DimCustomer".customerid
LEFT JOIN "DimMonth"
ON "FactBilling".monthid = "DimMonth".monthid
GROUP BY CUBE(year, country, category);
```

---

### Problem 3: Create a Materialized View Named `average_billamount`

The view should include the columns `year`, `quarter`, `category`, `country`, and `average_bill_amount`.

```sql
CREATE MATERIALIZED VIEW as average_billamount (year, quarter, category, country, average_bill_amount) AS 
(SELECT year, quarter, category, country, avg(billamount))
FROM "FactBilling"
LEFT JOIN "DimCustomer" 
ON "FactBilling".customerid = "DimCustomer".customerid
LEFT JOIN "DimMonth" 
ON "FactBilling".monthid = "DimMonth".monthid
GROUP BY (year, quarter, country, category)

REFRESH MATERIALIZED VIEW average_billamount;
``` 