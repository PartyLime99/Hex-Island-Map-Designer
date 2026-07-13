# Hex Island Map Designer — PWA

A hex-tile board-game map designer. Installs to a phone or desktop, works fully offline.

```
index.html              the whole app (art embedded, no dependencies)
manifest.webmanifest    app name, icons, colours
sw.js                   service worker — offline caching + updates
icons/                  app icons
```

---

## 1. Test it on your own machine first

A service worker only runs over `http://` or `https://`. Double-clicking
`index.html` will run the app but **not** the install/offline parts. So serve it:

```bash
cd map-designer-pwa
python3 -m http.server 8000
```

Open `http://localhost:8000`. `localhost` counts as secure, so install and
offline both work there.

---

## 2. Put it on GitHub (first time — about 10 minutes)

You don't need the command line for any of this.

1. **Make an account** at github.com if you haven't.
2. **New repository** (green button, or the `+` top-right).
   - Name it e.g. `map-designer`.
   - Set it **Public** (required for free GitHub Pages).
   - Tick **Add a README file**. Click *Create repository*.
3. **Upload the files.** On the repo page: *Add file* -> *Upload files*.
   Drag in `index.html`, `manifest.webmanifest`, `sw.js`, and the whole `icons`
   folder. Scroll down, click **Commit changes**.

   > Important: these files must sit at the **top level** of the repo, not inside
   > a `map-designer-pwa/` subfolder. If you drag the outer folder in, GitHub will
   > nest them and the site won't load.

4. **Turn on Pages.** Repo *Settings* -> *Pages* (left sidebar) ->
   under *Source* choose **Deploy from a branch**, branch **main**, folder **/ (root)** -> *Save*.
5. Wait ~1 minute, refresh that page, and it'll show your live URL:
   `https://<your-username>.github.io/map-designer/`

That URL is HTTPS, which is exactly what the PWA needs. Open it on your phone and
you'll get the install prompt.

---

## 3. How to "rev" it (ship an update)

Here's the bit that matters, and the bit people get wrong.

**Every user's phone has a cached copy of your app.** That's what makes it work
offline — but it also means that if you just replace `index.html`, some users may
keep running the old version indefinitely. The service worker only knows something
changed if you *tell* it.

### The rule: change the app -> bump the version in `sw.js`

Open `sw.js`. Near the top:

```js
const CACHE_VERSION = 'v2';
```

Change it to `'v3'`. That's it. That one character is what triggers everything:
the browser sees a byte-different `sw.js`, installs the new service worker,
downloads the fresh files under a new cache name, and throws the old cache away.

### The full update loop

1. Edit `index.html` (or the icons, or anything else).
2. **Bump `CACHE_VERSION` in `sw.js`.**
3. Upload both to GitHub: *Add file* -> *Upload files* -> drag the changed files in ->
   *Commit changes*. (Uploading a file with an existing name overwrites it — that's
   what you want.)
4. GitHub Pages redeploys automatically in ~1 minute.

### What users actually experience

- **Nothing is force-pushed to their phone.** There's no app-store-style background
  update. The check happens the next time they *open* the app.
- On opening, the browser quietly fetches `sw.js` in the background. If it changed,
  the new version downloads.
- They then see a small **"A new version is available — Reload"** prompt. One tap
  and they're on the new version.
- If they ignore it, they'll get the new version next time they fully close and
  reopen the app anyway.
- If they're **offline**, they keep using the cached version happily and update
  whenever they next have signal.

So: updates are automatic, but *lazy* — they land on next open, not instantly.
That's normal and fine for a tool like this.

### If you forget to bump the version

Users can get stuck on the old build. The fix is just to bump it and redeploy; they'll
catch up on their next visit. (To force it on your own device meanwhile: browser
settings -> clear site data, or on desktop DevTools -> Application -> Service Workers ->
*Unregister*.)

---

## Installing

- **Android / Chrome / Edge:** an "Install app" button appears in the app's header.
- **iPhone / Safari:** Share -> *Add to Home Screen*. (Apple allows no install button;
  the app shows a one-time tip.)
- **Desktop:** install icon in the address bar.

---

## Notes

- **Saved data** (defaults + save slots) lives in the browser's localStorage, tied to
  the site's URL. It survives updates. It does *not* sync between devices, and it's
  lost if the user clears site data — the Export button is the portable backup.
- Because the data is tied to the URL, **if you later move to a custom domain, users'
  saved setups won't follow.** Do it early if you're going to do it.
- **No analytics, no tracking, no server, no accounts.** Nothing leaves the device.
