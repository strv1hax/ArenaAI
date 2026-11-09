# Changelog

All releases are signed Android APKs. This repository publishes release binaries and documentation only; it does not contain source code.

Version names are user-facing labels. `versionCode` is the internal Android update number and is not reset when the visible version name changes.

---

## v1.0.0 — current release

- `versionName`: `1.0.0`
- `versionCode`: `10`
- Release asset: `ArenaAi.apk`
- SHA-256: `0a08da4022a03f87cfc3a8576b11068c9d498c7feb9eb1e50dc14928ec03d7d7`

### Fixed
- Normal-mode chat switching no longer re-injects the WebView enhancement payload on every SPA route change.
- Removed the global `window.open` override and `target="_blank"` stripping.
- Removed forced synchronous layout and `tab-anim` re-triggering from route hooks.
- Floating WebView sheet now closes on route changes so it cannot cover new-chat controls.
- `setAttribute` interception and head/viewport observers are now Desktop Mode only.
- Added a `booted` guard and single `onPageFinished` injection.

### Optimized
- `LAYER_TYPE_NONE` instead of a forced full-WebView hardware layer.
- Idempotent `applyDesktopMode()`; no repeated UA/zoom settings or six `zoomOut()` calls on chat switches.
- DOM scan cap `220 → 96`, total-iteration bound, guarded `data-neu-ui` writes.
- Direct-children-only body observer, 260 ms click-sync throttle, hidden-tab / `UI: OFF` early exits.
- Removed perpetual animations, `filter: brightness()` keyframes, `will-change`, paint-heavy transitions, and `both` fill-mode animations on third-party DOM.
- Native theme CSS patched in both shipped `.so` files; runtime CSS sanitizer added.
- Kept transform/opacity-only finite motion, `prefers-reduced-motion`, `content-visibility`, and `contain`.

### Preserved
- Exact orange accent family and black layered-depth base.
- `ARENA STUDIO` header with no colour-name status.
- WebView ON/OFF row exactly `UI`; local toggle `UI`; toast `UI: ON` / `UI: OFF`.
- `Launch Arena` as the only main launch-button text.
- Desktop Mode 1280 px spoofing, route changes, blob/data download-name capture, file chooser, and conservative startup path.
- v1/v2/v3 signing, `zipalign -p` page alignment, stored native libs.

### Support
- **Only arm64 devices are supported** (`arm64-v8a`, Android 7.0+ / API 24). A legacy `armeabi-v7a` library is bundled but is not part of the supported configuration.
- Static validation only; no device runtime test was available in the build environment.

---

## 1.0.8 — orange accent correction

- `versionCode`: `9`
- Replaced the rejected hue-rotated accents with the exact original orange family: `#D97757`, `#E08364`, `#B85A3C`, `#E58D6E`, matching RGBA tokens, and HSL `15 63% 60%`.
- Removed the colour-name status from the `ARENA STUDIO` header.
- Changed the WebView ON/OFF row to exactly `UI`.
- Changed the local toggle to `UI` and the toast to `UI: ON` / `UI: OFF`.
- Renamed internal motion keyframes to neutral accent names.
- Kept `Launch Arena` as the only main launch-button text.
- Kept black layered-depth design, finite transform/opacity motion, reduced-motion support, `content-visibility`/`contain`, Desktop Mode, route changes, blob/data download capture, file chooser, and the conservative startup path.
- Signed, page-aligned, statically validated only.

---

## 1.0.7 — rejected green accent build

- `versionCode`: `8`
- Added smooth animations, stronger WebView lag work, and a hue-rotated accent set.
- User rejected that accent colour and the visible colour-name wording.
- Superseded by 1.0.8; do not use.

---

## 1.0.6 — conservative startup path

- `versionCode`: `7`
- Rolled back the 1.0.5 early legacy-storage permission request, MediaStore helpers, and large native helper set.
- Restored the last known-working startup path.
- Clean manifest, no legacy storage flag.
- Signed, page-aligned, statically validated only. Launch success still required device feedback.

---

## 1.0.5 — crashing build

- `versionCode`: `6`
- Added layered-depth CSS, native helpers, startup/storage/MediaStore changes, and download-name helpers.
- User reported a crash on open.
- No `adb logcat` or root cause was captured; the build was rolled back in 1.0.6. Do not use.

---

## 1.0.4 — rejected flat neon-green design

- `versionCode`: `5`
- Flat black and a neon-green accent with removed shadows.
- User rejected the flat green look; the black layered-depth base was restored in later builds.

---

## 1.0.0 – 1.0.3 — early builds

- Initial Arena AI WebView client builds.
- `versionCode` values `1`–`4`.
- Established the local hub, WebView bridge, Desktop Mode, download handling, and the original orange/black theme direction.

---

## Notes

- The current `1.0.0` release intentionally reuses the visible version name `1.0.0` while using `versionCode 10`, so Android installs it as an update over `1.0.8`.
- Every release in this repository is statically validated only. Device runtime behaviour must be confirmed on a supported arm64 Android device.
- No source code, smali, native sources, build scripts, or signing keys are published here.
