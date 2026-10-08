# Android APK

This repository now includes a standalone Android build under `android/`.

The app is an Android-native shell around the validated offline attendance interface. It bundles the six Excel rosters and adds native Android handling for camera permission, Excel file selection, PDF/DOCX saving to Downloads, and PDF/DOCX sharing through the Android share sheet. The generated app icon uses the attendance QR/checklist artwork in `android/app/src/main/res/drawable/icon_attendance.png`.

## Install

Download `dist/Attendance.apk` to an Android phone and open it. Android may ask for permission to install an APK from the source used to download it. After installation, open **تسجيل الحضور** and grant camera permission when scanning begins.

## Included functionality

- Arabic RTL attendance interface
- Six bundled departments and Excel rosters
- Department-specific roster loading
- In-app `.xlsx` roster upload and replacement
- QR scanning with the phone camera
- Complete present/absent roster
- Green `حاضر` and red `غائب`
- Lecturer and subject fields
- Offline local persistence through the existing app storage
- RTL PDF and Word exports with signature footer
- Native Android save and share for both formats

## Build locally

The project uses Android Gradle Plugin 8.7.3, Gradle 8.10.2, Android SDK 35, and Java 17. From the repository root:

```bash
export ANDROID_SDK_ROOT=/path/to/android-sdk
export JAVA_HOME=/path/to/jdk-17
/path/to/gradle-8.10.2/bin/gradle :app:assembleDebug
```

The debug APK is written to `android/app/build/outputs/apk/debug/app-debug.apk`. The signed release artifact in this repository is `dist/Attendance.apk` (version 1.0.3).
