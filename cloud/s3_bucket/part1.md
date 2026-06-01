# Amazon S3 – Detailed Notes (Part 1)
**Source:** S3 Introduction 1 | Data Engineering Series

---

## 📌 Chapter Overview

This chapter covers **Amazon S3 (Simple Storage Service)** and is divided into two sections:
1. **S3** – Architecture, concepts, use cases
2. **S3 Glacier** – (covered in a later section)

Topics covered in Part 1:
- What is Distributed Storage?
- S3 as an Object Store
- Where S3 fits in the Data Engineering Pipeline
- Use Cases of S3
- Blog Store vs Object Store Difference

---

## 1. Why Distributed Storage?

### The Problem with Single-Server Storage
- Imagine you have files with a **combined size of 10 TB**.
- A personal laptop typically has 1–2 TB storage — **not enough**.
- Even at an organization level, a **single storage node has limitations**.
- Storing everything in one server has a **scalability problem**:
  - Today: 10 TB → In a few months: 20 TB
  - Single-server architecture cannot grow easily with data.

### The Solution: Distributed Storage
- **Distributed storage breaks the barrier of a single server.**
- Instead of storing a large file in one place, it:
  1. **Breaks the large file into smaller blocks**
  2. **Distributes those blocks across multiple servers/nodes**

#### Example:
- 10 TB of data → broken into **7 blocks × ~1,430 GB each**
- Each block is stored on a **separate storage node**
- Even a single 2 TB file gets split: each of 7 nodes stores ~300 GB of that file

### Just Storing Blocks Is NOT Enough
- Simply splitting files across disks doesn't solve the problem.
- You also need **intelligence** — knowing:
  - Where each block is stored
  - How to **stitch them back together** when reading
- That's why distributed storage involves **servers with compute power**, not just disks.

### Distributed Storage + Distributed Compute
- The same servers used to store data can also **run processing code**.
- Instead of moving data to a separate compute machine, you **send the code to the data**.
- Each server processes its own subset of data and returns results to the client/application.
- This is the foundation of **Big Data architecture** (e.g., Hadoop, Spark).

---

## 2. Amazon S3 – Simple Storage Service

### Why S3 After Distributed Storage?
- S3 **is** a distributed storage system — but it is **much more than that**.
- S3 is a **fully managed, highly scalable, distributed object storage service** by AWS.

### S3 Internal Architecture (Simplified)
- S3 has a **massive number of nodes** — potentially **hundreds of thousands**.
- Each node has:
  - **Disks/Storage** attached to it
  - **Multiple microservices/programs** running inside

#### Microservices in S3 Nodes:
- Each service handles a **specific S3 feature**. Examples:
  - **Storage class setting** → handled by one service
  - **Lifecycle policy** → handled by another service
  - **S3 Select** (query data in place) → handled by yet another service
- These services are **replicated across multiple nodes** for high availability.
  - If Node 1 goes down, the same service runs on Node 1000 — **no disruption**.
- Storage itself is also **replicated** behind the scenes.

### How You Interact with S3
- S3 exposes an **API layer** to end users.
- All operations (upload, download, set storage class, etc.) go through the **S3 API**.
- The API routes requests to the appropriate internal service/program.

---

## 3. Why is S3 Called an Object Store?

### S3 Can Store ANY Kind of Data:
| Category | Examples |
|---|---|
| Data Engineering files | CSV, Parquet, JSON, ORC, Avro |
| Images | JPG, PNG, JPEG |
| Videos | MP4, MOV |
| Documents | PDF, Word (.docx), Excel (.xlsx), PowerPoint |
| Any binary format | Any other file type |

### What is an "Object"?
An **object** in S3 = **Actual Data + Metadata**

#### Metadata stored alongside the file:
- **Type of file** (e.g., CSV, image, video)
- **File size** (e.g., 2 GB)
- **Encryption status** (enabled or not)
- **Encryption key** and **algorithm** (e.g., AES-256)
- **Last modified timestamp**
- **Location of the file on disk** (physical storage location)
- **Custom tags** assigned by the user

> 💡 The data file (e.g., a CSV) + all its metadata = **one Object**

### Object Key
- Each object is **not addressed by its filename**.
- It is addressed using an **Object Key** (unique identifier within a bucket).
- Object keys will be covered in more detail later in the series.

---

## 4. How Reads and Writes Work in S3

### Read / Download Flow:
1. Client sends a **read request** to the **S3 API** along with the **Object Key**.
2. S3 API contacts the **Metadata Service** (a microservice running on a node).
3. Metadata Service returns: node location, storage location, and metadata info.
4. S3 API then contacts a **Data Retrieval Service** (another microservice on another node).
5. That service reads the **actual file** (in CSV, JPG, binary, etc.) from storage.
6. Data is returned to S3 API → returned to the **client**.

> ⚠️ **Important:** This is much more complex than reading a file from your laptop or EC2 instance. Multiple services and nodes are involved.

### Write Flow:
- Similar process in reverse — data goes through the API, gets distributed to nodes, metadata is recorded.

---

## 5. Summary: What is an Object Store? (4 Key Points)

| Component | Description |
|---|---|
| **Object Key** | Unique identifier to address the object |
| **Object Metadata** | Type, size, encryption, last modified, location, tags |
| **Actual Data** | The file itself (CSV, image, video, etc.) |
| **Distributed Storage** | Data is stored across many nodes, not a single server |

All four combined = **Object Store**

---

## 6. Key Takeaways for Data Engineers

- S3 is not just a "cloud hard drive" — it's a **sophisticated distributed system**.
- It is used heavily in data engineering architectures as the **central data lake / storage layer**.
- It supports **any file format** relevant to data engineering (Parquet, CSV, JSON, ORC, etc.).
- The concept of **object** (data + metadata) is fundamental to working with S3.
- All interactions go through the **S3 API** — no direct file system access.
- S3 achieves **high availability** through service and storage **replication** across nodes.
- S3 enables both **distributed storage** and **distributed compute** (e.g., via S3 Select, Athena).

---

*Notes compiled from Part 1 of the S3 for Data Engineers playlist.*
