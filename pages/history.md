---
layout: default
title: Charging History
parent: App Pages
---

# Charging History Screen

The Charging History screen provides a comprehensive log of all previous charging sessions. It allows users to track their energy consumption and spending (Points) over time with instant local filtering.

---

## 1. Visual Interface
The layout is optimized for readability, grouping detailed session data into a unified "Sessions" card.

*   **Layout File:** `app/src/main/res/layout/fragment_history.xml`
*   **Filter Bar:** A horizontal button group that allows users to toggle between **All**, **This Month**, and **Last 3 Months**.
*   **Empty State:** A dedicated `tvEmptyHistory` view that appears if no sessions match the selected filter or if the account has no history.
*   **Dynamic Styling:** Buttons switch between `bg_button_green` (Active) and `bg_button_outline` (Inactive) to provide clear visual feedback on the current filter.

## 2. Technical Logic & Filtering
Unlike the Home screen, the History screen minimizes API calls by fetching the full dataset once and performing **local filtering** using Kotlin collections.

### Local Data Management
*   **Fetch Once:** Upon loading, `loadHistory()` retrieves the complete history and stores it in `allSessions`.
*   **Date Parsing:** The app parses ISO timestamps (e.g., `2026-04-10T14:50:00`) into native Java `Date` objects to perform mathematical comparisons against the current `Calendar` time.
*   **Mapping to UI:** The raw `ChargingSession` data is mapped into a `WalletHistoryUiItem` object, which standardizes formats for:
    *   **Date:** "Apr 10, 2026"
    *   **Time:** "14:50"
    *   **Energy:** Formatted from Wh to a readable string.

### Filtering Logic
The `filterHistory(monthsLimit: Int)` function handles the view logic:
*   **0 Months:** Shows the entire `allSessions` list.
*   **N Months:** Uses `calendar.add(Calendar.MONTH, -limit)` to create a cutoff point. It then filters the list to include only sessions where `started_at` is after the cutoff.

## 3. Data Integration
*   **RecyclerView Adapter:** Uses a `TransactionsAdapter` to render the list.
*   **Lifecycle Awareness:** Network calls are wrapped in `viewLifecycleOwner.lifecycleScope` to prevent memory leaks if the user navigates away during a data fetch.

---

## 4. Key API Endpoints
| Action | Endpoint | Response Model |
| :--- | :--- | :--- |
| Fetch History | `getChargingHistory` | `List<ChargingSession>` |

---

{: .note }
> **Implementation Detail:** The `formatDate` and `formatTime` methods include try-catch blocks with a "raw date" fallback. This ensures that even if the server sends an unexpected date format, the UI won't crash and will still display the available information.