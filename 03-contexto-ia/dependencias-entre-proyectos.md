# Dependencias Entre Proyectos - Escapemaster

## Visión General

Los proyectos de Escapemaster están diseñados con una arquitectura de **microservicios colaborativos** donde cada módulo tiene una responsabilidad clara pero se comunica con otros a través de la API central.

---

## Dependencias de API (Backend)

```
manager/api/
    ├─ Database: Supabase PostgreSQL
    ├─ Payments: Stripe
    ├─ Emails: Resend
    └─ Calendar: Google Calendar (integración futura)
```

**Dependencias de desarrollo**:
- `uvicorn` - Servidor ASGI
- `sqlalchemy` - ORM async
- `alembic` - Migraciones
- `pydantic` - Validación de datos
- `python-jose` - JWT tokens
- `passlib` - Hashing de contraseñas
- `psycopg2` - Driver PostgreSQL
- `supabase` - Cliente Supabase

**Dependencias de testing**:
- `pytest` - Framework de tests
- `pytest-asyncio` - Tests async
- `httpx` - Client HTTP para tests

---

## Dependencias de Web App (`/manager/gestor/`)

### Dependencias del Proyecto

```
manager/gestor/
    ├─ API Calls: manager/api
    ├─ State: Zustand
    ├─ Auth: JWT (tokens de backend)
    ├─ UI Components: manager/ui-kit
    └─ Forms: React Hook Form + Zod
```

**Dependencias de producción**:
- `next` - Framework (16.0+)
- `react` - UI Library (19.0+)
- `typescript` - Type system (5.0+)
- `tailwindcss` - Styling (4.0)
- `zustand` - State management
- `axios` - HTTP client
- `@dnd-kit/core` - Drag and drop
- `@dnd-kit/sortable` - DnD para listas
- `recharts` - Gráficos
- `lucide-react` - Iconos
- `react-hook-form` - Formularios
- `zod` - Validación de esquemas

**Dependencias de desarrollo**:
- `@vitejs/plugin-react` - Vite plugin para React
- `typescript` - Type checking
- `vitest` - Unit tests
- `@playwright/test` - E2E tests
- `@testing-library/react` - Testing library
- `eslint` - Linting
- `prettier` - Formating

---

## Dependencias de Admin App (`/manager/panel-admin/`)

```
manager/panel-admin/
    ├─ API Calls: manager/api (endpoints /admin/*)
    ├─ State: Zustand
    ├─ Auth: Admin JWT (diferente del user JWT)
    └─ UI Components: manager/ui-kit
```

**Dependencias de producción** (similar a Web):
- `next` - Framework
- `react` - UI Library
- `typescript` - Type system
- `tailwindcss` - Styling
- `zustand` - State management
- `axios` - HTTP client
- `lucide-react` - Iconos
- `swr` - Data fetching (Stale-While-Revalidate)

**Dependencias de desarrollo**:
- Vitest para unit tests
- ESLint para linting

---

## Dependencias de Mobile App (`/manager/staff-mobile/`)

```
manager/staff-mobile/
    ├─ API Calls: manager/api
    ├─ Auth: JWT tokens
    ├─ Storage: Expo SecureStore
    └─ UI: NativeWind + Lucide React Native
```

**Dependencias de producción**:
- `expo` - Framework (SDK 54)
- `expo-router` - File-based routing
- `nativewind` - Tailwind para React Native
- `lucide-react-native` - Iconos
- `zustand` - State management
- `expo-secure-store` - Almacenamiento seguro
- `axios` - HTTP client

**Dependencias de desarrollo**:
- `@types/react` - Type definitions
- `typescript` - Type checking

---

## Dependencias de UI Kit (`/manager/ui-kit/`)

```
manager/ui-kit/
    ├─ Framework: React 18.2
    ├─ Build: Vite 5.4
    ├─ Styling: Tailwind CSS 3.3
    └─ Deployment: GitHub Pages
```

**Dependencias de producción**:
- `react` - UI Library (18.2+)
- `typescript` - Type system (5.3+)
- `tailwindcss` - Styling
- `vite` - Build tool

**Dependencias de desarrollo**:
- `@vitejs/plugin-react` - Vite plugin
- `typescript` - Type checking
- `eslint` - Linting

---

## Flujo de Comunicación Entre Proyectos

### 1. Autenticación y Seguridad

```
User (Web/Mobile)
    │
    ├─ POST /auth/login (manager/api)
    │      ├─ Validate credentials
    │      ├─ Generate JWT tokens
    │      └─ Return { access_token, refresh_token }
    │
    ├─ Store tokens securely
    │      ├─ Web: localStorage/sessionStorage
    │      └─ Mobile: Expo SecureStore
    │
    └─ Include in requests
           ├─ Header: Authorization: Bearer {access_token}
           └─ Validate on each request (manager/api)
```

### 2. Gestión de Reservas

```
Web App (User)
    │
    ├─ GET /rooms (List available rooms)
    │      └─ manager/api
    │
    ├─ GET /rooms/{id}/availability (Check availability)
    │      └─ manager/api
    │
    ├─ POST /bookings (Create booking)
    │      ├─ Validate: room available
    │      ├─ Validate: user permissions
    │      ├─ Calculate price
    │      └─ manager/api
    │
    ├─ POST /bookings/{id}/payment (Process payment)
    │      ├─ Stripe integration
    │      └─ manager/api
    │
    └─ GET /bookings/{id} (View booking)
           ├─ Include room details
           ├─ Include payment status
           └─ manager/api
```

### 3. Gestión de Usuarios y Roles

```
Admin App (Super-admin)
    │
    ├─ GET /admin/organizations (List all orgs)
    │      └─ manager/api /admin/*
    │
    ├─ POST /admin/organizations (Create org)
    │      ├─ Create tenant entity
    │      ├─ Generate invitation code
    │      └─ manager/api /admin/*
    │
    └─ PATCH /admin/organizations/{id}/status (Suspend org)
           └─ manager/api /admin/*

Web App (Org Admin)
    │
    ├─ GET /users (List org users)
    │      ├─ Filtered by organization_id
    │      └─ manager/api /users
    │
    ├─ POST /users (Create user)
    │      ├─ Auto-assign to org
    │      ├─ Set initial role
    │      └─ manager/api /users
    │
    └─ POST /roles/assign (Assign role to user)
           └─ manager/api /roles
```

### 4. Mobile App - Operaciones en Sala

```
Mobile App (Game Master)
    │
    ├─ GET /bookings (Today's bookings)
    │      ├─ Filtered by date
    │      └─ manager/api /bookings
    │
    ├─ POST /bookings/{id}/claim (Assign to self)
    │      └─ manager/api /bookings
    │
    ├─ POST /timeclock/clock-in (Start work shift)
    │      └─ manager/api
    │
    └─ POST /timeclock/clock-out (End work shift)
           └─ manager/api
```

---

## Configuración de API en Frontend

### Web App (`manager/gestor/`)

**Variables de entorno** (`.env.local`):
```env
NEXT_PUBLIC_API_URL=http://localhost:8000
NEXT_PUBLIC_SUPABASE_URL=https://project.supabase.co
NEXT_PUBLIC_SUPABASE_KEY=anon_key
```

**Axios instance** (`src/lib/axios.ts`):
```typescript
import axios from 'axios';

const api = axios.create({
  baseURL: process.env.NEXT_PUBLIC_API_URL,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Add auth token to requests
api.interceptors.request.use((config) => {
  const token = localStorage.getItem('access_token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

// Handle 401 errors
api.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      // Redirect to login
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);

export default api;
```

### Mobile App (`manager/staff-mobile/`)

**Variables de entorno** (`.env`):
```env
EXPO_PUBLIC_API_URL=https://api.escapemaster.es
```

**Axios instance** (`src/services/api.ts`):
```typescript
import axios from 'axios';
import * as SecureStore from 'expo-secure-store';

const api = axios.create({
  baseURL: process.env.EXPO_PUBLIC_API_URL,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Add auth token from SecureStore
api.interceptors.request.use(async (config) => {
  const token = await SecureStore.getItemAsync('access_token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

export default api;
```

---

## Shared Code y Componentes

### UI Kit (`/manager/ui-kit/`)

El UI Kit es **consumido por**:
- Web App (`manager/gestor/`)
- Admin App (`manager/panel-admin/`)
- (Futuro) Mobile App (`manager/staff-mobile/`)

**Instalación**:
```bash
# Opción 1: Importar desde carpeta local
# (Actual: los proyectos importan desde manager/ui-kit/ local)

# Opción 2: NPM package (futuro)
npm install @escapemaster/ui
```

**Importación en proyectos**:
```tsx
// manager/gestor/src/app/dashboard/page.tsx
import { Button, Card, Calendar } from '@escapemaster/ui';

export default function Dashboard() {
  return (
    <Card>
      <Calendar size="md" variant="primary" />
      <Button onClick={handleAction}>Click me</Button>
    </Card>
  );
}
```

### Tipos Compartidos

Los tipos TypeScript que se comparten entre proyectos:

**Backend → Frontend**:
- Schemas Pydantic se traducen a interfaces TypeScript
- Ejemplo: `UserCreate` (Pydantic) → `UserCreate` (TypeScript)

**Ejemplo de traducción**:

**Backend** (`manager/api/schemas/user.py`):
```python
class UserCreate(BaseModel):
    email: EmailStr
    full_name: str
    role_id: UUID
```

**Frontend** (`manager/gestor/src/types/user.ts`):
```typescript
export interface UserCreate {
  email: string;
  full_name: string;
  role_id: string;
}
```

---

## Integraciones Externas

### 1. Stripe (Pagos)

**Proyecto**: Backend API

**Uso**:
- Procesar pagos de reservas
- Reembolsos automáticos
- Webhooks para notificaciones

**Endpoints**:
- `POST /payments/stripe/create-intent`
- `POST /payments/stripe/webhook`

### 2. Resend (Emails)

**Proyecto**: Backend API

**Uso**:
- Confirmación de reservas
- Recuperación de contraseñas
- Notificaciones de sistema

**Endpoints**:
- `POST /auth/forgot-password` (envía email con código)
- `POST /bookings/{id}/confirmation-email`

### 3. Supabase (Auth + Database)

**Proyecto**: Backend API + Frontend Apps

**Backend**:
- Cliente Python de Supabase para operaciones directas
- Connection pooler para alta concurrencia

**Frontend**:
- Auth de Supabase para login social (opcional)
- Realtime subscriptions para actualizaciones en vivo (futuro)

---

## Actualización de Dependencias

### Estrategia de Versionado

- **Backend**: Semver (v1.2.3) - Actualización en `manager/api/README.md`
- **Frontend**: Semver - Actualización en `apps/*/package.json`
- **UI Kit**: Semver - Actualización en `manager/ui-kit/package.json`

### Compatibilidad entre Versiones

| Backend | Web/Admin | Mobile | UI Kit |
|---------|------------|---------|----------|
| v1.2.0 | Compatible | Compatible | Compatible |
| v1.3.0 | Necesita actualización | Necesita actualización | Compatible |

### Flujo de Actualización

1. **Backend actualizado** → Avisar a equipos frontend
2. **Frontend actualizado** → Cambiar versiones en `package.json`
3. **UI Kit actualizado** → Publicar nueva versión, actualizar en proyectos

---

**Última actualización:** 4 de febrero de 2026
