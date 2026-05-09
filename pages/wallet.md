---
layout: default
title: Wallet & Payments
parent: App Pages
---

# Wallet & Payments Screen

The Wallet screen serves as the financial hub of the GESSGo application. It manages two distinct types of data: daily EV charging activity and external financial transactions through Stripe.

---

## 1. Visual Interface
The screen is built using a `NestedScrollView` containing two primary `CardView` modules to separate consumption from funding.

*   **Layout File:** `app/src/main/res/layout/fragment_wallet.xml`
*   **Wallet Activity Card:** Displays a 30-day summary of energy consumption ("In", "Out", "Net") in a vertical list.
*   **Payment Transactions Card:** Features a **Horizontal Scroll View** to accommodate wide table data (ID, External Ref, Amount, Status) and a filtering header for Month/Year selection.
*   **Top-Up Dialog:** A custom styled alert dialog (`dialog_top_up`) for entering USD amounts.

## 2. Technical Logic & Data Handling

### Dual Adapter System
The fragment manages two separate `RecyclerView` instances simultaneously:
1.  **WalletActivityAdapter:** Processes consumption logs retrieved from `getWalletLast30()`.
2.  **WalletTransactionsAdapter:** Processes Stripe payment records retrieved from `getStripePaymentDetails()`.

### Stripe Integration (In-Progress)
The wallet facilitates external funding via the **Stripe Checkout** web flow:
*   **Initiation:** `initiateStripeCheckout(amount)` sends a request to the backend to generate a unique Stripe session.
*   **Redirection:** Upon a successful API response, the app uses an `ACTION_VIEW` intent to open the `checkout_url` in the system browser.
*   **Validation:** The app enforces a minimum top-up of **$1.00** and validates numeric input to prevent runtime crashes.

### Multi-Layer Filtering
For the Payments Ledger, the app performs client-side filtering:
*   **Mechanism:** It maintains a `fullPaymentsList` in memory. When the "Go" button is pressed, it uses a `monthMap` to convert human-readable months (e.g., "Jan") into API-compatible date codes (e.g., "-01-") to filter the master list without redundant network calls.

## 3. Data Integration
*   **Consumption Recording:** The `recordChargeSession` method allows the app to manually post energy usage (kWh) to the ledger, which is then reflected in the Wallet balance.
*   **Auth Interceptor:** All wallet-related calls are secured; if a token expires during a top-up request, the `AuthInterceptor` automatically redirects the user to the Login screen.

---

## 4. Key API Endpoints
| Action | Endpoint | Description |
| :--- | :--- | :--- |
| Balance Check | `api/wallet/my-account-status` | Returns current Energy Points (EP). |
| Activity Log | `api/wallet/history-30-days` | Returns daily consumption/credit logs. |
| Stripe Details | `api/wallet/stripe-payments` | Returns history of successful/pending top-ups. |
| Top-Up | `api/stripe/initiate-checkout` | Generates the Stripe web checkout URL. |

---

{: .important }
> **Ongoing Development:** The Top-Up API integration is currently being finalized. Ensure the `TopUpRequest` model matches the backend's expected `amount` integer field.