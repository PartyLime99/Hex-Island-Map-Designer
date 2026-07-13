# Hex Island Map Designer — PWA

A self-contained board-game map designer. Installs to a phone or desktop, works fully offline.

## Files

```
index.html              the whole app (art embedded, no dependencies)
manifest.webmanifest    app name, icons, colours
sw.js                   service worker — offline caching
icons/                  app icons (192, 512, maskable, apple-touch, favicon)
```

## Test it locally

A service worker needs `http://` or `https://` — opening `index.html` from your
file system will run the app but **not** the PWA parts. So serve it:

```bash
cd this-folder
python3 -m http.server 8000
# open http://localhost:8000
```

`localhost` counts as a secure origin, so install and offline mode both work there.

## Deploy (pick one — all free)

**GitHub Pages**
1. Create a repo, upload these files to the root.
2. Settings → Pages → Source: `main` branch, `/ (root)` → Save.
3. Live in ~1 min at `https://<you>.github.io/<repo>/`.

**Netlify / Cloudflare Pages** — drag this folder onto their dashboard. Done.

All of these serve HTTPS automatically, which the PWA requires.

## Installing

- **Android / Chrome / Edge:** an "⤓ Install app" button appears in the header.
- **iPhone / Safari:** Share → *Add to Home Screen* (Apple gives no install button;
  the app shows a one-time tip).
- **Desktop:** install icon in the address bar, or the header button.

Once installed it opens fullscreen with no browser bar and works with no connection.

## Shipping an update

Users have the old version cached, so you must tell the service worker something changed:

1. Edit `index.html`.
2. **Bump `CACHE_VERSION` in `sw.js`** (`'v1'` → `'v2'`).
3. Re-deploy.

Users get a "new version is available — Reload" prompt on their next visit.
If you skip step 2, they may keep seeing the old version indefinitely.

## Notes

- **Saved data** (defaults + save slots) lives in the browser's localStorage, tied to
  the site's origin. It survives updates. It does *not* transfer between devices, and
  it is lost if the user clears site data — the Export button is the portable backup.
- **Fonts** come from Google Fonts and are cached after first load. Offline-first-run
  falls back to system fonts; the layout is unaffected.
- **No analytics, no tracking, no server, no accounts.** Nothing leaves the device, so
  a privacy policy can honestly state that the app collects no data.

## Before publishing publicly

The tile art was AI-generated — some app stores now require you to disclose that.
If you publish anything that names or visually imitates a specific commercial board
game, check that publisher's fan-content policy first; keeping the tool free and
clearly unofficial is the low-risk path. The generic naming used here ("Hex Island
Map Designer") is deliberate.
