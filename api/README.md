# Escapemaster API Documentation

## 1. System Overview & Architecture
Escapemaster API serves as the central nervous system for the Escapemaster hospitality ecosystem. Built on **FastAPI**, it leverages modern asynchronous Python capabilities to ensure high performance and scalability. The architecture follows a **Service-Repository pattern** to ensure separation of concerns, testability, and maintainability.

### 1.1 Core Technology Stack
- **Framework**: FastAPI (0.104+) - Chosen for its speed, automatic OpenAPI documentation, and native async support.
- **Database**: PostgreSQL 15+ - Robust relational database for structured data integrity.
- **ORM**: SQLAlchemy 2.0 (Async) - Modern, type-safe database interactions.
- **Migration Tool**: Alembic - Version control for database schema changes.
- **Authentication**: Supabase Auth (JWT) - Secure, scalable identity management.
- **Validation**: Pydantic v2 - High-performance data validation and serialization.

### 1.2 Directory Structure
```
/app
├── core/           # Global configs, security, and exceptions
├── db/             # Database connection and base models
├── models/         # SQLAlchemy ORM models (Users, Orgs, Roles)
├── schemas/        # Pydantic schemas for request/response
├── api/            # Route handlers grouped by module
├── services/       # Business logic layer
└── main.py         # Application entry point
```

---

## 2. Database Schema & Data Models

### 2.1 Multi-Tenancy Architecture
The system is designed with **logical multi-tenancy**. All data is stored in shared tables but strictly partitioned by `organization_id`.
- **Organizations Table**: The root entity. Contains subscription details, settings, and status.
- **Users Table**: Linked to organizations. Stores profile data and authentication references.
- **Roles & Permissions**: A sophisticated RBAC (Role-Based Access Control) system.

### 2.2 Role-Based Access Control (RBAC)
We have implemented a granular permission system rather than simple role checks.
- **Permissions**: Atomic actions (e.g., `users:create`, `reports:view`). There are currently **33 distinct permissions**.
- **Roles**: Collections of permissions.
  - **System Roles**: Immutable roles provided by default (`Superadmin`, `Admin`, `Manager`, `Receptionist`).
  - **Custom Roles**: Organizations can create their own roles with specific permission sets.

---

## 3. Key API Modules & Endpoints

### 3.1 Authentication & Security
The API enforces security via Bearer Tokens (JWT).
- **Middleware**: Custom middleware validates Supabase tokens on every protected request.
- **Context**: The `current_user` dependency injects the authenticated user into route handlers.

### 3.2 Organization Management (`/admin/organizations`)
- **POST /create**: Initiates the onboarding process. Creates the org entity and the initial admin user.
- **GET /{id}**: Retrieves full organization hierarchy including active users and subscription tier.
- **PATCH /{id}/status**: Toggles organization access (Active/Suspended).

### 3.3 User Management (`/users`)
- **POST /invite**: Sends an invitation email to a new user.
- **POST /create**: (Dev/Admin) Direct user creation bypassing email flow for rapid onboarding.
- **GET /me**: Returns the profile of the currently logged-in user with their computed permissions list.

### 3.4 Role Management (`/roles`)
- **GET /permissions**: Lists all available system permissions.
- **POST /**: Create a new custom role for an organization.
- **PUT /{id}/permissions**: Update the permission set for a specific role.

---

## 4. Development & Deployment

### 4.1 Local Development
The project includes a `start.sh` script for rapid local setup.
```bash
# Starts the server with hot-reload on port 8000
./start.sh
```

### 4.2 Environment Variables
Required configuration in `.env`:
- `DATABASE_URL`: PostgreSQL connection string (asyncpg driver).
- `SUPABASE_URL`: URL for the Supabase project.
- `SUPABASE_KEY`: Anon key for client-side operations.
- `SUPABASE_SERVICE_ROLE_KEY`: Admin key for backend management.

---

## 5. Future Roadmap & Pending Features

### 5.1 Immediate Priority (Q4 2025)
- **Stripe Integration**: Automate subscription billing and invoice generation.
- **Webhooks**: Event-driven architecture for external integrations (e.g., sending data to CRM).
- **Audit Logging**: Comprehensive tracking of all administrative actions for security compliance.

### 5.2 Long-term Goals
- **AI Concierge**: Integration with LLMs for automated guest responses.
- **IoT Integration**: Control smart locks and room thermostats directly from the API.
