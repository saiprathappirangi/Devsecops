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

# step 1: Launch Ec2 instance
Create the following Two EC2 Instances
* Ubuntu
* Amazon



# Security Group:
In SG the add following inbound rules

Type	Protocal	Port	Source

SSH	TCP	22	0.0.0.0/0
HTTP	TCP	80	0.0.0.0/0
NFS	TCP	2049	0.0.0.0/0
<img width="361" height="97" alt="image" src="https://github.com/user-attachments/assets/35747c76-a3ac-4839-9334-2a87e5681c5d" />













