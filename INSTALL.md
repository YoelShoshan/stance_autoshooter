# STANCE — install on Android (PWA)

You now have everything needed for an installable Progressive Web App:

```
stance-shooter.html        the game
manifest.json              app name, icons, fullscreen settings
service-worker.js          offline caching
icon-192.png               home-screen icon
icon-512.png               splash / store icon
icon-512-maskable.png      adaptive icon (Android safe-zone)
```

Keep all of these in the SAME folder. The game's start_url is `stance-shooter.html`.

## Why it must be hosted
A PWA needs **HTTPS** and a real URL — it cannot be installed from a `file://` path,
and the service worker / localStorage won't work locally. So upload the folder to any
free static host, then open the URL on your phone.

## Easiest path: GitHub Pages (free)
1. Create a new GitHub repo and upload all the files above to it.
2. Repo **Settings → Pages → Build and deployment**: Source = "Deploy from a branch",
   Branch = `main`, folder = `/ (root)`. Save.
3. Wait ~1 minute. You'll get a URL like `https://YOURNAME.github.io/REPO/stance-shooter.html`.

## Other one-click hosts
- **Netlify** or **Cloudflare Pages** or **Vercel**: drag-and-drop the folder, get an HTTPS URL.

## Install on the phone
1. Open the `stance-shooter.html` URL in **Chrome on Android**.
2. Tap it once (this also unlocks audio). Chrome shows an **"Install app" / "Add to Home screen"**
   prompt — or use the ⋮ menu → "Add to Home screen / Install app".
3. It installs with its own icon and launches **fullscreen, offline-capable**, like a native app.

Progress (cleared levels, unlocked sectors, best score, mute preference) is saved on the
device via localStorage and survives closing the app.

## Optional: turn it into a real .apk for the Play Store
Once the PWA is live at an HTTPS URL, wrap it with Google's **Bubblewrap**:
```
npm i -g @bubblewrap/cli
bubblewrap init --manifest https://YOURDOMAIN/manifest.json
bubblewrap build
```
This produces a signed `.aab`/`.apk` (Trusted Web Activity) you can sideload or publish.
Requires Node.js + Android SDK and a Play Console account for store listing.

## Notes
- To ship an update, change the game file and bump `CACHE_VERSION` in `service-worker.js`
  (e.g. `stance-v2`) so installed clients fetch the new build.
- The debug flag `DEBUG_UNLOCK_ALL` near the top of the script is `false` (real progression).
  Set it to `true` to open every level for testing.
- Debug keys (desktop only): H heal, K clear enemies, M mute.
