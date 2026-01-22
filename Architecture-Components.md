# Architecture Components Detail

## Legal Opinion SaaS Platform - Component Specifications

---

## 1. Frontend Application

### 1.1 Technology Stack

| Component | Technology | Purpose |
|-----------|------------|---------|
| Framework | React 18.x | UI rendering, component-based architecture |
| Language | TypeScript | Type safety, better DX |
| State Management | Zustand / React Query | Global state, server state caching |
| UI Library | Ant Design 5.x | Enterprise-grade components |
| HTTP Client | Axios | API communication |
| Form Handling | React Hook Form | Form validation, state |
| Routing | React Router 6.x | Client-side routing |
| Build Tool | Vite | Fast builds, HMR |

### 1.2 Module Structure

```
FRONTEND MODULES
────────────────

├── Auth Module
│   • Login / Logout
│   • MFA Verification
│   • Password Reset
│   • Session Management
│
├── Dashboard Module
│   • Statistics Cards
│   • Pending Tasks
│   • Recent Activity
│   • Quick Actions
│
├── Loan Requests Module
│   • Request List (filterable, sortable)
│   • Create New Request
│   • Request Details View
│   • Status Tracking
│
├── Documents Module
│   • Document Uploader (drag & drop)
│   • Document Viewer (PDF, images)
│   • Document Categorization
│   • Version History
│
├── Opinions Module
│   • Opinion List
│   • Opinion Editor (rich text)
│   • Template Selection
│   • PDF Preview
│   • Workflow Actions (Submit, Approve, Reject)
│
├── User Management Module (Admin)
│   • User List
│   • Create/Edit Users
│   • Role Assignment
│   • Access Control
│
└── Reports Module
    • Analytics Dashboard
    • Opinion Reports
    • Audit Log Viewer
    • Export Functions
```

### 1.3 Key Features

| Feature | Description |
|---------|-------------|
| **Document Upload** | Drag & drop, multi-file, progress tracking, file validation, resumable uploads |
| **Opinion Editor** | Rich text editing, templates, section management, auto-save, version history |
| **Real-time Updates** | WebSocket for status changes, notifications |
| **Offline Support** | PWA capabilities for basic offline viewing |
| **Responsive Design** | Desktop-first with tablet support |

---

## 2. Backend Microservices

### 2.1 AI/LLM Processing Overview

The platform uses AI/LLM capabilities for two critical functions:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    AI/LLM PROCESSING PIPELINE                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   STEP 1: DOCUMENT UPLOAD                                                   │
│   ┌───────────────────────────────────────────────────────────────────────┐ │
│   │  User uploads documents (PDF, images) → Stored in S3                  │ │
│   └───────────────────────────────────────────────────────────────────────┘ │
│                              │                                               │
│                              ▼                                               │
│   STEP 2: OCR EXTRACTION                                                    │
│   ┌───────────────────────────────────────────────────────────────────────┐ │
│   │  Amazon Textract extracts raw text from documents                     │ │
│   │  Output: Plain text, tables, forms data                               │ │
│   └───────────────────────────────────────────────────────────────────────┘ │
│                              │                                               │
│                              ▼                                               │
│   STEP 3: LLM DATA EXTRACTION                                               │
│   ┌───────────────────────────────────────────────────────────────────────┐ │
│   │  Amazon Bedrock (Claude) processes raw text with extraction prompt    │ │
│   │  Input: Raw text + Document type + Extraction prompt template         │ │
│   │  Output: Structured JSON with extracted fields + confidence scores    │ │
│   │                                                                        │ │
│   │  Extracted fields include:                                            │ │
│   │  • Property details (address, area, boundaries)                       │ │
│   │  • Party details (seller, buyer, guarantor names & IDs)              │ │
│   │  • Financial details (amounts, dates, terms)                         │ │
│   │  • Legal details (registration numbers, EC details)                  │ │
│   └───────────────────────────────────────────────────────────────────────┘ │
│                              │                                               │
│                              ▼                                               │
│   STEP 4: VALIDATION & STORAGE                                              │
│   ┌───────────────────────────────────────────────────────────────────────┐ │
│   │  Extracted data validated and stored in database                      │ │
│   │  Low-confidence extractions flagged for manual review                 │ │
│   └───────────────────────────────────────────────────────────────────────┘ │
│                              │                                               │
│                              ▼                                               │
│   STEP 5: OPINION GENERATION                                                │
│   ┌───────────────────────────────────────────────────────────────────────┐ │
│   │  Amazon Bedrock (Claude) generates draft opinion                      │ │
│   │  Input: Extracted data + Opinion template + Loan type                 │ │
│   │  Output: Draft legal opinion in structured format                     │ │
│   └───────────────────────────────────────────────────────────────────────┘ │
│                              │                                               │
│                              ▼                                               │
│   STEP 6: LAWYER REVIEW                                                     │
│   ┌───────────────────────────────────────────────────────────────────────┐ │
│   │  Lawyer reviews AI-generated draft                                    │ │
│   │  Options: Accept / Edit / Regenerate / Write manually                 │ │
│   │  Final opinion submitted for approval workflow                        │ │
│   └───────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 2.2 Service Overview

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        MICROSERVICES ARCHITECTURE                            │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   ┌───────────────────────────────────────────────────────────────────────┐ │
│   │                         API GATEWAY SERVICE                            │ │
│   │                                                                        │ │
│   │  Responsibilities:                                                     │ │
│   │  • Request routing to appropriate microservices                       │ │
│   │  • JWT token validation                                               │ │
│   │  • Rate limiting (per tenant, per user)                               │ │
│   │  • Request/Response logging                                           │ │
│   │  • CORS handling                                                      │ │
│   │  • API versioning (/v1, /v2)                                         │ │
│   │  • Circuit breaker pattern                                            │ │
│   │                                                                        │ │
│   │  Rate Limits:                                                         │ │
│   │  • Anonymous: 10 req/min                                              │ │
│   │  • Authenticated: 100 req/min                                         │ │
│   │  • Premium Tenant: 500 req/min                                        │ │
│   │                                                                        │ │
│   └───────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
│   ┌───────────────────────────────────────────────────────────────────────┐ │
│   │                          USER SERVICE                                  │ │
│   │                                                                        │ │
│   │  Responsibilities:                                                     │ │
│   │  • User registration and profile management                           │ │
│   │  • Authentication integration (Cognito)                               │ │
│   │  • Role-based access control (RBAC)                                   │ │
│   │  • Tenant-user association                                            │ │
│   │  • Lawyer profile and credentials                                     │ │
│   │  • Activity logging                                                   │ │
│   │                                                                        │ │
│   │  Key APIs:                                                            │ │
│   │  • POST /auth/login, /auth/logout, /auth/refresh                     │ │
│   │  • GET/POST/PUT/DELETE /users                                        │ │
│   │  • GET /users/me (current user profile)                              │ │
│   │  • GET/POST /roles, PUT /roles/:id/permissions                       │ │
│   │                                                                        │ │
│   └───────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
│   ┌───────────────────────────────────────────────────────────────────────┐ │
│   │                        DOCUMENT SERVICE                                │ │
│   │                                                                        │ │
│   │  Responsibilities:                                                     │ │
│   │  • Document upload (multipart, resumable)                             │ │
│   │  • Document storage (S3 with encryption)                              │ │
│   │  • Document categorization                                            │ │
│   │  • Virus scanning integration                                         │ │
│   │  • Thumbnail generation                                               │ │
│   │  • OCR processing (optional, for searchable content)                  │ │
│   │  • Presigned URL generation for secure downloads                      │ │
│   │                                                                        │ │
│   │  Key APIs:                                                            │ │
│   │  • POST /documents/presigned-url (get upload URL)                    │ │
│   │  • POST /documents/upload (direct upload)                            │ │
│   │  • GET /documents/:id (metadata)                                     │ │
│   │  • GET /documents/:id/download (presigned download URL)              │ │
│   │  • DELETE /documents/:id                                             │ │
│   │                                                                        │ │
│   └───────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
│   ┌───────────────────────────────────────────────────────────────────────┐ │
│   │                         OPINION SERVICE                                │ │
│   │                                                                        │ │
│   │  Responsibilities:                                                     │ │
│   │  • Opinion request creation and management                            │ │
│   │  • Workflow management (state machine)                                │ │
│   │  • Template management                                                │ │
│   │  • PDF generation with watermarks                                     │ │
│   │  • Opinion versioning                                                 │ │
│   │  • Digital signature integration (future)                             │ │
│   │                                                                        │ │
│   │  Key APIs:                                                            │ │
│   │  • GET/POST /opinions                                                │ │
│   │  • GET/PUT /opinions/:id                                             │ │
│   │  • PATCH /opinions/:id/status (workflow transitions)                 │ │
│   │  • GET /opinions/:id/pdf                                             │ │
│   │  • GET/POST/PUT/DELETE /templates                                    │ │
│   │                                                                        │ │
│   └───────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
│   ┌───────────────────────────────────────────────────────────────────────┐ │
│   │                    AI/LLM SERVICE (Document Intelligence)             │ │
│   │                                                                        │ │
│   │  Responsibilities:                                                     │ │
│   │  • OCR coordination (trigger Amazon Textract)                         │ │
│   │  • LLM prompt management for data extraction                          │ │
│   │  • Structured data extraction from raw text (Bedrock)                 │ │
│   │  • Confidence scoring for extracted fields                            │ │
│   │  • Manual review flagging for low-confidence extractions              │ │
│   │  • Extraction result validation                                       │ │
│   │                                                                        │ │
│   │  Key APIs:                                                            │ │
│   │  • POST /ai/extract/:documentId (trigger extraction)                 │ │
│   │  • GET /ai/extractions/:documentId (get extraction results)          │ │
│   │  • PUT /ai/extractions/:id/review (manual correction)                │ │
│   │  • GET /ai/extraction-prompts (list prompt templates)                │ │
│   │                                                                        │ │
│   └───────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
│   ┌───────────────────────────────────────────────────────────────────────┐ │
│   │                 OPINION GENERATION SERVICE (LLM-Powered)              │ │
│   │                                                                        │ │
│   │  Responsibilities:                                                     │ │
│   │  • Draft opinion generation using LLM (Bedrock)                       │ │
│   │  • Opinion template + extracted data → generation prompt              │ │
│   │  • Multi-turn generation for complex opinions                         │ │
│   │  • Draft versioning and regeneration                                  │ │
│   │  • Audit logging of all AI interactions                               │ │
│   │                                                                        │ │
│   │  Key APIs:                                                            │ │
│   │  • POST /ai/generate-opinion/:requestId (generate draft)             │ │
│   │  • POST /ai/regenerate-opinion/:draftId (regenerate)                 │ │
│   │  • GET /ai/opinion-drafts/:requestId (list drafts)                   │ │
│   │  • PUT /ai/opinion-drafts/:id/accept (accept draft)                  │ │
│   │  • GET /ai/generation-templates (list templates)                     │ │
│   │                                                                        │ │
│   └───────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
│   ┌───────────────────────────────────────────────────────────────────────┐ │
│   │                      NOTIFICATION SERVICE                              │ │
│   │                                                                        │ │
│   │  Responsibilities:                                                     │ │
│   │  • Email notifications (via SES)                                      │ │
│   │  • In-app notifications                                               │ │
│   │  • SMS notifications (optional, via SNS)                              │ │
│   │  • Notification preferences per user                                  │ │
│   │  • Template-based notifications                                       │ │
│   │                                                                        │ │
│   │  Notification Events:                                                 │ │
│   │  • Request created, assigned                                          │ │
│   │  • Documents uploaded                                                 │ │
│   │  • Opinion submitted, revision requested, approved, published        │ │
│   │  • User invited, password reset                                       │ │
│   │                                                                        │ │
│   └───────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
│   ┌───────────────────────────────────────────────────────────────────────┐ │
│   │                          AUDIT SERVICE                                 │ │
│   │                                                                        │ │
│   │  Responsibilities:                                                     │ │
│   │  • Audit log ingestion (async via SQS)                                │ │
│   │  • Audit log storage (PostgreSQL + S3 for archive)                    │ │
│   │  • Compliance reporting                                               │ │
│   │  • Data retention management                                          │ │
│   │  • Audit log search and export                                        │ │
│   │                                                                        │ │
│   │  Logged Actions:                                                      │ │
│   │  • CREATE, UPDATE, DELETE operations                                  │ │
│   │  • VIEW, DOWNLOAD (document access)                                   │ │
│   │  • STATUS_CHANGE (workflow transitions)                               │ │
│   │  • LOGIN, LOGOUT (authentication events)                              │ │
│   │                                                                        │ │
│   └───────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 2.2 Technology Options

| Option | Framework | Language | Best For |
|--------|-----------|----------|----------|
| Option A | NestJS | TypeScript | Full-stack JS teams, rapid development |
| Option B | FastAPI | Python | Data processing, ML integration |
| Option C | Spring Boot | Java | Enterprise teams, existing Java expertise |

**Recommendation:** NestJS (TypeScript) for consistency with frontend team and rapid development.

### 2.3 Service Communication

| Pattern | Use Case | Technology |
|---------|----------|------------|
| **Synchronous** | User authentication, real-time queries | REST / gRPC |
| **Asynchronous** | Document processing, notifications, audit logging | Amazon SQS |
| **Event-Driven** | Status changes, inter-service notifications | Amazon SNS + SQS |

---

## 3. Data Architecture

### 3.1 Primary Entities

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                          ENTITY RELATIONSHIPS                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│                                                                              │
│   ┌──────────────┐         ┌──────────────┐                                │
│   │   TENANTS    │◄────────│    USERS     │                                │
│   │   (Banks)    │ 1    N  │   (Lawyers,  │                                │
│   │              │         │   Officers)  │                                │
│   └──────────────┘         └──────────────┘                                │
│          │                        │                                         │
│          │                        │                                         │
│          │ 1                      │ N                                       │
│          ▼                        ▼                                         │
│   ┌──────────────┐         ┌──────────────┐                                │
│   │  BORROWERS   │◄────────│LOAN_REQUESTS │                                │
│   │              │ 1    N  │              │                                │
│   └──────────────┘         └──────┬───────┘                                │
│                                   │                                         │
│                    ┌──────────────┼──────────────┐                         │
│                    │              │              │                         │
│                    │ 1          N │            1 │                         │
│                    ▼              ▼              ▼                         │
│            ┌──────────────┐ ┌──────────────┐ ┌──────────────┐             │
│            │  DOCUMENTS   │ │   OPINIONS   │ │   COMMENTS   │             │
│            │              │ │              │ │              │             │
│            └──────────────┘ └──────┬───────┘ └──────────────┘             │
│                                    │                                       │
│                                    │ 1                                     │
│                                    ▼                                       │
│                            ┌──────────────┐                                │
│                            │  TEMPLATES   │                                │
│                            │              │                                │
│                            └──────────────┘                                │
│                                                                              │
│                                                                              │
│   ┌──────────────┐                                                         │
│   │ AUDIT_LOGS   │  (Linked to all entities via entity_type + entity_id)  │
│   └──────────────┘                                                         │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 3.2 Key Entities Description

| Entity | Description | Key Fields |
|--------|-------------|------------|
| **Tenants** | Bank organizations | name, code, subscription_tier, settings, branding |
| **Users** | All system users | email, role, profile, tenant_id, cognito_sub |
| **Borrowers** | Loan applicants | name, contact, PAN, Aadhaar, address, type (individual/company) |
| **Loan Requests** | Opinion request | reference_number, loan_type, amount, status, assigned_lawyer |
| **Documents** | Uploaded files | doc_type, s3_path, scan_status, ocr_text, metadata |
| **Opinions** | Legal opinions | content, status, recommendation, conditions, pdf_path |
| **Templates** | Opinion templates | name, content, placeholders, loan_type |
| **Audit Logs** | All actions | entity_type, entity_id, action, old_values, new_values |

### 3.3 Document Types

| Category | Document Types |
|----------|----------------|
| **Property** | Sale Deed, Title Deed, Encumbrance Certificate, Mutation Record, Property Tax Receipt |
| **Identity** | Aadhaar Card, PAN Card, Voter ID, Passport, Driving License |
| **Financial** | Bank Statements, ITR, Balance Sheet, P&L Statement, Form 26AS |
| **Collateral** | Hypothecation Agreement, Mortgage Deed, NOC, Insurance Policy |
| **Guarantor** | Personal Guarantee, Net Worth Statement, Guarantor KYC |

### 3.4 Opinion Statuses

| Status | Description | Next States |
|--------|-------------|-------------|
| **DRAFT** | Opinion being created | SUBMITTED |
| **SUBMITTED** | Sent for review | UNDER_REVIEW |
| **UNDER_REVIEW** | Senior advocate reviewing | REVISION_REQUESTED, APPROVED, REJECTED |
| **REVISION_REQUESTED** | Changes needed | DRAFT |
| **APPROVED** | Approved by senior | PUBLISHED |
| **REJECTED** | Rejected by senior | (Terminal) |
| **PUBLISHED** | Final, PDF generated | (Terminal) |

---

## 4. AWS Services Mapping

### 4.1 Core Services

| Purpose | AWS Service | Configuration |
|---------|-------------|---------------|
| **Compute** | EKS | 3-10 nodes, m5.large, auto-scaling |
| **Database** | RDS PostgreSQL | db.r6g.large, Multi-AZ, encrypted |
| **Cache** | ElastiCache Redis | cache.r6g.large, cluster mode |
| **Storage** | S3 | Versioning, encryption, lifecycle policies |
| **Auth** | Cognito | User pool, MFA required |
| **Email** | SES | Verified domain, templates |
| **Queue** | SQS | Standard queues, DLQ configured |
| **CDN** | CloudFront | S3 origin for static assets |
| **DNS** | Route 53 | Hosted zone, health checks |
| **Secrets** | Secrets Manager | Auto-rotation, encrypted |
| **Monitoring** | CloudWatch | Logs, metrics, alarms |
| **OCR** | Amazon Textract | Document analysis, forms extraction |
| **LLM** | Amazon Bedrock | Claude model for extraction & generation |
| **Serverless** | Lambda | Async triggers for AI processing |

### 4.2 Security Services

| Purpose | AWS Service |
|---------|-------------|
| Firewall | AWS WAF |
| DDoS Protection | AWS Shield |
| Threat Detection | GuardDuty |
| Compliance | Security Hub |
| Key Management | KMS |
| API Audit | CloudTrail |

### 4.3 S3 Bucket Structure

```
BUCKET STRUCTURE
────────────────

legal-opinion-docs-{env}/
│
├── {tenant_id}/
│   ├── loan-requests/
│   │   └── {request_id}/
│   │       ├── property/
│   │       ├── identity/
│   │       ├── financial/
│   │       └── collateral/
│   │
│   ├── opinions/
│   │   └── {opinion_id}/
│   │       └── opinion_v{n}.pdf
│   │
│   └── templates/
│       └── {template_id}/
│
└── system/
    ├── thumbnails/
    └── temp/
```

---

## 5. Deployment Architecture

### 5.1 Environment Strategy

| Environment | Purpose | Infrastructure |
|-------------|---------|----------------|
| **Development** | Active development | Single AZ, smaller instances |
| **Staging** | QA, UAT | Production-like, smaller scale |
| **Production** | Live system | Multi-AZ, full redundancy |

### 5.2 Kubernetes Namespace Design

```
EKS NAMESPACES
──────────────

├── production
│   ├── api-gateway (3 replicas)
│   ├── user-service (2 replicas)
│   ├── document-service (2 replicas)
│   ├── opinion-service (2 replicas)
│   ├── notification-service (2 replicas)
│   └── audit-service (2 replicas)
│
├── staging
│   └── (same services, 1 replica each)
│
├── monitoring
│   ├── prometheus
│   ├── grafana
│   └── fluent-bit
│
└── ingress
    └── nginx-ingress-controller
```

### 5.3 Scaling Strategy

| Component | Min | Max | Trigger |
|-----------|-----|-----|---------|
| API Gateway Pods | 2 | 10 | CPU > 70% |
| Other Service Pods | 2 | 5 | CPU > 70% |
| EKS Nodes | 3 | 20 | Pending pods |
| RDS | - | - | Manual vertical scaling |

---

## 6. Integration Points

### 6.1 External Integrations (Future)

| Integration | Purpose | Priority |
|-------------|---------|----------|
| Bank Core Banking | Loan data sync | Phase 2 |
| e-Sign Provider | Digital signatures | Phase 2 |
| SMS Gateway | OTP, alerts | Phase 1 |
| OCR Service | Document text extraction | Phase 2 |
| Government Registries | Property verification | Phase 3 |

### 6.2 API Standards

| Standard | Description |
|----------|-------------|
| REST | All external APIs |
| JSON | Request/response format |
| JWT | Authentication tokens |
| OpenAPI 3.0 | API documentation |
| ISO 8601 | Date/time format |
| UUID v4 | Entity identifiers |

---

*This document provides component-level details. For code implementation, refer to the development phase documentation.*
