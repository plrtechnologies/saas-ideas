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
   - **Regional Language Support**: OCR extracts text from documents in regional languages, then translates to English for processing
   - **Language Detection**: Automatically detects document language
   - **Translation**: Converts regional language text to English while preserving legal terms and names
2. **Opinion Generation**: Generate draft legal opinions by combining extracted data with **bank client-specific templates**
   - Each bank client has their own opinion format template
   - Opinions always generated in English only
3. **Content Validation**: Identify discrepancies and flag potential issues in documents

See **Section 10** for the detailed LLM Integration Architecture diagram.

### 2.4 Key Stakeholders
| Stakeholder | Role |
|-------------|------|
| Law Firm Admin | Manage users, bank clients, view reports, configure workflows |
| Senior Advocate | Review and approve opinions, assign cases to advocates |
| Panel Advocate | Review documents, generate legal opinions |
| Paralegal/Clerk | Upload documents, assist with data entry |
| Super Admin | Platform administration, law firm tenant onboarding |

### 2.5 Tenant vs Client Model

See **Section 7** for the detailed Multi-Tenancy Architecture diagram.

**Key Concept:**
- **TENANT = Law Firm** (e.g., Sharma & Associates) - Has own branding, users, isolated data, pays for SaaS
- **CLIENTS = Banks** (e.g., HDFC Bank, ICICI Bank) - Served by the law firm tenant
- **END CUSTOMERS = Loan Applicants/Borrowers** - End customers are asked by banks to approach lawyers for opinion

**Data Model:**
- `tenant_id` = Law Firm ID (data isolation)
- `client_id` = Bank ID (filter within tenant)
- `end_customer_id` = Borrower/Loan Applicant ID (end customer who needs opinion)
- Opinion requests tagged with tenant_id, client_id, AND end_customer_id

**Workflow:**
End Customer (Borrower) → Bank Client → Law Firm → Opinion Generation → Notifications to Bank Client + End Customer (configurable)

### 2.6 Document Types Handled
- Property Documents (Sale Deed, Title Deed, Encumbrance Certificate)
- Identity Documents (Aadhaar, PAN, Voter ID)
- Financial Documents (Bank Statements, ITR, Balance Sheets)
- Collateral Documents (Hypothecation Agreement, Mortgage Deed)
- Guarantor Documents (Personal Guarantee, Net Worth Statement)

**Note:** Documents may be in regional languages (not limited to English). The system automatically detects the language, translates to English, and processes them. Final opinions are always generated in English only.

---

## 3. System Overview

### 3.1 High-Level Capabilities

**Core Modules:**
- **Document Management** - Upload, OCR, AI extraction
- **Opinion Workflow** - Request creation, assignment, approval
- **User Management** - Roles, permissions, Keycloak integration
- **Reporting & Analytics** - Metrics, TAT, compliance reports
- **Tenant Branding** - Custom logos, colors per law firm
- **Audit Trail** - All actions logged for compliance
- **Notification Engine** - Email alerts, in-app notifications
- **AI/LLM Processing** - Document extraction, opinion generation

### 3.2 User Journey

1. **End Customer (Borrower)** approaches law firm with bank's referral (e.g., HDFC Bank referred them)
2. **Paralegal/Clerk** creates opinion request for bank client (HDFC) and end customer (borrower)
3. **Paralegal/Clerk** uploads documents (title deeds, agreements, etc.) - may be in regional languages
4. **System** processes documents:
   - OCR extraction (supports regional languages)
   - Translation to English (if documents are in regional language)
   - LLM extracts structured data from English text
5. **Panel Advocate** receives assignment notification
6. **Panel Advocate** reviews extracted data and generates opinion draft using AI:
   - Uses bank client-specific opinion template
   - Generates opinion in English only
7. **Panel Advocate** edits and submits the final opinion
8. **System** notifies opinion completion (both configurable per bank client):
   - Bank client (notified if bank_client.notify_bank_on_completion = true)
   - End customer (notified if bank_client.notify_end_customer_on_completion = true)

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

![3-Tier Architecture](ui-mockups/arch-01-three-tier.png?v=5)

### 5.2 Deployment Flexibility

![Deployment Architecture](ui-mockups/arch-04-deployment.png?v=5)

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
| **OCR** | Tesseract / AWS Textract | Text extraction from images (supports regional languages) |
| **Translation** | LLM API (GPT-4/Claude) | Regional language to English translation |
| **Email** | AWS SES / SendGrid | Notifications |
| **Container Runtime** | Docker | Containerization |
| **Orchestration (Prod)** | Kubernetes (EKS) | Production scaling |
| **IaC** | Terraform | AWS infrastructure |
| **CI/CD** | GitHub Actions | Build, test, deploy |

---

## 6. Component Design

### 6.1 Frontend Application (React SPA)

**Key Features:**
- **Keycloak Integration** - Uses `keycloak-js` adapter for authentication
- **Role-Based UI Routing** - Route guards control screen access based on JWT token roles
- **Tenant Branding** - Dynamic logo, colors, and favicon loaded per tenant
- **Token Management** - Auto-refresh tokens, include in API requests

**Key UI Screens** (See [Section 12: UI Screens](#12-ui-screens) for mockups):
- Login Page (with tenant branding)
- Dashboard (role-based widgets)
- Opinion Requests List
- Opinion Request Detail
- Document Upload
- Document Viewer
- Opinion Editor (with AI assist)
- User Management (firm_admin only)
- Tenant Settings (firm_admin only)
- Reports & Audit Logs

**Access Control:** See [Section 8.5](#85-single-token-for-ui--api-access-control) for details on how the same JWT token controls both UI visibility and API access.

### 6.2 Backend API (Centralized, Multi-Tenant)

**Key Features:**
- **Keycloak Integration** - Uses `@nestjs/keycloak-connect` (NestJS) or `python-keycloak` (FastAPI) for token validation
- **Role-Based API Guards** - Endpoints protected by role-based access control using JWT token claims
- **Tenant Middleware** - Automatically extracts `tenant_id` from JWT and filters all queries
- **Token Validation** - Validates JWT signature using Keycloak public keys (JWKS)

The backend follows a modular structure with tenant middleware that automatically filters all data by `tenant_id`.

**Access Control:** See [Section 8.5](#85-single-token-for-ui--api-access-control) for details on how the same JWT token controls both UI visibility and API access.

### 6.3 Tenant Middleware (Core Multi-Tenancy Logic)

The tenant middleware extracts `tenant_id` from the JWT token and automatically filters all database queries.

---

## 7. Multi-Tenancy Strategy

### 7.1 Tenant Isolation Model

![Multi-Tenancy Architecture](ui-mockups/arch-02-multi-tenancy.png?v=5)

**Approach: Shared Database, Shared Schema with tenant_id Column**

**Key Concept: Law Firms are Tenants, Banks are Clients within a Tenant**

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

![Authentication Flow with Keycloak](ui-mockups/arch-05-auth-flow.png?v=5)

**Identity Brokering Support:** Keycloak can act as an identity broker, allowing law firms to use their existing identity providers (IdPs) for authentication. This enables:
- **SSO Integration** - Law firms can connect their Azure AD, Okta, Google Workspace, or other enterprise IdPs
- **Federation** - Users authenticate with their law firm's IdP, and Keycloak issues JWT tokens for the SaaS app
- **Protocol Support** - Supports SAML 2.0, OpenID Connect (OIDC), and LDAP/Active Directory
- **Seamless Experience** - Your SaaS app continues to validate Keycloak JWT tokens (same flow), while users authenticate via their firm's IdP

This allows law firms to maintain centralized user management in their existing systems while accessing the SaaS platform.

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

The authentication flow uses OAuth 2.0 with PKCE for the frontend SPA. See the visual diagram above for the complete flow.

**Note:** When a law firm has configured identity brokering with their IdP (e.g., Azure AD, Okta), the flow extends to: User → Law Firm IdP → Keycloak (broker) → SaaS App. Keycloak still issues the JWT token, ensuring your app's authentication logic remains unchanged.

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

### 8.5 Single Token for UI & API Access Control

**Key Concept:** The same JWT token obtained from Keycloak (via auth code exchange) is used for **both frontend UI routing** and **backend API authorization**. This enables centralized role-based access control.

#### Frontend Integration (UI Screen Visibility)

The frontend uses the JWT token to:
1. **Decode token** to extract user roles and permissions
2. **Route Guards** - Control which screens/routes are accessible
3. **Component Rendering** - Show/hide UI elements based on roles
4. **API Calls** - Include token in Authorization header

```typescript
// Frontend: React Route Guard Example
import { useKeycloak } from '@react-keycloak/web';

const ProtectedRoute = ({ children, requiredRoles }) => {
  const { keycloak, initialized } = useKeycloak();
  
  if (!initialized) return <Loading />;
  
  if (!keycloak.authenticated) {
    keycloak.login();
    return null;
  }
  
  // Extract roles from token
  const tokenParsed = keycloak.tokenParsed;
  const userRoles = tokenParsed?.realm_access?.roles || [];
  
  // Check if user has required role
  const hasAccess = requiredRoles.some(role => userRoles.includes(role));
  
  if (!hasAccess) {
    return <AccessDenied />;
  }
  
  return children;
};

// Usage in Routes
<Route path="/opinions" element={
  <ProtectedRoute requiredRoles={['panel_advocate', 'senior_advocate']}>
    <OpinionEditor />
  </ProtectedRoute>
} />

<Route path="/users" element={
  <ProtectedRoute requiredRoles={['firm_admin']}>
    <UserManagement />
  </ProtectedRoute>
} />
```

```typescript
// Frontend: Conditional UI Rendering
const Dashboard = () => {
  const { keycloak } = useKeycloak();
  const userRoles = keycloak.tokenParsed?.realm_access?.roles || [];
  
  return (
    <div>
      {userRoles.includes('panel_advocate') && (
        <Card title="My Assigned Requests">
          <OpinionRequestsList />
        </Card>
      )}
      
      {userRoles.includes('firm_admin') && (
        <Card title="User Management">
          <UserManagement />
        </Card>
      )}
      
      {userRoles.includes('firm_admin') && (
        <Card title="Tenant Settings">
          <TenantSettings />
        </Card>
      )}
    </div>
  );
};
```

#### Backend Integration (API Authorization)

The backend validates the same JWT token to:
1. **Token Validation** - Verify signature and expiration
2. **Role Extraction** - Extract roles from token claims
3. **API Endpoint Guards** - Enforce access control per endpoint
4. **Tenant Isolation** - Extract tenant_id for data filtering

```typescript
// Backend: NestJS Role-Based Guard
import { Injectable, CanActivate, ExecutionContext } from '@nestjs/common';
import { Reflector } from '@nestjs/core';
import { KeycloakService } from '@nestjs/keycloak-connect';

@Injectable()
export class RolesGuard implements CanActivate {
  constructor(
    private reflector: Reflector,
    private keycloakService: KeycloakService
  ) {}

  canActivate(context: ExecutionContext): boolean {
    const requiredRoles = this.reflector.get<string[]>(
      'roles',
      context.getHandler()
    );
    
    if (!requiredRoles) {
      return true; // No role requirement
    }
    
    const request = context.switchToHttp().getRequest();
    const user = request.user; // Set by AuthGuard after token validation
    
    // Extract roles from validated token
    const userRoles = user.realm_access?.roles || [];
    
    // Check if user has at least one required role
    return requiredRoles.some(role => userRoles.includes(role));
  }
}

// Usage in Controllers
@Controller('opinions')
export class OpinionController {
  
  @Get()
  @UseGuards(AuthGuard, RolesGuard)
  @Roles('panel_advocate', 'senior_advocate', 'firm_admin')
  async getOpinions(@Req() req) {
    const tenantId = req.user.tenant_id; // From token
    return this.opinionService.findAll(tenantId);
  }
  
  @Post()
  @UseGuards(AuthGuard, RolesGuard)
  @Roles('panel_advocate', 'senior_advocate')
  async createOpinion(@Req() req, @Body() dto: CreateOpinionDto) {
    const tenantId = req.user.tenant_id;
    const userId = req.user.sub;
    return this.opinionService.create(tenantId, userId, dto);
  }
  
  @Delete(':id')
  @UseGuards(AuthGuard, RolesGuard)
  @Roles('firm_admin', 'senior_advocate')
  async deleteOpinion(@Req() req, @Param('id') id: string) {
    const tenantId = req.user.tenant_id;
    return this.opinionService.delete(tenantId, id);
  }
  
  @Get('/users')
  @UseGuards(AuthGuard, RolesGuard)
  @Roles('firm_admin') // Only firm admin can access
  async getUsers(@Req() req) {
    const tenantId = req.user.tenant_id;
    return this.userService.findAll(tenantId);
  }
}
```

#### Role-to-Permission Mapping

| Role | UI Screens Accessible | API Endpoints Allowed |
|------|----------------------|----------------------|
| **firm_admin** | All screens (Dashboard, Users, Settings, Reports, Opinions) | All APIs (GET/POST/PUT/DELETE on all resources) |
| **senior_advocate** | Dashboard, Opinion Requests, Opinion Editor, Reports | GET opinions, POST opinions, PUT opinions, GET reports |
| **panel_advocate** | Dashboard, Opinion Requests, Opinion Editor | GET opinions, POST opinions (create only), GET documents |
| **paralegal** | Dashboard, Opinion Requests (view only), Document Upload | GET opinion_requests, POST documents, GET documents |

#### Token Refresh Flow

Both frontend and backend handle token refresh:

```typescript
// Frontend: Auto token refresh
keycloak.onTokenExpired = () => {
  keycloak.updateToken(30) // Refresh if expires in 30 seconds
    .then((refreshed) => {
      if (refreshed) {
        console.log('Token refreshed');
        // Update axios interceptor with new token
        axios.defaults.headers.common['Authorization'] = 
          `Bearer ${keycloak.token}`;
      }
    })
    .catch(() => {
      // Refresh failed, redirect to login
      keycloak.login();
    });
};
```

```typescript
// Backend: Token validation with refresh token support
@Injectable()
export class AuthGuard implements CanActivate {
  async canActivate(context: ExecutionContext): Promise<boolean> {
    const request = context.switchToHttp().getRequest();
    const token = this.extractTokenFromHeader(request);
    
    if (!token) {
      throw new UnauthorizedException();
    }
    
    try {
      // Validate token with Keycloak public key
      const payload = await this.keycloakService.validateToken(token);
      request.user = payload; // Attach user info to request
      return true;
    } catch (error) {
      // Token expired or invalid
      throw new UnauthorizedException('Invalid or expired token');
    }
  }
}
```

#### Benefits of Single Token Approach

1. **Consistency** - Same roles/permissions enforced on both UI and API
2. **Security** - Backend always validates, even if frontend is compromised
3. **Simplicity** - Single source of truth (Keycloak) for access control
4. **Performance** - No additional permission checks needed, roles in token
5. **Auditability** - All access decisions traceable via JWT claims

---

## 9. Data Architecture

### 9.1 Entity Relationship Overview

**Key Tables:** All tables include a `tenant_id` column for multi-tenancy isolation.
- **tenants** - Law firm configuration and branding
- **bank_clients** - Banks served by each law firm (with opinion templates and notification settings)
- **end_customers** - Loan applicants/borrowers (end customers referred by banks)
- **users** - User accounts linked to Keycloak
- **opinion_requests** - Opinion requests linking bank client, end customer, and law firm
- **documents** - Uploaded documents with OCR data (supports regional languages)
- **opinion_templates** - Bank client-specific opinion format templates
- **opinions** - Generated legal opinions (always in English)
- **audit_logs** - Activity tracking for compliance

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
    -- Opinion Template Configuration
    default_template_id UUID REFERENCES opinion_templates(id),
    -- Notification Settings (all configurable per bank)
    notify_bank_on_completion BOOLEAN DEFAULT false,  -- Configurable: notify bank client
    notify_end_customer_on_completion BOOLEAN DEFAULT false,  -- Configurable: notify end customer
    end_customer_notification_email_template TEXT,
    bank_notification_email_template TEXT,
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(tenant_id, code)
);

-- End Customers Table (Loan Applicants/Borrowers)
CREATE TABLE end_customers (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    tenant_id UUID NOT NULL REFERENCES tenants(id),  -- Law Firm
    client_id UUID NOT NULL REFERENCES bank_clients(id),  -- Bank that referred them
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255),
    phone VARCHAR(20),
    pan_number VARCHAR(10),
    aadhaar_number VARCHAR(12),
    address TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(tenant_id, client_id, email)
);

-- Opinion Templates Table (Bank Client-Specific Formats)
CREATE TABLE opinion_templates (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    tenant_id UUID NOT NULL REFERENCES tenants(id),  -- Law Firm
    client_id UUID NOT NULL REFERENCES bank_clients(id),  -- Bank Client
    name VARCHAR(255) NOT NULL,
    description TEXT,
    template_content JSONB NOT NULL,  -- Structured template with sections, placeholders
    loan_type VARCHAR(50),  -- Optional: specific to loan type
    is_default BOOLEAN DEFAULT false,
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(tenant_id, client_id, name)
);

-- Opinion Requests Table
CREATE TABLE opinion_requests (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    tenant_id UUID NOT NULL REFERENCES tenants(id),    -- Law Firm
    client_id UUID NOT NULL REFERENCES bank_clients(id), -- Bank Client
    end_customer_id UUID NOT NULL REFERENCES end_customers(id), -- End Customer (Borrower)
    reference_number VARCHAR(50) NOT NULL,
    loan_type VARCHAR(50) NOT NULL,
    loan_amount DECIMAL(15,2),
    property_location TEXT,
    branch_code VARCHAR(20),
    template_id UUID REFERENCES opinion_templates(id),  -- Bank client-specific template
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
    -- Regional Language Support
    detected_language VARCHAR(10),  -- e.g., 'en', 'te', 'hi', 'ta', etc.
    original_language_text TEXT,    -- OCR text in original language
    translated_text TEXT,           -- Translated to English
    translation_status VARCHAR(20) DEFAULT 'PENDING',  -- PENDING, COMPLETED, FAILED
    -- OCR and Extraction
    ocr_status VARCHAR(20) DEFAULT 'PENDING',
    ocr_text TEXT,                   -- Final English text used for extraction
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

![LLM Integration Architecture](ui-mockups/arch-03-llm-integration.png?v=5)

**Key Features:**
- **Regional Language Support**: Documents in regional languages are OCR'd, detected, translated to English, then processed
- **Bank Client Templates**: Each bank client has their own opinion format template (configured per tenant + client)
- **English-Only Opinions**: Final opinions are always generated in English, regardless of source document language
- **Configurable Notifications**: Opinion completion notifications sent to bank client and/or end customer (both configurable per bank)

### 10.2 AI Workflow: Automated vs Manual Steps

**Key Question: Are opinions generated automatically?**

**Answer:** The workflow uses a **human-in-the-loop** approach with selective automation:

#### Automated Steps (Triggered on Document Upload)

1. **Document Upload** → User uploads documents for an opinion request (may be in regional languages)
2. **OCR Processing** → Automatic text extraction from PDFs/images (Tesseract/Textract)
   - Supports regional languages (not limited to English)
   - Extracts text in original document language
3. **Language Detection** → Automatic detection of document language
   - Detects if document is in English or regional language
   - Stores language code (e.g., 'en', 'te', 'hi', 'ta', etc.)
4. **Translation to English** → Automatic translation (if document is not in English)
   - Uses LLM API to translate regional language text to English
   - Preserves legal terms, names, addresses, and numbers exactly
   - Stores both original_language_text and translated_text
5. **AI Data Extraction** → Automatic structured data extraction using LLM (from English text):
   - Property address, survey number
   - Party names (buyer, seller, mortgagor)
   - Registration dates, document numbers
   - Property area, valuation
   - Encumbrances, if any
6. **Data Storage** → Extracted data saved to database with confidence scores
   - Stores: original_language_text, translated_text, detected_language, extracted_data

#### Optional: Auto-Generate Draft Opinion (Configurable)

**Option A: Manual Trigger (Default)**
- Lawyer clicks "Generate Draft Opinion" button in Opinion Editor
- AI generates draft opinion using:
  - Extracted data (from English text)
  - Bank client-specific opinion template (configured per bank)
- Opinion always generated in English only
- Lawyer reviews, edits, and submits

**Option B: Automatic Draft Generation (Configurable per Tenant)**
- After all documents are uploaded and extraction is complete
- System automatically triggers opinion draft generation
- Uses bank client's default opinion template
- Draft opinion saved with `status: 'DRAFT'` and `ai_generated: true`
- Opinion always in English
- Lawyer receives notification to review the draft
- Lawyer opens Opinion Editor, reviews AI-generated content, edits as needed, and submits

#### Always Manual: Review & Approval

- **Lawyer Review** → Always required (human-in-the-loop)
- **Edit & Refine** → Lawyer can modify AI-generated content
- **Final Approval** → Lawyer must explicitly submit/approve the opinion
- **Digital Signature** → Lawyer signs the final opinion

#### Complete Workflow Example

```
1. End Customer (Borrower) approaches law firm (referred by Bank Client - HDFC)
2. Paralegal creates opinion request for Bank Client (HDFC) and End Customer
   └─ Status: PENDING
   └─ Links: tenant_id (Law Firm), client_id (HDFC Bank), end_customer_id (Borrower)

3. Paralegal uploads documents (Title Deed, Sale Agreement, etc.)
   └─ Documents may be in regional languages (not English)
   └─ Status: DOCUMENTS_UPLOADED
   
4. [AUTOMATIC] System processes documents:
   ├─ OCR extracts text from images/PDFs (supports regional languages)
   ├─ Language Detection: Detects document language (e.g., regional language)
   ├─ Translation to English: If not English, translates to English using LLM
   ├─ LLM extracts structured data from English text (property, parties, dates)
   └─ Extracted data saved with confidence scores
   └─ Status: EXTRACTION_COMPLETE
   └─ Database: Stores both original_language_text and translated_text

5. [AUTOMATIC or MANUAL] Opinion Draft Generation:
   ├─ System loads bank client-specific opinion template
   │   └─ Each bank client has their own opinion format (configured in opinion_templates table)
   │   └─ Template selected from: request.template_id OR bank_client.default_template_id
   ├─ If auto-generate enabled: System generates draft opinion
   ├─ If manual: Lawyer clicks "Generate Draft" button
   └─ AI creates opinion using:
   │   ├─ Extracted data (from English text)
   │   ├─ Bank client-specific template structure
   │   └─ Always generates in English only (final opinion language)
   └─ Status: DRAFT (ai_generated: true)

6. [MANUAL] Lawyer receives notification/assignment
   └─ Opens Opinion Editor

7. [MANUAL] Lawyer reviews:
   ├─ Reviews extracted data (can verify/correct)
   ├─ Reviews AI-generated opinion draft (in English)
   ├─ Edits content as needed
   └─ Adds any additional legal analysis

8. [MANUAL] Lawyer submits final opinion (always in English)
   └─ Status: COMPLETED
   └─ ai_generated: true, lawyer_reviewed: true, lawyer_approved: true

9. [AUTOMATIC] System sends notifications (configurable per bank client):
   ├─ Bank Client (HDFC) - Conditionally notified
   │   └─ Notified if: bank_client.notify_bank_on_completion = true
   │   └─ Uses: bank_client.bank_notification_email_template
   └─ End Customer (Borrower) - Conditionally notified
   │   └─ Notified if: bank_client.notify_end_customer_on_completion = true
   │   └─ Uses: bank_client.end_customer_notification_email_template
   └─ Opinion ready for download (always in English)
```

#### Configuration Options

**Tenant Settings (per Law Firm):**
```json
{
  "ai_settings": {
    "auto_extract_on_upload": true,        // Always automatic
    "auto_generate_opinion_draft": false,  // Default: manual trigger
    "require_lawyer_review": true,         // Always required
    "min_confidence_score": 0.85,          // Threshold for auto-extraction
    "supported_regional_languages": ["te", "hi", "ta", "kn", "ml", "mr", "gu", "or", "pa", "bn", "as"],  // Generic regional language codes
    "default_translation_target": "en"     // Always translate to English
  }
}
```

**Bank Client Settings (per Bank):**
```json
{
  "client_id": "hdfc-bank-uuid",
  "default_template_id": "hdfc-template-uuid",  // Bank-specific opinion format
  "notify_bank_on_completion": false,            // Configurable: notify bank client (default: false)
  "notify_end_customer_on_completion": false,     // Configurable: notify end customer (default: false)
  "opinion_language": "en"                      // Always English for final opinion
}
```

#### Benefits of Human-in-the-Loop

1. **Legal Accuracy** - Lawyer ensures correctness before submission
2. **Compliance** - Final opinion is lawyer-approved, not AI-only
3. **Flexibility** - Lawyer can override AI suggestions
4. **Quality Control** - Human review catches edge cases
5. **Liability** - Lawyer takes responsibility for final opinion

#### API Endpoints

```typescript
// Automatic processing (triggered on document upload)
// Handles: OCR → Language Detection → Translation → Extraction
POST /api/v1/documents/{id}/process
→ Returns: ExtractionResult with structured data
→ Stores: original_language_text, translated_text, detected_language

// Manual opinion draft generation (uses bank client template)
POST /api/v1/opinions/{id}/generate-draft
→ Returns: OpinionDraft (AI-generated content in English)
→ Uses: Bank client-specific template from opinion_requests.template_id

// Lawyer review and submission
PUT /api/v1/opinions/{id}
Body: { content: "...", lawyer_reviewed: true, status: "COMPLETED" }

// Notification on opinion completion
POST /api/v1/opinions/{id}/notify
→ Sends notifications to (both configurable per bank client):
   - Bank client (if bank_client.notify_bank_on_completion = true)
   - End customer (if bank_client.notify_end_customer_on_completion = true)
```

#### Notification Flow

```typescript
// Notification service
async notifyOpinionCompletion(opinionId: string) {
  const opinion = await this.opinionService.findById(opinionId);
  const request = await this.requestService.findById(opinion.loan_request_id);
  const bankClient = await this.clientService.findById(request.client_id);
  const endCustomer = await this.endCustomerService.findById(request.end_customer_id);
  
  // Conditionally notify bank client (configurable per bank)
  if (bankClient.notify_bank_on_completion && bankClient.contact_email) {
    await this.emailService.send({
      to: bankClient.contact_email,
      template: bankClient.bank_notification_email_template,
      data: { opinion, request, bankClient }
    });
  }
  
  // Conditionally notify end customer (configurable per bank)
  if (bankClient.notify_end_customer_on_completion && endCustomer.email) {
    await this.emailService.send({
      to: endCustomer.email,
      template: bankClient.end_customer_notification_email_template,
      data: { opinion, request, endCustomer }
    });
  }
}
```

### 10.3 AI Service Implementation

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
  
  /**
   * Process document with regional language support
   * 1. OCR extraction (supports regional languages)
   * 2. Language detection
   * 3. Translation to English (if needed)
   * 4. Data extraction from English text
   */
  async processDocument(
    documentId: string,
    documentType: string
  ): Promise<ExtractionResult> {
    
    // Step 1: OCR Extraction (supports regional languages)
    const ocrResult = await this.ocrService.extract(documentId);
    // ocrResult.text contains text in original language
    // ocrResult.language contains detected language code
    
    // Step 2: Language Detection
    const detectedLanguage = await this.detectLanguage(ocrResult.text);
    
    // Step 3: Translation to English (if not already English)
    let englishText = ocrResult.text;
    if (detectedLanguage !== 'en') {
      englishText = await this.translateToEnglish(
        ocrResult.text, 
        detectedLanguage
      );
    }
    
    // Step 4: Extract structured data from English text
    return await this.extractDocumentData(
      documentId,
      englishText,
      documentType,
      ocrResult.text,  // Store original language text
      detectedLanguage
    );
  }
  
  async detectLanguage(text: string): Promise<string> {
    // Use LLM or language detection library
    const response = await this.llmService.detectLanguage(text);
    return response.language; // Returns ISO 639-1 code (e.g., 'en', 'te', 'hi')
  }
  
  async translateToEnglish(
    text: string, 
    sourceLanguage: string
  ): Promise<string> {
    const prompt = `Translate the following text from ${sourceLanguage} to English. 
    Preserve all legal terms, names, addresses, and numbers exactly as they appear.
    Text: ${text}`;
    
    const response = await this.llmService.translate(prompt);
    return response.translatedText;
  }
  
  async extractDocumentData(
    documentId: string, 
    englishText: string, 
    documentType: string,
    originalText?: string,
    detectedLanguage?: string
  ): Promise<ExtractionResult> {
    
    const prompt = this.buildExtractionPrompt(documentType, englishText);
    
    const response = await this.llmService.create({
      model: 'gpt-4-turbo-preview',
      messages: [
        { role: 'system', content: EXTRACTION_SYSTEM_PROMPT },
        { role: 'user', content: prompt }
      ],
      response_format: { type: 'json_object' },
      temperature: 0.2
    });
    
    const result = JSON.parse(response.choices[0].message.content);
    
    // Store both original and translated text in database
    await this.documentService.update(documentId, {
      detected_language: detectedLanguage,
      original_language_text: originalText,
      translated_text: englishText,
      ocr_text: englishText,  // Use English for extraction
      translation_status: detectedLanguage !== 'en' ? 'COMPLETED' : 'N/A'
    });
    
    return result;
  }
  
  /**
   * Generate opinion draft using bank client-specific template
   * Always generates in English only
   */
  async generateOpinionDraft(
    requestId: string,
    extractedData: Record<string, any>,
    clientId: string  // Bank client ID to get template
  ): Promise<OpinionDraft> {
    
    // Load bank client-specific opinion template
    const request = await this.requestService.findById(requestId);
    const templateId = request.template_id || 
      await this.getDefaultTemplateForClient(clientId);
    
    const template = await this.templateService.findById(templateId);
    
    // Build prompt with template structure
    const prompt = this.buildGenerationPrompt(
      extractedData, 
      template,
      'en'  // Always generate in English
    );
    
    const response = await this.llmService.create({
      model: 'gpt-4-turbo-preview',
      messages: [
        { 
          role: 'system', 
          content: `You are a legal expert. Generate legal opinions in English only. 
          Follow the provided template structure exactly. 
          ${GENERATION_SYSTEM_PROMPT}` 
        },
        { role: 'user', content: prompt }
      ],
      response_format: { type: 'json_object' },
      temperature: 0.4
    });
    
    return JSON.parse(response.choices[0].message.content);
  }
  
  async getDefaultTemplateForClient(clientId: string): Promise<string> {
    const client = await this.clientService.findById(clientId);
    return client.default_template_id;
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

![Deployment Architecture](ui-mockups/arch-04-deployment.png?v=5)

---

## 12. UI Screens

### 12.1 Screen Inventory

The following UI screens are required for the application. Mock HTML files and screenshots are available in the `ui-mockups/` folder.

---

#### 12.1.1 Login Page
**File:** [ui-mockups/01-login.html](ui-mockups/01-login.html)

Tenant-branded login page with Keycloak SSO integration. Branding (logo, colors) loaded from tenant config.

![Login Page](ui-mockups/01-login.png?v=5)

---

#### 12.1.2 Dashboard
**File:** [ui-mockups/02-dashboard.html](ui-mockups/02-dashboard.html)

Overview with key metrics, pending tasks, and recent activity.

![Dashboard](ui-mockups/02-dashboard.png?v=5)

---

#### 12.1.3 Opinion Requests List
**File:** [ui-mockups/03-opinion-requests.html](ui-mockups/03-opinion-requests.html)

List of all loan requests with search, filters (status, loan type, priority), and pagination.

![Opinion Requests List](ui-mockups/03-opinion-requests.png?v=5)

---

#### 12.1.4 Opinion Request Detail
**File:** [ui-mockups/04-opinion-request-detail.html](ui-mockups/04-opinion-request-detail.html)

Single request view with end customer (borrower) details, bank client information, documents list, quick actions, and activity timeline.

**AI Workflow Alignment:**
- Shows document extraction status (✓ AI Extracted / Pending Review)
- Displays "Generate Draft Opinion" button (manual trigger for AI draft generation)
- Activity timeline shows AI extraction completion events
- Quick action to view extracted data

![Opinion Request Detail](ui-mockups/04-opinion-request-detail.png?v=5)

---

#### 12.1.5 Document Upload
**File:** [ui-mockups/05-document-upload.html](ui-mockups/05-document-upload.html)

Drag-drop document upload with category selection and AI processing status.

**AI Workflow Alignment:**
- Shows real-time AI processing status (🤖 AI Processing... / ✓ Ready)
- Displays notification that documents will be automatically processed by AI
- File list shows extraction status per document
- Aligned with **Step 2-3** of AI workflow (Upload → Automatic OCR + Extraction)

![Document Upload](ui-mockups/05-document-upload.png?v=5)

---

#### 12.1.6 Document Viewer
**File:** [ui-mockups/06-document-viewer.html](ui-mockups/06-document-viewer.html)

View document with AI-extracted data panel showing property details, party information, and confidence scores.

**AI Workflow Alignment:**
- Displays extracted structured data (property, parties, dates, amounts)
- Shows confidence scores for extracted fields
- Allows lawyer to verify/correct extracted data
- Aligned with **Step 3** of AI workflow (Review Extracted Data)

![Document Viewer](ui-mockups/06-document-viewer.png?v=5)

---

#### 12.1.7 Opinion Editor
**File:** [ui-mockups/07-opinion-editor.html](ui-mockups/07-opinion-editor.html)

Create/edit legal opinion with AI assistance. Features document summary, rich text editor, and AI suggestions.

**AI Workflow Alignment:**
- **"🤖 AI Generate" button** - Manual trigger for draft opinion generation (Option A workflow)
- **AI-generated content sections** - Highlighted sections showing AI-generated text
- **AI Assistant panel** - Provides suggestions, missing information alerts, and prompt-based generation
- **"Generate full draft" quick action** - Alternative way to trigger complete draft generation
- **Document summary panel** - Shows extracted data for reference while editing
- Aligned with **Step 4-6** of AI workflow (Generate Draft → Review → Edit → Submit)

**Note:** If auto-generate is enabled (Option B), the draft would be pre-populated when lawyer opens this screen.

![Opinion Editor](ui-mockups/07-opinion-editor.png?v=5)

---

#### 12.1.8 User Management
**File:** [ui-mockups/08-user-management.html](ui-mockups/08-user-management.html)

Admin screen for user CRUD operations with role management.

![User Management](ui-mockups/08-user-management.png?v=5)

---

#### 12.1.9 Tenant Settings
**File:** [ui-mockups/09-tenant-settings.html](ui-mockups/09-tenant-settings.html)

Admin screen for tenant branding (logo, colors), organization info, and feature settings.

![Tenant Settings](ui-mockups/09-tenant-settings.png?v=5)

---

#### 12.1.10 Reports & Analytics
**File:** [ui-mockups/10-reports.html](ui-mockups/10-reports.html)

Analytics dashboard with charts, recent opinions table, and activity feed.

![Reports & Analytics](ui-mockups/10-reports.png?v=5)

---

### 12.2 Screen Flow

**Main User Flow:**
Login → Dashboard → Opinion Requests List → Request Detail → Document Viewer → Opinion Editor

**Document Upload Flow:**
New Request → Document Upload → AI Extraction → Review

**Admin Flow:**
Dashboard → User Management / Tenant Settings / Reports

### 12.3 AI Workflow Screen Mapping

The UI screens are aligned with the AI workflow described in [Section 10.2](#102-ai-workflow-automated-vs-manual-steps):

| Workflow Step | Screen | UI Features |
|---------------|--------|-------------|
| **1. Create Request** | Opinion Requests List → Create New | Create opinion request for bank client |
| **2. Upload Documents** | Document Upload | Drag-drop upload, shows "AI Processing..." status |
| **3. Automatic Extraction** | Document Upload / Opinion Request Detail | Status indicators: "🤖 AI Processing..." → "✓ AI Extracted" |
| **4. Review Extracted Data** | Document Viewer | Shows extracted data panel with confidence scores |
| **5. Generate Draft (Manual)** | Opinion Editor | "🤖 AI Generate" button triggers draft generation |
| **5. Generate Draft (Auto)** | Opinion Editor | Draft pre-populated if auto-generate enabled |
| **6. Review & Edit** | Opinion Editor | AI-generated content highlighted, lawyer can edit |
| **7. Submit Opinion** | Opinion Editor | "Submit" button, requires lawyer approval |

**Key UI Elements Supporting AI Workflow:**
- ✅ Document status badges (Extracted/Pending) in Opinion Request Detail
- ✅ AI processing indicators in Document Upload screen
- ✅ Extracted data display in Document Viewer
- ✅ "Generate Draft Opinion" button in Opinion Request Detail
- ✅ "AI Generate" button in Opinion Editor toolbar
- ✅ AI-generated content highlighting in Opinion Editor
- ✅ AI Assistant panel with suggestions in Opinion Editor
- ✅ Activity timeline showing AI extraction events

---

## 13. CI/CD Pipeline

### 13.1 Repository Structure

The application uses **two separate repositories** with independent CI/CD pipelines:

1. **`legal-ops-ui`** - Frontend React application
2. **`legal-ops-api`** - Backend API (NestJS/FastAPI)

Each repository has its own GitHub Actions workflows with similar logic but repository-specific configurations.

### 13.2 Pipeline Overview

Both repositories follow the same pipeline structure:

**Pipeline 1: PR Validation (dev branch)**
- Triggered on: Pull Request to `dev` branch
- Steps: Lint → SonarQube → Unit Tests
- Outcome: Merge to `dev` branch if all checks pass

**Pipeline 2: Dev Deployment**
- Triggered on: Merge to `dev` branch
- Steps: Build → Docker Image → Push to Registry → Deploy to Dev Environment

**Pipeline 3: Test Deployment**
- Triggered on: Merge from `dev` to `main` branch
- Steps: Build → Docker Image (Semantic Versioning) → Push to Registry → Deploy to Test Environment

### 13.3 Frontend Pipeline (legal-ops-ui)

#### 13.3.1 PR Validation Workflow

```yaml
# .github/workflows/pr-validation.yml
name: PR Validation - UI

on:
  pull_request:
    branches: [dev]
    types: [opened, synchronize, reopened]

jobs:
  lint:
    name: Lint Check
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install Dependencies
        run: npm ci
      
      - name: Run Linter
        run: npm run lint
        # TBD: Configure ESLint rules and fix mode
      
  sonarqube:
    name: SonarQube Analysis
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Shallow clones should be disabled for better analysis
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install Dependencies
        run: npm ci
      
      - name: Run SonarQube
        uses: sonarsource/sonarqube-scan-action@master
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
          SONAR_HOST_URL: ${{ secrets.SONAR_HOST_URL }}
        # TBD: Configure SonarQube project key and quality gates
      
  unit-tests:
    name: Unit Tests
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install Dependencies
        run: npm ci
      
      - name: Run Unit Tests
        run: npm run test:unit
        # TBD: Configure test coverage thresholds
      
      - name: Upload Coverage Reports
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/lcov.info
          # TBD: Configure codecov token
```

#### 13.3.2 Dev Deployment Workflow

```yaml
# .github/workflows/deploy-dev.yml
name: Deploy to Dev - UI

on:
  push:
    branches: [dev]

jobs:
  build-and-deploy:
    name: Build and Deploy to Dev
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install Dependencies
        run: npm ci
      
      - name: Build Application
        run: npm run build
        env:
          REACT_APP_API_URL: ${{ secrets.DEV_API_URL }}
          REACT_APP_KEYCLOAK_URL: ${{ secrets.DEV_KEYCLOAK_URL }}
        # TBD: Configure all environment variables
      
      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ap-south-1
      
      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v2
      
      - name: Build Docker Image
        run: |
          docker build -t legal-ops-ui:dev-${{ github.sha }} .
          docker tag legal-ops-ui:dev-${{ github.sha }} ${{ steps.login-ecr.outputs.registry }}/legal-ops-ui:dev-${{ github.sha }}
          docker tag legal-ops-ui:dev-${{ github.sha }} ${{ steps.login-ecr.outputs.registry }}/legal-ops-ui:dev-latest
        # TBD: Configure Dockerfile path and build args
      
      - name: Push Docker Image to ECR
        run: |
          docker push ${{ steps.login-ecr.outputs.registry }}/legal-ops-ui:dev-${{ github.sha }}
          docker push ${{ steps.login-ecr.outputs.registry }}/legal-ops-ui:dev-latest
      
      - name: Deploy to Dev Environment
        run: |
          # TBD: Configure kubectl context and namespace
          kubectl set image deployment/legal-ops-ui \
            legal-ops-ui=${{ steps.login-ecr.outputs.registry }}/legal-ops-ui:dev-${{ github.sha }} \
            -n legal-opinion-dev
          kubectl rollout status deployment/legal-ops-ui -n legal-opinion-dev
        env:
          KUBECONFIG: ${{ secrets.DEV_KUBECONFIG }}
```

#### 13.3.3 Test Deployment Workflow

```yaml
# .github/workflows/deploy-test.yml
name: Deploy to Test - UI

on:
  push:
    branches: [main]

jobs:
  build-and-deploy:
    name: Build and Deploy to Test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Required for semantic versioning
      
      - name: Generate Semantic Version
        id: version
        uses: mathieudutour/github-tag-action@v6
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          default_bump: patch
        # TBD: Configure version bump strategy (major/minor/patch)
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install Dependencies
        run: npm ci
      
      - name: Build Application
        run: npm run build
        env:
          REACT_APP_API_URL: ${{ secrets.TEST_API_URL }}
          REACT_APP_KEYCLOAK_URL: ${{ secrets.TEST_KEYCLOAK_URL }}
        # TBD: Configure all environment variables
      
      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ap-south-1
      
      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v2
      
      - name: Build Docker Image with Semantic Version
        run: |
          docker build -t legal-ops-ui:${{ steps.version.outputs.new_tag }} .
          docker tag legal-ops-ui:${{ steps.version.outputs.new_tag }} ${{ steps.login-ecr.outputs.registry }}/legal-ops-ui:${{ steps.version.outputs.new_tag }}
          docker tag legal-ops-ui:${{ steps.version.outputs.new_tag }} ${{ steps.login-ecr.outputs.registry }}/legal-ops-ui:test-latest
        # TBD: Configure Dockerfile path and build args
      
      - name: Push Docker Image to ECR
        run: |
          docker push ${{ steps.login-ecr.outputs.registry }}/legal-ops-ui:${{ steps.version.outputs.new_tag }}
          docker push ${{ steps.login-ecr.outputs.registry }}/legal-ops-ui:test-latest
      
      - name: Deploy to Test Environment
        run: |
          # TBD: Configure kubectl context and namespace
          kubectl set image deployment/legal-ops-ui \
            legal-ops-ui=${{ steps.login-ecr.outputs.registry }}/legal-ops-ui:${{ steps.version.outputs.new_tag }} \
            -n legal-opinion-test
          kubectl rollout status deployment/legal-ops-ui -n legal-opinion-test
        env:
          KUBECONFIG: ${{ secrets.TEST_KUBECONFIG }}
```

### 13.4 Backend Pipeline (legal-ops-api)

#### 13.4.1 PR Validation Workflow

```yaml
# .github/workflows/pr-validation.yml
name: PR Validation - API

on:
  pull_request:
    branches: [dev]
    types: [opened, synchronize, reopened]

jobs:
  lint:
    name: Lint Check
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js / Python
        # TBD: Use Node.js for NestJS or Python setup for FastAPI
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
        # OR for Python:
        # uses: actions/setup-python@v4
        # with:
        #   python-version: '3.11'
      
      - name: Install Dependencies
        run: npm ci  # or: pip install -r requirements.txt
      
      - name: Run Linter
        run: npm run lint  # or: pylint, flake8, black --check
        # TBD: Configure linter rules
      
  sonarqube:
    name: SonarQube Analysis
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      
      - name: Setup Node.js / Python
        # TBD: Configure based on backend technology
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install Dependencies
        run: npm ci
      
      - name: Run SonarQube
        uses: sonarsource/sonarqube-scan-action@master
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
          SONAR_HOST_URL: ${{ secrets.SONAR_HOST_URL }}
        # TBD: Configure SonarQube project key and quality gates
      
  unit-tests:
    name: Unit Tests
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js / Python
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install Dependencies
        run: npm ci
      
      - name: Run Unit Tests
        run: npm run test:unit
        # TBD: Configure test coverage thresholds
      
      - name: Upload Coverage Reports
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/lcov.info
          # TBD: Configure codecov token
```

#### 13.4.2 Dev Deployment Workflow

```yaml
# .github/workflows/deploy-dev.yml
name: Deploy to Dev - API

on:
  push:
    branches: [dev]

jobs:
  build-and-deploy:
    name: Build and Deploy to Dev
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js / Python
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install Dependencies
        run: npm ci
      
      - name: Run Tests
        run: npm run test
        # TBD: Configure test suite
      
      - name: Build Application
        run: npm run build
        # TBD: Configure build command and output
      
      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ap-south-1
      
      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v2
      
      - name: Build Docker Image
        run: |
          docker build -t legal-ops-api:dev-${{ github.sha }} .
          docker tag legal-ops-api:dev-${{ github.sha }} ${{ steps.login-ecr.outputs.registry }}/legal-ops-api:dev-${{ github.sha }}
          docker tag legal-ops-api:dev-${{ github.sha }} ${{ steps.login-ecr.outputs.registry }}/legal-ops-api:dev-latest
        # TBD: Configure Dockerfile path and build args
      
      - name: Push Docker Image to ECR
        run: |
          docker push ${{ steps.login-ecr.outputs.registry }}/legal-ops-api:dev-${{ github.sha }}
          docker push ${{ steps.login-ecr.outputs.registry }}/legal-ops-api:dev-latest
      
      - name: Deploy to Dev Environment
        run: |
          # TBD: Configure kubectl context and namespace
          kubectl set image deployment/legal-ops-api \
            legal-ops-api=${{ steps.login-ecr.outputs.registry }}/legal-ops-api:dev-${{ github.sha }} \
            -n legal-opinion-dev
          kubectl rollout status deployment/legal-ops-api -n legal-opinion-dev
        env:
          KUBECONFIG: ${{ secrets.DEV_KUBECONFIG }}
```

#### 13.4.3 Test Deployment Workflow

```yaml
# .github/workflows/deploy-test.yml
name: Deploy to Test - API

on:
  push:
    branches: [main]

jobs:
  build-and-deploy:
    name: Build and Deploy to Test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      
      - name: Generate Semantic Version
        id: version
        uses: mathieudutour/github-tag-action@v6
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          default_bump: patch
        # TBD: Configure version bump strategy
      
      - name: Setup Node.js / Python
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install Dependencies
        run: npm ci
      
      - name: Run Tests
        run: npm run test
      
      - name: Build Application
        run: npm run build
      
      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ap-south-1
      
      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v2
      
      - name: Build Docker Image with Semantic Version
        run: |
          docker build -t legal-ops-api:${{ steps.version.outputs.new_tag }} .
          docker tag legal-ops-api:${{ steps.version.outputs.new_tag }} ${{ steps.login-ecr.outputs.registry }}/legal-ops-api:${{ steps.version.outputs.new_tag }}
          docker tag legal-ops-api:${{ steps.version.outputs.new_tag }} ${{ steps.login-ecr.outputs.registry }}/legal-ops-api:test-latest
        # TBD: Configure Dockerfile path and build args
      
      - name: Push Docker Image to ECR
        run: |
          docker push ${{ steps.login-ecr.outputs.registry }}/legal-ops-api:${{ steps.version.outputs.new_tag }}
          docker push ${{ steps.login-ecr.outputs.registry }}/legal-ops-api:test-latest
      
      - name: Deploy to Test Environment
        run: |
          # TBD: Configure kubectl context and namespace
          kubectl set image deployment/legal-ops-api \
            legal-ops-api=${{ steps.login-ecr.outputs.registry }}/legal-ops-api:${{ steps.version.outputs.new_tag }} \
            -n legal-opinion-test
          kubectl rollout status deployment/legal-ops-api -n legal-opinion-test
        env:
          KUBECONFIG: ${{ secrets.TEST_KUBECONFIG }}
```

### 13.5 Pipeline Flow Summary

```
┌─────────────────────────────────────────────────────────────┐
│                    PIPELINE FLOW DIAGRAM                      │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  REPOSITORY: legal-ops-ui OR legal-ops-api                  │
│                                                              │
│  1. PR to dev branch                                         │
│     └─> PR Validation Pipeline                               │
│         ├─> Lint Check                                       │
│         ├─> SonarQube Analysis                               │
│         └─> Unit Tests                                        │
│         └─> ✅ All Pass → Merge to dev                       │
│                                                              │
│  2. Merge to dev branch                                      │
│     └─> Dev Deployment Pipeline                              │
│         ├─> Build Application                                │
│         ├─> Build Docker Image (dev-{sha})                   │
│         ├─> Push to ECR                                       │
│         └─> Deploy to Dev Environment                        │
│                                                              │
│  3. Merge dev → main branch                                  │
│     └─> Test Deployment Pipeline                             │
│         ├─> Generate Semantic Version (v1.2.3)               │
│         ├─> Build Application                                │
│         ├─> Build Docker Image (v1.2.3)                     │
│         ├─> Push to ECR                                       │
│         └─> Deploy to Test Environment                      │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### 13.6 Required GitHub Secrets

**For both repositories:**
- `AWS_ACCESS_KEY_ID` - AWS credentials for ECR access
- `AWS_SECRET_ACCESS_KEY` - AWS credentials for ECR access
- `SONAR_TOKEN` - SonarQube authentication token
- `SONAR_HOST_URL` - SonarQube server URL
- `DEV_KUBECONFIG` - Kubernetes config for dev environment
- `TEST_KUBECONFIG` - Kubernetes config for test environment

**Repository-specific:**
- `DEV_API_URL` / `TEST_API_URL` - API endpoint URLs (for UI repo)
- `DEV_KEYCLOAK_URL` / `TEST_KEYCLOAK_URL` - Keycloak URLs (for UI repo)

### 13.7 Configuration Notes (TBD)

All workflows include `# TBD` comments indicating areas that need configuration:

- **Linter Rules** - ESLint/Pylint configuration and rules
- **SonarQube** - Project keys, quality gates, analysis parameters
- **Test Coverage** - Coverage thresholds and reporting
- **Build Configuration** - Build commands, environment variables, build args
- **Dockerfile** - Dockerfile paths and optimization
- **Kubernetes** - Namespace, deployment names, rollout strategies
- **Semantic Versioning** - Version bump strategy (major/minor/patch)
- **Environment Variables** - All required env vars for each environment

These configurations will be finalized during implementation based on specific technology choices and infrastructure setup.

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
