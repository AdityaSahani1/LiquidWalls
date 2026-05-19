# LiquidWalls

**Photo, video and HTML live wallpapers for Android — with an auto-changer, scheduler, shake triggers and an offline-first library.**

LiquidWalls turns your home screen into a living gallery. Pick from a built-in store, drop in your own photos and short clips, or build a true live wallpaper from a single HTML/JavaScript file. Everything happens on-device.

- **Application ID:** `com.adisoft.liquidwalls`
- **Version:** `1.0.0` (build 1) — first public release
- **Min / Target SDK:** 24 (Android 7.0) / 35 (Android 15)
- **License:** Proprietary — © Adisoft

---

## What's in v1.0.0

- **Three wallpaper engines** — static images (any aspect ratio), short looping videos (≤ 30 s), and HTML/CSS/JS live wallpapers via a custom `WallpaperService`.
- **Smooth splash screen** using the Android 12+ `SplashScreen` API, back-ported to API 23 with `androidx.core:core-splashscreen`. Goes straight to the home screen — no onboarding flow.
- **First-launch legal consent popup** — blocks navigation until the user explicitly accepts the Privacy Policy, Terms of Use, and Cookies & Identifiers policy. Acceptance is stored locally; the popup never shows again.
- **Built-in cloud Store** powered by Supabase (REST) + Cloudinary (CDN) — anyone can upload, anyone can download, fully offline once cached.
- **Seven built-in categories** — Nature (`c_001`), Abstract (`c_002`), City (`c_003`), Cyberpunk (`c_004`), Dark (`c_005`), Minimal (`c_006`), Space (`c_007`) — each with a local `imageData.json` manifest bundled in `assets/category/<name>/`.
- **Color search** powered by `assets/data/color_names.csv` — maps English color names to hex ranges so searches like "teal" or "crimson" surface relevant wallpapers without any server round-trip.
- **Auto-changer** — schedule by interval, time-of-day, on unlock, on charging, on Wi-Fi connect, or on phone shake.
- **Quiet hours** + battery-aware live engine that pauses below 20%.
- **Personal collections + folders** synced from any local directory the user picks.
- **Full HTML wallpaper editor** with live preview.
- **Backup & Restore** — export the entire library and settings to a single ZIP.
- **Legal pages** (Privacy Policy, Terms of Use, Cookies & Identifiers) — all three are readable inside the app under Settings → About, and presented at first launch.
- **AdMob test ads** — banner row inside every wallpaper grid (every 9 items) and a banner at the bottom of Settings.

### Caps & guard-rails

| Limit | Where | Why |
|---|---|---|
| **30 s** max video duration on cloud upload | `StoreScreen.UploadDialog` | Keeps the catalogue browsable; rejects long files before any byte is uploaded. |
| **60 s** max video duration when scanning a user folder | `UserFolderRepository.scanAll` | Skips obvious non-wallpaper movies silently. |
| Skip videos missing duration metadata | both | Defensive — broken metadata = skip. |

---

## Tech stack

| Layer | Choice |
|---|---|
| Language | Kotlin 1.9.24 |
| UI | Jetpack Compose (BOM 2024.06.00), Material 3 |
| Build | Android Gradle Plugin 8.5.0, Gradle 8.7, JDK 17 |
| Storage | Room (KSP) + DataStore (Preferences) |
| Media | Coil 2 (images) + Media3/ExoPlayer (video) |
| Networking | OkHttp + manual JSON over Supabase REST |
| Cloud | Supabase Postgres + Cloudinary unsigned uploads |
| Ads | Google Mobile Ads SDK 23.2.0 (test units in v1.0.0) |
| Splash | `androidx.core:core-splashscreen` 1.0.1 |

---

## Repository layout

```
.
├── android/                          # The app
│   ├── app/
│   │   ├── src/main/
│   │   │   ├── AndroidManifest.xml
│   │   │   ├── assets/
│   │   │   │   ├── category/         # Per-category wallpaper manifests
│   │   │   │   │   ├── abstract/imageData.json
│   │   │   │   │   ├── city/imageData.json
│   │   │   │   │   ├── citycyberpunk/imageData.json
│   │   │   │   │   ├── dark/imageData.json
│   │   │   │   │   ├── minimal/imageData.json
│   │   │   │   │   ├── nature/imageData.json
│   │   │   │   │   └── space/imageData.json
│   │   │   │   ├── data/
│   │   │   │   │   └── color_names.csv   # English color → hex mapping for search
│   │   │   │   └── legal/                # privacy, terms, cookies HTML (shipped in APK)
│   │   │   ├── java/com/adisoft/liquidwalls/
│   │   │   │   ├── MainActivity.kt       # installs splash + ads
│   │   │   │   ├── data/                 # Room, repos, models
│   │   │   │   ├── ui/
│   │   │   │   │   ├── navigation/       # Routes.kt, AppNavigation.kt
│   │   │   │   │   ├── screens/
│   │   │   │   │   │   ├── SplashScreen.kt         # checks legal acceptance → HOME or LEGAL_CONSENT
│   │   │   │   │   │   ├── LegalConsentScreen.kt   # first-launch consent popup (back-blocked)
│   │   │   │   │   │   ├── LegalScreen.kt          # privacy / terms / cookies detail view
│   │   │   │   │   │   ├── HomeScreen.kt
│   │   │   │   │   │   ├── StoreScreen.kt
│   │   │   │   │   │   ├── CategoriesScreen.kt
│   │   │   │   │   │   ├── CategoryDetailScreen.kt
│   │   │   │   │   │   ├── SearchScreen.kt
│   │   │   │   │   │   ├── SettingsScreen.kt
│   │   │   │   │   │   └── … (AI, collection, folder, editor screens)
│   │   │   │   │   └── components/
│   │   │   │   ├── wallpaper/            # WallpaperService implementations
│   │   │   │   └── util/                 # PreferencesManager, ColorSearchHelper, AdsManager …
│   │   │   └── res/                      # themes.xml, drawables, mipmaps
│   │   └── build.gradle.kts              # versionCode / versionName / signing
│   ├── keystore/                         # liquidwalls.jks (gitignored creds)
│   └── build.gradle.kts
├── scripts/
│   ├── install-android-sdk.sh            # one-off: downloads cmdline-tools, platforms-35, build-tools 35
│   ├── build-debug-apk.sh                # debug build  → liquidwalls-1.0.0-debug.apk (48 MB)
│   ├── build-android.sh                  # release APK  → liquidwalls-1.0.0.apk (34 MB)
│   ├── build-release.sh                  # alias for build-android.sh
│   └── build-aab.sh                      # Play Store   → liquidwalls-1.0.0.aab (39 MB)
├── android-sdk/                          # local SDK (created by install script, not in git)
├── store-assets/                         # Play Store graphics (icon, feature graphic, HTML index)
├── docs/                                 # additional documentation
├── liquidwalls-1.0.0-debug.apk           # latest debug build (AdMob test IDs, safe to sideload)
├── liquidwalls-1.0.0.apk                 # latest signed release APK
├── liquidwalls-1.0.0.aab                 # latest Play Store bundle — upload this to Play Console
├── liquidwalls-complete.zip              # full project archive (source + all 3 artifacts, no SDK/cache)
├── liquidwalls_icon_512.png              # Play Store icon
├── liquidwalls.properties                # AdMob IDs + contact email (compile-time config)
├── supabase_schema.sql                   # full DDL: tables, indexes, RLS, migrations
├── LiquidWalls_PlayStore_Guide.html      # step-by-step guide to publishing on Play Store
├── liquidwalls-reference.html            # internal reference / feature spec
├── privacy.html                          # public mirror of in-app Privacy Policy
├── terms.html                            # public mirror of in-app Terms of Use
└── README.md                             # you are here
```

---

## Building

Three build scripts are available — run them directly or via the workspace workflow panel:

| Script | Output | Ad IDs |
|---|---|---|
| `bash scripts/build-debug-apk.sh` | `liquidwalls-<ver>-debug.apk` | **Always test** (dev phase, hardcoded) |
| `bash scripts/build-android.sh` | `liquidwalls-<ver>.apk` | Test when `AD_MODE=beta` (default) · Real when `AD_MODE=production` |
| `bash scripts/build-aab.sh` | `liquidwalls-<ver>.aab` | Test when `AD_MODE=beta` (default) · Real when `AD_MODE=production` |

Every script:
1. Bootstraps the Android SDK locally into `./android-sdk/` (idempotent — ~3 min first time, instant after).
2. Locates OpenJDK 17 in the nix store.
3. Cleans stale incremental artifacts.
4. Runs the Gradle task and copies the output to the project root.

**Ad mode switching** — edit one line in `liquidwalls.properties`:
```
AD_MODE=beta        # default — test IDs, safe for Play Console internal/closed testing
AD_MODE=production  # real IDs — use only for final production publish
```

Upload `liquidwalls-<ver>.aab` to Google Play Console. Use the debug APK for local sideloading — test IDs are always active.

---

## First-launch legal consent flow

On the very first run `SplashScreen.kt` checks `PreferencesManager.legalAccepted`. If it is `false`, navigation goes to `LegalConsentScreen` instead of `HomeScreen`.

- Back navigation is blocked on the consent screen (`BackHandler(true)`) — the user must accept to proceed.
- Tapping **Accept** calls `PreferencesManager.setLegalAccepted(true)` then navigates to `HomeScreen` with `popUpTo(SPLASH) { inclusive = true }` so the back stack is clean.
- Once accepted the flag persists in DataStore and the screen is never shown again.

The three legal documents are also reachable at any time from **Settings → About**:

| Row | Route | Content |
|---|---|---|
| Privacy policy | `Routes.legal("privacy")` | Data collection, third-party SDKs, user rights |
| Terms of use | `Routes.legal("terms")` | Usage rules, IP, liability |
| Cookies & Identifiers | `Routes.legal("cookies")` | AAID/AdMob, DataStore, SQLite, opt-out instructions |

---

## Wallpaper categories & asset structure

Each category ships its wallpapers as a JSON manifest inside the APK:

```
assets/category/<slug>/imageData.json
```

`imageData.json` is an array of objects. Every object must include a `categoryId` field (`c_001` … `c_007`) — the Room database uses this for filtering:

```json
[
  {
    "id": "c_001_img_001",
    "categoryId": "c_001",
    "title": "Forest Morning",
    "imagePath": "assets/category/nature/images/c_001_img_001.jpg",
    …
  }
]
```

The `WallpaperRepository` calls `observeByCategory(categoryId)` which matches on `categoryId` directly (and falls back to a `category LIKE` check for legacy rows with an empty `categoryId`).

---

## Color search

`SearchScreen` delegates to `ColorSearchHelper`, which loads `assets/data/color_names.csv` on first use. The CSV maps human-readable names ("teal", "crimson", "slate blue") to their sRGB hex values. When a user types a color word the helper computes the closest perceptual match and filters the wallpaper list by dominant color tag — no network round-trip required.

---

## Supabase setup

Run `supabase_schema.sql` once in the Supabase SQL Editor. It is idempotent — every `CREATE` uses `IF NOT EXISTS` and every `ALTER` is wrapped in `EXCEPTION WHEN duplicate_column THEN NULL`.

Tables created:
- `store_images` — JPEG / PNG / WebP wallpapers
- `store_videos` — MP4 / WebM short loops, with `duration_ms`
- `store_web` — HTML/CSS/JS wallpaper bundles

Row-Level Security is enabled with a `public read` + `public insert` policy (no UPDATE / DELETE).

### Environment variables expected by the app

| Key | Where it's used |
|---|---|
| `SUPABASE_URL` | `data/cloud/SupabaseClient.kt` |
| `SUPABASE_ANON_KEY` | same |
| `CLOUDINARY_CLOUD_NAME` | `data/cloud/CloudinaryClient.kt` |
| `CLOUDINARY_UNSIGNED_PRESET` | same |

### App configuration — `liquidwalls.properties`

Edit this file at the project root to change AdMob IDs or the contact email without touching Kotlin source:

```properties
CONTACT_EMAIL=adisoftcollection@gmail.com
ADMOB_APP_ID=ca-app-pub-3940256099942544~3347511713
ADMOB_BANNER_ID=ca-app-pub-3940256099942544/9214589741
ADMOB_INTERSTITIAL_ID=ca-app-pub-3940256099942544/1033173712
ADMOB_NATIVE_ID=ca-app-pub-3940256099942544/2247696110
```

Values are read by `build.gradle.kts` at compile time and exposed as `BuildConfig` fields. The AdMob App ID is also injected into `AndroidManifest.xml` via `manifestPlaceholders`.

### Keystore credentials — `android/local.properties`

The keystore password lives in `android/local.properties` (gitignored), not in `build.gradle.kts`. Add these keys to a fresh checkout:

```properties
KEYSTORE_STORE_PASSWORD=LiquidWalls2026!Secure
KEYSTORE_KEY_PASSWORD=LiquidWalls2026!Secure
```

---

## Splash screen design

`res/values/themes.xml` defines `Theme.LiquidWalls.Splash`:

```xml
<style name="Theme.LiquidWalls.Splash" parent="Theme.SplashScreen">
    <item name="windowSplashScreenBackground">@color/splash_bg</item>
    <item name="windowSplashScreenAnimatedIcon">@mipmap/ic_launcher</item>
    <item name="postSplashScreenTheme">@style/Theme.LiquidWalls</item>
</style>
```

The activity declares `android:theme="@style/Theme.LiquidWalls.Splash"` in the manifest and calls `installSplashScreen()` before `super.onCreate(...)` in `MainActivity.kt`. There is no onboarding screen — after the splash the user lands directly on the home screen (or the legal consent screen on first launch).

---

## Going live on Play Store

For everything you need — store listing copy, ASO keywords, screenshot recipe, the new-account 14-day closed-test rule, content-rating answers, data-safety form, launch-day promotion, and a final checklist — open:

> **`LiquidWalls_PlayStore_Guide.html`** in any browser.

It is a single self-contained file (no images, no JS, no network).

---

## Security notes

- **Never commit** `android/local.properties` (keystore passwords). It is in `.gitignore`.
- Back the keystore (`android/keystore/liquidwalls.jks`) up off-machine. Lose it and the only recovery is a new package name.
- The Supabase anon key is safe to ship in the APK (RLS enforces the rules) but the service-role key is not.
- Cloudinary uploads use an **unsigned** preset — lock it to a single folder + max file size in the Cloudinary dashboard.

---

© Adisoft, 2026.
