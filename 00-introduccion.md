# Introducción al Ecosistema Escapemaster

## 🎮 ¿Qué es Escapemaster?

Escapemaster es un **SaaS multi-tenant completo** diseñado para la gestión integral de Escape Rooms. La plataforma permite a dueños y gerentes de Escape Rooms gestionar todos los aspectos de su negocio desde una interfaz centralizada.

### Propuesta de Valor

Para **Dueños de Escape Rooms (B2B)**:
- **Gestión completa de salas:** Configurar horarios, precios, capacidades
- **Reservas eficientes:** Calendar interactivo con disponibilidad en tiempo real
- **Pagos automatizados:** Integración con Stripe para procesar pagos
- **Gestión de personal:** Sistema de roles y permisos granular (RBAC)
- **Métricas y analytics:** Dashboards personalizables con insights de negocio
- **Multi-canal de ventas:** Marketplace, widget propio, TPV
- **Liquidaciones automáticas:** Payouts semanales con comisiones de plataforma

Para **Jugadores (Usuarios Finales B2C)**:
- **Búsqueda inteligente:** Filtros avanzados por ubicación, temática, dificultad
- **Reserva simplificada:** Pago dividido para grupos (Split Payment 2.0)
- **Sistema de squads:** Crear equipos estables con amigos
- **Gamificación:** Niveles (XP), medallas (Achievements), rankings
- **Reseñas verificadas:** Solo usuarios reales pueden reseñar
- **Muro de la fama:** Compartir fotos de equipo

## 🏗️ Arquitectura del Sistema

El ecosistema Escapemaster está compuesto por **5 módulos principales** que trabajan en conjunto:

### 1. Backend API (`/manager/api/`)

**Tecnología:** FastAPI (Python 3.13)

**Responsabilidad:** Núcleo lógico del sistema

**Características principales:**
- Multi-tenancy con aislamiento por `organization_id`
- Sistema RBAC con 33 permisos granulares
- API REST con 70+ endpoints
- Integraciones: Stripe (pagos), Resend (emails), Google Calendar
- Base de datos: PostgreSQL (Supabase)

### 2. Escapemaster Web (`/manager/gestor/`)

**Tecnología:** Next.js 16 (React 19)

**Responsabilidad:** Dashboard de gestión para dueños de Escape Rooms

**Características principales:**
- Calendar virtualizado (miles de reservas sin lag)
- Drag-and-drop para reprogramación
- Sistema de temas dinámico (8+ paletas)
- Dashboard basado en widgets con DnDKit
- Gestión completa de RRHH (vacaciones, time-tracking)

### 3. Escapemaster Admin (`/manager/panel-admin/`)

**Tecnología:** Next.js 16

**Responsabilidad:** Super-admin para gestión del sistema

**Características principales:**
- Gestión del ciclo de vida de organizaciones
- Inyección en organizaciones para soporte (impersonación)
- Dashboard de métricas globales (MRR, orgs activas)
- Logs de auditoría para accountability

### 4. Escapemaster Mobile (`/manager/staff-mobile/`)

**Tecnología:** React Native + Expo SDK 54

**Responsabilidad:** App móvil para Game Masters y staff

**Características principales:**
- Escaneo QR de reservas
- Cronómetro sincronizado
- Checklists interactivos de tareas
- Fichaje geolocalizado

### 5. Escapemaster UI Kit (`/manager/ui-kit/`)

**Tecnología:** React 18 + TypeScript + Tailwind CSS

**Responsabilidad:** Biblioteca de componentes compartidos y sistema de diseño

**Características principales:**
- 20+ componentes React tipados
- 18 paletas de colores profesionales
- Sistema de theming con variables CSS
- Documentación completa

## 🔄 Flujo de Trabajo Típico

### Para Desarrolladores

1. **Clonar repositorios** necesarios para el trabajo
2. **Configurar variables de entorno** en cada proyecto
3. **Iniciar servicios** en el orden correcto:
   - Primero: Backend API (puerto 8000)
   - Después: Web App (puerto 3000) y Admin App (puerto 3001)
   - Opcional: Mobile App con `npx expo start`
   - Opcional: UI Kit para desarrollo de componentes

### Para Dueños de Escape Rooms

1. **Registrarse** en manager.escapemaster.es/register
2. **Completar KYB** (subir documentos legales)
3. **Configurar salas** en el dashboard
4. **Invitar staff** con roles específicos
5. **Comenzar a recibir reservas**

### Para Jugadores

1. **Explorar salas** en el marketplace
2. **Filtrar** por ubicación, temática, dificultad
3. **Reservar** con pago dividido para grupos
4. **Compartir** con squad (equipo de amigos)
5. **Ganar XP** y subir de nivel

## 📊 Tecnologías Utilizadas

| Componente | Tecnología | Versión | Propósito |
|------------|-------------|-----------|-----------|
| Backend API | FastAPI | 0.104.1 | API REST |
| Backend API | Python | 3.13 | Lenguaje |
| Backend API | SQLAlchemy | 2.0.35 | ORM |
| Web/Admin | Next.js | 16.0 | Framework |
| Web/Admin | React | 19.0 | UI Library |
| Web/Admin | TypeScript | 5.0 | Type system |
| Web/Admin | Tailwind CSS | 4.0 | Styling |
| Mobile | React Native | 0.76 | Framework |
| Mobile | Expo | SDK 54 | Build tool |
| Mobile | NativeWind | - | Styling |
| UI Kit | React | 18.2 | UI Library |
| UI Kit | Vite | 5.4 | Build tool |
| Database | PostgreSQL | 15+ | Database |
| Database | Supabase | - | Hosting DB |
| Auth | JWT | - | Authentication |
| Payments | Stripe | 7.7.0 | Payment gateway |
| Emails | Resend | 2.19.0 | Email service |

## 🔐 Seguridad y Multi-Tenancy

### Arquitectura Multi-Tenant

El sistema usa **multi-tenancy lógico**:
- Todos los datos se almacenan en las mismas tablas PostgreSQL
- Cada tabla principal tiene `organization_id`
- Los requests se filtran automáticamente por el `organization_id` del usuario autenticado
- Permisos RBAC controlan el acceso a recursos dentro de la organización

### Roles y Permisos

- **33 permisos** distribuidos en 8 categorías
- **Categorías:** bookings, users, rooms, coupons, payments, stats, settings, roles
- **Roles personalizados:** Las organizaciones pueden crear roles específicos

---

## 📚 Próximos Pasos

Para más información detallada, navega a la sección relevante:

### Para Desarrolladores

- **[Documentación Centralizada](./README.md)** - Índice completo de toda la documentación
- **[Contexto para IA](./03-contexto-ia/)** - Guía específica para desarrollar con asistencia de IA

### Para Usuarios Finales

- **[Guías de Usuario](./02-guias-usuario/)** - Guías B2B y B2C
- **[Onboarding Empresas](./02-guias-usuario/onboarding-empresas.md)** - Cómo unirse como dueño de Escape Room
- **[Guía Usuarios Finales](./02-guias-usuario/guia-usuarios-finales.md)** - Manual para jugadores

### Para Información Técnica

- **[Backend API](../manager/api/docs/)** - Documentación completa de la API
- **[Escapemaster Web](../manager/gestor/docs/)** - Documentación de Web App
- **[Escapemaster Admin](../manager/panel-admin/docs/)** - Documentación de Admin App
- **[Escapemaster Mobile](../manager/staff-mobile/docs/)** - Documentación de Mobile App
- **[Escapemaster UI Kit](../manager/ui-kit/docs/)** - Documentación de componentes

---

**Última actualización:** 4 de febrero de 2026
