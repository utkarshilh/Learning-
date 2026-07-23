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
