# AWS High-Availability Cloud Architecture

### Description:
The Project represents a highly available, secure, and scalable AWS environment tailored for enterprise workloads. At the edge, Amazon Route 53 provides DNS routing, while Amazon CloudFront ensures low-latency global content delivery. Security is reinforced through AWS WAF for application-level protection and AWS Certificate Manager (ACM) for SSL/TLS encryption.

### Architecture:
![Architecture:](images/Architecture.png)

---
# AWS Services Used
### AWS:
AWS (Amazon Web Services) is the world’s most widely adopted cloud platform, offering over 200 fully featured services ranging from compute and storage to networking, databases, AI/ML, and security. It’s designed to help individuals, startups, and enterprises build scalable, reliable, and cost‑effective solutions.

---

### AWS Regions:
* AWS has Regions all around the world
* A region is a cluster of data centers

---

### AWS Availability Zones:
* Each region has many availability zones (usually 3, min is 3, max is 6).
* Each availability zone (AZ) is one or more discrete data centers with redundant power, 
networking, and connectivity
* They’re separate from each other, so that they’re isolated from disasters.

---

### VPC in AWS:
- **VPC (Virtual Private Cloud)**
  - You can have multiple VPCs in an AWS region  
    - Maximum: 5 per region (soft limit)
    - Maximum CIDR blocks per VPC: 5
  - CIDR block size limits:
    - Minimum: `/28` → 16 IP addresses
    - Maximum: `/16` → 65,536 IP addresses
- **Because VPC is private, only Private IPv4 ranges are allowed:**
  - `10.0.0.0 – 10.255.255.255` (`10.0.0.0/8`)
  - `172.16.0.0 – 172.31.255.255` (`172.16.0.0/12`)
  - `192.168.0.0 – 192.168.255.255` (`192.168.0.0/16`)

--- 

### Subnet: A subnet (short for subnetwork) is a logical subdivision of an IP network.
- **AWS reserves 5 IP addresses in every subnet (first 4 + last 1):**
  - These addresses are not available for use and cannot be assigned to EC2 instances.
  - **Example: CIDR block `10.0.0.0/24`**
    - `10.0.0.0` → Network Address
    - `10.0.0.1` → Reserved by AWS for the VPC router
    - `10.0.0.2` → Reserved by AWS for mapping to Amazon-provided DNS
    - `10.0.0.3` → Reserved by AWS for future use
    - `10.0.0.255` → Network Broadcast Address  
      - AWS does ***not support broadcast*** in a VPC, so this address is ***reserved***


---

## Types of subnets:
### Private subnet: 
A private Subnet does not have direct access to the internet.
A subnet where resources (like EC2 instances) are isolated from the public internet. 

### Public Subnet:
A subnet where resources can be accessed from the internet.
A Public Subnet in AWS is a subnet inside your VPC that is directly connected to the internet through an Internet Gateway.

---

## Internet Gateway (IGW)
- An Internet Gateway allows resources (e.g., EC2 instances) in a **VPC** to connect to the **Internet**.
- It scales **horizontally**, is **highly available**, and **redundant**.
- Must be created **separately** from a VPC.
- **One VPC ↔ One IGW** (a VPC can only be attached to one IGW, and an IGW can only be attached to one VPC).
---

## NAT Gateway (NATGW)
- AWS-managed **Network Address Translation (NAT)** service.
- Provides **higher bandwidth**, **high availability**, and **no administration** overhead.
- Billed **per hour** for usage and **per GB** of bandwidth.

---

### Key Points
- No **Security Groups** required or managed.
- Ideal for allowing **private EC2 instances** to access the Internet (e.g., software updates, package downloads).
- Ensures outbound connectivity while keeping instances **unreachable from the Internet**.

---

## Security Groups
- Security Groups are the **fundamentals of network security** in AWS.
- They control how traffic is allowed **into or out of EC2 instances**.
- Operate at the **instance level** (not subnet level like NACLs).

---

## Network Access Control List (NACL)

- NACLs act like a **firewall** controlling traffic **to and from subnets**.
- Each subnet is associated with **one NACL**.
- Newly created subnets are assigned the **Default NACL**.
- **Newly created NACLs** → deny all inbound/outbound traffic by default.
- **Default NACL** → allows all inbound/outbound traffic.
- NACLs are useful for **blocking specific IP addresses** at the subnet level.
- Stateless: return traffic must be explicitly allowed.
- Applied at the **subnet level**, not instance level.
- Complementary to **Security Groups** (which operate at the instance level).

---

### EC2: EC2 = Elastic Compute Cloud = Infrastructure as a Service

* Virtual servers in the cloud
* Renting computing power on demand
* We can choose the operating system, CPU, memory, storage, and networking configuration to suit your workload.

---

## Elastic Load Balancer (ELB)

- An **Elastic Load Balancer** is a **managed load balancer** provided by AWS.
- Load balancers are servers that **forward traffic** to multiple downstream servers (e.g., EC2 instances).
- ELB improves **availability, fault tolerance, and scalability** by distributing traffic intelligently.

### Types of Load Balancers

#### 1. Application Load Balancer (ALB)
- Operates at **Layer 7** (Application Layer).
- Protocols: **HTTP, HTTPS, WebSocket**.
- Best for: **Web applications, APIs, microservices**.
- Features: Content-based routing (URL, headers, cookies), supports modern protocols.

#### 2. Network Load Balancer (NLB)
- Operates at **Layer 4** (Transport Layer).
- Protocols: **TCP, TLS (secure TCP), UDP**.
- Best for: **High-performance, low-latency applications**.
- Features: Handles millions of requests per second, supports static IPs.

#### 3. Gateway Load Balancer (GWLB)
- Operates at **Layer 3** (Network Layer).
- Protocol: **IP**.
- Best for: Deploying **security appliances** (firewalls, intrusion detection/prevention).
- Features: Transparent traffic inspection, integrates with third-party appliances.

---

## Target Groups

- A **Target Group** is a logical grouping of targets:
  - **EC2 instances**
  - **IP addresses**
  - **Lambda functions**
- Load balancers (ALB, NLB, GWLB) forward traffic to one or more target groups.

### Health Checks
- Each target group has its own **health check configuration**.
- Only healthy targets receive traffic.
- Health checks can be customized (path, interval, timeout, success codes).

### Routing with ALB
- Application Load Balancers (ALB) support **rule-based routing**:
  - **Path-based routing** → e.g., `/app/*` → App target group.
  - **Host-based routing** → e.g., `api.example.com` → API target group.
- Enables **microservices architecture** and **service isolation**.

---

### Amazon RDS:
# Amazon RDS (Relational Database Service)
- RDS is a managed database service by AWS.
- It supports SQL as a query language.
- It allows you to create and manage cloud databases without manual administration.

## Supported Database Engines
- PostgreSQL
- MySQL
- MariaDB
- Oracle
- Microsoft SQL Server
- IBM DB2
- Aurora (AWS proprietary database)

## Amazon RDS Multi-AZ Deployment
- **Synchronous replication** between primary and standby
- **Single DNS name** – automatic application failover to standby
- **High availability** – minimizes downtime
- **Automatic failover** in case of:
  - Loss of Availability Zone (AZ)
  - Network failure
  - Instance failure
  - Storage failure
- **No manual intervention** required in applications

## ACM

## Cloud Front

## WAF

## Route53

## Application Request Lifecycle (End‑to‑End Request Flow)

## Web Tier
- **AWS Two EC2** instances
- Deployed in **Public Subnets**
- Load Distributed across **two Availability Zones**
- Receives user requests through an **Application Load Balancer** (ELB)

## Database Tier
- **Amazon RDS** deployed in **private database subnets**  
- **Isolated from direct internet access** for enhanced security  
- **Accessible only from the Application Tier** within the VPC  
- **Primary Writer instance** handles all database write operations  
- **Read Replica** supports read workloads, improving performance and scalability

## Application Traffic Flow and Security Layer
- **Amazon Route 53** handles DNS resolution for the application domain  
- **Amazon CloudFront** serves as the global entry point for application traffic  
- **AWS WAF** protects against common web application exploits  
- **AWS Certificate Manager (ACM)** provides SSL/TLS certificates for secure HTTPS communication  
- **Application Load Balancer (ALB)** distributes incoming traffic across EC2 instances

## Networking & Subnet Layer
- **Dedicated AWS Virtual Private Cloud (VPC)** in the **North Virginia (us-east-1) region**  
- Spans **two Availability Zones**:  
  - `us-east-1a`  
  - `us-east-1b`  
- **Public Subnets** host the application EC2 instances in the current design  
- **Private Subnets** host the Amazon RDS database  
- **Security Groups** enforce controlled communication between the ALB, EC2, and RDS layers

## Traffic flow
![Traffic flow:](images/Architecture.png)








 


