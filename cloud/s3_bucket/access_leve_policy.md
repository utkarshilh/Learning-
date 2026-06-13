# S3 Access Point Policy - Practice Notes

## Objective

Learn how S3 Access Points work and how Access Point Policies control access to a bucket.

---

## Resources Created

### S3 Bucket

```text
utkarsh-access-lab
```

### Access Points

```text
de-access-point
analytics-access-point
```

### IAM Users

```text
user1
user2
user3
```

---

## Key Observations

### 1. Access Point is NOT a Bucket

Created:

```text
Bucket:
utkarsh-access-lab

Access Point:
de-access-point
```

Observation:

* Bucket count remained the same.
* Access Point appeared under Access Points section.
* Access Point is a separate entry point to the bucket.

---

### 2. Multiple Access Points Can Point to One Bucket

Created:

```text
de-access-point
analytics-access-point
```

Both pointed to:

```text
utkarsh-access-lab
```

Observation:

```text
One bucket
Multiple access points
```

---

### 3. Deleting Access Point

Deleted:

```text
analytics-access-point
```

Observation:

```text
Bucket still existed
Files still existed
Other access points still existed
```

Conclusion:

```text
Deleting an access point does not delete bucket data.
```

---

### 4. Access Point Policy

Location:

```text
S3
→ Access Points
→ de-access-point
→ Permissions
→ Access Point Policy
```

Policy attached only to:

```text
de-access-point
```

---

### 5. IAM Users Created

Users:

```text
user1
user2
```

Attached AWS Managed Policy:

```text
AmazonS3ReadOnlyAccess
```

Capabilities:

```text
View bucket      ✓
View objects     ✓
Download files   ✓
Upload files     ✗
Delete files     ✗
```

---

### 6. Console Access

Verified:

```text
IAM
→ Users
→ user1
```

Console Access:

```text
Enabled
```

Used IAM Sign-In URL to login as IAM user.

---

### 7. Upload Test

Logged in as:

```text
user2
```

Attempted:

```text
Upload CSV file
```

Result:

```text
Access Denied
```

Reason:

```text
AmazonS3ReadOnlyAccess
```

does not include:

```text
s3:PutObject
```

---

### 8. Custom Upload Policy

Required permission:

```json
{
  "Effect": "Allow",
  "Action": [
    "s3:GetObject",
    "s3:PutObject"
  ],
  "Resource": "arn:aws:s3:::utkarsh-access-lab/*"
}
```

Required bucket permission:

```json
{
  "Effect": "Allow",
  "Action": "s3:ListBucket",
  "Resource": "arn:aws:s3:::utkarsh-access-lab"
}
```

Observation:

```text
Upload works
Delete still fails
```

---

## Access Point Policy Concepts Learned

### Access Point

Acts as:

```text
Special entry point to a bucket
```

### Access Point Policy

Controls:

```text
Who can use the access point
What actions they can perform
```

### Access Point ARN

Format:

```text
arn:aws:s3:region:account-id:accesspoint/access-point-name
```

Example:

```text
arn:aws:s3:ap-south-1:123456789012:accesspoint/de-access-point
```

---

## Permission Flow

```text
User
 ↓
IAM Policy
 ↓
Access Point Policy
 ↓
Bucket
 ↓
Object
```

AWS evaluates permissions across all layers.

---

## Important Learning

### Read Only Access

```text
AmazonS3ReadOnlyAccess
```

Allows:

```text
ListBucket
GetObject
```

Does NOT allow:

```text
PutObject
DeleteObject
```

---

### Upload Permission

Required:

```text
s3:PutObject
```

---

### Delete Permission

Required:

```text
s3:DeleteObject
```

---

## Lab Cleanup

Delete Access Point:

```text
S3
→ Access Points
→ Delete
```

Delete Bucket:

```text
S3
→ Bucket
→ Empty
→ Delete
```

Delete IAM Users:

```text
IAM
→ Users
→ Delete
```
