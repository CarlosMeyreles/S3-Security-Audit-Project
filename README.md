# S3 Security Audit Project

## Objective

This hands-on cloud security project focused on auditing Amazon S3 bucket permissions and enforcing least privilege access. The goal was to simulate real-world misconfigurations in an AWS environment, detect potential security issues, and remediate them using IAM policies, bucket policies, and logging.

This project gave me direct experience in applying security best practices to cloud storage, using the AWS console and IAM tools to control and test access.

## Skills Learned

- Understanding of S3 bucket security configurations  
- Identifying public access misconfigurations  
- Implementing and testing IAM user permissions  
- Writing and applying bucket policies using least privilege principles  
- Enabling and configuring S3 server access logging  

## Tools Used

- **AWS S3** – To host and manage storage buckets  
- **AWS IAM** – To create and manage user permissions  
- **AWS Policy Editor** – For JSON-based custom permission policies  
- **AWS Console** – For manual access testing and auditing  
- **Incognito Browser Sessions** – To test access as different users  

---

## Steps

### Step 1: Create S3 Buckets

**What was done:**  
Created three buckets:

- `carlos-public-bucket-test` – Simulates an insecure bucket with public access  
- `carlos-private-bucket-test` – A secure, private bucket  
- `carlos-logging-bucket-test` – Stores logs of access activity

**Why:**  
Each bucket serves a purpose in the security audit. You need one potentially vulnerable bucket, one protected bucket, and one bucket for logging.


> 🔒 Only `carlos-public-bucket-test` had public access temporarily enabled for testing. The others remained fully private.
  ![PH0-Setup- Bucket list showing all 3 buckets created](https://github.com/user-attachments/assets/4f7700e4-c417-4da0-8038-410fde34b4f4)

---

### Step 2: Upload Test Files

**What was done:**  
Uploaded a simple `.docx` file into each bucket.

**Why:**  
To simulate real data. This helps test whether users can or cannot access bucket contents based on permissions.

  ![PH0-Setup-Logging Bucket With Files ](https://github.com/user-attachments/assets/98241515-b822-4a8d-affd-4821b8d3da7c)
  ![PH0-Setup-Public Bucket With Files ](https://github.com/user-attachments/assets/992944a6-6af0-4f49-a136-be608c6c173b)
  ![PH0-Setup-Private Bucket With Files ](https://github.com/user-attachments/assets/4802dfeb-c866-469c-b817-eb59a01c3e4e)

### Step 3: Check Bucket Permissions

**What was done:**  
Reviewed each bucket’s:
- Block Public Access settings
- Bucket Policy (checked for `"Principal": "*"` meaning public)
- Access Control List (ACL) for public access

**Why:**  
To identify whether any bucket is accidentally exposing its data to the internet or unauthorized users. Which we found the `carlos-public-bucket-test` had some poor security practices.

- Check **Block Public Access**
  ![PH1- FIND SECURITY ISSUES- Block Public Access if off](https://github.com/user-attachments/assets/cf5b493a-9dfb-484a-a6e5-48ddc47f4a0d)

- Check for **Bucket Policy** with `"Principal": "*"`
  ![PH1- FIND SECURITY ISSUES- Bucket Policy has a bad policy](https://github.com/user-attachments/assets/a33a5a2a-c44f-42a0-a58c-0e880eb423e2)

- Check **Access Control List (ACL)** for public access
  ![PH1- FIND SECURITY ISSUES- ACL bad configurations ](https://github.com/user-attachments/assets/fdfff611-b0d5-4de5-af73-68915d8e1864)


### Step 4: Create IAM Users

**What was done:**  
Created three IAM users:
- `public-user`  
- `internal-audit-user`  
- `unauthorized-user`  

Each had programmatic and console access.

**Why:**  
To simulate different types of users and test what each can access:
- A user with no rights (public-user)
- A user with scoped rights (internal-audit-user)
- A user outside of allowed users (unauthorized-user)
  ![PH2-CREATE IAM USERS   TEST ACCESS -IAM user list showing all 3 users](https://github.com/user-attachments/assets/f0bf384a-29cc-4897-b3aa-b60d9f944bb7)

### Step 5: Assign Permissions

**What was done:**  
- `public-user`: No permissions  
- `unauthorized-user`: No permissions  
- `internal-audit-user`: Granted **read-only** access to `carlos-private-bucket-test` with a custom policy:

**Why:**  
- To practice least privilege — internal-audit-user only gets the exact access needed. The others simulate external users or unauthorized roles.
  ![PH2-CREATE IAM USERS   TEST ACCESS -Policy Created for internalaudituser](https://github.com/user-attachments/assets/2a528c61-bfa1-41ea-8047-4df1b5101f5d)
  ![PH2-CREATE IAM USERS   TEST ACCESS -internalaudituser permissions page](https://github.com/user-attachments/assets/4677df0f-e8fb-4418-9b9b-e60a55fc5c6d)

### Step 6: Test Access as Each User

**What was done:**  
Logged in as each IAM user and attempted to access the buckets:

- `internal-audit-user`: Should successfully access `carlos-private-bucket-test`
- `public-user`: Should get "Access Denied"
- `unauthorized-user`: Should get "Access Denied"

**Why:**  
To validate whether IAM and bucket permissions are correctly set and unauthorized access is prevented.
- `public-user` and `unauthorized-user`
  ![PH2-CREATE IAM USERS   TEST ACCESS -Public User No Access to Private Bucket ](https://github.com/user-attachments/assets/40f56168-d821-4ac9-980c-11aa3b184180)
  ![PH2-CREATE IAM USERS   TEST ACCESS -Unauthorized User No Access to Private Bucket ](https://github.com/user-attachments/assets/a525e35c-98c9-4b5a-8ba2-e5f2eb266487)

- `internal-audit-user` with access to `carlos-private-bucket-test
  ![PH2-CREATE IAM USERS   TEST ACCESS - Internal Audit User access to private bucket ](https://github.com/user-attachments/assets/48f23926-7d0e-47ee-8f4b-e176a8efe13e)


### Step 7: Block Public Access

**What was done:**  
Re-enabled all 4 **Block Public Access** settings on `carlos-public-bucket-test`.

**Why:**  
Even though we initially allowed public access for testing, it is best practice to block all public access unless there is a legitimate business case.
  ![PH3- FIX ISSUES Bucket - Updated Block Public Access settings](https://github.com/user-attachments/assets/e0686b52-5d97-4ae5-8bfa-0d35853da61b)

### Step 8: Apply Least Privilege Bucket Policy

**What was done:**  
Added the following bucket policy to `carlos-private-bucket-test`:

**Why:**  
To explicitly allow only `internal-audit-user` to access the private bucket. This ensures access is controlled using the principle of **least privilege**.
  ![2025-07-08_09-28-13](https://github.com/user-attachments/assets/bc73a2e4-df46-4c6d-9c55-edd2d04bd385)

### Step 9: Enable Server Access Logging

**What was done:**  
Enabled **Server Access Logging** on `carlos-private-bucket-test` and set `carlos-logging-bucket-test` as the destination.

**Why:**  
To capture and monitor all access requests to `carlos-private-bucket-test`. This is crucial for:

- Audit trails  
- Incident response  
- Forensic investigations
  ![PH3- FIX ISSUES Bucket - Logging configuration screen](https://github.com/user-attachments/assets/f32d80f1-ca84-448c-8604-a2d1cd3908dd)

This audit process helps meet basic controls required by the **CIS Benchmarks** and supports logging/audit requirements for compliance frameworks such as **SOC 2** and **HIPAA**. Implementing access logging aligns with industry-standard recommendations for cloud resource accountability.

## Final Summary

### Bucket Overview

| Bucket Name             | Purpose                          |
|-------------------------|----------------------------------|
| carlos-public-bucket    | Simulated public misconfiguration |
| carlos-private-bucket   | Sensitive and protected storage   |
| carlos-logging-bucket   | Stores server access logs         |

---

### Issues Found

- Public access was enabled  
- No server access logging was configured  
- IAM users were not scoped by least privilege  

---

### Fixes Applied

- Blocked all public access  
- Applied precise IAM and bucket policies  
- Enabled server access logging  


