# Escapemaster Admin - The Superadmin Console

## 1. Introduction
**Escapemaster Admin** (Escapemaster Manage) is the administrative command center for the entire Escapemaster platform. While Escapemaster Web is for hotel staff, Escapemaster Admin is for the platform owners and support team. It provides god-mode access to configure organizations, manage subscriptions, and oversee system health.

## 2. Security & Architecture
Escapemaster Admin is built as a completely separate Next.js application from Escapemaster Web. This physical separation ensures:
- **Security Isolation**: Admin routes are not even present in the client-facing bundle.
- **Distinct Auth Flows**: Escapemaster Admin uses a separate admin authentication guard.
- **Dedicated API Access**: Escapemaster Admin interacts with privileged `/admin/*` endpoints that are inaccessible to standard users.

---

## 3. Core Modules & Workflows

### 3.1 Organization Lifecycle Management
This module handles the end-to-end lifecycle of a client (Hotel).
1. **Onboarding**: A streamlined wizard to create the tenant entity.
   - Sets up the organization profile.
   - Configures the initial subscription plan (Free, Pro, Enterprise).
   - Generates the unique `invitation_code`.
2. **Configuration**:
   - Enable/Disable specific modules (e.g., turn off TPV for a B&B).
   - Set limits (max users, max rooms).
3. **Suspension/Termination**: One-click "Kill Switch" to suspend access for non-payment or policy violations.

### 3.2 Advanced User & Role Management
Escapemaster Admin provides a cross-organizational view of all users.
- **Global User Search**: Find any user across any organization by email or name.
- **Role Injection**: Administrators can inject themselves into any organization to provide support (Impersonation).
- **Permission Matrix Editor**: A visual interface to define what each Role can do.
  - **Granularity**: We control access down to the button level.
  - **Inheritance**: Define base roles that organizations can extend.

### 3.3 System Health & Analytics (Dashboard)
- **Real-time Metrics**:
  - Total Active Organizations.
  - Total Monthly Recurring Revenue (MRR).
  - System Load & API Latency.
- **Audit Logs**: A chronological timeline of all critical actions taken within the system for accountability.

---

## 4. UI/UX Design System
Escapemaster Admin uses a distinct visual identity from Escapemaster Web to prevent confusion.
- **Color Scheme**: Uses a professional "Forest Green" & "Beige" palette (`#1F6357`, `#E8F5F3`) to convey stability and authority.
- **Layout**: A dense, data-heavy layout optimized for desktop monitors.
- **Components**: Heavy usage of Data Tables with advanced filtering, sorting, and bulk actions.

---

## 5. Technical Implementation Details

### 5.1 Protected Routes
We implement a Higher-Order Component (HOC) and Middleware strategy to protect routes.
- `useAdminAuth`: A custom hook that validates the admin token before rendering any content.
- **Redirect Logic**: Unauthenticated attempts are immediately bounced to the login screen.

### 5.2 Data Fetching
- **SWR (Stale-While-Revalidate)**: Used for fetching dashboard data to ensure the admin always sees live data without manual refreshing.
- **Optimistic UI**: Action buttons (like "Suspend User") update the UI immediately while the API request processes in the background.

---

## 6. Upcoming Features
- **Billing Dashboard**: Integration with Stripe Connect to manage platform payouts.
- **White-label Configuration**: Tools to upload custom logos and domains for Enterprise clients.
- **Automated Onboarding Links**: Generate one-time links for clients to self-onboard.
