# Changelog

## 2025-12-27

- **General:** Completed rebranding of all modules (Web, Admin, API, UI Kit) to **Escapemaster**.
- **General:** Updated API URL to `https://api.escapemaster.es` across all services and environment variables.
- **API:** Synchronized Supabase database with SQLAlchemy models (added `vacations` table and HR-related fields in `users` and `timeclock`).
- **API:** Enhanced room response with pending bookings and next sessions.
- **API:** Switched email service to SMTP (Hostinger) for better reliability.
- **API:** Implemented HR management backend (Vacations and Timeclock modules).
- **API:** Updated multiple routes (Bookings, Coupons, Organizations, Roles, Rooms, TPV, Webhooks) for consistency and new features.
- **Web:** Removed width constraints (`max-w-4xl`) in dashboard pages (Rooms, Bookings, Docs) for full-width layout.
- **Web:** Migrated Next.js middleware to `proxy.ts` to eliminate deprecated warnings and maintain E2E flows.
- **Web:** Mobile UI overhaul, including calendar fixes (weekly list default view) and touch-friendly improvements.
- **Web:** Added advanced filters to the bookings page (rooms, dates, status).
- **Web:** Implemented new frontend sections for HR management: `/hr-management`, `/time-tracking`, and `/roles`.
- **Admin:** Initialized testing infrastructure with Vitest and updated UI for rebranding.
- **UI Kit:** Added "Vista" color palette and enhanced visibility controls for components.
- **Docs:** Updated `master.agent.md` with SSH access instructions for the API server.

## 2025-12-26

- **Security (API):** Removed default Super Admin credentials (now required via env), enforced JWT `type=access`, and reduced token error leakage.
- **Security (API):** Webhook verification enabled for Stripe (`Stripe-Signature` + `STRIPE_WEBHOOK_SECRET`).
- **Security (API):** Upload hardening (max size, SVG blocked) and stricter RBAC/tenancy checks for bookings/coupons/organizations/TPV/roles.
- **Security (Web/Admin):** Added baseline security headers in Next.js and removed sensitive admin login logging.
- **Testing (Web):** Stabilized Playwright E2E by aligning specs with current Spanish UI/routes, using JWT-shaped tokens for auth, and preventing HTML reporter from blocking runs.
- **Testing (Web):** Separated unit vs E2E discovery (Vitest excludes `e2e/**`) to avoid runner conflicts.
- **Tooling (Web):** Fixed Playwright `webServer` root/cwd resolution in monorepo setups and set `turbopack.root` to avoid Turbopack root/lockfile warnings.
- **Tooling (Web):** Migrated Next request interception from `src/middleware.ts` to `src/proxy.ts` to remove the deprecated middleware file convention warning.
