# Security & Compliance Framework

## Legal Opinion SaaS Platform

---

## 1. Executive Summary

This document outlines the security architecture and compliance framework for the Legal Opinion SaaS Platform. Given that the platform handles sensitive financial and legal documents for banks in India, robust security and regulatory compliance are paramount.

---

## 2. Regulatory Landscape (India)

### 2.1 Applicable Regulations

| Regulation | Applicability | Key Requirements |
|------------|---------------|------------------|
| **RBI IT Guidelines** | Banking data processing | Data security, audit trails, access controls |
| **IT Act 2000** | All IT systems | Data protection, cyber security |
| **DPDP Act 2023** | Personal data processing | Consent, data minimization, rights |
| **IRDAI Guidelines** | If insurance documents involved | Data handling, security |
| **Aadhaar Act** | If Aadhaar data processed | Encryption, access controls |

### 2.2 Key Compliance Requirements

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    COMPLIANCE REQUIREMENTS                                   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  DATA RESIDENCY                                                             │
│  ┌────────────────────────────────────────────────────────────────────────┐ │
│  │ • All data must reside within India                                    │ │
│  │ • AWS Mumbai region (ap-south-1) selected                             │ │
│  │ • Backups also stored within India                                     │ │
│  │ • No data transfer outside India without consent                       │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
│  DATA PROTECTION                                                             │
│  ┌────────────────────────────────────────────────────────────────────────┐ │
│  │ • PII encryption mandatory                                             │ │
│  │ • Aadhaar numbers encrypted with AES-256                              │ │
│  │ • Data masking in logs and non-production environments                │ │
│  │ • Right to erasure implementation                                      │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
│  AUDIT & LOGGING                                                            │
│  ┌────────────────────────────────────────────────────────────────────────┐ │
│  │ • Complete audit trail for all operations                             │ │
│  │ • Log retention: 7 years (RBI requirement)                            │ │
│  │ • Tamper-proof logging                                                 │ │
│  │ • Regular audit reviews                                                │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
│  ACCESS CONTROL                                                              │
│  ┌────────────────────────────────────────────────────────────────────────┐ │
│  │ • Role-based access control (RBAC)                                    │ │
│  │ • Multi-factor authentication mandatory                               │ │
│  │ • Periodic access reviews                                              │ │
│  │ • Privileged access management                                         │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Security Architecture

### 3.1 Defense in Depth Model

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         DEFENSE IN DEPTH                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   ╔═══════════════════════════════════════════════════════════════════════╗ │
│   ║ LAYER 1: PERIMETER SECURITY                                           ║ │
│   ║                                                                        ║ │
│   ║  • AWS WAF (Web Application Firewall)                                 ║ │
│   ║    - OWASP Top 10 rule sets                                           ║ │
│   ║    - SQL injection prevention                                         ║ │
│   ║    - Cross-site scripting (XSS) blocking                             ║ │
│   ║    - Rate-based rules for DDoS mitigation                            ║ │
│   ║                                                                        ║ │
│   ║  • AWS Shield Standard                                                ║ │
│   ║    - Automatic DDoS protection                                        ║ │
│   ║    - Network and transport layer protection                          ║ │
│   ║                                                                        ║ │
│   ║  • CloudFront                                                         ║ │
│   ║    - Edge-level security                                              ║ │
│   ║    - Geo-restriction capabilities                                     ║ │
│   ║    - SSL/TLS termination                                              ║ │
│   ║                                                                        ║ │
│   ╚═══════════════════════════════════════════════════════════════════════╝ │
│                                    │                                         │
│                                    ▼                                         │
│   ╔═══════════════════════════════════════════════════════════════════════╗ │
│   ║ LAYER 2: NETWORK SECURITY                                             ║ │
│   ║                                                                        ║ │
│   ║  • VPC Architecture                                                   ║ │
│   ║    - Dedicated VPC for isolation                                      ║ │
│   ║    - Public subnets: Only load balancers, NAT gateways               ║ │
│   ║    - Private subnets: Application and database tiers                 ║ │
│   ║                                                                        ║ │
│   ║  • Security Groups (Stateful)                                         ║ │
│   ║    - Principle of least privilege                                     ║ │
│   ║    - Service-specific rules                                           ║ │
│   ║    - No 0.0.0.0/0 inbound rules                                       ║ │
│   ║                                                                        ║ │
│   ║  • Network ACLs (Stateless)                                           ║ │
│   ║    - Subnet-level protection                                          ║ │
│   ║    - Block known malicious IPs                                        ║ │
│   ║                                                                        ║ │
│   ║  • VPC Flow Logs                                                      ║ │
│   ║    - Traffic monitoring                                               ║ │
│   ║    - Anomaly detection                                                ║ │
│   ║                                                                        ║ │
│   ╚═══════════════════════════════════════════════════════════════════════╝ │
│                                    │                                         │
│                                    ▼                                         │
│   ╔═══════════════════════════════════════════════════════════════════════╗ │
│   ║ LAYER 3: IDENTITY & ACCESS MANAGEMENT                                 ║ │
│   ║                                                                        ║ │
│   ║  • Amazon Cognito                                                     ║ │
│   ║    - User authentication                                              ║ │
│   ║    - MFA enforcement (mandatory)                                      ║ │
│   ║    - Password policies (12+ chars, complexity)                        ║ │
│   ║    - Account lockout after failed attempts                           ║ │
│   ║                                                                        ║ │
│   ║  • JWT Tokens                                                         ║ │
│   ║    - Short-lived access tokens (60 minutes)                          ║ │
│   ║    - Refresh token rotation                                           ║ │
│   ║    - Token validation on every request                               ║ │
│   ║                                                                        ║ │
│   ║  • RBAC (Role-Based Access Control)                                   ║ │
│   ║    - Granular permissions                                             ║ │
│   ║    - Role hierarchy                                                   ║ │
│   ║    - Tenant-level isolation                                           ║ │
│   ║                                                                        ║ │
│   ╚═══════════════════════════════════════════════════════════════════════╝ │
│                                    │                                         │
│                                    ▼                                         │
│   ╔═══════════════════════════════════════════════════════════════════════╗ │
│   ║ LAYER 4: APPLICATION SECURITY                                         ║ │
│   ║                                                                        ║ │
│   ║  • Input Validation                                                   ║ │
│   ║    - Server-side validation for all inputs                           ║ │
│   ║    - Parameterized queries (SQL injection prevention)                ║ │
│   ║    - Content type validation for uploads                             ║ │
│   ║                                                                        ║ │
│   ║  • Secure File Handling                                               ║ │
│   ║    - Virus scanning for all uploads (ClamAV)                         ║ │
│   ║    - File type validation (magic bytes)                              ║ │
│   ║    - Size limits enforcement                                          ║ │
│   ║                                                                        ║ │
│   ║  • API Security                                                       ║ │
│   ║    - Rate limiting per user/tenant                                    ║ │
│   ║    - Request size limits                                              ║ │
│   ║    - CORS configuration                                               ║ │
│   ║                                                                        ║ │
│   ║  • Security Headers                                                   ║ │
│   ║    - Content-Security-Policy                                          ║ │
│   ║    - X-Frame-Options                                                  ║ │
│   ║    - X-Content-Type-Options                                           ║ │
│   ║    - Strict-Transport-Security                                        ║ │
│   ║                                                                        ║ │
│   ╚═══════════════════════════════════════════════════════════════════════╝ │
│                                    │                                         │
│                                    ▼                                         │
│   ╔═══════════════════════════════════════════════════════════════════════╗ │
│   ║ LAYER 5: DATA SECURITY                                                ║ │
│   ║                                                                        ║ │
│   ║  • Encryption at Rest                                                 ║ │
│   ║    - RDS: AES-256 with AWS KMS                                        ║ │
│   ║    - S3: SSE-KMS encryption                                           ║ │
│   ║    - ElastiCache: At-rest encryption enabled                         ║ │
│   ║    - EBS volumes: Encrypted                                           ║ │
│   ║                                                                        ║ │
│   ║  • Encryption in Transit                                              ║ │
│   ║    - TLS 1.3 for all communications                                   ║ │
│   ║    - Certificate management via ACM                                   ║ │
│   ║    - Internal service communication over TLS                         ║ │
│   ║                                                                        ║ │
│   ║  • Key Management                                                     ║ │
│   ║    - AWS KMS for key management                                       ║ │
│   ║    - Customer-managed keys (CMK)                                      ║ │
│   ║    - Automatic key rotation                                           ║ │
│   ║                                                                        ║ │
│   ║  • Secrets Management                                                 ║ │
│   ║    - AWS Secrets Manager                                              ║ │
│   ║    - Automatic rotation for DB credentials                           ║ │
│   ║    - No secrets in code or config files                              ║ │
│   ║                                                                        ║ │
│   ╚═══════════════════════════════════════════════════════════════════════╝ │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 3.2 Data Classification

| Classification | Examples | Security Controls |
|----------------|----------|-------------------|
| **Highly Sensitive** | Aadhaar numbers, PAN, financial data | Encrypted, masked, strict access |
| **Sensitive** | Legal opinions, borrower details | Encrypted, role-based access |
| **Internal** | System logs, metrics | Access controlled |
| **Public** | Marketing content | Standard protection |

### 3.3 PII Data Handling

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                       PII DATA HANDLING                                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   DATA TYPE           STORAGE              DISPLAY           ACCESS          │
│   ─────────           ───────              ───────           ──────          │
│                                                                              │
│   Aadhaar Number      AES-256 encrypted    Masked (XXXX-XXXX-1234)  Strict  │
│                                                                              │
│   PAN Number          AES-256 encrypted    Masked (XXXXX1234X)      Strict  │
│                                                                              │
│   Bank Account        AES-256 encrypted    Masked (XXXXXXXX1234)    Strict  │
│                                                                              │
│   Phone Number        Encrypted            Masked (+91-XXXXX-12345) RBAC    │
│                                                                              │
│   Email               Encrypted            Partial (j***@email.com) RBAC    │
│                                                                              │
│   Name                Plain text           Full                     RBAC    │
│                                                                              │
│   Address             Encrypted            Full (with permission)   RBAC    │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 4. Authentication & Authorization

### 4.1 Authentication Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    AUTHENTICATION FLOW                                       │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│                                                                              │
│   USER                    FRONTEND               COGNITO              BACKEND│
│    │                         │                      │                    │   │
│    │  1. Enter credentials   │                      │                    │   │
│    │─────────────────────────>                      │                    │   │
│    │                         │                      │                    │   │
│    │                         │  2. InitiateAuth     │                    │   │
│    │                         │─────────────────────>│                    │   │
│    │                         │                      │                    │   │
│    │                         │  3. MFA Challenge    │                    │   │
│    │                         │<─────────────────────│                    │   │
│    │                         │                      │                    │   │
│    │  4. Enter MFA code      │                      │                    │   │
│    │─────────────────────────>                      │                    │   │
│    │                         │                      │                    │   │
│    │                         │  5. VerifyMFA        │                    │   │
│    │                         │─────────────────────>│                    │   │
│    │                         │                      │                    │   │
│    │                         │  6. JWT Tokens       │                    │   │
│    │                         │<─────────────────────│                    │   │
│    │                         │                      │                    │   │
│    │                         │  7. API Request      │                    │   │
│    │                         │     + Bearer Token   │                    │   │
│    │                         │─────────────────────────────────────────>│   │
│    │                         │                      │                    │   │
│    │                         │                      │  8. Validate Token │   │
│    │                         │                      │<───────────────────│   │
│    │                         │                      │                    │   │
│    │                         │                      │  9. Token Valid    │   │
│    │                         │                      │───────────────────>│   │
│    │                         │                      │                    │   │
│    │                         │  10. Response        │                    │   │
│    │                         │<─────────────────────────────────────────│   │
│    │                         │                      │                    │   │
│    │  11. Display data       │                      │                    │   │
│    │<─────────────────────────                      │                    │   │
│    │                         │                      │                    │   │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 4.2 Password Policy

| Requirement | Value |
|-------------|-------|
| Minimum length | 12 characters |
| Uppercase required | Yes |
| Lowercase required | Yes |
| Numbers required | Yes |
| Special characters required | Yes |
| Password expiry | 90 days |
| Password history | Last 12 passwords |
| Failed login lockout | 5 attempts → 30 min lockout |

### 4.3 Role-Based Access Control (RBAC)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         RBAC MATRIX                                          │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   PERMISSION           OFFICER  ADVOCATE  SR.ADVOCATE  ADMIN  SUPER_ADMIN  │
│   ──────────           ───────  ────────  ───────────  ─────  ───────────  │
│                                                                              │
│   Create Request         ✓         ─           ─         ✓        ✓        │
│   View Own Requests      ✓         ✓           ✓         ✓        ✓        │
│   View All Requests      ─         ─           ✓         ✓        ✓        │
│   Upload Documents       ✓         ─           ─         ✓        ✓        │
│   View Documents         ✓         ✓           ✓         ✓        ✓        │
│   Download Documents     ✓         ✓           ✓         ✓        ✓        │
│   Create Opinion         ─         ✓           ✓         ─        ─        │
│   Edit Opinion           ─         ✓           ✓         ─        ─        │
│   Submit Opinion         ─         ✓           ─         ─        ─        │
│   Approve Opinion        ─         ─           ✓         ─        ─        │
│   Reject Opinion         ─         ─           ✓         ─        ─        │
│   View Reports           ─         ─           ✓         ✓        ✓        │
│   Manage Users           ─         ─           ─         ✓        ✓        │
│   Manage Templates       ─         ─           ✓         ✓        ✓        │
│   View Audit Logs        ─         ─           ─         ✓        ✓        │
│   Tenant Settings        ─         ─           ─         ✓        ✓        │
│   Platform Settings      ─         ─           ─         ─        ✓        │
│   Onboard Tenants        ─         ─           ─         ─        ✓        │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 5. Audit & Logging

### 5.1 Audit Log Requirements

| Requirement | Implementation |
|-------------|----------------|
| **What to log** | All CRUD operations, login/logout, document access, status changes |
| **Retention** | 7 years (RBI compliance) |
| **Immutability** | Write-once storage (S3 Object Lock) |
| **Accessibility** | Searchable, exportable |
| **Tamper-proof** | Hash chaining, CloudTrail integrity validation |

### 5.2 Audit Log Structure

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                      AUDIT LOG ENTRY                                         │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   {                                                                          │
│     "id": "uuid-v4",                                                        │
│     "timestamp": "2026-01-20T10:30:00.000Z",                                │
│     "tenant_id": "bank-hdfc-uuid",                                          │
│     "user_id": "user-uuid",                                                 │
│     "user_email": "john@hdfc.com",                                          │
│     "user_role": "PANEL_ADVOCATE",                                          │
│     "action": "OPINION_SUBMITTED",                                          │
│     "entity_type": "LEGAL_OPINION",                                         │
│     "entity_id": "opinion-uuid",                                            │
│     "entity_name": "Opinion #2026/HDFC/001",                                │
│     "old_values": { "status": "DRAFT" },                                    │
│     "new_values": { "status": "SUBMITTED" },                                │
│     "ip_address": "203.0.113.45",                                           │
│     "user_agent": "Mozilla/5.0...",                                         │
│     "session_id": "session-uuid",                                           │
│     "request_id": "req-uuid",                                               │
│     "metadata": {                                                            │
│       "loan_request_id": "request-uuid",                                    │
│       "opinion_version": 2                                                  │
│     }                                                                        │
│   }                                                                          │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 5.3 Log Retention & Archival

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    LOG LIFECYCLE                                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   TIMELINE            STORAGE              ACCESS                           │
│   ────────            ───────              ──────                           │
│                                                                              │
│   0 - 30 days         CloudWatch Logs      Real-time queries               │
│                       (Hot storage)         Full search capability          │
│                                                                              │
│   30 - 365 days       S3 Standard          On-demand access                │
│                       (Warm storage)        Search via Athena              │
│                                                                              │
│   1 - 7 years         S3 Glacier           Archive access                  │
│                       (Cold storage)        Retrieval in hours             │
│                       Object Lock enabled   Compliance hold                 │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 6. Incident Response

### 6.1 Security Incident Classification

| Severity | Description | Response Time | Examples |
|----------|-------------|---------------|----------|
| **P1 - Critical** | Active breach, data exfiltration | 15 minutes | Unauthorized data access, ransomware |
| **P2 - High** | Potential breach, vulnerability exploited | 1 hour | Suspicious login patterns, malware detected |
| **P3 - Medium** | Security policy violation | 4 hours | Failed MFA attempts, unusual access patterns |
| **P4 - Low** | Minor security event | 24 hours | Policy warnings, configuration drift |

### 6.2 Incident Response Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    INCIDENT RESPONSE PROCESS                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   ┌──────────────┐                                                          │
│   │   DETECT     │  GuardDuty, CloudWatch Alarms, WAF Logs                  │
│   │   Incident   │  User reports, Security scanning                         │
│   └──────┬───────┘                                                          │
│          │                                                                   │
│          ▼                                                                   │
│   ┌──────────────┐                                                          │
│   │   ASSESS     │  Determine severity (P1-P4)                              │
│   │   Severity   │  Identify scope and impact                               │
│   └──────┬───────┘                                                          │
│          │                                                                   │
│          ▼                                                                   │
│   ┌──────────────┐                                                          │
│   │   CONTAIN    │  Isolate affected systems                                │
│   │              │  Block suspicious IPs                                     │
│   │              │  Disable compromised accounts                             │
│   └──────┬───────┘                                                          │
│          │                                                                   │
│          ▼                                                                   │
│   ┌──────────────┐                                                          │
│   │  ERADICATE   │  Remove threat                                           │
│   │              │  Patch vulnerabilities                                    │
│   │              │  Clean affected systems                                   │
│   └──────┬───────┘                                                          │
│          │                                                                   │
│          ▼                                                                   │
│   ┌──────────────┐                                                          │
│   │   RECOVER    │  Restore from backups if needed                          │
│   │              │  Verify system integrity                                  │
│   │              │  Resume operations                                        │
│   └──────┬───────┘                                                          │
│          │                                                                   │
│          ▼                                                                   │
│   ┌──────────────┐                                                          │
│   │   REVIEW     │  Post-incident analysis                                  │
│   │              │  Update procedures                                        │
│   │              │  Document lessons learned                                 │
│   └──────────────┘                                                          │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 7. Business Continuity & Disaster Recovery

### 7.1 Recovery Objectives

| Metric | Target | Strategy |
|--------|--------|----------|
| **RTO** (Recovery Time Objective) | 4 hours | Multi-AZ deployment, automated failover |
| **RPO** (Recovery Point Objective) | 1 hour | Continuous replication, hourly snapshots |

### 7.2 Backup Strategy

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        BACKUP STRATEGY                                       │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   COMPONENT           BACKUP TYPE           FREQUENCY        RETENTION      │
│   ─────────           ───────────           ─────────        ─────────      │
│                                                                              │
│   RDS PostgreSQL      Automated snapshots   Daily            35 days        │
│                       Transaction logs      Continuous       7 days         │
│                       Cross-region replica  Async            Real-time      │
│                                                                              │
│   S3 Documents        Versioning            On every write   90 days        │
│                       Cross-region replica  Async            Real-time      │
│                                                                              │
│   Redis Cache         No backup             N/A              N/A            │
│                       (Reconstructible)                                      │
│                                                                              │
│   Secrets Manager     Automatic             Continuous       N/A            │
│                       (AWS managed)                                          │
│                                                                              │
│   Configuration       Git repository        On commit        Unlimited      │
│   (Terraform state)   S3 with versioning    On change        90 days        │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 7.3 Disaster Recovery Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    DISASTER RECOVERY                                         │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   PRIMARY REGION: ap-south-1 (Mumbai)                                       │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                                                                      │   │
│   │   ┌─────────────┐   ┌─────────────┐   ┌─────────────┐              │   │
│   │   │    EKS      │   │ RDS Primary │   │  S3 Bucket  │              │   │
│   │   │  Cluster    │   │ (Multi-AZ)  │   │  (Primary)  │              │   │
│   │   └─────────────┘   └──────┬──────┘   └──────┬──────┘              │   │
│   │                            │                 │                      │   │
│   └────────────────────────────┼─────────────────┼──────────────────────┘   │
│                                │                 │                          │
│                    Async Replication     Cross-Region                       │
│                                │          Replication                       │
│                                │                 │                          │
│   DR REGION: ap-south-2 (Hyderabad) - Future / Optional                    │
│   ┌────────────────────────────┼─────────────────┼──────────────────────┐   │
│   │                            │                 │                      │   │
│   │   ┌─────────────┐   ┌──────▼──────┐   ┌──────▼──────┐              │   │
│   │   │    EKS      │   │ RDS Read    │   │  S3 Bucket  │              │   │
│   │   │  (Standby)  │   │  Replica    │   │  (Replica)  │              │   │
│   │   └─────────────┘   └─────────────┘   └─────────────┘              │   │
│   │                                                                      │   │
│   │   Note: Activated only during DR event                              │   │
│   │                                                                      │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 8. Security Monitoring & Alerting

### 8.1 Monitoring Stack

| Tool | Purpose |
|------|---------|
| **AWS GuardDuty** | Threat detection, malicious activity |
| **AWS Security Hub** | Security posture management, compliance |
| **CloudWatch** | Metrics, logs, alarms |
| **AWS Config** | Configuration compliance |
| **CloudTrail** | API audit logging |

### 8.2 Security Alerts

| Alert | Condition | Severity | Action |
|-------|-----------|----------|--------|
| Brute force attack | >10 failed logins in 5 min | High | Block IP, notify security |
| Unusual data access | >100 documents accessed in 1 hour | Medium | Review, potential investigation |
| Privilege escalation | Role change without approval | Critical | Immediate investigation |
| Data exfiltration | Large download volumes | High | Block user, investigate |
| GuardDuty finding | Any HIGH severity finding | High | Immediate review |

---

## 9. Compliance Checklist

### 9.1 Pre-Launch Security Checklist

- [ ] Penetration testing completed
- [ ] Vulnerability assessment completed
- [ ] OWASP Top 10 review done
- [ ] Security code review completed
- [ ] Data encryption verified (rest & transit)
- [ ] MFA enabled for all users
- [ ] RBAC implemented and tested
- [ ] Audit logging operational
- [ ] Backup and recovery tested
- [ ] Incident response plan documented
- [ ] Security monitoring configured
- [ ] WAF rules configured
- [ ] SSL/TLS certificates installed
- [ ] Secrets management configured

### 9.2 Ongoing Compliance Activities

| Activity | Frequency |
|----------|-----------|
| Vulnerability scanning | Weekly |
| Penetration testing | Annually |
| Access review | Quarterly |
| Security training | Annually |
| DR drill | Semi-annually |
| Audit log review | Monthly |
| Configuration compliance check | Weekly |

---

## 10. AI/LLM Security Considerations

### 10.1 AI-Specific Security Risks

| Risk | Description | Mitigation |
|------|-------------|------------|
| **Prompt Injection** | Malicious input in documents could manipulate LLM | Input sanitization, prompt hardening |
| **Data Leakage** | Sensitive data exposed via LLM prompts | Use AWS Bedrock (data stays in AWS), no external APIs |
| **Hallucination** | LLM generates incorrect information | Human-in-the-loop review, confidence scoring |
| **Model Misuse** | Unauthorized use of AI capabilities | API rate limiting, usage auditing |
| **Training Data Exposure** | PII in training data | Use pre-trained models, no fine-tuning with customer data |

### 10.2 AI/LLM Data Handling

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                       AI/LLM DATA FLOW SECURITY                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   DATA HANDLING PRINCIPLES                                                  │
│   ────────────────────────                                                  │
│                                                                              │
│   ┌───────────────────────────────────────────────────────────────────────┐ │
│   │ 1. DATA RESIDENCY                                                     │ │
│   │    • All AI processing within AWS ap-south-1 (Mumbai)                │ │
│   │    • Amazon Bedrock - data does not leave AWS                        │ │
│   │    • No external LLM APIs (OpenAI, etc.) for production             │ │
│   └───────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
│   ┌───────────────────────────────────────────────────────────────────────┐ │
│   │ 2. PROMPT SECURITY                                                    │ │
│   │    • Input sanitization before LLM calls                             │ │
│   │    • System prompts stored securely, not in code                     │ │
│   │    • Prompt injection detection                                       │ │
│   │    • Output validation before storage                                │ │
│   └───────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
│   ┌───────────────────────────────────────────────────────────────────────┐ │
│   │ 3. AUDIT LOGGING                                                      │ │
│   │    • All LLM API calls logged                                        │ │
│   │    • Input prompts logged (PII masked)                               │ │
│   │    • Output responses logged                                          │ │
│   │    • Token usage tracked per tenant                                  │ │
│   └───────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
│   ┌───────────────────────────────────────────────────────────────────────┐ │
│   │ 4. HUMAN-IN-THE-LOOP                                                  │ │
│   │    • AI-generated content always reviewed by lawyer                  │ │
│   │    • Low-confidence extractions flagged for manual review            │ │
│   │    • Final opinion approval by human (Senior Advocate)               │ │
│   │    • No automated publishing of AI-generated content                 │ │
│   └───────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 10.3 Amazon Bedrock Security Configuration

| Configuration | Setting |
|---------------|---------|
| **Model Access** | Restricted to specific IAM roles |
| **VPC Endpoint** | Private access via VPC endpoint |
| **Encryption** | All data encrypted in transit (TLS 1.3) |
| **Logging** | CloudWatch logs for all invocations |
| **Data Retention** | No model training on customer data |
| **Guardrails** | Bedrock Guardrails for content filtering |

### 10.4 AI Content Labeling

All AI-generated content must be clearly labeled:
- Extraction results marked with confidence scores
- Draft opinions marked as "AI-Generated Draft"
- Final opinions include metadata on AI assistance used
- Audit trail shows AI vs human contributions

---

## 11. Data Privacy (DPDP Act 2023 Compliance)

### 10.1 Key Requirements

| Requirement | Implementation |
|-------------|----------------|
| **Consent** | Explicit consent for data collection |
| **Purpose limitation** | Data used only for stated purposes |
| **Data minimization** | Collect only necessary data |
| **Accuracy** | Keep data accurate and updated |
| **Storage limitation** | Delete data when no longer needed |
| **Security** | Appropriate security measures |
| **Accountability** | Document compliance measures |

### 10.2 Data Subject Rights

| Right | Implementation |
|-------|----------------|
| Right to access | Self-service data export |
| Right to correction | Profile editing capability |
| Right to erasure | Account deletion workflow |
| Right to grievance redressal | Grievance mechanism |

---

*This document should be reviewed and updated annually or when significant changes occur.*
