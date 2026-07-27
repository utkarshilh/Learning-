# Amazon EMR & Apache Spark Fundamentals

> Covers:
> - Why EMR
> - Distributed Computing
> - Amazon EMR
> - Hadoop
> - MapReduce
> - Spark
> - EMR Cluster
> - Master/Core/Task Nodes
> - YARN
> - Driver & Executor
> - Spark Application
> - Lazy Evaluation

---

# 1. Why Amazon EMR?

## Problem

- Large datasets (TB/PB) cannot be efficiently processed by a single machine.
- Vertical scaling has hardware limits and becomes expensive.

## Solution

- Use multiple machines (Horizontal Scaling).
- Process data in parallel.

```text
100 GB

↓

10 Machines

↓

10 GB each

↓

Parallel Processing

↓

Final Result
```

Amazon EMR automates the creation and management of these machines.

---

# 2. Distributed Computing

**Definition**

Processing one large dataset using multiple computers working together.

---

# 3. Amazon EMR

**Definition**

Amazon EMR is a managed AWS service that creates, configures and manages EC2 clusters for running distributed data processing frameworks like Spark and Hadoop.

## Responsibilities

- Create EC2 Cluster
- Install Spark/Hadoop
- Configure Cluster
- Scale Cluster
- Monitor Cluster

> **Remember**
>
> **EMR manages the cluster.**
>
> **EMR does NOT process data.**

---

# 4. Apache Hadoop

**Definition**

Hadoop is an open-source ecosystem for distributed storage and distributed processing of large datasets.

## Components

- HDFS → Distributed Storage
- YARN → Resource Manager
- MapReduce → Processing Model

> Hadoop is an **ecosystem**, not a single software.

---

# 5. MapReduce

## Map

- Split work
- Process independently

## Reduce

- Collect partial results
- Combine into final output

---

# 6. Apache Spark

**Definition**

Apache Spark is a distributed data processing framework optimized for fast in-memory computation.

## Responsibilities

- Read Data
- Process Data
- Perform Transformations
- Execute Computation

> Spark processes data.

---

# 7. Responsibilities

| Component | Responsibility |
|-----------|----------------|
| S3 | Store Data |
| EC2 | Compute |
| EMR | Manage Cluster |
| Spark | Process Data |
| Hadoop | Ecosystem |
| MapReduce | Split & Combine |
| YARN | Resource Management |

---

# 8. Spark vs Hadoop

| Hadoop | Spark |
|----------|-------|
| Ecosystem | Processing Framework |
| Includes HDFS, YARN, MapReduce | Performs Distributed Processing |
| Provides Storage + Resource Management | Provides Fast Computation |

> Spark commonly replaces **Hadoop MapReduce**, **not Hadoop itself**.

---

# 9. EMR Cluster

**Definition**

An EMR Cluster is a collection of EC2 instances managed by Amazon EMR.

```text
EMR Cluster

↓

EC2
EC2
EC2
EC2
```

---

# 10. EMR Node Types

## Master Node

Responsibilities

- Manage Cluster
- Schedule Jobs
- Monitor Nodes

---

## Core Node

Responsibilities

- Process Data
- Store Data (HDFS)

---

## Task Node

Responsibilities

- Process Data Only
- No Storage
- Optional
- Can be added or removed easily

---

## Summary

```text
Master → Manage

Core → Store + Process

Task → Process Only
```

---

# 11. YARN

**Definition**

YARN (Yet Another Resource Negotiator) is Hadoop's resource management system.

## Responsibilities

- Allocate CPU
- Allocate Memory
- Schedule Jobs
- Launch Containers
- Monitor Resources

> **Spark requests resources.**
>
> **YARN allocates resources.**

---

# 12. Driver & Executor

## Driver

**Definition**

The Driver is the main Spark process that coordinates the Spark Application.

### Responsibilities

- Read Spark Code
- Create Execution Plan
- Request Resources from YARN
- Assign Tasks
- Monitor Execution
- Handle Failures

---

## Executor

**Definition**

Executors are worker processes that execute Spark tasks.

### Responsibilities

- Read Data
- Execute Tasks
- Cache Data
- Return Results

---

## Remember

```text
Driver → Plans

Executor → Executes
```

---

# 13. Spark Application

**Definition**

One complete execution of a Spark program.

## Rule

> **One spark-submit = One Spark Application**

Contains

- Driver
- Executors
- SparkContext

---

## Relationship with EMR

```text
EMR Cluster

↓

Application 1

↓

Application 2

↓

Application 3
```

The cluster remains running.

Applications are temporary.

---

# 14. Lazy Evaluation

## Definition

Spark delays execution until an **Action** is encountered.

Instead of executing immediately, Spark builds an optimized execution plan.

---

## Transformations (Lazy)

- filter()
- select()
- groupBy()
- join()
- withColumn()
- orderBy()
- drop()

These only build the plan.

No execution occurs.

---

## Actions

- show()
- count()
- collect()
- first()
- take()
- write()
- save()

These trigger execution.

---

## Flow

```text
Transformations

↓

Logical Plan

↓

Action

↓

Job Created

↓

Execution Starts
```

---

# 15. Overall Architecture

```text
                    S3
                     │
                     ▼
                   EMR
                     │
                     ▼
               EC2 Cluster
                     │
                     ▼
                   YARN
             (Resource Manager)
                     │
                     ▼
              Spark Driver
          (Creates Execution Plan)
                     │
                     ▼
            Spark Executors
        (Read & Process Data)
                     │
                     ▼
                    S3
```

---

# Interview One-Liners

- EMR manages clusters.
- EMR does not process data.
- Spark processes data.
- Hadoop is an ecosystem.
- MapReduce splits and combines work.
- YARN allocates CPU and memory.
- Driver creates the execution plan.
- Executors execute tasks.
- One `spark-submit` = One Spark Application.
- Transformations are lazy.
- Actions trigger execution.

---

# Quick Revision

```text
S3
│
├── Stores Data

EC2
│
├── Provides Compute

EMR
│
├── Creates & Manages Cluster

YARN
│
├── Allocates CPU & Memory

Driver
│
├── Plans Execution

Executor
│
├── Executes Tasks

Spark
│
├── Processes Data

Application
│
├── One spark-submit

Transformation
│
├── Build Plan

Action
│
├── Execute Job
```




# Amazon EMR & Apache Spark Handbook
## Part 2 - Spark Execution Internals

> Covers:
> - Spark Job
> - Spark Stage
> - Spark Task
> - DAG (Directed Acyclic Graph)
> - Catalyst Optimizer

---

# 1. Spark Job

## Definition

A **Spark Job** is a unit of work created whenever an **Action** is triggered.

## Rule

> **One Action = One Job**

## Examples

### Example 1

```python
df.filter(...)
df.select(...)
df.show()
```

- Transformations → filter(), select()
- Action → show()

Result:

```
1 Application
      │
      ▼
    1 Job
```

---

### Example 2

```python
df.count()
df.show()
```

Result:

```
Application

├── Job 1 (count)

└── Job 2 (show)
```

---

## Remember

- Transformations do NOT create Jobs.
- Actions create Jobs.

---

# 2. Spark Stage

## Definition

A **Stage** is a group of parallel Tasks that can execute **without data shuffling**.

---

## Rule

```
No Shuffle
↓

Same Stage

Shuffle
↓

New Stage
```

---

## Operations Without Shuffle

- filter()
- select()
- withColumn()
- drop()

---

## Operations That Usually Cause Shuffle

- groupBy()
- join()
- distinct()
- orderBy()
- repartition()

---

## Example

```python
df.filter(...)
  .select(...)
  .groupBy(...)
  .show()
```

Execution:

```
Job

│

├── Stage 1
│      Read
│      Filter
│      Select
│
│   Shuffle
│
└── Stage 2
       GroupBy
       Show
```

---

## Remember

> **Shuffle creates a new Stage.**

---

# 3. Spark Task

## Definition

A **Task** is the smallest unit of work executed by one Executor on one data partition.

---

## Rule

> **One Partition = One Task**

---

### Example

```
100 Partitions

↓

100 Tasks
```

Even if only:

```
10 Executors
```

Spark still creates:

```
100 Tasks
```

Executors execute them in multiple rounds.

---

## Important

Tasks depend on:

- Number of partitions ✅

NOT on:

- Number of executors ❌

---

## Relationship

```
Driver

↓

Creates Tasks

↓

Executors Execute Tasks
```

---

## Remember

```
Executor = Worker

Task = Work
```

---

# 4. DAG (Directed Acyclic Graph)

## Definition

A **DAG (Directed Acyclic Graph)** is Spark's execution plan representing the sequence and dependencies of transformations before execution.

---

## Meaning

### Directed

Operations execute in order.

```
Read

↓

Filter

↓

Select

↓

GroupBy
```

---

### Acyclic

No loops.

Execution always moves forward.

---

### Graph

Each transformation becomes a node.

---

## Flow

```
PySpark Code

↓

Lazy Evaluation

↓

DAG

↓

Job

↓

Stages

↓

Tasks

↓

Execution
```

---

## Why DAG?

Spark analyses the complete workflow before execution.

This allows Spark to optimize execution.

---

## Example

Instead of:

```
Filter Age

↓

Filter Salary
```

Spark can optimize to:

```
Filter Age AND Salary
```

Result:

- Less computation
- Faster execution

---

# 5. Catalyst Optimizer

## Definition

Catalyst Optimizer is Spark's query optimization engine that converts a logical plan into an optimized physical execution plan.

---

## Workflow

```
PySpark Code

↓

Logical Plan

↓

Catalyst Optimizer

↓

Optimized Logical Plan

↓

Physical Plan

↓

Execution
```

---

## Responsibilities

- Merge filters
- Remove unnecessary columns
- Push filters closer to data source
- Choose efficient execution strategy

---

## Example 1 - Column Pruning

Table:

```
200 Columns
```

Code:

```python
df.select("name")
```

Catalyst reads:

```
Only "name"
```

instead of all 200 columns.

---

## Example 2 - Predicate Pushdown

Code:

```python
df.filter(df.salary > 50000)
```

Instead of:

```
Read Everything

↓

Filter
```

Catalyst (when supported by the data source) pushes the filter closer to the storage layer so less data is read.

---

# Complete Spark Execution Flow

```
spark-submit
      │
      ▼
Spark Application
      │
      ▼
Lazy Evaluation
      │
      ▼
DAG Created
      │
      ▼
Action
      │
      ▼
Spark Job
      │
      ▼
Stages
      │
      ▼
Tasks
      │
      ▼
Executors
      │
      ▼
Data Processed
```

---

# Complete Hierarchy

```
Spark Application
        │
        ▼
      Job
        │
        ▼
     Stage
        │
        ▼
      Task
        │
        ▼
    Executor
```

---

# Interview Cheat Sheet

| Concept | Remember |
|----------|----------|
| Application | One `spark-submit` |
| Job | Created by an Action |
| Stage | Split by Shuffle |
| Task | One Partition = One Task |
| Executor | Executes Tasks |
| DAG | Execution Plan |
| Catalyst | Optimizes Query |
| Lazy Evaluation | Wait until Action |

---

# One-Line Revision

- One `spark-submit` = One Application.
- One Action = One Job.
- One Shuffle = New Stage.
- One Partition = One Task.
- Executors execute Tasks.
- Driver creates execution plan.
- DAG represents execution dependencies.
- Catalyst optimizes the execution plan.
- Lazy Evaluation delays execution until an Action.


# Amazon EMR & Apache Spark Handbook
## Part 3 - EMR Production Concepts

> Covers:
> - Instance Groups vs Instance Fleets
> - On-Demand vs Spot Instances
> - Auto Scaling
> - Bootstrap Actions
> - EMRFS

---

# 1. Instance Groups vs Instance Fleets

## Instance Group

### Definition

A collection of EC2 instances of the **same instance type** performing the same node role.

### Characteristics

- One EC2 instance type per group
- Simple configuration
- Easy to manage
- Less flexible

### Example

```
Core Group

↓

m5.xlarge
m5.xlarge
m5.xlarge
```

### If Capacity is Unavailable

```
Allowed:

m5.xlarge

↓

No Capacity

↓

Cluster launch may fail (or wait for capacity)
```

---

## Instance Fleet

### Definition

A collection of EC2 instances where **multiple instance types** are allowed for the same node role.

### Characteristics

- Multiple instance types
- Higher availability
- Greater flexibility
- Better production choice

### Example

```
Core Fleet

↓

m5.xlarge
m6i.xlarge
c5.xlarge
```

### If Capacity is Unavailable

```
Allowed

m5.xlarge ❌
m6i.xlarge ✅
c5.xlarge ✅

↓

EMR launches an available allowed instance type
```

---

## Comparison

| Feature | Instance Group | Instance Fleet |
|----------|----------------|----------------|
| Instance Types | One | Multiple |
| Availability | Lower | Higher |
| Flexibility | Low | High |
| Production Use | Less Common | Recommended |

---

## Remember

> Instance Groups and Instance Fleets decide **how EC2 instances are selected**, not the cluster architecture.

---

# 2. On-Demand vs Spot Instances

## On-Demand

### Definition

Normal-priced EC2 instances that remain available until you stop or terminate them.

### Characteristics

- Reliable
- Higher cost
- Best for critical workloads

---

## Spot

### Definition

Unused EC2 capacity offered at a discounted price.

AWS can interrupt Spot instances when capacity is needed.

### Characteristics

- Much cheaper
- Can be interrupted
- Best for fault-tolerant workloads

---

## Comparison

| Feature | On-Demand | Spot |
|----------|------------|-------|
| Cost | Higher | Lower |
| Reliability | High | Lower |
| Interrupted by AWS | No | Yes |
| Best Use | Critical Nodes | Task Nodes |

---

## Typical EMR Cluster

```
Master

↓

On-Demand

---------------

Core

↓

Usually On-Demand

---------------

Task

↓

Spot
```

---

## Why Task Nodes Use Spot

- No HDFS storage
- Process data only
- Spark can reschedule failed tasks
- Lowest cost option

---

# 3. Auto Scaling

## Definition

Automatically increases or decreases EC2 instances based on workload.

---

## Why?

Without Auto Scaling:

```
Heavy Load

↓

Engineer manually adds nodes
```

With Auto Scaling:

```
Heavy Load

↓

EMR adds nodes automatically
```

---

## Which Nodes Scale?

| Node | Auto Scaling |
|------|--------------|
| Master | ❌ No |
| Core | ✅ Yes |
| Task | ✅ Yes |

---

## Why Master Doesn't Scale

Master handles:

- Cluster management
- Scheduling
- Monitoring

Only one Master coordinates the cluster.

---

## Benefits

- Better performance
- Lower cost
- Automatic resource management

---

## Typical Behaviour

```
Morning

Core = 2
Task = 20

↓

Night

Core = 2
Task = 0
```

Task nodes are usually removed first.

---

# 4. Bootstrap Actions

## Definition

A script that runs automatically on every EMR node during cluster startup before Hadoop/Spark starts.

---

## Execution Flow

```
Create Cluster

↓

Launch EC2

↓

Run Bootstrap Action

↓

Install Software

↓

Start Spark

↓

Cluster Ready
```

---

## Common Uses

- Install Python packages
- Install system software
- Configure Java
- Download files from S3
- Set environment variables
- Install monitoring agents

---

## Script Location

Usually stored in S3.

Example:

```
s3://company/bootstrap/install.sh
```

---

## Important Points

- Runs on **every node**
- Runs only during cluster creation
- If Bootstrap fails → Cluster creation fails

---

## Bootstrap vs Spark Job

| Bootstrap | Spark Job |
|------------|-----------|
| Configure machine | Process data |
| Install software | Read data |
| Set environment | Transform data |
| Copy config | Write output |

---

# 5. EMRFS (EMR File System)

## Definition

EMRFS is a file system implementation that allows Spark and Hadoop running on EMR to read from and write to Amazon S3.

---

## Important

> **EMRFS does NOT store data.**

S3 stores the data.

EMRFS acts as the bridge between EMR applications and S3.

---

## Architecture

```
Spark

↓

EMRFS

↓

Amazon S3
```

---

## Why EMRFS?

Without EMRFS

```
Spark

↓

HDFS

↓

Cluster Deleted

↓

Data Lost
```

With EMRFS

```
Spark

↓

EMRFS

↓

S3

↓

Cluster Deleted

↓

Data Still Exists
```

---

## HDFS vs S3

| HDFS | Amazon S3 |
|------|-----------|
| Cluster Storage | Persistent Storage |
| Lost with Cluster | Independent of Cluster |
| Temporary Processing | Long-Term Storage |
| Lives on EC2 | Separate AWS Service |

---

## Modern Architecture

```
Raw Data

↓

Amazon S3

↓

EMRFS

↓

Spark

↓

Processed Data

↓

Amazon S3

↓

Terminate Cluster
```

---

## Cloud Principle

> **Separate Compute from Storage**

```
Compute

↓

EMR

↓

EMRFS

↓

S3 (Persistent Storage)
```

EMR clusters are temporary.

S3 is permanent.

---

# Complete Architecture

```
              Amazon S3
                   │
        ┌──────────┼──────────┐
        │          │          │
        ▼          ▼          ▼
      Glue      Athena      EMR
                                │
                             Spark
                                │
                             EMRFS
                                │
                                ▼
                               S3
```

---

# Interview Cheat Sheet

| Concept | Remember |
|----------|----------|
| Instance Group | One instance type |
| Instance Fleet | Multiple instance types |
| On-Demand | Reliable, higher cost |
| Spot | Cheaper, interruptible |
| Auto Scaling | Scales Core & Task nodes |
| Master Node | Fixed, not auto-scaled |
| Bootstrap Action | Runs before Spark starts |
| EMRFS | Bridge between Spark and S3 |
| S3 | Persistent storage |
| HDFS | Temporary cluster storage |

---

# One-Line Revision

- Instance Groups use one EC2 instance type; Instance Fleets allow multiple.
- Spot Instances reduce cost but can be interrupted.
- Task nodes are ideal for Spot Instances.
- Auto Scaling adjusts Core and Task nodes based on workload.
- Bootstrap Actions prepare every node before Spark starts.
- EMRFS lets Spark read/write data in S3.
- EMRFS is **not storage**; S3 is the storage layer.
- Modern EMR separates **compute (EMR)** from **storage (S3)**.
