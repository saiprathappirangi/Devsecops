# AWS Multi‑Tier Scalable Architecture

---

## Project Description:
This project demonstrates the deployment of a highly available, secure, and scalable three‑tier web application architecture on AWS. 
It integrates load balancing, auto scaling, and multi‑AZ database redundancy to ensure resilience, while leveraging VPC networking and
security groups for controlled access across tiers.

---

## Architecture:
![Architecture](Project-1.png)

---

## Prerequisites:

* ✅ VPC
* ✅ Subnets
* ✅ Security groups configured
* ✅ NACL Default
* ✅ Four EC2 instances
* ✅ Auto scaling Groups
* ✅ Load Balancers-ALB
* ✅ AMI
* ✅ Launch Template
* ✅ NAT Gateway
* ✅ Internet Gateway
* ✅ RDS multi-Az
* ✅ SSH access to both instances
* ✅ Customer date to be inserted

---

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

### 🌐 Overview
- An Internet Gateway allows resources (e.g., EC2 instances) in a **VPC** to connect to the **Internet**.
- It scales **horizontally**, is **highly available**, and **redundant**.
- Must be created **separately** from a VPC.
- **One VPC ↔ One IGW** (a VPC can only be attached to one IGW, and an IGW can only be attached to one VPC).

### ⚠️ Important Notes
- An IGW **alone does not provide Internet access**.
- You must also configure **Route Tables**:
  - Add a route for `0.0.0.0/0` (IPv4) or `::/0` (IPv6).
  - Target → Internet Gateway (IGW).
- Without proper route table entries, instances will not be able to reach the Internet even if an IGW is attached.

### ✅ Key Points
- IGW enables **outbound traffic** from instances to the Internet and **inbound traffic** from the Internet to instances.
- Works together with **Security Groups** and **NACLs** to control traffic.
- Essential for **public subnets** in a VPC design.


---

## NAT Gateway (NATGW)

### 🔐 Overview
- AWS-managed **Network Address Translation (NAT)** service.
- Provides **higher bandwidth**, **high availability**, and **no administration** overhead.
- Billed **per hour** for usage and **per GB** of bandwidth.

### ⚙️ Characteristics
- Created in a **specific Availability Zone (AZ)**.
- Uses an **Elastic IP** for outbound traffic.
- Cannot be used by EC2 instances in the **same subnet** (only accessible from other subnets).
- Requires an **Internet Gateway (IGW)** for outbound traffic:
  - Private Subnet → NAT Gateway → IGW → Internet

### 📊 Performance
- **5 Gbps** of bandwidth by default.
- Automatically scales up to **100 Gbps**.

### ✅ Key Points
- No **Security Groups** required or managed.
- Ideal for allowing **private EC2 instances** to access the Internet (e.g., software updates, package downloads).
- Ensures outbound connectivity while keeping instances **unreachable from the Internet**.


---

## Security Groups

### 🔐 Overview
- Security Groups are the **fundamentals of network security** in AWS.
- They control how traffic is allowed **into or out of EC2 instances**.
- Operate at the **instance level** (not subnet level like NACLs).

### 📜 Rules
- Security Groups contain **allow rules only** (no deny rules).
- Rules can reference:
  - **IP addresses** (CIDR ranges).
  - **Other Security Groups** (for instance-to-instance communication).
- All rules are evaluated before deciding whether to allow traffic.

### ⚙️ Behavior
- **Stateful**: return traffic is automatically allowed, regardless of rules.
- Applied only when explicitly associated with an EC2 instance.
- Default Security Group allows **all outbound traffic** but no inbound traffic.

### ✅ Key Points
- Best practice: restrict SSH (port 22) to your own IP, not `0.0.0.0/0`.
- Use Security Group references for cleaner architecture (e.g., ALB SG → EC2 SG).
- Multiple Security Groups can be attached to a single EC2 instance.


---

## Network Access Control List (NACL)

### 🔐 Overview
- NACLs act like a **firewall** controlling traffic **to and from subnets**.
- Each subnet is associated with **one NACL**.
- Newly created subnets are assigned the **Default NACL**.

### 📜 NACL Rules
- Rules are numbered **1–32766**.
- **Lower numbers = higher precedence**.
- The **first matching rule** determines the decision.
- Example:
  - Rule #100 → `ALLOW 10.0.0.10/32`
  - Rule #200 → `DENY 10.0.0.10/32`
  - Result → Allowed (because #100 has higher precedence).
- The **last rule** is an asterisk `*` → denies traffic if no match.
- AWS recommends adding rules in increments of **100**.

### ⚠️ Behavior
- **Newly created NACLs** → deny all inbound/outbound traffic by default.
- **Default NACL** → allows all inbound/outbound traffic.
- NACLs are useful for **blocking specific IP addresses** at the subnet level.

### ✅ Key Points
- Stateless: return traffic must be explicitly allowed.
- Applied at the **subnet level**, not instance level.
- Complementary to **Security Groups** (which operate at the instance level).


---

### EC2: EC2 = Elastic Compute Cloud = Infrastructure as a Service
*virtual servers in the cloud
*renting computing power on demand
* We can choose the operating system, CPU, memory, storage, and networking configuration to suit your workload.

---

### Elastic Load Balancer: An Elastic Load Balancer is a managed load balancer
*Load Balances are servers that forward traffic to multiple 
servers (e.g., EC2 instances) downstream.
## Types Of Load balancer:
*Application Load Balancer- Operates at layer 7 - (application layer)-Protocol-HTTP, HTTPS, WebSocket
*Network Load Balancer- operates at layer 4 - Protocol- TCP, TLS (secure TCP), UDP
*Gateway Load Balancer- Operates at layer 3 (Network layer) – IP Protocol

---

### Target Groups:
*A target group is a logical grouping of targets (EC2 instances, IPs, or Lambda functions).
* Load balancers (Application Load Balancer, Network Load Balancer, Gateway Load Balancer) forward traffic to one or more target groups.
*Each target group has its own health check configuration to determine if targets are healthy and can receive traffic.
*With the help of application Load Balancers, you can route requests based on rules (e.g., path-based or host-based routing) to different target groups.

---

### Launch template:
*A Launch Template in AWS EC2 is a saved blueprint that contains all the settings needed to start an instance — 
like the AMI, instance type, key pair, security groups, and user data.

--- 

### AMI:
*An AMI (Amazon Machine Image) is a pre-configured template that contains the operating system, application server, and applications needed to launch an EC2 instance.
*Ready-made image used to create EC2 instances.

---

## Auto Scaling Group (ASG)

### 🌐 Real-life Context
- Website and application load can change dynamically.
- In the cloud, servers can be created or terminated quickly.

### 🎯 Goals of an ASG
- **Scale out** → Add EC2 instances to handle increased load.
- **Scale in** → Remove EC2 instances when load decreases.
- Maintain a **minimum** and **maximum** number of EC2 instances.
- Automatically **register new instances** with a Load Balancer.
- **Replace unhealthy instances** (re-create EC2 if terminated).

### 💰 Cost
- ASG itself is **free**.
- You only pay for the **underlying EC2 instances** that are launched.

### 📊 Key Benefits
- High availability
- Fault tolerance
- Cost efficiency
- Automatic elasticity



---

### Amazon RDS:
# Amazon RDS (Relational Database Service)

## Overview
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

### 🔑 Key Features
- **Synchronous replication** between primary and standby
- **Single DNS name** – automatic application failover to standby
- **High availability** – minimizes downtime
- **Automatic failover** in case of:
  - Loss of Availability Zone (AZ)
  - Network failure
  - Instance failure
  - Storage failure
- **No manual intervention** required in applications

---

### Process Steps:

## Step-1: Create VPC and Subnets by using option called VPC and more



## Step-2: Launch an EC2 Instances in Public Subnet and Private Subnet 


## Step-3: Attach NAT Gateway to the two Private subnets

## Step-4: Configure Security Groups 

## Step-5: SSH to EC2 Instances 

## Step-6: Create AMI image and Launch template 

## Step-7: Launch New two instances in public and private subnets accordingly by using own AMI

## Step-8: Create Target Groups for both External and Internal Load balancers

## Step-9: Create Application load balancer for both Web and App layers

## Step-10: Create RDS Data base for multi-AZ

## Step-10: Create Subnet for RDS and Configure the necessary connection

## Step-11: SSH from Web Tier to Application to Database

## Step-11: Insert data into Database

## Step-12: Verify Output 













               

