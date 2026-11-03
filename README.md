# GLOSSMAT

Admin app for a car PPF, ceramic coating, polish and wash studio. It covers staff attendance, vehicle job entries, payments, records, invoices and Excel/PDF export. Data is stored live in Firebase Firestore. Only admins can sign in.

## Files
- `index.html` - the whole app
- `firebase-config.js` - paste your Firebase project details here
- `firestore.rules` - database security rules
- `firebase.json` - Firebase Hosting and rules deployment
- `manifest.webmanifest`, `sw.js`, `icon-192.png`, `icon-512.png`, `icon.svg` - install on a phone (PWA / APK)

## One-time setup

### 1. Firebase project
1. Open https://console.firebase.google.com and create a project (or use an existing one).
2. Project settings -> Your apps -> add a Web app (`</>`). Copy the `firebaseConfig` values into `firebase-config.js`.

### 2. Enable sign-in
Build -> Authentication -> Get started -> Sign-in method -> enable **Email/Password**.

Then open the Users tab -> Add user:
- Email: `mainadmin@glossmat.app`
- Password: a strong password of your choice

Copy this user's **User UID**.

### 3. Database
Build -> Firestore Database -> Create database (pick the region closest to you, for example `asia-south1`).

Open the Rules tab, paste the full content of `firestore.rules` and click Publish.

### 4. Main admin record
In Firestore create a collection named `admins`. Add a document whose ID is the **User UID** copied above, with these fields:
- `id` (string): `mainadmin`
- `role` (string): `main`

Without this document the login will say "This account is not an admin".

### 5. GitHub and hosting
1. Push this folder to a GitHub repository.
2. Host it. The easiest option is Firebase Hosting: `npm i -g firebase-tools`, `firebase login`, `firebase init hosting` (public directory `.`, single-page app: No), then `firebase deploy`. GitHub Pages also works: repository Settings -> Pages -> branch `main`, folder `/ (root)`.
3. **Required:** in Firebase Console -> Authentication -> Settings -> Authorized domains, add the domain of your site (for example `yourname.github.io`). Login will not work without it.

### 6. Android APK
Once the site is live, open https://www.pwabuilder.com, enter the site URL and generate the Android package (APK or AAB). You can also open the site on a phone and use "Add to Home screen".

## Creating more admins
Sign in as the main admin -> Staff tab -> Admins -> enter an ID and a password -> Create admin. The new credentials are shown once on screen; give them to that admin. There is no sign-up on the login page.
To revoke access, press Remove next to the admin (tap twice to confirm). To change a password, use Firebase Console -> Authentication.

## Where data is stored
- `staff`, `attendance` (one document per employee per month), `jobs` (vehicle entries), `lists` (dropdown values) and `admins`.
- Vehicle photos are compressed and stored in `jobs/{id}/photos`, so Firebase Storage is not needed.
- Entries made while offline are saved on the device and uploaded when the connection returns.

## Notes
- The Firebase `apiKey` is not a secret. Security comes from `firestore.rules`, so make sure the rules are published.
- Excel and PDF downloads work in browsers, as an installed PWA and in Chrome-based APKs. If a different APK wrapper blocks downloads, use the PWABuilder package.
