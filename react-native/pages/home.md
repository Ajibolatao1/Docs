# Expo & React Native — Personal Documentation

A curated collection of notes, cheatsheets, and setup guides for building production-grade React Native apps with Expo.

---

## 📋 Pages Overview

| Page                                                                                         | Description                                                                                                                                                               |
| -------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**Expo & RN Development Notes**](/react-native/index.html#pages/expo-react-native-notes.md) | Project setup, NativeWind, prebuild, Google OAuth, custom fonts, markdown display, file-based routing, navigation, image picker, push notifications, utilities, dev tools |
| [**EAS CLI Commands**](/react-native/index.html#pages/eas-commands.md)                       | Complete cheatsheet for EAS Build, Submit, OTA updates, secrets, device management, credentials, and account commands                                                     |
| [**Environment Variables Guide**](/react-native/index.html#pages/expo-env-variables.md)      | Three-tier strategy for managing env vars: `.env` for local dev, `eas.json` for build profiles, EAS Secrets for sensitive values                                          |
| [**Auth Setup**](/react-native/index.html#pages/auth-setup.md)                               | Full auth architecture: token management, SecureStore, auto-refresh, `fetchWithAuth`, app startup initialization, logout flow                                             |
| [**Essential UI Libraries**](/react-native/index.html#pages/rn-essential-libraries.md)       | Curated list of RN libraries for inputs, navigation, lists, media, maps, animations, modals, storage, networking, auth, charts, and utilities                             |

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
adb devices
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

| Topic          | Link                                                                                                                |
| -------------- | ------------------------------------------------------------------------------------------------------------------- |
| Expo Docs      | <a href="https://docs.expo.dev/" target="_blank" rel="noopener noreferrer">docs.expo.dev</a>                        |
| Expo Router    | <a href="https://expo.github.io/router/" target="_blank" rel="noopener noreferrer">expo.github.io/router</a>        |
| NativeWind     | <a href="https://www.nativewind.dev/" target="_blank" rel="noopener noreferrer">nativewind.dev</a>                  |
| EAS CLI        | <a href="https://docs.expo.dev/eas/" target="_blank" rel="noopener noreferrer">docs.expo.dev/eas</a>                |
| React Native   | <a href="https://reactnative.dev/" target="_blank" rel="noopener noreferrer">reactnative.dev</a>                    |
| TanStack Query | <a href="https://tanstack.com/query/latest" target="_blank" rel="noopener noreferrer">tanstack.com/query</a>        |
| Zustand        | <a href="https://github.com/pmndrs/zustand" target="_blank" rel="noopener noreferrer">github.com/pmndrs/zustand</a> |
