---
layout: default
title: Home Dashboard
parent: App Pages
---

# Home Screen

The Home Screen is the central hub of the GESSGO application. It manages the end-to-end charging workflow, from station discovery to live power monitoring and session summaries.

---

## 1. Visual Interface
The dashboard uses a dynamic state-based UI that switches visibility based on the current stage of the charging process.

*   **Layout File:** `app/src/main/res/layout/fragment_home.xml`
*   **Key States:**
    *   **Input Area:** Field for manual Charger ID entry and a **QR Scanner** trigger.
    *   **Charger Preview:** Displays station metadata (Site Name, Charger Power) before starting.
    *   **Live Transaction:** Shows real-time metrics (Transaction ID, Duration, Energy).
    *   **Session Summary:** A post-charging breakdown showing total energy and time.



## 2. Technical Logic Flow
The logic is handled by `HomeFragment.kt` and utilizes Kotlin Coroutines for non-blocking UI updates.

### Charger Identification
1.  **Input:** Users enter a Charger ID (Format: `CP-001_1`) or scan a QR code.
2.  **Verification:** The app calls `fetchConnectorDetails` to check if the station is Available, Preparing, or Occupied.
3.  **UI Feedback:** The status pill (e.g., "● PREPARING") updates its color and text based on the server response.

### Session Persistence (Crash Recovery)
To ensure anyone can use the app without losing data, the fragment includes **Session Persistence**:
*   **Mechanism:** Uses `SharedPreferences` (`GessGo_Prefs`) to save the `active_session_id` and `active_tx_id`.
*   **Recovery:** On app restart or navigation back to Home, `restoreActiveSession()` calculates the elapsed time and resumes the live timer and meter polling automatically.

### Live Meter Data & Timer
Once charging begins, the app initiates a `chargingJob`:
*   **Polling:** Every 2 seconds, the app hits the `getMeterValues` endpoint to fetch Power (W), Energy (Wh), and Cumulative totals.
*   **UI Sync:** A smooth 1-second "tick" updates the `tvTimeValue` (HH:MM:SS) locally to ensure a responsive feel.

## 3. Data Integration
*   **Wallet:** Calls `getWallet()` to update the "Current Balance," "Today Usage," and "Monthly Average" cards.
*   **Interceptors:** All requests automatically include the bearer token via `AuthInterceptor`.

---

## 4. Key API Endpoints
| Action | Endpoint | Model Used |
| :--- | :--- | :--- |
| Check Station | `getConnectorDetails` | `ConnectorDetailsRequest` |
| Start Session | `startCharging` | `StartChargingRequest` |
| Poll Transaction | `pollForTransactions` | `TransactionStatusRequest` |
| Stop Session | `stopCharging` | `StopChargingRequest` |

---

{: .important }
> **Developer Note:** When debugging charging issues, check the `HOME_DEBUG` tag in Logcat. The polling mechanism uses `withTimeoutOrNull` to prevent the UI from freezing if the network is slow.