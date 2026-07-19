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
