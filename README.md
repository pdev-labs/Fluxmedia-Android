# FluxMedia for Android (Beta)

[![Latest release](https://img.shields.io/github/v/release/pdev-labs/Fluxmedia-Android?label=release&style=flat-square)](https://github.com/pdev-labs/Fluxmedia-Android/releases)
[![Licence](https://img.shields.io/badge/licence-GPL--3.0-blue?style=flat-square)](LICENSE)

A native Android app for [FluxMedia](https://github.com/pdev-labs/FluxMedia),
written in **Kotlin** with **Jetpack Compose** and **Material You** dynamic
colour.

> **Version 1.0.0 is the first release and is still in beta.** Features may
> change and some sites need a JavaScript runtime that is not bundled yet.
> Please report issues on the
> [issue tracker](https://github.com/pdev-labs/Fluxmedia-Android/issues).

The app embeds the FluxMedia Python downloader with
[Chaquopy](https://chaquo.com/chaquopy/), so downloads, settings, history and
the library run entirely on the device. There is no WebView and no local HTTP
server.

## Install

Download the signed APK from the
[releases page](https://github.com/pdev-labs/Fluxmedia-Android/releases) and
install it:

```bash
adb install -r app-release.apk
```

Requires Android 7.0 (API 24) or newer and a 64-bit device (`arm64-v8a`, or
`x86_64` for emulators).

> The release key differs from the debug key, so uninstall a debug build
> before installing the signed release for the first time.

## Features

- **Download** — paste a link, analyse it, pick a quality, watch live
  progress and logs.
- **High-resolution video** — downloads the best video and audio streams and
  merges them with bundled FFmpeg (up to the source's maximum resolution,
  including VP9/AV1/MKV/WebM).
- **MP3 and FLAC extraction** — bundled FFmpeg converts audio downloads.
- **Automatic saving** — every finished download is copied to the public
  `Download/FluxMedia` folder and a toast shows the file name and final path.
- **Library** — browse, search, open, share and delete files.
- **Themes** — system, light or dark mode, Material You dynamic colour on
  Android 12+, seven accent colours, and an animation toggle.
- **Settings, Health and About** — download quality, formats, speed limit,
  filename template, embedding options, delete confirmation, log visibility,
  diagnostics and logs.

## Screens

| Screen | What it does |
| --- | --- |
| **Download** | Paste a link, analyse it, pick a quality, start a download, follow live progress/logs. |
| **Library** | Browse, search, open, share and delete downloaded files. |
| **Settings** | Appearance (theme, dynamic colour, accent, animations) plus download, post-processing, interface and update options. |
| **Health** | Run the same diagnostics as `fluxmedia --doctor`, inspect the score and read recent logs. |
| **About** | App and engine versions, beta notice, library statistics, project links and licence. |

Material You dynamic colour is used automatically on Android 12+. Older
versions fall back to a FluxMedia blue/cyan scheme. Users can turn dynamic
colour off, pick an accent, force light or dark mode, and disable animations
from **Settings → Appearance**.

## Versioning

The Android app and the Python engine are versioned separately:

| Component | Version |
| --- | --- |
| Android app | 1.0.0 (first public beta) |
| Python engine | 1.18.1 |

The About screen shows both.

## Project layout

```
android/                 Gradle project (Kotlin + Compose + Chaquopy)
src/fluxmedia/           Vendored copy of the FluxMedia Python engine
.github/workflows/       Signed release build
```

The Android Gradle config embeds `src/fluxmedia` with
`srcDir("../../src")`, so the repo is self-contained. The vendored engine is
synced from the main FluxMedia repo; see **Syncing the Python engine** below.

## Build

Requirements: JDK 17, Python 3.12 on `PATH`, Android SDK platform 35, and the
Android NDK (for FFmpeg).

```bash
cd android
./build-apk.sh              # debug
./gradlew :app:assembleRelease
```

Outputs:

```
android/app/build/outputs/apk/debug/app-debug.apk
android/app/build/outputs/apk/release/app-release.apk
```

The CI workflow builds FFmpeg once and caches it, then builds and signs the
APK.

## Signing

The release build is signed when these environment variables point at a
keystore:

| Variable | Value |
| --- | --- |
| `FLUXMEDIA_KEYSTORE_PATH` | Path to a PKCS#12 or JKS keystore |
| `FLUXMEDIA_KEYSTORE_PASSWORD` | Keystore password |
| `FLUXMEDIA_KEY_ALIAS` | Key alias (default `fluxmedia`) |
| `FLUXMEDIA_KEY_PASSWORD` | Key password |

AGP signs with **v1, v2 and v3** schemes. Without a keystore the release
build falls back to the debug key so local builds still work.

The GitHub Actions workflow reads the keystore from repository secrets:

- `FLUXMEDIA_KEYSTORE_BASE64` — base64 of the keystore file
- `FLUXMEDIA_KEYSTORE_PASSWORD`
- `FLUXMEDIA_KEY_ALIAS`
- `FLUXMEDIA_KEY_PASSWORD`

Generate a keystore with:

```bash
openssl req -x509 -newkey rsa:2048 -sha256 -days 10000 -nodes \
  -keyout key.pem -out cert.pem \
  -subj "/O=FluxMedia/OU=Android/CN=FluxMedia"
openssl pkcs12 -export -in cert.pem -inkey key.pem \
  -out release.p12 -name fluxmedia -passout pass:YOUR_PASSWORD
base64 -w0 release.p12
```

## Syncing the Python engine

The Python source lives in the main repo under `src/fluxmedia`. To pick up
changes:

```bash
rsync -a --delete ../FluxMedia/src/fluxmedia/ src/fluxmedia/
```

Then rebuild and commit.

## Storage

- **Library:** app-scoped external storage under
  `Android/data/com.fluxmedia.app/files/Download/FluxMedia` (no storage
  permission needed on Android 10+).
- **Config, history and logs:** app-private internal storage.
- **Automatic save:** every finished download is copied into the public
  `Download/FluxMedia` collection via `MediaStore` (Android 10+) or a direct
  file copy (Android 9 and older). A toast shows the final path.

Files are never uploaded anywhere.

## Limitations

- **Beta:** the app is at 1.0.0 and features may change before it is declared
  stable.
- Downloads run inside the app process; there is no background service yet.
  If Android kills the app in the background the active job stops; completed
  files remain in the library.
- Some YouTube videos need a JavaScript runtime that is not bundled.
- FFmpeg is bundled as static binaries, which makes the APK large (~68 MB
  with both arm64-v8a and x86_64). The build is cached in CI.

## FFmpeg

`android/scripts/build-ffmpeg.sh` cross-compiles FFmpeg 7.1.1 and libmp3lame
for both ABIs with the Android NDK. The CI workflow runs it once and caches
`ffmpeg-out/`; the APK build then downloads those binaries into
`android/app/src/main/jniLibs`.

The app passes the native library directory to Python, which sets
`FLUXMEDIA_FFMPEG` to `libffmpeg.so`. yt-dlp finds `libffprobe.so` beside it
automatically. FFmpeg and libmp3lame are LGPL; see `THIRD_PARTY_NOTICES.md`.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). The Python engine itself is developed
in the [main repository](https://github.com/pdev-labs/FluxMedia); Android
changes belong here.

## Licence

FluxMedia for Android is free software licensed under the
**[GNU General Public License v3.0](LICENSE)** (GPL-3.0). See
[THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md) for the LGPL FFmpeg and
libmp3lame notices.
