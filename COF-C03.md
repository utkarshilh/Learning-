# Snowflake SnowPro Core (COF-C03) — Complete Study Notes

> **Exam at a glance**
> - Format: Multiple choice, ~100 questions
> - Duration: 115 minutes
> - Passing score: ~750/1000
> - Validity: 2 years
> - Prerequisites: 6+ months hands-on Snowflake experience, basic SQL
> - Live since: February 16, 2026 (successor to COF-C02)

---

## DOMAIN 1 — Snowflake AI Data Cloud Features and Architecture

### 1.1 Three-Layer Architecture

Snowflake's architecture separates storage, compute, and services — this is its defining feature.

| Layer | What it Does |
|---|---|
| **Cloud Services** | Brain of Snowflake. Handles authentication, query parsing, optimization, metadata, access control. Always on; billed at ~10% of daily compute. |
| **Compute (Virtual Warehouses)** | MPP clusters that execute queries. Separated from storage — no caching contention between different workloads. |
| **Database Storage** | Columnar, compressed micro-partitions stored in cloud object storage (S3, Azure Blob, GCS). Managed by Snowflake. |

**Key concept:** Because storage and compute are separate, you can scale each independently, and multiple warehouses can query the same data simultaneously.

---

### 1.2 Snowflake Editions

| Edition | Key Features |
|---|---|
| **Standard** | Core SQL, Time Travel (1 day), basic features |
| **Enterprise** | Multi-cluster warehouses, Time Travel up to 90 days, materialized views, data masking |
| **Business Critical** | HIPAA, PCI-DSS, PHI support, private connectivity (PrivateLink), enhanced encryption |
| **Virtual Private** | Dedicated Snowflake environment, highest isolation |

> **Exam tip:** Multi-cluster warehouses and 90-day Time Travel require **Enterprise or higher**.

---

### 1.3 Snowflake Interfaces

- **Snowsight** — Web UI; supports worksheets, dashboards, data profiling, Notebooks
- **Snowflake CLI** — Command-line tool for scripting, automation, CI/CD pipelines
- **IDE Integrations** — VS Code extension for authoring and running SQL/Snowpark code

---

### 1.4 Object Hierarchy

```
Organization
  └── Account
        └── Database
              └── Schema
                    └── Tables, Views, Stages, Pipes, Streams, Tasks,
                        UDFs, Stored Procedures, File Formats, Sequences,
                        ML Models, Applications, Shares
```

- **Session variables** are scoped to a user session
- **Parameter hierarchy** (most specific wins): Session > User > Account > Snowflake default

---

### 1.5 Virtual Warehouses

**Types:**
- **Standard (Gen 1 & Gen 2)** — General-purpose. Gen 2 uses Snowpark Container Services internally.
- **Snowpark Optimized** — Higher memory-per-node ratio; designed for ML training, large Snowpark DataFrame operations

**Sizes:** X-Small → X-Large → 2X-Large … 6X-Large. Each size up doubles compute (and cost).

**Scaling:**
- **Scale UP (resize):** Handle complex queries — bigger warehouse = more CPU/memory per node
- **Scale OUT (multi-cluster):** Handle high concurrency — more clusters = more parallel queries. Enterprise+ only.
  - **Economy mode:** Minimize cost; adds clusters conservatively
  - **Auto mode:** Minimize latency; adds clusters aggressively

**Auto-Suspend / Auto-Resume:**
- Auto-Suspend: Suspends after X seconds of inactivity (default 600s). Stops billing immediately.
- Auto-Resume: Automatically starts when a query arrives.

**Use-case sizing guide:**
| Workload | Recommendation |
|---|---|
| Ad-hoc / exploratory | Small warehouse, auto-suspend |
| ETL / data loading | Separate warehouse from BI |
| High concurrency BI | Multi-cluster warehouse |
| Complex ML / large joins | Snowpark Optimized or large size |

---

### 1.6 Storage Concepts

#### Micro-partitions
- Snowflake stores data in compressed columnar **micro-partitions** (~50–500 MB uncompressed each)
- Each partition has min/max metadata per column → enables **partition pruning**
- Automatic; no manual DBA management needed

#### Data Clustering
- By default, data is clustered by insertion order
- **Cluster keys** allow re-ordering data for better pruning on filter columns
- Use for large tables with frequent range/equality filters on specific columns
- Monitored via `SYSTEM$CLUSTERING_INFORMATION()`
- **Automatic Clustering** is a background service that continuously re-clusters; billed separately

#### Table Types

| Type | Persistence | Time Travel | Fail-safe | Cost |
|---|---|---|---|---|
| **Permanent** | Until dropped | 0–90 days | 7 days | Highest |
| **Temporary** | Session only | 0–1 day | None | Low |
| **Transient** | Until dropped | 0–1 day | None | Medium |
| **External** | Cloud storage | None | None | Storage only |
| **Apache Iceberg** | Cloud storage | Limited | None | External |
| **Dynamic** | Materialized from query | Per config | Per config | Medium |

#### View Types

| Type | Notes |
|---|---|
| **Standard** | Stored SQL; no caching |
| **Materialized** | Query results cached and refreshed automatically; faster reads; Enterprise+ |
| **Secure** | Hides DDL from non-owners; used for sharing sensitive data |

---

### 1.7 AI/ML and Application Development

| Feature | Purpose |
|---|---|
| **Snowflake Notebooks** | Interactive Python/SQL notebooks running inside Snowflake |
| **Streamlit in Snowflake** | Build and deploy data apps directly inside Snowflake |
| **Snowpark** | Developer framework to write Python/Java/Scala code that runs inside Snowflake (pushdown compute) |
| **Snowflake Cortex** | LLM-powered AI features |
| **Cortex AI SQL Functions** | `COMPLETE()`, `SUMMARIZE()`, `SENTIMENT()`, `TRANSLATE()`, etc. run directly in SQL |
| **Cortex Search** | Hybrid (semantic + keyword) search over Snowflake data |
| **Cortex Analyst** | Natural language to SQL interface |
| **Snowflake ML** | Feature Store, Model Registry, AutoML functions (e.g., `ML.FORECAST`, `ML.ANOMALY_DETECTION`) |

> **COF-C03 specific:** This exam has significantly more AI/ML questions than COF-C02. Know Cortex features.

---

## DOMAIN 2 — Account Management and Data Governance

### 2.1 Security Model

#### Role-Based Access Control (RBAC)
- Access is granted to **roles**, roles are assigned to **users** (or other roles)
- Objects are owned by roles; owners can grant privileges

#### System-Defined Roles (memorize this hierarchy)

```
ORGADMIN
  └── ACCOUNTADMIN
        ├── SYSADMIN
        │     └── Custom roles (for objects)
        └── SECURITYADMIN
              └── USERADMIN
                    └── PUBLIC (all users inherit)
```

| Role | Purpose |
|---|---|
| `ORGADMIN` | Manage organization-level settings |
| `ACCOUNTADMIN` | Top account role; full access; use sparingly |
| `SECURITYADMIN` | Manage users, roles, network policies |
| `USERADMIN` | Create/manage users and roles only |
| `SYSADMIN` | Create databases, warehouses, objects |
| `PUBLIC` | Default role; all users have this |

**Best practice:** Never use ACCOUNTADMIN for day-to-day work. Create custom roles.

#### Discretionary Access Control (DAC)
- Object owners can grant their privileges to others
- Combines with RBAC in Snowflake

#### Account Roles vs Database Roles
- **Account roles** — scoped to the entire account
- **Database roles** — scoped within a specific database; granted to account roles

#### Secondary Roles
- Users can activate multiple roles simultaneously using `USE SECONDARY ROLES ALL`
- Allows combining permissions from multiple roles in one session

---

### 2.2 Authentication Methods

| Method | Notes |
|---|---|
| **Password** | Default |
| **MFA** | Adds TOTP (e.g., Duo) on top of password |
| **Federated/SSO** | SAML 2.0 identity provider (Okta, Azure AD) |
| **OAuth** | Token-based; used by BI tools (Tableau, Power BI) |
| **Key-pair** | RSA key pair; used for programmatic/service accounts |

**Network Policies:** Restrict account/user access by IP allowlist/denylist. Set at account or user level.

---

### 2.3 Data Governance Features

#### Data Masking
- **Dynamic Data Masking** — Column-level masking policies that return masked values based on role at query time. No data is changed at rest.
- **Row Access Policies** — Row-level security; filter rows dynamically based on role or user attributes
- **Object Tagging** — Attach metadata tags to databases, schemas, tables, columns; used for classification and governance

#### Privacy Policies
Applied to columns to govern how data can be queried; works with masking.

#### Trust Center
Snowflake's unified security monitoring hub; shows security posture, compliance findings, and recommendations.

#### Encryption Key Management
- All data encrypted at rest and in transit by default (AES-256)
- **Tri-Secret Secure** (Business Critical+): Customer-managed key + Snowflake key; requires both to decrypt

#### Data Replication and Failover
- **Database Replication** — Replicate databases across regions/clouds
- **Business Continuity / Failover** — Promote secondary account to primary if primary fails; requires Business Critical+

#### Data Lineage
Track how data flows through pipelines; viewable in Snowsight's data graph.

#### Alerts & Notifications
- **Alerts** — SQL condition-based alerting using a warehouse to evaluate
- **Notifications** — Integrations with email, Slack, webhooks for pipeline events

---

### 2.4 Monitoring and Cost Management

#### Resource Monitors
- Set credit quotas on warehouses or the account
- Actions: Notify, Notify & Suspend, Notify & Suspend Immediately
- Applied at: Account level or warehouse level

#### Credit Usage Calculation
- Warehouse credits = (warehouse size factor) × (hours running)
- X-Small = 1 credit/hour; each size up doubles it (Small=2, Medium=4, Large=8, X-Large=16, 2X=32 …)
- Billed per-second with a **60-second minimum** per start

#### ACCOUNT_USAGE Schema
- In the `SNOWFLAKE` database; contains views with historical account data (up to 1 year)
- Key views: `QUERY_HISTORY`, `WAREHOUSE_METERING_HISTORY`, `LOGIN_HISTORY`, `ACCESS_HISTORY`, `COPY_HISTORY`
- Latency: 45 minutes to 3 hours depending on view

---

## DOMAIN 3 — Data Loading, Unloading, and Connectivity

### 3.1 Data Loading & Unloading

#### File Formats Supported
CSV, JSON, Avro, ORC, Parquet, XML

#### Stages

| Type | Description |
|---|---|
| **User Stage** (`@~`) | Per-user; not shareable |
| **Table Stage** (`@%tablename`) | Per-table; not shareable |
| **Named Internal Stage** (`@mystage`) | Shared; created explicitly |
| **Named External Stage** | Points to S3/Azure/GCS bucket |

**Directory Tables:** List files in a stage like a table; supports automatic metadata refresh.

#### COPY INTO
```sql
-- Load from stage into table
COPY INTO my_table FROM @my_stage/path/
  FILE_FORMAT = (TYPE = 'CSV' FIELD_DELIMITER = ',' SKIP_HEADER = 1)
  ON_ERROR = 'CONTINUE';   -- Options: ABORT_STATEMENT, CONTINUE, SKIP_FILE

-- Unload from table to stage
COPY INTO @my_stage/output/ FROM my_table
  FILE_FORMAT = (TYPE = 'PARQUET');
```

**Error Handling Options:**
- `ABORT_STATEMENT` — Roll back entire load on first error (default)
- `CONTINUE` — Skip bad rows, continue loading
- `SKIP_FILE` — Skip the entire file with errors

#### Validation Before Loading
```sql
COPY INTO my_table FROM @my_stage VALIDATION_MODE = 'RETURN_ERRORS';
```

---

### 3.2 Automated Data Ingestion

#### Snowpipe
- Micro-batch, continuous loading; triggered by cloud event notifications (SQS/SNS for S3)
- Uses **serverless compute** (no warehouse needed)
- Files loaded within ~1 minute of arriving in stage
- `COPY INTO` under the hood

#### Snowpipe Streaming
- Row-level streaming using the Snowflake Ingest SDK
- Lower latency than Snowpipe; for real-time use cases
- Data lands in in-memory buffer → flushed to table

#### Streams
- **Change Data Capture (CDC)** on tables
- Captures INSERT, UPDATE, DELETE changes with metadata columns: `METADATA$ACTION`, `METADATA$ISUPDATE`, `METADATA$ROW_ID`
- Types: **Standard** (DML changes), **Append-only** (inserts only — more efficient), **Insert-only** (for external tables)

#### Tasks
- Schedule SQL or Snowpark code on a cron or time interval
- Can form **DAG pipelines** (task trees with dependencies)
- Use serverless or user-managed warehouse compute

#### Dynamic Tables
- Declarative incremental materialization
- Define a query; Snowflake automatically refreshes when upstream data changes
- Simplifies streaming/batch pipeline orchestration
- Target lag: define how stale the data can be

---

### 3.3 Connectors and Integrations

| Integration | Purpose |
|---|---|
| **JDBC / ODBC Drivers** | Connect BI tools, applications |
| **Python Connector** | Python applications |
| **Kafka Connector** | Ingest from Apache Kafka via Snowpipe |
| **Spark Connector** | Read/write Snowflake from Apache Spark |
| **Storage Integration** | Secure access to external cloud storage without storing keys |
| **API Integration** | Connect external APIs for external functions or Snowpipe REST |
| **Git Integration** | Link GitHub/GitLab repos; sync Snowpark code, notebooks |

---

## DOMAIN 4 — Performance Optimization, Querying, and Transformation

### 4.1 Query Performance Analysis

#### Query Profile
Available in Snowsight for every executed query. Shows:
- **Operator tree** — Step-by-step execution plan
- **Bytes spilled to remote/local storage** — Indicates warehouse too small; resize up
- **Inefficient pruning** — Micro-partitions not being skipped; add clustering key
- **Exploding joins** — Cartesian product / many-to-many join producing huge intermediate results
- **Queuing** — Query waited for warehouse resources; scale out or resize

#### Query Insights
Snowsight's AI-powered summary of Query Profile findings — highlights top bottlenecks.

#### ACCOUNT_USAGE Views for Query Analysis
- `QUERY_HISTORY` — All queries with performance metrics
- `WAREHOUSE_LOAD_HISTORY` — Warehouse queue depth over time
- `QUERY_ATTRIBUTION_HISTORY` — Associate queries to cost centers

---

### 4.2 Optimization Techniques

#### Query Acceleration Service (QAS)
- Offloads parts of large, unpredictable queries to serverless compute
- Good for ad-hoc analytics with variable data scan sizes
- Enabled per warehouse; scale factor controls max serverless resources

#### Search Optimization Service (SOS)
- Builds a persistent index to speed up point lookups and substring searches
- Great for: equality filters, IN predicates, substring/regex on string columns
- Added per table, per column; billed for storage and maintenance

#### Clustering Keys
- Re-order micro-partitions on specified columns
- Improves pruning for range/equality filters
- Best for: large tables (billions of rows), stable, repeated filter patterns
- `ALTER TABLE t CLUSTER BY (col1, col2);`

#### Materialized Views
- Pre-computed query results; auto-refreshed in background
- Faster reads for expensive aggregation queries
- Enterprise+ only; billed for storage and background refresh compute

---

### 4.3 Caching (3 Types — memorize!)

| Cache | Scope | What it Stores | Expires |
|---|---|---|---|
| **Query Result Cache** | Account-wide | Exact query results | 24 hours (resets if data changes) |
| **Metadata Cache** | Cloud Services | Table stats, partition counts | Persistent |
| **Warehouse (Local Disk) Cache** | Per warehouse | Decompressed column data from storage | Cleared on suspend |

> **Exam tip:** Query result cache is used if the same SQL runs, the data hasn't changed, and the warehouse setting allows it. Warehouse cache is lost on suspend — avoid unnecessary suspend/resume to leverage it.

---

### 4.4 Data Transformation

#### Structured Data
Standard SQL tables with defined schemas.

#### Semi-Structured Data
- Stored natively as `VARIANT`, `OBJECT`, `ARRAY` types
- Load JSON, Avro, Parquet directly without pre-defining schema
- Query with dot notation: `SELECT v:customer.name::STRING FROM my_table`
- `FLATTEN()` function to explode arrays into rows
- `PARSE_JSON()`, `OBJECT_CONSTRUCT()`, `ARRAY_AGG()` are key functions

#### Unstructured Data
- Store files (PDFs, images, audio) in stages
- Reference via `BUILD_SCOPED_FILE_URL()` or `GET_PRESIGNED_URL()`

#### Key SQL Functions to Know
```sql
-- Window functions
SELECT name, dept, salary,
  RANK() OVER (PARTITION BY dept ORDER BY salary DESC) as dept_rank,
  SUM(salary) OVER (PARTITION BY dept) as dept_total
FROM employees;

-- Aggregate functions
GROUP BY GROUPING SETS ((col1), (col2), ())  -- flexible GROUP BY
GROUP BY ROLLUP (col1, col2)                 -- hierarchical subtotals
GROUP BY CUBE (col1, col2)                   -- all combinations

-- Semi-structured
SELECT src:order_id::INT,
       src:items[0]:name::STRING
FROM raw_orders;
```

---

## DOMAIN 5 — Data Collaboration

### 5.1 Time Travel and Fail-safe

#### Time Travel
- Query historical data up to N days in the past
- Editions: Standard = max 1 day; Enterprise+ = max 90 days
```sql
SELECT * FROM orders AT (TIMESTAMP => '2024-01-15 09:00:00'::TIMESTAMP);
SELECT * FROM orders BEFORE (STATEMENT => '<query_id>');
SELECT * FROM orders AT (OFFSET => -3600);  -- 1 hour ago
```
- `UNDROP TABLE/SCHEMA/DATABASE` — restore dropped objects within Time Travel window

#### Fail-safe
- 7-day additional retention period **after** Time Travel expires
- Only Snowflake support can recover data (not accessible via SQL)
- For Permanent tables only; not Transient or Temporary

#### Timeline
```
Data changed → [ Time Travel window (1-90 days) ] → [ Fail-safe (7 days) ] → Gone
```

---

### 5.2 Cloning

```sql
CREATE TABLE orders_backup CLONE orders;
CREATE DATABASE dev_db CLONE prod_db;
CREATE SCHEMA test_schema CLONE prod_schema AT (TIMESTAMP => ...);
```

- **Zero-copy clone** — No data is copied; cloned object shares micro-partitions with source
- Changes to clone or source create new micro-partitions (copy-on-write)
- Clones include all child objects (tables, views, etc.) when cloning a database/schema
- **Does NOT clone:** Grants/privileges, pipes, tasks (stopped), external stages

---

### 5.3 Secure Data Sharing

#### How It Works
- Provider account shares **metadata pointers** to its storage — no data is copied
- Consumer queries run on **provider's storage**
- Consumer pays for their own compute; provider pays for storage

#### Share Types
| Type | Description |
|---|---|
| **Direct Share** | Share with specific Snowflake accounts |
| **Snowflake Marketplace** | Public or private listings; data products |
| **Data Exchange** | Invite-only hub for a group of accounts |
| **Data Clean Room** | Privacy-preserving joint analysis without exposing raw data |

#### Reader Accounts
- For consumers who don't have a Snowflake account
- Created and managed (and billed) by the provider
- Consumer queries on reader accounts are billed to the **provider**

#### Resharing
- Consumers can reshare received data to other accounts (if allowed by provider)

---

### 5.4 Snowflake Marketplace and Native Apps

#### Marketplace
- Discover and access third-party data sets and services
- Listings can be: **Free**, **Paid** (through Snowflake), or **Custom price**
- **Public listing** — visible to all Snowflake customers
- **Private listing** — shared with specific accounts

#### Native Apps (Snowflake Native App Framework)
- Developers package Snowpark code + Streamlit UI + data as installable apps
- Apps run in the **consumer's account** with controlled access
- Provider code runs in consumer account but stays encapsulated

---

## Quick Reference: Key Numbers and Limits

| Item | Value |
|---|---|
| Time Travel — Standard | 0–1 day |
| Time Travel — Enterprise+ | 0–90 days |
| Fail-safe period | 7 days (permanent tables only) |
| Query Result Cache TTL | 24 hours |
| Warehouse credit minimum billing | 60 seconds |
| Cloud Services billing threshold | 10% of daily compute; above = billed |
| ACCOUNT_USAGE data latency | 45 min – 3 hours |
| ACCOUNT_USAGE retention | 1 year |
| Max clustering key columns | 3–4 recommended |

---

## Key SQL Commands Cheat Sheet

```sql
-- Warehouse control
ALTER WAREHOUSE my_wh SUSPEND;
ALTER WAREHOUSE my_wh RESUME;
ALTER WAREHOUSE my_wh SET WAREHOUSE_SIZE = 'LARGE';
ALTER WAREHOUSE my_wh SET MAX_CLUSTER_COUNT = 5 MIN_CLUSTER_COUNT = 1;

-- Time Travel
SELECT * FROM t AT (OFFSET => -3600);
UNDROP TABLE my_table;

-- Cloning
CREATE TABLE t2 CLONE t1;

-- Streams and Tasks
CREATE STREAM s ON TABLE orders;
CREATE TASK t WAREHOUSE = my_wh SCHEDULE = '5 MINUTE'
  WHEN SYSTEM$STREAM_HAS_DATA('s')
  AS INSERT INTO dest SELECT * FROM s WHERE METADATA$ACTION = 'INSERT';

-- COPY and Snowpipe
COPY INTO target FROM @stage FILE_FORMAT = (TYPE='JSON') ON_ERROR='CONTINUE';
ALTER PIPE my_pipe REFRESH;

-- Secure data sharing
CREATE SHARE my_share;
GRANT USAGE ON DATABASE d TO SHARE my_share;
ALTER SHARE my_share ADD ACCOUNTS = org.consumer_account;

-- Data masking
CREATE MASKING POLICY email_mask AS (val STRING) RETURNS STRING ->
  CASE WHEN CURRENT_ROLE() IN ('ANALYST') THEN val
       ELSE '***MASKED***'
  END;
ALTER TABLE customers MODIFY COLUMN email SET MASKING POLICY email_mask;

-- Row access policy
CREATE ROW ACCESS POLICY region_policy AS (region_col STRING) RETURNS BOOLEAN ->
  region_col = CURRENT_USER();
```

---

## Exam Tips

1. **Architecture questions** are very common — know the 3 layers and what each does.
2. **Caching** — know all 3 types and when each is used/invalidated.
3. **RBAC hierarchy** — know all system-defined roles and their purposes.
4. **Time Travel vs Fail-safe** — understand the difference; Fail-safe is not user-accessible.
5. **Table types** — transient vs temporary vs permanent; when to use each.
6. **Scaling up vs out** — up for complex queries, out for concurrency.
7. **COF-C03 focus** — AI/Cortex features are heavily tested; know Cortex functions, Snowpark, Notebooks.
8. **Streams and Tasks** — understand CDC use cases and scheduling.
9. **Data sharing** — know provider/consumer model, reader accounts, Marketplace.
10. **Micro-partitions and pruning** — foundational to performance optimization.

---

*Based on the official COF-C03 exam guide (January 2026). Good luck!*
