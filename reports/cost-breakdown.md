# AWS vs. Azure Cost Comparison Report

## 1. Workload Architecture Specifications
To achieve a true "apples-to-apples" baseline, the hosting specifications for our small web application have been standardized as follows:
* **Compute:** 2 vCPUs / 4GB RAM (Burstable Hardware Tier)
* **Operating System:** Ubuntu Linux 22.04 LTS vs. Windows Server
* **Local Block Storage:** 64GB Solid State Drive (SSD)
* **Cloud Object Storage:** 100GB Standard Tier (for media/database backups)
* **Network Bandwidth:** 500GB Outbound Data Transfer (Egress) per month

---

## 2. Monthly Financial Cost Matrix (Pay-As-You-Go)

| Infrastructure Component | AWS Equivalent Service & SKU | AWS Monthly Cost | Azure Equivalent Service & SKU | Azure Monthly Cost |
| :--- | :--- | :--- | :--- | :--- |
| **Linux Compute** | EC2 `t3.medium` (On-Demand) | $30.37 | Virtual Machine `B2s_v2` | $26.28 |
| **Windows Compute** | EC2 `t3.medium` (Retail Windows) | $68.33 | Virtual Machine `B2s_v2` (Retail) | $61.32 |
| **Windows + Hybrid Benefit** | EC2 `t3.medium` (BYOL License) | $30.37 | VM `B2s_v2` (+ AHUB Savings) | $26.28 |
| **Local Block Storage** | EBS `gp3` Volume (64GB) | $5.12 | Premium SSD Managed Disk (64GB P6) | $4.80 |
| **Cloud Object Storage** | S3 Standard Tier (100GB) | $2.30 | Blob Storage Hot Hot Tier (100GB) | $2.00 |
| **Outbound Data Transfer** | Data Transfer Out (Egress 500GB) | $44.10 | Bandwidth / Internet Egress (500GB) | $34.50 |
| **ESTIMATED TOTAL (LINUX)**| **All Core Web App Resources** | **$81.89** | **All Core Web App Resources** | **$67.58** |

---

## 3. High-Impact Architectural & Cost Drivers

### A. Networking Egress & Inter-Zone Data Transfer Fees
Data transfer is frequently a hidden operational cost driver. 
* **AWS Pricing**: AWS bills a fixed fee of **$0.01 per GB** for data moving symmetrically across different Availability Zones (inter-zone) within the same region. For high-availability multi-zone applications replicating 200GB of state data internally, this introduces an extra hidden cost.
* **Azure Pricing**: Historically, Azure matched this fee. However, Azure completely waived standard Availability Zone-to-Availability Zone data transfer charges, making it dramatically cheaper for highly available, multi-zone microservice clustering architectures.



### B. OS Licensing: The Azure Hybrid Benefit (AHUB) Variable
When evaluating Windows workloads, the pricing gap widens massively due to licensing optimization framework rules:
* **Azure Hybrid Benefit (AHUB)** allows enterprises to reuse their existing on-premises Windows Server licenses in the cloud. This effectively strips the retail operating system markup fee from the hourly instance cost, allowing a Windows VM to run at the baseline Linux price rate (saving roughly **$35.04/month** per instance on this specific SKU size).
* **AWS Alternative**: While AWS allows "Bring Your Own License" (BYOL), it requires dedicated, expensive hardware configurations or complex enterprise agreements, making retail-bundled OS compute pricing much more costly for smaller workloads.

---

## 4. Commitment-Based Optimization Discount Mechanisms

To transition away from expensive pay-as-you-go billing models, both providers offer steep discounts in exchange for consumption usage commitments:

### AWS Savings Plans
AWS offers a highly flexible model where you commit to a specific dollar amount of compute consumption per hour (e.g., $5.00/hr) for a 1 or 3-year period. It automatically applies up to a **72% discount** across any EC2 instance family, region, or operating system automatically, making it ideal for fast-growing dynamic architectures.

### Azure Reserved Instances (RIs)
Azure utilizes a more structured commitment model where you reserve a specific virtual machine family (such as the B-series) within a designated target region for 1 or 3 years. While slightly less flexible if you drastically change your architecture, it offers identical **savings thresholds of up to 72%** and interfaces cleanly with existing corporate billing profiles.

---

## 5. Strategic Cost-Optimization Recommendations

1.  **Transition to Compute Commitments:** Transitioning the baseline core web application from a Pay-As-You-Go On-Demand profile to a 1-Year Azure Reserved Instance or AWS Compute Savings Plan will immediately slash compute expenditures by roughly **35% to 40%**.
2.  **Implement Storage Lifecycle Management Configurations:** Object storage costs scale continuously over time. Configuring explicit structural automation bucket policies—such as setting S3 lifecycle rules or Azure Blob Access Tier rules to automatically transition older backups from the "Hot" tier to the "Glacier / Archive" tier after 30 days—reduces storage retention fees by up to **75%**.