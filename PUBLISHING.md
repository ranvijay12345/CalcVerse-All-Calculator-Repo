# Publishing Zentical to the Google Play Store

A complete, start-to-finish guide for shipping this app. Follow the phases in order.
Everything here is specific to **this** project (package `com.zentical.app`).

> **Estimated time:** ~3–4 hours of active work the first time, then Google review
> takes anywhere from a few hours to ~7 days for a brand-new app/account.

---

## Phase 0 — What you need before you start

| Requirement | Notes |
|---|---|
| **Google Play Developer account** | One-time **$25 USD** fee. Sign up at <https://play.google.com/console>. New personal accounts must also complete ID + (sometimes) address verification, which can take a couple of days — do this first. |
| **Android Studio** (latest stable) | Needed to build the signed release bundle. Download: <https://developer.android.com/studio> |
| **A real AdMob account** | Free. <https://admob.google.com>. Real ad IDs are already wired into the release build (Phase 2) — you just confirm they're yours and link the AdMob app to the package. |
| **A public URL for the privacy policy** | Play requires a publicly reachable privacy-policy link (Phase 6). |
| **Store graphics** | App icon (already in project), a 1024×500 feature graphic, and 2–8 screenshots (Phase 5). |

---

## Phase 1 — Open the project and confirm it builds

1. Launch **Android Studio** → **Open** → select the folder
   `D:\AI-Projects\Smart-Calculator_Hub`.
2. Wait for **Gradle sync** to finish (it downloads all dependencies, including
   MPAndroidChart from JitPack). Fix any SDK-location prompt by installing
   **Android SDK Platform 36** via *Tools → SDK Manager*.
3. Run the unit tests to confirm the calculator engine is healthy:
   - Terminal: `./gradlew :app:testDebugUnitTest`
   - These must pass: EMI/GST/Simple-Interest, BMI, and the Scientific engine tests.
4. Run the app once on an emulator or device (**Shift+F10**) and click through a few
   calculators to confirm it launches.

---

## Phase 2 — AdMob IDs (real IDs already configured) ✅

The **release** build type in `app/build.gradle` already ships your **real** AdMob IDs;
the **debug** build type keeps Google's public **test** IDs so you can develop safely.
There is nothing to replace for the first launch — just verify the values are yours.

Current release IDs (`app/build.gradle` → `buildTypes.release`):

```groovy
manifestPlaceholders = [admobAppId: "ca-app-pub-5753738385448226~8014030382"]
resValue "string", "admob_banner_unit_id",       "ca-app-pub-5753738385448226/4013302122"
resValue "string", "admob_interstitial_unit_id", "ca-app-pub-5753738385448226/9537091543"
resValue "string", "admob_app_open_unit_id",     "ca-app-pub-5753738385448226/6700948714"
```

- The app serves **three** ad formats: banner, interstitial, and **App Open** (shown on
  warm returns to the foreground — see `util/AppOpenAdManager.java`).
- Debug builds use test IDs (`ca-app-pub-3940256099942544/...`) for all three, so you can
  tap freely without risking an invalid-traffic strike.
- In the AdMob console, make sure the app whose App ID is `~8014030382` is **linked to
  package `com.zentical.app`**, and that all three ad units exist under it.

> Don't add these strings to `strings.xml` — they're generated here via `resValue`.
> Adding them elsewhere causes a duplicate-resource build error.

**Ads consent (recommended):** if you'll have EU/UK/other-region users, set up a
consent form in AdMob (**Privacy & messaging → GDPR / US states**) so the SDK can
show the required consent prompt.

---

## Phase 3 — Bump the version (do this before every upload)

Open **`app/build.gradle`** → `defaultConfig` (**lines 16–17**):

```groovy
versionCode 1          // integer — MUST increase by at least 1 for every Play upload
versionName "1.0"      // user-visible string, e.g. "1.0"
```

For your very first upload, `versionCode 1` / `versionName "1.0"` is fine. On the
next update, use `2` / `"1.0.1"`, and so on. **Play rejects an upload whose
`versionCode` is not higher than the last one.**

---

## Phase 4 — Create a signing key and build the signed AAB ⚠️ REQUIRED

Play requires an **`.aab`** (Android App Bundle), signed with **your own** upload key.
**Guard this keystore with your life** — losing it means you can never update the app
under this key (recoverable only via Play App Signing key reset, which takes days).

### 4a. Generate the keystore (one time)

In a terminal (adjust the path to where you want to keep it — **outside** the project
folder so it's never committed):

```bash
keytool -genkeypair -v \
  -keystore D:/keys/zentical-release.jks \
  -keyalg RSA -keysize 2048 -validity 10000 \
  -alias zentical
```

It will ask for a **store password**, a **key password**, and your name/org. Write
these down somewhere safe (a password manager).

### 4b. Wire signing into the build

Create **`keystore.properties`** in the project root
(`D:\AI-Projects\Smart-Calculator_Hub\keystore.properties`):

```properties
storeFile=D:/keys/zentical-release.jks
storePassword=YOUR_STORE_PASSWORD
keyAlias=zentical
keyPassword=YOUR_KEY_PASSWORD
```

Then in **`app/build.gradle`**, inside the `android { }` block, add a `signingConfigs`
block and reference it from the `release` build type. Replace the commented-out
signing section (**lines 37–53**) with this:

```groovy
    // --- Release signing ---
    def keystorePropsFile = rootProject.file("keystore.properties")
    def keystoreProps = new Properties()
    if (keystorePropsFile.exists()) {
        keystoreProps.load(new FileInputStream(keystorePropsFile))
    }

    signingConfigs {
        release {
            if (keystorePropsFile.exists()) {
                storeFile file(keystoreProps['storeFile'])
                storePassword keystoreProps['storePassword']
                keyAlias keystoreProps['keyAlias']
                keyPassword keystoreProps['keyPassword']
            }
        }
    }

    buildTypes {
        debug {
            applicationIdSuffix ".debug"
            versionNameSuffix "-debug"
        }
        release {
            signingConfig signingConfigs.release   // <-- uncomment / add this line
            minifyEnabled true
            shrinkResources true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
```

Add these lines to **`.gitignore`** so secrets never get committed:

```
keystore.properties
*.jks
```

### 4c. Build the bundle

```bash
./gradlew clean
./gradlew :app:testDebugUnitTest     # tests must pass
./gradlew :app:bundleRelease
```

Output file:
**`app/build/outputs/bundle/release/app-release.aab`** ← this is what you upload.

> **Test the release build on a real device first** (`./gradlew :app:installRelease`
> or build a release APK). Because `minifyEnabled true` runs R8/ProGuard, a missing
> keep-rule can crash only in release. Run each calculator category once + the
> scientific calculator. The keep-rules in `app/proguard-rules.pro` already cover
> Room, ViewBinding, MPAndroidChart, and the Ads SDK.

---

## Phase 5 — Prepare your store listing assets

Google requires these before you can publish:

| Asset | Spec |
|---|---|
| **App icon** | 512×512 PNG. (You can export from the project's `ic_launcher`, or make a clean 512px version.) |
| **Feature graphic** | 1024×500 PNG/JPG (shown at the top of your listing). |
| **Phone screenshots** | 2–8 images, PNG/JPG. Capture Home, a finance calc with the EMI pie chart, the scientific calculator, and History. |
| **Short description** | ≤ 80 characters. |
| **Full description** | ≤ 4000 characters. |

Suggested copy you can reuse:

- **Title:** `Zentical - All Calculators`
- **Short description:** `62 calculators — finance, health, dates, units & a scientific calculator. Offline.`
- **Full description:** describe the 5 categories (Finance, Health, Date & Time,
  Utility, Engineering), the scientific calculator, history/favorites/search,
  PDF & CSV export, widgets, dark theme, and that **everything works offline**.

---

## Phase 6 — Host the privacy policy publicly ⚠️ REQUIRED

Play requires a publicly reachable privacy-policy URL. The content is already written
in **`PRIVACY_POLICY.md`**. Host it anywhere public and free, e.g.:

- Create a **GitHub Gist** or a repo with `PRIVACY_POLICY.md` and use the raw/Pages URL, **or**
- Use **GitHub Pages**, Google Sites, or any static host.

Copy the final URL — you'll paste it into the Play Console listing **and** it's already
referenced from the in-app Settings → Privacy policy screen.

---

## Phase 7 — Create the app in Play Console & fill the required forms

Go to <https://play.google.com/console> → **Create app**.

1. **App details:** name `Zentical`, default language, type **App**, **Free**.
2. **Set up your app** checklist — complete each of these:
   - **Privacy policy** → paste your Phase 6 URL.
   - **App access** → "All functionality available without special access" (the app
     needs no login).
   - **Ads** → **Yes, my app contains ads** (you use AdMob).
   - **Content rating** → fill the questionnaire (this app rates **Everyone**).
   - **Target audience** → 13+ (the privacy policy states no data from under-13s).
   - **Data safety** → declare what's collected. For Zentical:
     - Calculation history/favorites/preferences: **stored on device only, not shared** (not "collected" in Play's sense since it never leaves the device).
     - **AdMob** uses a **device/advertising ID** for ads → declare *Device or other IDs → collected → for Advertising*.
     - If you enable Firebase later, also declare analytics/crash data.
   - **Government apps / Financial features / Health** → answer honestly; Zentical is
     a general utility, not a regulated financial or medical app.

3. **Store listing** → paste descriptions, upload icon, feature graphic, screenshots
   from Phase 5.

---

## Phase 8 — Upload the bundle via a testing track first

**Don't go straight to production.** Use a test track to catch issues:

1. Play Console → **Testing → Internal testing → Create new release**.
2. Google will prompt you to enable **Play App Signing** — **accept it** (Google
   manages the final app-signing key; your keystore from Phase 4 becomes the *upload*
   key). This is the recommended, safest setup.
3. **Upload** `app-release.aab`.
4. Add release notes (e.g. "Initial release — 62 calculators, offline.").
5. Add your own email as an internal tester, save, and roll out to the internal track.
6. Install via the opt-in link Play gives you, and run the smoke test:
   - [ ] Each category opens and lists its calculators
   - [ ] EMI shows the pie chart; a date calculator opens the picker
   - [ ] Scientific calculator: `2^3^2 = 512`, `sin(90) = 1` (DEG)
   - [ ] Favorite / History save, edit, delete, and export (PDF + CSV) share sheet opens
   - [ ] Theme switch persists across restart
   - [ ] Widget tiles open the right screens
   - [ ] Airplane mode: everything except ads still works
   - [ ] Real banner / interstitial / App Open ads appear (confirms Phase 2 is correct)

---

## Phase 9 — Promote to Production

1. When internal testing looks good: **Production → Create new release**.
2. Reuse the **same** `.aab` (or a higher `versionCode` if you rebuilt).
3. Choose a **staged rollout** (e.g. 20%) if you want to limit blast radius.
4. Submit for review.
5. Google reviews it (hours to ~7 days for a first release). You'll get an email when
   it's live or if changes are required.

---

## Quick reference — files you will edit

| File | What to change | Phase |
|---|---|---|
| `app/build.gradle` (`buildTypes.release`) | AdMob IDs (already set — verify they're yours) | 2 |
| `app/build.gradle` (lines 16–17) | `versionCode` / `versionName` | 3 |
| `app/build.gradle` (lines 37–53) | Enable `signingConfigs` + `signingConfig` | 4 |
| `keystore.properties` (new, project root) | Keystore path + passwords | 4 |
| `.gitignore` | Ignore `keystore.properties`, `*.jks` | 4 |
| `PRIVACY_POLICY.md` | Host publicly (edit contact if desired) | 6 |

## The 3 things people forget (and get rejected/lose money for)

1. **Shipping Google's TEST ad IDs in the release build** → policy strike + $0 revenue.
   (Already handled: the release build type carries your real IDs; only *debug* uses test IDs. Don't undo that.) (Phase 2)
2. **No hosted privacy policy URL** → listing can't be submitted. (Phase 6)
3. **Not testing the R8/minified release build on a device** → app installs from Play
   but crashes on launch. Always test the *release* build, not just debug. (Phase 4c)

---

See also: **`docs/RELEASE.md`** (build/signing detail) and
**`docs/FIREBASE_SETUP.md`** (optional analytics/crash reporting).
