# kΩunt — Deployment Guide

## Files
```
kount-app/
├── index.html          ← The app (single file, ~6100 lines)
├── manifest.json       ← PWA config (name, icons, display mode)
├── sw.js               ← Service worker (offline + caching)
├── favicon.ico         ← Browser tab icon
├── apple-touch-icon.png ← iOS home screen icon
├── icon-192.png        ← Android icon
├── icon-512.png        ← Splash screen icon
└── items.json          ← (Add your Bevager export here)
```

## Step 1: Set your Supabase key

Open `index.html`, find this line:
```js
const SUPABASE_ANON_KEY = 'REPLACE_WITH_YOUR_ANON_KEY';
```
Replace with your key from: Supabase Dashboard → Settings → API → anon public

---

## Step 2: Choose your hosting

### Option A: Vercel (recommended — free, instant, HTTPS)

1. Go to vercel.com → sign in with GitHub
2. Click "New Project" → Import your repo (or drag-drop the folder)
3. Framework: "Other" → Deploy
4. Done. You get: `https://kount-xyz.vercel.app`
5. Optional: Add custom domain like `count.yourcompany.com`

### Option B: Netlify (also free, also instant)

1. Go to netlify.com → sign in
2. Drag the `kount-app` folder onto the deploy area
3. Done. You get: `https://random-name.netlify.app`
4. Optional: Change site name to `kount.netlify.app`
5. Optional: Add custom domain

### Option C: Your own server (VPS, AWS, etc.)

Upload the files to any static web server. Requirements:
- HTTPS (required for PWA install + camera access)
- Correct MIME types (especially for .json and .js)

Nginx example:
```nginx
server {
    listen 443 ssl;
    server_name count.yourcompany.com;
    root /var/www/kount;
    index index.html;

    # HTTPS cert (use certbot)
    ssl_certificate /etc/letsencrypt/live/count.yourcompany.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/count.yourcompany.com/privkey.pem;

    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

### Option D: Custom domain on Vercel/Netlify

1. Buy a domain (e.g., `kount.app` or `count.hwood.com`)
2. In Vercel/Netlify dashboard → Domains → Add
3. Point your DNS CNAME to vercel/netlify
4. HTTPS is auto-provisioned

---

## Step 3: Install on phones

### iPhone
1. Open the URL in **Safari** (not Chrome)
2. Tap Share → "Add to Home Screen"
3. The Ω icon appears on home screen
4. Opens full-screen, no browser bar

### Android
1. Open the URL in **Chrome**
2. Chrome auto-prompts "Install kΩunt" (or tap ⋮ → Install app)
3. The Ω icon appears in the app drawer
4. Opens full-screen, no browser bar

### Desktop (Mac/Windows)
1. Open in Chrome
2. Click the install icon in the address bar
3. Or ⋮ → "Install kΩunt"
4. Opens as a standalone window

---

## Updating the app

1. Edit `index.html`
2. Push to your repo (Vercel/Netlify auto-deploy)
   OR re-upload the files
3. Bump the version in `sw.js`:
   ```js
   const CACHE_NAME = 'kount-v2';  // was v1
   ```
4. Users get the update on next open (service worker refreshes)

---

## Why HTTPS matters

PWAs require HTTPS for:
- Service worker registration (offline mode)
- Camera access (barcode scanning, photo capture)
- "Add to Home Screen" install prompt

Vercel and Netlify provide HTTPS automatically.
GitHub Pages also works but shows the github.io domain.

---

## Access control

Edit the `ACCESS_LIST` in `index.html`:
```js
{ email: 'admin@hwood.com', name: 'Admin', role: 'corporate', venueIds: 'all' },
{ email: 'counter@team.com', name: 'Counter', role: 'counter', venueIds: ['v1'] },
```
