# 📚 Escapemaster Documentation

Welcome to the central documentation hub for the Escapemaster ecosystem.

## 🏗️ System Architecture

The Escapemaster platform is composed of four main repositories:

| Component                                         | Description                                                   | Links                                    |
| ------------------------------------------------- | ------------------------------------------------------------- | ---------------------------------------- |
| **[Escapemaster API](../escapemaster-api)**       | The backend core handling logic, data, and external services. | [Architecture](./api/ARCHITECTURE.md)    |
| **[Escapemaster Web](../escapemaster-web)**       | The client-facing dashboard for hotel staff and owners.       | [Architecture](./web/ARCHITECTURE.md)    |
| **[Escapemaster Admin](../escapemaster-admin)**   | The super-admin control plane for platform management.        | [Architecture](./admin/ARCHITECTURE.md)  |
| **[Escapemaster UI Kit](../escapemaster-ui-kit)** | Shared component library and design system.                   | [Architecture](./ui-kit/ARCHITECTURE.md) |

## 🌟 Recent Updates (December 2025)

### Frontend (Web & Admin)

- **Full Rebranding**: All modules migrated to the **Escapemaster** brand.
- **HR Management**: New sections for staff management, vacations, and time tracking.
- **Mobile Optimization**: Overhauled UI for mobile devices and touch interactions.
- **Dynamic Theming System**: Implementation of a global theme context with 8+ predefined themes (Twilight, Tropical, Vista, etc.).
- **Modular Dashboard**: New widget-based architecture using DnD Kit for layout customization.

### Backend (API)

- **HR Backend**: Implemented modules for Vacations and Timeclock.
- **Enhanced Room Data**: Room responses now include pending bookings and next sessions.
- **SMTP Integration**: Switched to Hostinger SMTP for reliable email delivery.
- **RBAC Enhancements**: Refinement of roles and permissions system across all modules.

## 🚀 Getting Started

To set up the entire ecosystem locally, please refer to the `README.md` in each respective repository.

## 🔄 Development Workflow

1.  **API:** Start the backend server first (`uvicorn app.main:app`).
2.  **Web/Admin:** Start the frontend applications (`npm run dev`).
3.  **UI Kit:** Develop components in isolation (`npm run dev`) or link locally.

## 📝 Project Status

For a detailed breakdown of completed and pending features, please check the **Roadmap** section in each repository's README.
