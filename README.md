# Keepstone — Android build via GitHub Actions

This turns the Keepstone web app into a real installable Android app,
using [Capacitor](https://capacitorjs.com/) to wrap the HTML page in a
native WebView shell, and GitHub Actions to compile the APK in the
cloud — you don't need Android Studio or any local build tools.

## What's in this folder

- `www/index.html` — the vault app itself (already switched from the
  Claude-artifact storage API to plain `localStorage`, so it actually
  persists data once installed on a phone)
- `capacitor.config.json` — tells Capacitor the app's ID/name and that
  the web content lives in `www/`
- `package.json` — the two Capacitor packages needed
- `.github/workflows/build-apk.yml` — the GitHub Actions workflow that
  does the actual build
- `.gitignore` — keeps the generated `android/` project and
  `node_modules/` out of your repo (the workflow regenerates them
  fresh on every run)

## Step 1 — Create a GitHub repository

1. Go to [github.com/new](https://github.com/new)
2. Name it something like `keepstone-vault`
3. Leave it **Public** or **Private** (both work — Actions minutes are
   free for public repos and included for private repos too)
4. Click **Create repository** — don't add a README/gitignore, since
   you already have those here

## Step 2 — Upload these files

You have two options — pick whichever is easier:

**Option A — no git needed (drag and drop):**
1. On your new repo's page, click **"uploading an existing file"**
2. Drag this entire folder's contents in (keep the folder structure —
   `.github/workflows/build-apk.yml` needs to stay at that exact path)
3. Commit directly to the `main` branch

**Option B — using git from a terminal:**
```bash
cd keepstone-app
git init
git add .
git commit -m "Initial Keepstone app"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/keepstone-vault.git
git push -u origin main
```

## Step 3 — Let GitHub build it

As soon as you push to `main`, the workflow starts automatically.
To check on it (or re-run it manually):

1. Open your repo on GitHub
2. Click the **Actions** tab
3. You'll see a run called **"Build Android APK"** — click it
4. If it's not already running, you can trigger it yourself: click
   **Run workflow** (top right) → **Run workflow**
5. Wait 3–6 minutes for the green checkmark

## Step 4 — Download the APK

1. Still on that finished workflow run's page, scroll down to
   **Artifacts**
2. Click **keepstone-debug-apk** to download a zip
3. Unzip it — inside is `app-debug.apk`

## Step 5 — Install it on your phone

1. Transfer `app-debug.apk` to your Android phone (email it to
   yourself, use Google Drive, a USB cable, whatever's easiest)
2. Tap the file on your phone to install it
3. Android will likely warn about "unknown apps" the first time —
   you'll need to allow installs from that source (Settings will
   prompt you directly with a button to do this)
4. Open **Keepstone** from your app drawer

## Notes and limitations

- This produces a **debug-signed APK** — perfectly fine to install on
  your own device, but not suitable for the Play Store (that needs a
  release keystore and Play Console setup, which is a separate,
  heavier process).
- Data is stored locally in the WebView via `localStorage`, encrypted
  the same way it was in the browser version (AES-GCM, key derived
  from your PIN via PBKDF2). Uninstalling the app deletes the vault,
  same as clearing an app's storage on Android normally would.
- There's still no real fingerprint/biometric unlock — that would
  require adding a native Capacitor plugin (e.g.
  `@capacitor/biometric-auth` alternatives) and a few lines of glue
  code. Ask if you want that added next.
- If a build fails, click into the failed step in the Actions log —
  the most common causes are a typo in a file path or an npm registry
  hiccup (just re-run the workflow).
