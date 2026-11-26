# Contributing to FluxMedia for Android

Thanks for helping improve FluxMedia on Android. The app is at **1.0.0** and
still in beta, so bug reports and focused pull requests are especially
welcome.

## Where things live

- **Android app:** this repository (`android/`, Kotlin + Compose + Chaquopy).
- **Python engine:** the [main FluxMedia repository](https://github.com/pdev-labs/FluxMedia)
  under `src/fluxmedia`. Engine fixes belong there; sync the result into
  `src/fluxmedia/` here.
- **FFmpeg build:** `android/scripts/build-ffmpeg.sh`.

## Before you start

- Search the [issue tracker](https://github.com/pdev-labs/Fluxmedia-Android/issues)
  for an existing report.
- For large changes, open an issue first so the approach can be agreed on.
- Never commit keystores, `local.properties`, API tokens or downloaded media.

## Build and test

Requirements: JDK 17, Python 3.12 on `PATH`, Android SDK platform 35, and the
Android NDK.

```bash
cd android
./build-apk.sh              # debug APK
./gradlew :app:assembleRelease
```

Install the debug build on a device or emulator:

```bash
adb install -r app/build/outputs/apk/debug/app-debug.apk
```

Please test the screens you touch on at least one real device where possible,
including the Download, Library and Settings flows.

## Syncing the Python engine

```bash
rsync -a --delete ../FluxMedia/src/fluxmedia/ src/fluxmedia/
```

Commit the engine sync separately from Android changes so it is easy to
review.

## Code style

- Kotlin follows the existing Compose structure: one screen per file under
  `ui/screens`, state in `FluxMediaViewModel`, I/O in the repository.
- Use Material 3 components and `MaterialTheme.colorScheme` colours; do not
  hard-code colours except in the theme palette.
- Keep new user-facing strings in the composables for now, matching the
  surrounding code.
- Run `./gradlew :app:lintDebug` before opening a pull request if your
  environment supports it.

## Pull requests

1. Fork the repository and create a topic branch.
2. Keep the change focused and describe the user-visible effect.
3. Include screenshots or a short screen recording for UI changes.
4. Update `CHANGELOG.md` for user-visible changes.
5. Confirm the build and signature workflow passes.

## Licence

By contributing you agree that your contribution is licensed under the
**GNU General Public License v3.0** (see [LICENSE](LICENSE)).
