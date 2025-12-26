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

### Frontend (Web)

- **Dynamic Theming System**: Implementation of a global theme context with 8+ predefined themes (Twilight, Tropical, Vista, etc.).
- **Modular Dashboard**: New widget-based architecture using DnD Kit for layout customization.
- **Advanced Reports**: Enhanced filtering and visualization for revenue and occupancy.
- **Auth Stability**: Critical fixes in authentication flow and session management.

### Backend (API)

- **Widget System**: New endpoints and models for managing dashboard widgets and user collections.
- **RBAC Enhancements**: Refinement of roles and permissions system.
- **Dashboard Mocks**: Base structure for statistics and revenue endpoints ready for frontend integration.

## 🚀 Getting Started

To set up the entire ecosystem locally, please refer to the `README.md` in each respective repository.

## 🔄 Development Workflow

1.  **API:** Start the backend server first (`uvicorn app.main:app`).
2.  **Web/Admin:** Start the frontend applications (`npm run dev`).
3.  **UI Kit:** Develop components in isolation (`npm run dev`) or link locally.

## 📝 Project Status

For a detailed breakdown of completed and pending features, please check the **Roadmap** section in each repository's README.
