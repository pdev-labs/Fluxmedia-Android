# Android project

This is the Gradle project for the native FluxMedia Android app. See the
[project README](../README.md) for features, install instructions, signing
and the FFmpeg build.

## Quick build

Requirements: JDK 17, Python 3.12 on `PATH`, Android SDK platform 35, and the
Android NDK.

```bash
./build-apk.sh              # debug
./gradlew :app:assembleRelease
```

Outputs:

```
app/build/outputs/apk/debug/app-debug.apk
app/build/outputs/apk/release/app-release.apk
```

The app is licensed under **GPL-3.0**; see [../LICENSE](../LICENSE).
