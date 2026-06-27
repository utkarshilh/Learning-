# AWS Glue Crawlers - Notes

## What is a Glue Crawler?

An AWS Glue Crawler is a service that automatically scans a data source (such as Amazon S3), detects the schema, and creates or updates metadata in the AWS Glue Data Catalog.

> **Important:** A crawler does **not** modify the actual data. It only creates metadata.

---

# Crawler Workflow

```text
S3 Bucket
     │
     ▼
Glue Crawler
     │
     ▼
Infer Schema
     │
     ▼
Glue Data Catalog
     │
     ▼
Athena Queries
```

---

# What Does a Crawler Do?

- Reads files from the data source.
- Detects the file format (CSV, JSON, Parquet, ORC, etc.).
- Infers column names and data types.
- Creates or updates tables in the Glue Data Catalog.
- Detects partitions.

---

# Supported Data Sources

- Amazon S3
- Amazon RDS
- Amazon Redshift
- JDBC Databases
- DynamoDB
- Delta Lake / Iceberg / Hudi (supported configurations)

---

# Components of a Crawler

## 1. Data Source

The location where data exists.

Example:

```text
s3://my-bucket/data/
```

---

## 2. IAM Role

The crawler uses an IAM role to access the data source.

Minimum required S3 permissions:

```text
s3:GetObject
s3:ListBucket
```

---

## 3. Database

The Glue database where tables will be created.

Example:

```text
Database Name:
sales_db
```

---

## 4. Schedule

Crawler execution can be:

- On Demand
- Hourly
- Daily
- Weekly
- Cron Expression

---

# Glue Data Catalog

The Glue Data Catalog stores **metadata**, not actual data.

It contains:

- Database
- Table Name
- Columns
- Data Types
- S3 Location
- Partitions

---

# Schema Inference

Example CSV

```csv
id,name,salary
1,John,1000
```

Crawler creates

| Column | Data Type |
|---------|-----------|
| id | bigint |
| name | string |
| salary | bigint |

---

# Crawling Multiple Files

Example

```text
employees/

employee_day1.csv
employee_day2.csv
employee_day3.csv
```

If schemas are identical,

Crawler creates:

```text
One Table
```

---

# Hive Style Partitioning

Folder Structure

```text
sales/

year=2025/
    file1.csv

year=2026/
    file2.csv
```

Notice that **year is not inside the CSV**.

---

# Partition Column

Glue automatically creates

```text
year
```

as a partition column.

CSV

```csv
id,sales
1,100
```

Athena Table

| id | sales | year |
|----|-------|------|
| 1 | 100 | 2025 |

The value comes from the folder name.

---

# Where Does the Partition Value Come From?

Folder

```text
year=2025/
```

becomes

```text
Partition Key

year = 2025
```

No additional column is required inside the CSV.

---

# Partition Metadata

Glue stores

```text
year=2025
↓

s3://bucket/year=2025/
```

```text
year=2026
↓

s3://bucket/year=2026/
```

Athena reads this metadata instead of scanning every folder.

---

# Partition Pruning

Query

```sql
SELECT *
FROM sales
WHERE year='2025';
```

Athena reads only

```text
✔ year=2025/
```

It skips

```text
✘ year=2026/
```

### Benefits

- Faster queries
- Lower Athena cost
- Less data scanned

---

# S3 Folder vs Object

Object

```text
year=2025
```

Folder (Prefix)

```text
year=2025/
```

Although they look similar, they are different.

Uploading a file named

```text
year=2025
```

can confuse the crawler and create multiple tables.

---

# Common Errors

## AccessDenied

Cause

```
Missing IAM Permission
```

Solution

Grant

```text
s3:GetObject
s3:ListBucket
```

---

## No Table Created

Possible Reasons

- Wrong IAM Role
- Incorrect S3 Path
- Unsupported File Format

---

## Multiple Tables Created

Possible Reasons

- Incorrect S3 structure
- Standalone objects mixed with partition folders
- Different schemas

Correct Structure

```text
bucket/

year=2025/file1.csv

year=2026/file2.csv
```

---

## Crawler Succeeded but No Table

Check

- IAM Role
- CloudWatch Logs
- Target Database
- S3 Path
- Supported File Format

---

# Best Practices

- Keep files with identical schemas together.
- Use Hive-style partitioning (`column=value/`).
- Use meaningful Glue database names.
- Grant only required IAM permissions.
- Store data in Parquet for better Athena performance.
- Use partitions for large datasets.

---

# Interview Questions

## What is a Glue Crawler?

A Glue Crawler scans a data source, infers the schema, and creates or updates metadata in the Glue Data Catalog.

---

## Does a crawler modify the data?

No.

It only creates or updates metadata.

---

## What is stored in the Glue Data Catalog?

- Databases
- Tables
- Columns
- Data Types
- S3 Locations
- Partitions

---

## Why are partitions important?

Partitions reduce the amount of data Athena scans, making queries faster and more cost-effective.

---

## Where does the partition column come from?

From the folder structure.

Example

```text
year=2025/
```

creates the partition

```text
year = 2025
```

---

## Why isn't the partition column inside the CSV?

Because Glue extracts the partition value from the folder name and stores it as metadata in the Glue Data Catalog.

---

# Lab Summary

✔ Created Glue Crawlers

✔ Created Glue Database

✔ Configured IAM Role

✔ Crawled CSV files from S3

✔ Generated Glue Catalog Tables

✔ Queried data using Athena

✔ Created Hive-style partitions

✔ Verified partition pruning

✔ Debugged IAM and crawler issues

---

# Next Topic

AWS Glue ETL Jobs (PySpark)

- Read data from S3
- Transform data
- Filter records
- Change data types
- Write Parquet
- Incremental processing
- Job Bookmarks
