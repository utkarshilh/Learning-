# AWS S3 CLI Cheat Sheet

## AWS CLI Setup

### Check AWS CLI Version

```bash
aws --version
```

Checks whether AWS CLI is installed.

### Configure AWS CLI

```bash
aws configure
```

Configure:

* AWS Access Key ID
* AWS Secret Access Key
* Region (e.g. ap-south-1)
* Output Format (json)

### Verify Current User

```bash
aws sts get-caller-identity
```

Displays current AWS account and IAM user.

---

# S3 Commands

## List All Buckets

```bash
aws s3 ls
```

Displays all S3 buckets.

## Create Bucket

```bash
aws s3 mb s3://bucket-name
```

Creates a new bucket.

Example:

```bash
aws s3 mb s3://utkarsh-de-learning-2026
```

---

## List Files in Bucket

```bash
aws s3 ls s3://bucket-name/
```

Example:

```bash
aws s3 ls s3://utkarsh-de-learning-2026/
```

---

## Upload File to S3

```bash
aws s3 cp file.csv s3://bucket-name/
```

Example:

```bash
aws s3 cp employees.csv s3://utkarsh-de-learning-2026/
```

---

## Upload File Using Full Path

```bash
aws s3 cp /path/to/file.csv s3://bucket-name/
```

Example:

```bash
aws s3 cp /Users/utkarsh/Desktop/Exam/aa/temp/ut.csv s3://utkarsh-de-learning-2026/
```

---

## Upload File to Folder

```bash
aws s3 cp file.csv s3://bucket-name/raw/
```

Example:

```bash
aws s3 cp employees.csv s3://utkarsh-de-learning-2026/raw/
```

---

## Upload Entire Folder

```bash
aws s3 cp ./data s3://bucket-name/raw/ --recursive
```

Uploads all files from a local directory.

---

## Download File from S3

```bash
aws s3 cp s3://bucket-name/file.csv .
```

Downloads file to current directory.

---

## Delete File from S3

```bash
aws s3 rm s3://bucket-name/file.csv
```

Deletes a file from S3.

---

## Delete Folder from S3

```bash
aws s3 rm s3://bucket-name/folder/ --recursive
```

Deletes all files inside a folder.

---

# CloudShell Notes

## List Files

```bash
ls
```

Displays files available in CloudShell.

## Current Directory

```bash
pwd
```

Displays current working directory.

## Important

CloudShell cannot access local machine files.

This will NOT work in CloudShell:

```bash
aws s3 cp /Users/utkarsh/Desktop/file.csv s3://bucket-name/
```

Reason:

* `/Users/utkarsh/...` exists on Mac.
* CloudShell runs on an AWS-managed Linux machine.

### Solution

1. CloudShell → Actions → Upload File
2. Verify file:

```bash
ls
```

3. Upload to S3:

```bash
aws s3 cp ut.csv s3://bucket-name/
```

---

# Common Data Engineering S3 Structure

```text
s3://bucket-name/
├── raw/
├── processed/
├── archive/
└── logs/
```

* raw/ → Incoming files
* processed/ → Transformed files
* archive/ → Historical files
* logs/ → Application logs

---

# Quick Revision

```bash
# Configure CLI
aws configure

# Verify Identity
aws sts get-caller-identity

# List Buckets
aws s3 ls

# Create Bucket
aws s3 mb s3://bucket-name

# Upload File
aws s3 cp file.csv s3://bucket-name/

# List Files
aws s3 ls s3://bucket-name/

# Download File
aws s3 cp s3://bucket-name/file.csv .

# Delete File
aws s3 rm s3://bucket-name/file.csv
```
