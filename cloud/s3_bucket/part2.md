# Amazon S3 – Notes (Part 2)
**Source:** S3 Introduction 2 | Data Engineering Series

---

## 1. Where S3 Fits in the Data Engineering Pipeline

The pipeline has 4 stages: **Generate → Ingest → Process → Analyze/Consume**

### Stage-wise S3 Role:

| Stage | What Happens | S3's Role |
|---|---|---|
| **Generate** | Data from OLTP DBs (RDS, Aurora, DynamoDB), streaming apps, CDC tools (DMS, Debezium) | — |
| **Ingest** | Kafka / Kinesis collect data | **S3 as Data Lake** — stores raw data (CSV, JSON, Parquet, Avro, etc.) |
| **Process** | Spark, Glue, Flink, Lambda, EMR transform data | **S3 as both source and destination** for processed data |
| **Analyze** | Athena, PowerBI, Tableau, Redshift read processed data | **S3 as query target** (Athena runs directly on S3) |

> S3 appears **twice** in the pipeline — once for raw data, once for processed/transformed data.

### Processed Data Storage Format:
- **Raw layer:** CSV, JSON, Parquet, Avro (any format)
- **Processed layer:** Mostly **Parquet / ORC / columnar formats** (optimized for analytics)

### Additional S3 Integration Points:
- **Load into Redshift** → using `COPY` command
- **Load into Aurora / MySQL / PostgreSQL / DynamoDB** → directly from S3
- **Use S3 instead of HDFS** → Spark & Glue can use S3 as distributed storage source
- **Export from Redshift/Athena to S3** → using `UNLOAD` command
- **Export from DynamoDB/Aurora** → directly to S3
- S3 also feeds into **ML pipelines** as the source of processed/feature data

---

## 2. S3 Use Cases (Data Engineering Focus)

### ① Data Lake
- Store **raw + processed data** in any file format
- S3 is the **go-to Data Lake storage on AWS**

### ② Lake House
- Combination of Data Lake + Data Warehouse features
- Enabled by **Open Table Formats**: **Apache Iceberg, Apache Hudi, Delta Lake**
- These table formats are stored directly **on top of S3**
- Tools like Spark, Athena can write/read Iceberg or Hudi tables from S3

### ③ Distributed Storage (HDFS Replacement)
- **HDFS** (Hadoop Distributed File System) = breaks large files into blocks, stores across Data Nodes, with a Name Node for metadata
- **S3 can fully replace HDFS** in Spark/Glue workflows
- S3 is a **superset of HDFS** — it does everything HDFS does and more

### ④ Data Archival
- Archive old data from OLTP stores (MySQL, Oracle, PostgreSQL, SQL Server) to S3 → keep OLTP lean and performant
- Archive from Redshift → S3 using `UNLOAD`, then query with Athena
- Archive from DynamoDB → S3 using Table Export
- Use case: bank statements, compliance data (store 5+ years in S3, query with Athena)

### Other Use Cases (not DE-focused):
- **Backup storage** — EBS, RDS, Aurora, Redshift, DynamoDB all use S3 for backups
- **Application log storage** — historical audit/logs
- **Media content** — Netflix stores videos on S3; Amazon.com product images served from S3; YouTube equivalent uses GCS

> ⚠️ Focus for this course: **Data Lake, Lake House, Distributed Storage, Data Archival**

---

## 3. Object Store vs Block Store

### Block Store (e.g., AWS EBS)
- Data is read/written in **fixed-size blocks** (block size defined by OS/device)
- Example: 4 KB block size, 1 KB record → **4 records per block**
- Write: data goes to RAM first → then flushed to disk as a block
- Read: even if you need 1 record, the **entire block is read**
- Used by: databases (MySQL, PostgreSQL), applications that need low-latency random read/write

### Object Store (S3)
- Data is read/written as a **whole file/object** — not block by block
- Cannot do partial/block-level reads or writes
- You upload a full file; you download a full file

### Can You Build Object Store on Block Store?
- **Yes** — S3 itself uses block storage underneath (the discs on S3 nodes are block storage)
- But managing millions of discs manually (scaling, availability, replication) is extremely complex → that's why we use S3 as a managed service

### Why S3 ≠ Block Store for Applications/Databases:
- MySQL/PostgreSQL write and read **block by block**
- S3 doesn't support block-level operations
- ❌ **Cannot use S3 as storage for a MySQL database on EC2** — use **EBS** instead
- ✅ **Can use S3** as distributed storage for Spark, Glue, analytics workloads

### HDFS vs S3 (Object Store angle):
- HDFS also stores data + metadata + a form of object key → can be thought of as an object store
- **S3 is a superset of HDFS** with far more managed features

---

## Key Takeaways

- S3 is present at **every stage** of the AWS data engineering pipeline
- S3 supports 4 major DE use cases: **Data Lake → Lake House → Distributed Storage → Archival**
- Object store = **whole file reads/writes**; Block store = **partial block-level reads/writes**
- S3 uses block storage internally but exposes an **object store API**
- Cannot use S3 for database/application storage (no block-level access); use EBS for that
- S3 is a **superset of HDFS** and can replace it in Spark/Glue pipelines

---
*Notes from Part 2 of the S3 for Data Engineers playlist.*
