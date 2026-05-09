---
layout: default
title: Registration
parent: App Pages
---

# Registration Screen

The Registration screen allows new users to join the GESSGO community. It captures essential user details and initializes their first session token.

---

## 1. Visual Interface
The layout mirrors the Login screen to provide a cohesive branding experience while focusing on account creation.

*   **Layout File:** `app/src/main/res/layout/activity_register.xml`
*   **Key Assets:** Uses `@drawable/ic_logo` for branding and `@drawable/bg_input_field` for the input styling.
*   **Structure:** Hosted within a `ScrollView` to ensure all fields—**Full Name**, **Email Address**, and the **Register Button**—are accessible on any device size.

## 2. Functional Flow
The registration process is designed to be seamless, moving the user directly into an authenticated state or back to login upon success.

### Input Validation
*   **Logic File:** `RegisterActivity.kt`
*   **Check:** Before calling the API, the app verifies that both the Name and Email fields are not empty. If validation fails, a `Toast` message prompts the user to fill all fields.

### User Creation Process
1.  **Data Packaging:** The app uses the `RegisterRequest` model from `AuthModels.kt`.
2.  **API Call:** It launches a coroutine to hit the `registerUser` endpoint via `RetrofitClient`.
3.  **Token Handling:** 
    *   If the registration is successful (Status: true), the backend returns a unique `token`.
    *   The `TokenManager` saves this token locally to authenticate the user.
4.  **Redirect:** Upon success, the app displays a confirmation message and uses an `Intent` to navigate the user back to the **Login Screen** (`MainActivity`) while calling `finish()` to clear the registration activity from the backstack.

---

## 3. Navigation Links
*   **Login Link:** Provides a quick path for existing users to navigate back to `MainActivity`.
*   **Post-Registration:** By saving the token during registration, the app ensures that the user is recognized as "Authenticated" immediately upon their next login attempt.

---

{: .important }
> **Developer Note:** The `RegisterActivity` uses **ViewBinding** (`ActivityRegisterBinding`) to interact with UI elements, ensuring more stable and null-safe access to the layout views compared to traditional `findViewById`.