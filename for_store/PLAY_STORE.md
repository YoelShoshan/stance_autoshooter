# Publishing EDGELORD to the Google Play Store

Your game is already a working PWA, so publishing means wrapping it in a **Trusted Web
Activity (TWA)** — a thin native Android app that loads your hosted site full-screen.
Gameplay code doesn't change; you're shipping a launcher that points at
`https://yoelshoshan.github.io/stance_autoshooter/`.

--------------------------------------------------------------------------------
## ⚠ 2026 REQUIREMENTS — read first
--------------------------------------------------------------------------------
- **Upload format is Android App Bundle (`.aab`)**, not APK. Bubblewrap outputs the
  `.aab` automatically — that's the file you upload.
- **Target API level 36 (Android 16).** New apps and updates in 2026 must target
  API 36. This is set by the **Bubblewrap CLI version**, so install/upgrade to the
  latest CLI right before building: `npm install -g @bubblewrap/cli@latest`. Then
  `bubblewrap doctor` should report an up-to-date SDK. (Do NOT rely on an old CLI —
  older versions target lower API levels and Play will reject the upload.)
- **Developer identity verification** must be fully completed on your Play account,
  or the release can be held up. Check Play Console → account details.
- **Data safety form is mandatory** — for this game the honest answer is
  "no data collected / no data shared" (see Step 4).

This folder contains the pieces you control, pre-staged. Below is the full path.

--------------------------------------------------------------------------------
## Files staged for you here
--------------------------------------------------------------------------------
- `privacy.html`              → host this; Play requires a privacy policy URL.
- `.well-known/assetlinks.json` → domain-verification file (needs your key fingerprint).
- `twa-manifest.json`         → Bubblewrap config, pre-filled from your PWA manifest.
- `PLAY_STORE.md`             → this guide.

--------------------------------------------------------------------------------
## One-time prerequisites
--------------------------------------------------------------------------------
1. **Google Play Developer account** — one-time US $25 at
   https://play.google.com/console/signup . This is the only hard cost.
2. **Node.js** (you already have it) and a **JDK + Android SDK**. Bubblewrap can
   install the JDK/SDK for you on first run, so you don't need full Android Studio.

--------------------------------------------------------------------------------
## Step 1 — Publish the privacy policy
--------------------------------------------------------------------------------
- Open `privacy.html`, fill in the `[DATE]` and `[YOUR CONTACT EMAIL]` placeholders.
- Commit it to the repo so it's live at:
  `https://yoelshoshan.github.io/stance_autoshooter/privacy.html`
- Keep that URL — you paste it into the Play Console listing.

--------------------------------------------------------------------------------
## Step 2 — Generate the Android app with Bubblewrap
--------------------------------------------------------------------------------
```
npm install -g @bubblewrap/cli@latest      # @latest is important — gives you API 36 targeting
bubblewrap doctor                            # confirms JDK + Android SDK are set up
cd  (a NEW folder, outside your game repo — e.g. D:\gamedev\edgelord-twa)
bubblewrap init --manifest https://yoelshoshan.github.io/stance_autoshooter/manifest.json
```
- When prompted, accept the defaults from your manifest. Suggested answers:
  - Application ID / package: `io.github.yoelshoshan.stance`
  - Start URL: `/stance_autoshooter/`
  - (You can instead copy the provided `twa-manifest.json` into that folder to
    pre-seed the answers.)
- Let Bubblewrap install the JDK/Android SDK if it offers.
- It will create a **signing keystore** and ask for passwords.
  ⚠ BACK UP the keystore file and passwords somewhere safe. Losing them means you
     can never update this app's listing again.

Then build:
```
bubblewrap build
```
This produces:
- `app-release-bundle.aab`  → this is what you upload to Play.
- `app-release-signed.apk`  → handy for sideloading/testing on your own phone.

--------------------------------------------------------------------------------
## Step 3 — Domain verification (Digital Asset Links)
--------------------------------------------------------------------------------
This is what removes the browser address bar so it looks like a real app.

1. Get your signing key's SHA-256 fingerprint:
   ```
   bubblewrap fingerprint list
   ```
   (or Bubblewrap prints it after build; it looks like `AA:BB:CC:...`)
2. Paste that fingerprint into `.well-known/assetlinks.json`, replacing
   `REPLACE_WITH_YOUR_SHA256_FINGERPRINT`.
3. Commit the `.well-known/assetlinks.json` file to your repo so it's live at:
   `https://yoelshoshan.github.io/stance_autoshooter/.well-known/assetlinks.json`

   NOTE on GitHub Pages: it *does* serve `.well-known/` folders, but Jekyll (Pages'
   default) can ignore folders starting with a dot. If the file 404s after deploy,
   add an empty file named `.nojekyll` at the repo root and re-push — that disables
   Jekyll processing and the folder will serve.

4. Verify it works:
   `https://developers.google.com/digital-asset-links/tools/generator`
   (paste your domain + package + fingerprint; it checks the file is reachable).

Once **Play App Signing** is enabled (Step 4), Google re-signs your app with *its own*
key, so you'll come back and ADD Google's fingerprint to `assetlinks.json` too. Keep
both fingerprints in the array.

--------------------------------------------------------------------------------
## Step 4 — Create the Play Console listing
--------------------------------------------------------------------------------
In https://play.google.com/console :
1. Create app → name "EDGELORD", type Game, free.
2. Upload `app-release-bundle.aab` to a track (start with **Internal testing**).
3. Enable **Play App Signing** when prompted (recommended). Afterward, copy Google's
   SHA-256 from App Signing settings and add it to `assetlinks.json` (see note above).
4. Fill in the listing (assets checklist below).
5. Complete the required questionnaires. Exact answers for THIS game:
   - **Data safety:** "Does your app collect or share any of the required user data
     types?" → **No.** (The game only writes best-times/prefs to the device's own
     localStorage, which does NOT count as "collection" — collection means sending data
     off the device, which this game never does.) So: no data collected, no data
     shared, no data types to declare.
   - **Ads:** contains ads? → **No.**
   - **Content rating:** fill the IARC questionnaire honestly — mild/fantasy action,
     no real violence, no gambling, no user communication. It'll come out E/PEGI 3-ish.
   - **Target audience:** if you include under-13, extra Families-policy rules apply;
     simplest is to target 13+ unless you specifically want the kids' audience.
   - **Government app / financial / health:** No to all.
6. Roll out to Internal testing → install on your phone via the opt-in link and
   confirm it launches full-screen with NO address bar (that proves assetlinks works).
7. When happy, promote the release to **Production**. First review typically takes a
   few days.

--------------------------------------------------------------------------------
## Store listing assets you must provide
--------------------------------------------------------------------------------
- [x] App icon — 512×512 → you have `icon-512.png`.
- [x] Feature graphic — 1024×500 banner → you have `feature-graphic.png`.
- [ ] Phone screenshots — **at least 2**, min 320px, max 3840px per side, and one side
      no more than 2× the other. Portrait 1080×1920 is ideal for this game. Take them
      from the running game on a phone or emulator (or I can generate them). Show real
      gameplay — Google rejects placeholder/marketing-only screenshots (policy 4.3).
- [ ] Short description — max 80 chars.
- [ ] Full description — up to 4000 chars.
- [x] Privacy policy URL → `https://yoelshoshan.github.io/stance_autoshooter/privacy.html`
      (after you fill in the email and push).
- [ ] Content rating questionnaire answers (Step 4).
- [ ] Category (Arcade / Action) and contact email.

Suggested short description (≤80 chars):
  "Switch between offense and a reflecting shield to clear polygon sectors."

Ready-to-paste FULL description:
------------------------------------------------------------------------
EDGELORD is a fast, retro pixel-art auto-shooter about one decision: attack or defend.

Your guns fire on their own. What you control is your STANCE. In OFFENSE you unleash a
storm of fire. Flip to DEFENSE and an electric energy shield snaps up — it absorbs enemy
fire and REFLECTS it back as lethal shots, but it drains your stamina, so timing is
everything.

Fight through 10 sectors, each ruled by a polygon with one more edge than the last —
from the three-sided FLATLAND to THE LIMIT itself. Every sector ends in a boss built
from that shape.

FEATURES
• One-thumb control: move and switch stance — the shooting handles itself.
• A reflecting energy shield that turns the enemy's bullets into your best weapon.
• 7 unlockable ships, each with its own stats — speed, armor, hull, fire rate, and luck.
• 10 hand-tuned sectors with distinct bosses and fractal-noise nebula backdrops.
• Speedrun timers and per-level records — chase your best time on every sector.
• FLAWLESS clears: finish a sector without taking a hit to earn its badge.
• Works offline. No accounts. No ads. No tracking. Just the game.

Full controller support and touch controls included.
------------------------------------------------------------------------

Screenshots are pre-generated for you (portrait 1080×1920) in this folder:
  store-screenshot-1-title.png, -2-gameplay.png, -3-shield.png, -4-levels.png, -5-ships.png

--------------------------------------------------------------------------------
## Updating the app later
--------------------------------------------------------------------------------
- **Gameplay changes:** just `git push` as usual — the TWA loads your live site, so no
  Play resubmission is needed. (Remember to bump `CACHE_VERSION` so the service worker
  serves the new build, exactly like the web version.)
- **App shell changes** (icon, name, target API bumps Google may require): edit
  `twa-manifest.json`, bump `appVersionCode` (+1) and `appVersionName`, run
  `bubblewrap build`, upload the new `.aab`.

--------------------------------------------------------------------------------
## Reality check
--------------------------------------------------------------------------------
The TWA gives you Play Store presence, discoverability, and a base for future native
features (e.g. Play Games leaderboards — a natural fit for your speedrun-timing idea).
It does not otherwise change the game. If assetlinks cooperates, a signed `.aab` is an
afternoon's work; the account fee ($25) and store review (a few days) are the gating
items.
