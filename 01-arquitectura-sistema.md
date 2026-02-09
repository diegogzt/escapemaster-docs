# Arquitectura del Sistema - Escapemaster

## 📋 Visión General

Escapemaster implementa una arquitectura de **microservicios colaborativos** donde cada módulo tiene una responsabilidad clara pero se comunica con otros a través de la API central.

## 🏗️ Arquitectura de Alto Nivel

```
┌─────────────────────────────────────────────────────────────────┐
│                    Capa de Presentación                    │
├──────────────┬──────────────┬──────────────┬──────────────┤
│   Web App   │   Admin App   │  Mobile App   │   UI Kit     │
│   (Dueños)   │  (Super-Admin)│  (Game Masters)│  (Design Sys) │
│              │               │               │               │
│  Next.js 16   │   Next.js 16   │   React Native │   React 18     │
│  Tailwind v4   │   Tailwind v4   │   NativeWind   │   Tailwind v3   │
└──────┬───────┴───────┬───────┴───────┬───────┴───────────┘
       │                   │                   │
       └───────────────────┴───────────────────┘
                  │
                  │ HTTPS (Axios)
                  │
                  ↓
┌────────────────────────────────────────────────────────────┐
│                Capa de Backend (API)                  │
│                                                      │
│  ┌──────────────┬──────────────┬──────────────┐ │
│  │   Auth       │   Business    │   Admin       │ │
│  │   (JWT)      │   Logic      │   (/admin/*)   │ │
│  │              │               │               │ │
│  └──────┬───────┴───────┬───────┘       │
│         │                   │               │
│  ┌──────┬───────┬──────┐               │
│  │Supabase│Stripe │Resend│               │
│  │  PG    │Payments│Emails│               │
│  └───────┴───────┴──────┘               │
│                                                      │
└──────────────────────────────────────────────────────┘
```

## 📂 Módulos del Sistema

### 1. Módulo de Autenticación

**Responsabilidad:** Gestionar identidades y sesiones de usuarios

**Componentes:**
- Registro de usuarios (`POST /auth/register`)
- Login con generación de tokens (`POST /auth/login`)
- Refresh de tokens (`POST /auth/refresh`)
- Recuperación de contraseña (`POST /auth/forgot-password`, `/auth/reset-password`)

**Stack:**
- Backend: JWT (python-jose)
- Frontend: Axios interceptors para inyección automática de tokens
- Mobile: Expo SecureStore para almacenamiento seguro

---

### 2. Módulo de Organizaciones (Multi-Tenancy)

**Responsabilidad:** Gestionar tenants (Escape Rooms) del sistema

**Características:**
- Aislamiento completo por `organization_id`
- Configuración de organización (nombre, dirección, logo)
- Gestión de integraciones (Stripe, Resend, Calendar)
- Límites y suscripciones (Free, Pro, Enterprise)

**Base de datos:**
```
organizations (tenant principal)
├── users (usuarios de la org)
├── roles (roles personalizados)
├── rooms (salas de escape)
├── bookings (reservas)
├── payments (pagos)
└── coupons (cupones)

permissions (global, compartido)
└── role_permissions (M2M con roles)
```

---

### 3. Módulo de Gestión de Reservas

**Responsabilidad:** Core del sistema - gestión de reservas y salas

**Componentes:**
- **Gestión de Salas:** CRUD completo, horarios, disponibilidad
- **Gestión de Reservas:** Creación, edición, cancelación, asignación
- **Sistema de Precios:** Precio base, precio dinámico por jugador extra
- **Estado de Reservas:** pending → confirmed → in_progress → completed/cancelled/no_show
- **Calendar Virtualizado:** Manejo eficiente de miles de reservas sin lag

**Endpoints principales:**
```
GET /rooms - Listar salas
GET /rooms/{id}/availability - Ver disponibilidad por fecha
POST /bookings - Crear reserva
GET /bookings - Listar reservas
PUT /bookings/{id}/status - Cambiar estado
POST /bookings/{id}/assign - Asignar a empleado
POST /bookings/{id}/claim - Reclamar reserva
```

---

### 4. Módulo de Roles y Permisos (RBAC)

**Responsabilidad:** Sistema de autorización granular

**Arquitectura:**
- **Permissions:** Atómicos e inmutables (ej. `bookings:create`, `users:delete`)
- **Roles:** Colecciones de permisos, personalizables por organización
- **Roles del sistema:** Owner, Admin, Manager, Employee (no editables)
- **Roles custom:** Editables y eliminables por la organización

**33 Permisos en 8 Categorías:**

| Categoría | Permisos |
|------------|-----------|
| bookings | view, create, update, delete, cancel, manage_status |
| users | view, create, update, delete, manage_roles |
| rooms | view, create, update, delete, manage_schedules |
| coupons | view, create, update, delete, validate |
| payments | view, process, refund |
| stats | view, export |
| settings | view, update, manage_integrations |
| roles | view, create, update, delete |

---

### 5. Módulo de Pagos

**Responsabilidad:** Procesar transacciones y reembolsos

**Integraciones:**
- **Stripe:** Procesar pagos de tarjetas, webhooks, reembolsos
- **TPV:** Terminales de punto de venta configurables

**Flujo de pago:**
```
1. Usuario inicia reserva
2. Usuario selecciona método de pago
3. Si es Stripe: Cliente Stripe → Payment Intent → Webhook → Confirmación
4. Si es TPV: Procesar pago manual en terminal → Registrar transacción
5. Actualizar estado de reserva a "confirmed"
6. Generar factura (PDF o email)
```

---

### 6. Módulo de Dashboard y Analytics

**Responsabilidad:** Métricas y visualización de datos

**Componentes:**
- **Web App:** Dashboard personalizable con widgets arrastrables (DnDKit)
- **Admin App:** Métricas globales (MRR, orgs activas, carga del sistema)
- **Backend API:** Endpoints para stats, revenue, charts

**Endpoints:**
```
GET /dashboard/stats - Estadísticas generales
GET /dashboard/revenue - Ingresos por periodo
GET /dashboard/bookings-chart - Datos para gráfico de reservas
```

---

### 7. Módulo de Notifications

**Responsabilidad:** Comunicaciones transaccionales y de sistema

**Integraciones:**
- **Resend:** Envío de emails transaccionales
- **Stripe Webhooks:** Notificaciones de cambios en pagos
- **Google Calendar (futuro):** Sincronización de eventos

**Tipos de notificaciones:**
- Confirmación de reserva
- Recordatorio (24h antes)
- Cancelación
- Recuperación de contraseña
- Facturación

---

### 8. Módulo de Gamificación (Frontend)

**Responsabilidad:** Sistema de niveles y logros para jugadores

**Componentes:**
- **XP System:** Puntos por jugar, escapar, reseñar
- **Niveles:** Novato (0-500 XP), Investigador (500-2000 XP), Maestro (+2000 XP)
- **Medallas:** Logros visuales (Speedrunner, Sherlock, Night Owl, Marathon)
- **Squads:** Equipos estables con estadísticas agregadas

---

### 9. Módulo de RRHH

**Responsabilidad:** Gestión de recursos humanos

**Componentes:**
- **Gestión de Empleados:** CRUD completo con asignación de roles
- **Vacaciones:** Solicitud y aprobación de días libres
- **Timeclock:** Registro de entrada/salida geolocalizado
- **Control Horario:** Historial de turnos y cálculo de horas

**Endpoints:**
```
GET /users - Listar empleados
POST /users - Crear empleado
POST /vacaciones - Solicitar vacaciones
POST /timeclock/clock-in - Fichar entrada
POST /timeclock/clock-out - Fichar salida
```

---

## 🔐 Estrategias de Seguridad

### 1. Autenticación JWT

- **Access Token:** 30 minutos de validez
- **Refresh Token:** 30 días de validez
- **Almacenamiento seguro:**
  - Web: localStorage/sessionStorage
  - Mobile: Expo SecureStore
- **Rotación automática:** Los refresh tokens se invalidan tras usar

### 2. Multi-Tenancy

- **Aislamiento lógico:** Cada `organization_id` es único y obligatorio
- **Filtrado automático:** All queries incluyen WHERE organization_id = current_org_id
- **Validación de ownership:** Verificar que recursos pertenecen a la organización antes de permitir acceso

### 3. RBAC Granular

- **Permisos atómicos:** Cada permiso es una acción específica (ej. `bookings:create`)
- **Decoradores de permisos:** `@require_permission("users:delete")`
- **Validación en cada endpoint:** Verificar permisos antes de ejecutar acción

### 4. HTTPS y CORS

- **Comunicación encriptada:** Todo el tráfico HTTPS en producción
- **CORS configurado:** Orígenes permitidos configurados en backend
- **CSRF Protection:** Tokens CSRF para formularios POST

---

## 📊 Persistencia de Datos

### PostgreSQL (Supabase)

**Schema multi-tenant:**
- Tablas principales tienen `organization_id`
- Índices en `organization_id` para queries eficientes
- Migraciones versionadas con Alembic

**Modelos (14):**
- Organizations, Users, Roles, Permissions, Rooms, Bookings, BookingGuests, Payments, Coupons, TPV, RoomSchedules, GDPRSignatures, Integrations, Timeclock, PasswordResets

### Almacenamiento Frontend

- **Web:**
  - localStorage: access_token, refresh_token, theme preference
  - sessionStorage: Datos temporales de sesión

- **Mobile:**
  - Expo SecureStore: access_token, refresh_token (encriptados)
  - AsyncStorage: user preferences, settings

---

## 🚀 Despliegue y Hosting

### Entornos

| Ambiente | API | Web | Admin | Mobile | UI Kit |
|----------|-----|-----|--------|---------|----------|
| Desarrollo | localhost:8000 | localhost:3000 | localhost:3001 | Expo Go | localhost:5173 |
| Producción | api.escapemaster.es | TBD | TBD | EAS Build | GitHub Pages |

### Estrategias de Deploy

- **Backend:**
  - Railway (dev/staging)
  - Render (producción recomendada)

- **Frontend (Web/Admin):**
  - Vercel (recomendado por Next.js)
  - Netlify (alternativa)

- **Mobile:**
  - EAS Build para iOS App Store
  - EAS Build para Google Play Store

- **UI Kit:**
  - GitHub Pages para demo
  - NPM package (futuro) para consumo en proyectos

---

## 🔄 Comunicación Inter-Servicios

### Protocolos

- **HTTP/REST:** API RESTful con JSON
- **Seguridad:** HTTPS + JWT en todos los requests
- **Timeouts:** Configurados para evitar bloqueos infinitos
- **Retry Logic:** Exponential backoff para requests fallidos

### Event-Driven (Futuro)

Planes para implementar arquitectura basada en eventos:

- **Webhooks:** Stripe, Google Calendar
- **Message Queue:** Redis/RabbitMQ para tasks asíncronos
- **Real-time:** WebSockets para actualizaciones en vivo de reservas

---

## 📈 Escalabilidad y Performance

### Optimizaciones Implementadas

- **Virtualización:** Calendar en frontend maneja miles de reservas sin lag
- **Connection Pooling:** PostgreSQL con pool_size=10, max_overflow=20
- **Lazy Loading:** Componentes de frontend cargados bajo demanda
- **Code Splitting:** Next.js automatic code splitting por ruta
- **Image Optimization:** Next.js Image component

### Planes de Escalabilidad

- **Horizontal Scaling:** Backend en Railway/Render (auto-scaling configurado)
- **Caching:** Redis para caché de sesiones y datos frecuentes (planeado)
- **CDN:** CDN estático para assets de frontend (planeado)
- **Database Read Replicas:** Réplicas de lectura para alta concurrencia (planeado)

---

## 🧪 Testing Strategy

### Backend (Python)

- **Framework:** Pytest
- **Tipos de tests:**
  - Unit tests: Lógica pura de servicios
  - Integration tests: Test endpoints con base de datos en memoria
  - E2E tests: Flujos completos de usuario
- **Cobertura objetivo:** >80%

### Frontend (Web/Admin)

- **Unit Tests:** Vitest + React Testing Library
- **E2E Tests:** Playwright para flujos críticos
- **Visual Regression:** Percy/Chromatic (planeado)

### Mobile

- **Unit Tests:** Jest + React Native Testing Library
- **E2E Tests:** Detox o Appium (planeado)

---

## 📋 Monitoreo y Observabilidad

### Logging

- **Backend:** Python logging con niveles (DEBUG, INFO, WARNING, ERROR)
- **Frontend:** Sentry para capturar errores en producción
- **Métricas:** Custom events en GA4 (planeado)

### Health Checks

- **Backend:** `/health` endpoint retorna status, version, database connectivity
- **Frontend:** Service Worker para detectar conectividad
- **Uptime Monitors:** UptimeRobot o similar (planeado)

---

**Última actualización:** 4 de febrero de 2026
