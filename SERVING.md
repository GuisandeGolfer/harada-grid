# Running & hosting Open Window 64

It's one file (`index.html`). Three ways to use it.

## 1. Just open it (fastest test)
Double-click `index.html`, or drag it into a browser. Everything works except: opening from `file://` can be picky about clipboard copy on some browsers — if "Share link" doesn't copy, the link goes in the address bar instead.

## 2. Serve locally (recommended for testing share links + mobile)
From this folder:
```
python3 -m http.server 8080
```
Then open **http://localhost:8080** (on WSL2, open it in your Windows browser — `localhost` forwards).
To test on your phone on the same Wi-Fi, find your machine's LAN IP and visit `http://<that-ip>:8080`.

## 3. Host it online (share with followers)
Any static host works — drop `index.html` in:
- **GitHub Pages** — push to a repo, enable Pages on the branch.
- **Netlify / Cloudflare Pages / Vercel** — drag-and-drop the folder, done.
- **Your own server / Tailscale** — serve the folder as static files.

No build step, no env vars, no backend. The share link is just `https://your-site/#c=...` — the whole chart rides in the URL.
