---
layout: default
title: Overview
nav_order: 1
---

# GESS Mobile App Overview

This documentation provides a comprehensive guide to the mobile application's codebase.  
Whether you are updating the **Fragment** logic, modifying **Drawables**, changing deployment settings, or configuring third-party integrations, follow the sections in the sidebar.

---

# Core Configuration & Deployment Guide

This section outlines how to modify core application settings, dependencies, permissions, and deployment identifiers derived from the central build files.

---

## 1. Changing the Deploy / Base URL

The application's API base endpoint is configured dynamically via Gradle build fields.

### File Location
`app/build.gradle.kts`

### Action
Locate the `defaultConfig` block and update the `BASE_URL` value.

> Ensure you escape the inner quotation marks correctly.

```kotlin
defaultConfig {
    // ... other configurations

    buildConfigField(
        "String",
        "BASE_URL",
        "\"https://scada.gess-sweden.com/\"" // <-- Change your production/staging URL here
    )
}
```

---

## 2. Changing the App Name

The application name is controlled in two places:

1. The launcher name shown to users
2. An internal configuration constant used inside the Kotlin codebase

---

### Update the Launcher Name (User Facing)

#### File Location
`app/src/main/res/values/strings.xml`

#### Action
Change the value inside the `app_name` string tag.

```xml
<resources>
    <string name="app_name">Your New App Name</string>
</resources>
```

This directly populates:

```xml
android:label="@string/app_name"
```

inside the `AndroidManifest.xml`.

---

### Update the Build Config Name (Internal)

#### File Location
`app/build.gradle.kts`

#### Action
Update the `APP_NAME` field inside the `defaultConfig` block.

```kotlin
buildConfigField(
    "String",
    "APP_NAME",
    "\"Your New App Name\""
)
```

---

## 3. Changing the App Logo / Icon

The application icon uses both standard and adaptive/round icon variations defined in the manifest asset paths.

### File Location
`app/src/main/res/mipmap-*`

### Recommended Method

In Android Studio:

1. Right-click the `res` folder
2. Select:
   - `New`
   - `Image Asset`
3. Choose your source image or vector asset
4. Generate the icons

Ensure the generated output names remain:

- `ic_launcher`
- `ic_launcher_round`

---

### Manifest References

The manifest references these specific asset names:

```xml
android:icon="@mipmap/ic_launcher"
android:roundIcon="@mipmap/ic_launcher_round"
```

---

### Important Note

If replacing icons manually through the file explorer, ensure all resolutions are updated inside:

- `mipmap-hdpi`
- `mipmap-mdpi`
- `mipmap-xhdpi`
- `mipmap-xxhdpi`
- `mipmap-xxxhdpi`

---

## 4. Changing Third-Party API Keys (Google Maps)

If you need to change the deployment key for Google Maps integration, update the manifest metadata entry.

### File Location
`app/src/main/AndroidManifest.xml`

### Action
Locate the `<meta-data>` tag and replace the API key value.

```xml
<!-- Google Maps key MUST be inside <application> -->
<meta-data
    android:name="com.google.android.geo.API_KEY"
    android:value="YOUR_NEW_API_KEY_HERE" />
```

---

## 5. Changing the Application ID & Package Namespace

If deploying a white-labeled or rebranded version of the app, update the unique application identifiers.

### File Location
`app/build.gradle.kts`

### Action
Change both:

- `applicationId`
- `namespace`

```kotlin
android {

    namespace = "com.example.GessGo" // <-- Internal namespace

    compileSdk = 35

    defaultConfig {

        applicationId = "com.example.GessGo" // <-- Play Store identifier

        // ...
    }
}
```

---

### Important Note

If the `namespace` changes, you must also refactor the physical package structure of your Kotlin source files.

Example:

```text
app/src/main/java/com/example/GessGo/
```

---

## 6. Managing Network Security (Cleartext Traffic)

By default, the application allows unencrypted HTTP traffic for development or staging environments.

### File Location
`app/src/main/AndroidManifest.xml`

### Current Setting

```xml
<application
    android:usesCleartextTraffic="true">
```

---

### Security Recommendation

For production deployments:

- Set this value to `false`
- Ensure your `BASE_URL` uses secure HTTPS endpoints

```xml
<application
    android:usesCleartextTraffic="false">
```

---

## 7. Device Permissions & Features Reference

The app relies on several hardware and privacy permissions declared inside `AndroidManifest.xml`.

If certain functionality is no longer required, these permissions can be removed safely.

| Permission / Feature | Purpose |
|---|---|
| `android.permission.INTERNET` | Required for communicating with the API server and loading Google Maps data |
| `android.permission.ACCESS_FINE_LOCATION` | Required for precise user positioning on the map tracker |
| `android.permission.ACCESS_COARSE_LOCATION` | Required for approximate user positioning |
| `android.hardware.camera.any` | Declares camera hardware availability for barcode scanning |
| `android.permission.CAMERA` | Required to open the physical device camera in `ScannerActivity` |

---

## 8. SDK Target Configurations

The application targets modern Android runtimes and uses Java 11 compatibility.

### File Location
`app/build.gradle.kts`

### Current SDK Configuration

| Configuration | Value |
|---|---|
| `minSdk` | 24 (Android 7.0 Nougat) |
| `targetSdk` | 35 (Android 15) |
| `compileSdk` | 35 |
| JVM Target | 11 |

---

## 9. Build Configuration Summary

Below is a quick summary of the primary configurable deployment fields.

| Configuration | File | Purpose |
|---|---|---|
| `BASE_URL` | `app/build.gradle.kts` | API environment endpoint |
| `APP_NAME` | `app/build.gradle.kts` | Internal application name |
| `applicationId` | `app/build.gradle.kts` | Google Play unique package ID |
| `namespace` | `app/build.gradle.kts` | Internal Kotlin package namespace |
| `app_name` | `strings.xml` | Launcher-visible app name |
| Google Maps API Key | `AndroidManifest.xml` | Google Maps SDK authorization |
| `usesCleartextTraffic` | `AndroidManifest.xml` | HTTP/HTTPS network security policy |

---

## 10. Deployment Checklist

Before generating a production APK/AAB, verify the following:

- [ ] Production `BASE_URL` is configured
- [ ] HTTPS is enabled
- [ ] `usesCleartextTraffic="false"`
- [ ] Google Maps API key is valid
- [ ] App icons are updated
- [ ] Application name is finalized
- [ ] `applicationId` matches release branding
- [ ] Unused permissions are removed
- [ ] Release signing configuration is verified
- [ ] SDK versions match dependency requirements

---

# Additional Notes

- Always clean and rebuild the project after changing Gradle configuration values.
- Some changes (especially `applicationId` and `namespace`) may require:
  - Gradle sync
  - Cache invalidation
  - Full project rebuild
- Ensure third-party SDK versions remain compatible with:
  - `targetSdk = 35`
  - JVM target 11

---