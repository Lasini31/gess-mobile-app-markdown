---
layout: default
title: User Profile
parent: App Pages
---

# User Profile Screen

The Profile screen manages user identity, contact information, security settings, and app-level preferences like theme selection. It features a robust "Edit Mode" that toggles between read-only and input-enabled states.

---

## 1. Visual Interface
The layout is organized into four distinct functional cards for better scannability.

*   **Layout File:** `app/src/main/res/layout/fragment_profile.xml`
*   **Header Card:** Displays the user's profile picture (via Glide), full name, and email. In Edit Mode, the name label is replaced by `EditText` fields for First and Last names.
*   **Contact Details Card:** Contains fields for Address, Primary/Secondary contact numbers, and Date of Birth. These use `TextInputLayout` for a modern material look.
*   **Security Card:** Dedicated area for password management with built-in password visibility toggles.
*   **Settings Card:** Handles local device settings like **Dark Mode** and the logout action.

## 2. Technical Logic & State Management

### Dynamic Edit Mode
The fragment uses an `isEditMode` boolean to control the UI state:
*   **Read State:** Fields are disabled (`isEnabled = false`) and the header shows a single `TextView` for the full name.
*   **Edit State:** Triggered by `enterEditMode()`. Fields become interactive, and the "Edit" button changes to "Save".
*   **Persistence:** When "Save" is pressed, the app calls `performProfileUpdate()`, sending a structured `UpdateUserDataRequest` to the backend.

### Date of Birth Selection
Instead of manual text entry, the `etDOB` field uses a `DatePickerDialog`:
*   **Constraint:** Only triggers when `isEditMode` is active.
*   **Formatting:** Automatically converts the selection into a `YYYY-MM-DD` string to match backend database requirements.

### Theme & Session Management
*   **Dark Mode:** Integrated with `AppCompatDelegate`. The user's preference is saved in `SharedPreferences` (`theme_prefs`) to ensure the theme persists across app restarts.
*   **Logout:** Clears the `TokenManager` cache and uses `Intent.FLAG_ACTIVITY_CLEAR_TASK` to flush the activity stack, ensuring the user cannot navigate back into the app without re-authenticating.

## 3. Data Integration
*   **Image Loading:** Uses the **Glide** library to handle asynchronous profile image loading and circular cropping via `ShapeableImageView`.
*   **Data Binding:** The `bindUserData()` method handles null-safety, ensuring that if a field (like `contact_no_2`) is missing from the API, the app displays an empty field rather than "null".

---

## 4. Key API Endpoints
| Action | Endpoint | Description |
| :--- | :--- | :--- |
| Fetch Profile | `api/profile/user-data` | Retrieves full profile and contact info. |
| Update Profile | `api/profile/update` | Updates names, address, contacts, and DOB. |
| Security | `api/profile/change-password` | *(Ongoing)* Handles current/new password verification. |

---

{: .note }
> **Development Status:** The password update and profile image upload features are currently in the coding phase. The `handlePasswordUpdate()` method currently serves as a placeholder for the upcoming Security API integration.