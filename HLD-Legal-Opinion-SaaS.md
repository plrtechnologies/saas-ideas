# High-Level Design Document
## Legal Opinion SaaS Platform for Law Firms

**Version:** 2.0  
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
8. [Authentication with Keycloak](#8-authentication-with-keycloak)
9. [Data Architecture](#9-data-architecture)
10. [AI/LLM Integration](#10-aillm-integration)
11. [Infrastructure Design](#11-infrastructure-design)
12. [UI Screens](#12-ui-screens)
13. [CI/CD Pipeline](#13-cicd-pipeline)
14. [Non-Functional Requirements](#14-non-functional-requirements)
15. [Cost Estimation](#15-cost-estimation)
16. [Risks & Mitigations](#16-risks--mitigations)

---

## 1. Executive Summary

This document outlines the High-Level Design for a **Legal Opinion SaaS Platform** designed for law firms that serve as panel advocates for banks in India. The platform enables law firms to manage loan document scrutiny workflows and generate legally valid opinions on the authenticity and compliance of surety documents.

### Key Objectives
- Provide a secure, multi-tenant SaaS platform for multiple **law firms**
- Enable document upload, review, and legal opinion generation workflow
- **AI-powered document extraction and opinion generation using LLM APIs (OpenAI/Anthropic/Google)**
- Maintain audit trails and historical records for compliance
- Ensure data isolation and security across **law firm tenants**
- **Portable architecture**: Run on VM during development, scale on Kubernetes in production
- **Tenant-configurable UI** with custom branding (logos, colors) per **law firm**

---

## 2. Business Context

### 2.1 Problem Statement
Law firms that serve as panel advocates for banks need to verify loan application documents and provide legal opinions on the validity of surety/collateral documents. Currently, this process is:
- Manual and paper-heavy
- Lacks standardization across different bank clients
- Difficult to track and audit across multiple banks
- Time-consuming with poor visibility for both law firms and their bank clients

### 2.2 Solution Overview
A SaaS platform for **law firms** that:
- Digitizes the document submission and review process
- **AI-powered document content extraction** using OCR and LLM models
- **LLM-assisted opinion generation** using extracted data and templates
- Standardizes opinion generation with configurable templates
- Provides real-time tracking and dashboards
- Maintains complete audit trails
- Enables **multi-law firm (tenant)** operations with data isolation
- Allows law firms to manage **multiple bank clients** within their tenant
- **Supports white-labeling** with law firm-specific branding

### 2.3 AI/LLM Integration Overview
The platform leverages **LLM APIs** (configurable - OpenAI GPT-4, Anthropic Claude, Google Gemini) for:
1. **Document Intelligence**: Extract structured data from uploaded documents (property details, parties, dates, amounts)
2. **Opinion Generation**: Generate draft legal opinions by combining extracted data with predefined templates
3. **Content Validation**: Identify discrepancies and flag potential issues in documents

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         AI/LLM PROCESSING FLOW                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   ┌──────────┐    ┌──────────────┐    ┌──────────────┐    ┌──────────────┐ │
│   │ Document │───►│  OCR/Text    │───►│     LLM      │───►│  Structured  │ │
│   │  Upload  │    │  Extraction  │    │  Extraction  │    │    Data      │ │
│   └──────────┘    └──────────────┘    └──────────────┘    └──────┬───────┘ │
│                                                                   │         │
│                                                                   ▼         │
│   ┌──────────┐    ┌──────────────┐    ┌──────────────┐    ┌──────────────┐ │
│   │  Final   │◄───│   Lawyer     │◄───│     LLM      │◄───│  Template +  │ │
│   │ Opinion  │    │   Review     │    │  Generation  │    │  Raw Data    │ │
│   └──────────┘    └──────────────┘    └──────────────┘    └──────────────┘ │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 2.4 Key Stakeholders
| Stakeholder | Role |
|-------------|------|
| Law Firm Admin | Manage users, bank clients, view reports, configure workflows |
| Senior Advocate | Review and approve opinions, assign cases to advocates |
| Panel Advocate | Review documents, generate legal opinions |
| Paralegal/Clerk | Upload documents, assist with data entry |
| Super Admin | Platform administration, law firm tenant onboarding |

### 2.5 Tenant vs Client Model
```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        TENANT (Law Firm) vs CLIENT (Bank)                   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   TENANT = Law Firm                     CLIENTS = Banks                     │
│   ┌─────────────────────────┐          ┌─────────────────────────┐         │
│   │  Sharma & Associates    │          │  Bank Clients:          │         │
│   │  (Law Firm Tenant)      │          │  • HDFC Bank            │         │
│   │                         │◄─────────│  • ICICI Bank           │         │
│   │  • Has own branding     │  serves  │  • SBI                  │         │
│   │  • Own users/advocates  │          │  • Axis Bank            │         │
│   │  • Pays for SaaS        │          │                         │         │
│   │  • Isolated data        │          │  (Each is a "client"    │         │
│   └─────────────────────────┘          │   within the tenant)    │         │
│                                         └─────────────────────────┘         │
│                                                                              │
│   Data Model:                                                               │
│   • tenant_id = Law Firm ID (data isolation)                               │
│   • client_id = Bank ID (filter within tenant)                             │
│   • Loan requests tagged with both tenant_id AND client_id (bank)          │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 2.6 Document Types Handled
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
│  │   Tenant    │  │    Audit    │  │Notification │  │   AI/LLM    │        │
│  │  Branding   │  │    Trail    │  │   Engine    │  │  Processing │        │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘        │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 3.2 User Journey

```
Law Firm Clerk/Paralegal          Panel Advocate                    System
     │                               │                              │
     │  1. Create Request for        │                              │
     │     Bank Client (e.g., HDFC)  │                              │
     │──────────────────────────────────────────────────────────────>│
     │                               │                              │
     │  2. Upload Documents          │                              │
     │──────────────────────────────────────────────────────────────>│
     │                               │                              │
     │                               │  3. AI Extracts Document Data│
     │                               │<─────────────────────────────│
     │                               │                              │
     │                               │  4. Assignment Notification  │
     │                               │<─────────────────────────────│
     │                               │                              │
     │                               │  5. Review + AI Generate Draft│
     │                               │─────────────────────────────>│
     │                               │                              │
     │                               │  6. Edit & Submit Opinion    │
     │                               │─────────────────────────────>│
     │                               │                              │
     │  7. Opinion Ready for         │                              │
     │     Bank Client               │                              │
     │<─────────────────────────────────────────────────────────────│
     │                               │                              │
```

---

## 4. Architecture Principles

| Principle | Description |
|-----------|-------------|
| **Traditional 3-Tier** | Presentation → Business Logic → Data Layer |
| **Portable** | Run on VM (dev) or Kubernetes (production) |
| **Containerized** | Docker containers for all application components |
| **Multi-Tenant** | Shared infrastructure with logical data isolation via tenant_id |
| **API-First** | All functionality exposed via RESTful APIs |
| **Security-First** | Keycloak authentication, RBAC, audit logging |
| **Infrastructure as Code** | Terraform for AWS resources |
| **12-Factor App** | Follow 12-factor methodology |

---

## 5. High-Level Architecture

### 5.1 Traditional 3-Tier Architecture

![3-Tier Architecture](ui-mockups/arch-01-three-tier.png?v=3)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        TRADITIONAL 3-TIER ARCHITECTURE                       │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   TIER 1: PRESENTATION LAYER                                                │
│   ┌────────────────────────────────────────────────────────────────────────┐│
│   │                                                                         ││
│   │   ┌─────────────────────────────────────────────────────────────────┐  ││
│   │   │                    REACT SPA (Frontend)                          │  ││
│   │   │                                                                  │  ││
│   │   │  • Tenant-specific branding (logo, colors, favicon)             │  ││
│   │   │  • Responsive web application                                    │  ││
│   │   │  • Keycloak JS adapter for authentication                       │  ││
│   │   │  • Served via Nginx / S3 + CloudFront                           │  ││
│   │   │                                                                  │  ││
│   │   └─────────────────────────────────────────────────────────────────┘  ││
│   │                                                                         ││
│   └────────────────────────────────────────────────────────────────────────┘│
│                                      │                                       │
│                                      │ HTTPS (REST API)                      │
│                                      ▼                                       │
│   TIER 2: APPLICATION LAYER (BUSINESS LOGIC)                                │
│   ┌────────────────────────────────────────────────────────────────────────┐│
│   │                                                                         ││
│   │   ┌──────────────────────────────────────────────────────────────────┐ ││
│   │   │                   CENTRALIZED BACKEND API                         │ ││
│   │   │                   (Node.js/NestJS or Python/FastAPI)              │ ││
│   │   │                                                                   │ ││
│   │   │   ┌─────────────┐  ┌─────────────┐  ┌─────────────┐             │ ││
│   │   │   │   Tenant    │  │    User     │  │  Document   │             │ ││
│   │   │   │  Middleware │  │   Module    │  │   Module    │             │ ││
│   │   │   │ (tenant_id) │  │             │  │             │             │ ││
│   │   │   └─────────────┘  └─────────────┘  └─────────────┘             │ ││
│   │   │                                                                   │ ││
│   │   │   ┌─────────────┐  ┌─────────────┐  ┌─────────────┐             │ ││
│   │   │   │   Opinion   │  │   AI/LLM    │  │    Audit    │             │ ││
│   │   │   │   Module    │  │   Module    │  │   Module    │             │ ││
│   │   │   │             │  │    (LLM)    │  │             │             │ ││
│   │   │   └─────────────┘  └─────────────┘  └─────────────┘             │ ││
│   │   │                                                                   │ ││
│   │   │   • Single deployment, multi-tenant via tenant_id                │ ││
│   │   │   • Keycloak token validation                                    │ ││
│   │   │   • All queries filtered by tenant_id                            │ ││
│   │   │                                                                   │ ││
│   │   └──────────────────────────────────────────────────────────────────┘ ││
│   │                                                                         ││
│   └────────────────────────────────────────────────────────────────────────┘│
│                                      │                                       │
│                                      │ SQL / S3 API                          │
│                                      ▼                                       │
│   TIER 3: DATA LAYER                                                        │
│   ┌────────────────────────────────────────────────────────────────────────┐│
│   │                                                                         ││
│   │   ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐    ││
│   │   │   PostgreSQL     │  │     Amazon S3    │  │     Redis        │    ││
│   │   │   (AWS RDS)      │  │   (Documents)    │  │    (Cache)       │    ││
│   │   │                  │  │                  │  │                  │    ││
│   │   │ • Central DB     │  │ • Tenant folders │  │ • Session cache  │    ││
│   │   │ • tenant_id in   │  │ • /{tenant_id}/  │  │ • API cache      │    ││
│   │   │   all tables     │  │   /documents/    │  │                  │    ││
│   │   │ • RLS policies   │  │   /opinions/     │  │                  │    ││
│   │   │                  │  │                  │  │                  │    ││
│   │   └──────────────────┘  └──────────────────┘  └──────────────────┘    ││
│   │                                                                         ││
│   └────────────────────────────────────────────────────────────────────────┘│
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘

   EXTERNAL SERVICES
   ┌────────────────────────────────────────────────────────────────────────┐
   │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐                 │
   │  │   Keycloak   │  │   LLM API    │  │   SMTP       │                 │
   │  │   (Auth)     │  │  (Pluggable) │  │   (Email)    │                 │
   │  │              │  │              │  │              │                 │
   │  │ • User auth  │  │ • GPT-4      │  │ • AWS SES    │                 │
   │  │ • SSO        │  │ • Claude     │  │ • SendGrid   │                 │
   │  │ • MFA        │  │ • Gemini     │  │              │                 │
   │  │ • MFA        │  │ • Generation │  │              │                 │
   │  └──────────────┘  └──────────────┘  └──────────────┘                 │
   └────────────────────────────────────────────────────────────────────────┘
```

### 5.2 Deployment Flexibility

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        DEPLOYMENT OPTIONS                                    │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   DEVELOPMENT ENVIRONMENT (VM-based)                                        │
│   ┌────────────────────────────────────────────────────────────────────────┐│
│   │                                                                         ││
│   │   Single VM / Local Machine                                            ││
│   │   ┌─────────────────────────────────────────────────────────────────┐  ││
│   │   │                                                                  │  ││
│   │   │   docker-compose.yml                                             │  ││
│   │   │   ├── frontend (React)        → localhost:3000                  │  ││
│   │   │   ├── backend (NestJS/FastAPI)→ localhost:8080                  │  ││
│   │   │   ├── keycloak                → localhost:8180                  │  ││
│   │   │   ├── postgres                → localhost:5432                  │  ││
│   │   │   ├── redis                   → localhost:6379                  │  ││
│   │   │   └── minio (S3 compatible)   → localhost:9000                  │  ││
│   │   │                                                                  │  ││
│   │   └─────────────────────────────────────────────────────────────────┘  ││
│   │                                                                         ││
│   └────────────────────────────────────────────────────────────────────────┘│
│                                                                              │
│   PRODUCTION ENVIRONMENT (Kubernetes)                                       │
│   ┌────────────────────────────────────────────────────────────────────────┐│
│   │                                                                         ││
│   │   Kubernetes Cluster (EKS / Self-managed)                              ││
│   │   ┌─────────────────────────────────────────────────────────────────┐  ││
│   │   │                                                                  │  ││
│   │   │   Namespace: legal-opinion-prod                                  │  ││
│   │   │   ├── frontend-deployment     (replicas: 2-5)                   │  ││
│   │   │   ├── backend-deployment      (replicas: 3-10)                  │  ││
│   │   │   ├── keycloak-deployment     (replicas: 2)                     │  ││
│   │   │   ├── ingress-nginx           (load balancer)                   │  ││
│   │   │   └── HPA (auto-scaling)                                         │  ││
│   │   │                                                                  │  ││
│   │   │   External Services:                                             │  ││
│   │   │   ├── AWS RDS PostgreSQL      (managed)                         │  ││
│   │   │   ├── AWS S3                  (document storage)                │  ││
│   │   │   ├── AWS ElastiCache Redis   (caching)                         │  ││
│   │   │   └── LLM API                 (AI processing)                   │  ││
│   │   │                                                                  │  ││
│   │   └─────────────────────────────────────────────────────────────────┘  ││
│   │                                                                         ││
│   └────────────────────────────────────────────────────────────────────────┘│
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 5.3 Technology Stack

| Layer | Technology | Purpose |
|-------|------------|---------|
| **Frontend** | React.js + TypeScript | Single Page Application |
| **UI Framework** | Ant Design / Tailwind CSS | UI components |
| **Backend** | Node.js (NestJS) or Python (FastAPI) | REST API, business logic |
| **Database** | PostgreSQL (AWS RDS) | Relational data with tenant_id |
| **Cache** | Redis (AWS ElastiCache or self-hosted) | Session, API caching |
| **Document Storage** | Amazon S3 | Documents, PDFs (tenant folders) |
| **Authentication** | Keycloak | SSO, MFA, user management |
| **AI/LLM** | LLM API (GPT-4/Claude/Gemini) | Document extraction, opinion generation |
| **OCR** | Tesseract / AWS Textract | Text extraction from images |
| **Email** | AWS SES / SendGrid | Notifications |
| **Container Runtime** | Docker | Containerization |
| **Orchestration (Prod)** | Kubernetes (EKS) | Production scaling |
| **IaC** | Terraform | AWS infrastructure |
| **CI/CD** | GitHub Actions | Build, test, deploy |

---

## 6. Component Design

### 6.1 Frontend Application (React SPA)

```
┌─────────────────────────────────────────────────────────────────┐
│                   FRONTEND ARCHITECTURE                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │                 TENANT BRANDING LAYER                       │ │
│  │                                                             │ │
│  │  On app load:                                               │ │
│  │  1. Extract tenant from subdomain (sharma.legalopinion.com)│ │
│  │  2. OR from URL param (?tenant=sharma)                     │ │
│  │  3. Fetch tenant config from API: GET /api/tenants/config  │ │
│  │  4. Apply branding: logo, primary color, favicon           │ │
│  │                                                             │ │
│  │  Tenant Config Response (Law Firm):                        │ │
│  │  {                                                          │ │
│  │    "tenant_id": "uuid",                                    │ │
│  │    "name": "Sharma & Associates",                          │ │
│  │    "logo_url": "https://s3.../sharma/logo.png",           │ │
│  │    "favicon_url": "https://s3.../sharma/favicon.ico",     │ │
│  │    "primary_color": "#1a365d",                             │ │
│  │    "secondary_color": "#c9a227"                            │ │
│  │  }                                                          │ │
│  │                                                             │ │
│  └────────────────────────────────────────────────────────────┘ │
│                                                                  │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │                   APPLICATION MODULES                       │ │
│  │                                                             │ │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │ │
│  │  │    Auth      │  │  Dashboard   │  │   Requests   │     │ │
│  │  │  (Keycloak)  │  │              │  │  (Loan/Doc)  │     │ │
│  │  └──────────────┘  └──────────────┘  └──────────────┘     │ │
│  │                                                             │ │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │ │
│  │  │  Documents   │  │   Opinions   │  │    Users     │     │ │
│  │  │   Upload     │  │   Editor     │  │   (Admin)    │     │ │
│  │  └──────────────┘  └──────────────┘  └──────────────┘     │ │
│  │                                                             │ │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │ │
│  │  │   Reports    │  │  AI Assist   │  │   Settings   │     │ │
│  │  │   Audit      │  │   Panel      │  │   Tenant     │     │ │
│  │  └──────────────┘  └──────────────┘  └──────────────┘     │ │
│  │                                                             │ │
│  └────────────────────────────────────────────────────────────┘ │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

**Key UI Screens** (See [Section 12: UI Screens](#12-ui-screens) for mockups):
- Login Page (with tenant branding)
- Dashboard
- Opinion Requests List
- Opinion Request Detail
- Document Upload
- Document Viewer
- Opinion Editor (with AI assist)
- User Management
- Tenant Settings (Admin)
- Reports & Audit Logs

### 6.2 Backend API (Centralized, Multi-Tenant)

```
┌─────────────────────────────────────────────────────────────────┐
│                    BACKEND API STRUCTURE                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  src/                                                            │
│  ├── main.ts                    # Application entry              │
│  ├── app.module.ts              # Root module                    │
│  │                                                               │
│  ├── common/                                                     │
│  │   ├── middleware/                                             │
│  │   │   ├── tenant.middleware.ts    # Extract tenant_id        │
│  │   │   └── auth.middleware.ts      # Keycloak validation      │
│  │   ├── guards/                                                 │
│  │   │   ├── roles.guard.ts          # RBAC                     │
│  │   │   └── tenant.guard.ts         # Tenant access            │
│  │   ├── interceptors/                                           │
│  │   │   └── audit.interceptor.ts    # Auto audit logging       │
│  │   └── filters/                                                │
│  │       └── tenant.filter.ts        # Auto tenant_id filter    │
│  │                                                               │
│  ├── modules/                                                    │
│  │   ├── tenant/                                                 │
│  │   │   ├── tenant.controller.ts    # GET /tenants/config      │
│  │   │   ├── tenant.service.ts                                  │
│  │   │   └── tenant.entity.ts        # Branding config          │
│  │   │                                                           │
│  │   ├── user/                                                   │
│  │   │   ├── user.controller.ts                                 │
│  │   │   ├── user.service.ts                                    │
│  │   │   └── user.entity.ts          # tenant_id column         │
│  │   │                                                           │
│  │   ├── opinion-request/                                           │
│  │   │   ├── opinion-request.controller.ts                         │
│  │   │   ├── opinion-request.service.ts                            │
│  │   │   └── opinion-request.entity.ts  # tenant_id column         │
│  │   │                                                           │
│  │   ├── document/                                               │
│  │   │   ├── document.controller.ts                             │
│  │   │   ├── document.service.ts                                │
│  │   │   ├── document.entity.ts      # tenant_id column         │
│  │   │   └── s3.service.ts           # S3 with tenant folders   │
│  │   │                                                           │
│  │   ├── opinion/                                                │
│  │   │   ├── opinion.controller.ts                              │
│  │   │   ├── opinion.service.ts                                 │
│  │   │   └── opinion.entity.ts       # tenant_id column         │
│  │   │                                                           │
│  │   ├── ai/                                                     │
│  │   │   ├── ai.controller.ts        # AI endpoints             │
│  │   │   ├── ai.service.ts                                      │
│  │   │   ├── llm.service.ts          # LLM API calls (pluggable)│
│  │   │   └── extraction.entity.ts    # tenant_id column         │
│  │   │                                                           │
│  │   └── audit/                                                  │
│  │       ├── audit.controller.ts                                │
│  │       ├── audit.service.ts                                   │
│  │       └── audit.entity.ts         # tenant_id column         │
│  │                                                               │
│  └── config/                                                     │
│      ├── database.config.ts                                      │
│      ├── keycloak.config.ts                                      │
│      ├── s3.config.ts                                            │
│      └── openai.config.ts                                        │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 6.3 Tenant Middleware (Core Multi-Tenancy Logic)

```
REQUEST FLOW WITH TENANT RESOLUTION
───────────────────────────────────

┌──────────┐    ┌──────────────┐    ┌──────────────┐    ┌──────────────┐
│  Client  │───►│   Nginx /    │───►│   Keycloak   │───►│   Backend    │
│  (React) │    │   Ingress    │    │  Validation  │    │     API      │
└──────────┘    └──────────────┘    └──────────────┘    └──────┬───────┘
                                                               │
                                                               ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                      TENANT MIDDLEWARE                                   │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  1. Extract tenant_id from:                                             │
│     • JWT token claim: token.tenant_id (primary)                        │
│     • OR X-Tenant-ID header (internal service calls)                    │
│                                                                          │
│  2. Validate tenant exists and is active                                │
│                                                                          │
│  3. Set tenant context for request:                                     │
│     • req.tenantId = extracted_tenant_id                                │
│     • Set PostgreSQL session: SET app.current_tenant = 'tenant_id'      │
│                                                                          │
│  4. All subsequent queries automatically filtered by tenant_id          │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 7. Multi-Tenancy Strategy

### 7.1 Tenant Isolation Model

![Multi-Tenancy Architecture](ui-mockups/arch-02-multi-tenancy.png?v=3)

**Approach: Shared Database, Shared Schema with tenant_id Column**

**Key Concept: Law Firms are Tenants, Banks are Clients within a Tenant**

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                       MULTI-TENANCY ARCHITECTURE                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   UI LAYER (Per-Tenant Branding - Law Firm)                                 │
│   ┌────────────────────────────────────────────────────────────────────────┐│
│   │                                                                         ││
│   │   Law Firm A (sharma.app.com)    Law Firm B (kapoor.app.com)          ││
│   │   ┌─────────────────────┐        ┌─────────────────────┐              ││
│   │   │ [SHARMA & ASSOC]    │        │ [KAPOOR LAW FIRM]   │              ││
│   │   │ Primary: #1a365d    │        │ Primary: #2d3748    │              ││
│   │   │ Secondary: #c9a227  │        │ Secondary: #38a169  │              ││
│   │   │                     │        │                     │              ││
│   │   │ Bank Clients:       │        │ Bank Clients:       │              ││
│   │   │ • HDFC Bank         │        │ • SBI               │              ││
│   │   │ • ICICI Bank        │        │ • Axis Bank         │              ││
│   │   │ • Axis Bank         │        │ • Kotak Bank        │              ││
│   │   └─────────────────────┘        └─────────────────────┘              ││
│   │                                                                         ││
│   │   Same React codebase - law firm branding loaded at runtime            ││
│   │                                                                         ││
│   └────────────────────────────────────────────────────────────────────────┘│
│                                      │                                       │
│                                      ▼                                       │
│   API LAYER (Single Deployment, Tenant-Aware)                               │
│   ┌────────────────────────────────────────────────────────────────────────┐│
│   │                                                                         ││
│   │   ┌───────────────────────────────────────────────────────────────┐   ││
│   │   │                  CENTRALIZED BACKEND API                       │   ││
│   │   │                                                                │   ││
│   │   │   Request: Authorization: Bearer <jwt>                        │   ││
│   │   │                                                                │   ││
│   │   │   JWT Payload:                                                 │   ││
│   │   │   {                                                            │   ││
│   │   │     "sub": "user-uuid",                                       │   ││
│   │   │     "tenant_id": "sharma-tenant-uuid",  ← Law Firm ID         │   ││
│   │   │     "roles": ["panel_advocate"],                              │   ││
│   │   │     "email": "lawyer@sharmalaw.com"                           │   ││
│   │   │   }                                                            │   ││
│   │   │                                                                │   ││
│   │   │   All queries: WHERE tenant_id = :tenant_id                   │   ││
│   │   │   Bank filter: AND client_id = :client_id (optional)          │   ││
│   │   │                                                                │   ││
│   │   └───────────────────────────────────────────────────────────────┘   ││
│   │                                                                         ││
│   └────────────────────────────────────────────────────────────────────────┘│
│                                      │                                       │
│                                      ▼                                       │
│   DATA LAYER (Shared DB, Tenant Column + Client Column)                     │
│   ┌────────────────────────────────────────────────────────────────────────┐│
│   │                                                                         ││
│   │   PostgreSQL Database                                                  ││
│   │   ┌───────────────────────────────────────────────────────────────┐   ││
│   │   │                                                                │   ││
│   │   │   opinion_requests table                                          │   ││
│   │   │   ┌──────────┬──────────┬──────────┬──────────┬─────────┐    │   ││
│   │   │   │tenant_id │client_id │ id       │borrower  │ status  │    │   ││
│   │   │   │(Law Firm)│ (Bank)   │          │          │         │    │   ││
│   │   │   ├──────────┼──────────┼──────────┼──────────┼─────────┤    │   ││
│   │   │   │sharma-id │ hdfc-id  │ req001   │ Rajesh   │ pending │    │   ││
│   │   │   │sharma-id │ icici-id │ req002   │ Priya    │ review  │    │   ││
│   │   │   │kapoor-id │ sbi-id   │ req003   │ Amit     │ complete│    │   ││
│   │   │   └──────────┴──────────┴──────────┴──────────┴─────────┘    │   ││
│   │   │                                                                │   ││
│   │   │   Row Level Security (RLS):                                   │   ││
│   │   │   CREATE POLICY tenant_isolation ON opinion_requests             │   ││
│   │   │     USING (tenant_id = current_setting('app.current_tenant')) │   ││
│   │   │                                                                │   ││
│   │   └───────────────────────────────────────────────────────────────┘   ││
│   │                                                                         ││
│   │   Amazon S3 (Document Storage)                                         ││
│   │   ┌───────────────────────────────────────────────────────────────┐   ││
│   │   │                                                                │   ││
│   │   │   legal-opinion-docs/                                         │   ││
│   │   │   ├── {sharma-tenant-uuid}/     ← Law Firm folder            │   ││
│   │   │   │   ├── documents/                                          │   ││
│   │   │   │   ├── opinions/                                           │   ││
│   │   │   │   └── branding/             ← Law firm logo/favicon      │   ││
│   │   │   │       ├── logo.png                                        │   ││
│   │   │   │       └── favicon.ico                                     │   ││
│   │   │   ├── {kapoor-tenant-uuid}/                                   │   ││
│   │   │   │   ├── documents/                                          │   ││
│   │   │   │   ├── opinions/                                           │   ││
│   │   │   │   └── branding/                                           │   ││
│   │   │   └── ...                                                     │   ││
│   │   │                                                                │   ││
│   │   └───────────────────────────────────────────────────────────────┘   ││
│   │                                                                         ││
│   └────────────────────────────────────────────────────────────────────────┘│
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 7.2 Tenant Configuration Table (Law Firms)

```sql
CREATE TABLE tenants (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    code VARCHAR(50) UNIQUE NOT NULL,        -- 'SHARMA', 'KAPOOR'
    name VARCHAR(255) NOT NULL,              -- 'Sharma & Associates'
    
    -- Branding Configuration
    logo_url VARCHAR(500),                   -- S3 URL
    favicon_url VARCHAR(500),                -- S3 URL
    primary_color VARCHAR(7) DEFAULT '#1890ff',
    secondary_color VARCHAR(7) DEFAULT '#52c41a',
    
    -- Contact Info
    contact_email VARCHAR(255),
    contact_phone VARCHAR(20),
    address TEXT,
    
    -- Subscription
    subscription_tier VARCHAR(50) DEFAULT 'standard',
    max_users INT DEFAULT 100,
    max_documents_per_month INT DEFAULT 10000,
    
    -- Status
    is_active BOOLEAN DEFAULT true,
    trial_ends_at TIMESTAMP,
    
    -- Metadata
    settings JSONB DEFAULT '{}',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 7.3 Bank Clients Table (within Tenant)

```sql
-- Bank Clients (managed by Law Firm tenant)
CREATE TABLE bank_clients (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id),  -- Law Firm
    code VARCHAR(50) NOT NULL,             -- 'HDFC', 'ICICI'
    name VARCHAR(255) NOT NULL,            -- 'HDFC Bank'
    branch VARCHAR(255),                   -- 'Bandra West Branch'
    contact_name VARCHAR(255),             -- Bank contact person
    contact_email VARCHAR(255),
    contact_phone VARCHAR(20),
    address TEXT,
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(tenant_id, code)
);
```

### 7.4 Tenant Branding API (Law Firm)

```yaml
# API Endpoints for Tenant Configuration

# Public - Get law firm tenant config by subdomain/code (no auth required)
GET /api/v1/tenants/config?code=sharma
Response:
{
  "tenant_id": "uuid",
  "name": "HDFC Bank",
  "logo_url": "https://s3.../hdfc/logo.png",
  "favicon_url": "https://s3.../hdfc/favicon.ico", 
  "primary_color": "#004C8F",
  "secondary_color": "#ED1C24"
}

# Admin - Update tenant branding (requires tenant_admin role)
PUT /api/v1/tenants/branding
Authorization: Bearer <token>
Content-Type: multipart/form-data
Body: {
  "logo": <file>,
  "favicon": <file>,
  "primary_color": "#004C8F",
  "secondary_color": "#ED1C24"
}
```

---

## 8. Authentication with Keycloak

### 8.1 Keycloak Architecture

![Authentication Flow with Keycloak](ui-mockups/arch-05-auth-flow.png?v=3)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        KEYCLOAK AUTHENTICATION                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   KEYCLOAK SERVER                                                           │
│   ┌────────────────────────────────────────────────────────────────────────┐│
│   │                                                                         ││
│   │   Realm: legal-opinion-saas                                            ││
│   │                                                                         ││
│   │   ┌─────────────────────────────────────────────────────────────────┐  ││
│   │   │                     REALM CONFIGURATION                          │  ││
│   │   │                                                                  │  ││
│   │   │  • Login Settings: Email as username                            │  ││
│   │   │  • Password Policy: 12 chars, uppercase, number, special        │  ││
│   │   │  • MFA: TOTP (Google Authenticator) - Optional per tenant       │  ││
│   │   │  • Session: 8 hours, refresh 30 days                            │  ││
│   │   │  • Token: RS256 signed JWT                                      │  ││
│   │   │                                                                  │  ││
│   │   └─────────────────────────────────────────────────────────────────┘  ││
│   │                                                                         ││
│   │   ┌─────────────────────────────────────────────────────────────────┐  ││
│   │   │                        CLIENTS                                   │  ││
│   │   │                                                                  │  ││
│   │   │  ┌───────────────────┐    ┌───────────────────┐                │  ││
│   │   │  │ legal-opinion-web │    │ legal-opinion-api │                │  ││
│   │   │  │ (Public Client)   │    │(Confidential Client)│               │  ││
│   │   │  │                   │    │                   │                │  ││
│   │   │  │ • Frontend SPA    │    │ • Backend API     │                │  ││
│   │   │  │ • PKCE flow       │    │ • Service account │                │  ││
│   │   │  │ • Redirect URIs   │    │ • Token validation│                │  ││
│   │   │  └───────────────────┘    └───────────────────┘                │  ││
│   │   │                                                                  │  ││
│   │   └─────────────────────────────────────────────────────────────────┘  ││
│   │                                                                         ││
│   │   ┌─────────────────────────────────────────────────────────────────┐  ││
│   │   │                    ROLES (Realm Level)                           │  ││
│   │   │                                                                  │  ││
│   │   │  • super_admin      - Platform administration                   │  ││
│   │   │  • tenant_admin     - Tenant settings, user management          │  ││
│   │   │  • bank_officer     - Create requests, upload documents         │  ││
│   │   │  • panel_advocate   - Review, create opinions                   │  ││
│   │   │  • senior_advocate  - Approve opinions                          │  ││
│   │   │  • viewer           - Read-only access                          │  ││
│   │   │                                                                  │  ││
│   │   └─────────────────────────────────────────────────────────────────┘  ││
│   │                                                                         ││
│   │   ┌─────────────────────────────────────────────────────────────────┐  ││
│   │   │                USER ATTRIBUTES (Custom)                          │  ││
│   │   │                                                                  │  ││
│   │   │  • tenant_id: UUID (required) - mapped to JWT                   │  ││
│   │   │  • tenant_code: String        - mapped to JWT                   │  ││
│   │   │  • phone: String                                                │  ││
│   │   │  • bar_council_number: String (for advocates)                   │  ││
│   │   │                                                                  │  ││
│   │   └─────────────────────────────────────────────────────────────────┘  ││
│   │                                                                         ││
│   └────────────────────────────────────────────────────────────────────────┘│
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 8.2 JWT Token Structure

```json
{
  "exp": 1706200000,
  "iat": 1706196400,
  "jti": "jwt-id-uuid",
  "iss": "https://keycloak.example.com/realms/legal-opinion-saas",
  "aud": "legal-opinion-api",
  "sub": "user-uuid",
  "typ": "Bearer",
  "azp": "legal-opinion-web",
  
  "realm_access": {
    "roles": ["panel_advocate"]
  },
  
  "scope": "openid email profile",
  "email_verified": true,
  "name": "John Advocate",
  "preferred_username": "john@hdfc.com",
  "email": "john@hdfc.com",
  
  "tenant_id": "hdfc-tenant-uuid",
  "tenant_code": "HDFC"
}
```

### 8.3 Authentication Flows

```
FRONTEND AUTHENTICATION (PKCE Flow)
───────────────────────────────────

┌──────────┐     ┌──────────┐     ┌──────────┐
│  React   │     │ Keycloak │     │ Backend  │
│   App    │     │  Server  │     │   API    │
└────┬─────┘     └────┬─────┘     └────┬─────┘
     │                │                │
     │ 1. User clicks login            │
     │─────────────────────────────────>
     │                │                │
     │ 2. Redirect to Keycloak login page
     │<────────────────                │
     │                │                │
     │ 3. User enters credentials + MFA
     │─────────────────>               │
     │                │                │
     │ 4. Auth code (via redirect)     │
     │<────────────────                │
     │                │                │
     │ 5. Exchange code for tokens (PKCE)
     │─────────────────>               │
     │                │                │
     │ 6. Access token + Refresh token │
     │<────────────────                │
     │                │                │
     │ 7. API request with Bearer token│
     │────────────────────────────────>│
     │                │                │
     │                │ 8. Validate JWT │
     │                │    (public key)│
     │                │                │
     │ 9. Response    │                │
     │<────────────────────────────────│
     │                │                │


BACKEND TOKEN VALIDATION
────────────────────────

┌──────────────────────────────────────────────────────────────────┐
│                   BACKEND AUTH MIDDLEWARE                         │
├──────────────────────────────────────────────────────────────────┤
│                                                                   │
│  1. Extract token from Authorization header                      │
│     Authorization: Bearer eyJhbGciOiJSUzI1NiIs...                │
│                                                                   │
│  2. Validate JWT signature using Keycloak public key             │
│     • Fetch JWKS from: {keycloak}/realms/{realm}/protocol/       │
│       openid-connect/certs                                       │
│     • Cache public keys                                          │
│                                                                   │
│  3. Validate claims:                                             │
│     • exp: Token not expired                                     │
│     • iss: Correct Keycloak issuer                              │
│     • aud: Contains our API client                              │
│                                                                   │
│  4. Extract tenant_id and roles from token                       │
│                                                                   │
│  5. Set request context:                                         │
│     req.user = { id, email, roles, tenant_id }                  │
│                                                                   │
└──────────────────────────────────────────────────────────────────┘
```

### 8.4 Keycloak Integration Libraries

```javascript
// Frontend: keycloak-js
import Keycloak from 'keycloak-js';

const keycloak = new Keycloak({
  url: 'https://keycloak.example.com',
  realm: 'legal-opinion-saas',
  clientId: 'legal-opinion-web'
});

// Initialize
await keycloak.init({ 
  onLoad: 'check-sso',
  pkceMethod: 'S256'
});

// Get token for API calls
const token = keycloak.token;
```

```typescript
// Backend: @nestjs/keycloak-connect (NestJS)
// OR: python-keycloak (FastAPI)

// NestJS Guard Example
@UseGuards(AuthGuard, RolesGuard)
@Roles('panel_advocate', 'senior_advocate')
@Get('/opinions')
async getOpinions(@Req() req) {
  const tenantId = req.user.tenant_id;
  return this.opinionService.findAll(tenantId);
}
```

---

## 9. Data Architecture

### 9.1 Entity Relationship Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                          DATA MODEL OVERVIEW                                 │
│                    (All tables have tenant_id column)                        │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌──────────────┐         ┌──────────────┐         ┌──────────────┐        │
│  │   TENANTS    │         │    USERS     │         │    ROLES     │        │
│  ├──────────────┤         ├──────────────┤         ├──────────────┤        │
│  │ id (PK)      │◄───┐    │ id (PK)      │    ┌───►│ id (PK)      │        │
│  │ code         │    │    │ tenant_id(FK)│────┤    │ name         │        │
│  │ name         │    └────│ keycloak_id  │    │    │ permissions  │        │
│  │ logo_url     │         │ email        │    │    │              │        │
│  │ primary_color│         │ role_id (FK) │────┘    │              │        │
│  │ settings     │         │ profile      │         │              │        │
│  └──────────────┘         └──────┬───────┘         └──────────────┘        │
│                                  │                                          │
│  ┌──────────────┐         ┌──────▼───────┐         ┌──────────────┐        │
│  │   BORROWERS  │         │LOAN_REQUESTS │         │   DOCUMENTS  │        │
│  ├──────────────┤         ├──────────────┤         ├──────────────┤        │
│  │ id (PK)      │◄────────│ id (PK)      │────────►│ id (PK)      │        │
│  │ tenant_id    │         │ tenant_id    │         │ tenant_id    │        │
│  │ name         │         │ borrower_id  │         │ request_id   │        │
│  │ contact      │         │ loan_type    │         │ doc_type     │        │
│  │ pan_number   │         │ amount       │         │ s3_key       │        │
│  │ aadhaar      │         │ status       │         │ ocr_text     │        │
│  └──────────────┘         │ assigned_to  │         │ extracted_data│       │
│                           └──────┬───────┘         └──────────────┘        │
│                                  │                                          │
│  ┌──────────────┐         ┌──────▼───────┐         ┌──────────────┐        │
│  │  TEMPLATES   │         │   OPINIONS   │         │ AUDIT_LOGS   │        │
│  ├──────────────┤         ├──────────────┤         ├──────────────┤        │
│  │ id (PK)      │────────►│ id (PK)      │         │ id (PK)      │        │
│  │ tenant_id    │         │ tenant_id    │         │ tenant_id    │        │
│  │ name         │         │ request_id   │         │ entity_type  │        │
│  │ content      │         │ template_id  │         │ entity_id    │        │
│  │ loan_type    │         │ content      │         │ action       │        │
│  │              │         │ status       │         │ user_id      │        │
│  │              │         │ ai_generated │         │ changes      │        │
│  └──────────────┘         │ pdf_s3_key   │         │ timestamp    │        │
│                           └──────────────┘         └──────────────┘        │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 9.2 Key Tables Schema

```sql
-- Enable UUID extension
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- Tenants Table (Master)
CREATE TABLE tenants (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    code VARCHAR(50) UNIQUE NOT NULL,
    name VARCHAR(255) NOT NULL,
    logo_url VARCHAR(500),
    favicon_url VARCHAR(500),
    primary_color VARCHAR(7) DEFAULT '#1890ff',
    secondary_color VARCHAR(7) DEFAULT '#52c41a',
    contact_email VARCHAR(255),
    contact_phone VARCHAR(20),
    address TEXT,
    subscription_tier VARCHAR(50) DEFAULT 'standard',
    max_users INT DEFAULT 100,
    is_active BOOLEAN DEFAULT true,
    settings JSONB DEFAULT '{}',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Users Table
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    tenant_id UUID NOT NULL REFERENCES tenants(id),
    keycloak_id VARCHAR(255) UNIQUE NOT NULL,
    email VARCHAR(255) NOT NULL,
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    phone VARCHAR(20),
    role VARCHAR(50) NOT NULL,
    profile JSONB DEFAULT '{}',
    is_active BOOLEAN DEFAULT true,
    last_login_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(tenant_id, email)
);

-- Bank Clients Table (Banks served by the Law Firm)
CREATE TABLE bank_clients (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    tenant_id UUID NOT NULL REFERENCES tenants(id),  -- Law Firm
    code VARCHAR(50) NOT NULL,
    name VARCHAR(255) NOT NULL,
    branch VARCHAR(255),
    contact_name VARCHAR(255),
    contact_email VARCHAR(255),
    contact_phone VARCHAR(20),
    address TEXT,
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(tenant_id, code)
);

-- Opinion Requests Table
CREATE TABLE opinion_requests (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    tenant_id UUID NOT NULL REFERENCES tenants(id),    -- Law Firm
    client_id UUID NOT NULL REFERENCES bank_clients(id), -- Bank Client
    reference_number VARCHAR(50) NOT NULL,
    borrower_id UUID NOT NULL REFERENCES borrowers(id),
    loan_type VARCHAR(50) NOT NULL,
    loan_amount DECIMAL(15,2),
    property_location TEXT,
    branch_code VARCHAR(20),
    created_by UUID NOT NULL REFERENCES users(id),
    assigned_lawyer_id UUID REFERENCES users(id),
    status VARCHAR(30) DEFAULT 'PENDING',
    priority VARCHAR(20) DEFAULT 'NORMAL',
    due_date DATE,
    metadata JSONB DEFAULT '{}',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(tenant_id, reference_number)
);

-- Documents Table
CREATE TABLE documents (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    tenant_id UUID NOT NULL REFERENCES tenants(id),
    loan_request_id UUID NOT NULL REFERENCES opinion_requests(id),
    document_type VARCHAR(50) NOT NULL,
    original_filename VARCHAR(255) NOT NULL,
    s3_key VARCHAR(500) NOT NULL,
    file_size BIGINT,
    mime_type VARCHAR(100),
    ocr_status VARCHAR(20) DEFAULT 'PENDING',
    ocr_text TEXT,
    extracted_data JSONB,
    ai_confidence DECIMAL(3,2),
    uploaded_by UUID NOT NULL REFERENCES users(id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Legal Opinions Table
CREATE TABLE legal_opinions (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    tenant_id UUID NOT NULL REFERENCES tenants(id),
    loan_request_id UUID NOT NULL REFERENCES opinion_requests(id),
    template_id UUID REFERENCES opinion_templates(id),
    opinion_number VARCHAR(50) NOT NULL,
    version INT DEFAULT 1,
    status VARCHAR(30) DEFAULT 'DRAFT',
    content JSONB NOT NULL,
    summary TEXT,
    recommendation VARCHAR(50),
    conditions TEXT[],
    ai_generated BOOLEAN DEFAULT false,
    ai_draft_content JSONB,
    pdf_s3_key VARCHAR(500),
    created_by UUID NOT NULL REFERENCES users(id),
    reviewed_by UUID REFERENCES users(id),
    approved_by UUID REFERENCES users(id),
    submitted_at TIMESTAMP,
    approved_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(tenant_id, opinion_number)
);

-- Audit Logs Table
CREATE TABLE audit_logs (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    tenant_id UUID NOT NULL,
    entity_type VARCHAR(50) NOT NULL,
    entity_id UUID NOT NULL,
    action VARCHAR(50) NOT NULL,
    old_values JSONB,
    new_values JSONB,
    user_id UUID NOT NULL,
    user_email VARCHAR(255),
    ip_address INET,
    user_agent TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Indexes
CREATE INDEX idx_users_tenant ON users(tenant_id);
CREATE INDEX idx_opinion_requests_tenant ON opinion_requests(tenant_id);
CREATE INDEX idx_opinion_requests_status ON opinion_requests(tenant_id, status);
CREATE INDEX idx_documents_request ON documents(loan_request_id);
CREATE INDEX idx_opinions_request ON legal_opinions(loan_request_id);
CREATE INDEX idx_audit_logs_tenant ON audit_logs(tenant_id, created_at DESC);

-- Row Level Security
ALTER TABLE users ENABLE ROW LEVEL SECURITY;
ALTER TABLE opinion_requests ENABLE ROW LEVEL SECURITY;
ALTER TABLE documents ENABLE ROW LEVEL SECURITY;
ALTER TABLE legal_opinions ENABLE ROW LEVEL SECURITY;

CREATE POLICY tenant_isolation ON users
    USING (tenant_id = current_setting('app.current_tenant')::uuid);
CREATE POLICY tenant_isolation ON opinion_requests
    USING (tenant_id = current_setting('app.current_tenant')::uuid);
CREATE POLICY tenant_isolation ON documents
    USING (tenant_id = current_setting('app.current_tenant')::uuid);
CREATE POLICY tenant_isolation ON legal_opinions
    USING (tenant_id = current_setting('app.current_tenant')::uuid);
```

---

## 10. AI/LLM Integration

### 10.1 LLM Integration Architecture (Pluggable)

![LLM Integration Architecture](ui-mockups/arch-03-llm-integration.png?v=3)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                     LLM INTEGRATION (PLUGGABLE)                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   DOCUMENT EXTRACTION PIPELINE                                              │
│   ┌────────────────────────────────────────────────────────────────────────┐│
│   │                                                                         ││
│   │   ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐       ││
│   │   │ Document │───►│  OCR     │───►│   LLM    │───►│ Structured│       ││
│   │   │  (S3)    │    │(Tesseract│    │(GPT/Claude│   │   JSON    │       ││
│   │   │          │    │/Textract)│    │          │    │           │       ││
│   │   └──────────┘    └──────────┘    └──────────┘    └──────────┘       ││
│   │                                                                         ││
│   │   Extraction Prompt:                                                   ││
│   │   "Extract the following from this property document:                  ││
│   │    - property_address                                                  ││
│   │    - seller_name, seller_address                                       ││
│   │    - buyer_name, buyer_address                                         ││
│   │    - sale_amount                                                       ││
│   │    - registration_date, registration_number                            ││
│   │    - encumbrances                                                      ││
│   │    Return as JSON with confidence scores."                             ││
│   │                                                                         ││
│   └────────────────────────────────────────────────────────────────────────┘│
│                                                                              │
│   OPINION GENERATION PIPELINE                                               │
│   ┌────────────────────────────────────────────────────────────────────────┐│
│   │                                                                         ││
│   │   ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐       ││
│   │   │ Extracted│    │ Template │    │   LLM    │    │   Draft  │       ││
│   │   │   Data   │+   │ Prompt   │───►│(GPT/Claude│───►│  Opinion │       ││
│   │   │          │    │          │    │          │    │          │       ││
│   │   └──────────┘    └──────────┘    └──────────┘    └──────────┘       ││
│   │                                                                         ││
│   │   Generation Prompt:                                                   ││
│   │   "Generate a legal opinion for a home loan based on:                  ││
│   │    - Extracted document data: {json}                                   ││
│   │    - Template: {template}                                              ││
│   │    - Bank: {bank_name}                                                 ││
│   │    Include: title verification, chain of ownership,                    ││
│   │    encumbrance status, recommendations."                               ││
│   │                                                                         ││
│   └────────────────────────────────────────────────────────────────────────┘│
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 10.2 AI Service Implementation

```typescript
// ai.service.ts

interface ExtractionResult {
  fields: Record<string, any>;
  confidence: Record<string, number>;
  flags: string[];
}

interface OpinionDraft {
  content: {
    title_verification: string;
    ownership_chain: string;
    encumbrance_status: string;
    recommendations: string[];
  };
  summary: string;
  recommendation: 'POSITIVE' | 'NEGATIVE' | 'CONDITIONAL';
}

class AIService {
  
  async extractDocumentData(
    documentId: string, 
    ocrText: string, 
    documentType: string
  ): Promise<ExtractionResult> {
    
    const prompt = this.buildExtractionPrompt(documentType, ocrText);
    
    const response = await openai.chat.completions.create({
      model: 'gpt-4-turbo-preview',
      messages: [
        { role: 'system', content: EXTRACTION_SYSTEM_PROMPT },
        { role: 'user', content: prompt }
      ],
      response_format: { type: 'json_object' },
      temperature: 0.2
    });
    
    return JSON.parse(response.choices[0].message.content);
  }
  
  async generateOpinionDraft(
    requestId: string,
    extractedData: Record<string, any>,
    templateId: string
  ): Promise<OpinionDraft> {
    
    const template = await this.templateService.findById(templateId);
    const prompt = this.buildGenerationPrompt(extractedData, template);
    
    const response = await openai.chat.completions.create({
      model: 'gpt-4-turbo-preview',
      messages: [
        { role: 'system', content: GENERATION_SYSTEM_PROMPT },
        { role: 'user', content: prompt }
      ],
      response_format: { type: 'json_object' },
      temperature: 0.4
    });
    
    return JSON.parse(response.choices[0].message.content);
  }
}
```

---

## 11. Infrastructure Design

### 11.1 Development Environment (Docker Compose)

```yaml
# docker-compose.yml (Development)
version: '3.8'

services:
  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    environment:
      - REACT_APP_API_URL=http://localhost:8080
      - REACT_APP_KEYCLOAK_URL=http://localhost:8180
    volumes:
      - ./frontend/src:/app/src
    depends_on:
      - backend
      - keycloak

  backend:
    build: ./backend
    ports:
      - "8080:8080"
    environment:
      - DATABASE_URL=postgresql://postgres:postgres@db:5432/legal_opinion
      - REDIS_URL=redis://redis:6379
      - S3_ENDPOINT=http://minio:9000
      - KEYCLOAK_URL=http://keycloak:8080
      - OPENAI_API_KEY=${OPENAI_API_KEY}
    volumes:
      - ./backend/src:/app/src
    depends_on:
      - db
      - redis
      - minio
      - keycloak

  keycloak:
    image: quay.io/keycloak/keycloak:23.0
    ports:
      - "8180:8080"
    environment:
      - KEYCLOAK_ADMIN=admin
      - KEYCLOAK_ADMIN_PASSWORD=admin
      - KC_DB=postgres
      - KC_DB_URL=jdbc:postgresql://db:5432/keycloak
      - KC_DB_USERNAME=postgres
      - KC_DB_PASSWORD=postgres
    command: start-dev
    depends_on:
      - db

  db:
    image: postgres:15
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./scripts/init-db.sql:/docker-entrypoint-initdb.d/init.sql

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

  minio:
    image: minio/minio
    ports:
      - "9000:9000"
      - "9001:9001"
    environment:
      - MINIO_ROOT_USER=minioadmin
      - MINIO_ROOT_PASSWORD=minioadmin
    command: server /data --console-address ":9001"
    volumes:
      - minio_data:/data

volumes:
  postgres_data:
  minio_data:
```

### 11.2 Production Environment (Kubernetes + AWS)

![Deployment Architecture](ui-mockups/arch-04-deployment.png?v=3)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    PRODUCTION INFRASTRUCTURE (AWS)                           │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   ┌────────────────────────────────────────────────────────────────────────┐│
│   │                        AWS ACCOUNT                                      ││
│   │                                                                         ││
│   │   Internet                                                             ││
│   │      │                                                                  ││
│   │      ▼                                                                  ││
│   │   ┌──────────────┐                                                     ││
│   │   │ Route 53     │  DNS: *.legalopinion.com                           ││
│   │   └──────┬───────┘                                                     ││
│   │          │                                                              ││
│   │          ▼                                                              ││
│   │   ┌──────────────┐                                                     ││
│   │   │ CloudFront   │  CDN + SSL termination                             ││
│   │   └──────┬───────┘                                                     ││
│   │          │                                                              ││
│   │   ┌──────┴───────┬─────────────────────────────────────┐              ││
│   │   │              │                                      │              ││
│   │   ▼              ▼                                      ▼              ││
│   │ ┌────────┐  ┌────────────┐                        ┌────────────┐      ││
│   │ │   S3   │  │    ALB     │                        │  Keycloak  │      ││
│   │ │(Static)│  │ (API LB)   │                        │   (EC2)    │      ││
│   │ └────────┘  └─────┬──────┘                        └────────────┘      ││
│   │                   │                                                    ││
│   │   ┌───────────────┴───────────────┐                                   ││
│   │   │         VPC (10.0.0.0/16)     │                                   ││
│   │   │                               │                                   ││
│   │   │   ┌─────────────────────────┐ │                                   ││
│   │   │   │    EKS Cluster          │ │                                   ││
│   │   │   │                         │ │                                   ││
│   │   │   │  ┌─────────┐ ┌────────┐ │ │                                   ││
│   │   │   │  │Frontend │ │Backend │ │ │                                   ││
│   │   │   │  │ Pods    │ │ Pods   │ │ │                                   ││
│   │   │   │  │ (2-5)   │ │(3-10)  │ │ │                                   ││
│   │   │   │  └─────────┘ └────────┘ │ │                                   ││
│   │   │   │                         │ │                                   ││
│   │   │   └─────────────────────────┘ │                                   ││
│   │   │                               │                                   ││
│   │   │   ┌─────────────────────────┐ │                                   ││
│   │   │   │    Data Layer           │ │                                   ││
│   │   │   │                         │ │                                   ││
│   │   │   │  ┌─────────┐ ┌────────┐ │ │                                   ││
│   │   │   │  │  RDS    │ │ Redis  │ │ │                                   ││
│   │   │   │  │PostgreSQL││ElastiC │ │ │                                   ││
│   │   │   │  │(Multi-AZ)││        │ │ │                                   ││
│   │   │   │  └─────────┘ └────────┘ │ │                                   ││
│   │   │   │                         │ │                                   ││
│   │   │   └─────────────────────────┘ │                                   ││
│   │   │                               │                                   ││
│   │   └───────────────────────────────┘                                   ││
│   │                                                                         ││
│   │   ┌─────────────────────────────────────────────────────────────────┐  ││
│   │   │  S3 Buckets                                                      │  ││
│   │   │  • legal-opinion-documents (tenant folders)                     │  ││
│   │   │  • legal-opinion-static (frontend assets)                       │  ││
│   │   └─────────────────────────────────────────────────────────────────┘  ││
│   │                                                                         ││
│   └────────────────────────────────────────────────────────────────────────┘│
│                                                                              │
│   External Services:                                                        │
│   • LLM API (OpenAI/Anthropic/Google - configurable)                       │
│   • AWS SES (Email)                                                         │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 12. UI Screens

### 12.1 Screen Inventory

The following UI screens are required for the application. Mock HTML files and screenshots are available in the `ui-mockups/` folder.

---

#### 12.1.1 Login Page
**File:** [ui-mockups/01-login.html](ui-mockups/01-login.html)

Tenant-branded login page with Keycloak SSO integration. Branding (logo, colors) loaded from tenant config.

![Login Page](ui-mockups/01-login.png?v=3)

---

#### 12.1.2 Dashboard
**File:** [ui-mockups/02-dashboard.html](ui-mockups/02-dashboard.html)

Overview with key metrics, pending tasks, and recent activity.

![Dashboard](ui-mockups/02-dashboard.png?v=3)

---

#### 12.1.3 Opinion Requests List
**File:** [ui-mockups/03-opinion-requests.html](ui-mockups/03-opinion-requests.html)

List of all loan requests with search, filters (status, loan type, priority), and pagination.

![Opinion Requests List](ui-mockups/03-opinion-requests.png?v=3)

---

#### 12.1.4 Opinion Request Detail
**File:** [ui-mockups/04-opinion-request-detail.html](ui-mockups/04-opinion-request-detail.html)

Single request view with borrower details, documents list, quick actions, and activity timeline.

![Opinion Request Detail](ui-mockups/04-opinion-request-detail.png?v=3)

---

#### 12.1.5 Document Upload
**File:** [ui-mockups/05-document-upload.html](ui-mockups/05-document-upload.html)

Drag-drop document upload with category selection and AI processing status.

![Document Upload](ui-mockups/05-document-upload.png?v=3)

---

#### 12.1.6 Document Viewer
**File:** [ui-mockups/06-document-viewer.html](ui-mockups/06-document-viewer.html)

View document with AI-extracted data panel showing property details, party information, and confidence scores.

![Document Viewer](ui-mockups/06-document-viewer.png?v=3)

---

#### 12.1.7 Opinion Editor
**File:** [ui-mockups/07-opinion-editor.html](ui-mockups/07-opinion-editor.html)

Create/edit legal opinion with AI assistance. Features document summary, rich text editor, and AI suggestions.

![Opinion Editor](ui-mockups/07-opinion-editor.png?v=3)

---

#### 12.1.8 User Management
**File:** [ui-mockups/08-user-management.html](ui-mockups/08-user-management.html)

Admin screen for user CRUD operations with role management.

![User Management](ui-mockups/08-user-management.png?v=3)

---

#### 12.1.9 Tenant Settings
**File:** [ui-mockups/09-tenant-settings.html](ui-mockups/09-tenant-settings.html)

Admin screen for tenant branding (logo, colors), organization info, and feature settings.

![Tenant Settings](ui-mockups/09-tenant-settings.png?v=3)

---

#### 12.1.10 Reports & Analytics
**File:** [ui-mockups/10-reports.html](ui-mockups/10-reports.html)

Analytics dashboard with charts, recent opinions table, and activity feed.

![Reports & Analytics](ui-mockups/10-reports.png?v=3)

---

### 12.2 Screen Flow

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                           USER FLOW DIAGRAM                                   │
├──────────────────────────────────────────────────────────────────────────────┤
│                                                                               │
│   ┌─────────┐      ┌───────────┐      ┌─────────────────┐                   │
│   │  LOGIN  │─────►│ DASHBOARD │─────►│ LOAN REQUESTS   │                   │
│   │         │      │           │      │     LIST        │                   │
│   └─────────┘      └───────────┘      └────────┬────────┘                   │
│                                                │                             │
│                          ┌─────────────────────┼─────────────────────┐       │
│                          │                     │                     │       │
│                          ▼                     ▼                     ▼       │
│                   ┌─────────────┐       ┌─────────────┐       ┌───────────┐ │
│                   │ NEW REQUEST │       │   REQUEST   │       │  REPORTS  │ │
│                   │   FORM      │       │   DETAIL    │       │           │ │
│                   └──────┬──────┘       └──────┬──────┘       └───────────┘ │
│                          │                     │                             │
│                          ▼                     ▼                             │
│                   ┌─────────────┐       ┌─────────────┐                     │
│                   │  DOCUMENT   │       │  DOCUMENT   │                     │
│                   │   UPLOAD    │       │   VIEWER    │                     │
│                   └─────────────┘       └──────┬──────┘                     │
│                                                │                             │
│                                                ▼                             │
│                                         ┌─────────────┐                     │
│                                         │   OPINION   │                     │
│                                         │   EDITOR    │                     │
│                                         │ (AI Assist) │                     │
│                                         └─────────────┘                     │
│                                                                               │
│   ADMIN FLOW:                                                                │
│   ┌─────────────┐       ┌─────────────┐                                     │
│   │    USER     │       │   TENANT    │                                     │
│   │ MANAGEMENT  │       │  SETTINGS   │                                     │
│   └─────────────┘       └─────────────┘                                     │
│                                                                               │
└──────────────────────────────────────────────────────────────────────────────┘
```

---

## 13. CI/CD Pipeline

### 13.1 GitHub Actions Workflow

```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run Backend Tests
        run: |
          cd backend
          npm ci
          npm run test
      - name: Run Frontend Tests
        run: |
          cd frontend
          npm ci
          npm run test
      
  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - name: Build Docker Images
        run: |
          docker build -t backend:${{ github.sha }} ./backend
          docker build -t frontend:${{ github.sha }} ./frontend
      - name: Push to ECR
        run: |
          aws ecr get-login-password | docker login --username AWS --password-stdin $ECR_REGISTRY
          docker push $ECR_REGISTRY/backend:${{ github.sha }}
          docker push $ECR_REGISTRY/frontend:${{ github.sha }}
        
  deploy-dev:
    needs: build
    if: github.ref == 'refs/heads/develop'
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to Dev
        run: |
          kubectl set image deployment/backend backend=$ECR_REGISTRY/backend:${{ github.sha }}
          kubectl set image deployment/frontend frontend=$ECR_REGISTRY/frontend:${{ github.sha }}
        
  deploy-prod:
    needs: build
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment: production
    steps:
      - name: Deploy to Production
        run: |
          kubectl set image deployment/backend backend=$ECR_REGISTRY/backend:${{ github.sha }}
          kubectl set image deployment/frontend frontend=$ECR_REGISTRY/frontend:${{ github.sha }}
```

---

## 14. Non-Functional Requirements

| Metric | Target |
|--------|--------|
| API Response Time (P95) | < 500ms |
| Page Load Time | < 3s |
| Document Upload | < 30s for 50MB |
| Concurrent Users | 500 per tenant |
| Uptime SLA | 99.9% |
| RTO | < 4 hours |
| RPO | < 1 hour |

---

## 15. Cost Estimation

### 15.1 Monthly Cost (Production)

| Service | Configuration | Est. Cost |
|---------|---------------|-----------|
| **EKS Cluster** | 1 cluster | $73 |
| **EC2 (EKS Nodes)** | 3x t3.large | $150 |
| **RDS PostgreSQL** | db.t3.large, Multi-AZ | $250 |
| **ElastiCache Redis** | cache.t3.medium | $80 |
| **S3 Storage** | 500GB | $15 |
| **CloudFront** | 500GB transfer | $50 |
| **ALB** | 1 ALB | $25 |
| **Keycloak EC2** | t3.medium | $35 |
| **LLM API** | ~100K requests | $200-500 |
| **SES** | 50K emails | $5 |
| **TOTAL** | | **~$900-1,100/month** |

---

## 16. Risks & Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| Data breach | High | Encryption, WAF, regular audits |
| Multi-tenant data leakage | High | RLS, code reviews, pen testing |
| LLM API downtime | Medium | Fallback provider, manual mode, caching |
| Cost overrun (AI) | Medium | Token limits, monitoring |
| Keycloak single point of failure | Medium | HA deployment, backups |

---

## Appendix A: API Endpoints

```yaml
# Authentication (Keycloak)
GET    /auth/realms/legal-opinion-saas/protocol/openid-connect/auth
POST   /auth/realms/legal-opinion-saas/protocol/openid-connect/token

# Tenant Config (Public)
GET    /api/v1/tenants/config?code={code}

# Opinion Requests
GET    /api/v1/opinion-requests
POST   /api/v1/opinion-requests
GET    /api/v1/opinion-requests/{id}
PUT    /api/v1/opinion-requests/{id}
PATCH  /api/v1/opinion-requests/{id}/status
PATCH  /api/v1/opinion-requests/{id}/assign

# Documents
POST   /api/v1/documents/upload
GET    /api/v1/documents/{id}
GET    /api/v1/documents/{id}/download
POST   /api/v1/documents/{id}/extract  # AI extraction

# Opinions
GET    /api/v1/opinions
POST   /api/v1/opinions
GET    /api/v1/opinions/{id}
PUT    /api/v1/opinions/{id}
PATCH  /api/v1/opinions/{id}/status
GET    /api/v1/opinions/{id}/pdf
POST   /api/v1/opinions/{id}/generate-draft  # AI generation

# Users (Admin)
GET    /api/v1/users
POST   /api/v1/users
PUT    /api/v1/users/{id}
DELETE /api/v1/users/{id}

# Tenant Settings (Admin)
GET    /api/v1/tenant/settings
PUT    /api/v1/tenant/settings
PUT    /api/v1/tenant/branding

# Reports
GET    /api/v1/reports/dashboard
GET    /api/v1/reports/opinions-summary
GET    /api/v1/reports/audit-logs
```

---

**Document Control**

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | Jan 2026 | Solution Architect | Initial draft |
| 2.0 | Jan 2026 | Solution Architect | Traditional 3-tier, Keycloak, LLM API, UI mockups |

---

*This is a living document and will be updated as requirements evolve.*
