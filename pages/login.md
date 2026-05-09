---
layout: default
title: Login & Entry
parent: App Pages
---

# Login Screen

The Login screen serves as the primary entry point for the GESSGO application. It manages user authentication and initial session setup.

---

## 1. Visual Interface
The UI is designed to be clean and focused, ensuring the user can authenticate quickly.

*   **Layout File:** `app/src/main/res/layout/activity_main.xml`
*   **Key Assets:** Uses `@drawable/ic_logo` for branding and `@drawable/bg_input_field` for styled text inputs.
*   **Structure:** A scrollable interface containing email/password inputs, a primary Login button, and a link to the Registration screen.

## 2. Authentication Flow
The logic follows a "Gatekeeper" pattern to ensure users don't have to log in every time they open the app.

### Pre-Login Check (Auto-Login)
Before the screen is even shown, the app checks for an existing session:
1.  **Theme Sync:** Applies the saved Light/Dark mode preference from `theme_prefs`.
2.  **Token Validation:** Checks `TokenManager` for a saved `access_token`.
3.  **Bypass:** If a valid token exists, the app automatically navigates to the **Dashboard** (`MainContainerActivity`).

### The Login Process
*   **Logic File:** `MainActivity.kt`
*   **Data Model:** Uses `LoginRequest` from `AuthModels.kt` to package credentials.
*   **API Route:** Calls the `login` endpoint defined in `ApiService.kt`.
*   **Success Handler:** Upon a successful 200 OK response, the `access_token` is saved via `TokenManager`, and the user is redirected to the main app container.

---

## 3. Navigation Links
*   **Register:** Redirects users to `RegisterActivity`.
*   **Dashboard:** Upon successful auth, the app transitions to `MainContainerActivity`, which initializes the **Bottom Navigation Bar**.

---

{: .note }
> **Security Detail:** The application uses an `AuthInterceptor` to automatically attach the saved token to all subsequent API calls once the login is successful.