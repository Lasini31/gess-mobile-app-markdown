---
layout: default
title: Routes & Navigation
parent: Architecture & Logic
---

# Routes & Navigation

The GESS Mobile App uses a centralized navigation system. This section explains how to modify the screens, the navigation bar, and the API communication layer.

---

## 1. Global API Routes
All network communication and backend endpoints are defined in a single source of truth. If you need to change a URL or add a new feature, start here:

*   **File Path:** `app/src/main/java/com/example/project1/ApiService.kt`
*   **Purpose:** Contains all Retrofit interfaces and endpoint definitions (e.g., Wallet status, History logs).

## 2. Navigation UI (Bottom Bar)
The main navigation structure is managed by a container activity that hosts the core fragments (**Home, Map, History, Wallet**).

### Layout (XML)
*   **File Path:** `app/src/main/res/layout/activity_main_container.xml`
*   **Description:** This file defines the Bottom Navigation View. It controls the icons, labels, and the physical space where each page is loaded.

### Logic (Kotlin)
*   **File Path:** `app/src/main/java/com/example/project1/MainContainerActivity.kt`
*   **Description:** This activity handles the "click" events for the bottom menu. It switches between fragments when a user taps an icon.

## 3. Profile Access
Unlike the four main tabs, the **Profile Page** is accessed via a dedicated action button.

*   **Trigger:** A button located in the **Top Bar** of the `MainContainerActivity`.
*   **Action:** When clicked, it launches the `ProfileActivity` or navigates to the Profile Fragment.

---

{: .note }
> **Tip for Developers:** When adding a new page, you must define the route in `ApiService.kt` first, then add the corresponding menu item in the `navigation` resource folder before updating the XML layout.

# Authentication Logic

To keep the codebase clean and avoid repetitive code, the GESS app uses an **Interceptor** pattern for handling user authentication.

## The Auth Interceptor
Instead of manually adding an "Authorization" header to every single request in `ApiService.kt`, we use a central gatekeeper that automatically injects the necessary tokens.

*   **File Path:** `app/src/main/java/com/example/project1/AuthInterceptor.kt`
*   **How it works:** 
    1. It "intercepts" every outgoing network request before it leaves the app.
    2. It retrieves the saved user token (from SharedPreferences or EncryptedSharedPrefs).
    3. It attaches the token to the request header.
    4. It sends the request to the backend.

---

## Why this matters for Developers
If you are adding a new feature or a new page (like **Wallet** or **History**), you **do not** need to worry about headers.

**Example of Clean API Code:**
Because of the interceptor, your `ApiService.kt` remains clean:

```kotlin
// You DON'T need @Header("Authorization") here!
@GET("api/wallet/my-account-status")
suspend fun getWalletStatus(): Response<WalletResponse>