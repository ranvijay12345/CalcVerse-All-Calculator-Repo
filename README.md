# Calcverse — Smart Calculator Hub

A production-ready, **offline-first** Android calculator suite bundling **55 calculators** across
5 categories, plus a full scientific calculator. Built in **Java** with **MVVM + Clean
Architecture**, **Material Design 3**, **Room**, **ViewBinding**, **Navigation Component**, and a
dark/light/system theme engine. Minimum **Android 10 (API 29)**.

> Originally specified as "Smart Calculator Hub." Published under the name **Calcverse**
> (`com.calcverse.app`) to avoid a name clash on Google Play.

---

## Features

### Calculators (55)

| Category | Count | Examples |
|----------|-------|----------|
| **Finance** | 15 | EMI (with principal/interest pie chart), SIP, FD, RD, Loan Eligibility, Home/Car/Personal Loan, Compound & Simple Interest, GST (5/12/18/28%), Profit Margin, Break-Even, ROI, Currency Converter (offline static rates) |
| **Health** | 10 | BMI (+ category), BMR, Body Fat, Ideal Weight, Daily Calorie, Water Intake, Pregnancy Due Date, Heart Rate, Calories Burned, Healthy Weight Range |
| **Date & Time** | 8 | Age, Date Difference, Countdown, Workdays, Days Between, Anniversary, Retirement Age, Next Birthday |
| **Utility** | 10 | Percentage, Discount, Tip, Split Bill, Ratio, Average, Speed, Fuel Cost, Time, Unit Converter |
| **Engineering** | 12 | Binary/Hex/Decimal/Number-Base, Data Size, Ohm's Law, Power, Voltage, Current, Resistance, Percentage Error, **Scientific Calculator** |

### App-level

- **Home** hub: search entry, category grid, favorites rail, recently-used rail, most-popular rail
- **Search** across names/keywords, **Favorites**, **History** (save / edit / delete / clear)
- **Share** (plain text) and **Export** (PDF + CSV) — dependency-free PDF writer
- **Voice input** for numeric fields (device speech recognizer, optional)
- **Home-screen widget** with quick links (EMI / GST / BMI / Scientific)
- **Themes**: light / dark / system, persisted
- **Play Store ready**: AdMob banner + interstitial, In-App Review, bundled privacy policy,
  analytics/crash hooks behind an interface

---

## Architecture

Single-activity, data-driven MVVM. See [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) and
[`docs/FEATURE_FLOW.md`](docs/FEATURE_FLOW.md) for diagrams.

```
com.calcverse.app
├── domain            # Pure business logic — no Android deps, fully unit-tested
│   ├── model         #   CalculatorSpec, InputField, InputValues, CalcResult, Computation
│   └── calc          #   *Calculators (Finance/Health/Date/Utility/Engineering),
│                     #   ScientificEngine, CalculatorRegistry
├── data              # Repository pattern over Room
│   ├── local         #   entities, DAOs, AppDatabase
│   └── repository    #   Favorite/History/Recent repositories
├── core              # SettingsManager, analytics interface, manual DI (AppContainer)
├── ui                # MVVM: Fragments + ViewModels + adapters (ViewBinding)
└── util              # Share/Export, MinimalPdf, RateUs, AdBanner helpers
```

**Key idea — one generic calculator screen.** Every non-scientific calculator is a declarative
`CalculatorSpec`: a list of typed `InputField`s plus a pure `Computation` returning a
`CalcResult`. A single `CalculatorFragment` renders any spec's form, runs the computation off the
UI thread via its `ViewModel`, and hands the result to `ResultRenderer` (rows + optional pie
chart). Adding a calculator = adding one spec; no new UI code.

---

## Tech stack

- Java 17, AGP 8.5.2, Gradle 8.9, compileSdk 34, minSdk 29, targetSdk 34
- AndroidX, Material Components 1.12, Navigation Component, Lifecycle/LiveData
- Room (SQLite), ViewBinding
- MPAndroidChart (pie charts), Play Services Ads (AdMob), Play In-App Review
- JUnit 4 unit tests for the calculator engine

---

## Building

1. Open the project root in **Android Studio** (Koala or newer).
2. Let Gradle sync (fetches AndroidX, Material, MPAndroidChart via JitPack, Play libraries).
3. Run the `app` configuration on an emulator/device running Android 10+.

```bash
./gradlew :app:assembleDebug     # build APK
./gradlew :app:testDebugUnitTest # run engine unit tests
./gradlew :app:bundleRelease     # build AAB for Play (configure signing first)
```

### Release signing

Create `keystore.properties` (git-ignored) and reference it in `app/build.gradle`'s
`signingConfigs` before running `bundleRelease`. See [`docs/RELEASE.md`](docs/RELEASE.md).

---

## Play Store checklist

- [ ] Replace AdMob test IDs in `app/build.gradle` (`admobAppId`, banner/interstitial unit IDs)
- [ ] Add real Firebase `google-services.json` and enable Analytics/Crashlytics (see
      [`docs/FIREBASE_SETUP.md`](docs/FIREBASE_SETUP.md))
- [ ] Configure release signing
- [ ] Confirm the bundled privacy policy and host a public copy (see [`PRIVACY_POLICY.md`](PRIVACY_POLICY.md))
- [ ] Provide store listing assets (icon is generated adaptively; add feature graphic + screenshots)

---

## Testing

Pure-JVM unit tests live in `app/src/test/`:

- `ScientificEngineTest` — precedence, right-associative power, trig (deg/rad), log/ln/sqrt,
  factorial, error handling
- `FinanceCalculatorsTest` — EMI (standard amortization formula + zero-interest), simple interest, GST
- `HealthCalculatorsTest` — BMI across ranges

These run against the **shipped** `CalculatorRegistry` specs, so they exercise exactly the code
that ships.

---

## License

Proprietary — all rights reserved. Replace with your chosen license before distribution.
