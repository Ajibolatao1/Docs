# Expo & React Native — Personal Documentation

A curated collection of notes, cheatsheets, and setup guides for building production-grade React Native apps with Expo.

---

## 📋 Pages Overview

| Page | Description |
|------|-------------|
| [**Expo & RN Development Notes**](expo-react-native-notes.md) | Project setup, NativeWind, prebuild, Google OAuth, custom fonts, markdown display, file-based routing, navigation, image picker, push notifications, utilities, dev tools |
| [**EAS CLI Commands**](eas-commands.md) | Complete cheatsheet for EAS Build, Submit, OTA updates, secrets, device management, credentials, and account commands |
| [**Environment Variables Guide**](expo-env-variables.md) | Three-tier strategy for managing env vars: `.env` for local dev, `eas.json` for build profiles, EAS Secrets for sensitive values |
| [**Auth Setup**](auth-setup.md) | Full auth architecture: token management, SecureStore, auto-refresh, `fetchWithAuth`, app startup initialization, logout flow |
| [**Essential UI Libraries**](rn-essential-libraries.md) | Curated list of RN libraries for inputs, navigation, lists, media, maps, animations, modals, storage, networking, auth, charts, and utilities |

---

## 🚀 Quick Start — New Project

```bash
npx create-expo-app@latest my-app
cd my-app
npm run reset-project
```

Then set up the folder structure:

```bash
mkdir assets/audio assets/fonts assets/icons
mkdir auth components constants hooks lib store dummy-data types utils
touch .env
```

---

## 🔧 Common Workflows

### Development Build

```bash
npx expo prebuild --clean
npx expo run:android
npx expo start --dev-client
```

### Production Build & Submit

```bash
eas build --platform android --profile production
eas submit --platform android
```

### OTA Update

```bash
eas update --channel production --message "Release notes here"
```

---

## 🧠 Architecture Patterns

This documentation covers several key architectural decisions:

- **Auth**: Session token in SecureStore, access token in Zustand (memory), auto-refresh with subscriber queue
- **State**: Zustand for global state, TanStack Query for server state
- **Routing**: Expo Router with file-based routing, typed routes, route groups
- **Styling**: NativeWind (TailwindCSS) utility classes
- **API**: `fetchWithAuth` wrapper with automatic 401 handling and token refresh

---

## 📚 Reference

| Topic | Link |
|-------|------|
| Expo Docs | [docs.expo.dev](https://docs.expo.dev/) |
| Expo Router | [expo.github.io/router](https://expo.github.io/router/) |
| NativeWind | [nativewind.dev](https://www.nativewind.dev/) |
| EAS CLI | [docs.expo.dev/eas](https://docs.expo.dev/eas/) |
| React Native | [reactnative.dev](https://reactnative.dev/) |
| TanStack Query | [tanstack.com/query](https://tanstack.com/query/latest) |
| Zustand | [github.com/pmndrs/zustand](https://github.com/pmndrs/zustand) |
