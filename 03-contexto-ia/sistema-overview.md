# Sistema Overview - Escapemaster

## Arquitectura del Ecosistema

Escapemaster es un SaaS multi-tenant diseñado para la gestión completa de Escape Rooms. El sistema está compuesto por 5 módulos principales que trabajan en conjunto:

### 1. Backend API (`/manager/api/`)

**Tecnología**: FastAPI (Python 3.13) + SQLAlchemy 2.0

**Base de datos**: PostgreSQL (Supabase) con migraciones Alembic

**Responsabilidad**:
- Núcleo lógico del sistema
- Gestión de autenticación (JWT + Supabase Auth)
- API REST con 70+ endpoints
- Lógica de negocio multi-tenant
- Integraciones externas (Stripe, Resend, Google Calendar)

**Key features**:
- Multi-tenancy: Aislamiento completo por `organization_id`
- RBAC: Sistema de roles y permisos con 33 permisos granulares
- Gestión de reservas, salas, usuarios, pagos, cupones
- TPV y terminales de punto de venta
- Webhooks para integraciones externas

**Modelos de datos (14)**:
- Organizations, Users, Roles, Permissions, Rooms, Bookings, Payments, Coupons, TPV, RoomSchedules, GDPRSignatures, Integrations, Timeclock, PasswordResets

---

### 2. Web App (`/manager/gestor/`)

**Tecnología**: Next.js 16 (React 19) + TypeScript 5.0

**Styling**: Tailwind CSS v4 + CSS Variables para theming dinámico

**State Management**: Zustand (global store) + React Context (Theme/Auth)

**Responsabilidad**:
- Dashboard principal para dueños de Escape Rooms
- Interfaz de gestión diaria de operaciones
- Gestión de salas y configuración
- Calendar interactivo de reservas
- Gestión de pagos y facturación

**Key features**:
- Calendar virtualizado (miles de reservas sin lag)
- Drag-and-drop para reprogramación de reservas
- Sistema de temas dinámico (8+ paletas: Twilight, Tropical, Vista, etc.)
- Dashboard basado en widgets con DnDKit
- Gestión de RRHH (vacaciones, time-tracking)
- Formularios validados con React Hook Form + Zod

**Stack adicional**:
- Axios para llamadas API
- Lucide React para iconos
- Recharts para gráficos
- Playwright para E2E tests
- Vitest para unit tests

---

### 3. Admin App (`/manager/panel-admin/`)

**Tecnología**: Next.js 16 + TypeScript

**Styling**: Tailwind CSS v4

**Responsabilidad**:
- Super-admin para gestión del sistema
- Gestión de organizaciones (tenants)
- Control de suscripciones y facturación
- Soporte técnico global

**Key features**:
- Gestión de ciclo de vida de organizaciones
- Inyección en organizaciones para soporte
- Editor de permisos visuales
- Dashboard de métricas globales (MRR, orgs activas)
- Logs de auditoría

**Seguridad**:
- Aplicación físicamente separada de Web
- Endpoints `/admin/*` protegidos
- Flujo de autenticación dedicado

---

### 4. Mobile App (`/manager/staff-mobile/`)

**Tecnología**: React Native + Expo SDK 54

**Routing**: Expo Router

**Styling**: NativeWind (Tailwind para React Native)

**State Management**: Zustand

**Responsabilidad**:
- App para Game Masters y staff móvil
- Operaciones en sala (check-in, cronómetro)
- Control horario de empleados

**Key features**:
- Escaneo QR de reservas al llegar
- Cronómetro sincronizado con la sala
- Checklists interactivos de reset de sala
- Fichaje de entrada/salida geolocalizado
- Token almacenado en Expo Secure Store

**API Connection**: Se conecta a EscapeMaster API en `https://api.escapemaster.es`

---

### 5. UI Kit (`/manager/ui-kit/`)

**Tecnología**: React 18.2 + TypeScript 5.3 + Tailwind CSS 3.3

**Build Tool**: Vite 5.4

**Responsabilidad**:
- Biblioteca de componentes compartidos
- Sistema de diseño unificado
- Paletas de colores y theming

**Key features**:
- 20+ componentes React tipados
- 18 paletas de colores profesionales
- Componente Auth con popup de login
- Sistema de variables CSS para theming
- Documentación completa
- Deploy en GitHub Pages (https://diegogzt.github.io/manager/ui-kit/)

**Componentes principales**:
- Layout: Card, Container, Grid
- Forms: Button, Input, Textarea, Select, Checkbox, Radio, Toggle
- Data Display: Badge, Avatar, Table, List, Tabs
- Feedback: Alert, Spinner, ProgressBar
- Calendar: Componente de calendario completo (3 tamaños, 3 variantes)

---

## Flujo de Datos y Dependencias

```
┌─────────────────────────────────────────────────────────────────┐
│                     Mobile App (Expo)                      │
│                  ┌──────────────┐                           │
│                  │  Game Master │                           │
│                  │  QR Scanner   │                           │
│                  │  Timer       │                           │
│                  └───────┬──────┘                           │
│                          │ API Calls (Axios)                  │
└──────────────────────────┼──────────────────────────────────────┘
                           │ HTTPS
┌──────────────────────────┼──────────────────────────────────────┐
│                    Backend API (FastAPI)                    │
│         ┌──────────────┼──────────────┐                    │
│         │  Auth  │  RBAC  │  Business Logic             │
│         │  JWT   │ 33 Perms│  Multi-tenant              │
│         └──────────────┼──────────────┘                    │
│                        │                                  │
│         ┌──────────────┼──────────────┐                    │
│         │ Supabase  │  Stripe   │  Resend               │
│         │ PostgreSQL │ Payments  │  Emails               │
│         └──────────────┼──────────────┘                    │
└──────────────────────────┼──────────────────────────────────────┘
                           │ API Calls (Axios)
         ┌───────────────────┼───────────────────┐
         │                                       │
┌────────┼────────┐                    ┌───────────┼──────────┐
│ Web   │ Admin  │                    │ UI Kit               │
│ App   │ App    │                    │ (Shared Components)  │
│       │        │                    └───────────────────────┘
│       │        │
│ Users/Staff │ Platform Owners
│ Dashboard  │ Super-admin
│ Bookings   │ Orgs Mgmt
└───────┼─────┘
        │
   UI Components (imported)
        │
└───────────────────┘
```

---

## Entornos y Deploy

| Proyecto | Despliegue | URL |
|----------|--------------|-----|
| Backend API | Railway/Render | `api.escapemaster.es` |
| Web App | Vercel/Netlify | TBD |
| Admin App | Vercel/Netlify | TBD |
| Mobile App | EAS (Expo Application Services) | TBD |
| UI Kit | GitHub Pages | `https://diegogzt.github.io/manager/ui-kit/` |

---

## URL Base de API

- **Desarrollo**: `http://localhost:8000`
- **Producción**: `https://api.escapemaster.es`

**Documentación API Interactiva**:
- Swagger UI: `/docs`
- ReDoc: `/redoc`

---

## Arquitectura Multi-Tenant

El sistema usa **multi-tenancy lógico**:
- Todos los datos se almacenan en las mismas tablas PostgreSQL
- Cada tabla principal tiene `organization_id`
- Los requests se filtran automáticamente por el `organization_id` del usuario autenticado
- Permisos RBAC controlan el acceso a recursos dentro de la organización

---

## Estándares Técnicos Compartidos

### Backend
- **Lenguaje**: Python 3.13
- **Framework**: FastAPI 0.104+
- **ORM**: SQLAlchemy 2.0 (async)
- **Validación**: Pydantic v2
- **Testing**: Pytest

### Frontend (Web/Admin)
- **Framework**: Next.js 16 (React 19)
- **Lenguaje**: TypeScript 5.0+
- **Styling**: Tailwind CSS v4
- **Testing**: Vitest + Playwright

### Mobile
- **Framework**: React Native + Expo 54
- **Lenguaje**: TypeScript
- **Styling**: NativeWind (Tailwind)
- **Routing**: Expo Router

### UI Kit
- **Framework**: React 18.2
- **Lenguaje**: TypeScript 5.3
- **Styling**: Tailwind CSS 3.3
- **Build**: Vite 5.4

---

**Última actualización:** 4 de febrero de 2026
