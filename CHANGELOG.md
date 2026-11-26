# Changelog

All notable changes to FluxMedia for Android are documented here. The format
follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) and the
project uses [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] — 2026-11-26 · Beta

First public Android release.

### Added

- Native Kotlin + Jetpack Compose app with Material You dynamic colour.
- Chaquopy bridge that calls the FluxMedia Python service layer directly,
  with no local HTTP server and no WebView.
- Download screen with link analysis, quality picker, live progress, speed,
  ETA and expandable yt-dlp logs.
- Bundled FFmpeg 7.1.1 + libmp3lame for `arm64-v8a` and `x86_64`:
  MP3/FLAC extraction, high-resolution video+audio merging (VP9/AV1/MKV/WebM)
  and subtitle/thumbnail/metadata embedding.
- Automatic saving to `Download/FluxMedia` with a toast showing the final
  path, plus working Open and Share actions.
- Library with search, open, share and delete.
- Appearance settings: system/light/dark theme, dynamic colour toggle, seven
  accent colours, animation toggle and reset.
- Interface settings: delete confirmation and download-log visibility.
- Settings for quality, formats, speed limit, filename template, embedding,
  history and update checks.
- Health screen with the same diagnostics as `fluxmedia --doctor`, and an
  About screen with app and engine versions.
- Signed release builds (APK Signature Scheme v1, v2 and v3).
- Material You screen transitions, animated progress bars and expanding
  cards.

### Known limitations

- Still in beta; features may change.
- No background download service yet.
- Some YouTube videos need a JavaScript runtime that is not bundled.

[1.0.0]: https://github.com/pdev-labs/Fluxmedia-Android/releases/tag/android-v1.0.0
