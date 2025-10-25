# Project Overview: expo-updates

`expo-updates` is an Expo module designed to manage remote updates for React Native applications. It allows apps to fetch, download, and apply over-the-air updates to their JavaScript bundles and assets from a server implementing the Expo Update protocol (e.g., EAS Update). The module supports both iOS and Android platforms, with a significant portion of its native implementation in Kotlin for Android.

## Core Functionality

*   **Update Management:** Checks for, downloads, and applies new updates.
*   **Code Signing:** Verifies the authenticity and integrity of updates using code signing.
*   **Asset Management:** Downloads and manages application assets, including support for binary patching (BSPatch) to optimize download sizes.
*   **JavaScript API:** Provides a comprehensive API for React Native applications to interact with the update system, query update status, and trigger reloads.
*   **CLI Tools:** Offers command-line utilities for development and build processes related to updates, such as code signing generation and asset verification.

## Architecture Highlights

*   **Cross-Platform:** Implemented with native modules for iOS (Swift/Objective-C, though not fully explored here) and Android (Kotlin).
*   **Modular Design:** Separates concerns into distinct components like `UpdatesController` (native orchestration), `FileDownloader` (network operations), and `UpdatesModule` (JS bridge).
*   **State Machine:** Uses a state machine (`UpdatesStateMachine.kt` in Android) to manage the lifecycle and various states of the update process.
*   **Database Integration:** Utilizes Room (on Android) for persistent storage of update metadata and assets.
*   **Networking:** Employs OkHttp for robust and efficient network requests on Android.

## Android Specifics

The Android implementation is primarily in Kotlin and leverages several Android-specific features:

*   **Build Configuration:** `android/build.gradle` defines dependencies (e.g., `androidx.room`, `okhttp`), `BuildConfig` fields for runtime configuration, and native build settings (CMake for BSPatch/BZip2).
*   **UpdatesController:** The central singleton (`UpdatesController.kt`) for initializing and managing the update process, handling different configurations (e.g., development mode, custom initialization).
*   **UpdatesModule:** Exposes native update functionalities to the JavaScript layer via `UpdatesModule.kt`.
*   **FileDownloader:** (`FileDownloader.kt`) Manages downloading update manifests and assets, supporting multipart responses, code signing validation, and applying `bsdiff` patches for efficient updates.
*   **BSPatch/BZip2:** Integrates native C/C++ code for binary patching, specifically using a modified version of FreeBSD's BSPatch and BZip2 for decompression.

## Building and Running

This project is an Expo module. Building and running typically involves integrating it into a larger Expo or React Native project.

*   **Build Command:** `yarn build` (uses `expo-module build`)
*   **Test Command:** `yarn test` (uses `expo-module test`)
*   **CLI Commands:** The `bin/cli.js` entry point provides various commands:
    *   `npx expo-updates codesigning:generate`
    *   `npx expo-updates codesigning:configure`
    *   `npx expo-updates assets:verify`
    *   `npx expo-updates fingerprint:generate`
    *   `npx expo-updates runtimeversion:resolve`
    *   `npx expo-updates configuration:syncnative`

For local development and testing, `DEVELOPMENT.md` suggests:
*   Using a bare React Native app.
*   Replacing the `expo-updates` dependency in `package.json` with a file path to the local module: `"expo-updates": "file:/path/to/expo/expo/packages/expo-updates"`.
*   Running `yarn --force` after changes to copy them into `node_modules`.
*   Configuring `EXUpdatesURL` and `EXUpdatesRuntimeVersion` in `Expo.plist` (iOS) or `AndroidManifest.xml` (Android).
*   Setting `EX_UPDATES_NATIVE_DEBUG=1` environment variable to enable updates in debug builds.

## Development Conventions

*   **TypeScript:** Primary language for JavaScript/React Native code.
*   **Kotlin:** Primary language for Android native code.
*   **Testing:** Uses `expo-module-scripts` for testing.
*   **Linting:** Uses `expo-module lint`.
*   **Code Signing:** Emphasizes secure update delivery through code signing.
*   **Error Recovery:** Includes mechanisms for error recovery during update processes.
