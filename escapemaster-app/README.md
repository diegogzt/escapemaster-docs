# EscapeMaster App

Mobile application for iOS and Android built with React Native and Expo.

## Technology Stack

- **Framework**: [Expo](https://expo.dev/) (SDK 54)
- **Routing**: [Expo Router](https://docs.expo.dev/router/introduction/)
- **Styling**: [NativeWind](https://www.nativewind.dev/) (Tailwind CSS for React Native)
- **Icons**: [Lucide React Native](https://lucide.dev/guide/packages/lucide-react-native)
- **State Management**: [Zustand](https://github.com/pmndrs/zustand)
- **API Client**: [Axios](https://axios-http.com/)
- **Storage**: [Expo Secure Store](https://docs.expo.dev/versions/latest/sdk/secure-store/)

## Features Cloned from Web

- [x] Authentication (Login, Register, Forgot Password)
- [x] Dashboard Summary
- [x] Bookings Management (Placeholder)
- [x] Calendar View (Placeholder)
- [x] Rooms Management (Placeholder)
- [x] Settings & Profile

## Getting Started

1. Install dependencies:

   ```bash
   cd escapemaster-app
   npm install
   ```

2. Start the development server:

   ```bash
   npx expo start
   ```

3. Run on iOS/Android:
   - Press `i` for iOS simulator.
   - Press `a` for Android emulator.
   - Scan the QR code with the Expo Go app on your physical device.

## Project Structure

- `app/`: File-based routing.
  - `(auth)/`: Authentication screens.
  - `(dashboard)/`: Main app screens with tab navigation.
- `components/`: Reusable UI components.
- `services/`: API and other services.
- `constants/`: App constants and theme.
- `assets/`: Images, fonts, and other assets.

## API Connection

The app connects to the EscapeMaster API at `https://api.escapemaster.es`.
Authentication is handled via JWT tokens stored securely in `Expo Secure Store`.
