# Embroidery Ledger Pro → Android App (Capacitor)

## Files in this package
- `www/index.html` — tumhari app, Firebase auth ab native+web dono ke liye ready hai
- `capacitor.config.json` — app ID: `com.embroidery.ledgerpro`

---

## STEP 1 — Apne computer par (ya Codemagic ke pre-build script me) ye commands chalao

```bash
# is folder ke andar (jaha capacitor.config.json hai) jao, phir:
npm init -y
npm install @capacitor/core @capacitor/cli @capacitor/android
npm install @capacitor-firebase/authentication

npx cap add android
npx cap sync android
```

Ye `android/` folder bana dega — ye tumhara real Android Studio project hai.

---

## STEP 2 — Firebase Console me Android app add karo (Google Sign-in ke liye zaroori)

1. https://console.firebase.google.com → project `webapp-9504b` kholo
2. **Add app → Android** click karo
3. Package name: `com.embroidery.ledgerpro` (capacitor.config.json se match hona chahiye)
4. SHA-1 fingerprint add karna hoga — ye milega:
   ```bash
   cd android
   ./gradlew signingReport
   ```
   Output me `SHA1:` wali line copy karo aur Firebase console me paste karo
5. `google-services.json` file download karo Firebase se
6. Usko `android/app/google-services.json` me daalo

---

## STEP 3 — Android build config check

`android/app/build.gradle` ke top pe ye plugin line honi chahiye (Capacitor Firebase plugin khud add kar deta hai, na ho to manually daalo):
```gradle
apply plugin: 'com.google.gms.google-services'
```

`android/build.gradle` (project-level) me:
```gradle
buildscript {
  dependencies {
    classpath 'com.google.gms:google-services:4.4.1'
  }
}
```

---

## STEP 4 — Local test build (agar Android Studio hai)

```bash
npx cap open android
```
Android Studio khulega → Run button se emulator/phone pe test karo.

---

## STEP 5 — Codemagic se APK banwana (bina apne PC ke)

1. Is poore folder (www + capacitor.config.json + package.json) ko GitHub repo me push karo
2. https://codemagic.io pe signup karo, repo connect karo
3. Codemagic "Capacitor" workflow template choose karega automatically
4. Build settings me:
   - Pre-build script: `npm install && npx cap sync android`
   - `google-services.json` file ko Codemagic ke "Environment variables → Encrypted files" me upload karo
5. Start build → 5-10 min me APK/AAB download link milega

---

## Zaroori note

- App abhi `AIzaSyBwwiQUveVoSV9TlHhw7_9ALnvZFigsDCg` jaisi Firebase config keys use kar rahi hai — ye client-side keys hain, publicly expose hona normal hai for Firebase, koi security issue nahi (Firestore rules se security control hoti hai — wo already set hai kya, check kar lena).
- Package name (`com.embroidery.ledgerpro`) Play Store pe ek baar set hone ke baad change nahi hota — abhi hi final naam decide kar lo.
- App icon aur splash screen add karne ke liye: `npx @capacitor/assets generate` (icon.png 1024x1024 aur splash.png chahiye honge `assets/` folder me)
