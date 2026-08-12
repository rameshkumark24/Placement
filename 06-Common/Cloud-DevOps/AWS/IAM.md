# AWS IAM – COMPLETE & EASY NOTES (INDUSTRY LEVEL)

## 1. WHAT IS IAM?

IAM = **Identity and Access Management**

IAM is used to:
- Create Users
- Control Permissions
- Secure AWS Services
- Decide **Who can access What**

IAM provides:
✅ Authentication (Login)  
✅ Authorization (Permissions)

---

## 2. ROOT USER vs IAM USER

| Root User | IAM User |
|-----------|----------|
| Owner of AWS account | Non-owner |
| Full access | Limited access |
| Created at signup | Created by Root |
| Billing control | No billing control |
| Dangerous for daily use | Safe for daily work |

✅ Best Practice:  
**Never use Root User for daily work**

---

## 3. IAM COMPONENTS

| Component | Purpose |
|------------|----------|
| User | Individual login |
| Group | Collection of users |
| Policy | Permission rules |
| Role | Temporary access |
| ARN | Unique resource identity |

---

## 4. IAM USER CREATION (STEP-BY-STEP)

Path:
```

AWS Console → IAM → Users → Create User

```

### Step 1️⃣: Username
Example:
```

s3-user

```

---

### Step 2️⃣: Console Access

Checkbox:
```

✅ Provide user access to AWS Management Console

```

Password Options:
- Auto-generated
- Custom password

✅ You selected:
```

User must create new password (Recommended)

```

---

### Step 3️⃣: Permissions Options

Options:
- Add to group
- Copy permissions
- Attach policy directly ✅

You Selected:
```

Attach policy directly

```

---

### Step 4️⃣: Select Policy

Example:
```

AmazonS3FullAccess

```
OR  
Your **custom created help policy**

---

### Step 5️⃣: Create User ✅

---

## 5. IAM USER LOGIN PROCESS

After creation, user gets:

✅ Login URL  
✅ Username  
✅ Password  

Login Flow:
```

IAM Login URL → Enter Username → Enter Password → AWS Console Access

```

---

## 6. IAM POLICY (MOST IMPORTANT CONCEPT)

### What is a Policy?
A **Policy defines permissions** like:
- What actions are allowed
- On which service
- On which resource

Example Permissions:
- GetObject → Read
- PutObject → Upload
- DeleteObject → Delete

---

## 7. CREATING CUSTOM POLICY FOR S3 (STEP-BY-STEP)

Path:
```

IAM → Policies → Create Policy

```

### Step 1️⃣: Choose Service
```

S3

```

---

### Step 2️⃣: Allowed Actions

Select:
- List Bucket
- Read (GetObject)
- Write (PutObject)

❌ Do not select:
- Delete
- Permission Management
- Tagging
(If user should not control security)

---

### Step 3️⃣: Resources

If common access:
```

All Resources (*)

```

If restricted access:
```

Specific Bucket ARN

```

---

### Step 4️⃣: Policy Name
Example:
```

s3-upload-download-policy

```

Add Description:
```

Allows only S3 upload and download

```

---

### Step 5️⃣: Create Policy ✅

---

## 8. ATTACH POLICY TO IAM USER

Path:
```

IAM → Users → Select User → Permissions → Add Permission → Attach Policy Directly

```

Filter:
```

Customer Managed

```

Select:
```

s3-upload-download-policy

```

✅ Policy applied to IAM User

---

## 9. RESTRICT IAM USER TO ONLY ONE BUCKET (IMPORTANT)

By default, IAM user can access **all buckets**.

To restrict to **only one bucket**, use **Bucket Policy**.

---

## 10. BUCKET POLICY (ROOT USER CONFIGURATION)

Path:
```

S3 → Bucket → Permissions → Bucket Policy → Edit

````

---

### Example Bucket Policy (Restricted Access)

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "IAM-USER-ARN"
      },
      "Action": [
        "s3:GetObject",
        "s3:PutObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "BUCKET-ARN",
        "BUCKET-ARN/*"
      ]
    }
  ]
}
````

✅ This ensures:

* IAM user can access only this one bucket
* Cannot see any other bucket

---

## 11. IAM ROLE (INDUSTRY LEVEL)

### What is IAM Role?

A role is used to:
✅ Give **temporary permission**
✅ Avoid using access keys
✅ Used between services

Example:

```
EC2 → Access S3 → Using IAM Role
```

✅ More secure than Access Keys

---

## 12. IAM BEST SECURITY PRACTICES

✅ Never use Root user daily
✅ Enable MFA for Root
✅ Use least privilege access
✅ Do not give full admin to users
✅ Rotate access keys
✅ Use roles instead of keys
✅ Monitor using CloudTrail

---

## 13. IAM DOES NOT HAVE REGIONAL CONTROL

IAM is:
✅ Global service
❌ Not region specific

---

## 14. IAM + EC2 + S3 CONNECTION

```
IAM → User → Policy → S3 Access
IAM → Role → EC2 → S3 Backup
```

---

# ✅ AWS IAM INTERVIEW QUESTIONS & ANSWERS

### 1. What is IAM?

IAM is used to manage users and access permissions in AWS.

---

### 2. What is the difference between Root and IAM user?

| Root           | IAM             |
| -------------- | --------------- |
| Full control   | Limited control |
| Billing access | No billing      |
| Dangerous      | Safe            |

---

### 3. What is IAM Policy?

A JSON document that defines permissions.

---

### 4. What is IAM Role?

Temporary permission used by services.

---

### 5. What is ARN?

Amazon Resource Name – Unique identifier for AWS resources.

---

### 6. Can IAM user access all buckets by default?

Yes, unless restricted by bucket policy.

---

### 7. How do you restrict a user to only one S3 bucket?

Using Bucket Policy with user ARN.

---

### 8. What is MFA?

Multi-Factor Authentication for extra security.

---

### 9. Is IAM regional?

No, IAM is global.

---

### 10. Which is more secure: Access Keys or Roles?

✅ IAM Roles are more secure.

---

### 11. What happens if you delete an IAM user?

All permissions and access are permanently removed.

---

### 12. What is least privilege?

Giving only minimum required permissions.

---

### 13. What is Customer Managed Policy?

Policy created by you.

---

### 14. What is AWS Managed Policy?

Predefined policy by AWS.

---

### 15. Can IAM user access billing?

❌ Only Root user can access billing.
