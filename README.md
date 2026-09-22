# SafeHSE Ops — GitHub Pages deployment

This package is a standalone Progressive Web App (PWA): once it's hosted on
your own domain (GitHub Pages, in this guide), it installs like a real app on
Android, iOS and desktop, and keeps working offline after the first visit.

## Files in this package
```
index.html          the app itself — now wired to your Firebase project
manifest.json        app name, icons, colors — makes it installable
service-worker.js    caches the app so it works offline
firestore.rules      paste into Firebase so shared data doesn't lock after 30 days
icons/icon-192.png
icons/icon-512.png   app icons
```
Keep this folder structure exactly as-is — the icon and manifest paths inside
`index.html` assume `icons/` sits next to it.

## Shared data is now live
`index.html` is wired to your Firebase project (`site-hse-ops`). Every phone,
tablet and desktop that opens this app sees the same records in real time —
add an incident on one phone and it appears on every other device within a
second or two. Master passcode and company logo are shared the same way.

**One step you must do in the Firebase console** (2 minutes): Firebase's
default "test mode" only allows open access for 30 days, then locks
everything down. To keep it open permanently:
1. Go to console.firebase.google.com → your `site-hse-ops` project
2. Build → Firestore Database → **Rules** tab
3. Delete what's there and paste in the contents of `firestore.rules`
   (included in this package)
4. Click **Publish**

Because this rule allows anyone with your app's link to read/write the
database directly (bypassing the phone/PIN login if they inspected the page),
treat the app link itself as something to only share with your team — it's
not indexed or discoverable, but it isn't secret-proof either. If you want
real lock-and-key security later (so even someone with the link can't touch
the database without logging in through the app), I can add proper Firebase
authentication — just ask.

## Deploy it in 5 minutes

1. Go to github.com and create a **new repository** (e.g. `safehse-ops`).
   It can be public or private — GitHub Pages works either way (a private
   repo needs GitHub Pro/Team/Enterprise for Pages; public repos get Pages
   free).
2. Upload all the files in this package to the repository, **keeping the
   `icons/` folder** — either drag-and-drop them on the GitHub web UI
   ("Add file → Upload files") or push with git if you're comfortable with
   the command line.
3. In the repo, go to **Settings → Pages**.
4. Under "Build and deployment", set **Source** to "Deploy from a branch",
   pick the branch (usually `main`) and folder `/ (root)`, then **Save**.
5. GitHub gives you a live URL after a minute or two, in the form:
   `https://<your-username>.github.io/safehse-ops/`
6. Open that link on your phone or computer. On Android/desktop Chrome
   you'll now see a genuine **Install** option (either an install icon in
   the address bar, or the "Install app" button inside SafeHSE Ops); on
   iPhone/iPad, use Share → Add to Home Screen (Apple doesn't offer a
   one-tap install for any web app, even Google's own).

## Updating the app later
Whenever you (or I) change `index.html`, `manifest.json` or the icons:
1. Upload the new file(s) to the same GitHub repo, overwriting the old ones.
2. Open `service-worker.js` and bump the version string
   (`safehse-ops-v1` → `safehse-ops-v2`, etc.) so devices that already
   installed the app know to fetch the new version instead of serving the
   old cached copy.
3. Give it a minute for GitHub Pages to redeploy, then everyone who opens
   the app will pick up the update automatically (installed users get it
   next time they open the app with a connection).

## About offline use
- The app itself (layout, all 24 modules, forms, dashboard) works fully
  offline once it's been opened at least once on a device.
- Data entered while offline is saved on the device immediately and syncs
  to every other device automatically the moment it's back online
  (Firestore's offline queue handles this — nothing extra to do).
- PDF and Excel export need a live connection **the first time** you use them
  on a device, since they load a small library from a CDN. After that first
  successful export, the library is cached too and export keeps working
  offline (though it will only include whatever data had already synced to
  that device).

## About shared data across devices
Data now syncs live across every device through Firebase — see "Shared data
is now live" above for the one remaining setup step (publishing the
Firestore rules) and an honest note on its security model.
