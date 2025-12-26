# Changelog

## 2025-12-26

- **Security (API):** Removed default Super Admin credentials (now required via env), enforced JWT `type=access`, and reduced token error leakage.
- **Security (API):** Webhook verification enabled for Stripe (`Stripe-Signature` + `STRIPE_WEBHOOK_SECRET`).
- **Security (API):** Upload hardening (max size, SVG blocked) and stricter RBAC/tenancy checks for bookings/coupons/organizations/TPV/roles.
- **Security (Web/Admin):** Added baseline security headers in Next.js and removed sensitive admin login logging.
- **Testing (Web):** Stabilized Playwright E2E by aligning specs with current Spanish UI/routes, using JWT-shaped tokens for auth, and preventing HTML reporter from blocking runs.
- **Testing (Web):** Separated unit vs E2E discovery (Vitest excludes `e2e/**`) to avoid runner conflicts.
- **Tooling (Web):** Fixed Playwright `webServer` root/cwd resolution in monorepo setups and set `turbopack.root` to avoid Turbopack root/lockfile warnings.
- **Tooling (Web):** Migrated Next request interception from `src/middleware.ts` to `src/proxy.ts` to remove the deprecated middleware file convention warning.
