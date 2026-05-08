# Expo Android Build — Environment Variables Guide

## Overview

There are three ways to manage environment variables in an Expo project, depending on the stage:

| Stage | Method |
|---|---|
| Local development | `.env` file with `EXPO_PUBLIC_` prefix |
| EAS Build (dev/prod) | `eas.json` under the `env` block |
| Sensitive secrets | EAS Secrets via CLI |

---

## 1. Local Development — `.env` File

For local development (`expo start` or `npx expo run:android`), create a `.env.local` file in your project root. All variables **must be prefixed with `EXPO_PUBLIC_`** to be accessible in your app's JavaScript code.

```env
# .env.local
EXPO_PUBLIC_API_URL=https://dev.api.example.com
EXPO_PUBLIC_STRIPE_KEY=pk_test_xxx
```

Access them in your code like this:

```js
const apiUrl = process.env.EXPO_PUBLIC_API_URL;
console.log(apiUrl); // https://dev.api.example.com
```

> ⚠️ **Important:** Variables with the `EXPO_PUBLIC_` prefix are **bundled into the app** and visible to end users. Never store private keys or secrets here.

---

## 2. EAS Build — `eas.json`

For cloud builds via EAS, define your environment variables inside `eas.json` under each build profile's `env` block. These are injected at build time.

```json
{
  "build": {
    "development": {
      "android": {
        "buildType": "apk"
      },
      "env": {
        "EXPO_PUBLIC_API_URL": "https://dev.api.example.com",
        "EXPO_PUBLIC_APP_ENV": "development"
      }
    },
    "production": {
      "env": {
        "EXPO_PUBLIC_API_URL": "https://api.example.com",
        "EXPO_PUBLIC_APP_ENV": "production"
      }
    }
  }
}
```

Run the build for a specific profile:

```bash
# Development build
eas build --platform android --profile development

# Production build
eas build --platform android --profile production
```

---

## 3. Sensitive Secrets — EAS Secrets

For sensitive values (API keys, tokens, passwords) that you **don't want committed to your repository**, use EAS Secrets. These are stored encrypted on Expo's servers and injected at build time — same as `eas.json` env vars, but secure.

**Create a secret via the CLI:**

```bash
eas secret:create --scope project --name MY_SECRET_KEY --value "your-secret-value"
```

**List existing secrets:**

```bash
eas secret:list
```

**Delete a secret:**

```bash
eas secret:delete --name MY_SECRET_KEY
```

Once created, the secret is automatically available during EAS builds as an environment variable — no additional configuration needed.

---

## Putting It All Together

A typical project structure looks like this:

```
my-expo-app/
├── .env.local          # Local dev variables (gitignored)
├── .gitignore
├── eas.json            # Build profiles + non-secret env vars
├── app.json
└── app/
    └── index.tsx
```

**.gitignore** — make sure your `.env` files are excluded:

```
.env
.env.local
.env*.local
```

**Example usage in code** that works across all environments:

```js
// works locally, in EAS dev build, and in production
const API_URL = process.env.EXPO_PUBLIC_API_URL;
const APP_ENV = process.env.EXPO_PUBLIC_APP_ENV;

console.log(`Running in ${APP_ENV} — connecting to ${API_URL}`);
```

---

## Quick Reference

| Variable type | Visible to users? | Committed to repo? | Where to define |
|---|---|---|---|
| `EXPO_PUBLIC_*` (local) | ✅ Yes | ❌ No (gitignored) | `.env.local` |
| `EXPO_PUBLIC_*` (build) | ✅ Yes | ✅ Yes | `eas.json` |
| Secrets | ❌ No | ❌ No | EAS Secrets (CLI) |
