# Amazon S3 – Notes (Part 7)
**Source:** S3 Storage Classes & Conditional Upload | Data Engineering Series

---

## 1. What is a Storage Class?

- Defined at the **object level** (not bucket level) — set during upload via Console, CLI, or SDK
- Determines: **storage type, access latency, availability, and cost**
- All storage classes use **SSDs** underneath
- Choose based on **how frequently the data will be accessed**

---

## 2. Storage Classes Summary

### Frequently Accessed Data

| Storage Class | AZs | Availability | Durability | Key Trait | Use Case |
|---|---|---|---|---|---|
| **S3 Standard** | ≥3 AZs | 99.99% (4 nines) | 11 nines | Default, high perf | Analytics, active workloads |
| **S3 Express One Zone** | 1 AZ | 99.95% | 11 nines | **10x faster** than Standard, single-digit ms latency | ML preprocessing, dashboards, small objects |

### Infrequently Accessed Data (once/week to once/month)

| Storage Class | AZs | Availability | Key Trait | Use Case |
|---|---|---|---|---|
| **Standard-IA** | ≥3 AZs | 99.9% (3 nines) | Same perf as Standard but cheaper | Backup, DR, compliance |
| **One Zone-IA** | 1 AZ | 99.5% | Lowest availability, still good perf | Data you can recreate if lost |

### Archival Data (accessed once/quarter to once/year)

| Storage Class | Retrieval Time | Key Trait | Use Case |
|---|---|---|---|
| **Glacier Instant Retrieval** | Milliseconds | Archive with immediate access | Medical records, old social media photos |
| **Glacier Flexible Retrieval** | Minutes → Hours | Slow retrieval, very cheap | Backup, DR |
| **Glacier Deep Archive** | Up to 12 hours | Cheapest of all | 5–15 yr compliance (Finance, Insurance, Healthcare) |

> 💡 Glacier Deep Archive = modern replacement for **magnetic tape archives**

### Unknown Access Pattern

| Storage Class | Key Trait |
|---|---|
| **Intelligent Tiering** | S3 auto-moves objects between storage classes based on access frequency — no manual rules needed |

---

## 3. Why So Many Storage Classes? → **Pricing**

| Storage Class | Price (per GB/month) |
|---|---|
| S3 Standard | $0.023 |
| Standard-IA | $0.0125 |
| Express One Zone | $0.16 |
| One Zone-IA | $0.01 |
| Glacier Instant Retrieval | $0.004 |
| Glacier Flexible Retrieval | $0.0036 |
| Glacier Deep Archive | $0.00099 |

- Standard costs ~**2x more** than Standard-IA for similar performance — because availability is 99.99% vs 99.9%
- Express One Zone is the most expensive — because it gives single-digit ms latency
- Deep Archive is the cheapest — because retrieval takes up to 12 hours

---

## 4. Durability & Availability Quick Reference

| Storage Class | Durability | Availability | AZs |
|---|---|---|---|
| Standard | 11 nines | 99.99% | ≥3 |
| Standard-IA | 11 nines | 99.9% | ≥3 |
| Intelligent Tiering | 11 nines | 99.9% | ≥3 |
| One Zone-IA | 11 nines | 99.5% | 1 |
| Express One Zone | 11 nines | 99.95% | 1 |
| Glacier Instant Retrieval | 11 nines | 99.9% (after restore) | ≥3 |
| Glacier Flexible Retrieval | 11 nines | 99.99% (after restore) | ≥3 |
| Glacier Deep Archive | 11 nines | 99.99% (after restore) | ≥3 |

> ⚠️ For Glacier classes, availability figures apply **after restoring** to another storage class — you can't read directly from Flexible or Deep Archive

---

## 5. Conditional Upload

### Problem
- Same filename uploaded again → **silently overwrites** the existing object
- Versioning helps after the fact, but doesn't **prevent** the overwrite

### Solution — Conditional Upload
- S3 checks if the object key **already exists** before uploading
- If it exists → **upload is rejected** with a `PreconditionFailed` error
- If it doesn't exist → upload proceeds normally

### How to Use
- Only available via **API or CLI** (no console option)
- Requires **AWS CLI version ≥ 2.19.1**

```bash
aws s3api put-object \
  --bucket 3am-s3-bucket \
  --key retail/orders_21.csv \
  --body orders_21.csv \
  --if-none-match "*"
```

- `--if-none-match "*"` → the key flag that prevents overwriting
- Folder name **must be in the key**, not the bucket name
- If CLI version is old → error: `unknown option: if-none-match` → upgrade CLI first

---

## Key Takeaways

- Storage class is set at **object level**, not bucket level
- Pick based on access frequency: **Standard → Standard-IA → Glacier → Deep Archive**
- When access pattern is unknown → use **Intelligent Tiering**
- Express One Zone = fastest (10x Standard) but expensive and single AZ
- In practice: most workloads use **Standard + Lifecycle Policies** to auto-transition to cheaper classes over time
- Conditional Upload (`--if-none-match "*"`) prevents accidental overwrites — CLI only, requires v2.19.1+

---
*Notes from Part 7 of the S3 for Data Engineers playlist.*
