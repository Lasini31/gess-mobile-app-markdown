---
layout: default
title: Models & Data
parent: Architecture & Logic
---

# Models & Data

The GESS Mobile App uses a structured approach to data handling. Every API request and response is mapped to a specific Kotlin data class to ensure type safety and easy debugging.

---

## 1. Authentication Models
All models related to user access, including login credentials and registration details, are centralized here.

*   **File Path:** `app/src/main/java/com/example/project1/AuthModels.kt`
*   **Contents:** 
    *   **Login:** Includes the `LoginRequest` (email/password) and the `AuthResponse` (user token and profile data).
    *   **Registration:** Includes the data structures required to create a new user account.

## 2. Current Session: The User Model
While `AuthModels` handle the "gatekeeping," we use a specific standalone model to represent the **currently authenticated user session**.

*   **Model Name:** `User.kt`
*   **Purpose:** This model stores the specific profile details of the current user (e.g., you) throughout the app's lifecycle.
*   **Usage:** It is used to populate the **Profile Page** and personalize the **Wallet** and **History** headers.

## 3. Feature & Application Models
Every other data structure used in the app—from Wallet balances to Map coordinates—is organized within this file.

*   **File Path:** `app/src/main/java/com/example/project1/OtherModels.kt`
*   **Contents:** 
    *   **Wallet:** `WalletResponse` and transaction history objects.
    *   **Map/History:** Data classes for EV station locations and charging session logs.

---

## 4. API Mapping Pattern
To ensure the app communicates correctly with the backend, our models are designed to mirror the exact JSON format of the GESS API.

### Request & Response Format
Each route in `ApiService.kt` has a corresponding pair of models:
1.  **Request Model:** Defines the data the backend sends to the app via the API (e.g., `LoginRequest`).
2.  **Response Model:** Defines the data the app sends back to the backend via the API (e.g., `LoginResponse`).

---

{: .important }
> **Development Rule:** When modifying an API endpoint, you must update the corresponding model in `AuthModels.kt` or `OtherModels.kt` first. If the Kotlin variable names do not match the JSON keys from the API, use the `@SerializedName` annotation to map them correctly.