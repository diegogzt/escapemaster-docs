# 📚 Documentación Escapemaster

Bienvenido al hub central de documentación del ecosistema **Escapemaster**.

## 🏗️ Arquitectura del Sistema

El ecosistema Escapemaster está compuesto por cinco módulos principales:

| Componente | Descripción | Enlace |
|------------|-------------|---------|
| **[Backend API](../manager/api/docs/)** | El backend core que maneja lógica, datos y servicios externos | [Docs](../manager/api/docs/) |
| **[Escapemaster Web](../manager/gestor/docs/)** | El dashboard de gestión para dueños de Escape Rooms | [Docs](../manager/gestor/docs/) |
| **[Escapemaster Admin](../manager/panel-admin/docs/)** | El super-admin para gestión de plataforma | [Docs](../manager/panel-admin/docs/) |
| **[Escapemaster Mobile](../manager/staff-mobile/docs/)** | La app móvil para Game Masters y staff | [Docs](../manager/staff-mobile/docs/) |
| **[Escapemaster UI Kit](../manager/ui-kit/docs/)** | La biblioteca de componentes compartidos y sistema de diseño | [Docs](../manager/ui-kit/docs/) |

## 📚 Secciones de Documentación

### Contexto para Agentes IA

Documentación específica diseñada para proporcionar contexto completo a agentes de IA que trabajan en el proyecto Escapemaster.

- **[📋 Índice Contexto IA](./03-contexto-ia/README.md)** - Introducción al contexto para IA
- **[🏗️ Sistema Overview](./03-contexto-ia/sistema-overview.md)** - Visión general de la arquitectura del ecosistema
- **[🔗 Dependencias Entre Proyectos](./03-contexto-ia/dependencias-entre-proyectos.md)** - Cómo se conectan los módulos
- **[📝 Estándares de Código](./03-contexto-ia/estandares-codigo.md)** - Convenciones y patrones de código
- **[🧩 Patrones Comunes](./03-contexto-ia/patrones-comunes.md)** - Componentes y funciones reutilizables

### Guías de Usuario

Documentación orientada a usuarios finales del ecosistema Escapemaster.

- **[📋 Índice Guías de Usuario](./02-guias-usuario/README.md)** - Introducción a guías de usuario
- **[🏢 Onboarding de Empresas (B2B)](./02-guias-usuario/onboarding-empresas.md)** - Guía completa para dueños de Escape Rooms
- **[👥 Guía de Usuarios Finales](./02-guias-usuario/guia-usuarios-finales.md)** - Manual para jugadores que reservan salas

## 🌟 Actualizaciones Recientes (Febrero 2026)

### Reorganización de Documentación

- ✅ Documentación centralizada en `/docs/`
- ✅ Contexto específico para agentes IA
- ✅ Guías de usuario separadas por público objetivo
- ✅ Documentación técnica reorganizada en cada proyecto
- ✅ Eliminación de duplicación de UI Kits (mantenido solo manager/ui-kit)
- ✅ Todo en español

### Frontend (Web & Admin)

- **Rebranding Completo:** Todos los módulos migrados a **Escapemaster**
- **Gestión RRHH:** Secciones nuevas para gestión de staff, vacaciones y control horario
- **Optimización Móvil:** UI overhauled para dispositivos móviles y táctiles
- **Sistema de Temas Dinámico:** Implementación de contexto global con 8+ temas predefinidos
- **Dashboard Modular:** Nuevo sistema basado en widgets con capacidades de drag-and-drop

### Backend (API)

- **HR Backend:** Módulos implementados para Vacaciones y Timeclock
- **Datos de Salas Mejorados:** Las respuestas de salas ahora incluyen reservas pendientes y próximas sesiones
- **Integración SMTP:** Cambiado a Hostinger SMTP para entrega de emails confiable
- **Mejoras RBAC:** Refinamiento del sistema de roles y permisos en todos los módulos

## 🚀 Inicio Rápido

Para configurar todo el ecosistema localmente, refierete al `README.md` en cada repositorio:

1. **API:** Iniciar servidor backend primero (`uvicorn app.main:app`)
2. **Web/Admin:** Iniciar aplicaciones frontend (`npm run dev`)
3. **UI Kit:** Desarrollar componentes en aislamiento (`npm run dev`) o link localmente

## 🔄 Flujo de Trabajo de Desarrollo

### Flujo Típico de Desarrollo

```bash
# 1. Iniciar Backend (puerto 8000)
cd manager/api
source venv/bin/activate
uvicorn app.main:app --reload

# 2. Iniciar Web App (puerto 3000)
cd manager/gestor
npm run dev

# 3. Iniciar Admin App (puerto 3001)
cd manager/panel-admin
npm run dev

# 4. (Opcional) Iniciar Mobile App
cd manager/staff-mobile
npx expo start

# 5. (Opcional) Iniciar UI Kit para desarrollo de componentes
cd manager/ui-kit
npm run dev
```

### Arquitectura de Comunicación

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
│ Users/Staff │ Platform Owners
│ Dashboard  │ Super-admin
│ Bookings   │ Orgs Mgmt
└───────┼─────┘
        │
   UI Components (imported)
        │
└───────────────────┘
```

## 🔐 Autenticación

Sistema JWT con tokens de acceso y refresh:

1. **Registrar** un nuevo usuario vía `/auth/register`
2. **Login** vía `/auth/login` para obtener un `access_token`
3. Incluir el token en el header `Authorization` de tus peticiones:
   ```
   Authorization: Bearer <tu_access_token>
   ```

## 🌐 URLs del Sistema

| Servidor | URL |
|-----------|-----|
| API (Desarrollo) | http://localhost:8000 |
| API (Producción) | https://api.escapemaster.es |
| Web App (Dev) | http://localhost:3000 |
| Admin App (Dev) | http://localhost:3001 |
| UI Kit (Demo) | https://diegogzt.github.io/manager/ui-kit/ |
| API Docs | http://localhost:8000/docs |

## 📊 Estado del Proyecto

Para un desglose detallado de funcionalidades completadas y pendientes, consulta las secciones de roadmap en cada módulo:

- **Backend API:** [Estado del Proyecto](../manager/api/docs/estado-proyecto.md)
- **Web App:** [README.md](../manager/gestor/docs/README.md) - Sección "Fases de Desarrollo"
- **Admin App:** [README.md](../manager/panel-admin/docs/README.md) - Sección "Fases de Desarrollo"

## 🤝 Contribución

Para contribuir al proyecto Escapemaster:

1. Fork el repositorio correspondiente
2. Crea una rama (`git checkout -b feature/amazing`)
3. Commit cambios (`git commit -m 'Add: amazing feature'`)
4. Push (`git push origin feature/amazing`)
5. Abre un Pull Request

Para más información sobre procesos de contribución, refierete a:
- [Escapemaster Web - Contribución](../manager/gestor/docs/README.md#-contribución)
- [Escapemaster UI Kit - Contribución](../manager/ui-kit/docs/README.md#-contribución)

## 📞 Soporte y Contacto

### Documentación Técnica

- **Backend API:** [Backend Docs](../manager/api/docs/)
- **Escapemaster Web:** [Web Docs](../manager/gestor/docs/)
- **Escapemaster Admin:** [Admin Docs](../manager/panel-admin/docs/)
- **Escapemaster Mobile:** [Mobile Docs](../manager/staff-mobile/docs/)
- **Escapemaster UI Kit:** [UI Kit Docs](../manager/ui-kit/docs/)

### Soporte al Usuario

Para preguntas de usuarios finales (B2B y jugadores):

- 📧 **Email:** soporte@escapemaster.es
- 🌐 **Web:** https://escapemaster.es
- 🐛 **Reportar Issues:** Issues en GitHub correspondientes

### Desarrollo

- 🐛 **Reportar Bugs:** GitHub Issues por proyecto
- 💬 **Discusiones:** GitHub Discussions por proyecto
- 📧 **Email Dev:** dev@escapemaster.es

## 📖 Recursos Externos

- **FastAPI Docs:** https://fastapi.tiangolo.com/
- **Next.js Docs:** https://nextjs.org/docs
- **React Native Docs:** https://reactnative.dev/
- **Expo Docs:** https://docs.expo.dev/
- **Tailwind CSS:** https://tailwindcss.com/docs
- **TypeScript:** https://www.typescriptlang.org/docs/
- **Supabase:** https://supabase.com/docs
- **Stripe:** https://stripe.com/docs

---

**Última actualización:** 4 de febrero de 2026
