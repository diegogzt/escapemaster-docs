# Referencia Rápida - Escapemaster

Índice rápido de toda la documentación del ecosistema Escapemaster.

## 📋 Índice por Tema

### Desarrollo y Código

| Tema | Ubicación |
|--------|-----------|
| Introducción al Sistema | [docs/00-introduccion.md](./00-introduccion.md) |
| Arquitectura del Sistema | [docs/01-arquitectura-sistema.md](./01-arquitectura-sistema.md) |
| Sistema Overview | [docs/03-contexto-ia/sistema-overview.md](./03-contexto-ia/sistema-overview.md) |
| Dependencias Entre Proyectos | [docs/03-contexto-ia/dependencias-entre-proyectos.md](./03-contexto-ia/dependencias-entre-proyectos.md) |
| Estándares de Código | [docs/03-contexto-ia/estandares-codigo.md](./03-contexto-ia/estandares-codigo.md) |
| Patrones Comunes | [docs/03-contexto-ia/patrones-comunes.md](./03-contexto-ia/patrones-comunes.md) |

### Backend API

| Tema | Ubicación |
|--------|-----------|
| Visión General del Backend | [manager/api/docs/README.md](../manager/api/docs/README.md) |
| Estado del Proyecto | [manager/api/docs/estado-proyecto.md](../manager/api/docs/estado-proyecto.md) |
| Instalación | [manager/api/docs/configuracion/instalacion.md](../manager/api/docs/configuracion/instalacion.md) |
| Variables de Entorno | [manager/api/docs/configuracion/variables-entorno.md](../manager/api/docs/configuracion/variables-entorno.md) |
| Guía Rápida | [manager/api/docs/configuracion/guia-rapida.md](../manager/api/docs/configuracion/guia-rapida.md) |
| Resumen de API | [manager/api/docs/api/resumen.md](../manager/api/docs/api/resumen.md) |
| Autenticación | [manager/api/docs/api/autenticacion.md](../manager/api/docs/api/autenticacion.md) |
| Organizaciones | [manager/api/docs/api/organizaciones.md](../manager/api/docs/api/organizaciones.md) |
| Usuarios y Roles | [manager/api/docs/api/usuarios-y-roles.md](../manager/api/docs/api/usuarios-y-roles.md) |
| Salas y Reservas | [manager/api/docs/api/salas-y-reservas.md](../manager/api/docs/api/salas-y-reservas.md) |
| Pagos y Cupones | [manager/api/docs/api/pagos-y-cupones.md](../manager/api/docs/api/pagos-y-cupones.md) |
| Dashboard | [manager/api/docs/api/dashboard.md](../manager/api/docs/api/dashboard.md) |
| Admin | [manager/api/docs/api/admin.md](../manager/api/docs/api/admin.md) |
| Deploy en Railway | [manager/api/docs/despliegue/railway.md](../manager/api/docs/despliegue/railway.md) |
| Deploy en Render | [manager/api/docs/despliegue/render.md](../manager/api/docs/despliegue/render.md) |

### Frontend (Web/Admin/Mobile)

| Tema | Ubicación |
|--------|-----------|
| Escapemaster Web | [manager/gestor/docs/README.md](../manager/gestor/docs/README.md) |
| Escapemaster Admin | [manager/panel-admin/docs/README.md](../manager/panel-admin/docs/README.md) |
| Escapemaster Mobile | [manager/staff-mobile/docs/README.md](../manager/staff-mobile/docs/README.md) |

### UI Kit

| Tema | Ubicación |
|--------|-----------|
| Visión General del UI Kit | [manager/ui-kit/docs/README.md](../manager/ui-kit/docs/README.md) |
| Guía Completa de Componentes | [manager/ui-kit/docs/guia-completa.md](../manager/ui-kit/docs/guia-completa.md) |
| Instalación | [manager/ui-kit/docs/configuracion/instalacion.md](../manager/ui-kit/docs/configuracion/instalacion.md) |
| Despliegue | [manager/ui-kit/docs/configuracion/despliegue.md](../manager/ui-kit/docs/configuracion/despliegue.md) |
| GitHub Pages | [manager/ui-kit/docs/configuracion/github-pages.md](../manager/ui-kit/docs/configuracion/github-pages.md) |
| Componente Calendar | [manager/ui-kit/docs/desarrollo/calendario-componente.md](../manager/ui-kit/docs/desarrollo/calendario-componente.md) |

### Usuarios Finales

| Tema | Ubicación |
|--------|-----------|
| Guía de Usuarios Finales | [docs/02-guias-usuario/guia-usuarios-finales.md](./02-guias-usuario/guia-usuarios-finales.md) |

### Empresas (B2B)

| Tema | Ubicación |
|--------|-----------|
| Onboarding de Empresas | [docs/02-guias-usuario/onboarding-empresas.md](./02-guias-usuario/onboarding-empresas.md) |

---

## 🚀 Comandos Rápidos

### Backend (Python)

```bash
# Iniciar servidor
cd manager/api && uvicorn app.main:app --reload

# Ejecutar tests
pytest

# Ejecutar tests con cobertura
pytest --cov=app --cov-report=html

# Crear migración
alembic revision --autogenerate -m "mensaje"

# Aplicar migraciones
alembic upgrade head
```

### Frontend (Next.js)

```bash
# Web App
cd manager/gestor && npm run dev

# Admin App
cd manager/panel-admin && npm run dev

# Build
npm run build

# Tests unitarios
npm run test

# Tests E2E
npm run test:e2e

# Linting
npm run lint
```

### Mobile (Expo)

```bash
# Iniciar desarrollo
cd manager/staff-mobile && npx expo start

# Build para producción
eas build --platform ios
eas build --platform android
```

### UI Kit (Vite)

```bash
# Iniciar desarrollo
cd manager/ui-kit && npm run dev

# Build
npm run build

# Preview build
npm run preview
```

---

## 🔐 Credenciales y URLs

### Desarrollo

| Servicio | URL |
|----------|-----|
| Backend API | http://localhost:8000 |
| API Docs | http://localhost:8000/docs |
| Web App | http://localhost:3000 |
| Admin App | http://localhost:3001 |
| UI Kit | http://localhost:5173 |

### Producción

| Servicio | URL |
|----------|-----|
| Backend API | https://api.escapemaster.es |
| Web App | TBD |
| Admin App | TBD |
| UI Kit Demo | https://diegogzt.github.io/manager/ui-kit/ |

### Supabase

| Recurso | Valor |
|----------|-------|
| Dashboard | https://supabase.com/dashboard |
| Project Ref | Consultar en variables de entorno |
| Connection String | Consultar en variables de entorno |

---

## 📞 Contacto y Soporte

| Tipo | Contacto |
|-------|-----------|
| Soporte Usuarios | soporte@escapemaster.es |
| Soporte Desarrolladores | dev@escapemaster.es |
| Reportar Bugs | Issues en GitHub por proyecto |
| Discusiones | GitHub Discussions por proyecto |

---

**Última actualización:** 4 de febrero de 2026
