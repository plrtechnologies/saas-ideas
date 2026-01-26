# Legal Opinion SaaS - JIRA Epics, Stories & Tasks

**Project:** Legal Opinion SaaS Platform  
**Document Version:** 1.0  
**Date:** January 2026  
**Based on:** HLD-Legal-Opinion-SaaS.md v2.0

---

## JIRA Structure Overview

This document contains:
- **Epics** - High-level feature areas (14 epics)
- **Stories** - User-facing features within epics (~60+ stories)
- **Tasks** - Technical implementation details (~200+ tasks)

**Estimation Guidelines:**
- Story Points: 1 (XS), 2 (S), 3 (M), 5 (L), 8 (XL), 13 (XXL)
- Priority: Critical, High, Medium, Low
- Labels: `backend`, `frontend`, `devops`, `ai`, `security`, `testing`

---

## EPIC 1: Infrastructure & DevOps Foundation

**Epic Description:** Set up development and production infrastructure, containerization, and CI/CD pipelines for both UI and API repositories.

**Epic Goal:** Enable development team to build, test, and deploy the application efficiently.

**Acceptance Criteria:**
- Docker Compose setup working for local development
- Two separate GitHub repositories (legal-ops-ui, legal-ops-api) created
- CI/CD pipelines configured for both repositories
- Production-ready Kubernetes deployment manifests

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
   - Define Redis cache service
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

**Story Description:** Create and configure two separate GitHub repositories with proper structure and initial setup.

**Story Points:** 3  
**Priority:** Critical  
**Labels:** `devops`, `repository`

**Acceptance Criteria:**
- Two repositories created: legal-ops-ui and legal-ops-api
- Repository structure follows best practices
- .gitignore files configured appropriately
- README files with project overview
- Branch protection rules configured

**Tasks:**
1. **Task 1.2.1:** Create legal-ops-ui repository
   - Initialize repository with React project structure
   - Configure .gitignore for Node.js/React
   - Add README with project description
   - Set up initial package.json and dependencies
   - **Estimate:** 2 hours

2. **Task 1.2.2:** Create legal-ops-api repository
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

### Story 1.3: Frontend CI/CD Pipeline (legal-ops-ui)

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
   - Configure ECR login and push
   - Set up Kubernetes deployment to dev environment
   - **Estimate:** 5 hours

3. **Task 1.3.3:** Create test deployment workflow (.github/workflows/deploy-test.yml)
   - Configure semantic versioning generation
   - Set up build with test environment variables
   - Configure Docker build with version tags
   - Set up Kubernetes deployment to test environment
   - **Estimate:** 5 hours

4. **Task 1.3.4:** Configure GitHub secrets
   - Set up AWS credentials (ACCESS_KEY_ID, SECRET_ACCESS_KEY)
   - Configure SonarQube token and URL
   - Set up Kubernetes configs (DEV_KUBECONFIG, TEST_KUBECONFIG)
   - Configure environment-specific URLs
   - **Estimate:** 2 hours

5. **Task 1.3.5:** Test all workflows end-to-end
   - Test PR validation workflow
   - Test dev deployment workflow
   - Test test deployment workflow
   - Verify Docker images are created and pushed
   - **Estimate:** 4 hours

---

### Story 1.4: Backend CI/CD Pipeline (legal-ops-api)

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
   - Set up ECR push and Kubernetes deployment
   - **Estimate:** 5 hours

3. **Task 1.4.3:** Create test deployment workflow (.github/workflows/deploy-test.yml)
   - Configure semantic versioning
   - Set up test execution
   - Configure build and Docker image creation
   - Set up deployment to test environment
   - **Estimate:** 5 hours

4. **Task 1.4.4:** Configure GitHub secrets for backend
   - Set up AWS credentials
   - Configure SonarQube settings
   - Set up Kubernetes configs
   - Configure database and service URLs
   - **Estimate:** 2 hours

5. **Task 1.4.5:** Test all backend workflows
   - Test PR validation
   - Test dev deployment
   - Test test deployment
   - Verify deployments work correctly
   - **Estimate:** 4 hours

---

### Story 1.5: Production Infrastructure (Kubernetes)

**Story Description:** Create Kubernetes deployment manifests and configuration for production environment.

**Story Points:** 13  
**Priority:** High  
**Labels:** `devops`, `kubernetes`, `infrastructure`

**Acceptance Criteria:**
- Kubernetes manifests for all services
- ConfigMaps and Secrets configured
- Service and Ingress definitions
- Horizontal Pod Autoscaling configured
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
   - Frontend Service (ClusterIP)
   - Backend Service (ClusterIP)
   - Configure service ports and selectors
   - **Estimate:** 2 hours

4. **Task 1.5.4:** Create Ingress configuration
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

6. **Task 1.5.6:** Configure Horizontal Pod Autoscaling
   - Set up HPA for frontend
   - Set up HPA for backend
   - Configure min/max replicas
   - Set up CPU/memory thresholds
   - **Estimate:** 3 hours

7. **Task 1.5.7:** Create namespace and RBAC
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

**Epic Description:** Implement Keycloak-based authentication and authorization for both frontend and backend, with support for identity brokering.

**Epic Goal:** Provide secure, centralized authentication with SSO support and role-based access control.

**Acceptance Criteria:**
- Keycloak server deployed and configured
- Frontend integrates with Keycloak for login
- Backend validates JWT tokens from Keycloak
- Role-based access control working for UI and API
- Identity brokering configured for external IdPs

---

### Story 2.1: Keycloak Server Setup & Configuration

**Story Description:** Deploy and configure Keycloak server with realm, clients, and user management.

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `backend`, `security`, `keycloak`

**Acceptance Criteria:**
- Keycloak server running in Docker/Kubernetes
- Realm created for legal-opinion-saas
- Frontend and backend clients configured
- User roles defined (firm_admin, senior_advocate, panel_advocate, paralegal)
- MFA configuration available

**Tasks:**
1. **Task 2.1.1:** Deploy Keycloak server
   - Set up Keycloak in Docker Compose
   - Configure PostgreSQL database for Keycloak
   - Set up admin credentials
   - Configure environment variables
   - **Estimate:** 3 hours

2. **Task 2.1.2:** Create Keycloak realm
   - Create "legal-opinion-saas" realm
   - Configure realm settings (tokens, sessions)
   - Set up email configuration
   - Configure password policies
   - **Estimate:** 2 hours

3. **Task 2.1.3:** Configure frontend client (legal-opinion-web)
   - Create public client for React SPA
   - Enable PKCE flow
   - Configure redirect URIs
   - Set up CORS settings
   - **Estimate:** 2 hours

4. **Task 2.1.4:** Configure backend client (legal-opinion-api)
   - Create confidential client
   - Configure service account roles
   - Set up client credentials flow
   - Configure token settings
   - **Estimate:** 2 hours

5. **Task 2.1.5:** Define user roles
   - Create realm roles: firm_admin, senior_advocate, panel_advocate, paralegal
   - Configure role hierarchy
   - Set up role descriptions
   - **Estimate:** 1 hour

6. **Task 2.1.6:** Configure MFA (optional)
   - Set up TOTP authenticator
   - Configure SMS authenticator (if needed)
   - Test MFA flow
   - **Estimate:** 3 hours

7. **Task 2.1.7:** Set up identity brokering
   - Configure SAML 2.0 identity provider
   - Configure OIDC identity provider
   - Configure LDAP/Active Directory (if needed)
   - Test identity brokering flow
   - **Estimate:** 5 hours

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

6. **Task 2.2.6:** Create login page component
   - Design login UI with tenant branding
   - Integrate Keycloak login flow
   - Handle login errors
   - **Estimate:** 3 hours

7. **Task 2.2.7:** Test authentication flows
   - Test login flow
   - Test logout flow
   - Test token refresh
   - Test role-based access
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

**Epic Goal:** Enable multiple law firms to use the platform with complete data isolation and custom branding.

**Acceptance Criteria:**
- Tenant data isolation working
- Tenant branding API functional
- Tenant configuration stored and retrieved
- Row-level security implemented

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

2. **Task 3.1.2:** Create bank_clients table
   - Define schema with tenant_id FK
   - Add notification settings columns
   - Add template configuration
   - Create unique constraints
   - **Estimate:** 2 hours

3. **Task 3.1.3:** Create end_customers table
   - Define schema with tenant_id and client_id FKs
   - Add customer information fields
   - Create indexes
   - **Estimate:** 2 hours

4. **Task 3.1.4:** Create users table
   - Define schema with tenant_id FK
   - Add Keycloak integration fields
   - Create unique constraints
   - **Estimate:** 2 hours

5. **Task 3.1.5:** Create opinion_requests table
   - Define schema with tenant_id, client_id, end_customer_id FKs
   - Add request metadata fields
   - Create indexes for filtering
   - **Estimate:** 2 hours

6. **Task 3.1.6:** Create documents table
   - Define schema with regional language support fields
   - Add OCR and translation fields
   - Create indexes
   - **Estimate:** 2 hours

7. **Task 3.1.7:** Create opinion_templates table
   - Define schema with tenant_id and client_id FKs
   - Add template content JSONB field
   - Create indexes
   - **Estimate:** 2 hours

8. **Task 3.1.8:** Create legal_opinions table
   - Define schema with all opinion fields
   - Add version tracking
   - Create indexes
   - **Estimate:** 2 hours

9. **Task 3.1.9:** Create audit_logs table
   - Define schema for audit trail
   - Add indexes for querying
   - **Estimate:** 1 hour

10. **Task 3.1.10:** Configure Row Level Security (RLS)
    - Enable RLS on all tenant tables
    - Create RLS policies for tenant isolation
    - Test RLS policies
    - **Estimate:** 4 hours

11. **Task 3.1.11:** Create database migration scripts
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
   - Update user service
   - Update opinion request service
   - Update document service
   - Update opinion service
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
   - Require firm_admin role
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
   - Update Ant Design theme
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

## EPIC 4: Core Data Models & Services

**Epic Description:** Implement core data models, services, and APIs for bank clients, end customers, and opinion requests.

**Epic Goal:** Provide CRUD operations and business logic for core entities.

**Acceptance Criteria:**
- Bank client management working
- End customer management working
- Opinion request lifecycle managed
- All APIs properly secured and tested

---

### Story 4.1: Bank Client Management

**Story Description:** Implement CRUD operations for bank clients with notification and template configuration.

**Story Points:** 5  
**Priority:** High  
**Labels:** `backend`, `api`, `crud`

**Acceptance Criteria:**
- Create, read, update bank clients
- Configure notification settings
- Configure default template
- List bank clients with filtering

**Tasks:**
1. **Task 4.1.1:** Create bank client entity/model
   - Define data model
   - Add validation decorators
   - Create DTOs for create/update
   - **Estimate:** 2 hours

2. **Task 4.1.2:** Create bank client service
   - Implement create method
   - Implement findAll with filters
   - Implement findOne
   - Implement update
   - Implement delete (soft delete)
   - **Estimate:** 4 hours

3. **Task 4.1.3:** Create bank client controller
   - POST /api/v1/bank-clients
   - GET /api/v1/bank-clients
   - GET /api/v1/bank-clients/:id
   - PUT /api/v1/bank-clients/:id
   - DELETE /api/v1/bank-clients/:id
   - **Estimate:** 3 hours

4. **Task 4.1.4:** Implement notification configuration
   - Update notify_bank_on_completion flag
   - Update notify_end_customer_on_completion flag
   - Configure email templates
   - **Estimate:** 3 hours

5. **Task 4.1.5:** Add filtering and pagination
   - Filter by name, code, status
   - Implement pagination
   - Add sorting
   - **Estimate:** 2 hours

6. **Task 4.1.6:** Test bank client APIs
   - Test all CRUD operations
   - Test notification configuration
   - Test filtering
   - **Estimate:** 2 hours

---

### Story 4.2: End Customer Management

**Story Description:** Implement CRUD operations for end customers (borrowers).

**Story Points:** 3  
**Priority:** High  
**Labels:** `backend`, `api`, `crud`

**Acceptance Criteria:**
- Create, read, update end customers
- Link end customers to bank clients
- Search and filter end customers

**Tasks:**
1. **Task 4.2.1:** Create end customer entity/model
   - Define data model
   - Add validation
   - Create DTOs
   - **Estimate:** 2 hours

2. **Task 4.2.2:** Create end customer service
   - Implement CRUD methods
   - Link to bank client
   - Handle tenant isolation
   - **Estimate:** 3 hours

3. **Task 4.2.3:** Create end customer controller
   - POST /api/v1/end-customers
   - GET /api/v1/end-customers
   - GET /api/v1/end-customers/:id
   - PUT /api/v1/end-customers/:id
   - **Estimate:** 2 hours

4. **Task 4.2.4:** Add search functionality
   - Search by name, email, phone
   - Filter by bank client
   - Add pagination
   - **Estimate:** 2 hours

5. **Task 4.2.5:** Test end customer APIs
   - Test CRUD operations
   - Test search
   - Test filtering
   - **Estimate:** 2 hours

---

### Story 4.3: Opinion Request Management

**Story Description:** Implement opinion request lifecycle management with status tracking and assignment.

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `backend`, `api`, `workflow`

**Acceptance Criteria:**
- Create opinion requests
- Update request status
- Assign to lawyers
- Track request lifecycle
- Filter and search requests

**Tasks:**
1. **Task 4.3.1:** Create opinion request entity/model
   - Define data model
   - Add status enum
   - Create DTOs
   - **Estimate:** 2 hours

2. **Task 4.3.2:** Create opinion request service
   - Implement create method
   - Implement status updates
   - Implement assignment logic
   - Implement filtering
   - **Estimate:** 6 hours

3. **Task 4.3.3:** Create opinion request controller
   - POST /api/v1/opinion-requests
   - GET /api/v1/opinion-requests
   - GET /api/v1/opinion-requests/:id
   - PUT /api/v1/opinion-requests/:id
   - PATCH /api/v1/opinion-requests/:id/status
   - PATCH /api/v1/opinion-requests/:id/assign
   - **Estimate:** 4 hours

4. **Task 4.3.4:** Implement status workflow
   - Define status transitions
   - Validate status changes
   - Add status history tracking
   - **Estimate:** 4 hours

5. **Task 4.3.5:** Implement assignment logic
   - Assign to panel advocate
   - Reassign to different advocate
   - Track assignment history
   - **Estimate:** 3 hours

6. **Task 4.3.6:** Add filtering and search
   - Filter by status, priority, client
   - Search by reference number
   - Add date range filtering
   - Implement pagination
   - **Estimate:** 3 hours

7. **Task 4.3.7:** Test opinion request APIs
   - Test create request
   - Test status updates
   - Test assignment
   - Test filtering
   - **Estimate:** 3 hours

---

## EPIC 5: Document Management & OCR

**Epic Description:** Implement document upload, storage, OCR processing, and regional language support.

**Epic Goal:** Enable users to upload documents, extract text via OCR, detect language, and translate to English.

**Acceptance Criteria:**
- Document upload to S3 working
- OCR extraction functional
- Language detection working
- Translation to English implemented
- Document viewer available

---

### Story 5.1: Document Upload & Storage

**Story Description:** Implement document upload functionality with S3 storage and metadata management.

**Story Points:** 5  
**Priority:** Critical  
**Labels:** `backend`, `api`, `storage`, `s3`

**Acceptance Criteria:**
- Upload documents to S3
- Store document metadata in database
- Support multiple file types (PDF, images)
- Validate file size and type
- Organize by tenant folders

**Tasks:**
1. **Task 5.1.1:** Configure S3 client
   - Set up AWS SDK
   - Configure S3 bucket
   - Set up IAM permissions
   - **Estimate:** 2 hours

2. **Task 5.1.2:** Create document upload endpoint
   - POST /api/v1/documents/upload
   - Accept multipart/form-data
   - Validate file type and size
   - **Estimate:** 3 hours

3. **Task 5.1.3:** Implement S3 upload logic
   - Generate unique S3 key
   - Upload to tenant-specific folder
   - Set proper content type
   - Generate presigned URLs
   - **Estimate:** 4 hours

4. **Task 5.1.4:** Store document metadata
   - Save to documents table
   - Link to opinion request
   - Store file information
   - Track upload user
   - **Estimate:** 2 hours

5. **Task 5.1.5:** Implement file validation
   - Check file type (PDF, JPG, PNG)
   - Validate file size limits
   - Scan for viruses (optional)
   - **Estimate:** 3 hours

6. **Task 5.1.6:** Create document download endpoint
   - GET /api/v1/documents/:id/download
   - Generate presigned URL
   - Verify access permissions
   - **Estimate:** 2 hours

7. **Task 5.1.7:** Test document upload
   - Test file upload
   - Test S3 storage
   - Test metadata storage
   - Test download
   - **Estimate:** 3 hours

---

### Story 5.2: OCR Integration

**Story Description:** Integrate OCR service (Tesseract/AWS Textract) to extract text from documents.

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `backend`, `ocr`, `ai`

**Acceptance Criteria:**
- OCR extracts text from PDFs and images
- Supports regional languages
- OCR results stored in database
- OCR status tracked

**Tasks:**
1. **Task 5.2.1:** Set up OCR service
   - Choose OCR provider (Tesseract/Textract)
   - Configure OCR client
   - Set up service wrapper
   - **Estimate:** 3 hours

2. **Task 5.2.2:** Implement OCR extraction
   - Extract text from PDF
   - Extract text from images
   - Handle multiple pages
   - Store raw OCR text
   - **Estimate:** 5 hours

3. **Task 5.2.3:** Create OCR processing endpoint
   - POST /api/v1/documents/:id/ocr
   - Trigger OCR processing
   - Update document status
   - Return OCR results
   - **Estimate:** 3 hours

4. **Task 5.2.4:** Implement async OCR processing
   - Queue OCR jobs
   - Process in background
   - Update status on completion
   - Handle errors
   - **Estimate:** 5 hours

5. **Task 5.2.5:** Add OCR status tracking
   - Track PENDING, PROCESSING, COMPLETED, FAILED
   - Store OCR text in database
   - Add error messages
   - **Estimate:** 2 hours

6. **Task 5.2.6:** Test OCR integration
   - Test PDF OCR
   - Test image OCR
   - Test regional language OCR
   - Test error handling
   - **Estimate:** 3 hours

---

### Story 5.3: Language Detection & Translation

**Story Description:** Implement language detection and translation to English for regional language documents.

**Story Points:** 8  
**Priority:** High  
**Labels:** `backend`, `ai`, `translation`, `llm`

**Acceptance Criteria:**
- Language detection working
- Translation to English functional
- Original and translated text stored
- Translation status tracked

**Tasks:**
1. **Task 5.3.1:** Implement language detection
   - Use LLM API for language detection
   - Detect language from OCR text
   - Store detected language code
   - Handle detection errors
   - **Estimate:** 4 hours

2. **Task 5.3.2:** Implement translation service
   - Create translation method using LLM
   - Translate regional language to English
   - Preserve legal terms and names
   - Handle translation errors
   - **Estimate:** 5 hours

3. **Task 5.3.3:** Create translation endpoint
   - POST /api/v1/documents/:id/translate
   - Trigger translation if needed
   - Update translation status
   - Store translated text
   - **Estimate:** 3 hours

4. **Task 5.3.4:** Store original and translated text
   - Update documents table
   - Store original_language_text
   - Store translated_text
   - Store detected_language
   - Update translation_status
   - **Estimate:** 2 hours

5. **Task 5.3.5:** Integrate with document processing flow
   - Auto-detect language after OCR
   - Auto-translate if not English
   - Update status throughout process
   - **Estimate:** 4 hours

6. **Task 5.3.6:** Test language detection and translation
   - Test English document (no translation)
   - Test regional language detection
   - Test translation accuracy
   - Test error handling
   - **Estimate:** 4 hours

---

### Story 5.4: Document Processing Workflow

**Story Description:** Implement automated document processing workflow (OCR → Language Detection → Translation → Extraction).

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `backend`, `workflow`, `ai`

**Acceptance Criteria:**
- Documents automatically processed on upload
- Processing status tracked
- Errors handled gracefully
- Processing can be retried

**Tasks:**
1. **Task 5.4.1:** Create document processing service
   - Implement processDocument method
   - Orchestrate OCR → Detection → Translation
   - Handle errors at each step
   - **Estimate:** 5 hours

2. **Task 5.4.2:** Implement processing queue
   - Set up job queue (Bull/BullMQ)
   - Queue document processing jobs
   - Process jobs asynchronously
   - **Estimate:** 4 hours

3. **Task 5.4.3:** Create processing endpoint
   - POST /api/v1/documents/:id/process
   - Trigger full processing workflow
   - Return processing status
   - **Estimate:** 2 hours

4. **Task 5.4.4:** Implement status tracking
   - Track processing stages
   - Update document status
   - Store processing errors
   - **Estimate:** 3 hours

5. **Task 5.4.5:** Add retry logic
   - Retry failed OCR
   - Retry failed translation
   - Limit retry attempts
   - **Estimate:** 3 hours

6. **Task 5.4.6:** Test processing workflow
   - Test English document processing
   - Test regional language processing
   - Test error scenarios
   - Test retry logic
   - **Estimate:** 4 hours

---

### Story 5.5: Document Viewer Frontend

**Story Description:** Create frontend component to view documents with extracted data display.

**Story Points:** 5  
**Priority:** High  
**Labels:** `frontend`, `ui`, `documents`

**Acceptance Criteria:**
- Display document (PDF/image viewer)
- Show extracted data panel
- Display confidence scores
- Allow data correction

**Tasks:**
1. **Task 5.5.1:** Create document viewer component
   - Integrate PDF.js for PDF viewing
   - Display images
   - Add zoom controls
   - **Estimate:** 4 hours

2. **Task 5.5.2:** Create extracted data panel
   - Display structured extracted data
   - Show confidence scores
   - Format data nicely
   - **Estimate:** 3 hours

3. **Task 5.5.3:** Add data correction functionality
   - Allow editing extracted fields
   - Save corrections
   - Update confidence scores
   - **Estimate:** 4 hours

4. **Task 5.5.4:** Display processing status
   - Show OCR status
   - Show translation status
   - Show extraction status
   - **Estimate:** 2 hours

5. **Task 5.5.5:** Test document viewer
   - Test PDF viewing
   - Test image viewing
   - Test data display
   - Test data correction
   - **Estimate:** 2 hours

---

## EPIC 6: AI/LLM Integration

**Epic Description:** Integrate LLM APIs (OpenAI/Anthropic/Google) for document data extraction and opinion generation.

**Epic Goal:** Automate document data extraction and opinion draft generation using AI.

**Acceptance Criteria:**
- LLM API integration working
- Document data extraction functional
- Opinion draft generation working
- Configurable LLM provider
- Error handling implemented

---

### Story 6.1: LLM Service Abstraction

**Story Description:** Create abstracted LLM service that supports multiple providers (OpenAI, Anthropic, Google).

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `backend`, `ai`, `llm`

**Acceptance Criteria:**
- LLM service interface defined
- Implementations for OpenAI, Anthropic, Google
- Provider can be configured
- Consistent API across providers

**Tasks:**
1. **Task 6.1.1:** Design LLM service interface
   - Define common methods
   - Define request/response types
   - Document interface
   - **Estimate:** 3 hours

2. **Task 6.1.2:** Implement OpenAI provider
   - Create OpenAI client wrapper
   - Implement chat completion
   - Handle errors
   - **Estimate:** 4 hours

3. **Task 6.1.3:** Implement Anthropic provider
   - Create Anthropic client wrapper
   - Implement message API
   - Handle errors
   - **Estimate:** 4 hours

4. **Task 6.1.4:** Implement Google Gemini provider
   - Create Gemini client wrapper
   - Implement generation API
   - Handle errors
   - **Estimate:** 4 hours

5. **Task 6.1.5:** Create LLM factory
   - Factory to create provider instance
   - Load provider from config
   - Switch providers dynamically
   - **Estimate:** 3 hours

6. **Task 6.1.6:** Add provider configuration
   - Environment variables for API keys
   - Provider selection config
   - Model selection config
   - **Estimate:** 2 hours

7. **Task 6.1.7:** Test LLM service abstraction
   - Test OpenAI provider
   - Test Anthropic provider
   - Test Google provider
   - Test provider switching
   - **Estimate:** 4 hours

---

### Story 6.2: Document Data Extraction with LLM

**Story Description:** Use LLM to extract structured data from document text (property details, parties, dates, etc.).

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `backend`, `ai`, `extraction`

**Acceptance Criteria:**
- Extract structured data from English text
- Return confidence scores
- Handle extraction errors
- Store extracted data in database

**Tasks:**
1. **Task 6.2.1:** Design extraction prompt templates
   - Create prompts for different document types
   - Define output JSON schema
   - Test prompt effectiveness
   - **Estimate:** 5 hours

2. **Task 6.2.2:** Implement extraction service
   - Create extractDocumentData method
   - Build extraction prompt
   - Call LLM API
   - Parse JSON response
   - **Estimate:** 5 hours

3. **Task 6.2.3:** Define extraction schemas
   - Property document schema
   - Identity document schema
   - Financial document schema
   - Generic document schema
   - **Estimate:** 4 hours

4. **Task 6.2.4:** Implement confidence scoring
   - Calculate confidence per field
   - Flag low-confidence extractions
   - Store confidence scores
   - **Estimate:** 3 hours

5. **Task 6.2.5:** Store extracted data
   - Save to documents.extracted_data JSONB
   - Link to opinion request
   - Update extraction status
   - **Estimate:** 2 hours

6. **Task 6.2.6:** Create extraction endpoint
   - POST /api/v1/documents/:id/extract
   - Trigger extraction
   - Return extracted data
   - **Estimate:** 2 hours

7. **Task 6.2.7:** Test data extraction
   - Test with various document types
   - Test extraction accuracy
   - Test error handling
   - **Estimate:** 4 hours

---

### Story 6.3: Opinion Template Management

**Story Description:** Implement opinion template CRUD operations for bank client-specific formats.

**Story Points:** 5  
**Priority:** High  
**Labels:** `backend`, `api`, `templates`

**Acceptance Criteria:**
- Create, read, update opinion templates
- Templates linked to bank clients
- Template structure defined in JSONB
- Default template per bank client

**Tasks:**
1. **Task 6.3.1:** Create opinion template entity
   - Define data model
   - Define template JSONB structure
   - Create DTOs
   - **Estimate:** 3 hours

2. **Task 6.3.2:** Create template service
   - Implement CRUD methods
   - Link to bank client
   - Set default template
   - **Estimate:** 4 hours

3. **Task 6.3.3:** Create template controller
   - POST /api/v1/opinion-templates
   - GET /api/v1/opinion-templates
   - GET /api/v1/opinion-templates/:id
   - PUT /api/v1/opinion-templates/:id
   - **Estimate:** 3 hours

4. **Task 6.3.4:** Design template JSON structure
   - Define template sections
   - Define placeholders
   - Create template examples
   - **Estimate:** 4 hours

5. **Task 6.3.5:** Implement template validation
   - Validate JSON structure
   - Validate required sections
   - Validate placeholders
   - **Estimate:** 3 hours

6. **Task 6.3.6:** Test template management
   - Test CRUD operations
   - Test template structure
   - Test validation
   - **Estimate:** 2 hours

---

### Story 6.4: Opinion Draft Generation with LLM

**Story Description:** Use LLM to generate draft legal opinions from extracted data and bank client templates.

**Story Points:** 13  
**Priority:** Critical  
**Labels:** `backend`, `ai`, `opinion-generation`

**Acceptance Criteria:**
- Generate opinion draft from extracted data
- Use bank client-specific template
- Opinion always in English
- Draft stored with AI-generated flag

**Tasks:**
1. **Task 6.4.1:** Design opinion generation prompts
   - Create prompt templates
   - Include extracted data
   - Include template structure
   - Test prompt effectiveness
   - **Estimate:** 6 hours

2. **Task 6.4.2:** Implement opinion generation service
   - Create generateOpinionDraft method
   - Load bank client template
   - Build generation prompt
   - Call LLM API
   - **Estimate:** 6 hours

3. **Task 6.4.3:** Implement template application
   - Parse template structure
   - Fill template with extracted data
   - Generate opinion sections
   - Format opinion output
   - **Estimate:** 5 hours

4. **Task 6.4.4:** Ensure English-only generation
   - Configure LLM to generate in English
   - Validate output language
   - Handle non-English responses
   - **Estimate:** 3 hours

5. **Task 6.4.5:** Store generated draft
   - Save to legal_opinions table
   - Set ai_generated flag
   - Set status to DRAFT
   - Store draft content
   - **Estimate:** 2 hours

6. **Task 6.4.6:** Create generation endpoint
   - POST /api/v1/opinions/:id/generate-draft
   - Trigger draft generation
   - Return generated opinion
   - **Estimate:** 2 hours

7. **Task 6.4.7:** Implement auto-generation option
   - Check tenant settings
   - Auto-generate after extraction complete
   - Trigger on document upload completion
   - Send notification to lawyer
   - **Estimate:** 4 hours

---

### Story 6.5: Opinion Review & Editing Workflow

**Story Description:** Allow lawyers to review, edit, and submit AI-generated opinion drafts.

**Story Points:** 8  
**Priority:** Critical  
**Labels:** `backend`, `frontend`, `opinion-workflow`

**Acceptance Criteria:**
- Lawyer can view AI-generated draft
- Lawyer can edit opinion content
- Lawyer can submit final opinion
- Opinion status tracked through workflow

**Tasks:**
1. **Task 6.5.1:** Create opinion editor API endpoints
   - GET /api/v1/opinions/:id
   - PUT /api/v1/opinions/:id
   - PATCH /api/v1/opinions/:id/status
   - **Estimate:** 4 hours

2. **Task 6.5.2:** Implement opinion versioning
   - Track opinion versions
   - Store edit history
   - Maintain version chain
   - **Estimate:** 3 hours

3. **Task 6.5.3:** Add lawyer review tracking
   - Set lawyer_reviewed flag
   - Store review timestamp
   - Track review user
   - **Estimate:** 2 hours

4. **Task 6.5.4:** Implement opinion submission
   - Validate opinion completeness
   - Set status to COMPLETED
   - Store submitted_at timestamp
   - Trigger notifications
   - **Estimate:** 3 hours

5. **Task 6.5.5:** Build opinion editor UI
   - Rich text editor component
   - AI-generated content highlighting
   - Edit mode toggle
   - Save draft functionality
   - **Estimate:** 8 hours

6. **Task 6.5.6:** Add AI assistant panel
   - Show suggestions
   - Missing information alerts
   - Prompt-based generation
   - Quick actions
   - **Estimate:** 6 hours

---

## EPIC 7: Notification System

**Epic Description:** Implement configurable notification system for opinion completion events.

**Epic Goal:** Notify bank clients and end customers when opinions are completed (configurable per bank client).

**Acceptance Criteria:**
- Email notifications sent to bank clients (configurable)
- Email notifications sent to end customers (configurable)
- Email templates customizable per bank client
- Notification settings stored in bank_clients table

---

### Story 7.1: Notification Configuration

**Story Description:** Allow bank clients to configure notification settings (bank and end customer notifications).

**Story Points:** 5  
**Priority:** High  
**Labels:** `backend`, `frontend`, `notifications`

**Acceptance Criteria:**
- Bank client can enable/disable bank notifications
- Bank client can enable/disable end customer notifications
- Settings stored in database
- Settings accessible via API

**Tasks:**
1. **Task 7.1.1:** Add notification fields to bank_clients table
   - notify_bank_on_completion (default: false)
   - notify_end_customer_on_completion (default: false)
   - bank_notification_email_template
   - end_customer_notification_email_template
   - **Estimate:** 2 hours

2. **Task 7.1.2:** Create notification settings API
   - GET /api/v1/bank-clients/:id/notification-settings
   - PUT /api/v1/bank-clients/:id/notification-settings
   - Validate settings
   - **Estimate:** 3 hours

3. **Task 7.1.3:** Build notification settings UI
   - Settings form component
   - Toggle switches for notifications
   - Email template editor
   - Save functionality
   - **Estimate:** 5 hours

---

### Story 7.2: Email Notification Service

**Story Description:** Implement email notification service using AWS SES or SendGrid.

**Story Points:** 8  
**Priority:** High  
**Labels:** `backend`, `notifications`, `aws`

**Acceptance Criteria:**
- Email service configured
- Can send emails to bank clients
- Can send emails to end customers
- Email templates supported
- Delivery tracking

**Tasks:**
1. **Task 7.2.1:** Set up email service provider
   - Configure AWS SES or SendGrid
   - Set up SMTP credentials
   - Verify sender domain
   - **Estimate:** 4 hours

2. **Task 7.2.2:** Create email service module
   - EmailService class
   - Send email method
   - Template rendering
   - Error handling
   - **Estimate:** 5 hours

3. **Task 7.2.3:** Implement email templates
   - Bank notification template
   - End customer notification template
   - Template variables (opinion, request, customer)
   - HTML email support
   - **Estimate:** 4 hours

4. **Task 7.2.4:** Add email delivery tracking
   - Log email sends
   - Track delivery status
   - Handle bounces
   - Retry failed sends
   - **Estimate:** 3 hours

---

### Story 7.3: Opinion Completion Notifications

**Story Description:** Send notifications when opinion is completed (based on bank client configuration).

**Story Points:** 5  
**Priority:** High  
**Labels:** `backend`, `notifications`

**Acceptance Criteria:**
- Notifications sent after opinion submission
- Respects bank client notification settings
- Includes opinion download link
- Notifications logged

**Tasks:**
1. **Task 7.3.1:** Implement notification trigger
   - Hook into opinion submission
   - Check notification settings
   - Trigger notification service
   - **Estimate:** 2 hours

2. **Task 7.3.2:** Create notification service method
   - notifyOpinionCompletion method
   - Load bank client settings
   - Load end customer details
   - Conditional notification logic
   - **Estimate:** 4 hours

3. **Task 7.3.3:** Add notification endpoint
   - POST /api/v1/opinions/:id/notify
   - Manual notification trigger
   - Admin override option
   - **Estimate:** 2 hours

4. **Task 7.3.4:** Generate opinion download links
   - Create signed URLs for PDFs
   - Include in email templates
   - Set expiration
   - **Estimate:** 2 hours

---

## EPIC 8: Reporting & Analytics

**Epic Description:** Build reporting and analytics dashboard for law firms to track performance and compliance.

**Epic Goal:** Provide insights into opinion generation, turnaround times, and compliance metrics.

**Acceptance Criteria:**
- Dashboard with key metrics
- Opinion summary reports
- Audit log viewing
- Export capabilities

---

### Story 8.1: Dashboard Metrics

**Story Description:** Display key metrics on dashboard (pending requests, completed opinions, TAT, etc.).

**Story Points:** 8  
**Priority:** High  
**Labels:** `backend`, `frontend`, `analytics`

**Acceptance Criteria:**
- Dashboard shows real-time metrics
- Metrics filtered by tenant
- Role-based metric visibility
- Metrics update automatically

**Tasks:**
1. **Task 8.1.1:** Design dashboard metrics schema
   - Define required metrics
   - Design aggregation queries
   - Plan caching strategy
   - **Estimate:** 3 hours

2. **Task 8.1.2:** Implement metrics API
   - GET /api/v1/reports/dashboard
   - Calculate pending requests count
   - Calculate completed opinions count
   - Calculate average TAT
   - Calculate by status breakdown
   - **Estimate:** 6 hours

3. **Task 8.1.3:** Build dashboard UI
   - Metrics cards component
   - Charts component
   - Real-time updates
   - Loading states
   - **Estimate:** 8 hours

4. **Task 8.1.4:** Add metric caching
   - Cache dashboard metrics
   - Set TTL
   - Invalidate on updates
   - **Estimate:** 3 hours

---

### Story 8.2: Opinion Summary Reports

**Story Description:** Generate summary reports of opinions by date range, status, bank client, etc.

**Story Points:** 5  
**Priority:** Medium  
**Labels:** `backend`, `frontend`, `reports`

**Acceptance Criteria:**
- Reports filtered by date range
- Reports filtered by bank client
- Reports filtered by status
- Reports exportable to CSV/PDF

**Tasks:**
1. **Task 8.2.1:** Create report generation API
   - GET /api/v1/reports/opinions-summary
   - Filter by date range
   - Filter by bank client
   - Filter by status
   - Aggregate data
   - **Estimate:** 5 hours

2. **Task 8.2.2:** Build report UI
   - Report filters component
   - Report table component
   - Date range picker
   - Export buttons
   - **Estimate:** 6 hours

3. **Task 8.2.3:** Implement CSV export
   - Generate CSV format
   - Download functionality
   - Include all report columns
   - **Estimate:** 3 hours

4. **Task 8.2.4:** Implement PDF export
   - Generate PDF format
   - Format report nicely
   - Include charts/graphs
   - **Estimate:** 4 hours

---

### Story 8.3: Audit Log Viewer

**Story Description:** Allow admins to view audit logs for compliance and tracking.

**Story Points:** 5  
**Priority:** Medium  
**Labels:** `backend`, `frontend`, `audit`

**Acceptance Criteria:**
- Audit logs viewable by admins
- Logs filterable by entity, action, user, date
- Logs searchable
- Logs exportable

**Tasks:**
1. **Task 8.3.1:** Create audit log API
   - GET /api/v1/reports/audit-logs
   - Filter by entity type
   - Filter by action
   - Filter by user
   - Filter by date range
   - Pagination
   - **Estimate:** 4 hours

2. **Task 8.3.2:** Build audit log UI
   - Log table component
   - Filter controls
   - Search functionality
   - Pagination
   - **Estimate:** 6 hours

3. **Task 8.3.3:** Add log detail view
   - Show old/new values
   - Show user details
   - Show IP address
   - Show timestamp
   - **Estimate:** 3 hours

---

## EPIC 9: Security & Compliance

**Epic Description:** Implement security measures, compliance features, and audit logging.

**Epic Goal:** Ensure data security, compliance with Indian regulations, and complete audit trails.

**Acceptance Criteria:**
- All API endpoints secured
- Row-level security implemented
- Audit logging for all actions
- Data encryption at rest and in transit
- Compliance with DPDP Act

---

### Story 9.1: Row-Level Security (RLS)

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
1. **Task 9.1.1:** Enable RLS on all tables
   - ALTER TABLE ... ENABLE ROW LEVEL SECURITY
   - Apply to users, opinion_requests, documents, legal_opinions
   - **Estimate:** 2 hours

2. **Task 9.1.2:** Create RLS policies
   - CREATE POLICY tenant_isolation
   - Use current_setting('app.current_tenant')
   - Test policies
   - **Estimate:** 4 hours

3. **Task 9.1.3:** Implement tenant context middleware
   - Extract tenant_id from JWT
   - Set app.current_tenant session variable
   - Apply to all queries
   - **Estimate:** 4 hours

4. **Task 9.1.4:** Test RLS enforcement
   - Test cross-tenant access attempts
   - Verify data isolation
   - Test edge cases
   - **Estimate:** 3 hours

---

### Story 9.2: Audit Logging

**Story Description:** Log all user actions for compliance and audit purposes.

**Story Points:** 8  
**Priority:** High  
**Labels:** `backend`, `audit`, `compliance`

**Acceptance Criteria:**
- All CRUD operations logged
- Logs include user, entity, action, timestamp
- Logs include old/new values
- Logs searchable and exportable

**Tasks:**
1. **Task 9.2.1:** Create audit logging service
   - AuditService class
   - Log method
   - Store in audit_logs table
   - **Estimate:** 4 hours

2. **Task 9.2.2:** Add audit hooks to all endpoints
   - POST operations
   - PUT operations
   - DELETE operations
   - Status changes
   - **Estimate:** 6 hours

3. **Task 9.2.3:** Capture request metadata
   - IP address
   - User agent
   - Timestamp
   - User ID
   - **Estimate:** 2 hours

4. **Task 9.2.4:** Implement audit log retention
   - Archive old logs
   - Retention policy
   - Cleanup job
   - **Estimate:** 3 hours

---

### Story 9.3: Data Encryption

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
1. **Task 9.3.1:** Enable RDS encryption
   - Configure encryption at rest
   - Use AWS KMS
   - Verify encryption
   - **Estimate:** 2 hours

2. **Task 9.3.2:** Enable S3 encryption
   - Configure S3 bucket encryption
   - Use SSE-S3 or SSE-KMS
   - Verify encryption
   - **Estimate:** 2 hours

3. **Task 9.3.3:** Enforce TLS for APIs
   - Configure HTTPS only
   - Redirect HTTP to HTTPS
   - Verify certificates
   - **Estimate:** 2 hours

4. **Task 9.3.4:** Secure sensitive data in database
   - Encrypt PII fields
   - Use application-level encryption
   - Manage encryption keys
   - **Estimate:** 4 hours

---

### Story 9.4: Compliance Features

**Story Description:** Implement features to comply with Indian regulations (DPDP Act, RBI guidelines).

**Story Points:** 5  
**Priority:** Medium  
**Labels:** `backend`, `compliance`, `legal`

**Acceptance Criteria:**
- Data localization (India region)
- User consent tracking
- Data deletion requests
- Privacy policy integration

**Tasks:**
1. **Task 9.4.1:** Implement data localization
   - Ensure RDS in India region
   - Ensure S3 in India region
   - Verify data residency
   - **Estimate:** 2 hours

2. **Task 9.4.2:** Add consent tracking
   - Store user consents
   - Track consent versions
   - Consent expiry handling
   - **Estimate:** 4 hours

3. **Task 9.4.3:** Implement data deletion
   - User data deletion endpoint
   - Anonymize data
   - Soft delete option
   - Compliance with retention policies
   - **Estimate:** 5 hours

4. **Task 9.4.4:** Add privacy policy acceptance
   - Require acceptance on signup
   - Track acceptance
   - Version management
   - **Estimate:** 3 hours

---

## EPIC 10: Testing & Quality Assurance

**Epic Description:** Comprehensive testing strategy including unit, integration, and E2E tests.

**Epic Goal:** Ensure high code quality and reliability through automated testing.

**Acceptance Criteria:**
- Unit test coverage > 80%
- Integration tests for all APIs
- E2E tests for critical flows
- Performance tests completed

---

### Story 10.1: Unit Testing

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
1. **Task 10.1.1:** Set up testing framework (Backend)
   - Jest/Mocha for Node.js
   - pytest for Python
   - Configure test runner
   - **Estimate:** 3 hours

2. **Task 10.1.2:** Set up testing framework (Frontend)
   - React Testing Library
   - Jest
   - Configure test runner
   - **Estimate:** 3 hours

3. **Task 10.1.3:** Write service unit tests
   - Test all service methods
   - Mock dependencies
   - Test error cases
   - **Estimate:** 20 hours

4. **Task 10.1.4:** Write component unit tests
   - Test all components
   - Test user interactions
   - Test edge cases
   - **Estimate:** 20 hours

5. **Task 10.1.5:** Configure coverage reporting
   - Set up coverage tool
   - Configure thresholds
   - Generate reports
   - **Estimate:** 2 hours

---

### Story 10.2: Integration Testing

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
1. **Task 10.2.1:** Set up integration test environment
   - Test database setup
   - Test data fixtures
   - Test cleanup
   - **Estimate:** 4 hours

2. **Task 10.2.2:** Write API endpoint tests
   - Test all CRUD operations
   - Test authentication
   - Test authorization
   - Test error handling
   - **Estimate:** 20 hours

3. **Task 10.2.3:** Write multi-tenancy tests
   - Test tenant isolation
   - Test cross-tenant access prevention
   - Test tenant filtering
   - **Estimate:** 8 hours

4. **Task 10.2.4:** Write AI workflow tests
   - Test document processing
   - Test opinion generation
   - Test translation flow
   - **Estimate:** 10 hours

---

### Story 10.3: End-to-End Testing

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
1. **Task 10.3.1:** Set up E2E testing framework
   - Install Cypress/Playwright
   - Configure test environment
   - Set up test data
   - **Estimate:** 4 hours

2. **Task 10.3.2:** Write login flow tests
   - Test Keycloak login
   - Test tenant branding
   - Test role-based access
   - **Estimate:** 4 hours

3. **Task 10.3.3:** Write opinion workflow tests
   - Test request creation
   - Test document upload
   - Test opinion generation
   - Test opinion submission
   - **Estimate:** 10 hours

4. **Task 10.3.4:** Write admin flow tests
   - Test user management
   - Test tenant settings
   - Test bank client management
   - **Estimate:** 6 hours

5. **Task 10.3.5:** Add visual regression tests
   - Capture screenshots
   - Compare screenshots
   - Flag differences
   - **Estimate:** 4 hours

---

### Story 10.4: Performance Testing

**Story Description:** Conduct performance testing to ensure system meets NFR requirements.

**Story Points:** 8  
**Priority:** Medium  
**Labels:** `testing`, `performance`, `devops`

**Acceptance Criteria:**
- API response time < 500ms (P95)
- Page load time < 3s
- Document upload < 30s for 50MB
- System handles 500 concurrent users per tenant

**Tasks:**
1. **Task 10.4.1:** Set up performance testing tools
   - Install k6 or JMeter
   - Configure test scenarios
   - Set up monitoring
   - **Estimate:** 4 hours

2. **Task 10.4.2:** Create load test scenarios
   - API load tests
   - Document upload tests
   - Concurrent user tests
   - **Estimate:** 4 hours

3. **Task 10.4.3:** Run performance tests
   - Execute load tests
   - Measure response times
   - Identify bottlenecks
   - **Estimate:** 4 hours

4. **Task 10.4.4:** Optimize based on results
   - Fix performance issues
   - Add caching
   - Optimize queries
   - **Estimate:** 8 hours

---

## EPIC 11: Documentation

**Epic Description:** Create comprehensive documentation for developers, users, and administrators.

**Epic Goal:** Ensure all stakeholders have access to clear, comprehensive documentation.

**Acceptance Criteria:**
- API documentation complete
- User guides available
- Developer setup guides available
- Architecture documentation updated

---

### Story 11.1: API Documentation

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
1. **Task 11.1.1:** Set up Swagger/OpenAPI
   - Install Swagger dependencies
   - Configure OpenAPI spec
   - Set up Swagger UI
   - **Estimate:** 3 hours

2. **Task 11.1.2:** Document all endpoints
   - Add API annotations
   - Document request bodies
   - Document responses
   - Add examples
   - **Estimate:** 10 hours

3. **Task 11.1.3:** Document authentication
   - Document Keycloak flow
   - Document JWT structure
   - Document role requirements
   - **Estimate:** 3 hours

---

### Story 11.2: User Documentation

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
1. **Task 11.2.1:** Create user guides
   - Admin user guide
   - Advocate user guide
   - Paralegal user guide
   - **Estimate:** 8 hours

2. **Task 11.2.2:** Add in-app help
   - Tooltips on UI elements
   - Help modals
   - Contextual help
   - **Estimate:** 6 hours

3. **Task 11.2.3:** Create FAQ
   - Common questions
   - Troubleshooting guide
   - Best practices
   - **Estimate:** 4 hours

---

### Story 11.3: Developer Documentation

**Story Description:** Create setup guides and architecture documentation for developers.

**Story Points:** 5  
**Priority:** Medium  
**Labels:** `documentation`, `developer`

**Acceptance Criteria:**
- Local setup guide
   - Architecture overview
   - Code structure documentation
   - Contribution guidelines

**Tasks:**
1. **Task 11.3.1:** Create setup guide
   - Prerequisites
   - Installation steps
   - Configuration
   - Troubleshooting
   - **Estimate:** 4 hours

2. **Task 11.3.2:** Document architecture
   - System architecture
   - Component diagrams
   - Data flow diagrams
   - **Estimate:** 4 hours

3. **Task 11.3.3:** Document code structure
   - Folder structure
   - Naming conventions
   - Coding standards
   - **Estimate:** 3 hours

---

## EPIC 12: Production Deployment

**Epic Description:** Deploy application to production environment with monitoring and observability.

**Epic Goal:** Successfully deploy and operate the application in production.

**Acceptance Criteria:**
- Application deployed to production
- Monitoring and alerting configured
- Backup and disaster recovery in place
- Performance meets NFRs

---

### Story 12.1: Production Infrastructure Setup

**Story Description:** Set up production infrastructure on AWS using Terraform.

**Story Points:** 13  
**Priority:** Critical  
**Labels:** `devops`, `aws`, `terraform`

**Acceptance Criteria:**
- EKS cluster created
- RDS instance configured
- S3 buckets created
- ElastiCache configured
- All resources in India region

**Tasks:**
1. **Task 12.1.1:** Create Terraform modules
   - EKS module
   - RDS module
   - S3 module
   - ElastiCache module
   - **Estimate:** 10 hours

2. **Task 12.1.2:** Configure EKS cluster
   - Create cluster
   - Set up node groups
   - Configure networking
   - **Estimate:** 6 hours

3. **Task 12.1.3:** Configure RDS
   - Create PostgreSQL instance
   - Configure Multi-AZ
   - Set up backups
   - **Estimate:** 4 hours

4. **Task 12.1.4:** Configure S3
   - Create buckets
   - Configure encryption
   - Set up lifecycle policies
   - **Estimate:** 3 hours

5. **Task 12.1.5:** Configure ElastiCache
   - Create Redis cluster
   - Configure persistence
   - Set up backups
   - **Estimate:** 3 hours

---

### Story 12.2: Kubernetes Deployment

**Story Description:** Create Kubernetes manifests and deploy application to EKS.

**Story Points:** 13  
**Priority:** Critical  
**Labels:** `devops`, `kubernetes`, `deployment`

**Acceptance Criteria:**
- All services deployed to K8s
- Health checks configured
- Auto-scaling configured
- Rolling updates working

**Tasks:**
1. **Task 12.2.1:** Create deployment manifests
   - Frontend deployment
   - Backend deployment
   - Keycloak deployment
   - **Estimate:** 6 hours

2. **Task 12.2.2:** Create service manifests
   - Frontend service
   - Backend service
   - Keycloak service
   - **Estimate:** 2 hours

3. **Task 12.2.3:** Configure ingress
   - ALB ingress controller
   - SSL certificates
   - Routing rules
   - **Estimate:** 4 hours

4. **Task 12.2.4:** Configure health checks
   - Liveness probes
   - Readiness probes
   - Startup probes
   - **Estimate:** 3 hours

5. **Task 12.2.5:** Configure auto-scaling
   - HPA for backend
   - HPA for frontend
   - Set min/max replicas
   - **Estimate:** 4 hours

6. **Task 12.2.6:** Configure ConfigMaps and Secrets
   - Environment variables
   - Database credentials
   - API keys
   - **Estimate:** 3 hours

---

### Story 12.3: Monitoring & Observability

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
1. **Task 12.3.1:** Set up Prometheus
   - Install Prometheus
   - Configure scraping
   - Set up retention
   - **Estimate:** 4 hours

2. **Task 12.3.2:** Set up Grafana
   - Install Grafana
   - Create dashboards
   - Configure data sources
   - **Estimate:** 6 hours

3. **Task 12.3.3:** Set up logging
   - Configure Fluentd/Fluent Bit
   - Send logs to CloudWatch/ELK
   - Set up log aggregation
   - **Estimate:** 4 hours

4. **Task 12.3.4:** Configure alerts
   - Set up AlertManager
   - Define alert rules
   - Configure notifications
   - **Estimate:** 4 hours

5. **Task 12.3.5:** Add application metrics
   - Instrument code
   - Expose metrics endpoint
   - Track business metrics
   - **Estimate:** 6 hours

---

### Story 12.4: Backup & Disaster Recovery

**Story Description:** Implement backup and disaster recovery procedures.

**Story Points:** 8  
**Priority:** High  
**Labels:** `devops`, `backup`, `dr`

**Acceptance Criteria:**
- Automated backups configured
- RTO < 4 hours
- RPO < 1 hour
- DR plan documented

**Tasks:**
1. **Task 12.4.1:** Configure RDS backups
   - Automated daily backups
   - Point-in-time recovery
   - Backup retention
   - **Estimate:** 2 hours

2. **Task 12.4.2:** Configure S3 backups
   - Versioning enabled
   - Cross-region replication
   - Lifecycle policies
   - **Estimate:** 3 hours

3. **Task 12.4.3:** Create backup scripts
   - Database backup script
   - S3 backup script
   - Keycloak backup script
   - **Estimate:** 4 hours

4. **Task 12.4.4:** Test disaster recovery
   - Simulate failure
   - Test restore process
   - Measure RTO/RPO
   - **Estimate:** 6 hours

5. **Task 12.4.5:** Document DR plan
   - Write DR procedures
   - Document recovery steps
   - Create runbooks
   - **Estimate:** 4 hours

---

## Summary

### Epic Summary

| Epic | Stories | Total Story Points | Priority |
|------|---------|-------------------|----------|
| EPIC 1: Infrastructure & DevOps | 4 | 34 | Critical |
| EPIC 2: Database & Data Model | 3 | 21 | Critical |
| EPIC 3: Authentication & Authorization | 3 | 21 | Critical |
| EPIC 4: Multi-Tenancy Foundation | 3 | 21 | Critical |
| EPIC 5: Backend API Development | 8 | 60 | Critical |
| EPIC 6: AI/LLM Integration | 5 | 42 | Critical |
| EPIC 7: Notification System | 3 | 18 | High |
| EPIC 8: Reporting & Analytics | 3 | 18 | High |
| EPIC 9: Security & Compliance | 4 | 26 | High |
| EPIC 10: Testing & QA | 4 | 47 | High |
| EPIC 11: Documentation | 3 | 15 | Medium |
| EPIC 12: Production Deployment | 4 | 42 | Critical |
| **TOTAL** | **47** | **365** | |

### Recommended Sprint Planning

**Sprint 1-2 (Weeks 1-2): Foundation**
- EPIC 1: Infrastructure & DevOps (partial)
- EPIC 2: Database & Data Model
- EPIC 3: Authentication & Authorization (partial)

**Sprint 3-4 (Weeks 3-4): Core Features**
- EPIC 3: Authentication & Authorization (complete)
- EPIC 4: Multi-Tenancy Foundation
- EPIC 5: Backend API Development (partial)

**Sprint 5-6 (Weeks 5-6): Backend & AI**
- EPIC 5: Backend API Development (continue)
- EPIC 6: AI/LLM Integration (partial)

**Sprint 7-8 (Weeks 7-8): AI & Frontend**
- EPIC 6: AI/LLM Integration (complete)
- EPIC 5: Frontend Development (start)

**Sprint 9-10 (Weeks 9-10): Frontend & Features**
- EPIC 5: Frontend Development (continue)
- EPIC 7: Notification System
- EPIC 8: Reporting & Analytics

**Sprint 11-12 (Weeks 11-12): Security & Testing**
- EPIC 9: Security & Compliance
- EPIC 10: Testing & QA (partial)

**Sprint 13-14 (Weeks 13-14): Testing & Deployment**
- EPIC 10: Testing & QA (complete)
- EPIC 12: Production Deployment (partial)
- EPIC 11: Documentation

**Sprint 15-16 (Weeks 15-16): Production Ready**
- EPIC 12: Production Deployment (complete)
- Final testing and bug fixes
- Go-live preparation

---

**Document Control**

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | January 2026 | Solution Architect | Initial JIRA breakdown from HLD v2.0 |

---

*This document should be imported into JIRA and used for sprint planning and task assignment.*