# S3 Bucket Policy - Practice Notes

## Objective

Learn how Bucket Policies interact with IAM Policies and understand AWS permission evaluation.

---

# Resources Created

## Bucket

```text
utkarsh-bucket-policy-lab
```

## IAM User

```text
test-user
```

## IAM Policy Attached

```text
AmazonS3FullAccess
```

---

# Initial Verification

Logged in as:

```text
test-user
```

Verified:

```text
View bucket      ✓
View objects     ✓
Upload objects   ✓
Delete objects   ✓
Download objects ✓
```

Reason:

```text
AmazonS3FullAccess
```

grants all S3 permissions.

---

# Bucket Policy Lab 1

## Goal

Test whether a Bucket Policy can override IAM permissions.

---

## Bucket Policy

Applied policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyTestUser",
      "Effect": "Deny",
      "Principal": {
        "AWS": "USER_ARN"
      },
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::utkarsh-bucket-policy-lab",
        "arn:aws:s3:::utkarsh-bucket-policy-lab/*"
      ]
    }
  ]
}
```

---

## Observation

User still had:

```text
AmazonS3FullAccess
```

But Bucket Policy contained:

```text
Effect = Deny
Action = s3:*
```

Result:

```text
Bucket visible             ✓
Access to objects          ✗
Permission errors          ✓
```

---

# Important Learning

AWS permission evaluation:

```text
Allow + Allow = Allow

Allow + Explicit Deny = Deny
```

Most important rule:

```text
Explicit Deny always wins.
```

---

# Bucket Policy Lab 2

## Goal

Deny only object reads.

Modified policy:

```json
"Action": "s3:GetObject"
```

instead of:

```json
"Action": "s3:*"
```

---

## Observation

User could:

```text
See bucket              ✓
See object names        ✓
```

User could not:

```text
Open object             ✗
Download object         ✗
View file contents      ✗
```

---

# Important Learning

AWS treats these permissions separately.

## ListBucket

```text
s3:ListBucket
```

Meaning:

```text
Show me the files available.
```

---

## GetObject

```text
s3:GetObject
```

Meaning:

```text
Allow me to read/download the file.
```

---

## Result

Current permissions:

```text
ListBucket  → Allowed
GetObject   → Denied
```

Outcome:

```text
Can see hello.txt
Cannot open hello.txt
```

---

# Permission Concepts Learned

## List Objects

```json
"s3:ListBucket"
```

Allows:

```text
View bucket contents
```

---

## Read Objects

```json
"s3:GetObject"
```

Allows:

```text
Open file
Download file
Read contents
```

---

## Upload Objects

```json
"s3:PutObject"
```

Allows:

```text
Upload new file
Replace existing file
```

---

## Delete Objects

```json
"s3:DeleteObject"
```

Allows:

```text
Delete object
```

---

# Permission Evaluation Order

AWS evaluates:

```text
IAM Policy
Bucket Policy
Access Point Policy
```

Final rule:

```text
Any Explicit Deny
        ↓
Final Result = Deny
```

---

# Real World Example

Bucket:

```text
finance-data
├── salary.csv
├── bonus.csv
├── tax.csv
```

Allow:

```text
s3:ListBucket
```

Deny:

```text
s3:GetObject
```

Result:

```text
Employee can see files.
Employee cannot open files.
```

---

# Key Takeaways

### Explicit Deny

```text
Always overrides Allow.
```

### ListBucket

```text
Can see file names.
```

### GetObject

```text
Can read/download files.
```

### PutObject

```text
Can upload files.
```

### DeleteObject

```text
Can remove files.
```

### Bucket Policy

```text
Applies at bucket level.
Can override IAM permissions.
```
