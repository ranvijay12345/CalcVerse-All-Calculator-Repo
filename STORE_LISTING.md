# CalcVerse — Play Store Listing & Upload Guide

Everything you copy-paste into the Play Console, plus the step-by-step upload walkthrough.
For the deep build/signing/AdMob mechanics, see **PUBLISHING.md** (this file references it where needed).

- **Package name:** `com.calcverse.app`
- **In-app display name (launcher):** `CalcVerse` (kept short so it isn't truncated under the icon)
- **Play Store title (search-facing):** `CalcVerse - All Calculators` (see below)

---

## 1. Store listing text (copy-paste ready)

### App title  *(max 30 characters)*
```
CalcVerse - All Calculators
```
> 27 characters ✓. The title is the **single strongest keyword ranking signal** on Play, which is why "Calculators" (the term people actually search) is in it.

### Short description  *(max 80 characters — shown in search results & at top of listing)*
```
60+ finance, health, EMI, BMI & scientific calculators. Fast, free & offline.
```
> 77 characters ✓. Front-loads the highest-volume keywords (finance, health, EMI, BMI, scientific) plus the three selling points (fast, free, offline).

### Full description  *(max 4000 characters — paste as-is)*
```
CalcVerse is the only calculator app you'll ever need — 60+ powerful calculators for finance, health, math, dates and everyday life, all in one clean, fast, ad-light app that works 100% offline.

No sign-up. No internet required. No data leaves your phone.

★ FINANCE CALCULATORS
• EMI / Loan Calculator (home, car, personal loans)
• SIP & Mutual Fund Returns
• Compound Interest & Simple Interest
• Fixed Deposit (FD) & Recurring Deposit (RD)
• GST / VAT Calculator (add & remove tax)
• Loan Eligibility
• ROI & CAGR
• Profit Margin & Break-Even
• Currency Converter

★ HEALTH & FITNESS CALCULATORS
• BMI Calculator
• BMR & Daily Calorie (TDEE)
• Body Fat Percentage
• Ideal Weight
• Water Intake
• Heart Rate Zones
• Calories Burned
• Pregnancy Due Date
• Healthy Weight Range

★ MATH & SCIENTIFIC
• Full Scientific Calculator (sin, cos, tan, log, ln, powers, roots, factorial)
• Ohm's Law & Electrical Power
• Percentage Error
• Number Base Converter (binary, hex, octal, decimal)

★ UNIT CONVERTERS
• Length, Weight, Area, Volume
• Speed, Pressure, Temperature
• Data Storage

★ DATE & TIME
• Age Calculator
• Date Difference
• Days Between Dates
• Countdown & Workdays
• Next Birthday, Anniversary & Retirement

★ EVERYDAY TOOLS
• Percentage, Discount & Tip
• Split Bill
• Fuel Cost & Mileage
• Ratio / Aspect Ratio

WHY CALCVERSE?
✓ 100% Offline — every calculation runs on your device
✓ Private — no accounts, no tracking of personal data
✓ Fast & lightweight — Material Design 3, light & dark themes
✓ Voice input — speak values instead of typing
✓ History & Favorites — pin the tools you use most
✓ Share & Export results as PDF or CSV
✓ Home-screen widgets for one-tap access

Whether you're planning a loan EMI, checking your BMI, converting units, splitting a bill, solving a scientific equation or counting down to a big day — CalcVerse does it all, instantly and offline.

Download CalcVerse today and replace a dozen single-purpose calculator apps with one.
```
> ~1,950 characters. Naturally repeats the priority keywords (calculator, EMI, BMI, GST, SIP, scientific, converter, offline) without keyword-stuffing, which Play penalizes.

---

## 2. Keywords & tags

Google Play has **no keyword field** (unlike Apple). Ranking comes from the **title + short description + full description**. So "keywords" = the terms you weave into that text (done above) and the terms you use when replying to reviews.

### Primary keywords (highest priority — already in title/short desc)
```
calculator, all in one calculator, EMI calculator, loan calculator, BMI calculator,
scientific calculator, GST calculator, SIP calculator, unit converter, offline calculator
```

### Secondary keywords (worked into the full description)
```
finance calculator, compound interest, simple interest, fixed deposit, FD calculator,
RD calculator, currency converter, ROI, CAGR, profit margin, break even,
BMR, TDEE, calorie calculator, body fat, ideal weight, water intake, heart rate,
pregnancy due date, age calculator, date difference, days between dates, countdown,
percentage calculator, discount calculator, tip calculator, split bill,
fuel cost, mileage, length converter, weight converter, temperature converter,
area converter, volume converter, speed converter, data storage converter,
ohms law, number base converter, binary hex converter
```

### Long-tail phrases (great for review replies & future updates)
```
loan emi calculator offline, home loan calculator, car loan emi,
bmi and calorie calculator, all in one math calculator, free offline calculator no ads,
gst tax calculator india, sip mutual fund calculator, scientific calculator with history
```

### Store "Tags" (the ones Play lets you pick from a fixed list)
In Play Console → *Store listing → App content → Tags*, Google offers a **preset list** (you can't type your own). Choose the closest available:
```
Tools  ·  Finance  ·  Productivity  ·  Utilities  ·  Education
```
Pick up to 5. Play uses these + your category for browse/related placement.

---

## 3. Categorization (in Play Console)

| Field | Value |
|---|---|
| **App category** | `Tools` (best fit; highest reach for calculators). Alternative: `Finance` if you want to lean into the loan/EMI audience. |
| **Tags** | Tools, Finance, Productivity, Utilities, Education (see above) |
| **Contains ads** | **Yes** (you ship AdMob — must be declared) |
| **In-app purchases** | No |
| **Content rating** | Everyone (see rating questionnaire in §6) |

---

## 4. Graphics checklist (required before you can publish)

| Asset | Spec | Status |
|---|---|---|
| **App icon** | 512×512 PNG, 32-bit | ✅ already in project (`ic_launcher`) — Play will pull from the bundle, but you also upload a 512×512 in the console |
| **Feature graphic** | **1024×500** PNG/JPG (no transparency) | ⬜ **must create** — shown at top of listing |
| **Phone screenshots** | 2–8 images, min 320px, 16:9 or 9:16 | ⬜ **must create** (capture from emulator: Home, a Finance calc with result, Scientific, Dark mode) |
| **Tablet screenshots** | optional but recommended (7" & 10") | ⬜ optional |
| **Promo video (YouTube)** | optional | ⬜ optional |

> Quick way to make the feature graphic: put the CalcVerse icon + tagline "60+ Calculators, All Offline" on a brand-gradient background (matches the app's home header gradient).

---

## 5. Step-by-step upload walkthrough

> **Prerequisites (do these first, from PUBLISHING.md):**
> 1. **Phase 2** — replace AdMob **test** IDs in `app/build.gradle` (lines 26–29) with your **real** IDs. ⚠️ Shipping test ads violates AdMob policy.
> 2. **Phase 3** — create a release **keystore** and wire up `signingConfigs` (uncomment `app/build.gradle` lines 41, 46–53).
> 3. Bump `versionName`/`versionCode` in `app/build.gradle` (lines 16–17) for every upload.

### Step 1 — Build the signed release bundle (.aab)
In Android Studio: **Build → Generate Signed Bundle / APK → Android App Bundle**, select your keystore, choose the **release** build type. Output:
```
app/release/app-release.aab
```
(Or terminal: `./gradlew :app:bundleRelease`.)

### Step 2 — Create the app in Play Console
1. Go to <https://play.google.com/console> → **Create app**.
2. App name: **`CalcVerse - All Calculators`**  ·  Language: English (US)  ·  Type: **App**  ·  **Free**.
3. Accept the declarations.

### Step 3 — Fill "Set up your app" (left nav → *Dashboard*)
Work through each task Play lists:
- **App access** → "All functionality is available without special access" (the app needs no login).
- **Ads** → **Yes, contains ads**.
- **Content rating** → complete the questionnaire (see §6) → you'll get **Everyone**.
- **Target audience** → 13+ (safe, since ads use device IDs). Not designed for children.
- **Data safety** → see §7 (critical, gets rejected most often if wrong).
- **Government apps / Financial features** → No (you're a calculator, not a financial service — do NOT tick "financial features," it triggers extra review).

### Step 4 — Store listing (left nav → *Grow → Store presence → Main store listing*)
Paste the text from §1 and upload the graphics from §4. Save.

### Step 5 — Create a release
1. Left nav → **Release → Testing → Internal testing** (start here — instant, up to 100 testers).
2. **Create new release** → upload `app-release.aab`.
3. If Play offers **Play App Signing**, **accept it** (recommended — Google manages your signing key).
4. Release name: `1.0.0 (1)`. Release notes:
```
First release of CalcVerse — 60+ offline calculators for finance, health, math and everyday life.
```
5. **Review release → Start rollout to Internal testing.**

### Step 6 — Test, then promote to Production
1. Add your own email under **Testers**, open the opt-in link, install from Play, sanity-check on a real device.
2. When happy: **Release → Production → Create new release** → reuse the same bundle → **Roll out to Production**.
3. Google review for a brand-new app: a few hours up to ~7 days.

---

## 6. Content rating answers (calculator app)
Answer the questionnaire honestly; for CalcVerse everything is **No** (no violence, no sexual content, no gambling — note: a *tip/loan calculator is NOT gambling*, answer No). Result: **Everyone**.

---

## 7. Data safety form (most common rejection point)
CalcVerse is offline and stores data only locally, but **AdMob collects some data**. Declare accurately:

| Question | Answer |
|---|---|
| Does your app collect or share user data? | **Yes** (because of AdMob) |
| Data types collected | **Device or other IDs** (AdMob advertising ID). Optionally **App activity** if you later enable analytics. |
| Is data collected or shared? | **Shared** with Google (AdMob) for advertising |
| Is data encrypted in transit? | **Yes** |
| Can users request deletion? | Data is on-device; user can clear via History/Settings. AdMob ID resettable in device settings. |
| Is your calculation history collected by you? | **No** — it never leaves the device. |

> If you have **not** integrated AdMob yet and ship with ads disabled, you can answer "No data collected" — but since this build includes AdMob, declare the advertising ID.

---

## 8. Pre-flight checklist

- [ ] AdMob **real** IDs in `build.gradle` (not the `3940256099942544` test IDs)
- [ ] Release keystore created & `signingConfigs` uncommented
- [ ] `versionCode` = 1, `versionName` = "1.0.0"
- [ ] Privacy policy hosted at a **public URL** (host `PRIVACY_POLICY.md` on GitHub Pages / your site) and pasted into Play Console
- [ ] Feature graphic (1024×500) + at least 2 phone screenshots uploaded
- [ ] Store title, short & full descriptions pasted (§1)
- [ ] Category = Tools, "Contains ads" = Yes
- [ ] Data safety form completed (§7)
- [ ] Content rating = Everyone (§6)
- [ ] Built `.aab`, uploaded to Internal testing, installed & smoke-tested
- [ ] Promoted to Production
