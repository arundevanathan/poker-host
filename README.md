# Poker Host

A free, no-account tournament manager for home poker nights. One person hosts, everyone joins on their phone — no app install required.

**[Live demo →](https://arun-devanathan.github.io/poker-host)** *(replace with your deployed URL)*

---

## What it does

The host opens a controller on their phone or laptop and runs the tournament. Players join by entering a 4-digit PIN on their own phones and get a live view of the timer, blinds, their stack info, and the prize pool.

**Host controller**
- Configurable blind structure with import/export (CSV)
- Level timer with start / pause / reset
- Auto or manual level progression
- Player management — add players, track rebuys and add-ons, eliminate players
- Currency selection (USD, INR, EUR, GBP)
- Full reset or timer-only reset

**Player display**
- Live timer and current blinds
- Personal rebuy/add-on status
- Prize pool breakdown (70/30 split)

**TV / big screen dashboard**
- Large-format display optimised for a shared screen
- Player chip list, level alarm overlay on level change

---

## How to use

### Hosting a game

1. Open the app and tap **New Game**
2. Share the 4-digit PIN with players
3. Open **Tournament Settings** to configure blinds, buy-in, rebuys, etc.
4. Add players as they sit down, then hit **Start**

### Joining as a player

1. Go to the app URL on your phone
2. Tap **Join Game**, enter the PIN and your name
3. Your personal dashboard loads — it stays live as the tournament runs

### Big screen

Add `dashboard.html?pin=XXXX` to a browser on your TV or laptop for a shared view.

---

## Self-hosting

The app is a collection of plain HTML files backed by Firebase Realtime Database. No build step required.

### 1. Create a Firebase project

1. Go to [console.firebase.google.com](https://console.firebase.google.com) and create a project
2. Add a **Realtime Database** (start in test mode, then apply the rules below)
3. Go to **Project Settings → Your apps → Web** and register an app
4. Copy the config values

### 2. Configure the app

Copy `firebase-config.example.js` to `firebase-config.js` and fill in your values:

```js
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "your-project.firebaseapp.com",
  databaseURL: "https://your-project-default-rtdb.firebaseio.com",
  projectId: "your-project",
  storageBucket: "your-project.firebasestorage.app",
  messagingSenderId: "YOUR_SENDER_ID",
  appId: "YOUR_APP_ID"
};
```

### 3. Apply Firebase security rules

In the Firebase console go to **Realtime Database → Rules** and paste the contents of [`database.rules.json`](database.rules.json), or deploy via CLI:

```bash
npm install -g firebase-tools
firebase login
firebase init database   # select your project
firebase deploy --only database
```

The rules validate all data shapes and value ranges to prevent malformed writes.

### 4. Deploy

#### GitHub Pages (recommended)

1. Fork this repo
2. Add your Firebase API key as a repository secret named `FIREBASE_API_KEY`  
   *(Settings → Secrets and variables → Actions → New repository secret)*
3. Push to `main` — GitHub Actions injects the key and deploys automatically

The workflow is in [`.github/workflows/deploy.yml`](.github/workflows/deploy.yml).

#### Any static host

Drop the files onto any static host (Netlify, Vercel, Cloudflare Pages, S3, etc.) with `firebase-config.js` populated. No server-side component needed.

---

## Project structure

```
poker-host/
├── index.html              # Home — create or join a game
├── controller.html         # Host control panel
├── display.html            # Player personal view
├── dashboard.html          # TV / big-screen view
├── join.html               # Player join flow
├── about.html              # About page
├── firebase-config.js      # Firebase config (apiKey is a CI placeholder)
├── firebase-config.example.js
├── database.rules.json     # Firebase Realtime Database security rules
└── .github/workflows/
    └── deploy.yml          # GitHub Actions deploy pipeline
```

---

## Contributing

Bug reports and pull requests are welcome. Open an issue first for anything beyond a small fix so we can discuss approach.

When submitting a PR:
- Keep changes focused — one thing per PR
- Test on mobile (the primary target) and desktop
- Don't introduce external dependencies; the app is intentionally dependency-free

---

## License

MIT — see [LICENSE](LICENSE).

---

## Notes on security

- **Game PINs are 4 digits** — not a secret, just a session identifier. Don't put sensitive info in player names.
- Firebase API keys for Realtime Database are designed to be public. Access is controlled by the database security rules, not the key.
- Games currently persist in Firebase indefinitely. Treat it as temporary storage for the duration of your game night.
