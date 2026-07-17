# AWS Glue Handbook

# 00. Introduction

---

# What is AWS Glue?

AWS Glue is a **fully managed serverless data integration service** provided by Amazon Web Services (AWS). It is designed to discover, prepare, transform, and move data between different storage systems for analytics, machine learning, and reporting.

Unlike traditional ETL tools, AWS Glue does **not require you to provision or manage servers**. AWS automatically provisions the required infrastructure, executes your ETL workloads, and scales resources as needed.

In simple words,

> AWS Glue allows Data Engineers to build ETL (Extract, Transform, Load) pipelines without managing infrastructure.

---

# Why was AWS Glue created?

Imagine a company stores data in multiple places:

- Sales data in Amazon S3
- Customer data in MySQL
- Finance data in Oracle
- Logs in Amazon RDS
- Marketing data in Redshift

Before Glue existed, engineers had to:

- Write custom ETL scripts
- Schedule cron jobs
- Manage EC2 servers
- Handle scaling
- Maintain metadata manually

AWS Glue automates almost all of these activities.

---

# Real-world Example

Suppose Apple receives sales reports every night.

Every night,

```
sales_2026_01.csv
sales_2026_02.csv
sales_2026_03.csv
```

are uploaded to Amazon S3.

The company wants to:

- Read the files
- Remove bad records
- Convert data types
- Merge with historical data
- Store cleaned data in Parquet
- Query using Athena

Without Glue,

an engineer would have to write the complete ETL pipeline manually.

With Glue,

AWS provides services that automate most of these tasks.

---

# What problems does Glue solve?

AWS Glue helps solve the following problems.

## Problem 1

Different systems store data differently.

Example:

```
Oracle
↓

Customer_ID

MySQL
↓

customerId

CSV
↓

cust_id
```

Glue can transform them into a common format.

---

## Problem 2

Finding schema manually

Imagine receiving

```
employee.csv
```

containing 250 columns.

Instead of manually checking each column,

Glue Crawlers automatically detect

- Column names
- Datatypes
- Partitions

and store them in the Glue Data Catalog.

---

## Problem 3

Scheduling ETL pipelines

Instead of manually running jobs,

Glue can execute pipelines

- Every hour
- Every day
- Every month
- On S3 upload
- Using EventBridge
- Using Lambda

---

## Problem 4

Metadata Management

Instead of storing metadata inside applications,

Glue maintains a centralized

**Data Catalog**

which can be shared by

- Athena
- EMR
- Redshift Spectrum
- Glue Jobs

---

# Core Components of AWS Glue

AWS Glue consists of multiple services.

```
                    AWS Glue

                       │

 ┌─────────────────────────────────────┐
 │                                     │
 │   Crawlers                          │
 │   Data Catalog                      │
 │   ETL Jobs                          │
 │   Visual ETL                        │
 │   Workflows                         │
 │   Triggers                          │
 │   DataBrew                          │
 │   Data Quality                      │
 └─────────────────────────────────────┘
```

Each component performs a specific task.

We will learn every one of them in this handbook.

---

# AWS Glue Architecture Overview

```
               Source Systems

            Oracle   MySQL   S3

                    │

                    ▼

              AWS Glue Crawler

                    │

                    ▼

             AWS Glue Data Catalog

                    │

                    ▼

              AWS Glue ETL Jobs

                    │

         ┌──────────┴───────────┐

         ▼                      ▼

      Amazon S3             Redshift

         ▼                      ▼

       Athena              QuickSight
```

This architecture is sufficient for understanding Glue at a beginner level.

A detailed architecture will be covered in the next chapter.

---

# ETL Explained

AWS Glue is fundamentally an ETL service.

## Extract

Read data from

- Amazon S3
- MySQL
- PostgreSQL
- Oracle
- SQL Server
- Redshift

---

## Transform

Clean and process data.

Examples

- Remove NULL values
- Rename columns
- Change datatype
- Join datasets
- Remove duplicates
- Aggregate data

---

## Load

Write processed data to

- Amazon S3
- Redshift
- JDBC databases
- Iceberg tables
- Delta Lake

---

# Serverless Nature of Glue

One of the biggest advantages of Glue is that it is **Serverless**.

This means

You never launch

- EC2 Instances
- Virtual Machines
- Clusters

AWS automatically

- Creates compute resources
- Executes your ETL
- Scales resources
- Stops compute after execution

You only pay for the execution time.

---

# AWS Glue Ecosystem

AWS Glue does not work alone.

It integrates with many AWS services.

```
Amazon S3
      │
      ▼
Glue Crawlers
      │
      ▼
Glue Data Catalog
      │
      ▼
Glue ETL Jobs
      │
      ▼
Athena
      │
      ▼
QuickSight
```

It can also integrate with

- Lambda
- EventBridge
- CloudWatch
- IAM
- Lake Formation
- Redshift

---

# Skills You Will Learn

By the end of this handbook you will know

✓ Crawlers

✓ Data Catalog

✓ Glue Jobs

✓ Visual ETL

✓ Incremental Loading

✓ Scheduling

✓ EventBridge

✓ Lambda Trigger

✓ Workflows

✓ DataBrew

✓ Data Quality

✓ PySpark Jobs

✓ Performance Optimization

---

# Prerequisites

Basic understanding of

- SQL
- Amazon S3
- IAM
- CSV Files

is sufficient.

No prior Glue knowledge is required.

---

# Learning Approach

This handbook follows the same approach used during our practical sessions.

For every topic we will cover

1. Theory

2. Internal Working

3. AWS Console Walkthrough

4. Hands-on Exercise

5. Production Use Case

6. Best Practices

7. Common Mistakes

8. Interview Questions

9. Revision Summary

This ensures both conceptual understanding and practical knowledge.

---

# Summary

AWS Glue is a fully managed serverless ETL platform that enables organizations to discover, catalog, transform, orchestrate, and prepare data for analytics without managing infrastructure.

It acts as the backbone of modern AWS-based data engineering pipelines.

---

# Next Chapter

01_Glue_Architecture.md

In the next chapter, we will understand the complete internal architecture of AWS Glue and how all Glue services interact with each other.



# AWS Glue Handbook

# 01. AWS Glue Architecture

---

# Introduction

Before learning individual AWS Glue services like Crawlers, Jobs, Data Catalog, and Workflows, it is important to understand how they fit together.

Many beginners learn Glue services independently without understanding how data flows through the entire system.

A Data Engineer, however, always thinks in terms of an architecture rather than individual services.

This chapter explains how AWS Glue works internally and how different AWS services interact with one another.

---

# What is AWS Glue Architecture?

AWS Glue Architecture is the overall workflow that moves raw data from different data sources, transforms it, stores metadata, and finally makes it available for analytics.

Instead of viewing Glue as a single service, think of it as a collection of specialized services working together.

```
               AWS Glue Ecosystem

          Source Systems
────────────────────────────────────

Amazon S3

MySQL

PostgreSQL

Oracle

SQL Server

Redshift

JDBC Sources

────────────────────────────────────
              │
              ▼

        AWS Glue Crawlers

              │
              ▼

      AWS Glue Data Catalog

              │
              ▼

         AWS Glue Jobs

              │
              ▼

        Processed Data

              │
              ▼

Amazon S3

Redshift

Iceberg Tables

Delta Tables

              │
              ▼

Athena

QuickSight

Machine Learning

Business Intelligence
```

---

# High-Level Architecture

The complete architecture can be divided into six stages.

```
Source

↓

Discovery

↓

Catalog

↓

Transformation

↓

Storage

↓

Analytics
```

Each stage performs a specific task.

---

# Stage 1 — Source Layer

This is where data originates.

AWS Glue can connect to many different systems.

Examples

```
Amazon S3

Oracle Database

MySQL

PostgreSQL

Amazon RDS

SQL Server

Amazon Redshift

MongoDB

JDBC Databases
```

Example

Imagine an e-commerce company.

```
Orders

↓

MySQL

Customers

↓

Oracle

Website Logs

↓

Amazon S3

Payments

↓

PostgreSQL
```

All of these become data sources for Glue.

---

# Stage 2 — Discovery Layer

Discovery is performed by

```
Glue Crawlers
```

The crawler scans your source.

Example

```
employees.csv

employee_id

employee_name

salary

city
```

Crawler automatically detects

- Column Names

- Data Types

- Partitions

- File Format

No manual schema creation is required.

---

# Stage 3 — Metadata Layer

After scanning,

Crawler stores metadata inside

```
Glue Data Catalog
```

Important

The Data Catalog **does not store the actual data.**

It only stores information about the data.

Example

```
Table Name

employees

Location

s3://company-data/employees/

Columns

employee_id

employee_name

salary

city

Format

CSV
```

Notice

Only metadata is stored.

The CSV file remains inside S3.

---

# Stage 4 — Transformation Layer

This is where Glue Jobs operate.

Glue Job reads

```
Raw Data
```

performs

```
Transformations
```

and writes

```
Processed Data
```

Example

Before

```
Alice

50000

Bangalore
```

After

```
ALICE

50000

Bangalore
```

or

```
salary

String

↓

Integer
```

Glue supports

- Visual ETL

- PySpark

- Scala

---

# Stage 5 — Storage Layer

After processing,

Glue stores cleaned data.

Usually

```
Amazon S3
```

or

```
Amazon Redshift
```

Most companies use

```
CSV

↓

Parquet
```

because

Parquet

- is compressed

- is columnar

- is much faster

- reduces Athena cost

---

# Stage 6 — Analytics Layer

Now the cleaned data becomes available.

Examples

```
Athena

QuickSight

EMR

Machine Learning

SageMaker
```

Business users never access raw data.

They only access processed data.

---

# Complete Glue Architecture

```
                  RAW DATA

         CSV

         JSON

         Parquet

         Databases

                │

                ▼

          Glue Crawlers

                │

                ▼

       Glue Data Catalog

                │

                ▼

         Glue ETL Jobs

                │

        ┌───────┴────────┐

        ▼                ▼

     Amazon S3       Redshift

        ▼                ▼

     Athena        QuickSight

        ▼

     Business Users
```

---

# AWS Services Used Alongside Glue

Glue rarely works alone.

Most production architectures include

```
Amazon S3

IAM

CloudWatch

Lambda

EventBridge

Lake Formation

Athena

QuickSight

Redshift
```

Each service contributes a specific capability.

---

# Where Does IAM Fit?

IAM controls

- Who can run Crawlers

- Who can run Jobs

- Which S3 buckets Glue can read

- Which buckets Glue can write

Without IAM,

Glue cannot access your data.

---

# Where Does CloudWatch Fit?

CloudWatch stores

```
Job Logs

Crawler Logs

Error Messages

Execution Time

Metrics
```

Whenever a Glue Job fails,

CloudWatch is the first place engineers investigate.

---

# Where Does EventBridge Fit?

EventBridge allows event-driven architectures.

Example

```
New CSV Uploaded

↓

EventBridge

↓

Glue Workflow

↓

Glue Job

↓

Clean Data Generated
```

No manual execution required.

---

# Where Does Lambda Fit?

Lambda provides custom logic.

Example

```
CSV Uploaded

↓

Lambda

↓

Validate File Name

↓

Start Glue Job

↓

Send SNS Notification
```

Lambda acts as an intelligent controller.

---

# Where Do Workflows Fit?

Workflows orchestrate multiple jobs.

Example

```
Workflow

↓

Crawler

↓

Job A

↓

Job B

↓

Job C
```

Each task starts only after the previous task succeeds.

---

# Typical Production Architecture

```
                 Amazon S3

              Raw Files

                   │

                   ▼

             Glue Crawler

                   │

                   ▼

          Glue Data Catalog

                   │

                   ▼

           Glue Workflow

                   │

         ┌─────────┴──────────┐

         ▼                    ▼

      Job A                Job B

         │                    │

         └─────────┬──────────┘

                   ▼

             Cleaned Data

                   │

                   ▼

               Athena

                   │

                   ▼

             QuickSight Dashboard
```

This is one of the most common AWS analytics architectures.

---

# Architecture Used During Our Practice

Our hands-on sessions roughly followed this flow.

```
CSV

↓

Amazon S3

↓

Crawler

↓

Data Catalog

↓

Athena

↓

Visual ETL

↓

Glue Job

↓

Scheduling

↓

EventBridge

↓

Lambda

↓

Workflow

↓

DataBrew
```

Each service was learned after understanding the previous one.

---

# Advantages of AWS Glue Architecture

✓ Fully Managed

✓ Serverless

✓ Highly Scalable

✓ Metadata Centralization

✓ Event-Driven

✓ Pay-as-you-go

✓ Easy Integration

✓ Visual + Code Support

---

# Limitations

Glue is not suitable for every workload.

Examples

- Extremely low-latency streaming

- High-frequency transactional systems

- Interactive applications

Glue is designed primarily for

Batch ETL

Analytics

Data Warehousing

Data Lakes

---

# Interview Questions

## Q1. Why is Glue Data Catalog placed between Crawlers and Glue Jobs?

Because Crawlers discover metadata and store it inside the Data Catalog.

Glue Jobs later use this metadata instead of manually defining schemas.

---

## Q2. Does Glue Data Catalog store actual data?

No.

It stores only metadata.

Actual data remains inside S3 or the original data source.

---

## Q3. Which AWS service usually triggers a Glue Workflow automatically?

Amazon EventBridge.

---

## Q4. Which AWS service stores Glue Job logs?

Amazon CloudWatch.

---

## Q5. Which AWS service controls Glue permissions?

AWS IAM.

---

# Best Practices

• Keep raw and processed data in separate S3 locations.

• Use Crawlers only when schema changes are expected.

• Use Parquet instead of CSV for analytics.

• Monitor Glue Jobs using CloudWatch.

• Use EventBridge for automation.

• Use Workflows for dependency management.

• Store metadata in the Glue Data Catalog instead of hardcoding schemas.

---

# Revision Summary

AWS Glue Architecture can be summarized in one line:

```
Data Source

↓

Crawler

↓

Data Catalog

↓

Glue Job

↓

Processed Data

↓

Athena / Redshift / QuickSight
```

Always remember this flow.

Every AWS Glue service that you learn later fits somewhere inside this architecture.

---

# Next Chapter

02_Glue_Crawlers.md

In the next chapter, we will perform a deep dive into AWS Glue Crawlers, including their internal working, configuration options, partition discovery, scheduling, production use cases, and the hands-on exercises we completed.



# AWS Glue Handbook

# 02.1 Introduction to AWS Glue Crawlers

## What is a Glue Crawler?

An AWS Glue Crawler is a serverless component that scans a data source,
discovers its schema, and stores the metadata in the AWS Glue Data
Catalog.

The crawler **does not move or modify your data**. It only reads the
data to understand its structure.

------------------------------------------------------------------------

## Why Do We Need Crawlers?

Without a crawler, a data engineer would have to:

-   Create tables manually
-   Define every column manually
-   Specify data types manually
-   Update schemas whenever files change

With a crawler:

-   Schema is detected automatically
-   Partitions are discovered
-   Tables are created automatically
-   Metadata stays synchronized

------------------------------------------------------------------------

## Architecture

``` text
Amazon S3 / JDBC Database
            │
            ▼
     AWS Glue Crawler
            │
            ▼
    AWS Glue Data Catalog
            │
            ▼
Athena / Glue Jobs / EMR
```

------------------------------------------------------------------------

## How a Crawler Works

1.  Connects to a data source.
2.  Reads sample files.
3.  Detects the file format.
4.  Infers column names and data types.
5.  Detects partitions (if present).
6.  Creates or updates tables in the Glue Data Catalog.

------------------------------------------------------------------------

## Supported Data Sources

-   Amazon S3
-   Amazon RDS
-   MySQL
-   PostgreSQL
-   Oracle
-   SQL Server
-   Amazon Redshift
-   Other JDBC-compatible databases

------------------------------------------------------------------------

## Supported File Formats

-   CSV
-   JSON
-   Parquet
-   ORC
-   Avro

------------------------------------------------------------------------

## What Does the Crawler Create?

The crawler creates metadata such as:

-   Database
-   Table
-   Columns
-   Data types
-   File format
-   Partition information
-   S3 location

Example:

``` text
Database : employee_db
Table    : employees
Location : s3://company-data/employees/
Columns  :
    employee_id (int)
    employee_name (string)
    salary (double)
```

------------------------------------------------------------------------

## Important Concept

### Data vs Metadata

**Actual Data**

Stored in Amazon S3.

``` text
employee.csv
```

**Metadata**

Stored in Glue Data Catalog.

``` text
Table Name
Columns
Datatypes
Partitions
Location
```

The crawler never copies the data.

------------------------------------------------------------------------

## Where Does a Crawler Fit?

``` text
Raw CSV
   │
   ▼
Crawler
   │
   ▼
Glue Data Catalog
   │
   ▼
Athena / Glue Jobs
```

------------------------------------------------------------------------

## Advantages

-   Serverless
-   Automatic schema discovery
-   Partition detection
-   Reduces manual work
-   Integrates with AWS analytics services

------------------------------------------------------------------------

## Limitations

-   Does not transform data
-   Does not clean data
-   Cannot replace Glue Jobs
-   Accuracy depends on sampled files

------------------------------------------------------------------------

## Hands-on We Performed

During our practice we:

-   Created an S3 bucket.
-   Uploaded CSV files.
-   Created a Glue Crawler.
-   Ran the crawler.
-   Observed automatic table creation.
-   Queried the table using Athena.
-   Learned that folder names such as:

``` text
year=2025/month=01/
```

become partition columns automatically.

------------------------------------------------------------------------

## Production Use Case

Every night:

``` text
Sales CSV
      │
      ▼
Amazon S3
      │
      ▼
Glue Crawler
      │
      ▼
Glue Catalog
      │
      ▼
Glue ETL Job
      │
      ▼
Athena Dashboard
```

------------------------------------------------------------------------

## Best Practices

-   Separate raw and processed data.
-   Re-run crawlers only when schema changes.
-   Use meaningful database names.
-   Partition large datasets.

------------------------------------------------------------------------

## Common Mistakes

-   Expecting the crawler to clean data.
-   Confusing metadata with actual data.
-   Forgetting IAM permissions.
-   Assuming crawlers modify S3 files.

------------------------------------------------------------------------

## Interview Questions

**Q. Does a crawler move data?**

No. It only discovers metadata.

**Q. Where is the schema stored?**

AWS Glue Data Catalog.

**Q. Can a crawler detect partitions?**

Yes.

**Q. Does a crawler execute ETL logic?**

No. ETL is performed by Glue Jobs.

------------------------------------------------------------------------

## Revision Summary

``` text
Data Source
      │
      ▼
Glue Crawler
      │
      ▼
Glue Data Catalog
      │
      ▼
Athena / Glue Jobs
```

### Key Points

-   Crawler discovers schema.
-   Catalog stores metadata.
-   Data remains in the source.
-   Jobs perform transformations.


# AWS Glue Handbook

# 02.2 Why Crawlers Exist

---

# Introduction

One of the first questions every beginner asks is:

> **"Why do we even need AWS Glue Crawlers? Can't we just create the tables ourselves?"**

The answer is **Yes, you can.**

However, when working with real-world datasets containing hundreds or thousands of files, manually creating and maintaining metadata becomes extremely difficult.

AWS Glue Crawlers automate this entire process.

---

# The Problem Without Crawlers

Imagine your company stores sales data in Amazon S3.

```text
sales-data/

sales_2026_01.csv
sales_2026_02.csv
sales_2026_03.csv
sales_2026_04.csv
```

Every month a new file arrives.

If you don't use a crawler, you must manually:

- Create a table.
- Define every column.
- Specify each data type.
- Update the schema whenever the file structure changes.
- Add new partitions manually.

This quickly becomes time-consuming and error-prone.

---

# The Solution

Instead of manually maintaining metadata, AWS Glue Crawlers automatically:

- Scan the data source.
- Detect the schema.
- Identify data types.
- Detect partitions.
- Create or update Glue Catalog tables.

```text
Amazon S3

      │

      ▼

Glue Crawler

      │

      ▼

Glue Data Catalog
```

---

# Real-World Example

Suppose an HR department uploads a new employee file every day.

```text
employees/

employee_01.csv

employee_02.csv

employee_03.csv

employee_04.csv
```

Without a crawler:

Every time a new file arrives, a Data Engineer must verify:

- Are the columns the same?
- Has a new column been added?
- Has a datatype changed?
- Should the Glue table be updated?

With a crawler:

```text
New File

      │

      ▼

Crawler

      │

      ▼

Metadata Updated Automatically
```

---

# Problems Solved by Crawlers

## 1. Automatic Schema Discovery

Instead of defining:

```text
employee_id      INT
employee_name    STRING
salary           DOUBLE
department       STRING
```

Glue discovers them automatically.

---

## 2. Automatic Table Creation

Instead of creating:

```text
employees
customers
sales
orders
```

manually,

the crawler creates them automatically in the Glue Data Catalog.

---

## 3. Automatic Partition Discovery

Suppose your S3 bucket looks like this:

```text
sales/

year=2025/

month=01/

month=02/

year=2026/

month=01/
```

The crawler automatically creates:

```text
year

month
```

as partition columns.

No manual work is required.

---

## 4. Schema Evolution

Imagine today's file contains

```text
employee_id

employee_name

salary
```

Tomorrow's file contains

```text
employee_id

employee_name

salary

department
```

The crawler can detect the new column and update the Glue Catalog accordingly (based on crawler configuration).

---

## 5. Centralized Metadata

Without Glue:

Each application stores its own schema.

With Glue:

```text
Athena

      │

Glue Data Catalog

      │

Glue Jobs

      │

EMR
```

Everyone uses the same metadata.

---

# What Happens If We Don't Use Crawlers?

You would need to manually:

- Create databases.
- Create tables.
- Maintain schemas.
- Detect datatype changes.
- Add partitions.
- Keep metadata synchronized.

This may work for a few files but becomes impractical at scale.

---

# Manual vs Automatic

| Manual Process | Using Glue Crawler |
|---------------|--------------------|
| Create tables manually | Tables created automatically |
| Define schema manually | Schema discovered automatically |
| Add partitions manually | Partitions detected automatically |
| Update schema manually | Schema updated automatically |
| Time consuming | Automated |

---

# Small Company vs Large Company

## Small Company

```text
10 CSV Files
```

Manual metadata management may be acceptable.

---

## Large Enterprise

```text
Millions of Files

Thousands of Tables

Hundreds of Databases
```

Manual management becomes impossible.

This is why enterprises rely on Glue Crawlers.

---

# Production Scenario

Imagine a retail company receives daily sales data.

```text
Daily Sales File

        │

        ▼

Amazon S3

        │

        ▼

Glue Crawler

        │

        ▼

Glue Catalog Updated

        │

        ▼

Glue ETL Job

        │

        ▼

Athena Dashboard
```

Everything happens automatically.

---

# During Our Practice

While learning AWS Glue, we:

- Uploaded CSV files to S3.
- Created a Glue Crawler.
- Ran the crawler.
- Observed automatic table creation.
- Used Athena to query the generated table.
- Learned that partition folders become partition columns automatically.

This demonstrated exactly why Crawlers are useful.

---

# When Should You Use a Crawler?

Use a crawler when:

- New files arrive regularly.
- Schema may change.
- Multiple AWS services need shared metadata.
- You want to avoid manual schema management.

---

# When Might You Skip a Crawler?

Sometimes organizations already know the schema.

For example:

- Fixed database schema.
- Stable Parquet datasets.
- Infrastructure managed through Terraform or CloudFormation.

In these cases, engineers may create Glue tables manually.

---

# Best Practices

- Run crawlers only when needed.
- Keep raw and processed data separate.
- Use partitioned folders for large datasets.
- Review schema changes before using them in production.
- Give crawlers meaningful names.

---

# Common Misconceptions

### "Crawler cleans my data."

❌ Incorrect.

Crawler only discovers metadata.

---

### "Crawler moves files."

❌ Incorrect.

Crawler never moves or modifies data.

---

### "Crawler performs ETL."

❌ Incorrect.

ETL is performed by Glue Jobs.

---

### "Crawler stores the data."

❌ Incorrect.

The actual data remains in Amazon S3 or the original data source.

---

# Interview Questions

### Why do we need Glue Crawlers?

To automatically discover schemas and maintain metadata in the Glue Data Catalog.

---

### What is the biggest advantage of a Crawler?

Automatic schema discovery and metadata management.

---

### Can a Crawler replace a Glue Job?

No.

Crawler discovers metadata.

Glue Jobs transform data.

---

### Can a Crawler detect partitions?

Yes.

It automatically detects partition folders and stores them in the Data Catalog.

---

# Revision Summary

## Why Crawlers Exist

```text
Without Crawler

Data Source

↓

Manual Schema

↓

Manual Table

↓

Manual Updates

↓

High Maintenance
```

```text
With Crawler

Data Source

↓

Glue Crawler

↓

Automatic Schema Discovery

↓

Glue Data Catalog

↓

Athena / Glue Jobs
```

---

# Key Takeaways

- Crawlers exist to automate metadata management.
- They reduce manual effort.
- They discover schemas and partitions automatically.
- They keep the Glue Data Catalog synchronized.
- They do **not** clean, transform, or move data.

---

# Next Section

**02.3_Internal_Working.md**

In the next section, we'll explore **what happens internally when you click "Run Crawler"**, including how Glue samples data, infers schemas, detects partitions, and updates the Data Catalog.



# AWS Glue Handbook

# 02.3 Internal Working of AWS Glue Crawlers

---

# Introduction

So far, we know that a Glue Crawler scans a data source and creates metadata in the Glue Data Catalog.

But **what actually happens internally** when we click **Run Crawler**?

Understanding the internal workflow is important because interviewers often ask:

> **"Explain how a Glue Crawler works internally."**

This chapter answers that question.

---

# High-Level Workflow

When a crawler starts, it performs a series of operations.

```text
Data Source
      │
      ▼
Connect to Source
      │
      ▼
Read Sample Data
      │
      ▼
Detect File Format
      │
      ▼
Infer Schema
      │
      ▼
Detect Partitions
      │
      ▼
Compare Existing Metadata
      │
      ▼
Create/Update Glue Catalog
```

---

# Step 1 – Connect to the Data Source

The first task of the crawler is to connect to the source location.

The source can be:

- Amazon S3
- MySQL
- PostgreSQL
- Oracle
- SQL Server
- Amazon Redshift
- JDBC Database

Example:

```text
s3://employee-data/
```

The crawler establishes a connection using the IAM role assigned to it.

If the IAM role lacks permission, the crawler fails immediately.

---

# Step 2 – Scan the Files

After connecting successfully, the crawler begins scanning the files.

Example:

```text
employee-data/

employee1.csv

employee2.csv

employee3.csv
```

The crawler reads enough data to understand the dataset.

It **does not load the complete dataset into memory**.

Instead, it samples the files.

This makes crawling much faster.

---

# Step 3 – Detect the File Format

Next, the crawler identifies the file format.

Example:

```text
employee.csv
```

↓

CSV

or

```text
employee.parquet
```

↓

Parquet

Supported formats include:

- CSV
- JSON
- Parquet
- ORC
- Avro

Glue uses built-in classifiers to identify these formats automatically.

---

# Step 4 – Infer the Schema

Now the crawler examines the file contents.

Example:

```text
employee_id,employee_name,salary

1,Alice,50000

2,Bob,62000
```

It automatically determines:

| Column | Data Type |
|----------|-----------|
| employee_id | Integer |
| employee_name | String |
| salary | Double |

This process is called **Schema Inference**.

---

# Step 5 – Detect Partitions

Suppose the folder structure is:

```text
sales/

year=2025/

month=01/

sales.csv
```

The crawler automatically recognizes:

```text
year

month
```

as partition columns.

These partitions are stored in the Data Catalog.

This makes Athena queries much faster because only relevant partitions are scanned.

---

# Step 6 – Compare with Existing Metadata

If this is the first crawl,

↓

A new table is created.

If the table already exists,

↓

The crawler compares:

- Existing columns
- New columns
- Data types
- Partitions

Depending on the crawler configuration, it may:

- Update the table
- Ignore changes
- Add new partitions

---

# Step 7 – Update the Glue Data Catalog

Finally, the crawler writes the discovered metadata.

Example:

```text
Database

employee_db

Table

employees

Columns

employee_id

employee_name

salary

Location

s3://employee-data/
```

Notice that **only metadata is stored**.

The original files remain untouched.

---

# Complete Internal Workflow

```text
Amazon S3

      │

      ▼

Connect

      │

      ▼

Scan Files

      │

      ▼

Detect Format

      │

      ▼

Infer Schema

      │

      ▼

Detect Partitions

      │

      ▼

Compare Existing Metadata

      │

      ▼

Update Glue Data Catalog
```

---

# What Does the Crawler NOT Do?

Many beginners misunderstand the role of a crawler.

A crawler **does not**:

❌ Transform data

❌ Clean data

❌ Remove duplicates

❌ Convert CSV to Parquet

❌ Execute SQL

❌ Perform ETL

These tasks are performed by **Glue Jobs**.

---

# Example from Our Practice

During our hands-on exercise:

We uploaded CSV files to S3.

```text
employees.csv
```

↓

Created a crawler.

↓

Ran the crawler.

↓

The crawler:

- Connected to S3
- Detected CSV format
- Identified columns
- Created a table in the Glue Data Catalog

↓

Athena was then able to query the table.

This demonstrated the complete internal flow.

---

# What Happens If the Schema Changes?

Suppose today's file contains:

```text
employee_id

employee_name

salary
```

Tomorrow's file contains:

```text
employee_id

employee_name

salary

department
```

The crawler detects the new column.

Based on its configuration, it can update the Glue Catalog table automatically.

This process is known as **Schema Evolution**.

---

# What Happens If IAM Permissions Are Missing?

Suppose the crawler has no permission to access S3.

Workflow:

```text
Crawler

↓

Access S3

↓

Access Denied

↓

Crawler Failed
```

One of the most common reasons for crawler failure is an incorrect IAM role.

---

# Internal Components Used

During execution, AWS Glue internally uses:

- IAM (Permissions)
- Classifiers (File Detection)
- Glue Data Catalog
- CloudWatch Logs

These services work together behind the scenes.

---

# Performance Considerations

Crawler execution time depends on:

- Number of files
- Folder structure
- File size
- Number of partitions
- Network connectivity (for JDBC sources)

Large datasets naturally take longer to crawl.

---

# Best Practices

- Organize S3 folders properly.
- Use partitioned folders.
- Avoid running crawlers unnecessarily.
- Assign least-privilege IAM roles.
- Review schema updates before production deployment.

---

# Common Mistakes

### Expecting the crawler to read every record

Glue samples files rather than processing every row.

---

### Assuming the crawler modifies data

It only reads data.

---

### Ignoring CloudWatch logs

CloudWatch provides valuable debugging information when crawlers fail.

---

### Running the crawler repeatedly without changes

This wastes time and may incur unnecessary cost.

---

# Interview Questions

### Explain the internal working of a Glue Crawler.

A crawler connects to the data source, scans sample files, detects the file format, infers the schema, discovers partitions, compares existing metadata, and updates the Glue Data Catalog.

---

### Does a crawler read the complete dataset?

No.

It samples the data to infer metadata.

---

### What happens after schema inference?

The metadata is written to the Glue Data Catalog.

---

### Does a crawler modify the original S3 files?

No.

The original data remains unchanged.

---

# Revision Summary

```text
Run Crawler

↓

Connect to Source

↓

Read Sample Data

↓

Detect File Format

↓

Infer Schema

↓

Detect Partitions

↓

Compare Metadata

↓

Update Glue Catalog
```

---

# Key Takeaways

- Crawlers sample data instead of processing every record.
- They automatically detect schema and partitions.
- Metadata is stored in the Glue Data Catalog.
- Original data is never modified.
- Glue Jobs are responsible for ETL, not Crawlers.

---

# Next Section

**02.4 Crawler Architecture**

In the next chapter, we will study the complete architecture of AWS Glue Crawlers, including how Crawlers interact with IAM, S3, Classifiers, the Glue Data Catalog, CloudWatch, Athena, and Glue Jobs.



# AWS Glue Handbook

# 02.4 AWS Glue Crawler Architecture

---

# Introduction

Now that we understand **how a Glue Crawler works internally**, let's understand **where it fits in the complete AWS ecosystem**.

A Glue Crawler never works alone.

It interacts with multiple AWS services to discover metadata and make data available for analytics.

Understanding this architecture is important because it explains **how data flows from storage to analytics**.

---

# High-Level Architecture

```text
                +----------------------+
                |   Data Sources       |
                |----------------------|
                | Amazon S3            |
                | MySQL                |
                | PostgreSQL           |
                | Oracle               |
                | SQL Server           |
                | Redshift             |
                +----------+-----------+
                           |
                           |
                           ▼
                  AWS Glue Crawler
                           |
         --------------------------------
         |              |               |
         ▼              ▼               ▼
   Classifiers      IAM Role      CloudWatch
         |                              |
         |                              |
         ▼                              ▼
   Detect Format                  Execution Logs
         |
         ▼
  Glue Data Catalog
         |
         ▼
-------------------------------
| Athena                     |
| Glue Jobs                  |
| EMR                        |
| Redshift Spectrum          |
| Lake Formation             |
-------------------------------
```

---

# Components of the Architecture

The architecture consists of several components.

Each one has a specific responsibility.

---

# 1. Data Source

The crawler starts by reading data from a supported source.

Common sources include:

- Amazon S3
- MySQL
- PostgreSQL
- Oracle
- SQL Server
- Amazon Redshift
- JDBC databases

Example

```text
employee.csv

stored in

s3://employee-data/
```

The crawler never stores this data.

It only reads it.

---

# 2. IAM Role

Before accessing the data source,

the crawler must assume an IAM Role.

Example permissions:

```text
Read S3 Objects

Read Glue Catalog

Write Glue Catalog

Write CloudWatch Logs
```

Without these permissions,

the crawler cannot execute successfully.

---

# 3. AWS Glue Crawler

This is the core component.

Responsibilities:

- Connect to source
- Read sample data
- Infer schema
- Detect partitions
- Update Glue Catalog

Think of it as the **metadata discovery engine** of AWS Glue.

---

# 4. Classifiers

A crawler uses **Classifiers** to understand the data.

Their job is to determine:

- File format
- Record structure
- Column delimiter
- Schema pattern

Example

```text
employee.csv
```

↓

CSV Classifier

or

```text
employee.json
```

↓

JSON Classifier

AWS provides built-in classifiers for common formats.

Custom classifiers can also be created when needed.

---

# 5. Glue Data Catalog

After schema discovery,

the crawler writes metadata into the Glue Data Catalog.

Example

```text
Database

employee_db

↓

Table

employees

↓

Columns

employee_id

employee_name

salary
```

Only metadata is stored.

The actual data remains in S3.

---

# 6. CloudWatch

Every crawler execution generates logs.

These logs are stored in:

```text
Amazon CloudWatch
```

CloudWatch helps troubleshoot issues like:

- Permission denied
- Unsupported file format
- Connection failure
- Timeout
- Schema detection errors

During production,

CloudWatch is the first place engineers check when a crawler fails.

---

# 7. Athena

Athena uses the metadata stored in the Glue Data Catalog.

Workflow

```text
Amazon S3

↓

Crawler

↓

Glue Catalog

↓

Athena

↓

SQL Query
```

Notice:

Athena never talks directly to the crawler.

It reads metadata from the Catalog.

---

# 8. Glue Jobs

Glue Jobs also use the Glue Data Catalog.

Instead of manually specifying

```text
employee_id

employee_name

salary
```

the job simply reads the table metadata.

Workflow

```text
Crawler

↓

Catalog

↓

Glue Job
```

---

# 9. Redshift Spectrum

Redshift Spectrum can query S3 data directly.

Instead of creating schemas manually,

it uses the Glue Catalog metadata.

Workflow

```text
S3

↓

Crawler

↓

Glue Catalog

↓

Redshift Spectrum
```

---

# Complete Architecture Flow

```text
                Amazon S3

                     │

                     ▼

             AWS Glue Crawler

                     │

         ┌───────────┼───────────┐

         ▼           ▼           ▼

     IAM Role   Classifiers  CloudWatch

                     │

                     ▼

             Glue Data Catalog

         ┌───────────┼────────────┐

         ▼           ▼            ▼

      Athena     Glue Jobs   Redshift

                     │

                     ▼

              Business Users
```

---

# Data Flow Example

Suppose an HR department uploads

```text
employees.csv
```

The architecture behaves like this.

```text
employees.csv

↓

Amazon S3

↓

Glue Crawler

↓

CSV Classifier

↓

Infer Schema

↓

Glue Data Catalog

↓

Athena

↓

SELECT * FROM employees;
```

No manual schema creation is required.

---

# During Our Practice

Our architecture looked like this.

```text
CSV

↓

Amazon S3

↓

Glue Crawler

↓

Glue Catalog

↓

Athena

↓

SQL Query
```

Later,

we extended it further.

```text
CSV

↓

Crawler

↓

Catalog

↓

Glue Job

↓

Workflow

↓

Lambda

↓

EventBridge

↓

DataBrew
```

This gradually built a complete AWS data pipeline.

---

# Why This Architecture Is Powerful

Instead of every service maintaining its own schema,

AWS centralizes metadata.

Benefits:

- One source of truth
- Less duplication
- Easier maintenance
- Better integration
- Faster development

---

# Best Practices

- Store raw data in dedicated S3 buckets.
- Assign least-privilege IAM roles.
- Use meaningful database and table names.
- Partition large datasets.
- Monitor crawler executions using CloudWatch.
- Re-run crawlers only when metadata changes.

---

# Common Mistakes

### Assuming Athena reads S3 directly

Incorrect.

Athena uses the Glue Data Catalog metadata.

---

### Assuming Glue Jobs define schemas manually

Incorrect.

Glue Jobs commonly use metadata from the Catalog.

---

### Ignoring CloudWatch

CloudWatch provides valuable execution logs for troubleshooting.

---

### Giving excessive IAM permissions

Always follow the Principle of Least Privilege.

---

# Interview Questions

### Which AWS services interact directly with a Glue Crawler?

- Amazon S3
- IAM
- Classifiers
- CloudWatch
- Glue Data Catalog

---

### Which AWS services use the Glue Data Catalog?

- Athena
- Glue Jobs
- EMR
- Redshift Spectrum
- Lake Formation

---

### Does Athena communicate directly with the Crawler?

No.

Athena reads metadata from the Glue Data Catalog.

---

### What is the purpose of Classifiers?

They identify the file format and help infer the schema.

---

### Why is CloudWatch part of the architecture?

It stores crawler execution logs and helps with troubleshooting.

---

# Revision Summary

```text
Data Source

↓

IAM Authentication

↓

Glue Crawler

↓

Classifiers

↓

Glue Data Catalog

↓

Athena / Glue Jobs / EMR / Redshift
```

---

# Key Takeaways

- The Crawler is only one part of the AWS Glue ecosystem.
- IAM controls access.
- Classifiers identify file formats.
- The Glue Data Catalog stores metadata.
- CloudWatch stores execution logs.
- Athena and Glue Jobs consume metadata from the Catalog.
- This architecture enables a centralized, scalable, and serverless data engineering workflow.

---

# Next Section

**02.5 Schema Inference**

In the next chapter, we will learn how Glue Crawlers automatically determine column names, data types, and table structures using **Schema Inference**, including how AWS decides whether a column should be `string`, `integer`, `double`, `boolean`, or `date`.


# AWS Glue Handbook

# 02.5 Schema Inference

---

# Introduction

One of the most powerful features of an AWS Glue Crawler is its ability to **automatically understand the structure of your data**.

This process is called **Schema Inference**.

Instead of manually defining:

- Column names
- Data types
- Number of columns

the crawler discovers them automatically.

---

# What is Schema Inference?

**Schema Inference** is the process where a Glue Crawler analyzes your dataset and determines:

- Column names
- Data types
- Number of columns
- Overall table structure

Example CSV:

```csv
employee_id,employee_name,salary,is_active

1,Alice,50000,true
2,Bob,62000,false
```

The crawler infers:

| Column | Data Type |
|----------|-----------|
| employee_id | int |
| employee_name | string |
| salary | double |
| is_active | boolean |

---

# Why is Schema Inference Needed?

Imagine receiving thousands of files every day.

Without Schema Inference you would have to manually define every table.

```text
File

↓

Open File

↓

Check Columns

↓

Create Table

↓

Assign Datatypes
```

With Glue:

```text
File

↓

Crawler

↓

Schema Generated Automatically
```

This saves hours of manual work.

---

# How Schema Inference Works

```text
Read Sample Data

↓

Identify Header

↓

Read Values

↓

Analyze Data Types

↓

Create Schema

↓

Store in Glue Catalog
```

---

# Step 1 — Read the File

Suppose the crawler encounters

```csv
employee_id,employee_name,salary

1,Alice,50000

2,Bob,62000
```

The first row becomes the column names.

---

# Step 2 — Analyze Each Column

Now the crawler examines every column separately.

Example:

```text
employee_id

1
2
3
4
```

Since every value is numeric,

↓

Datatype becomes

```text
int
```

---

Another example:

```text
employee_name

Alice

Bob

John
```

↓

Datatype becomes

```text
string
```

---

# Step 3 — Build the Schema

The crawler combines all columns.

```text
employee_id      int

employee_name    string

salary           double
```

This becomes the table definition inside the Glue Catalog.

---

# Common Data Types

Glue commonly infers the following types.

| Data | Detected Type |
|------|---------------|
| 100 | int |
| 150.75 | double |
| Alice | string |
| true | boolean |
| 2026-07-15 | date |
| 2026-07-15 12:30:00 | timestamp |

---

# Example 1

CSV

```csv
id,name,age

1,Alice,25

2,Bob,30
```

Schema

```text
id      int

name    string

age     int
```

---

# Example 2

CSV

```csv
product,price

Laptop,65000.50

Phone,22000.00
```

Schema

```text
product

string

price

double
```

---

# Example 3

CSV

```csv
employee,active

Alice,true

Bob,false
```

Schema

```text
employee

string

active

boolean
```

---

# Example 4

CSV

```csv
order_date

2026-01-01

2026-02-10
```

Schema

```text
order_date

date
```

---

# Mixed Data Types

Suppose a column contains

```text
100

200

ABC
```

Should it become

```text
int
```

or

```text
string
```

Since one value is text,

Glue usually chooses the safest option:

```text
string
```

This prevents data loss.

---

# Missing Values

Example

```text
salary

50000

NULL

65000
```

Glue still recognizes

```text
salary

double
```

A few missing values normally do not change the datatype.

---

# Incorrect Data

Example

```text
salary

50000

ABC

60000
```

Now the column contains both numbers and text.

Glue may infer

```text
string
```

instead of

```text
double
```

This is why clean data is important.

---

# Schema Inference During Our Practice

During our AWS Glue practice,

we uploaded CSV files to Amazon S3.

When the crawler ran,

it automatically discovered:

- Column names
- Column count
- Datatypes

We did **not** create the schema manually.

After that,

Athena immediately recognized the table.

---

# Where is the Schema Stored?

After inference,

the schema is stored inside

```text
AWS Glue Data Catalog
```

Example

```text
Database

employee_db

↓

Table

employees

↓

Columns

employee_id

employee_name

salary
```

The actual CSV file remains in Amazon S3.

---

# Schema Inference vs Schema Definition

## Manual

```text
Create Table

↓

Define Columns

↓

Assign Datatypes
```

---

## Automatic

```text
Run Crawler

↓

Schema Inferred

↓

Table Created
```

---

# Benefits

- No manual schema creation
- Faster onboarding
- Reduced human errors
- Automatic datatype detection
- Easy integration with Athena and Glue Jobs

---

# Limitations

Schema inference is intelligent but not perfect.

Problems may occur when:

- Files contain inconsistent data.
- Columns contain mixed data types.
- Headers are missing.
- Data quality is poor.

In such cases,

you may need to clean the data before crawling.

---

# Best Practices

- Keep columns consistent.
- Avoid mixing numbers and text.
- Use proper headers.
- Keep date formats consistent.
- Validate data before running the crawler.

---

# Common Mistakes

### Mixing data types

```text
100

200

ABC
```

This often causes the datatype to become

```text
string
```

---

### Different date formats

```text
2026-01-01

01/01/2026
```

Inconsistent formats can lead to incorrect inference.

---

### Missing headers

Without proper headers,

column names may become generic, making the table harder to understand.

---

# Interview Questions

### What is Schema Inference?

It is the process where a Glue Crawler automatically determines column names, data types, and table structure from the data.

---

### Does Schema Inference modify the data?

No.

It only analyzes the data and generates metadata.

---

### Where is the inferred schema stored?

AWS Glue Data Catalog.

---

### What happens if a numeric column contains text?

Glue generally selects the safer datatype,

which is usually **string**.

---

### Why is clean data important?

Because inconsistent data can lead to incorrect datatype detection.

---

# Revision Summary

```text
CSV

↓

Read Header

↓

Read Sample Data

↓

Infer Datatypes

↓

Generate Schema

↓

Store Metadata

↓

Glue Data Catalog
```

---

# Key Takeaways

- Schema Inference is automatic.
- The crawler detects column names and data types.
- Metadata is stored in the Glue Data Catalog.
- Original data is never modified.
- Clean and consistent data improves inference accuracy.
- Mixed data types often result in a `string` datatype.

---

# Next Section

**02.6 Partition Discovery**

In the next chapter, we'll learn how AWS Glue Crawlers automatically identify partition folders (such as `year=2026/month=07/`) and why partitioning dramatically improves Athena query performance while reducing query costs.



# AWS Glue Handbook

# 02.6 Partition Discovery

---

# Introduction

As datasets grow larger, scanning every file for every query becomes slow and expensive.

To solve this problem, AWS Glue Crawlers automatically detect **partitions** while scanning your data.

Partition Discovery is one of the most valuable features of AWS Glue because it improves query performance and reduces costs.

---

# What is Partitioning?

Partitioning is the process of organizing data into separate folders based on one or more columns.

Instead of storing everything in a single folder,

```text
sales/

sales1.csv
sales2.csv
sales3.csv
sales4.csv
```

we organize the data like this:

```text
sales/

year=2025/

year=2026/
```

or even

```text
sales/

year=2026/

month=01/

month=02/

month=03/
```

Each folder represents a partition.

---

# Why Do We Need Partitioning?

Suppose you have sales data for five years.

Without partitions:

```text
sales/

2019.csv

2020.csv

2021.csv

2022.csv

2023.csv
```

When Athena executes

```sql
SELECT *
FROM sales
WHERE year = 2023;
```

it must scan **all files**.

---

With partitions:

```text
sales/

year=2019/

year=2020/

year=2021/

year=2022/

year=2023/
```

Athena only scans

```text
year=2023/
```

This makes queries:

- Faster
- Cheaper
- More efficient

---

# What is Partition Discovery?

Partition Discovery is the process where a Glue Crawler automatically detects partition folders and adds them to the Glue Data Catalog.

Example:

```text
sales/

year=2026/

month=07/

sales.csv
```

Crawler automatically creates:

```text
Partition Columns

year

month
```

No manual configuration is required.

---

# How Partition Discovery Works

```text
Amazon S3

↓

Read Folder Structure

↓

Identify Partition Folders

↓

Create Partition Metadata

↓

Store in Glue Data Catalog
```

---

# Folder Structure Example

Consider the following S3 bucket.

```text
sales-data/

year=2025/

month=01/

sales.csv

month=02/

sales.csv

year=2026/

month=01/

sales.csv
```

Glue automatically detects:

```text
year

month
```

as partition columns.

---

# Metadata Created

After crawling,

Glue Catalog contains:

```text
Database

sales_db

Table

sales

Columns

customer_id

amount

payment_type

Partition Columns

year

month
```

Notice that partition columns are stored separately from regular columns.

---

# How Athena Uses Partitions

Suppose you execute:

```sql
SELECT *
FROM sales
WHERE year = 2026;
```

Athena checks the Glue Catalog.

Instead of reading every folder,

it directly accesses:

```text
year=2026/
```

This greatly reduces the amount of data scanned.

---

# Example

Without partitioning:

```text
sales/

10 TB of data
```

Athena scans:

```text
10 TB
```

---

With partitioning:

```text
sales/

year=2026/
```

Athena may scan only:

```text
200 GB
```

Result:

- Lower cost
- Faster query execution

---

# During Our Practice

While learning AWS Glue,

we created an S3 structure similar to:

```text
taxi-data/

year=2025/

year=2026/
```

After running the crawler,

Glue automatically created

```text
year
```

as a partition column.

Later,

Athena was able to filter data using the partition.

This was one of the most useful hands-on exercises because it showed how folder names become partition metadata.

---

# Partition Discovery Flow

```text
CSV Files

↓

Amazon S3

↓

Partitioned Folders

↓

Glue Crawler

↓

Partition Detection

↓

Glue Data Catalog

↓

Athena Queries
```

---

# Partition Columns vs Regular Columns

Example table

| Regular Columns | Partition Columns |
|-----------------|-------------------|
| employee_id | year |
| employee_name | month |
| salary | day |

Regular columns come from the file.

Partition columns come from the folder names.

---

# Good Folder Structure

```text
sales/

year=2026/

month=01/

day=01/
```

This is easy for Glue to understand.

---

# Poor Folder Structure

```text
sales/

folder1/

folder2/

folder3/
```

Glue cannot determine what

```text
folder1
```

represents.

Meaningful folder names should always be used.

---

# Common Partition Strategies

### By Year

```text
year=2026/
```

---

### By Month

```text
year=2026/

month=07/
```

---

### By Day

```text
year=2026/

month=07/

day=15/
```

---

### By Country

```text
country=India/

country=USA/
```

---

### By Department

```text
department=HR/

department=Finance/
```

---

# Advantages of Partition Discovery

- Faster Athena queries
- Reduced query cost
- Automatic partition creation
- Better scalability
- No manual partition management

---

# Limitations

Partition Discovery works only when the folder structure follows a meaningful pattern.

For example,

Good:

```text
year=2026/
```

Bad:

```text
abc123/
```

Glue cannot infer business meaning from arbitrary folder names.

---

# Best Practices

- Use meaningful folder names.
- Keep partition levels consistent.
- Avoid too many partition levels unless necessary.
- Use year/month/day for time-based datasets.
- Re-run the crawler after adding new partition folders.

---

# Common Mistakes

### Uploading new partition folders without re-running the crawler

Example:

```text
year=2027/
```

Athena will not recognize the new partition until the Glue Catalog is updated.

---

### Partitioning by a column that is rarely filtered

Choose partition columns that are commonly used in queries.

Examples:

- year
- month
- country
- region

---

### Creating too many tiny partitions

Thousands of very small partitions can reduce performance instead of improving it.

---

# Interview Questions

### What is Partition Discovery?

It is the automatic detection of partition folders by a Glue Crawler.

---

### Where are partition details stored?

In the AWS Glue Data Catalog.

---

### Why is partitioning important?

Because it improves query performance and reduces the amount of data scanned.

---

### Does the partition column exist inside the CSV file?

Not necessarily.

It is usually derived from the folder structure.

---

### Which AWS service benefits most from partitioning?

Amazon Athena.

---

# Revision Summary

```text
Amazon S3

↓

year=2026/

↓

Glue Crawler

↓

Partition Detection

↓

Glue Data Catalog

↓

Athena

↓

Scan Only Required Partition
```

---

# Key Takeaways

- Partitioning organizes data into meaningful folders.
- Glue Crawlers automatically detect partition folders.
- Partition metadata is stored in the Glue Data Catalog.
- Athena uses partition metadata to scan only relevant data.
- Proper partitioning improves performance and lowers query costs.
- Use meaningful folder names such as `year=2026/month=07/`.

---

# Next Section

**02.7 Supported Data Sources**

In the next chapter, we'll explore all the data sources that AWS Glue Crawlers can connect to, including Amazon S3, JDBC databases, Amazon RDS, Redshift, and how Glue accesses each of them.

# AWS Glue Handbook

# 02.7 Supported Data Sources

---

# Introduction

An AWS Glue Crawler is not limited to Amazon S3.

It can scan data from multiple sources, discover their schema, and store the metadata in the AWS Glue Data Catalog.

This flexibility allows organizations to maintain a centralized metadata repository even when data is spread across different storage systems.

---

# What is a Data Source?

A **Data Source** is the location from which a Glue Crawler reads data.

Examples include:

- Amazon S3
- Relational Databases
- Data Warehouses
- Data Lakes

The crawler **only reads the data**. It never modifies or moves it.

---

# Supported Data Sources Overview

| Data Source | Supported | Common Use Case |
|-------------|-----------|----------------|
| Amazon S3 | ✅ | Data Lake |
| Amazon RDS | ✅ | Relational Database |
| MySQL | ✅ | OLTP Applications |
| PostgreSQL | ✅ | Business Applications |
| Oracle | ✅ | Enterprise Systems |
| SQL Server | ✅ | Enterprise Applications |
| Amazon Redshift | ✅ | Data Warehouse |
| JDBC Sources | ✅ | Custom Database Connections |
| DynamoDB | ✅ | NoSQL Database (via Glue support) |

---

# 1. Amazon S3

Amazon S3 is the most common data source used with AWS Glue.

Example:

```text
s3://company-data/employees/
```

Typical files:

```text
employees.csv

customers.parquet

sales.json
```

The crawler scans the files and creates metadata automatically.

### Common Use Cases

- Data Lake
- Log Storage
- CSV Files
- JSON Files
- Parquet Files

---

# 2. Amazon RDS

Glue Crawlers can connect to Amazon RDS databases.

Supported engines include:

- MySQL
- PostgreSQL
- MariaDB
- Oracle
- SQL Server

Example

```text
Amazon RDS

↓

employee_database

↓

employees table
```

The crawler reads the table structure and creates metadata.

---

# 3. MySQL

A crawler can connect directly to a MySQL database using JDBC.

Example

```text
Employee Database

↓

employees

departments

salary
```

Glue creates corresponding metadata tables.

---

# 4. PostgreSQL

PostgreSQL is commonly used in enterprise applications.

Glue connects through JDBC.

Example

```text
PostgreSQL

↓

sales_db

↓

orders table
```

Crawler reads the schema automatically.

---

# 5. Oracle Database

Many large enterprises still use Oracle databases.

Glue Crawlers support Oracle using JDBC connections.

Typical enterprise use cases:

- Finance
- ERP
- HR Systems

---

# 6. Microsoft SQL Server

Organizations using SQL Server can also use Glue Crawlers.

Example

```text
SQL Server

↓

Employee Table

↓

Glue Crawler

↓

Glue Catalog
```

---

# 7. Amazon Redshift

Glue Crawlers can crawl Amazon Redshift tables.

Workflow

```text
Amazon Redshift

↓

Glue Crawler

↓

Glue Data Catalog
```

This allows Glue Jobs and Athena to understand Redshift metadata.

---

# 8. JDBC Connections

Any JDBC-compatible database can be connected.

Examples

- DB2
- SAP HANA
- MariaDB
- Teradata
- Other supported JDBC databases

Workflow

```text
Database

↓

JDBC Connection

↓

Glue Crawler
```

---

# Connection Requirements

For database sources, the crawler needs:

- JDBC Connection
- Username
- Password
- IAM Role
- Network Connectivity
- VPC (if the database is private)

---

# File Formats Supported

For file-based sources like S3, Glue supports:

| Format | Supported |
|----------|-----------|
| CSV | ✅ |
| JSON | ✅ |
| Parquet | ✅ |
| ORC | ✅ |
| Avro | ✅ |
| XML (using classifiers) | ✅ |

---

# During Our Practice

In our hands-on sessions, we used:

```text
Amazon S3

↓

CSV Files

↓

Glue Crawler

↓

Glue Data Catalog

↓

Athena
```

Although Glue supports many other data sources, S3 is the most common starting point because it is simple, serverless, and widely used in AWS data engineering.

---

# Architecture

```text
                Data Sources

    Amazon S3
    Amazon RDS
    MySQL
    PostgreSQL
    Oracle
    SQL Server
    Redshift
    JDBC Databases

                │
                ▼

         AWS Glue Crawler

                │
                ▼

       Glue Data Catalog

                │
      -------------------
      │        │        │
      ▼        ▼        ▼

   Athena   Glue Jobs  EMR
```

---

# Choosing the Right Data Source

| Scenario | Recommended Source |
|----------|--------------------|
| CSV files | Amazon S3 |
| Data Lake | Amazon S3 |
| OLTP Database | MySQL / PostgreSQL |
| Enterprise ERP | Oracle |
| Data Warehouse | Amazon Redshift |
| Existing SQL Database | JDBC Connection |

---

# Best Practices

- Prefer Amazon S3 for analytics workloads.
- Use secure JDBC connections.
- Store database credentials securely.
- Ensure IAM permissions are configured correctly.
- For private databases, configure VPC access properly.

---

# Common Errors

### Access Denied

Cause:

IAM Role lacks permission.

---

### JDBC Connection Failed

Cause:

Incorrect endpoint, credentials, or network configuration.

---

### Timeout

Cause:

Crawler cannot reach the database.

Usually occurs when:

- Incorrect VPC
- Missing Security Group rules
- Database not publicly accessible

---

### Unsupported Format

Cause:

The file format is not recognized.

Solution:

Use supported formats or configure a custom classifier.

---

# Interview Questions

### Which is the most commonly used Glue Crawler data source?

Amazon S3.

---

### Can a Glue Crawler connect to relational databases?

Yes.

Examples include MySQL, PostgreSQL, Oracle, SQL Server, and Amazon RDS.

---

### How does Glue connect to databases?

Using JDBC connections.

---

### Can a Glue Crawler crawl Amazon Redshift?

Yes.

---

### Does a Glue Crawler support Parquet files?

Yes.

Along with CSV, JSON, ORC, and Avro.

---

# Revision Summary

```text
Amazon S3
Amazon RDS
MySQL
PostgreSQL
Oracle
SQL Server
Redshift
JDBC

        │

        ▼

AWS Glue Crawler

        │

        ▼

Glue Data Catalog

        │

        ▼

Athena / Glue Jobs / EMR
```

---

# Key Takeaways

- AWS Glue Crawlers support multiple data sources.
- Amazon S3 is the most commonly used source.
- Relational databases are accessed through JDBC.
- The crawler only reads data and creates metadata.
- The discovered metadata is stored in the Glue Data Catalog.
- Proper IAM permissions and network configuration are essential for successful crawling.

---

# Next Section

**02.8 Creating and Configuring Your First Glue Crawler**

In the next chapter, we'll revisit the exact hands-on steps we performed in the AWS Console, explaining every configuration option and why we selected it.


# AWS Glue Handbook

# 02.8 Creating and Configuring Your First Glue Crawler

---

# Introduction

In the previous sections, we learned:

- What a Glue Crawler is
- Why it is needed
- How it works internally
- How Schema Inference works
- How Partition Discovery works
- Supported Data Sources

Now it's time to create a crawler.

In this chapter, we'll revisit the exact hands-on exercise that we performed and explain every important configuration option.

---

# Prerequisites

Before creating a crawler, ensure you have:

- An AWS Account
- A Glue Database
- An S3 Bucket
- Sample CSV files uploaded to S3
- An IAM Role with Glue permissions

Example

```text
Bucket

employee-data

↓

employees.csv
```

---

# Step 1 – Open AWS Glue

Go to

```text
AWS Console

↓

AWS Glue
```

From the left navigation panel

```text
Data Catalog

↓

Crawlers
```

Click

```text
Create Crawler
```

---

# Step 2 – Enter Crawler Details

Provide a meaningful name.

Example

```text
employee-crawler
```

Avoid generic names like

```text
crawler1

test

abc
```

because production environments often contain hundreds of crawlers.

---

# Step 3 – Choose Data Source

Select

```text
Amazon S3
```

Then browse to your bucket.

Example

```text
s3://employee-data/
```

This tells the crawler where the files are located.

---

# Step 4 – Select IAM Role

Choose an IAM Role that has permission to:

- Read S3
- Write Glue Catalog
- Write CloudWatch Logs

Example

```text
AWSGlueServiceRole
```

If the required role does not exist, AWS allows you to create one automatically.

---

# Step 5 – Select Database

Choose an existing Glue Database.

Example

```text
employee_db
```

The discovered tables will be created inside this database.

---

# Step 6 – Configure Schedule

For learning purposes, choose

```text
Run On Demand
```

This means the crawler only runs when you manually start it.

In production, you can configure schedules such as:

- Every hour
- Daily
- Weekly

---

# Step 7 – Review

Review all settings.

Example

```text
Crawler Name

employee-crawler

Source

Amazon S3

Bucket

employee-data

Database

employee_db

IAM Role

AWSGlueServiceRole
```

Click

```text
Create Crawler
```

---

# Step 8 – Run the Crawler

Select the crawler.

Click

```text
Run
```

The crawler status changes to

```text
Starting

↓

Running

↓

Stopping

↓

Ready
```

---

# Step 9 – Verify Results

After completion,

open

```text
Glue Data Catalog

↓

Tables
```

You should see a new table.

Example

```text
employees
```

Click the table.

You can verify:

- Columns
- Data Types
- S3 Location
- Partitions (if available)

---

# Step 10 – Query Using Athena

Since the crawler created metadata,

Athena can now query the data.

Example

```sql
SELECT *
FROM employees;
```

If everything is configured correctly,

Athena returns the data stored in S3.

---

# Console Workflow

```text
AWS Glue

↓

Create Crawler

↓

Choose S3

↓

Choose IAM Role

↓

Choose Database

↓

Review

↓

Create

↓

Run

↓

Table Created
```

---

# Configuration Options Explained

## Crawler Name

Use descriptive names.

Good

```text
employee-crawler
sales-crawler
customer-crawler
```

Bad

```text
test
abc
crawler1
```

---

## Data Source

Specifies where the crawler reads data.

Examples

```text
Amazon S3

MySQL

PostgreSQL

Oracle

SQL Server
```

---

## IAM Role

Allows Glue to access AWS resources.

Without proper permissions,

the crawler fails.

---

## Database

Determines where metadata will be stored.

Example

```text
employee_db
```

Multiple tables can belong to the same database.

---

## Schedule

Controls how often the crawler runs.

Options include:

- On Demand
- Hourly
- Daily
- Weekly
- Custom Schedule

For learning, we used

```text
On Demand
```

---

# During Our Practice

We performed the following steps:

- Created an S3 bucket.
- Uploaded CSV files.
- Created a Glue Database.
- Created a Glue Crawler.
- Selected the S3 bucket as the source.
- Selected the IAM Role.
- Selected the Glue Database.
- Ran the crawler.
- Verified that a table was automatically created.
- Queried the table using Athena.

This was our first complete Glue workflow.

---

# Common Errors

## 1. Access Denied

Reason

IAM Role lacks permission.

Solution

Grant the required S3 and Glue permissions.

---

## 2. No Tables Created

Possible reasons

- Incorrect S3 path
- Empty folder
- Unsupported file format

---

## 3. Crawler Failed

Possible reasons

- IAM issue
- Network issue
- Invalid data source

Check CloudWatch logs for details.

---

## 4. Athena Cannot Query

Reason

Crawler completed,

but Athena is using a different database.

Always verify that Athena is pointing to the correct Glue Database.

---

# Best Practices

- Use meaningful crawler names.
- Organize files in dedicated S3 folders.
- Keep raw and processed data separate.
- Use On-Demand during development.
- Use scheduled crawlers only when needed.
- Monitor crawler runs using CloudWatch.

---

# Interview Questions

### What are the minimum requirements to create a Glue Crawler?

- Data Source
- IAM Role
- Glue Database

---

### Why is an IAM Role required?

To allow the crawler to read the data source and update the Glue Data Catalog.

---

### Can a crawler create tables automatically?

Yes.

If the table does not exist, it creates it automatically.

---

### What happens after the crawler finishes?

Metadata is stored in the Glue Data Catalog, making the data available to Athena and Glue Jobs.

---

# Revision Summary

```text
Upload CSV

↓

Amazon S3

↓

Create Glue Crawler

↓

Select IAM Role

↓

Select Database

↓

Run Crawler

↓

Glue Data Catalog

↓

Athena Query
```

---

# Key Takeaways

- Creating a Glue Crawler requires only a few essential configurations.
- The most important settings are the **Data Source**, **IAM Role**, and **Glue Database**.
- Running the crawler automatically creates or updates metadata.
- Athena and Glue Jobs use this metadata to work with the data stored in S3.
- During our practice, we successfully completed this end-to-end workflow and verified the results using Athena.

---

# Next Section

**02.9 Hands-on Exercise**

In the next chapter, we'll consolidate the complete hands-on exercise we performed—from creating the S3 bucket to querying the discovered table in Athena—along with practical observations and lessons learned.


# AWS Glue Handbook

# 02.9 Hands-on Exercise – Creating Your First AWS Glue Crawler

---

# Objective

In this exercise, we will recreate the exact workflow we performed while learning AWS Glue.

By the end of this exercise, you will be able to:

- Upload data into Amazon S3
- Create a Glue Database
- Create a Glue Crawler
- Discover the schema automatically
- Create a Glue Catalog table
- Query the data using Athena

---

# Architecture

```text
           CSV File
               │
               ▼
          Amazon S3
               │
               ▼
       AWS Glue Crawler
               │
               ▼
     AWS Glue Data Catalog
               │
               ▼
          Amazon Athena
               │
               ▼
        SQL Query Results
```

---

# Step 1 – Create an S3 Bucket

Navigate to

```text
AWS Console

↓

Amazon S3

↓

Create Bucket
```

Example

```text
Bucket Name

employee-data-demo
```

Keep all other settings as default.

---

# Step 2 – Upload the Dataset

Upload a CSV file.

Example

```text
employees.csv
```

Example CSV

```csv
employee_id,employee_name,department,salary

1,Alice,HR,50000

2,Bob,Finance,62000

3,John,IT,70000
```

---

# Step 3 – Create a Glue Database

Navigate to

```text
AWS Glue

↓

Data Catalog

↓

Databases

↓

Create Database
```

Example

```text
employee_db
```

---

# Step 4 – Create a Glue Crawler

Navigate to

```text
AWS Glue

↓

Data Catalog

↓

Crawlers

↓

Create Crawler
```

Configure:

| Setting | Value |
|----------|-------|
| Name | employee-crawler |
| Source | Amazon S3 |
| Path | employee-data-demo |
| IAM Role | AWSGlueServiceRole |
| Database | employee_db |
| Schedule | On Demand |

Click

```text
Create
```

---

# Step 5 – Run the Crawler

Select

```text
employee-crawler
```

Click

```text
Run
```

Status changes

```text
Ready

↓

Starting

↓

Running

↓

Stopping

↓

Ready
```

---

# Step 6 – Verify the Table

Navigate to

```text
Glue

↓

Tables
```

You should now see

```text
employees
```

Open the table.

Verify

- Table Name
- Columns
- Data Types
- S3 Location

Example

```text
employee_id

employee_name

department

salary
```

---

# Step 7 – Query Using Athena

Navigate to

```text
Amazon Athena
```

Choose

```text
employee_db
```

Execute

```sql
SELECT *
FROM employees;
```

Expected Output

```text
employee_id

employee_name

department

salary
```

---

# What Happened Internally?

```text
CSV Uploaded

↓

Crawler Started

↓

Schema Inferred

↓

Glue Catalog Updated

↓

Athena Read Metadata

↓

SQL Query Executed
```

Notice

The crawler never moved the CSV file.

It only created metadata.

---

# Observations from Our Practice

During our learning session we observed:

### Observation 1

The crawler completed successfully and automatically created the table.

No manual schema definition was required.

---

### Observation 2

Athena was immediately able to query the table because it reads metadata from the Glue Data Catalog.

---

### Observation 3

The CSV file always remained inside Amazon S3.

Glue never copied or modified it.

---

### Observation 4

When partition folders are present, the crawler automatically creates partition columns.

---

# Common Errors We Encountered

## Problem

Crawler failed.

### Possible Reasons

- Incorrect IAM Role
- Wrong S3 path
- Empty folder
- Unsupported file format

---

## Problem

No table created.

### Possible Reasons

- Folder contained no files.
- Incorrect S3 location.
- Wrong database selected.

---

## Problem

Athena returned

```text
Table not found
```

### Reason

Wrong Glue Database selected inside Athena.

---

## Problem

New files not visible.

### Reason

Crawler had not been executed again.

---

# Troubleshooting Checklist

Before running the crawler verify:

✅ CSV uploaded

✅ Correct S3 path

✅ IAM Role attached

✅ Glue Database exists

✅ Supported file format

✅ Crawler status is Ready

---

# Real Production Workflow

In production, the process is usually automated.

```text
Application

↓

CSV Generated

↓

Amazon S3

↓

EventBridge

↓

Glue Crawler

↓

Glue Data Catalog

↓

Glue Job

↓

Athena

↓

QuickSight Dashboard
```

Instead of manually running the crawler, automation services such as EventBridge can trigger it.

---

# Best Practices

- Keep raw and processed data in separate buckets.
- Give meaningful names to crawlers and databases.
- Organize S3 folders properly.
- Re-run the crawler whenever new partitions are added.
- Monitor crawler executions using CloudWatch.

---

# Interview Questions

### What was the purpose of creating the Glue Database before the crawler?

The crawler stores discovered metadata inside a Glue Database. Without a database, the crawler has nowhere to create the table.

---

### Does the crawler create the CSV file?

No.

The CSV file already exists in Amazon S3.

The crawler only creates metadata.

---

### Why was Athena able to query immediately?

Because the crawler created metadata inside the Glue Data Catalog, which Athena uses to understand the dataset.

---

### Which AWS services were involved in this exercise?

- Amazon S3
- AWS Glue
- IAM
- Glue Data Catalog
- Amazon Athena

---

# Revision Summary

```text
Create S3 Bucket

↓

Upload CSV

↓

Create Glue Database

↓

Create Glue Crawler

↓

Run Crawler

↓

Table Created

↓

Query Using Athena
```

---

# Key Takeaways

- A Glue Crawler automates metadata discovery.
- It scans the files stored in Amazon S3.
- It creates tables inside the Glue Data Catalog.
- Athena relies on this metadata to execute SQL queries.
- During our hands-on exercise, we successfully completed the complete workflow from S3 to Athena without manually creating any table or schema.

---

# Next Section

**02.10 Scheduling Glue Crawlers**

In the next chapter, we'll learn how to automate crawler execution using schedules instead of manually clicking the **Run** button every time new data arrives.



# AWS Glue Handbook

# 02.10 Scheduling Glue Crawlers

---

# Introduction

So far, we have manually started our Glue Crawler by clicking the **Run** button.

This works well during development and learning.

However, in a production environment, new data arrives continuously, making manual execution impractical.

AWS Glue allows you to **schedule crawlers** so they automatically scan data sources at predefined intervals.

---

# Why Schedule a Crawler?

Imagine a company that receives sales data every night.

```text
11:59 PM

↓

New CSV Uploaded

↓

Amazon S3
```

Without scheduling:

A Data Engineer must manually start the crawler every morning.

With scheduling:

```text
11:59 PM

↓

New File Arrives

↓

Scheduled Crawler Runs

↓

Glue Catalog Updated
```

Everything happens automatically.

---

# Manual vs Scheduled Crawlers

| Manual | Scheduled |
|---------|-----------|
| Started by user | Runs automatically |
| Suitable for learning | Suitable for production |
| Requires manual effort | No manual intervention |
| Good for testing | Good for recurring data |

---

# Scheduling Workflow

```text
Amazon S3

↓

New Data Arrives

↓

Scheduled Glue Crawler

↓

Glue Data Catalog Updated

↓

Athena / Glue Jobs
```

---

# Scheduling Options

AWS Glue provides several scheduling options.

### On Demand

The crawler runs only when you click **Run**.

Example:

```text
Developer

↓

Run Crawler
```

This is what we used during our practice.

---

### Hourly

The crawler runs every hour.

Example

```text
12:00 PM

↓

1:00 PM

↓

2:00 PM
```

Suitable for frequently updated datasets.

---

### Daily

Runs once every day.

Example

```text
Every Day

2:00 AM
```

Useful for daily ETL pipelines.

---

### Weekly

Runs once every week.

Example

```text
Every Sunday

1:00 AM
```

Suitable for reports or archival data.

---

### Custom Schedule

AWS Glue uses **Cron Expressions** for advanced scheduling.

Example

```text
Every Monday at 3:00 AM
```

or

```text
Every 6 Hours
```

---

# How to Configure a Schedule

When creating or editing a crawler,

you'll find the **Schedule** section.

Choose one of:

```text
On Demand

Hourly

Daily

Weekly

Custom
```

Select the desired frequency and save the crawler.

---

# Example Scenario

Suppose a company uploads transaction files every night.

```text
2:00 AM

↓

transactions.csv

↓

Amazon S3
```

Crawler Schedule

```text
2:30 AM

↓

Run Automatically

↓

Update Catalog

↓

Athena Ready
```

No engineer needs to start the crawler manually.

---

# During Our Practice

While learning AWS Glue, we selected:

```text
On Demand
```

Reason:

- Easy to test
- Immediate feedback
- No unnecessary crawler executions
- Saves cost during learning

For production, a scheduled crawler would be the preferred choice.

---

# When Should You Schedule a Crawler?

Use scheduling when:

- Data arrives regularly.
- Metadata changes frequently.
- Reports depend on updated tables.
- ETL jobs run on a fixed schedule.

Examples:

- Daily sales reports
- Nightly data warehouse refresh
- Log file processing
- IoT sensor data

---

# When Should You NOT Schedule a Crawler?

Avoid scheduling when:

- Data rarely changes.
- Schema is static.
- You manually control ETL execution.
- You are experimenting or developing.

In such cases, **On Demand** is sufficient.

---

# Relationship Between Crawler and ETL Jobs

A common production pipeline looks like this:

```text
New File

↓

Amazon S3

↓

Scheduled Glue Crawler

↓

Glue Catalog Updated

↓

Scheduled Glue Job

↓

Processed Data

↓

Athena Dashboard
```

The crawler updates metadata first.

The Glue Job then processes the latest data.

---

# Scheduling vs Event-Driven Execution

There are two common ways to automate a crawler.

## Time-Based Scheduling

```text
Every Day

↓

Run Crawler
```

Simple and predictable.

---

## Event-Driven

```text
File Uploaded

↓

Amazon EventBridge

↓

Lambda

↓

Glue Workflow

↓

Crawler / Glue Job
```

Runs only when new data arrives.

This approach is often more efficient.

---

# Best Practices

- Use **On Demand** during development.
- Schedule crawlers only when data changes regularly.
- Align crawler schedules with ETL job schedules.
- Avoid running crawlers too frequently.
- Monitor scheduled runs using CloudWatch.

---

# Common Mistakes

### Scheduling every few minutes

If new data arrives only once per day,

running the crawler every 5 minutes wastes resources.

---

### Running ETL before the crawler

If the Glue Job starts before the crawler updates the metadata,

the job may process outdated schemas.

---

### Forgetting Time Zones

Always verify the schedule timing, especially in global deployments.

---

# Interview Questions

### Why do we schedule Glue Crawlers?

To automatically update metadata whenever new data arrives.

---

### Which schedule did we use during practice?

**On Demand**, because we were learning and manually controlling execution.

---

### Can Glue Crawlers run automatically?

Yes.

They can run on hourly, daily, weekly, or custom schedules.

---

### Which is better: Scheduled or Event-Driven?

It depends.

- **Scheduled** → Best for predictable, recurring data.
- **Event-Driven** → Best when data arrives unpredictably.

---

# Revision Summary

```text
Data Arrives

↓

Scheduled Crawler

↓

Glue Data Catalog Updated

↓

Athena / Glue Jobs
```

---

# Key Takeaways

- Scheduling automates Glue Crawler execution.
- AWS Glue supports **On Demand**, **Hourly**, **Daily**, **Weekly**, and **Custom** schedules.
- We used **On Demand** during our hands-on exercises.
- Production systems commonly use scheduled or event-driven execution.
- Scheduling ensures that metadata stays synchronized with incoming data.

---

# Next Section

**02.11 Updating Existing Tables**

In the next chapter, we'll learn how Glue Crawlers handle existing tables, schema changes, new columns, and partition updates when data evolves over time.


# AWS Glue Handbook

# 02.11 Updating Existing Tables

---

# Introduction

In the previous chapters, we learned how a Glue Crawler creates a new table when it scans a data source for the first time.

But what happens when:

- New files are added?
- A new column appears?
- A partition is added?
- The schema changes?

AWS Glue Crawlers are designed to handle these situations by **updating existing tables** instead of creating duplicate tables.

---

# Why Table Updates are Important

Imagine your company receives an employee file every day.

### Day 1

```text
employees.csv

employee_id
employee_name
salary
```

The crawler creates a table named:

```text
employees
```

---

### Day 2

A new file arrives.

```text
employees.csv

employee_id
employee_name
salary
department
```

Instead of creating another table,

the crawler can update the existing **employees** table.

---

# How the Update Process Works

```text
Existing Table

↓

Crawler Runs Again

↓

Compare Schema

↓

Update Metadata

↓

Same Table Updated
```

---

# What Can a Crawler Update?

A Glue Crawler can update:

- New partitions
- New columns
- Column data types (depending on configuration)
- Table metadata
- S3 location (if applicable)

---

# Scenario 1 – New Files Added

Suppose your S3 bucket contains:

```text
employees/

employees1.csv
```

The crawler creates the table.

Later,

```text
employees/

employees1.csv
employees2.csv
employees3.csv
```

Running the crawler again does **not** create another table.

Instead,

the existing table is updated to include the new files.

---

# Scenario 2 – New Column Added

Original file

```csv
employee_id,employee_name,salary
```

New file

```csv
employee_id,employee_name,salary,department
```

Crawler execution:

```text
Old Schema

↓

Compare

↓

New Column Found

↓

Update Table
```

Result

```text
employee_id

employee_name

salary

department
```

---

# Scenario 3 – New Partition Added

Original folder

```text
sales/

year=2025/
```

New folder

```text
sales/

year=2025/

year=2026/
```

When the crawler runs again,

it discovers

```text
year=2026
```

and adds it as a new partition.

The existing table remains unchanged.

Only partition metadata is updated.

---

# Scenario 4 – No Changes Found

Suppose nothing changed.

```text
Same Files

↓

Crawler Runs

↓

No Schema Changes

↓

No Metadata Update
```

The crawler simply finishes successfully.

---

# How Glue Knows What Changed

Internally,

the crawler compares:

```text
Existing Glue Catalog

↓

Current Data Source

↓

Difference Found?

↓

Yes → Update

No → Finish
```

This comparison is automatic.

---

# Schema Evolution

Schema Evolution means the structure of the data changes over time.

Example

Day 1

```text
employee_id

employee_name
```

Day 30

```text
employee_id

employee_name

department

salary

joining_date
```

The crawler can detect these changes and update the metadata.

---

# During Our Practice

In our hands-on sessions,

we mainly created new tables.

However,

we also observed that:

- Running the crawler multiple times did **not** create duplicate tables.
- The existing metadata was reused.
- The crawler updates the existing table instead of creating a new one.

This behavior is exactly what production systems require.

---

# Table Update Flow

```text
Amazon S3

↓

Existing Table

↓

New File Added

↓

Run Crawler

↓

Compare Metadata

↓

Update Existing Table
```

---

# Benefits of Updating Existing Tables

- No duplicate tables
- Automatic schema maintenance
- Automatic partition discovery
- Less manual work
- Metadata stays synchronized

---

# Crawler Configuration

While creating a crawler,

AWS provides options that control how schema changes are handled.

Common choices include:

- Update existing table
- Add new partitions
- Ignore certain schema changes

The exact options may vary slightly depending on the AWS Console version.

---

# Best Practices

- Re-run the crawler after adding new partitions.
- Review schema changes before production deployment.
- Keep file formats consistent.
- Avoid frequent unnecessary schema changes.
- Test schema evolution in a development environment first.

---

# Common Mistakes

### Expecting a new table

Many beginners think a new file creates a new table.

Actually,

the crawler usually updates the existing table.

---

### Forgetting to run the crawler

Adding files alone does **not** update the Glue Catalog.

The crawler must run again.

---

### Changing column data types

Example

Old

```text
salary

50000
```

New

```text
salary

Fifty Thousand
```

Mixed data types may lead to unexpected schema inference.

---

# Interview Questions

### What happens if new files are added to the same S3 location?

Running the crawler updates the existing table metadata.

---

### Will a crawler create duplicate tables?

No.

It updates the existing table if it already exists.

---

### What is Schema Evolution?

Schema Evolution is the process of detecting and handling changes in the structure of the data over time.

---

### Can a crawler detect new partitions?

Yes.

It automatically adds newly discovered partitions to the Glue Data Catalog.

---

# Revision Summary

```text
Existing Table

↓

New Files Added

↓

Run Crawler

↓

Compare Existing Metadata

↓

Update Table

↓

Athena Reads Latest Metadata
```

---

# Key Takeaways

- Glue Crawlers update existing tables instead of creating duplicates.
- They automatically detect new columns and new partitions.
- Schema Evolution allows metadata to adapt as the data changes.
- Running the crawler again is necessary whenever new data or partitions are added.
- This automatic metadata maintenance is one of the biggest advantages of using Glue Crawlers in production.

---

# Next Section

**02.12 Best Practices**

In the next chapter, we'll cover the recommended practices for designing, organizing, and maintaining Glue Crawlers in real-world production environments, along with tips to improve performance, reduce costs, and avoid common pitfalls.

# AWS Glue Handbook

# 02.12 Best Practices

---

# Introduction

AWS Glue Crawlers are simple to create, but designing them correctly is important for building scalable, cost-effective, and maintainable data pipelines.

This chapter covers the best practices followed in production environments and highlights lessons we learned during our hands-on exercises.

---

# 1. Organize Your S3 Data Properly

A crawler works best when data is well organized.

Good Structure

```text
employee-data/

year=2026/

month=07/

employees.csv
```

Poor Structure

```text
employee-data/

abc/

xyz/

test/

employees.csv
```

Meaningful folder names help Glue detect partitions automatically.

---

# 2. Separate Raw and Processed Data

Never store raw and transformed files in the same folder.

Recommended Structure

```text
s3://company-data/

raw/

processed/

archive/
```

Benefits

- Easier management
- Prevents accidental processing
- Better security
- Cleaner pipelines

---

# 3. Use Meaningful Names

Avoid names like

```text
crawler1

abc

test
```

Instead use

```text
employee-crawler

sales-crawler

customer-crawler
```

Similarly,

Databases

```text
finance_db

sales_db

hr_db
```

Tables

```text
employees

orders

transactions
```

---

# 4. Follow Consistent Folder Structure

For time-series data

Preferred

```text
year=2026/

month=07/

day=15/
```

Avoid

```text
folder1/

folder2/

folder3/
```

Consistent folder structures improve partition discovery.

---

# 5. Use Partitioning for Large Datasets

Large datasets should always be partitioned.

Example

```text
sales/

year=2026/

month=07/
```

Benefits

- Faster Athena queries
- Lower query cost
- Better scalability

---

# 6. Schedule Crawlers Wisely

Don't run crawlers more often than necessary.

Example

Data arrives once per day

↓

Crawler should run once per day.

Avoid

```text
Every 5 Minutes
```

when no new data arrives.

---

# 7. Use On-Demand During Development

While learning or testing,

use

```text
On Demand
```

Benefits

- Easier debugging
- No unnecessary executions
- Lower cost

This is exactly what we used during our hands-on exercises.

---

# 8. Keep IAM Permissions Minimal

Follow the Principle of Least Privilege.

Grant only the permissions required.

Example

```text
Read S3

Write Glue Catalog

Write CloudWatch Logs
```

Avoid giving Administrator permissions unless absolutely necessary.

---

# 9. Monitor Crawlers with CloudWatch

Every crawler execution generates logs.

Use CloudWatch to investigate:

- Failed executions
- Permission errors
- Timeout issues
- Schema detection problems

CloudWatch should always be your first troubleshooting step.

---

# 10. Maintain Clean Data

Schema inference depends on data quality.

Good

```text
salary

50000

60000

70000
```

Poor

```text
salary

50000

ABC

70000
```

Mixed data types may cause Glue to infer an incorrect schema.

---

# 11. Keep File Formats Consistent

Avoid mixing formats within the same dataset.

Good

```text
employees/

employees1.csv

employees2.csv
```

Poor

```text
employees/

employees.csv

employees.json

employees.parquet
```

Consistency improves crawler accuracy.

---

# 12. Re-run Crawlers After Changes

Whenever you:

- Add new files
- Add new partitions
- Modify schema

Run the crawler again so the Glue Data Catalog stays up to date.

---

# 13. Review Schema Changes

In production,

don't blindly accept schema changes.

Example

Yesterday

```text
salary

double
```

Today

```text
salary

string
```

Unexpected changes should be investigated before affecting downstream systems.

---

# 14. Don't Use Crawlers for Data Cleaning

Remember

Crawler

✅ Reads metadata

Glue Job

✅ Cleans and transforms data

DataBrew

✅ Data preparation

Each service has a different responsibility.

---

# 15. Document Your Pipelines

Maintain documentation for:

- S3 Bucket
- Glue Database
- Crawler
- Glue Job
- Workflow
- EventBridge Rule
- Lambda Function

Good documentation simplifies maintenance and onboarding.

---

# Best Practices We Followed During Our Practice

While learning AWS Glue, we followed several good practices:

- Used **On-Demand** crawlers during development.
- Stored CSV files in Amazon S3.
- Verified metadata using the Glue Data Catalog.
- Queried data through Athena after the crawler completed.
- Learned that partition folders become partition columns automatically.
- Used meaningful Glue databases and table names whenever possible.

---

# Production Pipeline Example

```text
Application

↓

Amazon S3 (Raw)

↓

Glue Crawler

↓

Glue Data Catalog

↓

Glue Job

↓

Processed S3

↓

Athena

↓

QuickSight Dashboard
```

Every component has a clear responsibility.

---

# Common Mistakes to Avoid

❌ Running crawlers too frequently

❌ Giving excessive IAM permissions

❌ Mixing raw and processed data

❌ Ignoring CloudWatch logs

❌ Using poor folder structures

❌ Expecting crawlers to clean data

❌ Forgetting to re-run the crawler after adding new partitions

---

# Interview Questions

### Why should datasets be partitioned?

To improve query performance and reduce data scanned.

---

### Why should raw and processed data be separated?

To avoid accidental processing and maintain clean data pipelines.

---

### Why is CloudWatch important?

It stores execution logs that help diagnose crawler failures.

---

### Why should crawlers use least-privilege IAM roles?

To improve security and follow AWS best practices.

---

### Why should schema changes be reviewed?

Unexpected schema changes can break ETL jobs, Athena queries, or downstream applications.

---

# Revision Summary

```text
Organize S3 Properly

↓

Use Partitions

↓

Use Meaningful Names

↓

Least-Privilege IAM

↓

Clean Data

↓

Monitor CloudWatch

↓

Re-run Crawlers When Needed

↓

Keep Metadata Updated
```

---

# Key Takeaways

- Organize S3 data using meaningful folder structures.
- Separate raw and processed datasets.
- Use partitioning for large datasets.
- Prefer On-Demand execution during development.
- Schedule crawlers appropriately in production.
- Monitor crawler executions through CloudWatch.
- Maintain clean and consistent data for accurate schema inference.
- Follow the Principle of Least Privilege for IAM roles.
- Keep documentation up to date for easier maintenance.

---

# Next Section

**02.13 Common Errors**

In the next chapter, we'll cover the most common AWS Glue Crawler errors, the causes behind them, and the troubleshooting steps to resolve them quickly.



# AWS Glue Handbook

# 02.13 Common Errors

---

# Introduction

While working with AWS Glue Crawlers, you will encounter errors related to permissions, networking, schema detection, file formats, and metadata.

Most crawler failures fall into a few common categories. Understanding these errors makes troubleshooting much easier.

This chapter covers the most common errors, why they occur, and how to fix them.

---

# Error 1 – Access Denied

## Error Message

```text
Access Denied

AccessDeniedException

Permission denied
```

---

## Cause

The IAM Role attached to the crawler does not have permission to access the S3 bucket or update the Glue Data Catalog.

---

## Solution

Verify that the IAM Role has permissions to:

- Read the S3 bucket
- Update the Glue Data Catalog
- Write CloudWatch Logs

---

## Example

```text
Crawler

↓

Read S3

❌ Permission Denied

↓

Crawler Failed
```

---

# Error 2 – No Tables Created

## Symptom

Crawler completes successfully but no table appears.

---

## Possible Causes

- Wrong S3 path
- Empty folder
- Unsupported file format
- Incorrect Glue Database

---

## Solution

Check:

- Bucket name
- Folder path
- Uploaded files
- Database selection

---

# Error 3 – Crawler Failed

## Error Message

```text
Crawler Failed
```

---

## Possible Causes

- IAM issue
- Invalid S3 location
- Network problem
- Internal AWS error

---

## Solution

Always check:

```text
CloudWatch Logs
```

This is the first place to investigate.

---

# Error 4 – Athena Cannot Find Table

## Error Message

```text
Table does not exist
```

---

## Cause

Usually one of the following:

- Wrong Glue Database selected
- Crawler has not run yet
- Table was deleted

---

## Solution

Verify:

- Correct Athena database
- Table exists in Glue Catalog
- Crawler completed successfully

---

# Error 5 – Incorrect Schema Detected

## Example

CSV

```text
salary

50000

ABC

60000
```

Crawler detects

```text
salary

string
```

instead of

```text
double
```

---

## Cause

Mixed data types.

---

## Solution

Keep column values consistent.

---

# Error 6 – New Files Not Appearing

## Symptom

New CSV files are uploaded but Athena cannot see them.

---

## Cause

Crawler has not been executed again.

---

## Solution

Run the crawler again.

Remember:

Uploading files **does not automatically update metadata**.

---

# Error 7 – New Partitions Missing

## Example

```text
year=2025/

year=2026/
```

Athena only shows

```text
year=2025
```

---

## Cause

Crawler has not discovered the new partition.

---

## Solution

Re-run the crawler.

---

# Error 8 – Unsupported File Format

## Example

```text
random.xyz
```

---

## Cause

Glue cannot understand the file format.

---

## Solution

Use supported formats such as:

- CSV
- JSON
- Parquet
- ORC
- Avro

---

# Error 9 – Empty Table

## Symptom

Table exists but returns zero rows.

---

## Possible Causes

- Empty files
- Incorrect S3 path
- Wrong folder selected

---

## Solution

Verify:

- File contents
- Bucket path
- Athena query

---

# Error 10 – Timeout

## Error

```text
Crawler Timed Out
```

---

## Cause

Large datasets

or

Network connectivity problems.

---

## Solution

- Verify connectivity
- Optimize dataset organization
- Reduce unnecessary scans

---

# Error 11 – JDBC Connection Failed

## Error

```text
Unable to connect
```

---

## Cause

- Wrong endpoint
- Incorrect credentials
- Missing VPC configuration
- Security Group restrictions

---

## Solution

Verify:

- Database endpoint
- Username/password
- VPC
- Security Groups

---

# Error 12 – CloudWatch Logs Not Available

## Cause

IAM Role cannot write logs.

---

## Solution

Grant CloudWatch Logs permissions to the crawler's IAM Role.

---

# Error 13 – Duplicate Tables

## Symptom

Two similar tables appear.

---

## Cause

Crawler pointed to different folders or different databases.

---

## Solution

Use:

- Consistent crawler configuration
- Meaningful databases
- Proper folder organization

---

# Error 14 – Partition Not Used in Athena

## Symptom

Athena scans the entire dataset even when filtering.

---

## Cause

Data is not partitioned correctly, or the crawler has not detected the partitions.

---

## Solution

Use folder structures like:

```text
year=2026/

month=07/
```

and re-run the crawler.

---

# Error 15 – Wrong Folder Selected

## Example

Crawler points to

```text
s3://company-data/
```

instead of

```text
s3://company-data/employees/
```

---

## Result

Unexpected tables or schemas are created.

---

## Solution

Always verify the S3 location before running the crawler.

---

# During Our Practice

While learning AWS Glue, we encountered and discussed several practical issues:

- Athena showing old metadata until the crawler was re-run.
- Automatic partition detection only after the crawler scanned the new folders.
- Importance of using the correct Glue Database in Athena.
- The crawler creating metadata only—it never modified the original CSV files.

These experiences reflect common real-world troubleshooting scenarios.

---

# Troubleshooting Checklist

Before running a crawler, verify:

✅ Correct S3 bucket

✅ Correct folder path

✅ CSV or supported file format

✅ IAM Role attached

✅ Glue Database selected

✅ Files uploaded

✅ CloudWatch permissions

---

# Debugging Workflow

```text
Crawler Failed

↓

Read Error Message

↓

Open CloudWatch Logs

↓

Identify Root Cause

↓

Fix Configuration

↓

Run Again
```

---

# Best Practices

- Always review CloudWatch logs after failures.
- Keep S3 folder structures organized.
- Use supported file formats.
- Re-run crawlers after schema or partition changes.
- Keep IAM permissions up to date.
- Test changes in a development environment before production.

---

# Interview Questions

### What is the most common reason for an "Access Denied" error?

Missing IAM permissions.

---

### Why might Athena not show newly uploaded files?

Because the Glue Crawler has not been run again to update the metadata.

---

### Why can a numeric column become a string?

Because mixed data types were detected during schema inference.

---

### Where should you look first when a crawler fails?

CloudWatch Logs.

---

### Why are new partitions sometimes missing?

The crawler has not yet discovered and registered them in the Glue Data Catalog.

---

# Revision Summary

```text
Crawler Error

↓

CloudWatch Logs

↓

Identify Cause

↓

Fix Configuration

↓

Run Crawler Again

↓

Metadata Updated
```

---

# Key Takeaways

- Most crawler issues are related to IAM, S3 paths, schema changes, or unsupported formats.
- CloudWatch Logs should always be the first place to investigate failures.
- Re-running the crawler is required after adding new files or partitions.
- Clean and consistent data improves schema inference.
- Proper configuration and folder organization prevent many common errors.

# AWS Glue Handbook

# 02.14 Interview Questions

---

# Introduction

This chapter contains interview questions related to **AWS Glue Crawlers**. The questions progress from beginner to advanced and are designed for Data Engineer interviews.

> **Tip:** Try answering each question yourself before reading the answer.

---

# Beginner Level Questions

---

## Q1. What is an AWS Glue Crawler?

### Answer

An AWS Glue Crawler is a serverless service that scans a data source, automatically discovers its schema, and stores the metadata in the AWS Glue Data Catalog.

It does **not** modify or move the original data.

---

## Q2. What is the primary purpose of a Glue Crawler?

### Answer

To automate metadata discovery by:

- Detecting schema
- Creating tables
- Detecting partitions
- Updating the Glue Data Catalog

---

## Q3. Does a Glue Crawler perform ETL?

### Answer

No.

Glue Crawlers only discover metadata.

ETL is performed by AWS Glue Jobs.

---

## Q4. Where does a Glue Crawler store metadata?

### Answer

Inside the **AWS Glue Data Catalog**.

---

## Q5. Does a Glue Crawler copy data?

### Answer

No.

The original data always remains in Amazon S3 or the source database.

---

## Q6. What happens after a crawler finishes?

### Answer

It creates or updates metadata inside the Glue Data Catalog.

Services like Athena and Glue Jobs can immediately use that metadata.

---

## Q7. Which AWS service is most commonly used with Glue Crawlers?

### Answer

Amazon S3.

---

## Q8. Which query engine commonly uses the Glue Data Catalog?

### Answer

Amazon Athena.

---

## Q9. Can multiple services use the same Glue Catalog?

### Answer

Yes.

Examples include:

- Athena
- Glue Jobs
- EMR
- Redshift Spectrum
- Lake Formation

---

## Q10. What is Schema Inference?

### Answer

Schema Inference is the automatic process of detecting:

- Column names
- Data types
- Table structure

from the source data.

---

# Intermediate Level Questions

---

## Q11. What is Partition Discovery?

### Answer

Partition Discovery is the automatic detection of partition folders like

```text
year=2026/
month=07/
```

and storing them as partition metadata.

---

## Q12. Why is Partitioning important?

### Answer

Partitioning:

- Improves query performance
- Reduces Athena query cost
- Reduces data scanned

---

## Q13. Can a Glue Crawler update an existing table?

### Answer

Yes.

It can:

- Add new columns
- Detect new partitions
- Update metadata

without creating duplicate tables.

---

## Q14. What happens if new files are added?

### Answer

The crawler scans the new files and updates the existing metadata.

---

## Q15. Can a Glue Crawler connect to databases?

### Answer

Yes.

Examples:

- MySQL
- PostgreSQL
- Oracle
- SQL Server
- Amazon RDS
- Redshift

---

## Q16. How does Glue connect to databases?

### Answer

Using JDBC connections.

---

## Q17. What IAM permissions are required?

### Answer

Typically:

- Read S3
- Write Glue Catalog
- Write CloudWatch Logs

---

## Q18. What happens if IAM permissions are missing?

### Answer

The crawler fails with an Access Denied error.

---

## Q19. Does a crawler read every record?

### Answer

No.

It samples the data to infer the schema.

---

## Q20. What is Schema Evolution?

### Answer

Schema Evolution is the process of handling schema changes over time, such as new columns or updated structures.

---

#
