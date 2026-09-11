# AWS High-Availability Cloud Architecture

### Description:
The Project represents a highly available, secure, and scalable AWS environment tailored for enterprise workloads. At the edge, Amazon Route 53 provides DNS routing, while Amazon CloudFront ensures low-latency global content delivery. Security is reinforced through AWS WAF for application-level protection and AWS Certificate Manager (ACM) for SSL/TLS encryption.

### Architecture:
![Architecture:](images/Architecture.png)

---

## Step-1: Web Tier
- **AWS Two EC2** instances
- Deployed in **Public Subnets**
- Load Distributed across **two Availability Zones**
- Receives user requests through an **Application Load Balancer** (ELB)

## Step-2: Database Tier
- **Amazon RDS** deployed in **private database subnets**  
- **Isolated from direct internet access** for enhanced security  
- **Accessible only from the Application Tier** within the VPC  
- **Primary Writer instance** handles all database write operations  
- **Read Replica** supports read workloads, improving performance and scalability  


