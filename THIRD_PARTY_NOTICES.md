# Third-party notices

FluxMedia Android bundles the following third-party components.

## FFmpeg

- Version: 7.1.1
- Licence: LGPL-2.1-or-later (built without `--enable-gpl`)
- Source: https://ffmpeg.org/download.html
- Build script: [`android/scripts/build-ffmpeg.sh`](android/scripts/build-ffmpeg.sh)

FFmpeg is cross-compiled as static `libffmpeg.so` and `libffprobe.so` for
arm64-v8a and x86_64.

## LAME (libmp3lame)

- Version: 3.100
- Licence: LGPL-2.1
- Source: https://lame.sourceforge.io/

libmp3lame is statically linked into the bundled FFmpeg to provide MP3
encoding.

## AndroidX / Jetpack Compose

- Licence: Apache License 2.0
- Source: https://developer.android.com/jetpack

## Chaquopy

- Licence: MIT
- Source: https://chaquo.com/chaquopy/

## yt-dlp and Python dependencies

- Licences: see the individual packages (Unlicense, MIT, BSD, Apache 2.0)
- Sources: https://github.com/yt-dlp/yt-dlp and PyPI

The full licence texts are available from the upstream projects linked above.
