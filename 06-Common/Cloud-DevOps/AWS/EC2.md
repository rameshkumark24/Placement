
# AMAZON EC2 – COMPLETE & EASY NOTES

## 1. AWS ACCOUNT TYPES

### ROOT USER
- Owner of the entire AWS account
- Full access to all services and billing
- Created when AWS account is created
- Should NOT be used for daily work

### IAM USER (NON-OWNER)
- Created by Root User
- Limited access based on permissions
- Used for daily operations
- More secure than using Root User

---

## 2. WHAT IS AMAZON EC2?

EC2 = **Elastic Compute Cloud**

It provides:
- Virtual Servers on the Cloud
- Used for running applications, websites, software, databases

EC2 is:
- A **Virtual Computer**
- Has:
  - CPU (Processor)
  - RAM (Memory)
  - Storage (Disk)
  - Network Adapter
- Can be accessed through the **Internet**
- This virtual computer is called an **Instance**

👉 EC2 acts as a **Server**

---

## 3. IMPORTANT AWS SERVICES RELATION

| Purpose | AWS Service |
|----------|-------------|
| Server | EC2 |
| Storage | S3 |
| Network | VPC |
| Domain Name | Route 53 |
| Security & User Access | IAM |

---

## 4. EC2 DASHBOARD

Path:
```

AWS Console → EC2 → Resources → Instances

```

Hierarchy:
```

EC2 → Instance → Server

```

---

## 5. VIRTUAL MACHINE = INSTANCE

- Virtual Computer = EC2 Instance
- Each instance has:
  - Operating System
  - Storage
  - IP Address
  - Firewall rules
  - SSH Access

---

## 6. LAUNCHING AN EC2 INSTANCE (STEP BY STEP)

### 1️⃣ NAME & TAG
- Example:
```

Name: My_First_Server

```
Tags help in identifying your server.

---

### 2️⃣ AMAZON MACHINE IMAGE (AMI)
- AMI = Template for OS + Preinstalled software
- Examples:
- Amazon Linux
- Ubuntu
- Windows

You selected:
```

Amazon Linux OS

```

AMI Contains:
- Operating System
- Basic Software
- Security Settings
- Startup Configurations

---

### 3️⃣ INSTANCE TYPE (HARDWARE)

Defines:
- CPU Power
- RAM Size
- Performance

Examples:
- **t2.micro** → 1 vCPU, 1 GB RAM (Free tier)
- **R5** → Memory Optimized (Amazon)
- **C6g** → Compute Optimized (Netflix)
- **G5** → GPU Optimized (PUBG / Gaming)

Your Choice:
```

t2.micro → 1vCPU + 1GB RAM

```

---

### 4️⃣ KEY PAIR (LOGIN SECURITY)

- Required to login into EC2
- Used for SSH authentication
- Consists of:
  - Private Key (.pem file)
- Without key pair:
  ❌ You cannot login into instance

Example:
```

Key Pair Name: my-first-server-login

```

---

### 5️⃣ STORAGE (EBS - Elastic Block Store)

- Default root disk
- You selected:
```

gp3 Storage

```
Used for:
- OS
- Applications
- Data

---

### 6️⃣ NETWORK SETTINGS (VPC & IP)

- VPC provides networking
- Public IP assigned → Internet access enabled

---

### 7️⃣ SECURITY GROUP (FIREWALL)

Controls:
- Who can access your EC2
- Which ports are open

You allowed:
```

SSH → Port 22 → From 0.0.0.0/0 (Public Access)

```
⚠️ This allows access from **anywhere in the world**

---

### ✅ FINAL STEP: LAUNCH INSTANCE

Click:
```

Launch Instance

```

---

## 7. CONNECTING TO EC2 INSTANCE

Steps:
```

EC2 → Instance → Connect → EC2 Instance Connect

```
This opens:
- Linux Terminal (Command Line)

Command to check network:
```

ifconfig

```

---

## 8. EC2 INSTANCE STATES

| State | Meaning |
|--------|----------|
| Running | Server is ON |
| Stopped | Server is OFF (No billing for compute) |
| Terminated | Server is deleted permanently |

### STOP INSTANCE
- Data remains
- Instance can be restarted

### TERMINATE INSTANCE
- Instance is deleted forever
- Data is lost (unless backed up)

---

## 9. EC2 REAL-WORLD USE CASES

- Website Hosting
- App Hosting
- Database Server
- AI/ML Model Hosting
- Gaming Servers
- Video Processing
- Corporate Applications

---

## 10. EC2 PRICING MODEL (BASIC IDEA)

- Pay only for usage
- Charged for:
  - Instance runtime
  - Storage
  - Bandwidth

Types:
- On-Demand
- Reserved
- Spot Instances

---

## 11. EC2 DEPENDS ON THESE AWS SERVICES

- IAM → User access
- VPC → Network
- EBS → Storage
- Security Group → Firewall
- S3 → Backup storage
- Route 53 → Domain mapping

---

# ✅ EC2 INTERVIEW QUESTIONS & ANSWERS

### 1. What is Amazon EC2?
EC2 is a cloud service that provides virtual servers to run applications.

---

### 2. What is an EC2 Instance?
An EC2 instance is a virtual machine running on AWS.

---

### 3. Difference between Stop and Terminate?

| Stop | Terminate |
|------|------------|
| Instance OFF | Instance Deleted |
| Data Safe | Data Lost |
| Can Restart | Cannot Restart |

---

### 4. What is AMI?
AMI is a template containing OS and preinstalled software used to launch EC2 instances.

---

### 5. What is Instance Type?
It decides CPU, RAM and performance power of the instance.

---

### 6. What is Key Pair?
Used for secure login into EC2 using SSH.

---

### 7. What is Security Group?
Acts as a firewall that controls traffic to EC2.

---

### 8. What is VPC?
Virtual Private Cloud is a private network for AWS resources.

---

### 9. What is EBS?
Elastic Block Store used as hard disk for EC2.

---

### 10. Can we change instance type after creation?
Yes, but the instance must be stopped first.

---

### 11. What is Public IP?
Public IP allows internet access to EC2.

---

### 12. What happens if key pair is lost?
You cannot login. Recovery requires advanced steps.

---

### 13. What port is used for SSH?
Port 22

---

### 14. What is the Free Tier EC2 size?
t2.micro or t3.micro (1vCPU, 1GB RAM)

---

### 15. What is the default OS for EC2?
Amazon Linux

---

### 16. EC2 is IaaS or PaaS?
EC2 is **Infrastructure as a Service (IaaS)**

---

### 17. Can we host a website on EC2?
Yes, EC2 is widely used for website hosting.

---

### 18. What is Elastic IP?
A static public IP address for EC2.

---

### 19. What is Auto Scaling?
Automatically increases or decreases EC2 instances based on load.

---

### 20. What is Load Balancer?
Distributes traffic among multiple EC2 instances.


