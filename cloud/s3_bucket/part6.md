# Amazon S3 – Notes (Part 6)
**Source:** S3 Transfer Acceleration & Requester Pays | Data Engineering Series

---

## 1. S3 Transfer Acceleration

### Problem it Solves
- Uploading from a distant location (e.g., India) to a bucket in another region (e.g., US East N. Virginia) is **slow** due to physical distance
- S3 is a global service — experience should be consistent regardless of where you upload from

### How it Works
1. Instead of uploading directly to the S3 bucket, you upload to the **nearest AWS Edge Location** (powered by CloudFront)
2. You get a **success response immediately** after reaching the Edge Location
3. AWS then uses its **private global network backbone** to transfer the data from the Edge Location to the target S3 bucket — much faster than the public internet

### Key Points
- Requires a **different endpoint** to use — not the standard bucket URI
  - Standard: `s3://3am-s3-bucket/`
  - Accelerated: `3am-s3-bucket.s3-accelerate.amazonaws.com`
- Has an **additional cost** — not free (pricing covered in the pricing section)
- Enable via: `Bucket → Properties → Transfer Acceleration → Edit → Enable`

### Speed Example (from AWS Speed Comparison Tool)
- Bangalore → US East 1 (direct): slow
- Bangalore → US East 1 (with Transfer Acceleration): **~91% faster**
- Bangalore → San Francisco (with Transfer Acceleration): **3x+ faster**
- AWS provides a [Speed Comparison Tool](https://s3-accelerate-speedtest.s3-accelerate.amazonaws.com/en/accelerate-speed-comparsion.html) to test your specific location vs regions

---

## 2. Requester Pays

### Problem it Solves
- By default, **the bucket owner pays** for all data transfer/download costs
- If you share a large dataset publicly or with other orgs, and thousands of users download it → **you bear the cost**, not the downloaders

### How it Works
- When **Requester Pays is enabled**, the **downloader's AWS account** is billed for download charges — not the bucket owner
- The downloader **must have an AWS account** (anonymous access not allowed)

### When to Use
- **Cross-organization data sharing** — another business unit or org wants to use your dataset
- Large public/shared datasets where download costs would otherwise hit your account

### Enable via:
`Bucket → Properties → Requester Pays → Edit → Enable`

---

## Key Takeaways

| Feature | Purpose | Cost |
|---|---|---|
| **Transfer Acceleration** | Faster uploads over long distances via Edge Locations | Extra charge |
| **Requester Pays** | Shifts download cost from bucket owner to the downloader | No extra feature cost — just billing shift |

- Transfer Acceleration = upload speed problem → solved via CloudFront Edge + AWS backbone
- Requester Pays = billing problem for shared datasets → solved by charging the downloader's account
- Both enabled from `Bucket → Properties`

---
*Notes from Part 6 of the S3 for Data Engineers playlist.*
