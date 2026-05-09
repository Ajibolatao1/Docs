# Expo & React Native Development Notes

## Table of Contents

1. [Project Setup](#1-project-setup)
2. [NativeWind (TailwindCSS)](#2-nativewind-tailwindcss)
3. [Prebuild & Running the App](#3-prebuild-amp-running-the-app)
4. [Google OAuth](#4-google-oauth)
5. [Custom Fonts](#5-custom-fonts)
6. [Markdown Display](#6-markdown-display)
7. [File-Based Routing](#7-file-based-routing)
8. [Navigation & Params](#8-navigation--params)
9. [Layouts & Slots](#9-layouts--slots)
10. [Image Picker](#10-image-picker)
11. [Push Notifications](#11-push-notifications)
12. [Useful Utilities](#12-useful-utilities)
13. [Developer Tools](#13-developer-tools)

---

## 1. Project Setup

### Create an Expo Project

```bash
npx create-expo-app@latest my-app
cd my-app
npm run reset-project #To reeset the project and remove the boilerplate
```

> Full docs: <a href="https://docs.expo.dev/" target="_blank" rel="noopener noreferrer">https://docs.expo.dev/</a>

### Recommended Project Structure

```
my-app/
├── app/                  # File-based routing (Expo Router)
│   ├── _layout.tsx       # Root layout
│   ├── index.tsx         # Home screen
│   └── (tabs)/           # Tab group
├── assets/
│   └── fonts/            # Custom font files (.ttf / .otf)
├── components/
├── constants/
├── hooks/
├── react-native.config.js
├── app.json
└── package.json
```

---

### Then

```bash
mkdir assets/audio assets/fonts assets/icons
mkdir auth components constants hooks lib store dummy-data types utils
touch .env
```

## 2. NativeWind (TailwindCSS)

NativeWind lets you style React Native components using Tailwind CSS utility classes via the `className` prop.

> Full docs: <a href="https://www.nativewind.dev/" target="_blank" rel="noopener noreferrer">https://www.nativewind.dev/</a>

---

## 3. Prebuild & Running the App

Prebuild generates the native `android/` and `ios/` folders from your Expo config.

```bash
# Ensure that the device is connected
adb devices

# Clean prebuild (recommended when changing native config)
npx expo prebuild --clean

# Install app
npx expo run:android # You can unplug the usb after this

# Start the dev server only (Every time after installing app - step 3)
npx expo start --dev-client
```

> Full docs: <a href="https://docs.expo.dev/workflow/continuous-native-generation/" target="_blank" rel="noopener noreferrer">https://docs.expo.dev/workflow/continuous-native-generation/</a>

> **Note:** After every change to `app.json`, `package.json` plugins, or native dependencies, re-run `npx expo prebuild --clean` and rebuild the app.

---

## 4. Google OAuth

### Prerequisites

- Follow YouTube Example <a href="https://www.youtube.com/watch?v=Hbru5P1Uxg0&pp=ygUjR29vZ2xlIHNpZ24gaW4gd2loIGVhY3QgTmF0aXZlIEV4cG8%3D" target="_blank" rel="noopener noreferrer">Google Sign-In with React Native Expo</a>
- A project on <a href="https://console.cloud.google.com/auth" target="_blank" rel="noopener noreferrer">Google Cloud Console</a>
- A debug/release keystore SHA-1 fingerprint (Android)
- A Bundle ID (iOS)

### Get the SHA-1 Fingerprint (Android)

```bash
# Managed by EAS
eas credentials
```

### Google Cloud Console Setup

1. Go to **APIs & Services > Credentials > Create Credentials > OAuth Client ID**
2. Create two clients:
   - **Android** — enter your package name (e.g. `com.yourname.app`) and SHA-1
   - **Web** — used as the `webClientId` in your app
3. For iOS, create an **iOS** client and enter your Bundle ID

### Install the Library

```bash
npx expo install @react-native-google-signin/google-signin
```

> Full docs: <a href="https://react-native-google-signin.github.io/" target="_blank" rel="noopener noreferrer">https://react-native-google-signin.github.io/</a>

### Usage

```tsx
import {
  GoogleSignin,
  GoogleSigninButton,
  statusCodes,
} from "@react-native-google-signin/google-signin";
import { useEffect } from "react";

// Configure once (e.g. in _layout.tsx or App.tsx)
GoogleSignin.configure({
  webClientId: "YOUR_WEB_CLIENT_ID.apps.googleusercontent.com",
});

export default function SignInScreen() {
  const signIn = async () => {
    try {
      await GoogleSignin.hasPlayServices();
      const userInfo = await GoogleSignin.signIn();
      console.log(userInfo);
    } catch (error: any) {
      if (error.code === statusCodes.SIGN_IN_CANCELLED) {
        console.log("User cancelled sign-in");
      } else if (error.code === statusCodes.IN_PROGRESS) {
        console.log("Sign-in already in progress");
      } else {
        console.error(error);
      }
    }
  };

  return <GoogleSigninButton onPress={signIn} />;
}
```

---

## 5. Custom Fonts

### Step 1 — Add Font Files

Place your `.ttf` or `.otf` files in `assets/fonts/`:

```
assets/
└── fonts/
    ├── MyFont-Regular.ttf
    └── MyFont-Bold.ttf
```

### Step 2 — Create `react-native.config.js`

```js
// react-native.config.js
module.exports = {
  assets: ["./assets/fonts"],
};
```

### Step 3 — Link the Assets

```bash
npx react-native-asset
```

This copies fonts into the native Android/iOS folders automatically.

### Step 4 — Use the Font

```tsx
<Text style={{ fontFamily: "MyFont-Regular" }}>Hello World</Text>
```

> **Tip:** With Expo, you can also load fonts at runtime using `expo-font`:
>
> ```tsx
> import { useFonts } from "expo-font";
>
> const [fontsLoaded] = useFonts({
>   "MyFont-Regular": require("./assets/fonts/MyFont-Regular.ttf"),
> });
> ```

---

## 6. Markdown Display

### Install

```bash
npx expo install react-native-markdown-display
```

### Usage

```tsx
import Markdown from "react-native-markdown-display";

export default function MarkdownScreen() {
  const content = `
# Hello
This is **bold** and _italic_ text.
- Item 1
- Item 2
  `;

  return <Markdown>{content}</Markdown>;
}
```

### Custom Styles

```tsx
const markdownStyles = {
  heading1: { fontSize: 24, fontWeight: "bold", color: "#333" },
  body: { fontSize: 16, lineHeight: 24 },
};

<Markdown style={markdownStyles}>{content}</Markdown>;
```

---

## 7. File-Based Routing

Expo Router uses the file system to define routes, similar to Next.js.

### Dynamic Routes

Create a file named `[id].tsx` inside the `app/` folder:

```tsx
// app/post/[id].tsx
import { useLocalSearchParams } from "expo-router";
import { Text, View } from "react-native";

export default function PostScreen() {
  const { id } = useLocalSearchParams();

  return (
    <View>
      <Text>Post ID: {id}</Text>
    </View>
  );
}
```

### Route Groups (No URL Segment)

Use parentheses to group routes without affecting the URL:

```
app/
├── (auth)/
│   ├── login.tsx      → /login
│   └── register.tsx   → /register
└── (tabs)/
    ├── index.tsx      → /
    └── profile.tsx    → /profile
```

---

## 8. Navigation & Params

### Navigate to a Route

```tsx
import { router } from "expo-router";

// Basic navigation
router.push("/page2");

// With params
router.push({ pathname: "/page2", params: { from: "Home", userId: "123" } });

// Replace current screen (no back button)
router.replace("/login");

// Go back
router.back();
```

### Read Params on the Destination Page

```tsx
import { useLocalSearchParams } from "expo-router";

export default function Page2() {
  const { from, userId } = useLocalSearchParams();

  return <Text>Came from: {from}</Text>;
}
```

### Typed Routes (Recommended)

Enable typed routes in `app.json` for autocomplete and safety:

```json
{
  "expo": {
    "experiments": {
      "typedRoutes": true
    }
  }
}
```

---

## 9. Layouts & Slots

Use `<Slot />` in a layout file when you want to wrap screens with shared UI like a `Header` or `Footer` without replacing the navigator.

```tsx
// app/_layout.tsx
import { Slot } from "expo-router";
import { View } from "react-native";
import Header from "@/components/Header";
import Footer from "@/components/Footer";

export default function RootLayout() {
  return (
    <View style={{ flex: 1 }}>
      <Header />
      <Slot /> {/* Child screen renders here */}
      <Footer />
    </View>
  );
}
```

> **`<Stack>`** replaces the child with a full stack navigator.  
> **`<Slot>`** just renders the matched child screen in place — useful when you want full control of the layout.

---

## 10. Image Picker

### Install

```bash
npx expo install expo-image-picker
```

### Add Permission to `app.json`

```json
{
  "expo": {
    "plugins": [
      [
        "expo-image-picker",
        {
          "photosPermission": "Allow $(PRODUCT_NAME) to access your photos.",
          "cameraPermission": "Allow $(PRODUCT_NAME) to use the camera."
        }
      ]
    ]
  }
}
```

### Usage

```tsx
import * as ImagePicker from "expo-image-picker";
import { Button, Image, View } from "react-native";
import { useState } from "react";

export default function ImagePickerScreen() {
  const [image, setImage] = useState<string | null>(null);

  const pickImage = async () => {
    const result = await ImagePicker.launchImageLibraryAsync({
      mediaTypes: ImagePicker.MediaTypeOptions.Images,
      allowsEditing: true,
      aspect: [4, 3],
      quality: 1,
    });

    if (!result.canceled) {
      setImage(result.assets[0].uri);
    }
  };

  const takePhoto = async () => {
    const permission = await ImagePicker.requestCameraPermissionsAsync();
    if (!permission.granted) return;

    const result = await ImagePicker.launchCameraAsync({
      allowsEditing: true,
      quality: 1,
    });

    if (!result.canceled) {
      setImage(result.assets[0].uri);
    }
  };

  return (
    <View>
      <Button title="Pick from Library" onPress={pickImage} />
      <Button title="Take Photo" onPress={takePhoto} />
      {image && (
        <Image source={{ uri: image }} style={{ width: 200, height: 200 }} />
      )}
    </View>
  );
}
```

---

## 11. Push Notifications

### Build with EAS First

```bash
npm install -g eas-cli
eas build --platform android
```

---

### Android Setup (Firebase Cloud Messaging — FCM V1)

#### Step 1 — Create a Firebase Project

1. Go to <a href="https://console.firebase.google.com" target="_blank" rel="noopener noreferrer">Firebase Console</a>
2. Click **Add Project** and follow the steps
3. Register your Android app using your package name (e.g. `com.yourname.app`)
4. Download the `google-services.json` file

#### Step 2 — Generate a Firebase Service Account Key

1. In Firebase, go to **Project Settings > Service Accounts**
2. Click **Generate new private key** and download the JSON file

#### Step 3 — Upload to Expo Credentials

1. In the <a href="https://expo.dev" target="_blank" rel="noopener noreferrer">Expo dashboard</a>, open your project
2. Go to **Credentials > Android**
3. Upload the downloaded key to **FCM V1 Service Account Key**

#### Step 4 — Add `google-services.json` to Your Project

Place the file in the root or any directory, then reference it in `app.json`:

```json
{
  "expo": {
    "android": {
      "googleServicesFile": "./google-services.json",
      "package": "com.yourname.app"
    }
  }
}
```

#### Step 5 — Install the Notifications Library

```bash
npx expo install expo-notifications expo-device
```

#### Step 6 — Request Permission & Get Push Token

```tsx
import * as Notifications from "expo-notifications";
import * as Device from "expo-device";
import { useEffect } from "react";
import { Platform } from "react-native";

Notifications.setNotificationHandler({
  handleNotification: async () => ({
    shouldShowAlert: true,
    shouldPlaySound: true,
    shouldSetBadge: false,
  }),
});

export async function registerForPushNotificationsAsync(): Promise<
  string | null
> {
  if (!Device.isDevice) {
    alert("Must use a physical device for push notifications");
    return null;
  }

  const { status: existingStatus } = await Notifications.getPermissionsAsync();
  let finalStatus = existingStatus;

  if (existingStatus !== "granted") {
    const { status } = await Notifications.requestPermissionsAsync();
    finalStatus = status;
  }

  if (finalStatus !== "granted") {
    alert("Permission not granted for push notifications!");
    return null;
  }

  const token = (await Notifications.getExpoPushTokenAsync()).data;
  console.log("Expo Push Token:", token);

  if (Platform.OS === "android") {
    await Notifications.setNotificationChannelAsync("default", {
      name: "default",
      importance: Notifications.AndroidImportance.MAX,
    });
  }

  return token;
}
```

---

### iOS Setup

1. An Apple Developer account is required
2. EAS will automatically handle APNs certificates when you run `eas build --platform ios`
3. Add the following to `app.json`:

```json
{
  "expo": {
    "ios": {
      "bundleIdentifier": "com.yourname.app"
    }
  }
}
```

---

## 12. Useful Utilities

### Screen Dimensions

```tsx
import { Dimensions } from "react-native";

const SCREEN_WIDTH = Dimensions.get("window").width;
const SCREEN_HEIGHT = Dimensions.get("window").height;
```

For dynamic updates on rotation, use the hook instead:

```tsx
import { useWindowDimensions } from "react-native";

const { width, height } = useWindowDimensions();
```

### Platform Detection

```tsx
import { Platform } from "react-native";

if (Platform.OS === "android") {
  // Android-specific code
} else if (Platform.OS === "ios") {
  // iOS-specific code
}
```

### Safe Area

```bash
npx expo install react-native-safe-area-context
```

```tsx
import { SafeAreaView } from "react-native-safe-area-context";

export default function Screen() {
  return <SafeAreaView style={{ flex: 1 }}>{/* content */}</SafeAreaView>;
}
```

### Async Storage

```bash
npx expo install @react-native-async-storage/async-storage
```

```tsx
import AsyncStorage from "@react-native-async-storage/async-storage";

await AsyncStorage.setItem("key", "value");
const value = await AsyncStorage.getItem("key");
await AsyncStorage.removeItem("key");
```

---

## 13. Developer Tools

| Action                            | How                                |
| --------------------------------- | ---------------------------------- |
| Open React Native DevTools        | Press `j` in the terminal          |
| Reload the app                    | Press `r` in the terminal          |
| Open Expo developer menu (device) | Shake the device or press `m`      |
| Toggle performance monitor        | Expo menu > Performance Monitor    |
| Inspect element                   | Expo menu > Show Element Inspector |
| Open React DevTools standalone    | `npx react-devtools`               |

---

## Quick Reference — Common Commands

```bash
# Create project
npx create-expo-app@latest my-app

# Prebuild (generates native folders)
npx expo prebuild --clean

# Run
npm run android
npm run ios

# Link fonts
npx react-native-asset

# EAS build
eas build --platform android
eas build --platform ios
eas build --platform all
```
