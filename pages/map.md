---
layout: default
title: Station Map
parent: App Pages
---

# Station Map Screen

The Map Screen provides a spatial view of all charging infrastructure. It integrates the Google Maps SDK to help users locate their current position, discover nearby stations, and calculate driving routes.

---

## 1. Visual Interface
The map uses a full-screen `SupportMapFragment` with custom-styled overlay controls for navigation and discovery.

*   **Layout File:** `app/src/main/res/layout/fragment_map.xml`
*   **Map Controls:**
    *   **Locate Me:** Centers the camera on the user’s GPS coordinates.
    *   **Route:** Calculates and draws a polyline between the user and the selected station.
    *   **Closest:** Automatically finds the station with the shortest physical distance.
    *   **Show All:** Adjusts the zoom level (Camera Bounds) to fit every available marker on the screen.

## 2. Technical Logic
The fragment implements `OnMapReadyCallback` and handles real-time location tracking and external API calls.

### Station Discovery
*   **Marker Mapping:** When stations are fetched from the server, they are stored in a `markerStationMap`. This allows the app to instantly retrieve station metadata (like site name or alias) when a user taps a specific Google Map marker.
*   **Nearest Station Algorithm:** The "Closest" feature uses `android.location.Location.distanceBetween` to iterate through all loaded markers and compare their coordinates to the user's `currentLatLng`.

### Routing & Navigation
The app uses the **Google Directions API** to provide driving paths:
1.  **Request:** It sends a background HTTP request using `OkHttpClient` containing the origin (User) and destination (Station) coordinates.
2.  **Polyline Decoding:** The API returns an encoded string. The fragment includes a custom `decodePoly` method to convert this string into a list of `LatLng` points.
3.  **Visual Styling:** The route is drawn as a blue, rounded polyline with a width of `15f` to ensure visibility over the map terrain.

## 3. Permissions & Requirements
*   **Location Access:** Requires `Manifest.permission.ACCESS_FINE_LOCATION`. The app checks this at runtime before enabling the "My Location" blue dot.
*   **API Key:** Uses a Google Maps API Key for both the Map SDK and the Directions API.
*   **UI Padding:** `mMap.setPadding(0, 150, 0, 300)` is applied programmatically to ensure the Google logo and zoom buttons remain visible behind the custom floating action buttons.

---

## 4. Key API Endpoints
| Action | Endpoint | Provider |
| :--- | :--- | :--- |
| Fetch Stations | `getAllStations` | GESSGo Backend |
| Driving Route | `maps.googleapis.com/.../directions` | Google Maps API |

---

{: .warning }
> **Configuration Note:** The Directions API requires a valid Billing Account on the Google Cloud Console. If the `status` returns "REQUEST_DENIED" in Logcat, verify that the Directions API is enabled for your specific API key.