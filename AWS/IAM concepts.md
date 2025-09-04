Here is a **detailed and structured explanation of AWS Identity and Access Management (IAM)**—one of the core security services in AWS. The content includes definitions, concepts, examples, and diagrams (in text or ASCII where applicable) to help you understand deeply.

---

# 🌐 AWS Identity and Access Management (IAM) – Full Notes

---

## 📘 1. What is IAM?

**AWS Identity and Access Management (IAM)** is a web service that helps you securely control access to AWS services and resources. Using IAM, you can create and manage AWS **users**, **groups**, **roles**, and **permissions** to allow or deny access to AWS resources.

---

## 🎯 2. Goals of IAM

* **Authentication**: Who are you? (User, service, application)
* **Authorization**: What are you allowed to do?
* **Least privilege principle**: Only give the minimum permissions required.
* **Granular access control**: Use policies to define fine-grained permissions.

---

## 🔑 3. IAM Components

### A. **Users**

* Represents a **person or service** using AWS.
* Created under your AWS account.
* Each user has:

  * Unique credentials (password/access keys)
  * Permissions via policies

**Example:**

```json
{
  "UserName": "david-dev",
  "Permissions": ["AmazonEC2FullAccess"]
}
```

---

### B. **Groups**

* A **collection of IAM users**.
* Apply the same policies to multiple users at once.
* Users can belong to multiple groups.

**Example:**

* Group: `Developers`
* Users: `david`, `ravi`, `john`
* Policy attached: `AmazonS3ReadOnlyAccess`

---

### C. **Roles**

* Used to **delegate permissions** to AWS services or federated users.
* No credentials associated.
* IAM Roles are **assumed temporarily**.

**Common use cases**:

* EC2 instance role to access S3
* Lambda role to call DynamoDB
* Cross-account access

**Example Role Policy:**

```json
{
  "Effect": "Allow",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::mybucket/*"
}
```

---

### D. **Policies**

* **JSON documents** that define permissions.
* Attached to users, groups, or roles.
* Each policy has:

  * Effect: `Allow` or `Deny`
  * Action: API call like `s3:ListBucket`
  * Resource: AWS ARN

**Example: Allow EC2 Start/Stop**

```json
{
  "Effect": "Allow",
  "Action": [
    "ec2:StartInstances",
    "ec2:StopInstances"
  ],
  "Resource": "*"
}
```

---

### E. **Permissions Boundaries**

* Advanced feature.
* Set the **maximum permissions** a user/role can have.
* Helps enforce security guardrails.

---

### F. **Service Control Policies (SCPs)**

* Used with AWS Organizations.
* Defines **limits on accounts** under an organization.
* **SCPs don’t grant** permissions, they **restrict** them.

---

## 🏛️ 4. IAM Policy Types

| Policy Type                     | Description                               | Attached To                 |
| ------------------------------- | ----------------------------------------- | --------------------------- |
| Identity-based                  | Define what a user/group/role can do      | User, Group, Role           |
| Resource-based                  | Attached directly to a resource (like S3) | Resources (e.g., S3 bucket) |
| Permissions boundaries          | Max permissions a principal can get       | User, Role                  |
| SCPs (Service Control Policies) | Org-level restrictions                    | AWS Accounts (via Org)      |
| Session policies                | Temporary limits during role assumption   | Session                     |

---

## 🔄 5. IAM Authentication Methods

| Method               | Used For                            |
| -------------------- | ----------------------------------- |
| Password             | AWS Management Console login        |
| Access Keys          | CLI, SDK, API                       |
| MFA (Multi-Factor)   | Extra security using OTP or device  |
| Roles                | EC2, Lambda, cross-account access   |
| Federated Identities | SAML, OIDC for single sign-on (SSO) |

---

## 🔐 6. IAM Best Practices

* **Use MFA** for root and users.
* **Never use root user** for day-to-day tasks.
* Use **roles for EC2 and Lambda** instead of storing keys.
* **Grant least privilege** (only needed permissions).
* Regularly **rotate access keys**.
* Use **IAM Access Analyzer** to identify over-permissive policies.
* Monitor usage with **CloudTrail and IAM Access Advisor**.

---

## 🧩 7. IAM Use Case Examples

### 1. Allow Developer to Read S3 Only

```json
{
  "Effect": "Allow",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::dev-bucket/*"
}
```

### 2. Role for EC2 to access DynamoDB

* Create IAM Role
* Attach policy:

```json
{
  "Effect": "Allow",
  "Action": ["dynamodb:*"],
  "Resource": "*"
}
```

* Assign role to EC2 instance

---

## 🏗️ 8. IAM Architecture (ASCII Diagram)

```
           +-------------------------+
           |     AWS Account         |
           +-------------------------+
                      |
          +-----------+-----------+
          |                       |
     +----v----+             +----v----+
     | IAM User|             | IAM Role|
     +---------+             +---------+
          |                       |
     +----v----+             +----v----+
     | Policies |             | Policies |
     +----------+             +----------+
```

---

## 📊 9. IAM vs Resource-based policies

| Feature                | IAM Policy           | Resource-based Policy    |
| ---------------------- | -------------------- | ------------------------ |
| Attached to            | Users, Groups, Roles | Resources like S3 bucket |
| Supports cross-account | No                   | Yes                      |
| Example                | EC2 FullAccess       | S3 bucket policy         |

---

## 🕒 10. IAM Temporal Access

* Use **STS (Security Token Service)** for temporary credentials.
* Ideal for:

  * Federation (SSO)
  * Temporary elevated access
  * Applications needing short-lived access

---

## 🧪 11. IAM Access Analyzer

* Automatically reviews policies.
* Identifies:

  * Public access
  * Cross-account access
  * Unused permissions
* Helps in **auditing** and **compliance**.

---

## 💡 12. IAM FAQs

### Q. Is IAM free?

* Yes. IAM is a **global service** and is **free of charge**.

### Q. Can IAM control access to specific EC2 instances?

* Yes, using **tags** and **conditions** in policies.

### Q. What's the difference between role and user?

* A **user** has long-term credentials.
* A **role** is **assumed temporarily**, typically by services or federated users.

---

## 📚 13. Further Reading

* AWS IAM Documentation: [https://docs.aws.amazon.com/IAM/latest/UserGuide/](https://docs.aws.amazon.com/IAM/latest/UserGuide/)
* IAM Policy Generator: [https://awspolicygen.s3.amazonaws.com/policygen.html](https://awspolicygen.s3.amazonaws.com/policygen.html)
* AWS Well-Architected Security Pillar

