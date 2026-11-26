# FluxMedia Android

A native Android app for [FluxMedia](https://github.com/pdev-labs/FluxMedia),
written in **Kotlin** with **Jetpack Compose** and **Material You** dynamic
colour.

The app embeds the FluxMedia Python downloader with
[Chaquopy](https://chaquo.com/chaquopy/), so downloads, settings, history and
the library run entirely on the device. There is no WebView and no local HTTP
server.

## Features

- **Download** — paste a link, analyse it, pick a quality, watch live
  progress and logs.
- **High-resolution video** — downloads H.264 video and AAC audio separately
  and remuxes them on-device with Android's `MediaMuxer`. Typically up to
  1080p without bundling FFmpeg.
- **Automatic saving** — every finished download is copied to the public
  `Download/FluxMedia` folder and a toast shows the file name and final path.
- **Library** — browse, search, open, share and delete files.
- **Settings, Health and About** — the same configuration, diagnostics and
  logs as the desktop app.
- **Material You** — dynamic colour on Android 12+, blue/cyan fallback below.

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

Requirements: JDK 17, Python 3.12 on `PATH`, and Android SDK platform 35.

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

> The release key differs from the debug key, so uninstall a debug build
> before installing the signed release for the first time.

## Syncing the Python engine

The Python source lives in the main repo under `src/fluxmedia`. To pick up
changes:

```bash
rsync -a --delete ../FluxMedia/src/fluxmedia/ src/fluxmedia/
```

Then rebuild and commit.

## Limitations

- FFmpeg is not bundled. MP3/FLAC extraction still needs a desktop build.
  VP9/AV1-only streams (e.g. 1440p/4K on YouTube) fall back to the best
  pre-muxed stream.
- Downloads run inside the app process; there is no background service yet.
- Some YouTube videos need a JavaScript runtime that is not bundled.

## Licence

See [LICENSE](LICENSE).
