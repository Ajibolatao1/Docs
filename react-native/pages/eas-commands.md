# EAS CLI — Common Commands Cheatsheet

> **Tip:** Keep your CLI updated to avoid warnings.
>
> ```bash
> npm install -g eas-cli
> ```

---

## 🔧 Setup & Configuration

```bash
# Login to your Expo account
eas login

# Logout
eas logout

# Check who is logged in
eas whoami

# Initialize EAS in your project (creates eas.json)
eas build:configure
```

---

## 🏗️ Build

```bash
# Build for Android (uses "production" profile by default)
eas build --platform android

# Build for iOS
eas build --platform ios

# Build for both platforms
eas build --platform all

# Build using a specific profile
eas build --platform android --profile development
eas build --platform android --profile preview
eas build --platform android --profile production

# Build locally (on your machine, not EAS servers)
eas build --platform android --local

# List all previous builds
eas build:list

# Check build status/details
eas build:view
```

> **Note:** To output an **APK** build when using the `preview` profile, add the following configuration inside the `preview` object in your `eas.json`:
>
> ```json
> "android": {
>   "buildType": "apk"
> }
> ```

---

## 🚀 Submit (Upload to Store)

```bash
# Submit Android build to Google Play Store
eas submit --platform android

# Submit iOS build to Apple App Store
eas submit --platform ios

# Submit a specific build by ID
eas submit --platform android --id <build-id>
```

---

## 🔄 Update (OTA Updates)

```bash
# Publish an OTA update to a channel
eas update --channel production --message "Fix login bug"

# Publish to a specific branch
eas update --branch main --message "UI improvements"

# List all updates
eas update:list

# View details of a specific update
eas update:view <update-id>

# Roll back to a previous update
eas update:roll-back-to-embedded
```

---

## 🔐 Secrets & Environment Variables

```bash
# List all secrets for the project
eas secret:list

# Create a new secret
eas secret:create --scope project --name MY_SECRET --value "your-value"

# Delete a secret
eas secret:delete --name MY_SECRET

# List environment variables
eas env:list
```

---

## 📱 Device Management

```bash
# Register a device for development builds (iOS)
eas device:create

# List registered devices
eas device:list

# Delete a registered device
eas device:delete
```

---

## 🔑 Credentials

```bash
# Manage credentials interactively
eas credentials

# View Android credentials
eas credentials --platform android

# View iOS credentials
eas credentials --platform ios
```

---

## 👤 Account & Project

```bash
# Link project to your EAS account
eas init

# View project info
eas project:info

# Open project on expo.dev in browser
eas open
```

---

## Quick Reference

| Task                 | Command                                                    |
| -------------------- | ---------------------------------------------------------- |
| Login                | `eas login`                                                |
| Android dev build    | `eas build --platform android --profile development`       |
| Android prod build   | `eas build --platform android --profile production`        |
| OTA update           | `eas update --channel production --message "..."`          |
| Submit to Play Store | `eas submit --platform android`                            |
| Add a secret         | `eas secret:create --scope project --name KEY --value val` |
| List secrets         | `eas secret:list`                                          |
| List env vars        | `eas env:list`                                             |
| View builds          | `eas build:list`                                           |
