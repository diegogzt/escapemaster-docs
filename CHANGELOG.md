# Changelog

## 2025-12-28

- **Web:** Replaced the main landing page with a "Coming Soon" / "In Development" page.
- **Web:** Integrated GSAP for high-quality animations on the new landing page.
- **Web:** Removed public login/register buttons from the landing page while keeping `/login` accessible via direct URL.
- **Web:** Created a backup branch `feature/original-landing` containing the previous landing page.
- **Mobile:** Implemented a multi-theme system (Tropical, Twilight, Vista, Mint, Sunset, Ocean, Lavender, Fire) matching the web version.
- **Mobile:** Added theme persistence using Zustand and AsyncStorage.
- **Mobile:** Refactored all main screens (Dashboard, Rooms, Calendar, Login, Register) to use theme-aware Tailwind classes (`bg-primary`, `text-primary`, etc.).
- **Mobile:** Fixed iOS-specific crash in the registration flow by optimizing `KeyboardAvoidingView` behavior.
- **Mobile:** Implemented permission-based UI logic using a new `usePermissions` hook.
- **API:** Deployed missing `/dashboard/summary` endpoint to production server to fix 404 errors in the mobile app.
- **API:** Updated production environment via SSH and Docker container restart.

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
- **Deployment:** Successfully deployed all components (Web, Admin, API) to production environments.
- **Deployment:** Configured API server on VPS with Docker and Nginx reverse proxy on port 8001.

## 2025-12-26

- **Security (API):** Removed default Super Admin credentials (now required via env), enforced JWT `type=access`, and reduced token error leakage.
- **Security (API):** Webhook verification enabled for Stripe (`Stripe-Signature` + `STRIPE_WEBHOOK_SECRET`).
- **Security (API):** Upload hardening (max size, SVG blocked) and stricter RBAC/tenancy checks for bookings/coupons/organizations/TPV/roles.
- **Security (Web/Admin):** Added baseline security headers in Next.js and removed sensitive admin login logging.
- **Testing (Web):** Stabilized Playwright E2E by aligning specs with current Spanish UI/routes, using JWT-shaped tokens for auth, and preventing HTML reporter from blocking runs.
- **Testing (Web):** Separated unit vs E2E discovery (Vitest excludes `e2e/**`) to avoid runner conflicts.
- **Tooling (Web):** Fixed Playwright `webServer` root/cwd resolution in monorepo setups and set `turbopack.root` to avoid Turbopack root/lockfile warnings.
- **Tooling (Web):** Migrated Next request interception from `src/middleware.ts` to `src/proxy.ts` to remove the deprecated middleware file convention warning.
