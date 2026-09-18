# AWS Cloud Security & Governance Portfolio

**Description:** End-to-end technical documentation and evidence artifacts for AWS Account IAM Hardening, Centralized Audit Logging (CloudTrail/S3), and EC2 Network Security Baseline.

**Author:** Godfred Acheampong  
**Role:** Municipal ICT Coordinator / Cybersecurity Practitioner  
**Account Name:** `godfred-aws-lab`  
**Account ID:** `262250696282`  
**Primary Region:** `eu-north-1` (Stockholm)  
**Primary IAM Administrator:** `godfred-admin`  

---

## Executive Overview
This repository contains the technical implementation, architectural designs, and verification audit trails for securing the AWS account **`godfred-aws-lab`** (`262250696282`). The security framework spans three core operational phases:
1. **Account Hardening, Budget Governance & Identity Baseline**
2. **Centralized CloudTrail Audit Logging with Hardened S3 Storage**
3. **EC2 Network Security Hardening & Least-Privilege Firewall Controls**

---

## Phase 1: Account Hardening, Budget Governance & Identity Baseline

### Operational Scope
Established cost guardrails via AWS Billing Budgets, enforced Root account Multi-Factor Authentication (MFA), established role-based administrative delegation (`godfred-admin`), enforced virtual TOTP MFA on administrative users, and validated end-to-end credential security via console sign-in testing.

### Step-by-Step Execution Trail
1. **AWS Zero-Spend Budget Configuration:** Created a monthly zero-spend budget (`godfred-aws-budget`) with $0.01 threshold triggers and email alerts. *(Evidence: `01-aws-budget-creation.png`)*
2. **Budget Alert Verification:** Validated active monitoring with notification triggers set at 80%, 90%, and 100%. *(Evidence: `02a-aws-budget-dashboard-active.png`)*
3. **Root MFA Assignment:** Assigned virtual authenticator device `root-mfa-app` to the account root user. *(Evidence: `02c-select-mfa-device.png`)*
4. **Root MFA Pairing:** Scanned QR code and validated two consecutive TOTP codes for time synchronization. *(Evidence: `03a-mfa-qr-code-and-codes.png`)*
5. **Root MFA Confirmation:** Verified active status via green console confirmation banner. *(Evidence: `03b-mfa-assigned-confirmation.png`)*
6. **IAM Security Audit:** Verified zero security warnings on IAM Dashboard ("Root user has MFA" and "No active access keys"). *(Evidence: `04-iam-dashboard.png`)*
7. **Clean Directory Audit:** Verified initial state of 0 existing IAM users. *(Evidence: `05a-iam-users-empty.png`)*
8. **IAM Admin Creation:** Provisioned administrative identity `godfred-admin` with AWS Management Console access. *(Evidence: `05b-specify-user-details.png`)*
9. **Policy Attachment:** Assigned AWS Managed Policy `AdministratorAccess` directly to `godfred-admin`. *(Evidence: `05c-set-permissions-options.png`)*
10. **Provisioning Review:** Conducted final configuration review before executing identity creation. *(Evidence: `06-review-and-create.png`)*
11. **Portal Generation:** Retrieved custom sign-in portal URL: `https://262250696282.signin.aws.amazon.com/console`. *(Evidence: `07-retrieve-password.png`)*
12. **User List Refresh:** Confirmed active status and initial password age in IAM user directory. *(Evidence: `08a-iam-users-list.png`)*
13. **Console Login Test:** Authenticated as `godfred-admin` to confirm proper account delegation. *(Evidence: `08b-admin-login-verification.png`)*
14. **Admin MFA Selection:** Initiated virtual MFA setup (`godfred-admin-mfa`) under the `godfred-admin` context. *(Evidence: `08c-mfa-device-selection.png`)*
15. **Admin MFA Enforcement:** Completed TOTP pairing for `arn:aws:iam::262250696282:user/godfred-admin`. *(Evidence: `09-mfa-enabled-success.png`)*
16. **Secondary Auth Test:** Verified console redirection to mandatory second-factor prompt upon sign-in. *(Evidence: `10a-admin-mfa-login-prompt.png`)*
17. **TOTP Challenge:** Executed 6-digit authenticator code verification. *(Evidence: `10b-mfa-challenge-prompt.png`)*
18. **Secure Session Landing:** Established fully authenticated session under enforced multi-factor authentication. *(Evidence: `11-mfa-login-success-console.png`)*

### Technical Summary Matrix
| Step # | Configuration Phase | Target Identity | Asset Name / Parameter | Evidence File |
| :---: | :--- | :--- | :--- | :--- |
| **1** | Billing Guardrails | Account Scope | Zero-Spend Budget | `01-aws-budget-creation.png` |
| **2** | Budget Monitoring | Account Scope | `godfred-aws-budget` | `02a-aws-budget-dashboard-active.png` |
| **3** | Root MFA Selection | Root | `root-mfa-app` | `02c-select-mfa-device.png` |
| **4** | Root MFA Pairing | Root | TOTP Synchronized | `03a-mfa-qr-code-and-codes.png` |
| **5** | Root MFA Active | Root | Root Identity | `03b-mfa-assigned-confirmation.png` |
| **6** | IAM Dashboard Audit | Root | 0 Active Warnings | `04-iam-dashboard.png` |
| **7** | Baseline IAM Check | Account Scope | IAM users (0) | `05a-iam-users-empty.png` |
| **8** | IAM User Details | `godfred-admin` | Console Access Enabled | `05b-specify-user-details.png` |
| **9** | Attach Policy | `godfred-admin` | `AdministratorAccess` | `05c-set-permissions-options.png` |
| **10** | Review Creation | `godfred-admin` | Custom Password Configured | `06-review-and-create.png` |
| **11** | Credentials & Link | `godfred-admin` | Sign-in URL Generated | `07-retrieve-password.png` |
| **12** | Directory Validation | `godfred-admin` | Active User List | `08a-iam-users-list.png` |
| **13** | Console Session Test | `godfred-admin` | Console Home Login | `08b-admin-login-verification.png` |
| **14** | User MFA Setup | `godfred-admin` | `godfred-admin-mfa` | `08c-mfa-device-selection.png` |
| **15** | User MFA Active | `godfred-admin` | Active Virtual MFA | `09-mfa-enabled-success.png` |
| **16** | Primary Auth Test | `godfred-admin` | Password Submission | `10a-admin-mfa-login-prompt.png` |
| **17** | MFA Challenge | `godfred-admin` | TOTP Entry | `10b-mfa-challenge-prompt.png` |
| **18** | Secure Session | `godfred-admin` | MFA Authenticated Console | `11-mfa-login-success-console.png` |

---

## Phase 2: Centralized Audit Logging (AWS CloudTrail & Hardened S3)

### Architectural Overview
Constructed a multi-region audit trail delivering continuous management API logs to an immutable, server-side encrypted Amazon S3 bucket protected by least-privilege resource policies.

### Component Specifications
| Component | Resource Name / ID | Configuration Details | Security & Governance Purpose |
| :--- | :--- | :--- | :--- |
| **S3 Storage Bucket** | `godfred-aws-lab-sec-logs-2026` | Region: `eu-north-1`<br>Public Access: Blocked<br>Encryption: SSE-S3 | Dedicated, encrypted, and immutable destination for account-wide audit logs. |
| **S3 Bucket Policy** | Resource Policy | Grants `s3:GetBucketAcl` & `s3:PutObject` to CloudTrail principal | Enforces least-privilege write access strictly for CloudTrail while denying public write access. |
| **CloudTrail Trail** | `godfred-aws-lab-mgmt-trail` | Multi-Region: Enabled<br>Log Validation: Enabled | Captures management activity across all AWS regions with cryptographic anti-tampering verification. |
| **Logging Scope** | Management Events | Read/Write: Enabled<br>Data/Insights: Disabled | Records control-plane API actions while eliminating unnecessary data storage charges. |

### Evidence Artifact Registry
* `17a-cloudtrail-log-events.png`: Step 2 event selection page showing Management events enabled and Data/Insights disabled.
* `17b-cloudtrail-review-summary.png`: Trail summary showing multi-region status, target S3 path, and log file validation enabled.
* `18-cloudtrail-active-logging.png`: Active status banner and CloudTrail dashboard listing.
* `19-s3-cloudtrail-logs-verified.png`: Bucket path traversal demonstrating active delivery of compressed `.json.gz` log objects.

---

## Phase 3: EC2 Security Hardening & Least-Privilege Network Access

### Executive Summary
Replaced an overly-permissive default security group (`launch-wizard-1`) attached to host `portfolio-linux-lab` with a hardened security perimeter (`godfred-aws-lab-ec2-sg`). Restricted administrative SSH access exclusively to the authorized administrator IP address while maintaining public HTTP availability for web service delivery.

### Target Resource Profile
* **Instance Name:** `portfolio-linux-lab`
* **Instance ID:** `i-03c4985f821d33203`
* **VPC ID:** `vpc-02896ea2c7a7b794a`
* **Public IPv4:** `51.21.201.85`
* **Network Interface:** `eni-0b6a9ae86a585e13d`
* **Service Stack:** Apache Web Server (`httpd`)

### Inbound Firewall Policy Baseline (`godfred-aws-lab-ec2-sg`)
| Type | Protocol | Port Range | Source | Description / Purpose |
| :--- | :---: | :---: | :--- | :--- |
| **SSH** | TCP | 22 | `154.163.201.203/32` | Admin SSH Access (Strict Least Privilege) |
| **HTTP** | TCP | 80 | `0.0.0.0/0` | Public Web Access |
| **All traffic** | All | All | `0.0.0.0/0` | Outbound Egress for Updates/Dependencies |

### Step-by-Step Hardening Procedure
1. **Risk Audit:** Identified broad exposure risks on default security group `launch-wizard-1`.
2. **Custom Security Group Provisioning:** Provisioned `godfred-aws-lab-ec2-sg` in VPC `vpc-02896ea2c7a7b794a` with restricted SSH and open HTTP rules.
3. **ENI Association Swap:** Attached `godfred-aws-lab-ec2-sg` (`sg-06c20c96fc85e29d9`) to `eni-0b6a9ae86a585e13d` and detached `launch-wizard-1`.
4. **Live Verification:** Queried `http://51.21.201.85` in browser and confirmed successful load of the *Cloud & Cybersecurity Portfolio Lab* dashboard (`SYSTEM ONLINE`).

### Evidence Artifact Registry
* `20-ec2-hardened-sg-created.png`: Creation proof displaying custom SSH and HTTP rules in AWS Console.
* `21-ec2-hardened-sg-attached.png`: Confirmation banner verifying security group swap on instance `i-03c4985f821d33203`.
* `22-ec2-web-access-verified.png`: Successful HTTP browser validation displaying the live web server status page.
