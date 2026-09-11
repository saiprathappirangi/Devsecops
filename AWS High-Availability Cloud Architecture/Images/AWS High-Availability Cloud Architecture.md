# AWS High-Availability Cloud Architecture

### Description:
The Project represents a highly available, secure, and scalable AWS environment tailored for enterprise workloads. At the edge, Amazon Route 53 provides DNS routing, while Amazon CloudFront ensures low-latency global content delivery. Security is reinforced through AWS WAF for application-level protection and AWS Certificate Manager (ACM) for SSL/TLS encryption.

### Architecture:
![Architecture:](images/Architecture.png)

---
# AWS Services Used
- Amazon VPC
- Public Subnets
- Private Subnets
- Amazon EC2
- Internet Gateway
- Security Groups
- Application Load Balancer (ALB)
- Route Tables
- Amazon RDS
- ACM
- Cloud front
- WAF
- Route53
  
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

## Step-3: Application Traffic Flow and Security Layer
- **Amazon Route 53** handles DNS resolution for the application domain  
- **Amazon CloudFront** serves as the global entry point for application traffic  
- **AWS WAF** protects against common web application exploits  
- **AWS Certificate Manager (ACM)** provides SSL/TLS certificates for secure HTTPS communication  
- **Application Load Balancer (ALB)** distributes incoming traffic across EC2 instances

## Step-4: Networking & Subnet Layer
- **Dedicated AWS Virtual Private Cloud (VPC)** in the **North Virginia (us-east-1) region**  
- Spans **two Availability Zones**:  
  - `us-east-1a`  
  - `us-east-1b`  
- **Public Subnets** host the application EC2 instances in the current design  
- **Private Subnets** host the Amazon RDS database  
- **Security Groups** enforce controlled communication between the ALB, EC2, and RDS layers

## Traffic flow
![Traffic flow:](images/Architecture.png)








 


