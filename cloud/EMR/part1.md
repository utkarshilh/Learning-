# Amazon EMR 

## Why EMR?

-   Big data cannot be processed efficiently by one machine.
-   Use multiple machines for parallel processing.
-   EMR manages those machines (cluster).

------------------------------------------------------------------------

## Amazon EMR

**Definition:** Managed AWS service that creates and manages EC2
clusters to run big data frameworks.

**Remember:** **EMR manages, Spark processes.**

------------------------------------------------------------------------

## Hadoop

**Definition:** Open-source ecosystem for distributed storage and
processing.

**Components:** HDFS \| YARN \| MapReduce

------------------------------------------------------------------------

## MapReduce

**Map:** Split work into parallel tasks.

**Reduce:** Combine partial results.

------------------------------------------------------------------------

## Spark

**Definition:** Distributed data processing framework.

**Key Point:** Fast because it performs much of its processing in
memory.

------------------------------------------------------------------------

## Responsibilities

-   **S3** → Store data
-   **EMR** → Manage cluster
-   **EC2** → Compute
-   **Spark** → Process data
-   **MapReduce** → Split & Combine
-   **Hadoop** → Ecosystem

------------------------------------------------------------------------

## Spark vs Hadoop

-   **Spark** → Processing framework
-   **Hadoop** → Ecosystem
-   **Spark** commonly replaces **Hadoop MapReduce**, **not** Hadoop
    itself.

------------------------------------------------------------------------

## Architecture

``` text
S3
 ↓
EMR
 ↓
EC2 Cluster
 ↓
Spark
 ↓
S3
```

# Amazon EMR - Ultra Crisp Notes (Part 2)

## EMR Cluster

**Definition:** Collection of EC2 instances managed by EMR.

``` text
EMR Cluster
↓
EC2 + EC2 + EC2 + EC2
```

------------------------------------------------------------------------

## EMR Node Types

**Master Node** - Manage cluster - Schedule jobs - Monitor nodes

**Core Node** - Process data - Store data (HDFS)

**Task Node** - Process only - No storage - Optional

------------------------------------------------------------------------

## Node Summary

``` text
Master → Manage
Core → Store + Process
Task → Process Only
```

------------------------------------------------------------------------

## Cluster Example

``` text
Master
├── Core 1
├── Core 2
├── Core 3
├── Task 1
└── Task 2
```

------------------------------------------------------------------------

## YARN

**Definition:** Resource Manager of Hadoop.

**Responsibilities** - Allocate CPU - Allocate Memory - Schedule Jobs -
Launch Containers - Monitor Resources

------------------------------------------------------------------------

## Remember

-   **EMR** → Creates Cluster
-   **YARN** → Allocates Resources
-   **Spark** → Processes Data

------------------------------------------------------------------------

## Flow

``` text
You
↓
Spark Job
↓
YARN
↓
EC2 Cluster
↓
S3
```

------------------------------------------------------------------------

## Complete Architecture

``` text
S3
↓
EMR
↓
Master Node
↓
YARN
↓
Core / Task Nodes
↓
Spark Executors
↓
S3
```

------------------------------------------------------------------------

## Interview Points

**Does EMR allocate CPU/RAM?**

❌ No

✅ YARN does.

**Does Spark allocate resources?**

❌ No

✅ Spark requests resources from YARN.

**Can Task Nodes be removed?**

✅ Yes

Reason: - No data storage - Compute only - Optional

------------------------------------------------------------------------

## One-Line Revision

-   **EMR** → Cluster Manager
-   **Master** → Cluster Manager Node
-   **Core** → Compute + Storage
-   **Task** → Compute Only
-   **YARN** → Resource Manager
-   **Spark** → Processing Engine


