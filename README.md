# Arena AI — Android app

> **Distribution and documentation repository only.** No source code, smali, native sources, build scripts, or keystores are published here.

Arena AI is a lightweight Android WebView client for [Arena.ai](https://lmarena.ai/) / LMArena, wrapped in a custom local hub with a black layered-depth design and an exact orange accent palette. It adds Desktop Mode, a UI toggle, download-name capture, a file chooser, and a set of WebView route/performance fixes.

**Current release:** `v1.0.0` · **Release asset:** `ArenaAi.apk`

---

## ⚠️ Supported devices

**Only arm64 devices are supported.**

| Item | Detail |
| --- | --- |
| Supported ABI | `arm64-v8a` (64-bit ARM) |
| Minimum Android | Android 7.0 / API 24 |
| Target Android | API 34 |
| Not supported | 32-bit-only ARM devices, `x86`, `x86_64` |
| Note | The APK also bundles a legacy `armeabi-v7a` native library for compatibility, but official support, testing, and troubleshooting are limited to 64-bit ARM devices. |

Install this APK on an arm64 Android phone/tablet. Do not expect support on 32-bit-only or Intel/AMD Android devices.

---

## Quick facts

| Field | Value |
| --- | --- |
| App label | `Arena AI` |
| Package | `com.arenaai.neumorphic` |
| `versionName` | `1.0.0` |
| `versionCode` | `10` |
| Release tag | `v1.0.0` |
| APK file in Releases | `ArenaAi.apk` |
| APK size | 190,615 bytes |
| APK SHA-256 | `0a08da4022a03f87cfc3a8576b11068c9d498c7feb9eb1e50dc14928ec03d7d7` |
| Signing | v1 / v2 / v3 APK signatures |
| Alignment | `zipalign -p` 4-byte + page-aligned stored native libs |
| Build type | Signed release APK |
| Source code | **Not included in this repository** |

`versionCode 10` is higher than the earlier `1.0.8` build's code `9`, so the app installs as an update while displaying the requested version name `1.0.0`. You do not need to uninstall first.

---

## Table of contents

- [What the app does](#what-the-app-does)
- [Main features](#main-features)
- [v1.0.0 changes](#v100-changes)
- [Design and wording](#design-and-wording)
- [Desktop Mode vs normal mode](#desktop-mode-vs-normal-mode)
- [Downloads and files](#downloads-and-files)
- [Permissions and WebView settings](#permissions-and-webview-settings)
- [Installation](#installation)
- [Validation performed](#validation-performed)
- [Known limitations](#known-limitations)
- [Version history](#version-history)
- [Repository contents](#repository-contents)
- [Support / bug reports](#support--bug-reports)
- [Disclaimer](#disclaimer)

---

## What the app does

1. Opens a local hub screen (`file:///android_asset/index.html`) with:
   - **Launch Arena** — opens the Arena.ai / LMArena site;
   - a prompt vault and notes editor;
   - mode tiles for Direct / Agent, Battle 1v1, and Leaderboard;
   - **Desktop Mode** and **UI** toggles.
2. Loads the remote site inside an Android `WebView`.
3. Injects a small enhancement layer that themes the page, handles viewport/DesktopMode spoofing, keeps route changes responsive, captures download names, and improves performance.
4. Uses a tiny native library for the theme CSS, engine info, and prompt-batch filtering.

The app is a WebView wrapper, not a native Arena.ai client. It requires a working internet connection and a current Android System WebView.

---

## Main features

- **Local hub**: prompt vault, notes, seed prompts, copy actions, mode shortcuts, and a clean launch button.
- **Exact orange accent palette**: `#D97757`, `#E08364`, `#B85A3C`, `#E58D6E`, matching RGBA tokens, and HSL `15 63% 60%`; danger red `#E96A6A`.
- **Black layered-depth base**: `#0D0D0F`, `#151519`, `#08080A`, `#F4F2ED`, `#9E9B95`.
- **Finite, transform/opacity-only motion** with `prefers-reduced-motion` support.
- **Desktop Mode**: 1280 px viewport spoofing, desktop user agent, zoom controls, `matchMedia` shims, and `onTabChanged` handling.
- **Normal mode**: mobile user agent and a lightweight route-stability path.
- **Route changes**: `pushState`, `replaceState`, `popstate`, and `hashchange` hooks.
- **Blob/data download capture**: clicked filename capture, `HTMLAnchorElement.prototype.click` guard, `fetch`/`FileReader` bridge, and local magic-byte filename resolver.
- **File chooser**: `ACTION_GET_CONTENT` with `CATEGORY_OPENABLE` and `*/*` MIME.
- **Download handling**: `DownloadManager` for HTTP files and `saveBase64FileToDownloads` for data/blob URLs.
- **Performance layer**: `content-visibility`, `contain`, `WeakSet`, rAF, `requestIdleCallback`, throttled observers, and guarded DOM writes.
- **Conservative startup path**: no legacy external-storage flag, no early permission request, and no MediaStore helper set.
- **Native core**: three JNI exports per ABI with no `NEEDED` dependencies.

---

## v1.0.0 changes

### Fixed: normal-mode chat switching
Earlier builds could land on a new-chat page with unresponsive buttons when switching chats in normal mode. v1.0.0 removes the likely causes:

- Enhancements are injected **once per document**, from `onPageFinished` only.
- `onPageStarted` and `doUpdateVisitedHistory` no longer re-inject the payload on SPA route changes.
- The global `window.open` override and `target="_blank"` stripping were removed.
- `Element.prototype.setAttribute` interception and the head/viewport observer are Desktop Mode only.
- Route hooks no longer force synchronous layout or re-trigger `tab-anim`; the floating sheet closes on every route change.
- A `booted` guard makes repeated `evaluateJavascript` a no-op.
- `applyDesktopMode()` is idempotent, so chat switches no longer repeat UA/zoom settings or six `zoomOut()` calls.

### Performance / optimization
- WebView uses `LAYER_TYPE_NONE` instead of a forced full hardware layer.
- DOM scan cap reduced from `220` to `96`, with an iteration bound and guarded `data-neu-ui` writes.
- Body `MutationObserver` is direct-children-only; click-driven sync is throttled; hidden tabs and `UI: OFF` exit early.
- Removed perpetual animations, `filter: brightness()` keyframes, `will-change`, paint-heavy transitions, and `both` fill-mode animations on third-party DOM.
- Native theme CSS in both shipped `.so` files was patched in place; runtime JS also sanitizes the theme CSS.
- Kept transform/opacity-only finite motion, reduced-motion support, `content-visibility`, and `contain`.

### Release
- `versionName` set to exactly `1.0.0`.
- `versionCode` raised to `10` for installability as an update.
- Signed, page-aligned, and statically validated.

Full technical notes are kept outside this repository; this README summarizes everything relevant to installing and using the app.

---

## Design and wording

- The WebView sheet header shows only `ARENA STUDIO` — no colour-name status.
- The WebView ON/OFF row label is exactly `UI`.
- The local toggle is `UI`; the toast is `UI: ON` / `UI: OFF`.
- `Launch Arena` is the only text on the main launch button.
- The seed prompt is `Arena Studio UI System`.
- The app UI contains no rejected accent colours and no earlier colour-name or theme-name labels.
- The black layered-depth styling and depth shadows are preserved.

---

## Desktop Mode vs normal mode

| Mode | Behaviour |
| --- | --- |
| **Normal / mobile** | Mobile user agent, standard viewport, lightweight enhancement layer. This is the mode where chat-switch stability was fixed in v1.0.0. |
| **Desktop Mode** | Desktop user agent, 1280 px viewport spoofing, `innerWidth`/`outerWidth`/`screen.width`/`matchMedia` shims, viewport meta enforcement, zoom controls, and `onTabChanged` bridge calls. |

Desktop Mode is available from the local hub and from the floating WebView sheet. Toggling it saves the preference and reloads the page.

---

## Downloads and files

- The app captures a friendly filename when a blob/data download is clicked.
- Detached blob anchors are handled with a guarded `HTMLAnchorElement.prototype.click` override.
- Data URLs and blob URLs are converted through `fetch` + `FileReader` and passed to `ArenaBridge.saveBase64FileToDownloads`.
- HTTP downloads go through Android's `DownloadManager` with cookies and a `.bin` → `.dat` fallback.
- A local magic-byte resolver detects APK, ZIP, PDF, PNG, JPG, GIF, DOCX, XLSX, and PPTX headers.
- The file chooser uses `ACTION_GET_CONTENT`, `CATEGORY_OPENABLE`, `*/*`, and `EXTRA_ALLOW_MULTIPLE`.

---

## Permissions and WebView settings

Declared permissions:

- `android.permission.INTERNET`
- `android.permission.ACCESS_NETWORK_STATE`
- `android.permission.READ_EXTERNAL_STORAGE` (`maxSdkVersion 32`)
- `android.permission.WRITE_EXTERNAL_STORAGE` (`maxSdkVersion 28`)

WebView configuration highlights:

- JavaScript, DOM storage, database, file access, and content access enabled.
- `setJavaScriptCanOpenWindowsAutomatically(true)`, `setSupportMultipleWindows(false)`.
- `setMixedContentMode(MIXED_CONTENT_COMPATIBILITY_MODE)`, `setTextZoom(100)`.
- Cookies and third-party cookies accepted.
- `ArenaBridge` JavaScript interface exposed to the page.
- `usesCleartextTraffic=true` in the manifest.
- `LAYER_TYPE_NONE` for the WebView.

No `requestLegacyExternalStorage` flag is present.

---

## Installation

1. Go to [Releases](https://github.com/strv1hax/ArenaAI/releases).
2. Download **`ArenaAi.apk`** from release `v1.0.0`.
3. Verify the SHA-256:
   ```
   0a08da4022a03f87cfc3a8576b11068c9d498c7feb9eb1e50dc14928ec03d7d7
   ```
4. On an **arm64 Android 7.0+** device, allow installation from unknown sources for the browser/file manager you use.
5. Install the APK.
6. If updating from the earlier `1.0.8` build, install directly over it — `versionCode 10` is treated as an update.

There is no Play Store listing and no in-app auto-updater. New builds are published as GitHub releases.

---

## Validation performed

The APK was **statically validated** before publication:

- `smali assemble -a 34` and `baksmali` round-trip.
- `node --check` on the extracted WebView JavaScript and the local HTML script.
- C++ syntax check on the native source reference; native ELF checks with no `NEEDED` and three JNI exports per ABI.
- Packaged `classes.dex`, `assets/index.html`, and both `.so` hashes matched the build tree.
- Final token scan: no forbidden green/accent/wording/legacy-storage tokens; all required orange, `ARENA STUDIO`, `UI`, `Launch Arena`, reduced-motion, `content-visibility`, `contain`, download, bridge, and file-chooser tokens present.
- `aapt dump badging`: `versionCode='10'`, `versionName='1.0.0'`, minSdk 24, targetSdk 34, label `Arena AI`.
- `apksigner verify` with min-sdk 21: v1 / v2 / v3 true, one signer.
- `zipalign -c -p 4` passed; stored native-lib data offsets 131072 and 163840, both page-aligned.

**No Android device, emulator, or `adb logcat` was available in the build environment.** Launch success, the normal-mode chat-switch fix, and the performance gain are not device-proven by these static checks.

---

## Known limitations

- **arm64 only** is the supported configuration; see [Supported devices](#supported-devices).
- Requires an internet connection and a compatible Android System WebView.
- Arena.ai / LMArena is a third-party website; its UI, routes, and features can change without this APK changing.
- The app uses a JavaScript bridge and mixed-content compatibility mode; install only from this release and verify the APK hash.
- No source code or build toolchain is published here.
- No runtime device validation was performed by the publisher's build environment.

---

## Version history

See [CHANGELOG.md](CHANGELOG.md) for the full version-by-version history, including the orange accent correction, the rejected flat neon-green design, the rolled-back crashing build, and the conservative startup path.

Current release: **v1.0.0** — normal-mode chat-switch fix, performance optimization, and `1.0.0` release.

---

## Repository contents

| Path | Purpose |
| --- | --- |
| `README.md` | This document — all app information, install steps, support policy, and limitations. |
| `CHANGELOG.md` | Version history and change summary. |
| [Releases](https://github.com/strv1hax/ArenaAI/releases) | Signed APK assets. `v1.0.0` contains `ArenaAi.apk`. |

There are intentionally **no source files** in this repository.

---

## Support / bug reports

Use [GitHub Issues](https://github.com/strv1hax/ArenaAI/issues) for bug reports.

If normal-mode chat switching still fails after installing v1.0.0, include:

- `adb logcat` captured while reproducing the issue;
- Android version;
- ColorOS / device UI version, if applicable;
- whether Desktop Mode was ON or OFF;
- the exact steps that lead to the unresponsive screen.

That information is needed to distinguish a WebView renderer issue, an Arena.ai SPA route issue, or a remaining injected-script interaction.

---

## Disclaimer

Arena AI is an independent WebView client. It is not affiliated with, endorsed by, or officially connected to Arena.ai, LMArena, Anthropic, or any model provider. All trademarks belong to their respective owners.

This repository grants **no open-source license** because it contains no source code. The APK is provided as-is, without warranty. Verify the SHA-256 before installing.
