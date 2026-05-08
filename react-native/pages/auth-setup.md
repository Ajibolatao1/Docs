# Auth Setup — React Native (Dosify)

## Files

| File | Role |
|---|---|
| `auth/authHelper.ts` | Token validation, SecureStore helpers, refresh logic |
| `store/authStore.ts` | Global auth state (Zustand) |
| `components/AuthInitializer.tsx` | Runs `initializeAuth` on app mount |
| `utils/fetchWithAuth.ts` | Authenticated fetch wrapper — handles 401s with auto-refresh |
| `utils/Provider.tsx` | Wraps app with QueryClientProvider + AuthInitializer |

---

## How It Works

### 1. Login

After a successful login request, the backend returns:

```json
{
  "success": true,
  "message": "Login successful",
  "accessToken": "<jwt>",
  "sessionToken": "<jwt>",
  "user": { ... }
}
```

Call `loginSuccess(accessToken, sessionToken, user)` from the auth store. This:
- Saves the `sessionToken` to **SecureStore** (encrypted on-device storage)
- Stores the `accessToken` and `user` in Zustand state (in-memory)
- Sets `isLoggedIn: true`

The `accessToken` is short-lived (used for API requests). The `sessionToken` is long-lived (used only to refresh the access token).

---

### 2. Making Authenticated Requests

Use `fetchWithAuth` from `utils/fetchWithAuth.ts` instead of raw `fetch`. It reads the access token from the store automatically, and handles expiry transparently:

```ts
import { fetchWithAuth } from "@/utils/fetchWithAuth";

const res = await fetchWithAuth(api.some.endpoint);
const res = await fetchWithAuth(api.some.endpoint, {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify(payload),
});
```

On a 401 response, `fetchWithAuth` will:
1. Call `refreshCredentials()` to silently get a new access token
2. Retry the original request with the new token
3. If the refresh fails, call `logout()` and return the 401 response

> Do **not** manually attach `Authorization` headers or read `accessToken` from the store for API calls — use `fetchWithAuth` for all authenticated requests.

---

### 3. Token Refresh (`refreshCredentials`)

Called automatically by `fetchWithAuth` and during app startup — you should not need to call this directly. It:
1. Reads the `sessionToken` from SecureStore
2. Sends a `POST /auth/mobile-refresh-token` request with `Authorization: Bearer <sessionToken>`
3. Saves the rotated `sessionToken` returned in the response back to SecureStore
4. Returns a new `accessToken` (or `null` on failure)

A global lock (`isRefreshing`) prevents parallel refresh calls — concurrent calls queue up as subscribers and resolve once the single refresh completes. On failure, all queued subscribers are cleared and `null` is returned.

> **Why a separate mobile endpoint?** The web `POST /auth/refresh-token` reads the session token from an HttpOnly cookie (set automatically by the browser). Mobile clients have no cookie jar, so a dedicated endpoint reads it from the `Authorization` header instead and returns the new session token in the JSON body.

---

### 4. App Startup (`initializeAuth`)

`AuthInitializer` runs `initializeAuth()` once on mount. The flow:

1. If already logged in (state hydrated), exit early
2. Call `refreshCredentials()` — attempts a silent login using the stored `sessionToken`
3. If a valid `accessToken` comes back, fetch `GET /users/me` to rehydrate the user object
4. On success: set `isLoggedIn: true` with fresh user data
5. On failure (expired/missing session): clear SecureStore, reset state → user goes to login screen

This means users stay logged in across app restarts as long as their `sessionToken` is valid.

> Screens that render auth-dependent UI should gate on `isAuthLoading` from the auth store to avoid a flash of "Not Logged In" before `initializeAuth` completes:
> ```ts
> const { isLoggedIn, isLoading: isAuthLoading } = useAuthStore();
> if (isAuthLoading) return <LoadingScreen />;
> ```

---

### 5. Logout

`logout()`:
1. Reads `sessionToken` from SecureStore
2. Calls `POST /auth/logout` with `Authorization: Bearer <sessionToken>`
3. Deletes `sessionToken` from SecureStore
4. Resets all auth state in Zustand

---

## Storage Strategy

| Data | Where | Why |
|---|---|---|
| `sessionToken` | `expo-secure-store` | Encrypted, persists across restarts, never exposed to JS bundle |
| `accessToken` | Zustand (memory) | Short-lived, no need to persist — refreshed on each startup |
| `user` | Zustand (memory) | Re-fetched on startup to stay fresh |

> **Why not AsyncStorage?** SecureStore encrypts data using the device keychain (iOS) / Keystore (Android). AsyncStorage is plaintext — unsuitable for auth tokens.

---

## Key Difference from Web Version

| Concern | Web (Next.js) | React Native |
|---|---|---|
| Session persistence | HTTP-only cookie (browser) | `expo-secure-store` |
| Sending session on refresh | Cookie sent automatically | `Authorization: Bearer <sessionToken>` header |
| Refresh endpoint | `POST /auth/refresh-token` | `POST /auth/mobile-refresh-token` |
| New session token delivery | Set-Cookie header | JSON response body (`sessionToken`) |
| Dev checks | `process.env.NODE_ENV` | `__DEV__` |
| `"use client"` directives | Required for Next.js | Removed — not valid in RN |
