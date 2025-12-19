# Escapemaster Web - Hospitality Management Frontend

## 1. Project Philosophy & UX Goals
Escapemaster Web is the operational heart of the hotel. It is designed for **high-velocity usage** by receptionists and managers who need to perform complex tasks quickly while attending to guests.
- **Zero-Latency Interactions**: Optimistic UI updates ensure the interface feels instant.
- **Visual Clarity**: Information density is carefully balanced to show critical data without overwhelming.
- **Touch-Ready**: Fully optimized for tablet usage (iPad/Android tablets) for mobile staff.

---

## 2. Technical Architecture

### 2.1 Framework & Core Libraries
- **Next.js 15 (App Router)**: We utilize the latest React Server Components (RSC) architecture for optimal performance and SEO.
- **TypeScript**: Strict type safety across the entire codebase to prevent runtime errors.
- **Tailwind CSS**: Utility-first styling for rapid, consistent UI development.
- **Lucide React**: A consistent, lightweight icon set.
- **Axios**: Configured HTTP client with interceptors for auth token injection and global error handling.

### 2.2 Directory Structure
```
/src
├── app/                # Next.js App Router pages and layouts
├── components/         # Reusable UI components
│   ├── ui/             # Atomic components (Button, Input, Card)
│   └── features/       # Complex business components (Calendar, BookingForm)
├── context/            # React Context providers (Theme, Auth)
├── hooks/              # Custom React hooks (useBooking, usePermissions)
├── lib/                # Utility functions and helpers
└── services/           # API integration layer
```

---

## 3. Key Features & Modules

### 3.1 The Dynamic Calendar (The Heart of Escapemaster)
The calendar view is a custom-built engineering marvel, not an off-the-shelf library.
- **Virtualization**: Handles hundreds of rooms and thousands of bookings without lag.
- **Drag-and-Drop**: Intuitive rescheduling mechanism.
- **Smart Filtering**: Filter by room type, floor, or cleaning status.
- **Status Color Coding**:
  - 🟢 **Confirmed**: Booking guaranteed.
  - 🔵 **Checked-in**: Guest is in-house.
  - 🔴 **Checked-out**: Room needs cleaning.
  - ⚪ **Blocked**: Maintenance or out of order.

### 3.2 Theming & Personalization Engine
We understand that software should fit the brand. Escapemaster Web includes a sophisticated theming engine.
- **CSS Variables**: All colors are defined as CSS variables, allowing runtime switching without page reloads.
- **Context API**: `ThemeContext` manages the active theme state and persists user preference.
- **Current Palettes**:
  - **Twilight**: A deep, high-contrast dark theme optimized for night shifts and reducing eye strain. Uses deep purples and indigos.
  - **Tropical**: A bright, welcoming theme inspired by nature. Uses teals, emeralds, and warm sand tones.

### 3.3 Booking Management
- **Multi-Step Wizard**: A guided flow for creating new reservations to ensure no data is missed.
- **Guest Profiles**: CRM-lite functionality to store guest preferences and history.
- **Invoicing**: Generation of PDF invoices directly from the browser.

---

## 4. State Management Strategy
We avoid heavy global state libraries like Redux in favor of a more modern approach:
- **Server State**: Managed via **React Query** (TanStack Query) for caching, re-fetching, and optimistic updates.
- **UI State**: Managed via **React Context** for global UI preferences (Sidebar open/close, Theme).
- **Form State**: Managed via **React Hook Form** for performant, uncontrolled form inputs with Zod validation.

---

## 5. Deployment & CI/CD
- **Vercel/Netlify**: Optimized for edge deployment.
- **Environment Variables**:
  - `NEXT_PUBLIC_API_URL`: Pointing to the Escapemaster API.
  - `NEXT_PUBLIC_SUPABASE_URL`: For client-side auth interactions.

---

## 6. Future Roadmap
- **Offline Mode**: PWA capabilities to allow basic operations during internet outages.
- **Channel Manager Sync**: Real-time updates from Booking.com, Expedia, etc.
- **Housekeeping App**: A simplified mobile view specifically for cleaning staff.
