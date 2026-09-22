# SafeHSE Ops — GitHub Pages deployment

This package is a standalone Progressive Web App (PWA): once it's hosted on
your own domain (GitHub Pages, in this guide), it installs like a real app on
Android, iOS and desktop, and keeps working offline after the first visit.

## Files in this package
```
index.html          the app itself
manifest.json        app name, icons, colors — makes it installable
service-worker.js    caches the app so it works offline
icons/icon-192.png
icons/icon-512.png   app icons
```
Keep this folder structure exactly as-is — the icon and manifest paths inside
`index.html` assume `icons/` sits next to it.

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
- PDF and Excel export need a live connection **the first time** you use them
  on a device, since they load a small library from a CDN. After that first
  successful export, the library is cached too and export keeps working
  offline.
- Data entered while offline is saved on that device immediately (it isn't
  lost) — see the note on shared data below.

## About shared data across devices
Right now, data lives in each device's own browser storage — a phone that
logs an incident won't show it on another phone or on the office desktop.
Getting every device to see the same live data needs a small free cloud
database wired in (Firebase is the natural fit here). That's a separate step
from this deployment — let me know when you're ready to set it up and I'll
walk you through it and wire it into this same package.
