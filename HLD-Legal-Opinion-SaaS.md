# High-Level Design Document
## Legal Opinion SaaS Platform for Bank Panel Advocates

**Version:** 1.0  
**Date:** January 2026  
**Status:** Draft

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Business Context](#2-business-context)
3. [System Overview](#3-system-overview)
4. [Architecture Principles](#4-architecture-principles)
5. [High-Level Architecture](#5-high-level-architecture)
6. [Component Design](#6-component-design)
7. [Multi-Tenancy Strategy](#7-multi-tenancy-strategy)
8. [Data Architecture](#8-data-architecture)
9. [Security Architecture](#9-security-architecture)
10. [Infrastructure Design](#10-infrastructure-design)
11. [CI/CD Pipeline](#11-cicd-pipeline)
12. [Non-Functional Requirements](#12-non-functional-requirements)
13. [Cost Estimation](#13-cost-estimation)
14. [Risks & Mitigations](#14-risks--mitigations)

---

## 1. Executive Summary

This document outlines the High-Level Design for a **Legal Opinion SaaS Platform** designed for panel advocates of banks in India. The platform enables lawyers to scrutinize loan application supporting documents (surety documents) and generate legally valid opinions on their authenticity and compliance.

### Key Objectives
- Provide a secure, multi-tenant SaaS platform for multiple banks
- Enable document upload, review, and legal opinion generation workflow
- Maintain audit trails and historical records for compliance
- Ensure data isolation and security across bank tenants
- Build a scalable, cost-effective solution on AWS

---

## 2. Business Context

### 2.1 Problem Statement
Banks in India engage panel advocates to verify loan application documents and provide legal opinions on the validity of surety/collateral documents. Currently, this process is:
- Manual and paper-heavy
- Lacks standardization across advocates
- Difficult to track and audit
- Time-consuming with poor visibility

### 2.2 Solution Overview
A cloud-based SaaS platform that:
- Digitizes the document submission and review process
- **AI-powered document content extraction** using OCR and LLMs
- **LLM-assisted opinion generation** using extracted data and templates
- Standardizes opinion generation with configurable templates
- Provides real-time tracking and dashboards
- Maintains complete audit trails
- Enables multi-bank (tenant) operations with data isolation

### 2.3 AI/LLM Integration Overview
The platform leverages Large Language Models (LLMs) for:
1. **Document Intelligence**: Extract structured data from uploaded documents (property details, parties, dates, amounts)
2. **Opinion Generation**: Generate draft legal opinions by combining extracted data with predefined templates
3. **Content Validation**: Identify discrepancies and flag potential issues in documents

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         AI/LLM PROCESSING FLOW                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   ┌──────────┐    ┌──────────────┐    ┌──────────────┐    ┌──────────────┐ │
│   │ Document │───►│  OCR/Text    │───►│  LLM Data    │───►│  Structured  │ │
│   │  Upload  │    │  Extraction  │    │  Extraction  │    │    Data      │ │
│   └──────────┘    └──────────────┘    └──────────────┘    └──────┬───────┘ │
│                                                                   │         │
│                                                                   ▼         │
│   ┌──────────┐    ┌──────────────┐    ┌──────────────┐    ┌──────────────┐ │
│   │  Final   │◄───│   Lawyer     │◄───│  LLM Draft   │◄───│  Template +  │ │
│   │ Opinion  │    │   Review     │    │  Generation  │    │  Raw Data    │ │
│   └──────────┘    └──────────────┘    └──────────────┘    └──────────────┘ │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 2.4 Key Stakeholders
| Stakeholder | Role |
|-------------|------|
| Bank Officers | Initiate legal opinion requests, upload documents |
| Panel Advocates | Review documents, generate legal opinions |
| Bank Admin | Manage users, view reports, configure workflows |
| Super Admin | Platform administration, tenant onboarding |

### 2.5 Document Types Handled
- Property Documents (Sale Deed, Title Deed, Encumbrance Certificate)
- Identity Documents (Aadhaar, PAN, Voter ID)
- Financial Documents (Bank Statements, ITR, Balance Sheets)
- Collateral Documents (Hypothecation Agreement, Mortgage Deed)
- Guarantor Documents (Personal Guarantee, Net Worth Statement)

---

## 3. System Overview

### 3.1 High-Level Capabilities

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        LEGAL OPINION SAAS PLATFORM                          │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │  Document   │  │   Opinion   │  │    User     │  │  Reporting  │        │
│  │  Management │  │  Workflow   │  │ Management  │  │  Analytics  │        │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘        │
│                                                                             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │   Tenant    │  │    Audit    │  │Notification │  │ Integration │        │
│  │   Config    │  │    Trail    │  │   Engine    │  │    Hub      │        │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘        │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 3.2 User Journey

```
Bank Officer                    Panel Advocate                    System
     │                               │                              │
     │  1. Create Opinion Request    │                              │
     │──────────────────────────────────────────────────────────────>│
     │                               │                              │
     │  2. Upload Documents          │                              │
     │──────────────────────────────────────────────────────────────>│
     │                               │                              │
     │                               │  3. Assignment Notification  │
     │                               │<─────────────────────────────│
     │                               │                              │
     │                               │  4. Review Documents         │
     │                               │<─────────────────────────────│
     │                               │                              │
     │                               │  5. Generate Opinion         │
     │                               │─────────────────────────────>│
     │                               │                              │
     │  6. Opinion Delivered         │                              │
     │<─────────────────────────────────────────────────────────────│
     │                               │                              │
```

---

## 4. Architecture Principles

| Principle | Description |
|-----------|-------------|
| **Cloud-Native** | Leverage AWS managed services where possible |
| **Containerized** | All application components run in containers |
| **Multi-Tenant** | Shared infrastructure with logical data isolation |
| **API-First** | All functionality exposed via RESTful APIs |
| **Security-First** | Encryption, RBAC, audit logging by default |
| **Infrastructure as Code** | All infrastructure defined in Terraform |
| **12-Factor App** | Follow 12-factor methodology for cloud apps |
| **Event-Driven** | Async processing for document handling |

---

## 5. High-Level Architecture

### 5.1 Architecture Diagram

```
                                    ┌─────────────────┐
                                    │   CloudFront    │
                                    │      CDN        │
                                    └────────┬────────┘
                                             │
                                    ┌────────▼────────┐
                                    │   Route 53      │
                                    │     DNS         │
                                    └────────┬────────┘
                                             │
                         ┌───────────────────┼───────────────────┐
                         │                   │                   │
                ┌────────▼────────┐ ┌────────▼────────┐ ┌────────▼────────┐
                │  Web App (S3)   │ │    AWS WAF      │ │  Certificate    │
                │  React SPA      │ │   Firewall      │ │   Manager       │
                └────────┬────────┘ └────────┬────────┘ └─────────────────┘
                         │                   │
                         └─────────┬─────────┘
                                   │
                          ┌────────▼────────┐
                          │ Application     │
                          │ Load Balancer   │
                          └────────┬────────┘
                                   │
┌──────────────────────────────────┼──────────────────────────────────────────┐
│                         AWS VPC (Private)                                   │
│                                  │                                          │
│    ┌─────────────────────────────┼─────────────────────────────────────┐   │
│    │                    EKS Cluster                                     │   │
│    │   ┌─────────────┐  ┌─────────────┐  ┌─────────────┐               │   │
│    │   │   API       │  │  Document   │  │  Opinion    │               │   │
│    │   │  Gateway    │  │  Service    │  │  Service    │               │   │
│    │   │  Service    │  │             │  │             │               │   │
│    │   └─────────────┘  └─────────────┘  └─────────────┘               │   │
│    │                                                                    │   │
│    │   ┌─────────────┐  ┌─────────────┐  ┌─────────────┐               │   │
│    │   │   User      │  │ Notification│  │  Audit      │               │   │
│    │   │  Service    │  │  Service    │  │  Service    │               │   │
│    │   └─────────────┘  └─────────────┘  └─────────────┘               │   │
│    │                                                                    │   │
│    └────────────────────────────────────────────────────────────────────┘   │
│                                  │                                          │
│    ┌─────────────────────────────┼─────────────────────────────────────┐   │
│    │                     Data Layer                                     │   │
│    │   ┌─────────────┐  ┌─────────────┐  ┌─────────────┐               │   │
│    │   │  RDS        │  │ ElastiCache │  │  Amazon     │               │   │
│    │   │ PostgreSQL  │  │   Redis     │  │    S3       │               │   │
│    │   │ (Multi-AZ)  │  │  (Cluster)  │  │  (Docs)     │               │   │
│    │   └─────────────┘  └─────────────┘  └─────────────┘               │   │
│    │                                                                    │   │
│    └────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

                    ┌─────────────────────────────────────────┐
                    │           Supporting Services           │
                    │  ┌──────────┐ ┌──────────┐ ┌──────────┐│
                    │  │ Cognito  │ │   SES    │ │CloudWatch││
                    │  │  Auth    │ │  Email   │ │ Logging  ││
                    │  └──────────┘ └──────────┘ └──────────┘│
                    │  ┌──────────┐ ┌──────────┐ ┌──────────┐│
                    │  │  Secrets │ │  Lambda  │ │   SQS    ││
                    │  │ Manager  │ │Functions │ │  Queues  ││
                    │  └──────────┘ └──────────┘ └──────────┘│
                    └─────────────────────────────────────────┘
```

### 5.2 Technology Stack

| Layer | Technology | Justification |
|-------|------------|---------------|
| **Frontend** | React.js + TypeScript | Modern SPA framework, rich ecosystem |
| **UI Framework** | Ant Design / Material UI | Enterprise-grade components |
| **API Gateway** | Kong / AWS API Gateway | Rate limiting, auth, routing |
| **Backend** | Node.js (NestJS) or Python (FastAPI) | Async support, fast development |
| **Database** | PostgreSQL (RDS) | ACID compliance, JSON support |
| **Cache** | Redis (ElastiCache) | Session management, caching |
| **Storage** | Amazon S3 | Document storage, encryption |
| **Container Orchestration** | Amazon EKS | Managed Kubernetes |
| **Auth** | Amazon Cognito | Multi-tenant auth, MFA support |
| **Email** | Amazon SES | Transactional emails |
| **Queue** | Amazon SQS | Async document processing |
| **Monitoring** | CloudWatch + Prometheus + Grafana | Observability |
| **IaC** | Terraform | Infrastructure automation |
| **CI/CD** | GitHub Actions | Build, test, deploy automation |
| **OCR** | Amazon Textract | Document text extraction |
| **LLM** | Amazon Bedrock (Claude) / OpenAI | AI-powered data extraction & opinion generation |
| **Vector Store** | Amazon OpenSearch / Pinecone | Template embeddings, semantic search (optional) |

### 5.3 AI/LLM Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         AI/LLM PIPELINE ARCHITECTURE                         │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   DOCUMENT PROCESSING PIPELINE                                               │
│   ────────────────────────────                                               │
│                                                                              │
│   ┌─────────────┐   ┌─────────────┐   ┌─────────────┐   ┌─────────────┐    │
│   │   S3       │   │   Amazon    │   │   LLM       │   │  Structured │    │
│   │ (Document) │──►│  Textract   │──►│  Extraction │──►│    JSON     │    │
│   │            │   │   (OCR)     │   │  (Bedrock)  │   │    Data     │    │
│   └─────────────┘   └─────────────┘   └─────────────┘   └──────┬──────┘    │
│                                                                 │           │
│                                                                 │           │
│   OPINION GENERATION PIPELINE                                   │           │
│   ───────────────────────────                                   │           │
│                                                                 ▼           │
│   ┌─────────────┐   ┌─────────────┐   ┌─────────────┐   ┌─────────────┐    │
│   │  Template   │   │   Prompt    │   │    LLM      │   │   Draft     │    │
│   │  Library   │──►│ Engineering │──►│  Generation │──►│  Opinion    │    │
│   │            │   │             │   │  (Bedrock)  │   │             │    │
│   └─────────────┘   └─────────────┘   └─────────────┘   └──────┬──────┘    │
│                                                                 │           │
│                                                                 ▼           │
│                                                         ┌─────────────┐    │
│                                                         │   Lawyer    │    │
│                                                         │   Review &  │    │
│                                                         │    Edit     │    │
│                                                         └─────────────┘    │
│                                                                              │
│   KEY COMPONENTS:                                                           │
│   • OCR Engine: Amazon Textract for text extraction from scanned docs      │
│   • Data Extraction LLM: Extract structured fields (names, dates, amounts) │
│   • Template Engine: Configurable opinion templates per loan/document type │
│   • Generation LLM: Combine template + extracted data to generate opinion  │
│   • Human-in-the-Loop: Lawyer reviews, edits, and approves AI-generated draft │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 6. Component Design

### 6.1 Frontend Application

**Architecture:** Single Page Application (SPA)

```
┌─────────────────────────────────────────────────────────────────┐
│                     React Frontend Application                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                    Presentation Layer                     │   │
│  │  ┌────────────┐ ┌────────────┐ ┌────────────┐           │   │
│  │  │   Pages    │ │ Components │ │   Layouts  │           │   │
│  │  └────────────┘ └────────────┘ └────────────┘           │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                    State Management                       │   │
│  │  ┌────────────┐ ┌────────────┐ ┌────────────┐           │   │
│  │  │ Redux/Zustand│ │  React Query│ │ Context API │         │   │
│  │  └────────────┘ └────────────┘ └────────────┘           │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                    Service Layer                          │   │
│  │  ┌────────────┐ ┌────────────┐ ┌────────────┐           │   │
│  │  │ API Client │ │ Auth Service│ │File Upload │           │   │
│  │  │  (Axios)   │ │  (Cognito) │ │  Service   │           │   │
│  │  └────────────┘ └────────────┘ └────────────┘           │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

**Key Modules:**
- **Authentication Module**: Login, MFA, Password reset, Session management
- **Dashboard Module**: Overview, statistics, pending tasks
- **Document Module**: Upload, preview, categorization
- **Opinion Module**: Create, edit, templates, PDF generation
- **User Management**: User CRUD, role assignment (admin)
- **Reports Module**: Analytics, export, audit logs

### 6.2 Backend Microservices

#### 6.2.1 API Gateway Service
- Request routing and load balancing
- Rate limiting per tenant
- JWT validation
- Request/Response transformation
- API versioning

#### 6.2.2 User Service
```
Responsibilities:
├── User registration and profile management
├── Role-based access control (RBAC)
├── Tenant-user association
├── Lawyer profile and credentials
└── Activity logging
```

#### 6.2.3 Document Service
```
Responsibilities:
├── Document upload (multipart, resumable)
├── Document storage (S3 with encryption)
├── Document categorization
├── Virus scanning integration
├── Document versioning
├── Thumbnail generation
├── OCR processing trigger (via Textract)
└── AI extraction pipeline orchestration
```

#### 6.2.4 AI/LLM Service (Document Intelligence)
```
Responsibilities:
├── OCR coordination (Amazon Textract)
├── Raw text to structured data extraction (LLM)
├── Document field extraction:
│   ├── Property details (address, area, boundaries)
│   ├── Party details (names, addresses, identification)
│   ├── Financial details (amounts, dates, terms)
│   ├── Legal details (registration numbers, dates)
│   └── Encumbrance details (mortgages, liens)
├── Template management for extraction prompts
├── Extraction result validation
├── Confidence scoring for extracted fields
└── Manual review flagging for low-confidence extractions
```

#### 6.2.5 Opinion Generation Service (LLM-Powered)
```
Responsibilities:
├── Opinion template management
├── Prompt engineering for opinion generation
├── LLM integration (Amazon Bedrock / OpenAI)
├── Draft opinion generation workflow:
│   ├── Fetch extracted document data
│   ├── Select appropriate template
│   ├── Construct generation prompt
│   ├── Call LLM for draft generation
│   └── Store draft with source tracking
├── Regeneration with modified parameters
├── Version tracking for AI-generated content
└── Audit logging of AI interactions
```

#### 6.2.6 Opinion Service
```
Responsibilities:
├── Opinion request creation
├── Workflow management (Draft → Review → Approved → Published)
├── Template management
├── PDF generation
├── Digital signature integration
└── Opinion versioning
```

#### 6.2.7 Notification Service
```
Responsibilities:
├── Email notifications (SES)
├── In-app notifications
├── SMS notifications (optional)
├── Notification preferences
└── Notification templates
```

#### 6.2.8 Audit Service
```
Responsibilities:
├── Audit log ingestion
├── Audit log storage
├── Compliance reporting
├── Data retention management
└── Audit log search
```

### 6.3 Service Communication

```
┌─────────────────────────────────────────────────────────────────┐
│                    Inter-Service Communication                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Synchronous (REST/gRPC):                                       │
│  ┌──────────┐    HTTP/gRPC    ┌──────────┐                     │
│  │ Service A│ ◄──────────────► │ Service B│                     │
│  └──────────┘                  └──────────┘                     │
│  - User authentication                                          │
│  - Real-time data queries                                       │
│  - Health checks                                                │
│                                                                  │
│  Asynchronous (SQS/SNS):                                        │
│  ┌──────────┐    SQS/SNS      ┌──────────┐                     │
│  │ Service A│ ──────────────► │ Service B│                     │
│  └──────────┘                  └──────────┘                     │
│  - Document processing                                          │
│  - Notification dispatch                                        │
│  - Audit log events                                             │
│  - Report generation                                            │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 7. Multi-Tenancy Strategy

### 7.1 Tenant Isolation Model

**Chosen Approach: Shared Database, Shared Schema with Tenant ID**

```
┌─────────────────────────────────────────────────────────────────┐
│                    Multi-Tenancy Model                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │                    Application Layer                     │    │
│  │  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐    │    │
│  │  │ Bank A  │  │ Bank B  │  │ Bank C  │  │ Bank D  │    │    │
│  │  │ Context │  │ Context │  │ Context │  │ Context │    │    │
│  │  └────┬────┘  └────┬────┘  └────┬────┘  └────┬────┘    │    │
│  │       │            │            │            │          │    │
│  │       └────────────┴─────┬──────┴────────────┘          │    │
│  │                          │                              │    │
│  │                   ┌──────▼──────┐                       │    │
│  │                   │   Tenant    │                       │    │
│  │                   │  Resolver   │                       │    │
│  │                   │ Middleware  │                       │    │
│  │                   └──────┬──────┘                       │    │
│  │                          │                              │    │
│  └──────────────────────────┼──────────────────────────────┘    │
│                             │                                    │
│  ┌──────────────────────────▼──────────────────────────────┐    │
│  │                    Database Layer                        │    │
│  │                                                          │    │
│  │  ┌────────────────────────────────────────────────────┐ │    │
│  │  │              PostgreSQL Database                    │ │    │
│  │  │  ┌──────────────────────────────────────────────┐  │ │    │
│  │  │  │ users     | tenant_id | id | name | email    │  │ │    │
│  │  │  │ documents | tenant_id | id | doc_type | path │  │ │    │
│  │  │  │ opinions  | tenant_id | id | status | content│  │ │    │
│  │  │  │ ...       | tenant_id | ...                  │  │ │    │
│  │  │  └──────────────────────────────────────────────┘  │ │    │
│  │  │                                                     │ │    │
│  │  │  Row Level Security (RLS) enforced per tenant_id   │ │    │
│  │  └─────────────────────────────────────────────────────┘ │    │
│  │                                                          │    │
│  └──────────────────────────────────────────────────────────┘    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 7.2 Tenant Resolution Strategy

```typescript
// Tenant Resolution Flow
1. User authenticates → JWT contains tenant_id claim
2. Request arrives at API Gateway
3. Tenant Resolver Middleware extracts tenant_id from:
   - JWT token (primary)
   - X-Tenant-ID header (internal services)
   - Subdomain (bank-a.legalopinion.com) - optional
4. Tenant context set for entire request lifecycle
5. All database queries automatically filtered by tenant_id
6. All S3 paths prefixed with tenant_id
```

### 7.3 Tenant Data Model

```sql
-- Tenant Master Table
CREATE TABLE tenants (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(255) NOT NULL,
    code VARCHAR(50) UNIQUE NOT NULL,  -- e.g., 'HDFC', 'ICICI'
    subscription_tier VARCHAR(50) NOT NULL,
    settings JSONB DEFAULT '{}',
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Row Level Security Policy Example
ALTER TABLE users ENABLE ROW LEVEL SECURITY;

CREATE POLICY tenant_isolation_policy ON users
    USING (tenant_id = current_setting('app.current_tenant')::uuid);
```

### 7.4 S3 Bucket Structure (Per Tenant)

```
legal-opinion-documents-{env}/
├── {tenant_id}/
│   ├── loan-applications/
│   │   └── {application_id}/
│   │       ├── property-docs/
│   │       ├── identity-docs/
│   │       ├── financial-docs/
│   │       └── collateral-docs/
│   ├── generated-opinions/
│   │   └── {opinion_id}/
│   │       └── opinion_v1.pdf
│   └── templates/
│       └── {template_id}/
```

---

## 8. Data Architecture

### 8.1 Entity Relationship Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                          DATA MODEL OVERVIEW                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌──────────────┐         ┌──────────────┐         ┌──────────────┐        │
│  │   TENANTS    │         │    USERS     │         │    ROLES     │        │
│  ├──────────────┤         ├──────────────┤         ├──────────────┤        │
│  │ id           │◄───┐    │ id           │    ┌───►│ id           │        │
│  │ name         │    │    │ tenant_id    │────┤    │ name         │        │
│  │ code         │    └────│ email        │    │    │ permissions  │        │
│  │ settings     │         │ role_id      │────┘    │              │        │
│  │ tier         │         │ profile      │         │              │        │
│  └──────────────┘         └──────┬───────┘         └──────────────┘        │
│                                  │                                          │
│                                  │                                          │
│  ┌──────────────┐         ┌──────▼───────┐         ┌──────────────┐        │
│  │   BORROWERS  │         │LOAN_REQUESTS │         │   DOCUMENTS  │        │
│  ├──────────────┤         ├──────────────┤         ├──────────────┤        │
│  │ id           │◄────────│ id           │────────►│ id           │        │
│  │ tenant_id    │         │ tenant_id    │         │ tenant_id    │        │
│  │ name         │         │ borrower_id  │         │ request_id   │        │
│  │ contact      │         │ loan_type    │         │ doc_type     │        │
│  │ address      │         │ amount       │         │ s3_path      │        │
│  │ kyc_details  │         │ status       │         │ status       │        │
│  └──────────────┘         │ assigned_to  │         │ metadata     │        │
│                           └──────┬───────┘         └──────────────┘        │
│                                  │                                          │
│                                  │                                          │
│  ┌──────────────┐         ┌──────▼───────┐         ┌──────────────┐        │
│  │  TEMPLATES   │         │   OPINIONS   │         │ AUDIT_LOGS   │        │
│  ├──────────────┤         ├──────────────┤         ├──────────────┤        │
│  │ id           │────────►│ id           │         │ id           │        │
│  │ tenant_id    │         │ tenant_id    │         │ tenant_id    │        │
│  │ name         │         │ request_id   │         │ entity_type  │        │
│  │ content      │         │ template_id  │         │ entity_id    │        │
│  │ doc_type     │         │ content      │         │ action       │        │
│  │              │         │ status       │         │ user_id      │        │
│  │              │         │ pdf_path     │         │ changes      │        │
│  └──────────────┘         │ version      │         │ timestamp    │        │
│                           └──────────────┘         └──────────────┘        │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 8.2 Key Tables Schema

```sql
-- Loan Requests Table
CREATE TABLE loan_requests (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id),
    borrower_id UUID NOT NULL REFERENCES borrowers(id),
    reference_number VARCHAR(50) UNIQUE NOT NULL,
    loan_type VARCHAR(50) NOT NULL,  -- HOME, LAP, COMMERCIAL
    loan_amount DECIMAL(15,2),
    property_location TEXT,
    branch_code VARCHAR(20),
    created_by UUID NOT NULL REFERENCES users(id),
    assigned_lawyer_id UUID REFERENCES users(id),
    status VARCHAR(30) DEFAULT 'PENDING',
    -- PENDING, DOCUMENTS_UPLOADED, IN_REVIEW, OPINION_DRAFT, 
    -- OPINION_APPROVED, COMPLETED, REJECTED
    priority VARCHAR(20) DEFAULT 'NORMAL',
    due_date DATE,
    metadata JSONB DEFAULT '{}',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Documents Table
CREATE TABLE documents (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id),
    loan_request_id UUID NOT NULL REFERENCES loan_requests(id),
    document_type VARCHAR(50) NOT NULL,
    -- SALE_DEED, TITLE_DEED, EC, AADHAAR, PAN, etc.
    original_filename VARCHAR(255) NOT NULL,
    s3_key VARCHAR(500) NOT NULL,
    s3_bucket VARCHAR(100) NOT NULL,
    file_size BIGINT,
    mime_type VARCHAR(100),
    checksum VARCHAR(64),
    upload_status VARCHAR(20) DEFAULT 'UPLOADED',
    scan_status VARCHAR(20) DEFAULT 'PENDING',
    -- PENDING, SCANNING, CLEAN, INFECTED
    ocr_status VARCHAR(20),
    ocr_text TEXT,
    metadata JSONB DEFAULT '{}',
    uploaded_by UUID NOT NULL REFERENCES users(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Legal Opinions Table
CREATE TABLE legal_opinions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id),
    loan_request_id UUID NOT NULL REFERENCES loan_requests(id),
    template_id UUID REFERENCES opinion_templates(id),
    opinion_number VARCHAR(50) UNIQUE NOT NULL,
    version INT DEFAULT 1,
    status VARCHAR(30) DEFAULT 'DRAFT',
    -- DRAFT, SUBMITTED, UNDER_REVIEW, REVISION_REQUESTED, 
    -- APPROVED, REJECTED, PUBLISHED
    content JSONB NOT NULL,  -- Structured opinion content
    summary TEXT,
    recommendation VARCHAR(50),  -- POSITIVE, NEGATIVE, CONDITIONAL
    conditions TEXT[],  -- Array of conditions if CONDITIONAL
    pdf_s3_key VARCHAR(500),
    created_by UUID NOT NULL REFERENCES users(id),
    reviewed_by UUID REFERENCES users(id),
    approved_by UUID REFERENCES users(id),
    submitted_at TIMESTAMP,
    approved_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Audit Logs Table (for compliance)
CREATE TABLE audit_logs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL,
    entity_type VARCHAR(50) NOT NULL,
    entity_id UUID NOT NULL,
    action VARCHAR(50) NOT NULL,
    -- CREATE, UPDATE, DELETE, VIEW, DOWNLOAD, STATUS_CHANGE
    old_values JSONB,
    new_values JSONB,
    user_id UUID NOT NULL,
    user_email VARCHAR(255),
    ip_address INET,
    user_agent TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- AI/LLM Extraction Results Table
CREATE TABLE document_extractions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL,
    document_id UUID NOT NULL REFERENCES documents(id),
    extraction_type VARCHAR(50) NOT NULL,
    -- PROPERTY_DETAILS, PARTY_DETAILS, FINANCIAL_DETAILS, etc.
    raw_text TEXT,
    extracted_data JSONB NOT NULL,
    -- Structured extracted fields
    confidence_score DECIMAL(3,2),
    -- 0.00 to 1.00
    llm_model VARCHAR(100),
    llm_prompt_version VARCHAR(50),
    requires_review BOOLEAN DEFAULT false,
    reviewed_by UUID REFERENCES users(id),
    reviewed_at TIMESTAMP,
    corrections JSONB,
    -- Manual corrections made
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- AI-Generated Opinion Drafts Table
CREATE TABLE opinion_drafts (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL,
    loan_request_id UUID NOT NULL REFERENCES loan_requests(id),
    template_id UUID REFERENCES opinion_templates(id),
    generation_prompt TEXT,
    input_data JSONB NOT NULL,
    -- Extracted data used
    generated_content JSONB NOT NULL,
    llm_model VARCHAR(100),
    llm_response_metadata JSONB,
    generation_status VARCHAR(30) DEFAULT 'GENERATED',
    -- GENERATED, ACCEPTED, REJECTED, MODIFIED
    accepted_by UUID REFERENCES users(id),
    accepted_at TIMESTAMP,
    modifications_made TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Create indexes for performance
CREATE INDEX idx_loan_requests_tenant ON loan_requests(tenant_id);
CREATE INDEX idx_loan_requests_status ON loan_requests(tenant_id, status);
CREATE INDEX idx_documents_request ON documents(loan_request_id);
CREATE INDEX idx_opinions_request ON legal_opinions(loan_request_id);
CREATE INDEX idx_audit_logs_entity ON audit_logs(tenant_id, entity_type, entity_id);
CREATE INDEX idx_audit_logs_time ON audit_logs(tenant_id, created_at DESC);
CREATE INDEX idx_extractions_document ON document_extractions(document_id);
CREATE INDEX idx_opinion_drafts_request ON opinion_drafts(loan_request_id);
```

---

## 9. Security Architecture

### 9.1 Security Layers

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         SECURITY ARCHITECTURE                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌────────────────────────────────────────────────────────────────────────┐ │
│  │ LAYER 1: PERIMETER SECURITY                                            │ │
│  │  • AWS WAF - Web Application Firewall (SQL injection, XSS protection)  │ │
│  │  • AWS Shield - DDoS protection                                        │ │
│  │  • CloudFront - Edge security, Geo-blocking                           │ │
│  │  • Rate Limiting at API Gateway                                        │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
│  ┌────────────────────────────────────────────────────────────────────────┐ │
│  │ LAYER 2: NETWORK SECURITY                                              │ │
│  │  • VPC with public/private subnets                                     │ │
│  │  • Security Groups (stateful firewall)                                 │ │
│  │  • NACLs (stateless firewall)                                          │ │
│  │  • VPC Flow Logs for monitoring                                        │ │
│  │  • Private subnets for databases and internal services                 │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
│  ┌────────────────────────────────────────────────────────────────────────┐ │
│  │ LAYER 3: IDENTITY & ACCESS                                             │ │
│  │  • Amazon Cognito for user authentication                              │ │
│  │  • JWT tokens with short expiry                                        │ │
│  │  • MFA enforcement for all users                                       │ │
│  │  • RBAC with fine-grained permissions                                  │ │
│  │  • Session management with Redis                                       │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
│  ┌────────────────────────────────────────────────────────────────────────┐ │
│  │ LAYER 4: DATA SECURITY                                                 │ │
│  │  • Encryption at rest (RDS, S3 - AES-256)                              │ │
│  │  • Encryption in transit (TLS 1.3)                                     │ │
│  │  • S3 bucket policies and ACLs                                         │ │
│  │  • Database Row-Level Security                                         │ │
│  │  • Secrets Manager for credentials                                     │ │
│  │  • Data masking for PII in logs                                        │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
│  ┌────────────────────────────────────────────────────────────────────────┐ │
│  │ LAYER 5: APPLICATION SECURITY                                          │ │
│  │  • Input validation and sanitization                                   │ │
│  │  • OWASP Top 10 protection                                             │ │
│  │  • Content Security Policy (CSP)                                       │ │
│  │  • Anti-virus scanning for uploads                                     │ │
│  │  • Secure file upload handling                                         │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
│  ┌────────────────────────────────────────────────────────────────────────┐ │
│  │ LAYER 6: MONITORING & COMPLIANCE                                       │ │
│  │  • CloudTrail for API audit logs                                       │ │
│  │  • GuardDuty for threat detection                                      │ │
│  │  • Security Hub for compliance dashboard                               │ │
│  │  • Application audit logs                                              │ │
│  │  • Alerting on suspicious activities                                   │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 9.2 Authentication Flow

```
┌──────────┐     ┌──────────┐     ┌──────────┐     ┌──────────┐
│  Client  │     │ Cognito  │     │   API    │     │ Backend  │
│   App    │     │          │     │ Gateway  │     │ Services │
└────┬─────┘     └────┬─────┘     └────┬─────┘     └────┬─────┘
     │                │                │                │
     │  1. Login      │                │                │
     │  (email/pass)  │                │                │
     │───────────────>│                │                │
     │                │                │                │
     │  2. MFA        │                │                │
     │  Challenge     │                │                │
     │<───────────────│                │                │
     │                │                │                │
     │  3. MFA Code   │                │                │
     │───────────────>│                │                │
     │                │                │                │
     │  4. JWT Tokens │                │                │
     │  (access,      │                │                │
     │   refresh,     │                │                │
     │   id token)    │                │                │
     │<───────────────│                │                │
     │                │                │                │
     │  5. API Request with Bearer Token                │
     │────────────────────────────────>│                │
     │                │                │                │
     │                │  6. Validate   │                │
     │                │     JWT        │                │
     │                │<───────────────│                │
     │                │                │                │
     │                │  7. Token OK   │                │
     │                │───────────────>│                │
     │                │                │                │
     │                │                │  8. Forward    │
     │                │                │  Request with  │
     │                │                │  User Context  │
     │                │                │───────────────>│
     │                │                │                │
     │  9. Response   │                │                │
     │<────────────────────────────────────────────────│
     │                │                │                │
```

### 9.3 Role-Based Access Control (RBAC)

| Role | Permissions |
|------|-------------|
| **Super Admin** | Platform management, tenant onboarding, global settings |
| **Bank Admin** | Tenant settings, user management, reports, audit logs |
| **Bank Officer** | Create requests, upload documents, view opinions |
| **Panel Advocate** | Review documents, create/edit opinions, submit for approval |
| **Senior Advocate** | Approve/reject opinions, manage templates |
| **Viewer** | Read-only access to opinions and reports |

---

## 10. Infrastructure Design

### 10.1 AWS Infrastructure Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           AWS INFRASTRUCTURE                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  Region: ap-south-1 (Mumbai)                                                │
│                                                                              │
│  ┌────────────────────────────────────────────────────────────────────────┐ │
│  │                              VPC (10.0.0.0/16)                          │ │
│  │                                                                         │ │
│  │  ┌─────────────────────────────────────────────────────────────────┐   │ │
│  │  │ Availability Zone A (ap-south-1a)                                │   │ │
│  │  │  ┌──────────────────┐  ┌──────────────────┐                     │   │ │
│  │  │  │ Public Subnet A  │  │ Private Subnet A │                     │   │ │
│  │  │  │ 10.0.1.0/24      │  │ 10.0.10.0/24     │                     │   │ │
│  │  │  │  ┌────────────┐  │  │  ┌────────────┐  │                     │   │ │
│  │  │  │  │ NAT GW     │  │  │  │ EKS Node   │  │                     │   │ │
│  │  │  │  │            │  │  │  │ Group A    │  │                     │   │ │
│  │  │  │  └────────────┘  │  │  └────────────┘  │                     │   │ │
│  │  │  │  ┌────────────┐  │  │  ┌────────────┐  │                     │   │ │
│  │  │  │  │ ALB        │  │  │  │ RDS        │  │                     │   │ │
│  │  │  │  │ (Public)   │  │  │  │ Primary    │  │                     │   │ │
│  │  │  │  └────────────┘  │  │  └────────────┘  │                     │   │ │
│  │  │  └──────────────────┘  └──────────────────┘                     │   │ │
│  │  └─────────────────────────────────────────────────────────────────┘   │ │
│  │                                                                         │ │
│  │  ┌─────────────────────────────────────────────────────────────────┐   │ │
│  │  │ Availability Zone B (ap-south-1b)                                │   │ │
│  │  │  ┌──────────────────┐  ┌──────────────────┐                     │   │ │
│  │  │  │ Public Subnet B  │  │ Private Subnet B │                     │   │ │
│  │  │  │ 10.0.2.0/24      │  │ 10.0.20.0/24     │                     │   │ │
│  │  │  │  ┌────────────┐  │  │  ┌────────────┐  │                     │   │ │
│  │  │  │  │ NAT GW     │  │  │  │ EKS Node   │  │                     │   │ │
│  │  │  │  │            │  │  │  │ Group B    │  │                     │   │ │
│  │  │  │  └────────────┘  │  │  └────────────┘  │                     │   │ │
│  │  │  │                  │  │  ┌────────────┐  │                     │   │ │
│  │  │  │                  │  │  │ RDS        │  │                     │   │ │
│  │  │  │                  │  │  │ Standby    │  │                     │   │ │
│  │  │  │                  │  │  └────────────┘  │                     │   │ │
│  │  │  │                  │  │  ┌────────────┐  │                     │   │ │
│  │  │  │                  │  │  │ElastiCache │  │                     │   │ │
│  │  │  │                  │  │  │ Redis      │  │                     │   │ │
│  │  │  │                  │  │  └────────────┘  │                     │   │ │
│  │  │  └──────────────────┘  └──────────────────┘                     │   │ │
│  │  └─────────────────────────────────────────────────────────────────┘   │ │
│  │                                                                         │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
│  ┌────────────────────────────────────────────────────────────────────────┐ │
│  │                         Global Services                                 │ │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐     │ │
│  │  │   S3     │ │ Cognito  │ │   SES    │ │   SQS    │ │  Secrets │     │ │
│  │  │ Buckets  │ │User Pool │ │  Email   │ │  Queues  │ │ Manager  │     │ │
│  │  └──────────┘ └──────────┘ └──────────┘ └──────────┘ └──────────┘     │ │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐     │ │
│  │  │CloudWatch│ │CloudTrail│ │ Route53  │ │CloudFront│ │   WAF    │     │ │
│  │  │  Logs    │ │  Audit   │ │   DNS    │ │   CDN    │ │Firewall  │     │ │
│  │  └──────────┘ └──────────┘ └──────────┘ └──────────┘ └──────────┘     │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 10.2 Kubernetes (EKS) Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        EKS CLUSTER ARCHITECTURE                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌────────────────────────────────────────────────────────────────────────┐ │
│  │                        Namespaces                                       │ │
│  │                                                                         │ │
│  │  ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐          │ │
│  │  │  production     │ │   staging       │ │   monitoring    │          │ │
│  │  │  namespace      │ │   namespace     │ │   namespace     │          │ │
│  │  └─────────────────┘ └─────────────────┘ └─────────────────┘          │ │
│  │                                                                         │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
│  ┌────────────────────────────────────────────────────────────────────────┐ │
│  │                    Production Namespace                                 │ │
│  │                                                                         │ │
│  │  ┌────────────────────────────────────────────────────────────────┐    │ │
│  │  │                      Deployments                                │    │ │
│  │  │  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐            │    │ │
│  │  │  │ api-gateway  │ │user-service  │ │doc-service   │            │    │ │
│  │  │  │ replicas: 3  │ │ replicas: 2  │ │ replicas: 2  │            │    │ │
│  │  │  └──────────────┘ └──────────────┘ └──────────────┘            │    │ │
│  │  │  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐            │    │ │
│  │  │  │opinion-svc   │ │notify-service│ │ audit-service│            │    │ │
│  │  │  │ replicas: 2  │ │ replicas: 2  │ │ replicas: 2  │            │    │ │
│  │  │  └──────────────┘ └──────────────┘ └──────────────┘            │    │ │
│  │  └────────────────────────────────────────────────────────────────┘    │ │
│  │                                                                         │ │
│  │  ┌────────────────────────────────────────────────────────────────┐    │ │
│  │  │                        Services                                 │    │ │
│  │  │  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐            │    │ │
│  │  │  │ ClusterIP    │ │ ClusterIP    │ │  LoadBalancer│            │    │ │
│  │  │  │ (internal)   │ │ (internal)   │ │   (ingress)  │            │    │ │
│  │  │  └──────────────┘ └──────────────┘ └──────────────┘            │    │ │
│  │  └────────────────────────────────────────────────────────────────┘    │ │
│  │                                                                         │ │
│  │  ┌────────────────────────────────────────────────────────────────┐    │ │
│  │  │                     Config & Secrets                            │    │ │
│  │  │  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐            │    │ │
│  │  │  │ ConfigMaps   │ │ Secrets      │ │ExternalSecrets│           │    │ │
│  │  │  │              │ │(from AWS SM) │ │  Operator    │            │    │ │
│  │  │  └──────────────┘ └──────────────┘ └──────────────┘            │    │ │
│  │  └────────────────────────────────────────────────────────────────┘    │ │
│  │                                                                         │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
│  ┌────────────────────────────────────────────────────────────────────────┐ │
│  │                    Monitoring Namespace                                 │ │
│  │  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐                   │ │
│  │  │ Prometheus   │ │   Grafana    │ │ Fluent Bit   │                   │ │
│  │  │              │ │              │ │ (log forwarder)                  │ │
│  │  └──────────────┘ └──────────────┘ └──────────────┘                   │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 10.3 Terraform Module Structure

```
terraform/
├── environments/
│   ├── dev/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   ├── terraform.tfvars
│   │   └── backend.tf
│   ├── staging/
│   │   └── ...
│   └── prod/
│       └── ...
├── modules/
│   ├── vpc/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   ├── eks/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   ├── rds/
│   │   └── ...
│   ├── s3/
│   │   └── ...
│   ├── cognito/
│   │   └── ...
│   ├── elasticache/
│   │   └── ...
│   ├── alb/
│   │   └── ...
│   └── monitoring/
│       └── ...
└── README.md
```

---

## 11. CI/CD Pipeline

### 11.1 GitHub Actions Workflow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           CI/CD PIPELINE                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌────────────────────────────────────────────────────────────────────────┐ │
│  │                         CONTINUOUS INTEGRATION                          │ │
│  │                                                                         │ │
│  │   ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐              │ │
│  │   │  Code   │   │  Lint   │   │  Test   │   │  Build  │              │ │
│  │   │  Push   │──►│  Check  │──►│ (Unit)  │──►│ Docker  │              │ │
│  │   │         │   │         │   │         │   │  Image  │              │ │
│  │   └─────────┘   └─────────┘   └─────────┘   └─────────┘              │ │
│  │                                                     │                  │ │
│  │                                                     ▼                  │ │
│  │   ┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐              │ │
│  │   │Security │   │  SAST   │   │Container│   │  Push   │              │ │
│  │   │  Scan   │◄──│  Scan   │◄──│  Scan   │◄──│   ECR   │              │ │
│  │   │         │   │(SonarQube)  │(Trivy)  │   │         │              │ │
│  │   └─────────┘   └─────────┘   └─────────┘   └─────────┘              │ │
│  │                                                                         │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
│                                      │                                       │
│                                      ▼                                       │
│  ┌────────────────────────────────────────────────────────────────────────┐ │
│  │                        CONTINUOUS DEPLOYMENT                            │ │
│  │                                                                         │ │
│  │  ┌───────────────────────────────────────────────────────────────┐     │ │
│  │  │                    Development (Auto Deploy)                   │     │ │
│  │  │   PR Merge ──► Deploy to Dev EKS ──► Integration Tests        │     │ │
│  │  └───────────────────────────────────────────────────────────────┘     │ │
│  │                                      │                                  │ │
│  │                                      ▼                                  │ │
│  │  ┌───────────────────────────────────────────────────────────────┐     │ │
│  │  │                    Staging (Manual Approval)                   │     │ │
│  │  │   Tag Release ──► Approval ──► Deploy ──► E2E Tests           │     │ │
│  │  └───────────────────────────────────────────────────────────────┘     │ │
│  │                                      │                                  │ │
│  │                                      ▼                                  │ │
│  │  ┌───────────────────────────────────────────────────────────────┐     │ │
│  │  │                   Production (Canary/Blue-Green)               │     │ │
│  │  │   Approval ──► Canary 10% ──► Monitor ──► Full Rollout        │     │ │
│  │  └───────────────────────────────────────────────────────────────┘     │ │
│  │                                                                         │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 11.2 Pipeline Configuration (GitHub Actions)

```yaml
# .github/workflows/ci-cd.yml (example structure)
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run ESLint
        run: npm run lint
      
  test:
    runs-on: ubuntu-latest
    needs: lint
    steps:
      - uses: actions/checkout@v4
      - name: Run Tests
        run: npm run test:coverage
      
  security-scan:
    runs-on: ubuntu-latest
    needs: lint
    steps:
      - name: SonarQube Scan
        uses: sonarsource/sonarqube-scan-action@master
      
  build:
    runs-on: ubuntu-latest
    needs: [test, security-scan]
    steps:
      - name: Build Docker Image
        run: docker build -t $ECR_REPO:$GITHUB_SHA .
      - name: Trivy Container Scan
        uses: aquasecurity/trivy-action@master
      - name: Push to ECR
        run: docker push $ECR_REPO:$GITHUB_SHA
        
  deploy-dev:
    runs-on: ubuntu-latest
    needs: build
    if: github.ref == 'refs/heads/develop'
    environment: development
    steps:
      - name: Deploy to Dev EKS
        run: kubectl apply -k overlays/dev
        
  deploy-prod:
    runs-on: ubuntu-latest
    needs: build
    if: github.ref == 'refs/heads/main'
    environment: production
    steps:
      - name: Deploy to Prod EKS
        run: kubectl apply -k overlays/prod
```

---

## 12. Non-Functional Requirements

### 12.1 Performance Requirements

| Metric | Target | Measurement |
|--------|--------|-------------|
| API Response Time (P95) | < 500ms | CloudWatch Metrics |
| Page Load Time | < 3s | Lighthouse |
| Document Upload | < 30s for 50MB | Application Logs |
| Concurrent Users | 500 per tenant | Load Testing |
| Database Query Time | < 100ms | RDS Insights |

### 12.2 Availability & Reliability

| Metric | Target |
|--------|--------|
| Uptime SLA | 99.9% |
| RTO (Recovery Time Objective) | < 4 hours |
| RPO (Recovery Point Objective) | < 1 hour |
| Backup Frequency | Daily full, hourly incremental |

### 12.3 Scalability

```
┌─────────────────────────────────────────────────────────────────┐
│                    SCALABILITY STRATEGY                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Horizontal Scaling (Auto-scaling):                             │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │  EKS Pods: HPA (Horizontal Pod Autoscaler)              │    │
│  │  - Scale on CPU > 70%                                   │    │
│  │  - Scale on Memory > 80%                                │    │
│  │  - Min: 2, Max: 10 replicas per service                 │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │  EKS Nodes: Cluster Autoscaler                          │    │
│  │  - Scale based on pending pods                          │    │
│  │  - Min: 3, Max: 20 nodes                                │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
│  Vertical Scaling:                                              │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │  RDS: Scale up instance type as needed                  │    │
│  │  - Start: db.r6g.large                                  │    │
│  │  - Max: db.r6g.4xlarge                                  │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 12.4 Monitoring & Observability

```
┌─────────────────────────────────────────────────────────────────┐
│                  OBSERVABILITY STACK                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  METRICS:                                                        │
│  ┌─────────────┐     ┌─────────────┐     ┌─────────────┐        │
│  │ Prometheus  │────►│  Grafana    │────►│  Alerting   │        │
│  │ (collect)   │     │(visualize)  │     │ (PagerDuty) │        │
│  └─────────────┘     └─────────────┘     └─────────────┘        │
│                                                                  │
│  LOGS:                                                           │
│  ┌─────────────┐     ┌─────────────┐     ┌─────────────┐        │
│  │ Fluent Bit  │────►│ CloudWatch  │────►│  OpenSearch │        │
│  │ (collect)   │     │   Logs      │     │(search/view)│        │
│  └─────────────┘     └─────────────┘     └─────────────┘        │
│                                                                  │
│  TRACES:                                                         │
│  ┌─────────────┐     ┌─────────────┐                            │
│  │   X-Ray     │────►│  Service    │                            │
│  │ (tracing)   │     │    Map      │                            │
│  └─────────────┘     └─────────────┘                            │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 13. Cost Estimation

### 13.1 Monthly Cost Breakdown (Production)

| Service | Configuration | Est. Monthly Cost |
|---------|---------------|-------------------|
| **EKS Cluster** | 1 cluster | $73 |
| **EC2 (EKS Nodes)** | 3x m5.large (on-demand) | $210 |
| **RDS PostgreSQL** | db.r6g.large, Multi-AZ | $350 |
| **ElastiCache Redis** | cache.r6g.large | $180 |
| **S3 Storage** | 500GB + requests | $50 |
| **ALB** | 1 ALB + traffic | $50 |
| **CloudFront** | 500GB transfer | $60 |
| **NAT Gateway** | 2 gateways | $90 |
| **Route 53** | Hosted zone + queries | $10 |
| **Cognito** | 10,000 MAU | $50 |
| **SES** | 50,000 emails | $5 |
| **CloudWatch** | Logs + metrics | $50 |
| **Secrets Manager** | 20 secrets | $10 |
| **WAF** | Rules + requests | $30 |
| **Data Transfer** | 100GB | $10 |
| **Amazon Textract** | 10,000 pages/month | $150 |
| **Amazon Bedrock (LLM)** | ~500K tokens/day (Claude) | $300-500 |
| **TOTAL** | | **~$1,700-1,900/month** |

*Note: LLM costs can vary significantly based on usage. Consider token optimization and caching strategies.*

### 13.2 Cost Optimization Strategies

1. **Reserved Instances**: 40-60% savings on EC2, RDS
2. **Spot Instances**: For non-production EKS nodes
3. **S3 Lifecycle Policies**: Move old documents to Glacier
4. **Right-sizing**: Regular review of instance utilization
5. **Savings Plans**: Compute savings plans for predictable workloads

---

## 14. Risks & Mitigations

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| Data breach | High | Medium | Encryption, WAF, regular audits |
| Multi-tenant data leakage | High | Low | RLS, code reviews, penetration testing |
| Vendor lock-in (AWS) | Medium | Medium | Use Kubernetes for portability |
| Performance degradation | Medium | Medium | Auto-scaling, caching, monitoring |
| Compliance violations | High | Low | Regular audits, audit logging |
| Key person dependency | Medium | Medium | Documentation, cross-training |
| Cost overrun | Medium | Medium | Budget alerts, cost monitoring |

---

## Appendix A: API Endpoints (Sample)

```yaml
# Core API Endpoints

# Authentication
POST   /api/v1/auth/login
POST   /api/v1/auth/logout
POST   /api/v1/auth/refresh
POST   /api/v1/auth/mfa/verify

# Loan Requests
GET    /api/v1/loan-requests
POST   /api/v1/loan-requests
GET    /api/v1/loan-requests/{id}
PUT    /api/v1/loan-requests/{id}
PATCH  /api/v1/loan-requests/{id}/status
GET    /api/v1/loan-requests/{id}/documents
GET    /api/v1/loan-requests/{id}/opinions

# Documents
POST   /api/v1/documents/upload
GET    /api/v1/documents/{id}
GET    /api/v1/documents/{id}/download
DELETE /api/v1/documents/{id}

# Opinions
GET    /api/v1/opinions
POST   /api/v1/opinions
GET    /api/v1/opinions/{id}
PUT    /api/v1/opinions/{id}
PATCH  /api/v1/opinions/{id}/status
GET    /api/v1/opinions/{id}/pdf

# Users (Admin)
GET    /api/v1/users
POST   /api/v1/users
GET    /api/v1/users/{id}
PUT    /api/v1/users/{id}
DELETE /api/v1/users/{id}

# Reports
GET    /api/v1/reports/dashboard
GET    /api/v1/reports/opinions-summary
GET    /api/v1/reports/audit-logs
```

---

## Appendix B: Folder Structure

```
legal-opinion-saas/
├── frontend/                    # React Frontend
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── services/
│   │   ├── store/
│   │   ├── hooks/
│   │   ├── utils/
│   │   └── types/
│   ├── Dockerfile
│   └── package.json
│
├── backend/                     # Backend Services
│   ├── api-gateway/
│   │   ├── src/
│   │   ├── Dockerfile
│   │   └── package.json
│   ├── user-service/
│   ├── document-service/
│   ├── opinion-service/
│   ├── notification-service/
│   └── audit-service/
│
├── infrastructure/              # Terraform IaC
│   ├── terraform/
│   │   ├── environments/
│   │   └── modules/
│   └── k8s/
│       ├── base/
│       └── overlays/
│
├── .github/                     # GitHub Actions
│   └── workflows/
│       ├── ci.yml
│       ├── cd-dev.yml
│       └── cd-prod.yml
│
├── docs/                        # Documentation
│   ├── HLD.md
│   ├── API.md
│   └── RUNBOOK.md
│
└── scripts/                     # Utility scripts
    ├── setup-local.sh
    └── db-migrate.sh
```

---

**Document Control**

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | Jan 2026 | Solution Architect | Initial draft |

---

*This is a living document and will be updated as requirements evolve.*
