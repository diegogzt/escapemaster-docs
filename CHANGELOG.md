# Changelog

## 2025-01-15

### B2B Onboarding - Sistema Completo de Verificación KYB

- **Database:** Ejecutada migración masiva con 26 nuevas tablas para soportar onboarding B2B y marketplace B2C.
  - Nuevas tablas KYB: `organization_kyb_documents`, `organization_bank_accounts`
  - Sistema de pagos: `payouts`, `payout_bookings`
  - Marketplace: `players`, `player_preferences`, `xp_transactions`, `player_credits`
  - Social: `squads`, `squad_members`, `squad_invitations`
  - Reviews: `reviews`, `room_photos`
  - Gamification: `achievements`, `player_achievements`
  - Split Payment: `split_payment_lobbies`, `split_payment_participants`
  - Collections: `curated_collections`, `collection_rooms`
  - Extras: `room_extras`, `booking_extras`

- **API - B2B Endpoints:**
  - `/kyb/*` - Gestión de documentos KYB, verificación CIF, cuentas bancarias con validación IBAN
  - `/payouts/*` - Liquidaciones semanales, historial de pagos, facturas

- **API - B2C Endpoints:**
  - `/players/*` - Perfiles de jugador, XP, rangos, preferencias
  - `/squads/*` - Creación de grupos, invitaciones, gestión de miembros
  - `/reviews/*` - Reviews con ratings múltiples, fotos, moderación
  - `/achievements/*` - Sistema de logros con progreso y desbloqueo automático
  - `/split-payment/*` - Pagos divididos para reservas grupales
  - `/marketplace/*` - Búsqueda de salas, colecciones curadas, descubrimiento

- **Admin UI:**
  - Nueva página `/kyb` - Panel de verificación de documentos KYB con preview, aprobación/rechazo
  - Nueva página `/payouts` - Gestión de liquidaciones semanales a empresas
  - Actualizado Sidebar con sección "Onboarding B2B"

- **Models & Schemas:** 8 nuevos archivos de modelos SQLAlchemy, 5 nuevos schemas Pydantic
- **Triggers DB:** Auto-actualización de rangos según XP, actualización de stats de salas en reviews

## 2026-01-03 (Evening)

- **Web:** Refactorización completa para eliminar todos los datos mock hardcoded.
  - `bookings/page.tsx`: Usa `bookingsApi.list()` en lugar de `MOCK_BOOKINGS`
  - `bookings/[id]/page.tsx`: Usa `bookingsApi.get()` en lugar de `MOCK_BOOKING_DETAILS`
  - `calendar/page.tsx`: Usa API real en lugar de `generateMockSessions()`
  - `reports/page.tsx`: Obtiene datos reales de la API en lugar de constantes mock
  - `calendar-widget.tsx`: Obtiene salas desde la API
  - `revenue-table.tsx`: Obtiene transacciones desde `bookingsApi`
- **Web:** Actualizado `api.ts` para manejar respuestas paginadas (`.bookings`, `.rooms`).
- **Web:** Implementados estados de carga y error en todas las páginas refactorizadas.
- **Despliegue:** Desplegada la web actualizada a producción en Vercel.

## 2026-01-04

- **Mobile:** Auditoría extensa de UI/UX completada. Refactorizado el sistema de colores a variables semánticas y estandarizado el feedback táctil.
- **Mobile:** Refactorizado el flujo de autenticación (Login, Registro, Recuperación) para mayor consistencia visual y mejor manejo de teclado.
- **Mobile:** Implementada suite de pruebas con Vitest y React Testing Library. Añadidos tests para servicios API, hooks y componentes.
- **Mobile:** Nuevo componente `StatusBadge` para visualización estandarizada de estados.
- **Mobile:** Mejorada la consistencia en las pantallas de Dashboard, Calendario, Salas y Reservas.

## 2026-01-03

- **Mobile:** Implementada vista semanal en el calendario. Ahora los usuarios pueden alternar entre vista diaria y semanal, con las sesiones agrupadas por día.
- **Mobile:** Expandido el Dashboard con nuevos widgets: Gráfico de Reservas, Salas Populares y Tasa de Ocupación.
- **Mobile:** Corregido error 401 ruidoso en los logs al iniciar la aplicación sin sesión activa.
- **API:** Añadido soporte para filtrado por rango de fechas (`date_from`, `date_to`) en el listado de reservas.

## 2025-12-31

- **Web:** Implementado sistema de configuración de widgets en el dashboard. Ahora los usuarios pueden elegir qué estadísticas visualizar (Ingresos, Reservas, Ocupación, Ticket Medio).
- **Admin:** Rediseño completo del formulario de creación de usuarios para evitar solapamientos y mejorar la accesibilidad en dispositivos móviles.
- **API:** Corregido error en la creación de usuarios. Se ha implementado el hashing de contraseñas (bcrypt) y se ha hecho obligatorio el campo `full_name`.
- **API:** Ejecutada migración de base de datos para añadir la columna `hashed_password` a la tabla `users`.
- **Despliegue:** Actualizadas las aplicaciones Web y Admin en Vercel (Next.js 16.1.0).
- **Despliegue:** Actualizada la API en el servidor de producción mediante SSH y Docker.

## 2025-12-28

- **Web:** Implemented high-end animations inspired by industry leaders (GSAP, Lusion, Gianluca Jahn).
- **Web:** Added **Magnetic Button** effect to the main CTA for improved interactivity.
- **Web:** Integrated **Mouse Parallax** background effects for a sense of depth.
- **Web:** Enhanced text reveal animations with skew and stagger effects.
- **Web:** Refined scroll-based animations using GSAP ScrollTrigger with scale and smooth easing.
- **Web:** Updated Tailwind classes to the latest standards (e.g., `bg-linear-to-b`).
- **Deployment:** Deployed the enhanced landing page to production on Vercel.
- **Web:** Updated the "Coming Soon" landing page to the **Tropical** theme with a new color palette (Emerald/Amber).
- **Web:** Added a dedicated section for the upcoming **iOS and Android** mobile apps.
- **Web:** Fixed public access to legal pages (`/privacy`, `/cookies`) by whitelisting them in the authentication context.
- **Web:** Styled legal pages to match the Tropical theme.
- **Deployment:** Deployed the updated landing page to production on Vercel.
- **Web:** Expanded the "Coming Soon" landing page with detailed sections: Features, Customization, and Early Access Waitlist.
- **Web:** Created Privacy Policy and Cookies Policy pages with detailed information on data handling and cookie usage (auth tokens, preferences).
- **Web:** Integrated GSAP ScrollTrigger for scroll-based animations on the landing page.
- **Web:** Added a functional signup form for the waitlist.
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
