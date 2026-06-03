# Amazon S3 – Notes (Part 3)
**Source:** S3 Basics | Data Engineering Series

---

## Agenda for This Chapter
- S3 Storage Hierarchy (Buckets, Folders, Objects)
- Object Keys & URIs
- Bucket & Object Properties (versioning, encryption, storage class, multipart upload, etc.) ← *deeper topics coming in later parts*

---

## 1. S3 Storage Hierarchy

**Top Level → Bucket → Folder(s) → Objects**

### Bucket
- The **top-level container** in S3 — think of it like a hard drive or your Google Drive
- You create buckets to **logically separate data** (e.g., one per department, per business unit, per environment)
- Example: separate buckets for HR, Finance, IT

### Folders
- Created **inside buckets** to organize objects
- Can be **nested** (sub-folders / sub-directories)
- Folders are just a logical grouping — S3 is still flat storage underneath

### Objects
- The actual files stored in S3: CSV, Parquet, Avro, JSON, JPEG, PNG, MP4, PDF, DOCX, etc.
- Stored directly in a bucket **or** inside folders
- At the end of the day, you always access **objects**, not folders

---

## 2. Accessing S3 — URI vs URL

| Format | Used For | Example |
|---|---|---|
| **URI** | Data Engineering (Athena, Redshift, Glue, EMR) | `s3://bucket-name/folder/file.csv` |
| **URL** | Frontend apps, media content embedding | `https://bucket-name.s3.us-east-1.amazonaws.com/file.csv` |

> ✅ In Data Engineering, always use **URI format** (`s3://...`)

---

## 3. Object Keys

The full URI path to an object is called its **Object Key**.

### Examples:

```
Bucket:         s3://3am-s3-bucket/
Object (root):  s3://3am-s3-bucket/orders.csv
Object (root):  s3://3am-s3-bucket/aws.png

Folder:         s3://3am-s3-bucket/retail/
Object:         s3://3am-s3-bucket/retail/customers.json
Object:         s3://3am-s3-bucket/retail/sellers.avro

Sub-folder:     s3://3am-s3-bucket/retail/images/
Object:         s3://3am-s3-bucket/retail/images/iphone.png
Object:         s3://3am-s3-bucket/retail/images/samsung.jpeg
```

### Key Rule — Global Uniqueness:
- **Bucket names must be globally unique** across all AWS accounts and all regions
- If someone else already created `my-bucket`, you cannot create one with the same name — even in a different account or region
- Because bucket names are globally unique → **object keys are also globally unique**

---

## 4. How to Upload Objects

Two ways — both ultimately call the **S3 API**:

- **AWS Console** → upload from local machine via browser → Console calls S3 API
- **AWS CLI** → run from local machine or EC2 instance → CLI calls S3 API directly

---

## Key Takeaways

- Hierarchy: **Bucket > Folder(s) > Object**
- Always use **URI (`s3://...`)** in data engineering contexts
- The full path to an object = **Object Key** (must be globally unique)
- Bucket names are **globally unique** across all accounts and regions
- Objects are what you store and access; folders are just organizational

---
*Notes from Part 3 of the S3 for Data Engineers playlist.*
