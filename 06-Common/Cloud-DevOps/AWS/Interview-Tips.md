# ✅ AWS FRESHER INTERVIEW PACK (EC2 + S3 + IAM)

## ✅ SECTION 1: BASIC AWS QUESTIONS

### 1. What is Amazon Web Services (AWS)?
AWS is a cloud computing platform that provides services like servers, storage, databases, networking, and security over the internet on a pay-as-you-go basis.

---

### 2. What are the main types of Cloud Computing services?

| Type | Meaning | Example |
|------|---------|---------|
| IaaS | Infrastructure as a Service | EC2 |
| PaaS | Platform as a Service | Elastic Beanstalk |
| SaaS | Software as a Service | Gmail |

---

### 3. What is a Region?
A Region is a geographical area where AWS data centers are located.

---

### 4. What is an Availability Zone?
An Availability Zone is an isolated data center inside a region.

---

### 5. Is IAM global or regional?
✅ IAM is a **global service**.

---

## ✅ SECTION 2: EC2 INTERVIEW QUESTIONS

### 6. What is Amazon EC2?
EC2 provides virtual servers in the cloud to run applications.

---

### 7. What is an EC2 Instance?
An EC2 instance is a virtual machine running on AWS.

---

### 8. What is AMI?
AMI (Amazon Machine Image) is a template that contains OS and preinstalled software.

---

### 9. What is an Instance Type?
It defines CPU, RAM, and performance capacity of the EC2 instance.

---

### 10. What is the Free Tier EC2 instance?
t2.micro or t3.micro (1 vCPU, 1 GB RAM)

---

### 11. What is a Key Pair?
A key pair is used for secure login into EC2 via SSH.

---

### 12. What is a Security Group?
A security group is a firewall that controls inbound and outbound traffic for EC2.

---

### 13. Which port is used for SSH?
✅ Port 22

---

### 14. Difference between Stop and Terminate?

| Stop | Terminate |
|------|------------|
| Instance Off | Instance Deleted |
| Data Safe | Data Lost |
| Can Restart | Cannot Restart |

---

### 15. Can we change instance type after launch?
✅ Yes, but the instance must be stopped first.

---

### 16. What is Elastic IP?
Elastic IP is a static public IP for EC2.

---

### 17. What is Load Balancer?
It distributes traffic among multiple EC2 instances.

---

### 18. What is Auto Scaling?
It automatically increases or decreases EC2 instances based on load.

---

## ✅ SECTION 3: S3 INTERVIEW QUESTIONS

### 19. What is Amazon S3?
S3 is a cloud object storage service for storing files and data.

---

### 20. What is a Bucket?
A bucket is a container used to store objects (files) in S3.

---

### 21. What is an Object?
A file stored inside a bucket is called an object.

---

### 22. What is S3 Versioning?
It keeps multiple versions of the same file.

---

### 23. What is S3 Glacier?
Low-cost archival storage for long-term backups.

---

### 24. Can we host a website using S3?
✅ Yes, S3 supports **static website hosting**.

---

### 25. Difference between S3 and EC2?

| S3 | EC2 |
|-----|-----|
| Storage | Compute |
| No OS | Has OS |
| Stores files | Runs applications |

---

### 26. What is S3 Bucket Policy?
It controls who can access the S3 bucket.

---

### 27. What is Object Lock?
It prevents deletion of objects for a fixed period.

---

## ✅ SECTION 4: IAM INTERVIEW QUESTIONS

### 28. What is IAM?
IAM manages users, permissions, and security in AWS.

---

### 29. Difference between Root User and IAM User?

| Root | IAM |
|------|-----|
| Owner | Normal User |
| Full Access | Limited Access |
| Billing Control | No Billing |

---

### 30. What is IAM Policy?
A JSON document that defines permissions.

---

### 31. What is IAM Role?
Temporary permission for services like EC2 to access S3.

---

### 32. What is ARN?
Amazon Resource Name – unique identifier for AWS resources.

---

### 33. What is Least Privilege?
Giving only minimum required permissions.

---

### 34. Is IAM regional?
❌ No, IAM is **global**.

---

### 35. Can IAM user access billing?
❌ No. Only Root user can access billing.

---

### 36. Which is more secure: Access Key or Role?
✅ IAM Role is more secure.

---

## ✅ SECTION 5: SCENARIO-BASED QUESTIONS

### 37. How do you allow EC2 to access S3 securely?
Using an IAM Role attached to EC2.

---

### 38. How do you restrict an IAM user to one S3 bucket?
Using Bucket Policy with IAM User ARN.

---

### 39. What happens if EC2 key pair is lost?
You cannot login into the instance.

---

### 40. If your website traffic increases suddenly, what AWS feature helps?
✅ Auto Scaling + Load Balancer

---

### 41. How do you take EC2 backup?
Using:
- Snapshot
- AMI Image

---

### 42. What service monitors EC2 CPU usage?
✅ CloudWatch

---

## ✅ SECTION 6: SHORT ONE-LINERS (VERY IMPORTANT)

- EC2 is **IaaS**
- S3 is **Object Storage**
- IAM is **Access Control**
- SSH uses **Port 22**
- HTTP uses **Port 80**
- HTTPS uses **Port 443**
- Glacier is **Archive Storage**
- VPC is **Private Network**
- Security Group is **Firewall**
- Key Pair is **Login Security**

---

# ✅ FINAL INTERVIEW TIP

If the interviewer asks:

> “Have you worked on AWS practically?”

You can answer:

✅ “Yes, I have created EC2 instances, configured Security Groups, connected using SSH, created S3 buckets, enabled versioning, controlled public access, created IAM users with custom policies, and restricted users to specific buckets using bucket policies.”

