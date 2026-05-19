# Changelog

All notable changes to LiquidWalls are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).
Versions follow [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.0.0] — 2026-05-08

First public release.

### Added

#### Core app
- Three wallpaper engines: static image, looping video, and HTML/CSS/JS live wallpaper via a custom `WallpaperService`.
- Splash screen using Android 12+ `SplashScreen` API, back-ported to API 23 via `androidx.core:core-splashscreen`. No onboarding flow — the user lands directly on Home.
- First-launch legal consent popup (`LegalConsentScreen`) — blocks navigation with `BackHandler` until the user accepts. Acceptance stored in DataStore; never shown again after first acceptance.

#### Categories & assets
- Seven built-in wallpaper categories shipped as local assets: Nature (`c_001`), Abstract (`c_002`), City (`c_003`), Cyberpunk (`c_004`), Dark (`c_005`), Minimal (`c_006`), Space (`c_007`).
- Each category backed by `assets/category/<slug>/imageData.json` with a `categoryId` field (`c_001`–`c_007`) used for Room database filtering.
- All image paths use `.jpg` extension for consistency with the physical asset files.

#### Search
- Color search powered by `assets/data/color_names.csv` — maps 140+ English color names to sRGB hex values for fully offline color-based wallpaper discovery.

#### Store
- Cloud Store powered by Supabase (REST) + Cloudinary (CDN): browse, download, and upload wallpapers.
- 30-second cap on cloud video uploads; 60-second cap when scanning local user folders.
- Videos missing duration metadata are skipped silently.

#### Auto-changer
- Schedule wallpaper changes by interval, time-of-day window, on device unlock, on charging, on Wi-Fi connect, or on phone shake.
- Quiet hours support — auto-changer respects a configurable do-not-disturb window.
- Battery-saver mode — live wallpaper engine pauses below 20% battery.

#### Collections & folders
- Personal collections with user-defined folders.
- Backup and Restore — export/import the full library and settings as a ZIP.

#### HTML wallpaper editor
- In-app editor with live preview for creating custom HTML/CSS/JS wallpapers.

#### Legal pages
- Privacy Policy, Terms of Use, and Cookies & Identifiers policy readable in-app under Settings → About.
- All three also presented at first launch via the legal consent screen.

#### Advertising
- Google AdMob integration: banner ads inside every wallpaper grid (every 9 items) and at the bottom of Settings.
- Test ad unit IDs active in v1.0.0 — replace with real IDs via `liquidwalls.properties` before publishing.

#### AI features
- AI Hub for AI-assisted wallpaper generation (requires user-supplied API key; key stored encrypted on-device, never transmitted by Adisoft).

#### Build pipeline
- Three build scripts with three corresponding workspace workflows:
  - `build-debug-apk.sh` → `liquidwalls-1.0.0-debug.apk` (AdMob test IDs, dev phase)
  - `build-android.sh` → `liquidwalls-1.0.0.apk` (release-signed, AD_MODE switchable)
  - `build-aab.sh` → `liquidwalls-1.0.0.aab` (Play Store bundle, AD_MODE switchable)

#### Configuration
- `liquidwalls.properties` at project root: configure AdMob IDs and contact email at compile time without touching Kotlin source.
- Keystore credentials stored in `android/local.properties` (gitignored), injected into Gradle at build time.

#### Repository
- `supabase_schema.sql` — idempotent DDL for the Supabase backend.
- `LiquidWalls_PlayStore_Guide.html` — step-by-step guide to publishing on Google Play.
- `liquidwalls-complete.zip` — full project archive (source + all 3 artifacts, SDK and build caches excluded).

---

## [1.0.0 — build 2] — 2026-05-10

### Added
- **Copy Wallpaper ID** — New copy button (tag icon) on every preview screen. Works for home/built-in wallpapers AND Store downloads, so users can grab the exact wallpaper ID to include in a takedown email.
- **Rate LiquidWalls** — New "Rate LiquidWalls" item in Settings → Feedback. Opens the Play Store listing directly; falls back to the web Play Store URL if the Play app is unavailable.
- **Send Feedback** — New "Send Feedback" item in Settings → Feedback. Pre-fills an email addressed to the configured `CONTACT_EMAIL` with subject "LiquidWalls Feedback".

### Fixed / Improved
- **Splash screen** — Replaced the fully-transparent `splash_blank.xml` icon with the real `ic_launcher_foreground_scaled` drawable as `windowSplashScreenAnimatedIcon`. The app logo now appears instantly on the system-managed splash (dark background), eliminating the long black void users saw before Compose loaded — especially noticeable on first (cold) launch.
- **Takedown request** — Updated "Wallpaper name or link" requirement label and description to also mention the new Copy ID button. Updated the "How to get a wallpaper link" info card to explain both the ID copy button and the cloud-link copy button.
- **Version bump** — `versionCode` incremented to 2; version display in Settings → About updated to "LiquidWalls 1.0.0 (build 2)". `versionName` remains "1.0.0".

### Verified (no code changes required)
- **Supabase SQL schema** — All 14 columns read/written by the app (`id`, `name`, `category`, `tags`, `type`, `aspect_ratio`, `width_px`, `height_px`, `size_bytes`, `cloud_url`, `thumb_url`, `uploader`, `created_at`, `duration_ms`) are present in `store_images`, `store_videos`, and `store_web`. Schema is at version 1.0.0 and fully matches the Kotlin model classes.
- **Build flavors / AD_MODE** — Three-tier AdMob configuration confirmed correct: `debug` always injects Google test IDs; `release`/AAB reads `AD_MODE` from `liquidwalls.properties` (`beta` = test IDs, `production` = real IDs). `IS_BETA_MODE` and `BUILD_PHASE` BuildConfig fields remain accurate.
- **Room DB schema** — Six entities and five DAOs verified complete. DB schema version stays at 5; no new migrations needed for build 2 changes.
- **JSON asset versions** — `category.json` version field is `1`; individual `imageData.json` / `videoData.json` / `webData.json` files carry no version field — both correct per design.

---

## [Unreleased]

### Fixed / Improved (v1.0.0 patch — 2026-05-10)

- **API 35** — `compileSdk` and `targetSdk` raised to 35 (Android 15); `android.suppressUnsupportedCompileSdk=35` added to `gradle.properties` to silence AGP 8.5 compatibility note.
- **Splash screen flash** — `launch_background` colour corrected from `#0A0A0F` to `#06070C`, exactly matching the Compose splash screen colour. Eliminates the brief black flash on cold start.
- **Store texts** — Subtitle changed to "Community-uploaded wallpapers"; empty-state body updated to explain community-managed content; "Retry" button renamed "Retry loading".
- **Takedown request screen** — New `TakedownScreen.kt` accessible from Settings. Collects reason, authenticity statement, proof, and wallpaper name/link; sends via email.
- **Copy link on preview** — Link icon button appears in the preview overlay for Store wallpapers that have a `cloudUrl`. Copies the URL to clipboard.
- **Builtin-to-builtin collection prevention** — `CollectionPickerSheet` now filters out all built-in collections (`c.builtIn == true`) so Home, Downloads, My Webs, and category collections can never be chosen as a destination.
- **Gesture lock / resize toggle** — Preview gestures locked by default. Crop icon unlocks for free resize/pan; "Done" pill re-locks. Gestures auto-relock on every page swipe.
- **Build flavors / AD_MODE** — Three-tier AdMob ID system: `debug` always uses test IDs; `release`/AAB reads `AD_MODE` from `liquidwalls.properties` (`beta` = test IDs, `production` = real IDs). `IS_BETA_MODE` and `BUILD_PHASE` `BuildConfig` fields exposed for runtime checks.
- **DB schema v5** — `cloudUrl TEXT NOT NULL DEFAULT ''` column added to `wallpapers` table via `MIGRATION_4_5`. All entities, DAOs, and repositories updated.
- **Build workflows** — "Build Debug APK" and "Build AAB (Beta)" workflows configured. Both confirmed BUILD SUCCESSFUL: `liquidwalls-1.0.0-debug.apk` (48 MB, test IDs) and `liquidwalls-1.0.0.aab` (39 MB, AD_MODE=beta).
- **No external branding** — All third-party development-platform references removed from source, scripts, and documentation.

---

[1.0.0]: https://github.com/adisoft/liquidwalls/releases/tag/v1.0.0
[Unreleased]: https://github.com/adisoft/liquidwalls/compare/v1.0.0...HEAD
