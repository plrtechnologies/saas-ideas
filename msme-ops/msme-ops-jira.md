# MiniERP SaaS - JIRA Epics, Stories & Tasks

**Project:** MiniERP SaaS Platform for Local Businesses (India-first)  
**Document Version:** 1.0  
**Date:** January 2026  
**Based on:** HLD-MiniERP-SaaS.md v1.0

---

## JIRA Structure Overview

This document contains:
- **Epics** - High-level feature areas (17 epics)
- **Stories** - User-facing features within epics (~70+ stories)
- **Tasks** - Technical implementation details (~250+ tasks)

**Estimation Guidelines:**
- Story Points: 1 (XS), 2 (S), 3 (M), 5 (L), 8 (XL), 13 (XXL)
- Priority: Critical, High, Medium, Low
- Labels: `backend`, `frontend`, `devops`, `security`, `testing`, `gst`, `inventory`, `jobs`

---

## EPIC 1: Infrastructure & DevOps Foundation

**Epic Description:** Set up development and production infrastructure, containerization, and CI/CD pipelines for both UI and API repositories.

**Epic Goal:** Enable development team to build, test, and deploy the application efficiently.

**Acceptance Criteria:**
- Docker Compose setup working for local development
- Two separate GitHub repositories (minierp-ui, minierp-api) or mono-repo structure
- CI/CD pipelines configured for both repositories
- Production-ready deployment manifests (VM/Compose or Kubernetes)

---

### Story 1.1: Development Environment Setup (Docker Compose)

**Story Description:** Create Docker Compose configuration for local development with all required services.

**Story Points:** 5  
**Priority:** Critical  
**Labels:** `devops`, `infrastructure`

**Acceptance Criteria:**
- Docker Compose file includes all services (frontend, backend, keycloak, postgres, redis, minio)
- All services can start with single command
- Services are properly networked and can communicate
- Environment variables are configurable via .env file
- Health checks implemented for all services

**Tasks:**
1. **Task 1.1.1:** Create docker-compose.yml file with service definitions
   - Define frontend service (React app)
   - Define backend service (NestJS/FastAPI)
   - Define Keycloak service with PostgreSQL backend
   - Define PostgreSQL database service
   - Define Redis cache service (optional)
   - Define MinIO S3-compatible storage service
   - **Estimate:** 3 hours

2. **Task 1.1.2:** Configure service networking and dependencies
   - Set up service dependencies (depends_on)
   - Configure internal networking
   - Set up service aliases for DNS resolution
   - **Estimate:** 2 hours

3. **Task 1.1.3:** Create environment variable configuration
   - Create .env.example file with all required variables
   - Document all environment variables
   - Set up default values for development
   - **Estimate:** 2 hours

4. **Task 1.1.4:** Add health checks for all services
   - Implement health check endpoints
   - Configure Docker healthcheck directives
   - Test service startup and health verification
   - **Estimate:** 3 hours

5. **Task 1.1.5:** Create initialization scripts
   - Database initialization SQL scripts
   - Keycloak realm configuration scripts
   - MinIO bucket creation scripts
   - **Estimate:** 4 hours

6. **Task 1.1.6:** Document local development setup
   - Write README with setup instructions
   - Document common issues and troubleshooting
   - Create quick start guide
   - **Estimate:** 2 hours

---

### Story 1.2: Repository Setup & Structure

**Story Description:** Create and configure GitHub repositories with proper structure and initial setup.

**Story Points:** 3  
**Priority:** Critical  
**Labels:** `devops`, `repository`

**Acceptance Criteria:**
- Repositories created (minierp-ui, minierp-api or mono-repo)
- Repository structure follows best practices
- .gitignore files configured appropriately
- README files with project overview
- Branch protection rules configured

**Tasks:**
1. **Task 1.2.1:** Create minierp-ui repository (or frontend folder in mono-repo)
   - Initialize repository with React project structure
   - Configure .gitignore for Node.js/React
   - Add README with project description
   - Set up initial package.json and dependencies
   - **Estimate:** 2 hours

2. **Task 1.2.2:** Create minierp-api repository (or backend folder in mono-repo)
   - Initialize repository with NestJS or FastAPI structure
   - Configure .gitignore for Node.js/Python
   - Add README with project description
   - Set up initial package.json/requirements.txt
   - **Estimate:** 2 hours

3. **Task 1.2.3:** Configure branch protection rules
   - Set up main and dev branch protection
   - Require PR reviews before merge
   - Require status checks to pass
   - Configure branch naming conventions
   - **Estimate:** 1 hour

4. **Task 1.2.4:** Set up repository structure
   - Create folder structure (src, tests, docs, etc.)
   - Add CONTRIBUTING.md guidelines
   - Add LICENSE file
   - **Estimate:** 1 hour

---

### Story 1.3: Frontend CI/CD Pipeline

**Story Description:** Implement GitHub Actions workflows for frontend repository: PR validation, dev deployment, and test deployment.

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `devops`, `ci-cd`, `frontend`

**Acceptance Criteria:**
- PR validation workflow (lint, SonarQube, unit tests)
- Dev deployment workflow (build, docker, push, deploy)
- Test deployment workflow (semantic versioning, build, deploy)
- All workflows properly configured with secrets

**Tasks:**
1. **Task 1.3.1:** Create PR validation workflow (.github/workflows/pr-validation.yml)
   - Configure lint job with ESLint
   - Configure SonarQube analysis job
   - Configure unit tests job with coverage
   - Set up job dependencies and parallel execution
   - **Estimate:** 4 hours

2. **Task 1.3.2:** Create dev deployment workflow (.github/workflows/deploy-dev.yml)
   - Configure build job with environment variables
   - Set up Docker build and tagging
   - Configure container registry login and push
   - Set up deployment to dev environment (VM/Compose or K8s)
   - **Estimate:** 5 hours

3. **Task 1.3.3:** Create test deployment workflow (.github/workflows/deploy-test.yml)
   - Configure semantic versioning generation
   - Set up build with test environment variables
   - Configure Docker build with version tags
   - Set up deployment to test environment
   - **Estimate:** 5 hours

4. **Task 1.3.4:** Configure GitHub secrets
   - Set up cloud provider credentials (if applicable)
   - Configure SonarQube token and URL
   - Set up deployment configs (DEV_CONFIG, TEST_CONFIG)
   - Configure environment-specific URLs
   - **Estimate:** 2 hours

5. **Task 1.3.5:** Test all workflows end-to-end
   - Test PR validation workflow
   - Test dev deployment workflow
   - Test test deployment workflow
   - Verify Docker images are created and pushed
   - **Estimate:** 4 hours

---

### Story 1.4: Backend CI/CD Pipeline

**Story Description:** Implement GitHub Actions workflows for backend repository: PR validation, dev deployment, and test deployment.

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `devops`, `ci-cd`, `backend`

**Acceptance Criteria:**
- PR validation workflow (lint, SonarQube, unit tests)
- Dev deployment workflow (build, docker, push, deploy)
- Test deployment workflow (semantic versioning, build, deploy)
- All workflows properly configured with secrets

**Tasks:**
1. **Task 1.4.1:** Create PR validation workflow (.github/workflows/pr-validation.yml)
   - Configure lint job (ESLint/Pylint)
   - Configure SonarQube analysis job
   - Configure unit tests job with coverage
   - Set up job dependencies
   - **Estimate:** 4 hours

2. **Task 1.4.2:** Create dev deployment workflow (.github/workflows/deploy-dev.yml)
   - Configure test execution before build
   - Set up application build
   - Configure Docker build and tagging
   - Set up container registry push and deployment
   - **Estimate:** 5 hours

3. **Task 1.4.3:** Create test deployment workflow (.github/workflows/deploy-test.yml)
   - Configure semantic versioning
   - Set up test execution
   - Configure build and Docker image creation
   - Set up deployment to test environment
   - **Estimate:** 5 hours

4. **Task 1.4.4:** Configure GitHub secrets for backend
   - Set up cloud provider credentials
   - Configure SonarQube settings
   - Set up deployment configs
   - Configure database and service URLs
   - **Estimate:** 2 hours

5. **Task 1.4.5:** Test all backend workflows
   - Test PR validation
   - Test dev deployment
   - Test test deployment
   - Verify deployments work correctly
   - **Estimate:** 4 hours

---

### Story 1.5: Production Infrastructure Setup

**Story Description:** Create production deployment manifests and configuration (VM/Compose or Kubernetes).

**Story Points:** 13  
**Priority:** High  
**Labels:** `devops`, `infrastructure`, `kubernetes`

**Acceptance Criteria:**
- Deployment manifests for all services
- ConfigMaps and Secrets configured
- Service and Ingress definitions
- Horizontal Pod Autoscaling configured (if K8s)
- Resource limits and requests set

**Tasks:**
1. **Task 1.5.1:** Create frontend deployment manifest
   - Define Deployment with replicas
   - Configure resource limits and requests
   - Set up environment variables from ConfigMap
   - Configure health checks and probes
   - **Estimate:** 3 hours

2. **Task 1.5.2:** Create backend deployment manifest
   - Define Deployment with replicas
   - Configure resource limits
   - Set up environment variables
   - Configure health checks
   - **Estimate:** 3 hours

3. **Task 1.5.3:** Create Service definitions
   - Frontend Service (ClusterIP or LoadBalancer)
   - Backend Service (ClusterIP)
   - Configure service ports and selectors
   - **Estimate:** 2 hours

4. **Task 1.5.4:** Create Ingress configuration (if K8s) or Nginx config (if VM)
   - Configure Ingress for frontend
   - Configure Ingress for backend API
   - Set up SSL/TLS certificates
   - Configure routing rules
   - **Estimate:** 3 hours

5. **Task 1.5.5:** Create ConfigMaps and Secrets
   - Application configuration ConfigMaps
   - Database connection secrets
   - API keys and tokens secrets
   - Environment-specific configurations
   - **Estimate:** 4 hours

6. **Task 1.5.6:** Configure Horizontal Pod Autoscaling (if K8s)
   - Set up HPA for frontend
   - Set up HPA for backend
   - Configure min/max replicas
   - Set up CPU/memory thresholds
   - **Estimate:** 3 hours

7. **Task 1.5.7:** Create namespace and RBAC (if K8s)
   - Define namespaces (dev, test, prod)
   - Configure ServiceAccounts
   - Set up RBAC rules
   - **Estimate:** 2 hours

8. **Task 1.5.8:** Document deployment procedures
   - Write deployment guide
   - Document rollback procedures
   - Create troubleshooting guide
   - **Estimate:** 2 hours

---

## EPIC 2: Authentication & Authorization (Keycloak)

**Epic Description:** Implement Keycloak-based authentication and authorization for both frontend and backend, with support for SSO and MFA.

**Epic Goal:** Provide secure, centralized authentication with SSO support and role-based access control.

**Acceptance Criteria:**
- Keycloak server deployed and configured
- Frontend integrates with Keycloak for login (tenant-aware)
- Backend validates JWT tokens from Keycloak
- Role-based access control working for UI and API
- Tenant-aware token claims (tenant_id in JWT)

---

### Story 2.1: Keycloak Server Setup & Configuration

**Story Description:** Deploy and configure Keycloak server with realm, clients, and user management.

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `backend`, `security`, `keycloak`

**Acceptance Criteria:**
- Keycloak server running in Docker/Kubernetes
- Realm created for minierp-saas
- Frontend and backend clients configured
- User roles defined (tenant_admin, owner, manager, accountant, storekeeper, worker, platform_admin)
- MFA configuration available
- Tenant-aware token claims configured

**Tasks:**
1. **Task 2.1.1:** Deploy Keycloak server
   - Set up Keycloak in Docker Compose
   - Configure PostgreSQL database for Keycloak
   - Set up admin credentials
   - Configure environment variables
   - **Estimate:** 3 hours

2. **Task 2.1.2:** Create Keycloak realm
   - Create "minierp-saas" realm
   - Configure realm settings (tokens, sessions)
   - Set up email configuration
   - Configure password policies
   - **Estimate:** 2 hours

3. **Task 2.1.3:** Configure frontend client (minierp-web)
   - Create public client for React SPA
   - Enable PKCE flow
   - Configure redirect URIs
   - Set up CORS settings
   - **Estimate:** 2 hours

4. **Task 2.1.4:** Configure backend client (minierp-api)
   - Create confidential client
   - Configure service account roles
   - Set up client credentials flow
   - Configure token settings
   - **Estimate:** 2 hours

5. **Task 2.1.5:** Define user roles
   - Create realm roles: tenant_admin, owner, manager, accountant, storekeeper, worker, platform_admin
   - Configure role hierarchy
   - Set up role descriptions
   - **Estimate:** 1 hour

6. **Task 2.1.6:** Configure MFA (optional)
   - Set up TOTP authenticator
   - Configure SMS authenticator (if needed)
   - Test MFA flow
   - **Estimate:** 3 hours

7. **Task 2.1.7:** Configure tenant-aware token claims
   - Add tenant_id to token claims
   - Add tenant_code to token claims
   - Configure token mapper
   - Test token claims
   - **Estimate:** 3 hours

---

### Story 2.2: Frontend Keycloak Integration

**Story Description:** Integrate Keycloak authentication in React frontend with token management and route guards.

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `frontend`, `security`, `keycloak`

**Acceptance Criteria:**
- Keycloak JS adapter integrated
- Login/logout functionality working
- Token refresh implemented
- Route guards based on roles
- UI elements shown/hidden based on roles
- Tenant-aware login (subdomain or tenant code)

**Tasks:**
1. **Task 2.2.1:** Install and configure Keycloak JS adapter
   - Install @react-keycloak/web package
   - Create Keycloak configuration file
   - Initialize Keycloak instance
   - Configure PKCE flow
   - **Estimate:** 3 hours

2. **Task 2.2.2:** Implement authentication context
   - Create KeycloakProvider wrapper
   - Set up authentication state management
   - Handle token refresh automatically
   - Implement logout functionality
   - **Estimate:** 4 hours

3. **Task 2.2.3:** Create route guards component
   - Build ProtectedRoute component
   - Extract roles from JWT token
   - Check role-based access
   - Redirect unauthorized users
   - **Estimate:** 4 hours

4. **Task 2.2.4:** Implement role-based UI rendering
   - Create useRoles hook
   - Conditionally render UI elements
   - Show/hide menu items based on roles
   - Update dashboard based on roles
   - **Estimate:** 3 hours

5. **Task 2.2.5:** Set up API request interceptor
   - Configure axios with token injection
   - Handle token refresh on 401 errors
   - Retry failed requests after refresh
   - **Estimate:** 3 hours

6. **Task 2.2.6:** Implement tenant-aware login
   - Detect tenant from subdomain
   - Allow tenant code input on login
   - Pass tenant context to Keycloak
   - Store tenant information
   - **Estimate:** 4 hours

7. **Task 2.2.7:** Create login page component
   - Design login UI with tenant branding
   - Integrate Keycloak login flow
   - Handle login errors
   - **Estimate:** 3 hours

8. **Task 2.2.8:** Test authentication flows
   - Test login flow
   - Test logout flow
   - Test token refresh
   - Test role-based access
   - Test tenant-aware login
   - **Estimate:** 3 hours

---

### Story 2.3: Backend Keycloak Integration

**Story Description:** Implement JWT token validation and role-based authorization in backend API.

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `backend`, `security`, `keycloak`

**Acceptance Criteria:**
- JWT token validation middleware
- Role-based guards for endpoints
- Tenant ID extraction from token
- Error handling for invalid/expired tokens
- Tenant-aware authorization

**Tasks:**
1. **Task 2.3.1:** Install and configure Keycloak adapter
   - Install @nestjs/keycloak-connect (NestJS) or python-keycloak (FastAPI)
   - Configure Keycloak connection
   - Set up JWKS endpoint for token validation
   - **Estimate:** 3 hours

2. **Task 2.3.2:** Create authentication guard
   - Implement token extraction from headers
   - Validate JWT token signature
   - Verify token expiration
   - Extract user information from token
   - **Estimate:** 4 hours

3. **Task 2.3.3:** Create role-based authorization guard
   - Implement RolesGuard
   - Extract roles from token claims
   - Check user roles against required roles
   - Return 403 for unauthorized access
   - **Estimate:** 4 hours

4. **Task 2.3.4:** Create tenant middleware
   - Extract tenant_id from JWT token
   - Extract tenant_code from JWT token
   - Attach tenant_id to request object
   - Validate tenant_id exists
   - **Estimate:** 3 hours

5. **Task 2.3.5:** Apply guards to API endpoints
   - Add AuthGuard to all protected endpoints
   - Add RolesGuard with appropriate roles
   - Configure public endpoints (tenant config)
   - **Estimate:** 4 hours

6. **Task 2.3.6:** Implement error handling
   - Handle invalid token errors
   - Handle expired token errors
   - Handle missing role errors
   - Handle missing tenant_id errors
   - Return appropriate HTTP status codes
   - **Estimate:** 2 hours

7. **Task 2.3.7:** Test authentication and authorization
   - Test token validation
   - Test role-based access control
   - Test tenant isolation
   - Test error scenarios
   - **Estimate:** 3 hours

---

### Story 2.4: User Management API

**Story Description:** Create API endpoints for user CRUD operations synchronized with Keycloak.

**Story Points:** 5  
**Priority:** High  
**Labels:** `backend`, `api`, `keycloak`

**Acceptance Criteria:**
- Create user in Keycloak and local DB
- Update user information
- Assign roles to users
- Deactivate users
- List users with filtering

**Tasks:**
1. **Task 2.4.1:** Create user service
   - Implement createUser method
   - Sync user with Keycloak
   - Store user in local database
   - Handle errors
   - **Estimate:** 4 hours

2. **Task 2.4.2:** Create user controller endpoints
   - POST /api/v1/users (create)
   - GET /api/v1/users (list with filters)
   - GET /api/v1/users/:id (get by id)
   - PUT /api/v1/users/:id (update)
   - DELETE /api/v1/users/:id (deactivate)
   - **Estimate:** 5 hours

3. **Task 2.4.3:** Implement role assignment
   - Assign roles in Keycloak
   - Update role in local database
   - Validate role exists
   - **Estimate:** 3 hours

4. **Task 2.4.4:** Add user filtering and pagination
   - Filter by role, status, tenant
   - Implement pagination
   - Add search functionality
   - **Estimate:** 3 hours

5. **Task 2.4.5:** Test user management APIs
   - Test create user
   - Test update user
   - Test role assignment
   - Test filtering
   - **Estimate:** 2 hours

---

## EPIC 3: Multi-Tenancy Foundation

**Epic Description:** Implement multi-tenancy infrastructure with tenant isolation, branding, and configuration.

**Epic Goal:** Enable multiple businesses to use the platform with complete data isolation and custom branding.

**Acceptance Criteria:**
- Tenant data isolation working (shared schema + tenant_id + RLS)
- Tenant branding API functional
- Tenant configuration stored and retrieved
- Row-level security implemented
- Tenant routing via subdomain or tenant code

---

### Story 3.1: Database Schema & Multi-Tenancy Tables

**Story Description:** Create database schema with all tables including tenant_id for multi-tenancy.

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `backend`, `database`, `multi-tenancy`

**Acceptance Criteria:**
- All tables created with tenant_id column
- Foreign key relationships established
- Indexes created for performance
- Row-level security policies configured

**Tasks:**
1. **Task 3.1.1:** Create tenants table
   - Define schema with all columns
   - Add branding fields (logo_url, colors)
   - Add subscription fields
   - Create indexes
   - **Estimate:** 2 hours

2. **Task 3.1.2:** Create users table
   - Define schema with tenant_id FK
   - Add Keycloak integration fields
   - Create unique constraints
   - **Estimate:** 2 hours

3. **Task 3.1.3:** Create customers table
   - Define schema with tenant_id FK
   - Add customer information fields
   - Create indexes
   - **Estimate:** 2 hours

4. **Task 3.1.4:** Create items table
   - Define schema with tenant_id FK
   - Add item information fields
   - Create indexes
   - **Estimate:** 2 hours

5. **Task 3.1.5:** Create warehouses table
   - Define schema with tenant_id FK
   - Add warehouse information fields
   - **Estimate:** 1 hour

6. **Task 3.1.6:** Create stock_ledger table
   - Define schema with tenant_id FK
   - Add stock movement fields
   - Create indexes for performance
   - **Estimate:** 2 hours

7. **Task 3.1.7:** Create job_orders table
   - Define schema with tenant_id and customer_id FKs
   - Add job order fields
   - Create indexes for filtering
   - **Estimate:** 2 hours

8. **Task 3.1.8:** Create job_materials, job_time_entries, job_scrap tables
   - Define schemas with tenant_id and job_id FKs
   - Add respective fields
   - Create indexes
   - **Estimate:** 3 hours

9. **Task 3.1.9:** Create workforce table
   - Define schema with tenant_id FK
   - Add employee information fields
   - Create indexes
   - **Estimate:** 2 hours

10. **Task 3.1.10:** Create expenses table
    - Define schema with tenant_id FK
    - Add expense fields
    - Create indexes
    - **Estimate:** 2 hours

11. **Task 3.1.11:** Create invoices and einvoices tables
    - Define schemas with tenant_id FKs
    - Add invoice and e-invoice fields
    - Create indexes
    - **Estimate:** 2 hours

12. **Task 3.1.12:** Create documents table
    - Define schema with tenant_id FK
    - Add document metadata fields
    - Create polymorphic entity linking
    - Create indexes
    - **Estimate:** 2 hours

13. **Task 3.1.13:** Create audit_logs table
    - Define schema for audit trail
    - Add indexes for querying
    - **Estimate:** 1 hour

14. **Task 3.1.14:** Configure Row Level Security (RLS)
    - Enable RLS on all tenant tables
    - Create RLS policies for tenant isolation
    - Test RLS policies
    - **Estimate:** 4 hours

15. **Task 3.1.15:** Create database migration scripts
    - Create initial migration
    - Add rollback scripts
    - Document migration process
    - **Estimate:** 3 hours

---

### Story 3.2: Tenant Middleware & Data Isolation

**Story Description:** Implement tenant middleware that automatically filters all queries by tenant_id.

**Story Points:** 5  
**Priority:** Critical  
**Labels:** `backend`, `multi-tenancy`, `middleware`

**Acceptance Criteria:**
- Tenant ID extracted from JWT token
- All database queries automatically filtered by tenant_id
- Tenant isolation verified in all endpoints
- No data leakage between tenants

**Tasks:**
1. **Task 3.2.1:** Create tenant extraction middleware
   - Extract tenant_id from JWT token
   - Validate tenant_id exists
   - Attach tenant_id to request object
   - Handle missing tenant_id
   - **Estimate:** 3 hours

2. **Task 3.2.2:** Create tenant-aware repository base class
   - Implement base repository with tenant filtering
   - Override findAll, findOne, create, update methods
   - Automatically add tenant_id to queries
   - **Estimate:** 5 hours

3. **Task 3.2.3:** Update all services to use tenant filtering
   - Update customer service
   - Update job order service
   - Update inventory service
   - Update workforce service
   - Update expense service
   - **Estimate:** 6 hours

4. **Task 3.2.4:** Add tenant validation
   - Validate tenant exists and is active
   - Check tenant subscription limits
   - Return appropriate errors
   - **Estimate:** 3 hours

5. **Task 3.2.5:** Test tenant isolation
   - Create test data for multiple tenants
   - Verify queries only return tenant's data
   - Test cross-tenant access attempts
   - **Estimate:** 4 hours

---

### Story 3.3: Tenant Branding API

**Story Description:** Implement API endpoints for tenant branding configuration (logo, colors, favicon).

**Story Points:** 5  
**Priority:** High  
**Labels:** `backend`, `api`, `branding`

**Acceptance Criteria:**
- Public endpoint to get tenant config by code
- Admin endpoint to update branding
- Logo and favicon upload to S3
- Branding applied to frontend

**Tasks:**
1. **Task 3.3.1:** Create public tenant config endpoint
   - GET /api/v1/tenants/config?code={code}
   - Return tenant branding information
   - No authentication required
   - Cache response
   - **Estimate:** 3 hours

2. **Task 3.3.2:** Create tenant branding update endpoint
   - PUT /api/v1/tenants/branding
   - Require tenant_admin role
   - Accept logo, favicon, colors
   - Upload files to S3
   - **Estimate:** 5 hours

3. **Task 3.3.3:** Implement S3 file upload
   - Configure S3 client
   - Upload logo to tenant-specific folder
   - Upload favicon
   - Generate S3 URLs
   - **Estimate:** 4 hours

4. **Task 3.3.4:** Add image validation
   - Validate file types (PNG, JPG, SVG)
   - Validate file sizes
   - Resize images if needed
   - **Estimate:** 3 hours

5. **Task 3.3.5:** Test branding API
   - Test public config endpoint
   - Test branding update
   - Test file uploads
   - Test authorization
   - **Estimate:** 2 hours

---

### Story 3.4: Frontend Tenant Branding

**Story Description:** Implement dynamic tenant branding in React frontend (logo, colors, favicon).

**Story Points:** 5  
**Priority:** High  
**Labels:** `frontend`, `branding`, `ui`

**Acceptance Criteria:**
- Logo loaded from tenant config
- Colors applied to UI theme
- Favicon set dynamically
- Branding loads on app initialization

**Tasks:**
1. **Task 3.4.1:** Create tenant config service
   - Fetch tenant config by code
   - Cache config in localStorage
   - Handle config loading errors
   - **Estimate:** 3 hours

2. **Task 3.4.2:** Implement dynamic theme provider
   - Create theme context
   - Apply primary/secondary colors
   - Update Ant Design/MUI theme
   - **Estimate:** 4 hours

3. **Task 3.4.3:** Implement dynamic logo loading
   - Load logo from tenant config
   - Display in header/navbar
   - Handle missing logo
   - **Estimate:** 2 hours

4. **Task 3.4.4:** Implement dynamic favicon
   - Set favicon from tenant config
   - Update document head
   - Handle favicon loading
   - **Estimate:** 2 hours

5. **Task 3.4.5:** Create tenant settings UI
   - Build branding configuration form
   - Add logo upload component
   - Add color picker
   - Save branding changes
   - **Estimate:** 5 hours

6. **Task 3.4.6:** Test tenant branding
   - Test logo display
   - Test color application
   - Test favicon
   - Test branding update
   - **Estimate:** 2 hours

---

## EPIC 4: Core Data Models & Customer Management

**Epic Description:** Implement core data models, services, and APIs for customers, order requests, and consumer orders.

**Epic Goal:** Provide CRUD operations and business logic for core customer and order entities.

**Acceptance Criteria:**
- Customer/Consumer management working
- Order request/intake workflow functional
- Consumer order lifecycle managed
- Order acceptance workflow (REQUESTED → ACCEPTED/REJECTED)
- All APIs properly secured and tested

---

### Story 4.1: Customer/Consumer Management

**Story Description:** Implement CRUD operations for customers/consumers.

**Story Points:** 5  
**Priority:** High  
**Labels:** `backend`, `api`, `crud`

**Acceptance Criteria:**
- Create, read, update customers
- Search and filter customers
- Customer profile management
- Link customers to orders

**Tasks:**
1. **Task 4.1.1:** Create customer entity/model
   - Define data model
   - Add validation decorators
   - Create DTOs for create/update
   - **Estimate:** 2 hours

2. **Task 4.1.2:** Create customer service
   - Implement create method
   - Implement findAll with filters
   - Implement findOne
   - Implement update
   - Implement delete (soft delete)
   - **Estimate:** 4 hours

3. **Task 4.1.3:** Create customer controller
   - POST /api/v1/customers
   - GET /api/v1/customers
   - GET /api/v1/customers/:id
   - PUT /api/v1/customers/:id
   - DELETE /api/v1/customers/:id
   - **Estimate:** 3 hours

4. **Task 4.1.4:** Add search and filtering
   - Search by name, email, phone
   - Filter by status, type
   - Implement pagination
   - Add sorting
   - **Estimate:** 3 hours

5. **Task 4.1.5:** Test customer APIs
   - Test all CRUD operations
   - Test search
   - Test filtering
   - **Estimate:** 2 hours

---

### Story 4.2: Order Request/Intake Management

**Story Description:** Implement order request/intake form with phased delivery requirements.

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `backend`, `api`, `workflow`, `frontend`

**Acceptance Criteria:**
- Create order request with customer selection
- Phased delivery requirements (quantity + dates per phase)
- Design type and reference number
- Specifications, packing instructions, delivery address
- Pricing & contract terms
- Design files & attachments upload
- Order status (REQUESTED → ACCEPTED/REJECTED)

**Tasks:**
1. **Task 4.2.1:** Create order request entity/model
   - Define data model
   - Add phased delivery JSONB field
   - Create DTOs
   - **Estimate:** 3 hours

2. **Task 4.2.2:** Create order request service
   - Implement create method
   - Handle phased delivery data
   - Link to customer
   - Store design files references
   - **Estimate:** 5 hours

3. **Task 4.2.3:** Create order request controller
   - POST /api/v1/order-requests
   - GET /api/v1/order-requests
   - GET /api/v1/order-requests/:id
   - PUT /api/v1/order-requests/:id
   - **Estimate:** 4 hours

4. **Task 4.2.4:** Implement phased delivery structure
   - Define phased delivery JSON schema
   - Validate phased delivery data
   - Store phase requirements
   - **Estimate:** 4 hours

5. **Task 4.2.5:** Build order intake form UI
   - Customer selection component
   - Order details form
   - Phased delivery input component
   - Design type and reference fields
   - File upload component
   - **Estimate:** 8 hours

6. **Task 4.2.6:** Link design files to order request
   - Upload design files
   - Store file references
   - Link to order request
   - **Estimate:** 3 hours

7. **Task 4.2.7:** Test order request APIs
   - Test create order request
   - Test phased delivery
   - Test file uploads
   - **Estimate:** 3 hours

---

### Story 4.3: Order Acceptance Workflow

**Story Description:** Implement order acceptance workflow where Org reviews and accepts/rejects order requests.

**Story Points:** 5  
**Priority:** Critical  
**Labels:** `backend`, `api`, `workflow`

**Acceptance Criteria:**
- Manager can review order requests
- Accept order request (REQUESTED → ACCEPTED)
- Reject order request (REQUESTED → REJECTED)
- Update accepted quantities and committed dates per phase
- Convert accepted request to job order

**Tasks:**
1. **Task 4.3.1:** Implement status workflow
   - Define status transitions
   - Validate status changes
   - Add status history tracking
   - **Estimate:** 3 hours

2. **Task 4.3.2:** Create order acceptance endpoint
   - PATCH /api/v1/order-requests/:id/accept
   - Update status to ACCEPTED
   - Update accepted quantities per phase
   - Update committed dates per phase
   - **Estimate:** 4 hours

3. **Task 4.3.3:** Create order rejection endpoint
   - PATCH /api/v1/order-requests/:id/reject
   - Update status to REJECTED
   - Store rejection reason
   - **Estimate:** 2 hours

4. **Task 4.3.4:** Implement phase acceptance logic
   - Allow partial phase acceptance
   - Update accepted quantities
   - Update committed dates
   - Validate phase data
   - **Estimate:** 4 hours

5. **Task 4.3.5:** Create convert to job order endpoint
   - POST /api/v1/order-requests/:id/convert
   - Create job order from accepted request
   - Link job to order request
   - **Estimate:** 3 hours

6. **Task 4.3.6:** Test order acceptance workflow
   - Test accept order
   - Test reject order
   - Test phase acceptance
   - Test convert to job
   - **Estimate:** 3 hours

---

### Story 4.4: Consumer Orders Management

**Story Description:** Implement consumer orders management with status tracking and inline editing.

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `backend`, `api`, `frontend`, `workflow`

**Acceptance Criteria:**
- List consumer orders with filters (status, customer)
- Order stats (active, ready, dispatched, overdue)
- Inline editing of quantity and due dates
- Order status tracking
- Navigation to consumer edit
- Update order status (READY → DISPATCHED)

**Tasks:**
1. **Task 4.4.1:** Create consumer order entity/model
   - Define data model
   - Link to customer and order request
   - Add status field
   - **Estimate:** 2 hours

2. **Task 4.4.2:** Create consumer order service
   - Implement CRUD methods
   - Calculate order stats
   - Handle status updates
   - **Estimate:** 5 hours

3. **Task 4.4.3:** Create consumer order controller
   - GET /api/v1/consumer-orders
   - GET /api/v1/consumer-orders/stats
   - GET /api/v1/consumer-orders/:id
   - POST /api/v1/consumer-orders
   - PUT /api/v1/consumer-orders/:id
   - PATCH /api/v1/consumer-orders/:id/quantity
   - PATCH /api/v1/consumer-orders/:id/due-date
   - PATCH /api/v1/consumer-orders/:id/status
   - **Estimate:** 5 hours

4. **Task 4.4.4:** Implement order stats calculation
   - Calculate active orders count
   - Calculate ready for dispatch count
   - Calculate dispatched count
   - Calculate overdue count
   - **Estimate:** 3 hours

5. **Task 4.4.5:** Build consumer orders list UI
   - Order list table
   - Stats cards component
   - Filter controls
   - Status badges
   - **Estimate:** 6 hours

6. **Task 4.4.6:** Implement inline editing
   - Inline quantity edit
   - Inline due date edit
   - Save/cancel actions
   - Optimistic UI updates
   - **Estimate:** 5 hours

7. **Task 4.4.7:** Add navigation to consumer edit
   - Link to customer details
   - Customer edit page navigation
   - **Estimate:** 2 hours

8. **Task 4.4.8:** Test consumer orders APIs
   - Test list and filtering
   - Test stats calculation
   - Test inline editing
   - Test status updates
   - **Estimate:** 3 hours

---

## EPIC 5: Inventory Management

**Epic Description:** Implement inventory management system with items, warehouses, stock ledger, and stock movements.

**Epic Goal:** Provide real-time inventory visibility, stock tracking, and material management.

**Acceptance Criteria:**
- Items CRUD operations working
- Warehouse management functional
- Stock ledger as source of truth (immutable entries)
- Stock posting (issue/receipt/adjust) working
- Low stock alerts functional
- Stock valuation and reporting

---

### Story 5.1: Items Management

**Story Description:** Implement CRUD operations for inventory items.

**Story Points:** 5  
**Priority:** High  
**Labels:** `backend`, `api`, `crud`

**Acceptance Criteria:**
- Create, read, update items
- Item categorization
- Item search and filtering
- Item details (unit, HSN code, etc.)

**Tasks:**
1. **Task 5.1.1:** Create item entity/model
   - Define data model
   - Add validation decorators
   - Create DTOs for create/update
   - **Estimate:** 2 hours

2. **Task 5.1.2:** Create item service
   - Implement create method
   - Implement findAll with filters
   - Implement findOne
   - Implement update
   - Implement delete (soft delete)
   - **Estimate:** 4 hours

3. **Task 5.1.3:** Create item controller
   - POST /api/v1/items
   - GET /api/v1/items
   - GET /api/v1/items/:id
   - PUT /api/v1/items/:id
   - DELETE /api/v1/items/:id
   - **Estimate:** 3 hours

4. **Task 5.1.4:** Add item categorization
   - Define item categories
   - Assign category to items
   - Filter by category
   - **Estimate:** 2 hours

5. **Task 5.1.5:** Add search and filtering
   - Search by name, code, HSN
   - Filter by category, stock status
   - Implement pagination
   - Add sorting
   - **Estimate:** 3 hours

6. **Task 5.1.6:** Test item APIs
   - Test all CRUD operations
   - Test search
   - Test filtering
   - **Estimate:** 2 hours

---

### Story 5.2: Warehouse Management

**Story Description:** Implement warehouse/location management.

**Story Points:** 3  
**Priority:** High  
**Labels:** `backend`, `api`, `crud`

**Acceptance Criteria:**
- Create, read, update warehouses
- Warehouse assignment to items
- Multi-warehouse support

**Tasks:**
1. **Task 5.2.1:** Create warehouse entity/model
   - Define data model
   - Add validation
   - Create DTOs
   - **Estimate:** 1 hour

2. **Task 5.2.2:** Create warehouse service
   - Implement CRUD methods
   - Handle tenant isolation
   - **Estimate:** 2 hours

3. **Task 5.2.3:** Create warehouse controller
   - POST /api/v1/warehouses
   - GET /api/v1/warehouses
   - GET /api/v1/warehouses/:id
   - PUT /api/v1/warehouses/:id
   - **Estimate:** 2 hours

4. **Task 5.2.4:** Test warehouse APIs
   - Test CRUD operations
   - Test multi-warehouse support
   - **Estimate:** 1 hour

---

### Story 5.3: Stock Ledger Implementation

**Story Description:** Implement immutable stock ledger as source of truth for all stock movements.

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `backend`, `database`, `inventory`

**Acceptance Criteria:**
- Stock ledger table with immutable entries
- Every stock change creates ledger entry
- Current stock derived from ledger
- Stock ledger queries optimized
- Audit trail for all stock movements

**Tasks:**
1. **Task 5.3.1:** Design stock ledger schema
   - Define ledger entry structure
   - Add transaction type (issue, receipt, adjust)
   - Add reference fields (job_id, document_id)
   - Create indexes
   - **Estimate:** 3 hours

2. **Task 5.3.2:** Create stock ledger service
   - Implement create ledger entry method
   - Ensure immutability (no updates/deletes)
   - Calculate running balance
   - **Estimate:** 5 hours

3. **Task 5.3.3:** Implement current stock calculation
   - Materialize current stock from ledger
   - Update items.current_stock field
   - Optimize calculation queries
   - **Estimate:** 4 hours

4. **Task 5.3.4:** Create stock ledger query service
   - Query ledger by item
   - Query ledger by date range
   - Query ledger by transaction type
   - Optimize queries with indexes
   - **Estimate:** 4 hours

5. **Task 5.3.5:** Test stock ledger
   - Test ledger entry creation
   - Test current stock calculation
   - Test ledger queries
   - Test immutability
   - **Estimate:** 3 hours

---

### Story 5.4: Stock Posting (Issue/Receipt/Adjust)

**Story Description:** Implement stock posting operations for material issue, receipt, and adjustment.

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `backend`, `api`, `inventory`

**Acceptance Criteria:**
- Stock issue to jobs
- Stock receipt (GRN)
- Stock adjustment
- Stock posting creates ledger entry
- Stock validation before posting
- Idempotent posting with keys

**Tasks:**
1. **Task 5.4.1:** Create stock posting service
   - Implement issue method
   - Implement receipt method
   - Implement adjust method
   - Validate stock availability
   - **Estimate:** 6 hours

2. **Task 5.4.2:** Implement idempotency
   - Add idempotency key support
   - Check for duplicate postings
   - Return existing entry if duplicate
   - **Estimate:** 3 hours

3. **Task 5.4.3:** Create stock posting controller
   - POST /api/v1/stock/postings
   - Accept transaction type (issue, receipt, adjust)
   - Validate request data
   - **Estimate:** 4 hours

4. **Task 5.4.4:** Link stock posting to ledger
   - Create ledger entry on posting
   - Update current stock
   - Handle errors and rollback
   - **Estimate:** 4 hours

5. **Task 5.4.5:** Implement stock validation
   - Check stock availability for issue
   - Validate warehouse exists
   - Validate item exists
   - Validate quantities
   - **Estimate:** 3 hours

6. **Task 5.4.6:** Test stock posting
   - Test stock issue
   - Test stock receipt
   - Test stock adjustment
   - Test idempotency
   - Test validation
   - **Estimate:** 4 hours

---

### Story 5.5: Low Stock Alerts

**Story Description:** Implement low stock alerts and notifications.

**Story Points:** 5  
**Priority:** Medium  
**Labels:** `backend`, `api`, `notifications`

**Acceptance Criteria:**
- Low stock threshold configuration
- Low stock detection
- Low stock alerts on dashboard
- Email/SMS notifications (optional)

**Tasks:**
1. **Task 5.5.1:** Add low stock threshold to items
   - Add min_stock_level field
   - Allow threshold configuration
   - **Estimate:** 2 hours

2. **Task 5.5.2:** Create low stock detection service
   - Query items below threshold
   - Calculate stock deficit
   - **Estimate:** 3 hours

3. **Task 5.5.3:** Create low stock API endpoint
   - GET /api/v1/stock/low
   - Return items below threshold
   - Include stock deficit information
   - **Estimate:** 2 hours

4. **Task 5.5.4:** Add low stock alerts to dashboard
   - Display low stock items
   - Show stock deficit
   - Link to item details
   - **Estimate:** 3 hours

5. **Task 5.5.5:** Test low stock alerts
   - Test threshold configuration
   - Test detection logic
   - Test dashboard display
   - **Estimate:** 2 hours

---

### Story 5.6: Stock Valuation & Reporting

**Story Description:** Implement stock valuation and inventory reports.

**Story Points:** 5  
**Priority:** Medium  
**Labels:** `backend`, `api`, `reports`

**Acceptance Criteria:**
- Stock valuation calculation
- Inventory reports (stock aging, valuation)
- Stock movement reports
- Export capabilities

**Tasks:**
1. **Task 5.6.1:** Implement stock valuation calculation
   - Calculate total stock value
   - Use item cost/price
   - Calculate by warehouse
   - **Estimate:** 3 hours

2. **Task 5.6.2:** Create stock ledger query endpoint
   - GET /api/v1/stock/ledger
   - Filter by item, date range, type
   - Pagination support
   - **Estimate:** 3 hours

3. **Task 5.6.3:** Implement stock aging report
   - Calculate stock age
   - Group by age buckets
   - Include valuation
   - **Estimate:** 4 hours

4. **Task 5.6.4:** Create inventory reports API
   - GET /api/v1/reports/inventory
   - Include stock valuation
   - Include stock aging
   - Include movement summary
   - **Estimate:** 4 hours

5. **Task 5.6.5:** Add export capabilities
   - Export to CSV
   - Export to PDF
   - Include all report data
   - **Estimate:** 3 hours

6. **Task 5.6.6:** Test stock valuation and reports
   - Test valuation calculation
   - Test stock ledger queries
   - Test reports
   - Test exports
   - **Estimate:** 3 hours

---

## EPIC 6: Job/Work Order Management

**Epic Description:** Implement job/work order lifecycle management with material consumption, time tracking, scrap recording, and profitability calculation.

**Epic Goal:** Enable complete job order tracking from creation to closure with profitability insights.

**Acceptance Criteria:**
- Job order CRUD operations working
- Job status workflow (DRAFT → RELEASED → IN_PROGRESS → READY → DISPATCHED → INVOICED → CLOSED)
- Material consumption tracking
- Time entry recording per job
- Scrap recording per job/batch
- Profitability calculation (materials + time + scrap + overhead)
- Batch readiness notifications
- Job-to-consumer order linking

---

### Story 6.1: Job Order CRUD Operations

**Story Description:** Implement CRUD operations for job/work orders.

**Story Points:** 5  
**Priority:** Critical  
**Labels:** `backend`, `api`, `crud`

**Acceptance Criteria:**
- Create, read, update job orders
- Job order details (customer, description, quantity, dates)
- Job order search and filtering
- Link job to consumer order

**Tasks:**
1. **Task 6.1.1:** Create job order entity/model
   - Define data model
   - Add validation decorators
   - Create DTOs for create/update
   - Link to customer and consumer order
   - **Estimate:** 3 hours

2. **Task 6.1.2:** Create job order service
   - Implement create method
   - Implement findAll with filters
   - Implement findOne
   - Implement update
   - Implement delete (soft delete)
   - **Estimate:** 4 hours

3. **Task 6.1.3:** Create job order controller
   - POST /api/v1/jobs
   - GET /api/v1/jobs
   - GET /api/v1/jobs/:id
   - PUT /api/v1/jobs/:id
   - **Estimate:** 3 hours

4. **Task 6.1.4:** Add search and filtering
   - Search by job number, customer, description
   - Filter by status, customer, date range
   - Implement pagination
   - Add sorting
   - **Estimate:** 3 hours

5. **Task 6.1.5:** Link job to consumer order
   - Create link on job creation
   - Update consumer order status
   - Display linked order in job details
   - **Estimate:** 2 hours

6. **Task 6.1.6:** Test job order APIs
   - Test all CRUD operations
   - Test search
   - Test filtering
   - Test consumer order linking
   - **Estimate:** 2 hours

---

### Story 6.2: Job Status Workflow

**Story Description:** Implement job status workflow with state transitions.

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `backend`, `api`, `workflow`

**Acceptance Criteria:**
- Status workflow: DRAFT → RELEASED → IN_PROGRESS → READY → DISPATCHED → INVOICED → CLOSED
- Status transition validation
- Status history tracking
- Stock issue allowed only after RELEASED
- Batch recording for partial completions

**Tasks:**
1. **Task 6.2.1:** Define status workflow state machine
   - Define valid status transitions
   - Create state machine configuration
   - Validate transitions
   - **Estimate:** 3 hours

2. **Task 6.2.2:** Implement status transition service
   - Create updateStatus method
   - Validate status transitions
   - Enforce business rules
   - **Estimate:** 4 hours

3. **Task 6.2.3:** Create status update endpoint
   - PATCH /api/v1/jobs/:id/status
   - Accept new status
   - Validate transition
   - Update job status
   - **Estimate:** 3 hours

4. **Task 6.2.4:** Implement status history tracking
   - Create status_history table
   - Record each status change
   - Store changed_by and timestamp
   - Query status history
   - **Estimate:** 4 hours

5. **Task 6.2.5:** Enforce stock issue rules
   - Block stock issue before RELEASED
   - Validate job status for material issue
   - Return appropriate errors
   - **Estimate:** 3 hours

6. **Task 6.2.6:** Implement batch recording
   - Track partial completions
   - Record batch quantities
   - Link batches to job
   - **Estimate:** 4 hours

7. **Task 6.2.7:** Test status workflow
   - Test all status transitions
   - Test invalid transitions
   - Test status history
   - Test stock issue rules
   - **Estimate:** 3 hours

---

### Story 6.3: Material Consumption Tracking

**Story Description:** Implement material consumption tracking per job.

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `backend`, `api`, `inventory`, `jobs`

**Acceptance Criteria:**
- Record material consumption per job
- Link consumption to stock ledger
- Material consumption reports
- Material cost calculation

**Tasks:**
1. **Task 6.3.1:** Create job_materials entity/model
   - Define data model
   - Link to job and item
   - Store quantity and cost
   - **Estimate:** 2 hours

2. **Task 6.3.2:** Create material consumption service
   - Implement addMaterial method
   - Link to stock ledger entry
   - Calculate material cost
   - **Estimate:** 5 hours

3. **Task 6.3.3:** Create material consumption endpoints
   - POST /api/v1/jobs/:id/materials
   - GET /api/v1/jobs/:id/materials
   - Update material entry
   - **Estimate:** 4 hours

4. **Task 6.3.4:** Link to stock ledger
   - Create stock ledger entry on consumption
   - Link ledger entry to job material
   - Ensure immutability
   - **Estimate:** 4 hours

5. **Task 6.3.5:** Implement material cost calculation
   - Calculate cost from item price
   - Calculate total material cost per job
   - Update job profitability
   - **Estimate:** 3 hours

6. **Task 6.3.6:** Create material consumption reports
   - Material consumption by job
   - Material consumption by item
   - Cost analysis
   - **Estimate:** 4 hours

7. **Task 6.3.7:** Test material consumption
   - Test add material
   - Test stock ledger linking
   - Test cost calculation
   - Test reports
   - **Estimate:** 3 hours

---

### Story 6.4: Time Entry Recording

**Story Description:** Implement time entry recording per job for workers.

**Story Points:** 5  
**Priority:** High  
**Labels:** `backend`, `api`, `jobs`, `workforce`

**Acceptance Criteria:**
- Record time entries per job
- Link time to workers
- Time entry validation
- Labor cost calculation

**Tasks:**
1. **Task 6.4.1:** Create job_time_entries entity/model
   - Define data model
   - Link to job and worker
   - Store hours and date
   - **Estimate:** 2 hours

2. **Task 6.4.2:** Create time entry service
   - Implement addTimeEntry method
   - Validate time entry
   - Calculate labor cost
   - **Estimate:** 4 hours

3. **Task 6.4.3:** Create time entry endpoints
   - POST /api/v1/jobs/:id/time
   - GET /api/v1/jobs/:id/time
   - Update time entry
   - **Estimate:** 3 hours

4. **Task 6.4.4:** Implement labor cost calculation
   - Get worker rate
   - Calculate cost from hours and rate
   - Calculate total labor cost per job
   - Update job profitability
   - **Estimate:** 4 hours

5. **Task 6.4.5:** Test time entry recording
   - Test add time entry
   - Test validation
   - Test labor cost calculation
   - **Estimate:** 2 hours

---

### Story 6.5: Job Profitability Calculation

**Story Description:** Implement profitability calculation for jobs (materials + time + scrap + overhead).

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `backend`, `api`, `jobs`, `reports`

**Acceptance Criteria:**
- Calculate material cost
- Calculate labor cost
- Calculate scrap cost
- Apply overhead percentage
- Calculate profit margin
- Profitability updated continuously
- Final profitability at CLOSED status

**Tasks:**
1. **Task 6.5.1:** Create profitability calculation service
   - Aggregate material costs
   - Aggregate labor costs
   - Aggregate scrap costs
   - **Estimate:** 5 hours

2. **Task 6.5.2:** Implement overhead calculation
   - Configure overhead percentage
   - Calculate overhead cost
   - Apply to total cost
   - **Estimate:** 3 hours

3. **Task 6.5.3:** Calculate total cost and revenue
   - Sum all cost components
   - Get job revenue (from invoice or contract)
   - Calculate profit/loss
   - **Estimate:** 4 hours

4. **Task 6.5.4:** Calculate profit margin
   - Calculate profit margin percentage
   - Calculate profit amount
   - Store profitability metrics
   - **Estimate:** 3 hours

5. **Task 6.5.5:** Create profitability endpoint
   - GET /api/v1/jobs/:id/profitability
   - Return cost breakdown
   - Return profit metrics
   - **Estimate:** 3 hours

6. **Task 6.5.6:** Implement continuous profitability updates
   - Update on material consumption
   - Update on time entry
   - Update on scrap entry
   - Trigger recalculation
   - **Estimate:** 5 hours

7. **Task 6.5.7:** Finalize profitability on job closure
   - Lock profitability at CLOSED
   - Store final profitability
   - Prevent further updates
   - **Estimate:** 3 hours

8. **Task 6.5.8:** Test profitability calculation
   - Test cost aggregation
   - Test overhead application
   - Test profit calculation
   - Test continuous updates
   - **Estimate:** 4 hours

---

### Story 6.6: Batch Readiness & Notifications

**Story Description:** Implement batch readiness tracking and customer notifications.

**Story Points:** 5  
**Priority:** High  
**Labels:** `backend`, `api`, `jobs`, `notifications`

**Acceptance Criteria:**
- Track batch quantities
- Mark batch ready for dispatch
- Notify customer when batch ready
- Customer confirmation for dispatch
- Dispatch decision logic

**Tasks:**
1. **Task 6.6.1:** Create batch tracking entity/model
   - Define data model
   - Store batch quantity
   - Link to job
   - Track batch status
   - **Estimate:** 2 hours

2. **Task 6.6.2:** Implement batch readiness service
   - Track completed quantities
   - Check batch threshold
   - Mark batch ready
   - **Estimate:** 4 hours

3. **Task 6.6.3:** Create batch readiness endpoint
   - POST /api/v1/jobs/:id/mark-ready
   - Mark batch ready for dispatch
   - Update batch status
   - **Estimate:** 3 hours

4. **Task 6.6.4:** Implement customer notification
   - Send notification when batch ready
   - Include batch details
   - Request customer confirmation
   - **Estimate:** 4 hours

5. **Task 6.6.5:** Implement dispatch decision logic
   - Check customer confirmation
   - Dispatch if confirmed
   - Hold if not confirmed
   - Continue production
   - **Estimate:** 4 hours

6. **Task 6.6.6:** Test batch readiness and notifications
   - Test batch tracking
   - Test readiness marking
   - Test notifications
   - Test dispatch logic
   - **Estimate:** 3 hours

---

### Story 6.7: Job Detail Frontend

**Story Description:** Build job detail page with progress tracking, materials, profitability, and actions.

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `frontend`, `ui`, `jobs`

**Acceptance Criteria:**
- Job information and status display
- Progress tracking visualization
- Materials consumed table
- Profitability metrics display
- Document attachments
- Activity timeline
- Quick actions (add entry, issue materials, update status)

**Tasks:**
1. **Task 6.7.1:** Create job detail page layout
   - Job information section
   - Status badge display
   - Progress bar component
   - **Estimate:** 4 hours

2. **Task 6.7.2:** Build materials consumed table
   - Display material entries
   - Show quantity and cost
   - Add material entry form
   - **Estimate:** 5 hours

3. **Task 6.7.3:** Build profitability metrics display
   - Cost breakdown cards
   - Profit margin display
   - Revenue and profit display
   - **Estimate:** 4 hours

4. **Task 6.7.4:** Implement document attachments section
   - Display linked documents
   - Upload new documents
   - Preview documents
   - **Estimate:** 4 hours

5. **Task 6.7.5:** Build activity timeline
   - Display status changes
   - Show material entries
   - Show time entries
   - Show scrap entries
   - **Estimate:** 5 hours

6. **Task 6.7.6:** Implement quick actions
   - Add material entry button
   - Issue materials button
   - Update status dropdown
   - Add time entry button
   - **Estimate:** 5 hours

7. **Task 6.7.7:** Test job detail page
   - Test all sections
   - Test quick actions
   - Test data display
   - **Estimate:** 3 hours

---

## EPIC 7: Workforce Management

**Epic Description:** Implement workforce management with employee profiles, attendance tracking, overtime management, and KYC document handling.

**Epic Goal:** Enable businesses to manage their workforce, track attendance, and maintain employee records.

**Acceptance Criteria:**
- Worker CRUD operations working
- Attendance tracking (30/30 format)
- Overtime tracking (days/hours)
- Employee profile management (phone, address, employment details)
- KYC document upload/view (Aadhar, PAN)
- Workforce statistics and reporting

---

### Story 7.1: Worker CRUD Operations

**Story Description:** Implement CRUD operations for workers/employees.

**Story Points:** 5  
**Priority:** High  
**Labels:** `backend`, `api`, `crud`

**Acceptance Criteria:**
- Create, read, update, delete workers
- Worker profile management
- Worker search and filtering
- Department assignment

**Tasks:**
1. **Task 7.1.1:** Create workforce entity/model
   - Define data model
   - Add validation decorators
   - Create DTOs for create/update
   - **Estimate:** 2 hours

2. **Task 7.1.2:** Create workforce service
   - Implement create method
   - Implement findAll with filters
   - Implement findOne
   - Implement update
   - Implement delete (soft delete)
   - **Estimate:** 4 hours

3. **Task 7.1.3:** Create workforce controller
   - POST /api/v1/workforce
   - GET /api/v1/workforce
   - GET /api/v1/workforce/:id
   - PUT /api/v1/workforce/:id
   - DELETE /api/v1/workforce/:id
   - **Estimate:** 3 hours

4. **Task 7.1.4:** Add search and filtering
   - Search by name, employee ID
   - Filter by department, status
   - Implement pagination
   - Add sorting
   - **Estimate:** 3 hours

5. **Task 7.1.5:** Test workforce APIs
   - Test all CRUD operations
   - Test search
   - Test filtering
   - **Estimate:** 2 hours

---

### Story 7.2: Attendance Tracking

**Story Description:** Implement attendance tracking with 30/30 format.

**Story Points:** 8  
**Priority:** High  
**Labels:** `backend`, `api`, `workforce`

**Acceptance Criteria:**
- Record attendance per worker
- 30/30 format display (days present / total days)
- Attendance calendar view
- Attendance reports

**Tasks:**
1. **Task 7.2.1:** Create attendance entity/model
   - Define data model
   - Link to worker
   - Store date and status
   - **Estimate:** 2 hours

2. **Task 7.2.2:** Create attendance service
   - Implement recordAttendance method
   - Calculate attendance statistics
   - Get attendance by month
   - **Estimate:** 5 hours

3. **Task 7.2.3:** Create attendance endpoints
   - POST /api/v1/workforce/:id/attendance
   - GET /api/v1/workforce/:id/attendance
   - Calculate 30/30 format
   - **Estimate:** 4 hours

4. **Task 7.2.4:** Implement 30/30 format calculation
   - Count days present in month
   - Count total days in month
   - Display as "X/30" format
   - **Estimate:** 3 hours

5. **Task 7.2.5:** Build attendance calendar UI
   - Monthly calendar view
   - Mark present/absent days
   - Display attendance summary
   - **Estimate:** 6 hours

6. **Task 7.2.6:** Create attendance reports
   - Monthly attendance report
   - Attendance by worker
   - Attendance statistics
   - **Estimate:** 4 hours

7. **Task 7.2.7:** Test attendance tracking
   - Test attendance recording
   - Test 30/30 calculation
   - Test calendar view
   - Test reports
   - **Estimate:** 3 hours

---

### Story 7.3: Overtime Tracking

**Story Description:** Implement overtime tracking for workers.

**Story Points:** 5  
**Priority:** Medium  
**Labels:** `backend`, `api`, `workforce`

**Acceptance Criteria:**
- Record overtime (days/hours)
- Record overtime (days/hours)
- Overtime calculation
- Overtime reports
- Overtime linked to jobs (optional)

**Tasks:**
1. **Task 7.3.1:** Create overtime entity/model
   - Define data model
   - Link to worker
   - Store overtime hours/days
   - Link to job (optional)
   - **Estimate:** 2 hours

2. **Task 7.3.2:** Create overtime service
   - Implement recordOvertime method
   - Calculate overtime totals
   - Get overtime by period
   - **Estimate:** 4 hours

3. **Task 7.3.3:** Create overtime endpoints
   - POST /api/v1/workforce/:id/overtime
   - GET /api/v1/workforce/:id/overtime
   - Calculate overtime statistics
   - **Estimate:** 3 hours

4. **Task 7.3.4:** Implement overtime calculation
   - Calculate total overtime hours
   - Calculate overtime days
   - Monthly overtime summary
   - **Estimate:** 3 hours

5. **Task 7.3.5:** Create overtime reports
   - Overtime by worker
   - Overtime by month
   - Overtime statistics
   - **Estimate:** 3 hours

6. **Task 7.3.6:** Test overtime tracking
   - Test overtime recording
   - Test calculation
   - Test reports
   - **Estimate:** 2 hours

---

### Story 7.4: Employee Profile Management

**Story Description:** Implement comprehensive employee profile management.

**Story Points:** 5  
**Priority:** High  
**Labels:** `backend`, `api`, `frontend`, `workforce`

**Acceptance Criteria:**
- Phone number management
- Address management
- Employment details
- Expandable profile view in UI

**Tasks:**
1. **Task 7.4.1:** Extend workforce model with profile fields
   - Add phone number field
   - Add address fields
   - Add employment details fields
   - **Estimate:** 2 hours

2. **Task 7.4.2:** Create profile update endpoints
   - PUT /api/v1/workforce/:id/profile
   - Update phone number
   - Update address
   - Update employment details
   - **Estimate:** 4 hours

3. **Task 7.4.3:** Build expandable profile UI
   - Collapsible profile section
   - Display basic info
   - Expand to show full profile
   - **Estimate:** 5 hours

4. **Task 7.4.4:** Create profile edit form
   - Phone number input
   - Address form
   - Employment details form
   - Save functionality
   - **Estimate:** 5 hours

5. **Task 7.4.5:** Test profile management
   - Test profile updates
   - Test expandable UI
   - Test form validation
   - **Estimate:** 2 hours

---

### Story 7.5: KYC Document Management

**Story Description:** Implement KYC document upload and viewing (Aadhar, PAN).

**Story Points:** 8  
**Priority:** High  
**Labels:** `backend`, `api`, `frontend`, `documents`, `workforce`

**Acceptance Criteria:**
- Upload KYC documents (Aadhar, PAN)
- View KYC documents
- Document type validation
- Secure document storage
- Document linked to worker profile

**Tasks:**
1. **Task 7.5.1:** Create KYC document entity/model
   - Define data model
   - Link to worker
   - Store document type (Aadhar, PAN)
   - Store document metadata
   - **Estimate:** 2 hours

2. **Task 7.5.2:** Create KYC document service
   - Implement uploadKYC method
   - Validate document type
   - Store document in S3
   - Link to worker profile
   - **Estimate:** 5 hours

3. **Task 7.5.3:** Create KYC document endpoints
   - POST /api/v1/workforce/:id/kyc
   - GET /api/v1/workforce/:id/kyc
   - GET /api/v1/workforce/:id/kyc/:docId
   - **Estimate:** 4 hours

4. **Task 7.5.4:** Implement document type validation
   - Validate file type (PDF, JPG, PNG)
   - Validate file size
   - Validate document type (Aadhar/PAN)
   - **Estimate:** 3 hours

5. **Task 7.5.5:** Build KYC document upload UI
   - Document upload component
   - Document type selector
   - Upload progress indicator
   - **Estimate:** 5 hours

6. **Task 7.5.6:** Build KYC document viewer
   - Display uploaded documents
   - View document preview
   - Download document
   - **Estimate:** 4 hours

7. **Task 7.5.7:** Test KYC document management
   - Test document upload
   - Test document viewing
   - Test validation
   - Test security
   - **Estimate:** 3 hours

---

### Story 7.6: Workforce Statistics & Reporting

**Story Description:** Implement workforce statistics and reporting.

**Story Points:** 5  
**Priority:** Medium  
**Labels:** `backend`, `api`, `reports`, `workforce`

**Acceptance Criteria:**
- Workforce statistics dashboard
- Attendance reports
- Overtime reports
- Workforce analytics

**Tasks:**
1. **Task 7.6.1:** Create workforce statistics service
   - Calculate total workers
   - Calculate active workers
   - Calculate attendance statistics
   - Calculate overtime statistics
   - **Estimate:** 4 hours

2. **Task 7.6.2:** Create workforce stats endpoint
   - GET /api/v1/workforce/stats
   - Return statistics
   - Include attendance metrics
   - Include overtime metrics
   - **Estimate:** 3 hours

3. **Task 7.6.3:** Create attendance reports API
   - GET /api/v1/reports/attendance
   - Filter by date range
   - Filter by worker
   - **Estimate:** 3 hours

4. **Task 7.6.4:** Create overtime reports API
   - GET /api/v1/reports/overtime
   - Filter by date range
   - Filter by worker
   - **Estimate:** 3 hours

5. **Task 7.6.5:** Test workforce statistics and reports
   - Test statistics calculation
   - Test reports
   - Test filtering
   - **Estimate:** 2 hours

---

## EPIC 8: Scrap Management

**Epic Description:** Implement scrap tracking and management system with pricing, history, and analytics.

**Epic Goal:** Track scrap generation across jobs, maintain pricing, and provide insights on scrap trends.

**Acceptance Criteria:**
- Scrap entry CRUD operations working
- Scrap pricing management (per type, per kg/ton)
- Scrap submission history tracking
- Scrap analytics dashboard (line graph, stats)
- Scrap linked to jobs/batches
- Scrap value calculation

---

### Story 8.1: Scrap Entry CRUD Operations

**Story Description:** Implement CRUD operations for scrap entries.

**Story Points:** 5  
**Priority:** High  
**Labels:** `backend`, `api`, `crud`, `scrap`

**Acceptance Criteria:**
- Create, read, update, delete scrap entries
- Scrap linked to jobs/batches
- Scrap type categorization
- Scrap quantity recording (kg/ton)

**Tasks:**
1. **Task 8.1.1:** Create scrap entry entity/model
   - Define data model
   - Link to job
   - Store scrap type
   - Store quantity (kg/ton)
   - **Estimate:** 2 hours

2. **Task 8.1.2:** Create scrap service
   - Implement create method
   - Implement findAll with filters
   - Implement findOne
   - Implement update
   - Implement delete
   - **Estimate:** 4 hours

3. **Task 8.1.3:** Create scrap controller
   - POST /api/v1/scrap
   - GET /api/v1/scrap
   - GET /api/v1/scrap/:id
   - PUT /api/v1/scrap/:id
   - DELETE /api/v1/scrap/:id
   - **Estimate:** 3 hours

4. **Task 8.1.4:** Link scrap to jobs/batches
   - Link scrap entry to job
   - Link to batch (optional)
   - Display scrap in job details
   - **Estimate:** 3 hours

5. **Task 8.1.5:** Add scrap type categorization
   - Define scrap types
   - Assign type to scrap entry
   - Filter by type
   - **Estimate:** 2 hours

6. **Task 8.1.6:** Test scrap entry APIs
   - Test all CRUD operations
   - Test job linking
   - Test type categorization
   - **Estimate:** 2 hours

---

### Story 8.2: Scrap Pricing Management

**Story Description:** Implement scrap pricing management per type.

**Story Points:** 5  
**Priority:** High  
**Labels:** `backend`, `api`, `scrap`

**Acceptance Criteria:**
- Configure scrap pricing per type
- Pricing per kg/ton
- Update scrap pricing
- Scrap value calculation

**Tasks:**
1. **Task 8.2.1:** Create scrap pricing entity/model
   - Define data model
   - Store scrap type
   - Store price per kg
   - Store price per ton
   - **Estimate:** 2 hours

2. **Task 8.2.2:** Create scrap pricing service
   - Implement getPricing method
   - Implement updatePricing method
   - Get pricing by type
   - **Estimate:** 3 hours

3. **Task 8.2.3:** Create scrap pricing endpoints
   - GET /api/v1/scrap/pricing
   - PUT /api/v1/scrap/pricing
   - Update pricing per type
   - **Estimate:** 3 hours

4. **Task 8.2.4:** Implement scrap value calculation
   - Calculate value from quantity and price
   - Use kg or ton pricing
   - Update scrap entry value
   - **Estimate:** 3 hours

5. **Task 8.2.5:** Test scrap pricing management
   - Test pricing configuration
   - Test value calculation
   - Test pricing updates
   - **Estimate:** 2 hours

---

### Story 8.3: Scrap Submission History

**Story Description:** Implement scrap submission history tracking.

**Story Points:** 5  
**Priority:** Medium  
**Labels:** `backend`, `api`, `scrap`, `reports`

**Acceptance Criteria:**
- Track scrap submissions over time
- History data for line graph
- Recent submissions list
- Scrap submission by date

**Tasks:**
1. **Task 8.3.1:** Create scrap history service
   - Aggregate scrap by date
   - Calculate total tons per date
   - Get recent submissions
   - **Estimate:** 4 hours

2. **Task 8.3.2:** Create scrap history endpoint
   - GET /api/v1/scrap/history
   - Filter by date range
   - Return data for line graph
   - Include total tons per date
   - **Estimate:** 4 hours

3. **Task 8.3.3:** Implement recent submissions list
   - Get last N submissions
   - Include submission details
   - Sort by date descending
   - **Estimate:** 3 hours

4. **Task 8.3.4:** Test scrap history
   - Test history aggregation
   - Test date filtering
   - Test recent submissions
   - **Estimate:** 2 hours

---

### Story 8.4: Scrap Analytics Dashboard

**Story Description:** Build scrap analytics dashboard with line graph and statistics.

**Story Points:** 8  
**Priority:** High  
**Labels:** `backend`, `frontend`, `scrap`, `reports`, `analytics`

**Acceptance Criteria:**
- Scrap stats dashboard (total scrap, value, entries, avg price)
- Line graph showing tons over time
- Scrap pricing table display
- Recent submissions sidebar
- Scrap entries table

**Tasks:**
1. **Task 8.4.1:** Create scrap stats service
   - Calculate total scrap (tons)
   - Calculate total scrap value
   - Calculate total entries
   - Calculate average price
   - **Estimate:** 4 hours

2. **Task 8.4.2:** Create scrap stats endpoint
   - GET /api/v1/scrap/stats
   - Return all statistics
   - Include calculated metrics
   - **Estimate:** 3 hours

3. **Task 8.4.3:** Build stats dashboard UI
   - Stats cards component
   - Display total scrap
   - Display total value
   - Display entries count
   - Display avg price
   - **Estimate:** 5 hours

4. **Task 8.4.4:** Build line graph component
   - Integrate charting library
   - Display tons over time
   - Date range selector
   - **Estimate:** 6 hours

5. **Task 8.4.5:** Build scrap pricing table
   - Display pricing per type
   - Show price per kg/ton
   - Allow editing (if authorized)
   - **Estimate:** 4 hours

6. **Task 8.4.6:** Build recent submissions sidebar
   - Display recent submissions
   - Show submission details
   - Link to job details
   - **Estimate:** 4 hours

7. **Task 8.4.7:** Build scrap entries table
   - Display all scrap entries
   - Filter by type, job, date
   - Show quantity and value
   - **Estimate:** 5 hours

8. **Task 8.4.8:** Test scrap analytics dashboard
   - Test stats calculation
   - Test line graph
   - Test all components
   - **Estimate:** 3 hours

---

## EPIC 9: Expense Tracker

**Epic Description:** Implement expense tracking system for accountants/owners to maintain all organizational expenses.

**Epic Goal:** Enable businesses to track and categorize expenses with vendor information and payment status.

**Acceptance Criteria:**
- Expense CRUD operations working
- Expense categorization (Raw Materials, Labor, Utilities, Maintenance, Transport, Other)
- Vendor information tracking
- Payment status management (Paid, Pending, Overdue)
- Expense statistics and reporting
- Monthly expense tracking

---

### Story 9.1: Expense CRUD Operations

**Story Description:** Implement CRUD operations for expenses.

**Story Points:** 5  
**Priority:** High  
**Labels:** `backend`, `api`, `crud`, `expenses`

**Acceptance Criteria:**
- Create, read, update, delete expenses
- Expense date, amount, description
- Expense search and filtering
- Expense linked to categories

**Tasks:**
1. **Task 9.1.1:** Create expense entity/model
   - Define data model
   - Add validation decorators
   - Create DTOs for create/update
   - **Estimate:** 2 hours

2. **Task 9.1.2:** Create expense service
   - Implement create method
   - Implement findAll with filters
   - Implement findOne
   - Implement update
   - Implement delete
   - **Estimate:** 4 hours

3. **Task 9.1.3:** Create expense controller
   - POST /api/v1/expenses
   - GET /api/v1/expenses
   - GET /api/v1/expenses/:id
   - PUT /api/v1/expenses/:id
   - DELETE /api/v1/expenses/:id
   - **Estimate:** 3 hours

4. **Task 9.1.4:** Add search and filtering
   - Search by description, vendor
   - Filter by category, status, date range
   - Implement pagination
   - Add sorting
   - **Estimate:** 3 hours

5. **Task 9.1.5:** Test expense APIs
   - Test all CRUD operations
   - Test search
   - Test filtering
   - **Estimate:** 2 hours

---

### Story 9.2: Expense Categorization

**Story Description:** Implement expense categorization system.

**Story Points:** 3  
**Priority:** High  
**Labels:** `backend`, `api`, `expenses`

**Acceptance Criteria:**
- Expense categories (Raw Materials, Labor, Utilities, Maintenance, Transport, Other)
- Category assignment
- Category-based filtering
- Category statistics

**Tasks:**
1. **Task 9.2.1:** Define expense categories
   - Create category enum/constants
   - Define category list
   - Add category descriptions
   - **Estimate:** 1 hour

2. **Task 9.2.2:** Add category to expense model
   - Add category field
   - Validate category
   - **Estimate:** 1 hour

3. **Task 9.2.3:** Create category endpoints
   - GET /api/v1/expenses/categories
   - Return available categories
   - **Estimate:** 1 hour

4. **Task 9.2.4:** Implement category-based filtering
   - Filter expenses by category
   - Category statistics calculation
   - **Estimate:** 2 hours

5. **Task 9.2.5:** Test expense categorization
   - Test category assignment
   - Test category filtering
   - Test category statistics
   - **Estimate:** 1 hour

---

### Story 9.3: Vendor Information Tracking

**Story Description:** Implement vendor information tracking for expenses.

**Story Points:** 5  
**Priority:** Medium  
**Labels:** `backend`, `api`, `expenses`

**Acceptance Criteria:**
- Vendor name and details
- Vendor linked to expenses
- Vendor list and search
- Vendor statistics

**Tasks:**
1. **Task 9.3.1:** Add vendor fields to expense model
   - Add vendor_name field
   - Add vendor_details field (optional)
   - **Estimate:** 1 hour

2. **Task 9.3.2:** Create vendor service
   - Extract unique vendors
   - Get vendor list
   - Calculate vendor statistics
   - **Estimate:** 3 hours

3. **Task 9.3.3:** Create vendor endpoints
   - GET /api/v1/expenses/vendors
   - Return vendor list
   - Include vendor statistics
   - **Estimate:** 3 hours

4. **Task 9.3.4:** Implement vendor search
   - Search vendors by name
   - Filter expenses by vendor
   - **Estimate:** 2 hours

5. **Task 9.3.5:** Test vendor tracking
   - Test vendor extraction
   - Test vendor list
   - Test vendor statistics
   - **Estimate:** 2 hours

---

### Story 9.4: Payment Status Management

**Story Description:** Implement payment status tracking for expenses.

**Story Points:** 5  
**Priority:** High  
**Labels:** `backend`, `api`, `expenses`

**Acceptance Criteria:**
- Payment status (Paid, Pending, Overdue)
- Update payment status
- Payment date tracking
- Payment status filtering

**Tasks:**
1. **Task 9.4.1:** Add payment fields to expense model
   - Add payment_status field
   - Add payment_date field
   - Add due_date field (optional)
   - **Estimate:** 1 hour

2. **Task 9.4.2:** Implement payment status logic
   - Calculate overdue status
   - Update payment status
   - Track payment date
   - **Estimate:** 4 hours

3. **Task 9.4.3:** Create payment status update endpoint
   - PATCH /api/v1/expenses/:id/payment-status
   - Update payment status
   - Update payment date
   - **Estimate:** 3 hours

4. **Task 9.4.4:** Implement payment status filtering
   - Filter by payment status
   - Filter overdue expenses
   - **Estimate:** 2 hours

5. **Task 9.4.5:** Test payment status management
   - Test status updates
   - Test overdue calculation
   - Test filtering
   - **Estimate:** 2 hours

---

### Story 9.5: Expense Statistics & Reporting

**Story Description:** Implement expense statistics and reporting dashboard.

**Story Points:** 8  
**Priority:** High  
**Labels:** `backend`, `frontend`, `expenses`, `reports`

**Acceptance Criteria:**
- Expense stats dashboard (total expenses, entries, pending payments, avg monthly)
- Monthly expense tracking
- Category-wise expense reports
- Payment status reports
- Export capabilities

**Tasks:**
1. **Task 9.5.1:** Create expense statistics service
   - Calculate total expenses
   - Calculate total entries
   - Calculate pending payments
   - Calculate average monthly expenses
   - **Estimate:** 5 hours

2. **Task 9.5.2:** Create expense stats endpoint
   - GET /api/v1/expenses/stats
   - Return all statistics
   - Include monthly averages
   - **Estimate:** 3 hours

3. **Task 9.5.3:** Implement monthly expense tracking
   - Aggregate expenses by month
   - Calculate monthly totals
   - Track monthly trends
   - **Estimate:** 4 hours

4. **Task 9.5.4:** Create category-wise reports
   - Aggregate expenses by category
   - Calculate category totals
   - Category percentage breakdown
   - **Estimate:** 4 hours

5. **Task 9.5.5:** Create payment status reports
   - Aggregate by payment status
   - Calculate pending amounts
   - Calculate overdue amounts
   - **Estimate:** 3 hours

6. **Task 9.5.6:** Build expense stats dashboard UI
   - Stats cards component
   - Display total expenses
   - Display entries count
   - Display pending payments
   - Display avg monthly
   - **Estimate:** 6 hours

7. **Task 9.5.7:** Add export capabilities
   - Export to CSV
   - Export to PDF
   - Include all report data
   - **Estimate:** 4 hours

8. **Task 9.5.8:** Test expense statistics and reports
   - Test statistics calculation
   - Test monthly tracking
   - Test category reports
   - Test exports
   - **Estimate:** 3 hours

---

## EPIC 10: Document Management & Storage

**Epic Description:** Implement document upload, storage, versioning, and management for designs, invoices, and job attachments.

**Epic Goal:** Enable secure, tenant-isolated document storage with versioning and lifecycle management.

**Acceptance Criteria:**
- Document upload to S3/R2/MinIO working
- Document metadata stored in PostgreSQL
- Pre-signed URLs for secure access
- Document versioning for design files
- Document linking to entities (jobs, invoices, customers, order requests)
- Document preview/viewer functional
- Lifecycle policies (retention, archival)

---

### Story 10.1: Document Upload & Storage

**Story Description:** Implement document upload functionality with S3/R2/MinIO storage and metadata management.

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `backend`, `api`, `storage`, `s3`, `documents`

**Acceptance Criteria:**
- Upload documents to S3/R2/MinIO
- Store document metadata in database
- Support multiple file types (PDF, images, CAD files)
- Validate file size and type
- Organize by tenant folders
- Tenant isolation at storage level

**Tasks:**
1. **Task 10.1.1:** Configure S3/R2/MinIO client
   - Set up AWS SDK or MinIO client
   - Configure bucket/container
   - Set up IAM permissions
   - **Estimate:** 3 hours

2. **Task 10.1.2:** Create document upload endpoint
   - POST /api/v1/documents/upload
   - Accept multipart/form-data
   - Validate file type and size
   - **Estimate:** 4 hours

3. **Task 10.1.3:** Implement S3/R2/MinIO upload logic
   - Generate unique S3 key
   - Upload to tenant-specific folder
   - Set proper content type
   - Generate storage path
   - **Estimate:** 5 hours

4. **Task 10.1.4:** Store document metadata
   - Save to documents table
   - Link to entity (job, invoice, customer, order request)
   - Store file information (name, size, type)
   - Track upload user
   - **Estimate:** 3 hours

5. **Task 10.1.5:** Implement file validation
   - Check file type (PDF, JPG, PNG, CAD files)
   - Validate file size limits
   - Scan for viruses (optional)
   - **Estimate:** 4 hours

6. **Task 10.1.6:** Organize by tenant folders
   - Create tenant-specific folder structure
   - Organize by entity type
   - Maintain folder hierarchy
   - **Estimate:** 3 hours

7. **Task 10.1.7:** Test document upload
   - Test file upload
   - Test S3 storage
   - Test metadata storage
   - Test validation
   - **Estimate:** 3 hours

---

### Story 10.2: Pre-signed URLs & Access Control

**Story Description:** Implement pre-signed URLs for secure document access.

**Story Points:** 5  
**Priority:** High  
**Labels:** `backend`, `api`, `security`, `documents`

**Acceptance Criteria:**
- Generate pre-signed URLs for download
- Generate pre-signed URLs for preview
- URL expiration configuration
- Access control validation
- Audit logging for document access

**Tasks:**
1. **Task 10.2.1:** Implement pre-signed URL generation
   - Generate download URL
   - Generate preview URL
   - Configure expiration time
   - **Estimate:** 4 hours

2. **Task 10.2.2:** Create document download endpoint
   - GET /api/v1/documents/:id/download
   - Generate pre-signed URL
   - Verify access permissions
   - **Estimate:** 3 hours

3. **Task 10.2.3:** Create document preview endpoint
   - GET /api/v1/documents/:id/preview
   - Generate pre-signed preview URL
   - Verify access permissions
   - **Estimate:** 3 hours

4. **Task 10.2.4:** Implement access control validation
   - Check user permissions
   - Verify tenant access
   - Validate entity access
   - **Estimate:** 4 hours

5. **Task 10.2.5:** Add audit logging for document access
   - Log document downloads
   - Log document previews
   - Store access metadata
   - **Estimate:** 3 hours

6. **Task 10.2.6:** Test pre-signed URLs and access control
   - Test URL generation
   - Test expiration
   - Test access control
   - Test audit logging
   - **Estimate:** 3 hours

---

### Story 10.3: Document Versioning

**Story Description:** Implement document versioning for design files.

**Story Points:** 8  
**Priority:** High  
**Labels:** `backend`, `api`, `documents`

**Acceptance Criteria:**
- Upload new version of design file
- List all versions of a document
- Version history tracking
- Access specific version
- Version metadata storage

**Tasks:**
1. **Task 10.3.1:** Extend document model for versioning
   - Add version field
   - Add parent_document_id field
   - Track version chain
   - **Estimate:** 2 hours

2. **Task 10.3.2:** Implement version upload service
   - Create new version entry
   - Increment version number
   - Link to parent document
   - Store version metadata
   - **Estimate:** 5 hours

3. **Task 10.3.3:** Create version upload endpoint
   - POST /api/v1/documents/:id/versions
   - Upload new version
   - Store version metadata
   - **Estimate:** 3 hours

4. **Task 10.3.4:** Create version list endpoint
   - GET /api/v1/documents/:id/versions
   - List all versions
   - Include version metadata
   - **Estimate:** 3 hours

5. **Task 10.3.5:** Implement version access
   - Access specific version
   - Generate pre-signed URL for version
   - Display version details
   - **Estimate:** 4 hours

6. **Task 10.3.6:** Build version history UI
   - Display version list
   - Show version details
   - Allow version selection
   - **Estimate:** 5 hours

7. **Task 10.3.7:** Test document versioning
   - Test version upload
   - Test version listing
   - Test version access
   - **Estimate:** 3 hours

---

### Story 10.4: Document Linking to Entities

**Story Description:** Implement document linking to various entities (jobs, invoices, customers, order requests).

**Story Points:** 5  
**Priority:** High  
**Labels:** `backend`, `api`, `documents`

**Acceptance Criteria:**
- Link documents to jobs
- Link documents to invoices
- Link documents to customers
- Link documents to order requests
- Polymorphic document linking
- List documents by entity

**Tasks:**
1. **Task 10.4.1:** Implement polymorphic document linking
   - Add entity_type field
   - Add entity_id field
   - Support multiple entity types
   - **Estimate:** 3 hours

2. **Task 10.4.2:** Create entity-specific upload endpoints
   - POST /api/v1/jobs/:id/documents
   - POST /api/v1/invoices/:id/documents
   - POST /api/v1/order-requests/:id/documents
   - Link document to entity
   - **Estimate:** 5 hours

3. **Task 10.4.3:** Create entity-specific document list endpoints
   - GET /api/v1/jobs/:id/documents
   - GET /api/v1/invoices/:id/documents
   - GET /api/v1/order-requests/:id/documents
   - Filter documents by entity
   - **Estimate:** 4 hours

4. **Task 10.4.4:** Implement document search by entity
   - Query documents by entity_type and entity_id
   - Support multiple entity types
   - Optimize queries
   - **Estimate:** 3 hours

5. **Task 10.4.5:** Test document linking
   - Test linking to jobs
   - Test linking to invoices
   - Test linking to customers
   - Test linking to order requests
   - Test document listing
   - **Estimate:** 3 hours

---

### Story 10.5: Document Preview & Viewer

**Story Description:** Implement document preview and viewer functionality.

**Story Points:** 8  
**Priority:** High  
**Labels:** `frontend`, `ui`, `documents`

**Acceptance Criteria:**
- In-browser preview for PDFs
- Image viewer
- Document metadata display
- Download functionality
- Thumbnail generation (optional)

**Tasks:**
1. **Task 10.5.1:** Create document viewer component
   - Integrate PDF.js for PDF viewing
   - Display images
   - Add zoom controls
   - **Estimate:** 6 hours

2. **Task 10.5.2:** Build document metadata display
   - Display file name
   - Display file size
   - Display upload date
   - Display uploader
   - **Estimate:** 3 hours

3. **Task 10.5.3:** Implement download functionality
   - Download button
   - Generate download URL
   - Handle download
   - **Estimate:** 3 hours

4. **Task 10.5.4:** Add thumbnail generation (optional)
   - Generate thumbnails for images
   - Generate thumbnails for PDFs
   - Store thumbnails in S3
   - Display thumbnails in lists
   - **Estimate:** 5 hours

5. **Task 10.5.5:** Create document list component
   - Display document list
   - Show thumbnails
   - Link to viewer
   - **Estimate:** 4 hours

6. **Task 10.5.6:** Test document preview and viewer
   - Test PDF viewing
   - Test image viewing
   - Test metadata display
   - Test download
   - **Estimate:** 3 hours

---

### Story 10.6: Document Lifecycle Management

**Story Description:** Implement document lifecycle policies (retention, archival).

**Story Points:** 5  
**Priority:** Medium  
**Labels:** `backend`, `api`, `documents`, `storage`

**Acceptance Criteria:**
- Retention policies per document type
- Automated archival
- Lifecycle policy configuration
- Document deletion (soft/hard)

**Tasks:**
1. **Task 10.6.1:** Define document lifecycle policies
   - Retention policy per type
   - Archival policy
   - Deletion policy
   - **Estimate:** 2 hours

2. **Task 10.6.2:** Implement retention tracking
   - Track document age
   - Check retention policies
   - Flag documents for archival
   - **Estimate:** 4 hours

3. **Task 10.6.3:** Implement automated archival
   - Archive old documents
   - Move to archive storage tier
   - Update document status
   - **Estimate:** 4 hours

4. **Task 10.6.4:** Implement document deletion
   - Soft delete (mark as deleted)
   - Hard delete (remove from storage)
   - Handle deletion based on policy
   - **Estimate:** 4 hours

5. **Task 10.6.5:** Create lifecycle policy configuration
   - Configure retention per type
   - Configure archival rules
   - Configure deletion rules
   - **Estimate:** 3 hours

6. **Task 10.6.6:** Test document lifecycle management
   - Test retention policies
   - Test archival
   - Test deletion
   - **Estimate:** 3 hours

---

## EPIC 11: Sales Orders & Invoicing

**Epic Description:** Implement sales order management and invoice generation with basic GST support.

**Epic Goal:** Enable businesses to create sales orders, generate invoices, and manage delivery.

**Acceptance Criteria:**
- Sales order CRUD operations working
- Invoice generation functional
- Invoice PDF generation
- Basic GST calculation
- Invoice-to-job linking
- Delivery note generation

---

### Story 11.1: Sales Order Management

**Story Description:** Implement sales order CRUD operations.

**Story Points:** 5  
**Priority:** High  
**Labels:** `backend`, `api`, `crud`, `sales`

**Acceptance Criteria:**
- Create, read, update sales orders
- Sales order linked to customer
- Sales order linked to jobs
- Sales order status tracking

**Tasks:**
1. **Task 11.1.1:** Create sales order entity/model
   - Define data model
   - Add validation decorators
   - Create DTOs for create/update
   - Link to customer and jobs
   - **Estimate:** 3 hours

2. **Task 11.1.2:** Create sales order service
   - Implement create method
   - Implement findAll with filters
   - Implement findOne
   - Implement update
   - **Estimate:** 4 hours

3. **Task 11.1.3:** Create sales order controller
   - POST /api/v1/sales-orders
   - GET /api/v1/sales-orders
   - GET /api/v1/sales-orders/:id
   - PUT /api/v1/sales-orders/:id
   - **Estimate:** 3 hours

4. **Task 11.1.4:** Link sales order to customer and jobs
   - Link to customer on creation
   - Link to jobs
   - Display linked entities
   - **Estimate:** 3 hours

5. **Task 11.1.5:** Implement sales order status tracking
   - Define status enum
   - Track status changes
   - Status history
   - **Estimate:** 3 hours

6. **Task 11.1.6:** Test sales order APIs
   - Test all CRUD operations
   - Test customer linking
   - Test job linking
   - Test status tracking
   - **Estimate:** 2 hours

---

### Story 11.2: Invoice Generation

**Story Description:** Implement invoice generation from jobs/sales orders.

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `backend`, `api`, `invoicing`

**Acceptance Criteria:**
- Generate invoice from job
- Generate invoice from sales order
- Invoice line items
- Invoice totals calculation
- Invoice number generation

**Tasks:**
1. **Task 11.2.1:** Create invoice entity/model
   - Define data model
   - Link to job or sales order
   - Store invoice line items
   - Store invoice totals
   - **Estimate:** 3 hours

2. **Task 11.2.2:** Create invoice service
   - Implement generateFromJob method
   - Implement generateFromSalesOrder method
   - Calculate invoice totals
   - Generate invoice number
   - **Estimate:** 6 hours

3. **Task 11.2.3:** Implement invoice line items
   - Create line items from job items
   - Create line items from sales order
   - Calculate line item totals
   - **Estimate:** 4 hours

4. **Task 11.2.4:** Implement invoice totals calculation
   - Calculate subtotal
   - Calculate tax totals
   - Calculate grand total
   - **Estimate:** 3 hours

5. **Task 11.2.5:** Implement invoice number generation
   - Generate unique invoice number
   - Format: INV-YYYY-MM-XXXX
   - Ensure uniqueness
   - **Estimate:** 2 hours

6. **Task 11.2.6:** Create invoice controller
   - POST /api/v1/invoices
   - GET /api/v1/invoices
   - GET /api/v1/invoices/:id
   - **Estimate:** 3 hours

7. **Task 11.2.7:** Test invoice generation
   - Test generate from job
   - Test generate from sales order
   - Test line items
   - Test totals calculation
   - **Estimate:** 3 hours

---

### Story 11.3: Invoice PDF Generation

**Story Description:** Implement invoice PDF generation.

**Story Points:** 8  
**Priority:** High  
**Labels:** `backend`, `api`, `invoicing`, `pdf`

**Acceptance Criteria:**
- Generate invoice PDF
- PDF template with company details
- PDF includes line items and totals
- PDF stored in object storage
- PDF download endpoint

**Tasks:**
1. **Task 11.3.1:** Set up PDF generation library
   - Install PDF library (PDFKit, Puppeteer, etc.)
   - Configure PDF generation
   - **Estimate:** 2 hours

2. **Task 11.3.2:** Create invoice PDF template
   - Design invoice template
   - Include company details
   - Include customer details
   - Include invoice header
   - **Estimate:** 5 hours

3. **Task 11.3.3:** Implement PDF generation service
   - Generate PDF from invoice data
   - Populate template with data
   - Include line items table
   - Include totals section
   - **Estimate:** 6 hours

4. **Task 11.3.4:** Store PDF in object storage
   - Upload PDF to S3/R2/MinIO
   - Store PDF path in database
   - Link PDF to invoice
   - **Estimate:** 3 hours

5. **Task 11.3.5:** Create PDF download endpoint
   - GET /api/v1/invoices/:id/pdf
   - Generate pre-signed URL
   - Return PDF download link
   - **Estimate:** 3 hours

6. **Task 11.3.6:** Test invoice PDF generation
   - Test PDF generation
   - Test template rendering
   - Test PDF storage
   - Test PDF download
   - **Estimate:** 3 hours

---

### Story 11.4: Basic GST Calculation

**Story Description:** Implement basic GST calculation for invoices.

**Story Points:** 5  
**Priority:** High  
**Labels:** `backend`, `api`, `gst`, `invoicing`

**Acceptance Criteria:**
- GST calculation (CGST, SGST, IGST)
- HSN code support
- GST rate configuration
- GST breakdown in invoice

**Tasks:**
1. **Task 11.4.1:** Create GST calculation service
   - Implement CGST calculation
   - Implement SGST calculation
   - Implement IGST calculation
   - Determine GST type based on state
   - **Estimate:** 5 hours

2. **Task 11.4.2:** Add HSN code support
   - Add HSN code to items
   - Validate HSN code format
   - Link HSN to GST rate
   - **Estimate:** 3 hours

3. **Task 11.4.3:** Implement GST rate configuration
   - Configure GST rates per HSN
   - Store GST rates
   - Update GST rates
   - **Estimate:** 3 hours

4. **Task 11.4.4:** Calculate GST for invoice line items
   - Apply GST rate per line item
   - Calculate CGST/SGST/IGST per item
   - Sum GST totals
   - **Estimate:** 4 hours

5. **Task 11.4.5:** Add GST breakdown to invoice
   - Display CGST breakdown
   - Display SGST breakdown
   - Display IGST breakdown
   - Display total GST
   - **Estimate:** 3 hours

6. **Task 11.4.6:** Test GST calculation
   - Test CGST calculation
   - Test SGST calculation
   - Test IGST calculation
   - Test HSN code handling
   - **Estimate:** 3 hours

---

### Story 11.5: Delivery Note Generation

**Story Description:** Implement delivery note generation.

**Story Points:** 5  
**Priority:** Medium  
**Labels:** `backend`, `api`, `invoicing`

**Acceptance Criteria:**
- Generate delivery note
- Delivery note PDF
- Delivery note linked to invoice
- Delivery note linked to dispatch

**Tasks:**
1. **Task 11.5.1:** Create delivery note entity/model
   - Define data model
   - Link to invoice
   - Link to dispatch
   - Store delivery details
   - **Estimate:** 2 hours

2. **Task 11.5.2:** Create delivery note service
   - Implement generateDeliveryNote method
   - Extract items from invoice
   - Include delivery address
   - **Estimate:** 4 hours

3. **Task 11.5.3:** Create delivery note PDF template
   - Design delivery note template
   - Include delivery details
   - Include items list
   - **Estimate:** 4 hours

4. **Task 11.5.4:** Generate delivery note PDF
   - Generate PDF from template
   - Store PDF in object storage
   - Link PDF to delivery note
   - **Estimate:** 3 hours

5. **Task 11.5.5:** Create delivery note endpoints
   - POST /api/v1/delivery-notes
   - GET /api/v1/delivery-notes/:id
   - GET /api/v1/delivery-notes/:id/pdf
   - **Estimate:** 3 hours

6. **Task 11.5.6:** Test delivery note generation
   - Test delivery note creation
   - Test PDF generation
   - Test invoice linking
   - Test dispatch linking
   - **Estimate:** 2 hours

---

## EPIC 12: GST e-Invoice Integration

**Epic Description:** Implement GST e-Invoice integration for IRN generation, QR code, and compliance.

**Epic Goal:** Enable businesses to generate compliant GST e-Invoices with IRN and QR codes.

**Acceptance Criteria:**
- e-Invoice API provider integration (GSP/ASP)
- IRN generation functional
- QR code generation and embedding
- Signed invoice payload storage
- ACK handling and error management
- Idempotent e-Invoice calls
- Retry mechanism for failures
- Invoice PDF with IRN + QR

---

### Story 12.1: e-Invoice Provider Integration

**Story Description:** Implement e-Invoice API provider integration (GSP/ASP) with abstraction layer.

**Story Points:** 13  
**Priority:** Critical  
**Labels:** `backend`, `api`, `gst`, `integration`

**Acceptance Criteria:**
- e-Invoice provider interface/abstraction
- Support for multiple providers (swappable)
- Provider configuration
- API client implementation
- Error handling

**Tasks:**
1. **Task 12.1.1:** Design e-Invoice provider interface
   - Define common methods
   - Define request/response types
   - Document interface
   - **Estimate:** 4 hours

2. **Task 12.1.2:** Implement provider abstraction layer
   - Create provider interface
   - Create base provider class
   - Define provider contract
   - **Estimate:** 5 hours

3. **Task 12.1.3:** Implement first provider (e.g., ClearTax, Tally)
   - Create provider implementation
   - Implement API client
   - Handle authentication
   - **Estimate:** 8 hours

4. **Task 12.1.4:** Create provider factory
   - Factory to create provider instance
   - Load provider from config
   - Switch providers dynamically
   - **Estimate:** 4 hours

5. **Task 12.1.5:** Add provider configuration
   - Environment variables for provider
   - Provider API credentials
   - Provider endpoint URLs
   - **Estimate:** 3 hours

6. **Task 12.1.6:** Implement error handling
   - Handle API errors
   - Handle network errors
   - Handle timeout errors
   - Return appropriate error messages
   - **Estimate:** 4 hours

7. **Task 12.1.7:** Test provider integration
   - Test provider selection
   - Test API calls
   - Test error handling
   - Test provider switching
   - **Estimate:** 5 hours

---

### Story 12.2: IRN Generation

**Story Description:** Implement IRN (Invoice Reference Number) generation.

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `backend`, `api`, `gst`

**Acceptance Criteria:**
- Generate IRN for invoice
- Validate invoice data before IRN generation
- IRN generation API call
- Store IRN in database
- IRN status tracking

**Tasks:**
1. **Task 12.2.1:** Create e-Invoice data model
   - Define einvoices table
   - Store IRN, ACK details
   - Link to invoice
   - Store status
   - **Estimate:** 3 hours

2. **Task 12.2.2:** Implement invoice data validation
   - Validate GSTIN
   - Validate HSN codes
   - Validate tax breakdown
   - Validate invoice totals
   - **Estimate:** 5 hours

3. **Task 12.2.3:** Create e-Invoice payload builder
   - Build e-Invoice JSON payload
   - Format according to e-Invoice schema
   - Include all required fields
   - **Estimate:** 6 hours

4. **Task 12.2.4:** Implement IRN generation API call
   - Call provider API
   - Send e-Invoice payload
   - Handle response
   - Extract IRN from response
   - **Estimate:** 5 hours

5. **Task 12.2.5:** Store IRN and status
   - Store IRN in database
   - Store ACK details
   - Update invoice status
   - Track IRN generation status
   - **Estimate:** 3 hours

6. **Task 12.2.6:** Create IRN generation endpoint
   - POST /api/v1/invoices/:id/generate-einvoice
   - Trigger IRN generation
   - Return IRN status
   - **Estimate:** 3 hours

7. **Task 12.2.7:** Test IRN generation
   - Test data validation
   - Test payload building
   - Test API call
   - Test IRN storage
   - **Estimate:** 4 hours

---

### Story 12.3: QR Code Generation & Embedding

**Story Description:** Implement QR code generation and embedding in invoice PDF.

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `backend`, `api`, `gst`, `pdf`

**Acceptance Criteria:**
- Generate QR code from invoice data
- Generate QR code from invoice data
- Embed QR code in invoice PDF
- QR code format compliance
- QR code positioning in PDF

**Tasks:**
1. **Task 12.3.1:** Install QR code generation library
   - Install QR code library
   - Configure QR code generation
   - **Estimate:** 1 hour

2. **Task 12.3.2:** Create QR code data builder
   - Build QR code data string
   - Include IRN, invoice number, date
   - Include GSTIN, invoice value
   - Format according to e-Invoice spec
   - **Estimate:** 5 hours

3. **Task 12.3.3:** Implement QR code generation
   - Generate QR code image
   - Set QR code size
   - Set error correction level
   - **Estimate:** 3 hours

4. **Task 12.3.4:** Embed QR code in PDF
   - Add QR code to PDF template
   - Position QR code correctly
   - Ensure QR code visibility
   - **Estimate:** 5 hours

5. **Task 12.3.5:** Validate QR code format
   - Verify QR code data format
   - Test QR code scanning
   - Ensure compliance
   - **Estimate:** 3 hours

6. **Task 12.3.6:** Test QR code generation and embedding
   - Test QR code generation
   - Test QR code embedding
   - Test QR code scanning
   - **Estimate:** 3 hours

---

### Story 12.4: Signed Invoice Payload Storage

**Story Description:** Implement storage of signed invoice payload and ACK details.

**Story Points:** 5  
**Priority:** High  
**Labels:** `backend`, `database`, `gst`

**Acceptance Criteria:**
- Store signed invoice payload
- Store ACK details
- Store e-Invoice status
- Store error messages if any

**Tasks:**
1. **Task 12.4.1:** Extend einvoices table
   - Add signed_payload field (JSONB)
   - Add ack_number field
   - Add ack_date field
   - Add error_message field
   - **Estimate:** 2 hours

2. **Task 12.4.2:** Implement signed payload storage
   - Store signed invoice payload
   - Store payload as JSONB
   - Link to invoice
   - **Estimate:** 3 hours

3. **Task 12.4.3:** Implement ACK details storage
   - Extract ACK from provider response
   - Store ACK number
   - Store ACK date
   - Store ACK status
   - **Estimate:** 3 hours

4. **Task 12.4.4:** Implement error message storage
   - Store error messages
   - Store error codes
   - Store error details
   - **Estimate:** 2 hours

5. **Task 12.4.5:** Create e-Invoice status tracking
   - Track status (PENDING, SUCCESS, FAILED)
   - Update status on events
   - Store status history
   - **Estimate:** 3 hours

6. **Task 12.4.6:** Test payload and ACK storage
   - Test signed payload storage
   - Test ACK storage
   - Test error storage
   - Test status tracking
   - **Estimate:** 2 hours

---

### Story 12.5: Idempotency & Retry Mechanism

**Story Description:** Implement idempotent e-Invoice calls and retry mechanism.

**Story Points:** 8  
**Priority:** High  
**Labels:** `backend`, `api`, `gst`, `reliability`

**Acceptance Criteria:**
- Idempotent e-Invoice calls (invoice_id + attempt key)
- Retry mechanism for failures
- Retry limit configuration
- Error logging and tracking
- User-visible status

**Tasks:**
1. **Task 12.5.1:** Implement idempotency key generation
   - Generate idempotency key (invoice_id + attempt)
   - Store idempotency key
   - Check for duplicate calls
   - **Estimate:** 4 hours

2. **Task 12.5.2:** Create idempotency check service
   - Check if IRN already generated
   - Return existing IRN if found
   - Prevent duplicate calls
   - **Estimate:** 3 hours

3. **Task 12.5.3:** Implement retry mechanism
   - Retry on failure
   - Configure retry attempts
   - Configure retry delay
   - Exponential backoff
   - **Estimate:** 5 hours

4. **Task 12.5.4:** Add retry limit configuration
   - Configure max retry attempts
   - Configure retry intervals
   - Store retry configuration
   - **Estimate:** 2 hours

5. **Task 12.5.5:** Implement error logging and tracking
   - Log retry attempts
   - Log failure reasons
   - Track retry history
   - **Estimate:** 3 hours

6. **Task 12.5.6:** Create user-visible status
   - Display IRN generation status
   - Display error messages
   - Display retry status
   - **Estimate:** 3 hours

7. **Task 12.5.7:** Test idempotency and retry
   - Test idempotency
   - Test retry mechanism
   - Test error handling
   - **Estimate:** 4 hours

---

### Story 12.6: Invoice PDF with IRN + QR

**Story Description:** Update invoice PDF generation to include IRN and QR code.

**Story Points:** 5  
**Priority:** Critical  
**Labels:** `backend`, `api`, `gst`, `pdf`

**Acceptance Criteria:**
- Invoice PDF includes IRN
- Invoice PDF includes QR code
- PDF template updated
- PDF regeneration with IRN + QR

**Tasks:**
1. **Task 12.6.1:** Update invoice PDF template
   - Add IRN field to template
   - Add QR code section
   - Position IRN and QR code
   - **Estimate:** 4 hours

2. **Task 12.6.2:** Update PDF generation service
   - Include IRN in PDF data
   - Include QR code in PDF
   - Generate QR code if IRN exists
   - **Estimate:** 4 hours

3. **Task 12.6.3:** Implement PDF regeneration
   - Regenerate PDF after IRN generation
   - Update existing PDF
   - Store updated PDF
   - **Estimate:** 3 hours

4. **Task 12.6.4:** Handle PDF update workflow
   - Trigger PDF regeneration on IRN
   - Update PDF in storage
   - Update PDF path if needed
   - **Estimate:** 3 hours

5. **Task 12.6.5:** Test invoice PDF with IRN + QR
   - Test PDF with IRN
   - Test PDF with QR code
   - Test PDF regeneration
   - Test QR code scanning
   - **Estimate:** 3 hours

---

## EPIC 13: Reporting & Analytics

**Epic Description:** Build reporting and analytics dashboard for businesses to track performance, profitability, and operations.

**Epic Goal:** Provide insights into job profitability, inventory status, WIP, and operational metrics.

**Acceptance Criteria:**
- Dashboard with key metrics
- Job profitability reports
- Inventory reports (stock aging, low stock)
- WIP (Work-in-Progress) reports
- Scrap analytics
- Expense reports
- Export capabilities (PDF/Excel)

---

### Story 13.1: Dashboard Metrics

**Story Description:** Display key metrics on dashboard (active jobs, pending tasks, low stock alerts, etc.).

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `backend`, `frontend`, `analytics`, `dashboard`

**Acceptance Criteria:**
- Dashboard shows real-time metrics
- Metrics filtered by tenant
- Role-based metric visibility
- Metrics update automatically
- Active jobs count
- Pending tasks list
- Low stock alerts

**Tasks:**
1. **Task 13.1.1:** Create dashboard metrics service
   - Calculate active jobs count
   - Calculate pending tasks
   - Calculate low stock items
   - Calculate other key metrics
   - **Estimate:** 6 hours

2. **Task 13.1.2:** Create dashboard metrics endpoint
   - GET /api/v1/reports/dashboard/metrics
   - Return all metrics
   - Filter by tenant
   - **Estimate:** 3 hours

3. **Task 13.1.3:** Create pending tasks endpoint
   - GET /api/v1/reports/dashboard/tasks
   - Return pending tasks list
   - Include task details
   - **Estimate:** 4 hours

4. **Task 13.1.4:** Implement metrics caching
   - Cache dashboard metrics
   - Set TTL
   - Invalidate on updates
   - **Estimate:** 3 hours

5. **Task 13.1.5:** Build dashboard UI
   - Metrics cards component
   - Pending tasks list
   - Low stock alerts
   - Real-time updates
   - **Estimate:** 8 hours

6. **Task 13.1.6:** Implement role-based metric visibility
   - Filter metrics by role
   - Show/hide metrics based on permissions
   - **Estimate:** 3 hours

7. **Task 13.1.7:** Test dashboard metrics
   - Test metrics calculation
   - Test caching
   - Test UI display
   - Test role-based visibility
   - **Estimate:** 3 hours

---

### Story 13.2: Job Profitability Reports

**Story Description:** Generate job profitability reports with cost breakdown.

**Story Points:** 8  
**Priority:** High  
**Labels:** `backend`, `frontend`, `reports`, `jobs`

**Acceptance Criteria:**
- Job profitability by date range
- Job profitability by customer
- Cost breakdown (materials, labor, scrap, overhead)
- Profit margin analysis
- Export to PDF/Excel

**Tasks:**
1. **Task 13.2.1:** Create profitability report service
   - Aggregate job profitability data
   - Calculate cost breakdown
   - Calculate profit margins
   - **Estimate:** 6 hours

2. **Task 13.2.2:** Create profitability report endpoint
   - GET /api/v1/reports/profitability
   - Filter by date range
   - Filter by customer
   - Return profitability data
   - **Estimate:** 4 hours

3. **Task 13.2.3:** Implement cost breakdown calculation
   - Calculate material costs
   - Calculate labor costs
   - Calculate scrap costs
   - Calculate overhead
   - **Estimate:** 5 hours

4. **Task 13.2.4:** Implement profit margin analysis
   - Calculate profit margin per job
   - Calculate average profit margin
   - Identify low-margin jobs
   - **Estimate:** 4 hours

5. **Task 13.2.5:** Build profitability report UI
   - Report filters
   - Profitability table
   - Cost breakdown visualization
   - Profit margin charts
   - **Estimate:** 8 hours

6. **Task 13.2.6:** Add export capabilities
   - Export to PDF
   - Export to Excel
   - Include all report data
   - **Estimate:** 4 hours

7. **Task 13.2.7:** Test profitability reports
   - Test report generation
   - Test filtering
   - Test cost breakdown
   - Test exports
   - **Estimate:** 3 hours

---

### Story 13.3: Inventory Reports

**Story Description:** Generate inventory reports (stock aging, low stock, valuation).

**Story Points:** 5  
**Priority:** High  
**Labels:** `backend`, `frontend`, `reports`, `inventory`

**Acceptance Criteria:**
- Stock aging report
- Stock aging report
- Low stock report
- Stock valuation report
- Stock movement report
- Export capabilities

**Tasks:**
1. **Task 13.3.1:** Create inventory report service
   - Aggregate inventory data
   - Calculate stock aging
   - Calculate stock valuation
   - **Estimate:** 5 hours

2. **Task 13.3.2:** Create inventory report endpoint
   - GET /api/v1/reports/inventory
   - Return stock aging data
   - Return low stock data
   - Return valuation data
   - **Estimate:** 4 hours

3. **Task 13.3.3:** Implement stock aging calculation
   - Calculate stock age per item
   - Group by age buckets
   - Include valuation
   - **Estimate:** 4 hours

4. **Task 13.3.4:** Build inventory report UI
   - Stock aging table
   - Low stock alerts
   - Valuation summary
   - Stock movement summary
   - **Estimate:** 6 hours

5. **Task 13.3.5:** Add export capabilities
   - Export to PDF
   - Export to Excel
   - Include all report data
   - **Estimate:** 3 hours

6. **Task 13.3.6:** Test inventory reports
   - Test stock aging
   - Test low stock
   - Test valuation
   - Test exports
   - **Estimate:** 3 hours

---

### Story 13.4: WIP (Work-in-Progress) Reports

**Story Description:** Generate WIP reports showing jobs in progress.

**Story Points:** 5  
**Priority:** High  
**Labels:** `backend`, `frontend`, `reports`, `jobs`

**Acceptance Criteria:**
- WIP jobs list
- WIP by status
- WIP by customer
- WIP value calculation
- Export capabilities

**Tasks:**
1. **Task 13.4.1:** Create WIP report service
   - Query jobs in progress
   - Filter by status
   - Filter by customer
   - **Estimate:** 4 hours

2. **Task 13.4.2:** Implement WIP value calculation
   - Calculate WIP value per job
   - Sum WIP value by status
   - Sum WIP value by customer
   - **Estimate:** 4 hours

3. **Task 13.4.3:** Create WIP report endpoint
   - GET /api/v1/reports/wip
   - Filter by status
   - Filter by customer
   - Return WIP data
   - **Estimate:** 3 hours

4. **Task 13.4.4:** Build WIP report UI
   - WIP jobs table
   - WIP by status summary
   - WIP by customer summary
   - WIP value totals
   - **Estimate:** 5 hours

5. **Task 13.4.5:** Add export capabilities
   - Export to PDF
   - Export to Excel
   - Include all report data
   - **Estimate:** 3 hours

6. **Task 13.4.6:** Test WIP reports
   - Test WIP calculation
   - Test filtering
   - Test exports
   - **Estimate:** 2 hours

---

### Story 13.5: Scrap Analytics Reports

**Story Description:** Generate scrap analytics reports.

**Story Points:** 5  
**Priority:** Medium  
**Labels:** `backend`, `frontend`, `reports`, `scrap`

**Acceptance Criteria:**
- Scrap by type report
- Scrap by job report
- Scrap value trends
- Scrap cost analysis
- Export capabilities

**Tasks:**
1. **Task 13.5.1:** Create scrap report service
   - Aggregate scrap by type
   - Aggregate scrap by job
   - Calculate scrap trends
   - **Estimate:** 4 hours

2. **Task 13.5.2:** Create scrap report endpoint
   - GET /api/v1/reports/scrap
   - Filter by type
   - Filter by job
   - Filter by date range
   - **Estimate:** 3 hours

3. **Task 13.5.3:** Implement scrap value trends
   - Calculate scrap value over time
   - Identify trends
   - Calculate averages
   - **Estimate:** 4 hours

4. **Task 13.5.4:** Build scrap report UI
   - Scrap by type table
   - Scrap by job table
   - Scrap trends chart
   - Scrap cost analysis
   - **Estimate:** 5 hours

5. **Task 13.5.5:** Add export capabilities
   - Export to PDF
   - Export to Excel
   - Include all report data
   - **Estimate:** 3 hours

6. **Task 13.5.6:** Test scrap reports
   - Test report generation
   - Test filtering
   - Test trends
   - Test exports
   - **Estimate:** 2 hours

---

### Story 13.6: Expense Reports

**Story Description:** Generate expense reports by category, vendor, and time period.

**Story Points:** 5  
**Priority:** Medium  
**Labels:** `backend`, `frontend`, `reports`, `expenses`

**Acceptance Criteria:**
- Expense by category report
- Expense by vendor report
- Monthly expense report
- Payment status report
- Export capabilities

**Tasks:**
1. **Task 13.6.1:** Create expense report service
   - Aggregate expenses by category
   - Aggregate expenses by vendor
   - Aggregate expenses by month
   - Aggregate by payment status
   - **Estimate:** 5 hours

2. **Task 13.6.2:** Create expense report endpoint
   - GET /api/v1/reports/expenses
   - Filter by category
   - Filter by vendor
   - Filter by date range
   - Filter by payment status
   - **Estimate:** 4 hours

3. **Task 13.6.3:** Build expense report UI
   - Expense by category table
   - Expense by vendor table
   - Monthly expense chart
   - Payment status summary
   - **Estimate:** 6 hours

4. **Task 13.6.4:** Add export capabilities
   - Export to PDF
   - Export to Excel
   - Include all report data
   - **Estimate:** 3 hours

5. **Task 13.6.5:** Test expense reports
   - Test report generation
   - Test filtering
   - Test exports
   - **Estimate:** 2 hours

---

## EPIC 14: Security & Compliance

**Epic Description:** Implement security measures, compliance features, and audit logging.

**Epic Goal:** Ensure data security, compliance with Indian regulations, and complete audit trails.

**Acceptance Criteria:**
- All API endpoints secured
- Row-level security implemented
- Audit logging for all actions
- Data encryption at rest and in transit
- Compliance with DPDP Act (data localization)
- Stock ledger immutability
- Document access security

---

### Story 14.1: Row-Level Security (RLS)

**Story Description:** Implement PostgreSQL Row-Level Security to enforce tenant isolation at database level.

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `backend`, `database`, `security`

**Acceptance Criteria:**
- RLS enabled on all tenant tables
- Policies enforce tenant_id filtering
- Application sets current_tenant context
- RLS tested and verified

**Tasks:**
1. **Task 14.1.1:** Enable RLS on all tenant tables
   - ALTER TABLE ... ENABLE ROW LEVEL SECURITY
   - Apply to all tenant tables
   - Verify RLS enabled
   - **Estimate:** 2 hours

2. **Task 14.1.2:** Create RLS policies
   - CREATE POLICY tenant_isolation
   - Use current_setting('app.current_tenant')
   - Apply to SELECT, INSERT, UPDATE, DELETE
   - **Estimate:** 5 hours

3. **Task 14.1.3:** Implement tenant context middleware
   - Extract tenant_id from JWT
   - Set app.current_tenant session variable
   - Apply to all database connections
   - **Estimate:** 4 hours

4. **Task 14.1.4:** Test RLS enforcement
   - Test cross-tenant access attempts
   - Verify data isolation
   - Test edge cases
   - **Estimate:** 4 hours

5. **Task 14.1.5:** Document RLS policies
   - Document all policies
   - Document tenant context setup
   - Create troubleshooting guide
   - **Estimate:** 2 hours

---

### Story 14.2: Audit Logging

**Story Description:** Log all user actions for compliance and audit purposes.

**Story Points:** 8  
**Priority:** High  
**Labels:** `backend`, `audit`, `compliance`

**Acceptance Criteria:**
- All CRUD operations logged
- Logs include user, entity, action, timestamp
- Logs include old/new values
- Logs searchable and exportable
- Stock ledger changes logged

**Tasks:**
1. **Task 14.2.1:** Create audit logging service
   - AuditService class
   - Log method
   - Store in audit_logs table
   - **Estimate:** 4 hours

2. **Task 14.2.2:** Add audit hooks to all endpoints
   - POST operations
   - PUT operations
   - DELETE operations
   - Status changes
   - **Estimate:** 8 hours

3. **Task 14.2.3:** Capture request metadata
   - IP address
   - User agent
   - Timestamp
   - User ID
   - Tenant ID
   - **Estimate:** 3 hours

4. **Task 14.2.4:** Capture old/new values
   - Store old values before update
   - Store new values after update
   - Compare and log changes
   - **Estimate:** 5 hours

5. **Task 14.2.5:** Implement audit log search
   - Search by user, entity, action
   - Filter by date range
   - Pagination support
   - **Estimate:** 4 hours

6. **Task 14.2.6:** Add export capabilities
   - Export audit logs to CSV
   - Export audit logs to PDF
   - **Estimate:** 3 hours

7. **Task 14.2.7:** Test audit logging
   - Test logging for all operations
   - Test search
   - Test exports
   - **Estimate:** 3 hours

---

### Story 14.3: Data Encryption

**Story Description:** Implement encryption at rest and in transit for sensitive data.

**Story Points:** 5  
**Priority:** High  
**Labels:** `backend`, `security`, `aws`

**Acceptance Criteria:**
- Database encryption enabled
- S3 encryption enabled
- TLS for all API calls
- Encryption keys managed securely

**Tasks:**
1. **Task 14.3.1:** Enable RDS encryption
   - Configure encryption at rest
   - Use AWS KMS
   - Verify encryption
   - **Estimate:** 2 hours

2. **Task 14.3.2:** Enable S3 encryption
   - Configure S3 bucket encryption
   - Use SSE-S3 or SSE-KMS
   - Verify encryption
   - **Estimate:** 2 hours

3. **Task 14.3.3:** Enforce TLS for APIs
   - Configure HTTPS only
   - Redirect HTTP to HTTPS
   - Verify certificates
   - **Estimate:** 2 hours

4. **Task 14.3.4:** Secure sensitive data in database
   - Encrypt PII fields (optional)
   - Use application-level encryption if needed
   - Manage encryption keys
   - **Estimate:** 4 hours

5. **Task 14.3.5:** Test encryption
   - Test database encryption
   - Test S3 encryption
   - Test TLS enforcement
   - **Estimate:** 2 hours

---

### Story 14.4: Compliance Features

**Story Description:** Implement features to comply with Indian regulations (DPDP Act, GST).

**Story Points:** 5  
**Priority:** Medium  
**Labels:** `backend`, `compliance`, `legal`

**Acceptance Criteria:**
- Data localization (India region)
- User consent tracking
- Data deletion requests
- Privacy policy integration
- GST compliance features

**Tasks:**
1. **Task 14.4.1:** Implement data localization
   - Ensure RDS in India region
   - Ensure S3 in India region
   - Verify data residency
   - **Estimate:** 2 hours

2. **Task 14.4.2:** Add consent tracking
   - Store user consents
   - Track consent versions
   - Consent expiry handling
   - **Estimate:** 4 hours

3. **Task 14.4.3:** Implement data deletion
   - User data deletion endpoint
   - Anonymize data
   - Soft delete option
   - Compliance with retention policies
   - **Estimate:** 5 hours

4. **Task 14.4.4:** Add privacy policy acceptance
   - Require acceptance on signup
   - Track acceptance
   - Version management
   - **Estimate:** 3 hours

5. **Task 14.4.5:** Test compliance features
   - Test data localization
   - Test consent tracking
   - Test data deletion
   - Test privacy policy
   - **Estimate:** 2 hours

---

## EPIC 15: Testing & Quality Assurance

**Epic Description:** Comprehensive testing strategy including unit, integration, and E2E tests.

**Epic Goal:** Ensure high code quality and reliability through automated testing.

**Acceptance Criteria:**
- Unit test coverage > 80%
- Integration tests for all APIs
- E2E tests for critical flows
- Performance tests completed
- Multi-tenancy isolation tests

---

### Story 15.1: Unit Testing

**Story Description:** Write unit tests for all backend services and frontend components.

**Story Points:** 13  
**Priority:** High  
**Labels:** `testing`, `backend`, `frontend`

**Acceptance Criteria:**
- All services have unit tests
- All components have unit tests
- Test coverage > 80%
- Tests run in CI/CD

**Tasks:**
1. **Task 15.1.1:** Set up testing framework (Backend)
   - Jest/Mocha for Node.js
   - pytest for Python
   - Configure test runner
   - **Estimate:** 3 hours

2. **Task 15.1.2:** Set up testing framework (Frontend)
   - React Testing Library
   - Jest
   - Configure test runner
   - **Estimate:** 3 hours

3. **Task 15.1.3:** Write service unit tests
   - Test all service methods
   - Mock dependencies
   - Test error cases
   - **Estimate:** 25 hours

4. **Task 15.1.4:** Write component unit tests
   - Test all components
   - Test user interactions
   - Test edge cases
   - **Estimate:** 25 hours

5. **Task 15.1.5:** Configure coverage reporting
   - Set up coverage tool
   - Configure thresholds (> 80%)
   - Generate reports
   - **Estimate:** 2 hours

6. **Task 15.1.6:** Integrate tests in CI/CD
   - Run tests on PR
   - Fail build if coverage < 80%
   - Generate coverage reports
   - **Estimate:** 3 hours

---

### Story 15.2: Integration Testing

**Story Description:** Write integration tests for API endpoints and database operations.

**Story Points:** 13  
**Priority:** High  
**Labels:** `testing`, `backend`, `api`

**Acceptance Criteria:**
- All API endpoints tested
- Database operations tested
- Authentication flows tested
- Multi-tenancy tested

**Tasks:**
1. **Task 15.2.1:** Set up integration test environment
   - Test database setup
   - Test data fixtures
   - Test cleanup
   - **Estimate:** 4 hours

2. **Task 15.2.2:** Write API endpoint tests
   - Test all CRUD operations
   - Test authentication
   - Test authorization
   - Test error handling
   - **Estimate:** 25 hours

3. **Task 15.2.3:** Write multi-tenancy tests
   - Test tenant isolation
   - Test cross-tenant access prevention
   - Test tenant filtering
   - **Estimate:** 8 hours

4. **Task 15.2.4:** Write database operation tests
   - Test queries
   - Test transactions
   - Test RLS policies
   - **Estimate:** 6 hours

5. **Task 15.2.5:** Write authentication flow tests
   - Test login flow
   - Test token validation
   - Test role-based access
   - **Estimate:** 5 hours

6. **Task 15.2.6:** Integrate tests in CI/CD
   - Run integration tests on PR
   - Run tests in test environment
   - **Estimate:** 3 hours

---

### Story 15.3: End-to-End Testing

**Story Description:** Write E2E tests for critical user flows using Cypress or Playwright.

**Story Points:** 13  
**Priority:** Medium  
**Labels:** `testing`, `e2e`, `frontend`

**Acceptance Criteria:**
- Critical flows covered
- Tests run in CI/CD
- Tests stable and reliable
- Visual regression tests

**Tasks:**
1. **Task 15.3.1:** Set up E2E testing framework
   - Install Cypress/Playwright
   - Configure test environment
   - Set up test data
   - **Estimate:** 4 hours

2. **Task 15.3.2:** Write login flow tests
   - Test Keycloak login
   - Test tenant branding
   - Test role-based access
   - **Estimate:** 4 hours

3. **Task 15.3.3:** Write order-to-delivery flow tests
   - Test order intake
   - Test order acceptance
   - Test job creation
   - Test material issue
   - Test job completion
   - Test invoice generation
   - **Estimate:** 12 hours

4. **Task 15.3.4:** Write inventory flow tests
   - Test item creation
   - Test stock posting
   - Test stock ledger
   - **Estimate:** 5 hours

5. **Task 15.3.5:** Add visual regression tests
   - Capture screenshots
   - Compare screenshots
   - Flag differences
   - **Estimate:** 4 hours

6. **Task 15.3.6:** Integrate E2E tests in CI/CD
   - Run E2E tests on PR
   - Run tests in test environment
   - **Estimate:** 3 hours

---

### Story 15.4: Performance Testing

**Story Description:** Conduct performance testing to ensure system meets NFR requirements.

**Story Points:** 8  
**Priority:** Medium  
**Labels:** `testing`, `performance`, `devops`

**Acceptance Criteria:**
- API response time < 500ms (P95)
- Page load time < 3s
- System handles 5-15 concurrent users/tenant
- Performance bottlenecks identified

**Tasks:**
1. **Task 15.4.1:** Set up performance testing tools
   - Install k6 or JMeter
   - Configure test scenarios
   - Set up monitoring
   - **Estimate:** 4 hours

2. **Task 15.4.2:** Create load test scenarios
   - API load tests
   - Concurrent user tests
   - Database query tests
   - **Estimate:** 4 hours

3. **Task 15.4.3:** Run performance tests
   - Execute load tests
   - Measure response times
   - Measure page load times
   - Identify bottlenecks
   - **Estimate:** 4 hours

4. **Task 15.4.4:** Optimize based on results
   - Fix performance issues
   - Add caching
   - Optimize queries
   - Add indexes
   - **Estimate:** 10 hours

5. **Task 15.4.5:** Verify NFR compliance
   - Verify API response time < 500ms (P95)
   - Verify page load time < 3s
   - Verify concurrent user handling
   - **Estimate:** 3 hours

---

## EPIC 16: Documentation

**Epic Description:** Create comprehensive documentation for developers, users, and administrators.

**Epic Goal:** Ensure all stakeholders have access to clear, comprehensive documentation.

**Acceptance Criteria:**
- API documentation complete
- User guides available
- Developer setup guides available
- Architecture documentation updated
- GST e-Invoice integration guide

---

### Story 16.1: API Documentation

**Story Description:** Create comprehensive API documentation using OpenAPI/Swagger.

**Story Points:** 5  
**Priority:** Medium  
**Labels:** `documentation`, `backend`, `api`

**Acceptance Criteria:**
- All endpoints documented
- Request/response examples provided
- Authentication documented
- Error responses documented

**Tasks:**
1. **Task 16.1.1:** Set up Swagger/OpenAPI
   - Install Swagger dependencies
   - Configure OpenAPI spec
   - Set up Swagger UI
   - **Estimate:** 3 hours

2. **Task 16.1.2:** Document all endpoints
   - Add API annotations
   - Document request bodies
   - Document responses
   - Add examples
   - **Estimate:** 12 hours

3. **Task 16.1.3:** Document authentication
   - Document Keycloak flow
   - Document JWT structure
   - Document role requirements
   - **Estimate:** 3 hours

4. **Task 16.1.4:** Document error responses
   - Document error codes
   - Document error formats
   - Add error examples
   - **Estimate:** 2 hours

---

### Story 16.2: User Documentation

**Story Description:** Create user guides and help documentation for end users.

**Story Points:** 5  
**Priority:** Low  
**Labels:** `documentation`, `user-guide`

**Acceptance Criteria:**
- User guides for all roles
- Video tutorials (optional)
- FAQ section
- In-app help tooltips

**Tasks:**
1. **Task 16.2.1:** Create user guides
   - Owner/Admin user guide
   - Manager user guide
   - Accountant user guide
   - Worker user guide
   - **Estimate:** 10 hours

2. **Task 16.2.2:** Add in-app help
   - Tooltips on UI elements
   - Help modals
   - Contextual help
   - **Estimate:** 6 hours

3. **Task 16.2.3:** Create FAQ
   - Common questions
   - Troubleshooting guide
   - Best practices
   - **Estimate:** 4 hours

---

### Story 16.3: Developer Documentation

**Story Description:** Create setup guides and architecture documentation for developers.

**Story Points:** 5  
**Priority:** Medium  
**Labels:** `documentation`, `developer`

**Acceptance Criteria:**
- Local setup guide
- Architecture overview
- Code structure documentation
- Contribution guidelines
- GST e-Invoice integration guide

**Tasks:**
1. **Task 16.3.1:** Create setup guide
   - Prerequisites
   - Installation steps
   - Configuration
   - Troubleshooting
   - **Estimate:** 4 hours

2. **Task 16.3.2:** Document architecture
   - System architecture
   - Component diagrams
   - Data flow diagrams
   - **Estimate:** 4 hours

3. **Task 16.3.3:** Document code structure
   - Folder structure
   - Naming conventions
   - Coding standards
   - **Estimate:** 3 hours

4. **Task 16.3.4:** Create contribution guidelines
   - PR process
   - Code review guidelines
   - Testing requirements
   - **Estimate:** 2 hours

5. **Task 16.3.5:** Create GST e-Invoice integration guide
   - Provider setup
   - API integration
   - Error handling
   - Testing
   - **Estimate:** 3 hours

---

## EPIC 17: Production Deployment

**Epic Description:** Deploy application to production environment with monitoring and observability.

**Epic Goal:** Successfully deploy and operate the application in production.

**Acceptance Criteria:**
- Application deployed to production
- Monitoring and alerting configured
- Backup and disaster recovery in place
- Performance meets NFRs
- Storage account configured and secured

---

### Story 17.1: Production Infrastructure Setup

**Story Description:** Set up production infrastructure (VM/Compose or Kubernetes).

**Story Points:** 13  
**Priority:** Critical  
**Labels:** `devops`, `infrastructure`, `aws`

**Acceptance Criteria:**
- Production environment created
- Database instance configured
- S3 buckets created
- Redis configured (if needed)
- All resources in India region

**Tasks:**
1. **Task 17.1.1:** Create Terraform modules (if using cloud)
   - EKS/ECS module (if K8s)
   - RDS module
   - S3 module
   - ElastiCache module (if Redis)
   - **Estimate:** 10 hours

2. **Task 17.1.2:** Configure production database
   - Create RDS instance (or managed Postgres)
   - Configure Multi-AZ
   - Set up backups
   - Ensure India region
   - **Estimate:** 4 hours

3. **Task 17.1.3:** Configure S3 buckets
   - Create document storage bucket
   - Configure encryption
   - Set up lifecycle policies
   - Ensure India region
   - **Estimate:** 3 hours

4. **Task 17.1.4:** Configure Redis (if needed)
   - Create ElastiCache cluster
   - Configure persistence
   - Set up backups
   - Ensure India region
   - **Estimate:** 3 hours

5. **Task 17.1.5:** Set up VM infrastructure (if using VM)
   - Provision VM
   - Configure networking
   - Set up security groups
   - **Estimate:** 4 hours

6. **Task 17.1.6:** Verify all resources in India region
   - Verify database region
   - Verify S3 region
   - Verify other resources
   - **Estimate:** 2 hours

---

### Story 17.2: Application Deployment

**Story Description:** Deploy application to production environment.

**Story Points:** 13  
**Priority:** Critical  
**Labels:** `devops`, `deployment`, `kubernetes`

**Acceptance Criteria:**
- All services deployed
- Health checks configured
- Auto-scaling configured (if K8s)
- Rolling updates working

**Tasks:**
1. **Task 17.2.1:** Create production deployment manifests
   - Frontend deployment
   - Backend deployment
   - Keycloak deployment
   - **Estimate:** 6 hours

2. **Task 17.2.2:** Configure health checks
   - Liveness probes
   - Readiness probes
   - Startup probes
   - **Estimate:** 3 hours

3. **Task 17.2.3:** Configure auto-scaling (if K8s)
   - HPA for backend
   - HPA for frontend
   - Set min/max replicas
   - **Estimate:** 4 hours

4. **Task 17.2.4:** Configure rolling updates
   - Set update strategy
   - Configure rollback
   - Test rolling updates
   - **Estimate:** 4 hours

5. **Task 17.2.5:** Deploy to production
   - Deploy frontend
   - Deploy backend
   - Deploy Keycloak
   - Verify deployments
   - **Estimate:** 5 hours

6. **Task 17.2.6:** Test production deployment
   - Test all services
   - Test health checks
   - Test auto-scaling
   - Test rolling updates
   - **Estimate:** 4 hours

---

### Story 17.3: Monitoring & Observability

**Story Description:** Set up monitoring, logging, and alerting for production.

**Story Points:** 8  
**Priority:** High  
**Labels:** `devops`, `monitoring`, `observability`

**Acceptance Criteria:**
- Application metrics collected
- Logs aggregated
- Alerts configured
- Dashboards created

**Tasks:**
1. **Task 17.3.1:** Set up Prometheus (if using)
   - Install Prometheus
   - Configure scraping
   - Set up retention
   - **Estimate:** 4 hours

2. **Task 17.3.2:** Set up Grafana (if using)
   - Install Grafana
   - Create dashboards
   - Configure data sources
   - **Estimate:** 6 hours

3. **Task 17.3.3:** Set up logging
   - Configure Fluentd/Fluent Bit
   - Send logs to CloudWatch/ELK
   - Set up log aggregation
   - **Estimate:** 4 hours

4. **Task 17.3.4:** Configure alerts
   - Set up AlertManager
   - Define alert rules
   - Configure notifications
   - **Estimate:** 4 hours

5. **Task 17.3.5:** Add application metrics
   - Instrument code
   - Expose metrics endpoint
   - Track business metrics
   - **Estimate:** 6 hours

6. **Task 17.3.6:** Test monitoring and alerting
   - Test metrics collection
   - Test log aggregation
   - Test alerts
   - **Estimate:** 3 hours

---

### Story 17.4: Backup & Disaster Recovery

**Story Description:** Implement backup and disaster recovery procedures.

**Story Points:** 8  
**Priority:** High  
**Labels:** `devops`, `backup`, `dr`

**Acceptance Criteria:**
- Automated backups configured
- RTO < 8h (VM) → < 4h (managed)
- RPO < 24h (VM) → < 1h (managed)
- DR plan documented
- Storage account backups configured

**Tasks:**
1. **Task 17.4.1:** Configure RDS backups
   - Automated daily backups
   - Point-in-time recovery
   - Backup retention
   - **Estimate:** 2 hours

2. **Task 17.4.2:** Configure S3 backups
   - Versioning enabled
   - Cross-region replication
   - Lifecycle policies
   - **Estimate:** 3 hours

3. **Task 17.4.3:** Create backup scripts
   - Database backup script
   - S3 backup script
   - Keycloak backup script
   - **Estimate:** 4 hours

4. **Task 17.4.4:** Test disaster recovery
   - Simulate failure
   - Test restore process
   - Measure RTO/RPO
   - **Estimate:** 6 hours

5. **Task 17.4.5:** Document DR plan
   - Write DR procedures
   - Document recovery steps
   - Create runbooks
   - **Estimate:** 4 hours

---

## Summary

### Epic Summary

| Epic | Stories | Total Story Points | Priority |
|------|---------|-------------------|----------|
| EPIC 1: Infrastructure & DevOps | 5 | 37 | Critical |
| EPIC 2: Authentication & Authorization | 4 | 29 | Critical |
| EPIC 3: Multi-Tenancy Foundation | 4 | 23 | Critical |
| EPIC 4: Core Data Models & Customer Management | 4 | 26 | Critical |
| EPIC 5: Inventory Management | 6 | 34 | Critical |
| EPIC 6: Job/Work Order Management | 7 | 47 | Critical |
| EPIC 7: Workforce Management | 6 | 36 | High |
| EPIC 8: Scrap Management | 4 | 23 | High |
| EPIC 9: Expense Tracker | 5 | 26 | High |
| EPIC 10: Document Management & Storage | 6 | 39 | High |
| EPIC 11: Sales Orders & Invoicing | 5 | 31 | High |
| EPIC 12: GST e-Invoice Integration | 6 | 47 | Critical |
| EPIC 13: Reporting & Analytics | 6 | 36 | High |
| EPIC 14: Security & Compliance | 4 | 26 | High |
| EPIC 15: Testing & Quality Assurance | 4 | 47 | High |
| EPIC 16: Documentation | 3 | 15 | Medium |
| EPIC 17: Production Deployment | 4 | 42 | Critical |
| **TOTAL** | **77** | **563** | |

### Recommended Sprint Planning

**Sprint 1-2 (Weeks 1-2): Foundation**
- EPIC 1: Infrastructure & DevOps (partial)
- EPIC 2: Authentication & Authorization (partial)
- EPIC 3: Multi-Tenancy Foundation (partial)

**Sprint 3-4 (Weeks 3-4): Core Features**
- EPIC 2: Authentication & Authorization (complete)
- EPIC 3: Multi-Tenancy Foundation (complete)
- EPIC 4: Core Data Models & Customer Management (partial)

**Sprint 5-6 (Weeks 5-6): Inventory & Jobs**
- EPIC 4: Core Data Models & Customer Management (complete)
- EPIC 5: Inventory Management (partial)
- EPIC 6: Job/Work Order Management (partial)

**Sprint 7-8 (Weeks 7-8): Jobs & Workforce**
- EPIC 5: Inventory Management (complete)
- EPIC 6: Job/Work Order Management (continue)
- EPIC 7: Workforce Management (partial)

**Sprint 9-10 (Weeks 9-10): Scrap, Expenses & Documents**
- EPIC 6: Job/Work Order Management (complete)
- EPIC 7: Workforce Management (complete)
- EPIC 8: Scrap Management
- EPIC 9: Expense Tracker
- EPIC 10: Document Management & Storage (partial)

**Sprint 11-12 (Weeks 11-12): Invoicing & GST**
- EPIC 10: Document Management & Storage (complete)
- EPIC 11: Sales Orders & Invoicing
- EPIC 12: GST e-Invoice Integration (partial)

**Sprint 13-14 (Weeks 13-14): GST & Reports**
- EPIC 12: GST e-Invoice Integration (complete)
- EPIC 13: Reporting & Analytics
- EPIC 14: Security & Compliance (partial)

**Sprint 15-16 (Weeks 15-16): Security, Testing & Deployment**
- EPIC 14: Security & Compliance (complete)
- EPIC 15: Testing & Quality Assurance (partial)
- EPIC 16: Documentation
- EPIC 17: Production Deployment (partial)

**Sprint 17-18 (Weeks 17-18): Production Ready**
- EPIC 15: Testing & Quality Assurance (complete)
- EPIC 17: Production Deployment (complete)
- Final testing and bug fixes
- Go-live preparation

---

**Document Control**

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | January 2026 | Solution Architect | Initial JIRA breakdown from HLD v1.0 |

---

---

**Document Control**

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | January 2026 | Solution Architect | Initial JIRA breakdown from HLD v1.0 with all epics, stories, and tasks |

---

*This document should be imported into JIRA and used for sprint planning and task assignment.*
