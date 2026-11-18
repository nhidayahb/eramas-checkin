# ERAMAS Luncheon Check-in – setup guide

Files in this folder:

- `index.html` – the app (this is what GitHub Pages serves)
- `firestore.rules` – the database security rules (paste into Firebase)
- `README.md` – this guide

**Never upload the guest list CSV to GitHub.** Import it inside the app only.

---

## Part 1 – Firebase (database + logins), about 10 minutes

1. Go to <https://console.firebase.google.com> → **Add project** (Google Analytics not needed).
2. **Build → Firestore Database → Create database** → *Production mode* → region **asia-southeast1 (Singapore)**.
3. **Build → Authentication → Get started → Sign-in method** → enable **Email/Password** only.
   If **Anonymous** is enabled from the old version, **disable it**.
4. **Authentication → Users → Add user**, create two accounts (no real mailbox needed):
   - `admin@checkin.eramas.my` – organiser (you). Strong password.
   - `crew@checkin.eramas.my` – shared by registration counters and ushers.
5. **Authentication → Settings → User actions** → untick **Enable create (sign-up)**.
   Now nobody can create their own account.
6. **Firestore → Rules** → paste everything from `firestore.rules` → **Publish**.
   If you used different emails in step 4, change them in the rules **and** in
   `window.ADMIN_EMAILS` near the top of `index.html`.
7. **Project settings (gear) → General → Your apps → Web (`</>`)** → register app →
   copy the `firebaseConfig` values and paste them into `index.html`:

   ```js
   window.FIREBASE_CONFIG = { apiKey: "...", authDomain: "...", projectId: "...", appId: "..." };
   ```

   This config is not a password. It is designed to be public; the rules protect the data.

## Part 2 – GitHub Pages, about 5 minutes

1. Sign in at <https://github.com> → **New repository**, e.g. `eramas-checkin`.
   (Free accounts need it **Public** for Pages. That's fine: only code is in it, no guest data.)
2. **Add file → Upload files** → drag in `index.html`, `firestore.rules`, `README.md` → **Commit**.
3. **Settings → Pages** → Source: **Deploy from a branch** → Branch `main`, folder `/ (root)` → **Save**.
4. Wait 1–2 minutes. Your link appears at the top:
   `https://YOUR-USERNAME.github.io/eramas-checkin/`
   Tick **Enforce HTTPS** if shown.

## Part 3 – Extra lock on the Firebase key (recommended)

1. Open <https://console.cloud.google.com/apis/credentials> and pick your Firebase project.
2. Click the **Browser key (auto created by Firebase)**.
3. **Application restrictions → Websites** → add `https://YOUR-USERNAME.github.io/*` → Save.

Now the key only works from your GitHub page.

## On the day

- Every device: open the link → sign in with the crew login. Admin login only on the organiser's device.
- Organiser: **Guest list** tab → import the CSV (save it as *CSV UTF-8*).
- Do a rehearsal with "Load 365 sample guests", then import the real list (import replaces everything).
- On borrowed or personal phones, tap **Sign out** at the end. This also clears the guest list stored on that phone.

## After the event (personal data clean-up, PDPA)

1. **Guest list → Download report (CSV)** and keep it somewhere safe internally.
2. **Organiser tools → type DELETE → Delete all guest data.**
3. Firebase → **Authentication → Users** → disable or delete the crew account (or change its password).
4. Optional: delete the Firebase project and the GitHub repository.

## What protects what

| Risk | Protection |
|---|---|
| Stranger finds the link | Sees only a sign-in page; database refuses anyone not listed in the rules |
| Someone creates their own account | Sign-up disabled; rules only accept the listed emails |
| Crew account misused | Crew can only tick check-in/seated and edit guest details; cannot import, delete or wipe |
| Search engines | Page is marked `noindex` |
| Data left on devices | **Sign out** wipes the local copy |
| Data kept after event | **Delete all guest data** button |
| Malicious names in CSV report | Formula-looking cells are neutralised before export |

Note: the crew password is shared, so treat it like a door key – give it only to crew, and change it after the event.
