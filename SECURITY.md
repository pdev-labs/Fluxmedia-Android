# Security policy

## Supported versions

FluxMedia for Android is in beta. Security fixes are applied to the latest
release on the `main` branch.

| Version | Supported |
| --- | --- |
| 1.0.x (beta) | Yes |
| Older builds | No |

## Reporting a vulnerability

Please do **not** open a public issue for security problems.

Use GitHub's private vulnerability reporting:
[Security → Report a vulnerability](https://github.com/pdev-labs/Fluxmedia-Android/security/advisories/new).

Include:

- the app version and Android version,
- the affected screen or feature,
- steps to reproduce,
- the impact you believe it has.

You should receive a response within a few days. Once a fix is available it
will be released and credited unless you prefer to stay anonymous.

## Scope

In scope:

- the Kotlin app and Compose UI,
- the Chaquopy bridge and bundled Python engine,
- the signed build and release workflow.

Out of scope:

- vulnerabilities in upstream projects (yt-dlp, FFmpeg, Chaquopy) that do not
  affect this app's usage — report those upstream,
- issues that require a rooted device or a modified APK,
- denial of service caused by intentionally huge media files.
