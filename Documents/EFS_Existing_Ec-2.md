# EFS file Transfer between existing both Ubuntu and Amazon Ec-2
---
## Objective: To establish a secure, scalable, and high‑availability shared storage solution using Amazon Elastic File System (EFS) for seamless file transfer and synchronization between two existing EC2 instances within the same VPC, ensuring data consistency, minimal latency, and simplified management of shared resources.

---

## What is EFS?
***Amazon EFS – Elastic File System***
* Managed NFS (network file system) that can be mounted on many EC2
* EFS works with EC2 instances in **multi-AZ**
* Highly available, scalable, expensive (3x gp2), pay per use

---

# Architecture:
![Architecture](EFS.png.png)

---

# Prerequisites:
* Two EC2 instances (Ubuntu and Amazon Linux)
* Security groups configured
* EFS file system
* SSH access to both instances

---

  

---




