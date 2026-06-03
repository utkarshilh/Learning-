# Amazon S3 – Notes (Part 4)
**Source:** S3 Basics – Hands On | Data Engineering Series

---

## What This Session Covers
- S3 Console walkthrough
- Creating a bucket
- Uploading objects via Console and AWS CLI (from EC2)
- Key AWS S3 CLI commands

---

## 1. S3 Console Overview

**Left navigation includes:**
- **Buckets** — list of all your buckets
- **Access Points, Object Lambda Access Points, Multi-Region Access Points** — covered later
- **Batch Operations** — bulk object operations
- **Storage Lens** — storage analytics
- **Block Public Access (account level)** — account-wide setting

### ⚠️ Best Practice — Block Public Access
- Always keep **"Block all public access" ENABLED** at account level
- If a bucket is public, anyone with the URL can download files → **you pay for that data transfer**
- Never leave buckets publicly accessible unless specifically required

---

## 2. Creating a Bucket

Steps: **Create Bucket → Choose type → Name it → Configure settings**

- **Bucket type:** `General Purpose` (default) vs `Directory` (for S3 Express One Zone — covered later)
- **Globally unique name required** — trying a common name like `test` will immediately fail because someone else already owns it
- **Block Public Access** → keep enabled
- **Versioning** → leave disabled for now (covered in a later topic)
- **Tags** → optional, e.g., `Name = my-bucket-name`
- Everything else → leave as default for now

---

## 3. Uploading Objects via Console

- Click **Upload** inside a bucket or folder → browse/drag-drop file → click Upload
- Object goes directly to bucket root **or** inside a folder depending on where you initiate upload
- Upload from India to a US East (N. Virginia) bucket = longer transfer time (distance matters)
- If you **upload the same file again** with the same name → it **overwrites** the existing object (no duplicate created)

### Object Key Confirmed in Console:
After upload, clicking on the object shows:
- **S3 URI** → `s3://bucket-name/folder/filename.csv` ← use this in data engineering
- **Object URL** → `https://bucket-name.s3.us-east-1.amazonaws.com/filename.csv`
- **Object Key** → everything after the bucket name (e.g., `retail/orders_21.csv`)
- Folder name is **part of the object key** → `retail/orders_21.csv` not just `orders_21.csv`

---

## 4. Uploading Objects via AWS CLI (from EC2)

**Prerequisite:** EC2 instance must have an **IAM Instance Profile** with S3 permissions attached.

### Upload from EC2 to S3:
```bash
aws s3 cp customer_2.csv s3://3am-s3-bucket/
# Uploads to bucket root

aws s3 cp order_items_new_data_set_2.csv s3://3am-s3-bucket/retail/
# Uploads into the retail folder
```

> Use CLI for **large files (>5 GB)** — console cannot handle objects larger than 5 GB

---

## 5. Common AWS S3 CLI Commands

| Command | Purpose | Example |
|---|---|---|
| `aws s3 ls` | List objects in a bucket or folder | `aws s3 ls s3://bucket-name/` |
| `aws s3 cp` | Copy file (local→S3, S3→local, S3→S3) | `aws s3 cp file.csv s3://bucket/folder/` |
| `aws s3 mv` | Move file (removes from source) | `aws s3 mv s3://bucket/a/file.csv s3://bucket/b/` |
| `aws s3 rm` | Delete an object or folder | `aws s3 rm s3://bucket/folder/file.csv` |
| `aws s3api` | Advanced/low-level S3 operations | Used for versioning, etc. (covered later) |

### CP — 3 possible source/target combinations:
- **Local → S3** (upload)
- **S3 → Local** (download)
- **S3 → S3** (copy between folders/buckets)

### MV vs CP:
- `cp` — copies file, **original remains** at source
- `mv` — moves file, **original is deleted** from source

### RM on a folder:
- Deletes the folder and all its contents

---

## Key Takeaways

- **Block public access** at account level — always on
- Console upload is fine for small files; use **CLI for files >5 GB**
- Uploading the same filename again **overwrites** the existing object
- Folder name is included in the **Object Key**
- Core CLI commands: `ls`, `cp`, `mv`, `rm`, `s3api`
- `aws s3api` is a more powerful command used for advanced operations (e.g., versioning) — covered later

---
*Notes from Part 4 of the S3 for Data Engineers playlist.*
