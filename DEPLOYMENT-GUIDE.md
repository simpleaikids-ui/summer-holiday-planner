# Summer Holiday Planner — Deployment Guide

Two deployment-ready packages are included. Pick the one that fits your needs.

---

## Option A: Standalone HTML (Simplest)

**Location:** `deploy-html/index.html`

This is a single file that runs entirely in the browser using CDN-hosted libraries. No install or build step needed.

### Deploy to Netlify Drop (30 seconds)

1. Open [https://app.netlify.com/drop](https://app.netlify.com/drop)
2. Drag the `deploy-html` folder onto the page
3. Your site is live at a random Netlify URL (e.g., `cheerful-sundae-abc123.netlify.app`)
4. Optionally click "Set up your site" to choose a custom subdomain

### Deploy to any web host

Upload `deploy-html/index.html` to any static hosting provider: GitHub Pages, Cloudflare Pages, Firebase Hosting, Amazon S3, or a traditional web host.

### Limitations

- Slightly slower initial load (Babel compiles JSX in the browser)
- Harder to add features like PWA/offline mode or a backend
- All libraries loaded from CDN (requires internet)

---

## Option B: Vite + React Project (Recommended for Growth)

**Location:** `deploy-vite/`

A proper React project with Tailwind CSS. Produces an optimized, minified build.

### Prerequisites

- Node.js 18+ installed ([https://nodejs.org](https://nodejs.org))
- A terminal (Command Prompt, PowerShell, or Terminal)

### Local Development

```bash
cd deploy-vite
npm install
npm run dev
```

Open `http://localhost:5173` in your browser. Changes auto-reload.

### Build for Production

```bash
npm run build
```

This creates a `dist/` folder with optimized static files ready for any host.

### Deploy to Vercel (Recommended, Free)

1. Create a free account at [https://vercel.com](https://vercel.com)
2. Install the Vercel CLI: `npm install -g vercel`
3. Run from the `deploy-vite` folder:
   ```bash
   cd deploy-vite
   vercel
   ```
4. Follow the prompts. Your site goes live at `summer-holiday-planner.vercel.app` (or similar)
5. Every future `git push` auto-deploys

### Deploy to Netlify (Free)

1. Create a free account at [https://www.netlify.com](https://www.netlify.com)
2. Push the `deploy-vite` folder to a GitHub repo
3. In Netlify: "Add new site" → "Import an existing project" → connect your GitHub repo
4. Build settings:
   - Build command: `npm run build`
   - Publish directory: `dist`
5. Click "Deploy site"

### Deploy to GitHub Pages (Free)

1. Push to a GitHub repository
2. Install the deploy plugin:
   ```bash
   npm install -D vite-plugin-gh-pages
   ```
3. In `vite.config.js`, set `base: '/your-repo-name/'`
4. Run:
   ```bash
   npm run build
   npx gh-pages -d dist
   ```

---

## After Deployment: Unlocked Features

Once deployed on a real domain with HTTPS, these features become available:

### Real Weather API
The Open-Meteo weather API will work on a real domain (it was blocked in the artifact sandbox). Users who enter a zip code during onboarding will see live weather on their dashboard.

### PWA / Offline Mode (Future)
Add to `deploy-vite`:
```bash
npm install -D vite-plugin-pwa
```
Then configure in `vite.config.js`:
```js
import { VitePWA } from 'vite-plugin-pwa'

export default defineConfig({
  plugins: [
    react(),
    VitePWA({
      registerType: 'autoUpdate',
      manifest: {
        name: 'Summer Holiday Planner',
        short_name: 'Summer Plan',
        theme_color: '#6366f1',
        icons: [{ src: '/sun.svg', sizes: '192x192', type: 'image/svg+xml' }]
      }
    })
  ]
})
```

### Real Push Notifications (Future)
Requires a service worker (included with PWA setup above) and the Notification API:
```js
if ('Notification' in window) {
  Notification.requestPermission().then(p => {
    if (p === 'granted') {
      new Notification('Good Morning!', { body: 'You have 3 tasks today!' });
    }
  });
}
```

### Avatar Gallery with PNG Uploads (Future)
Use Firebase Storage, Cloudflare R2, or Supabase Storage for user-uploaded avatar images. The "Coming Soon" placeholder in the Avatar tab is ready to be replaced.

### Persistent Data / User Accounts (Future)
Add Firebase, Supabase, or a custom backend to save user data between sessions. Currently all data lives in React state (in-memory only).

---

## Custom Domain

To use your own domain (e.g., `summerplanner.com`):

1. Purchase a domain from Namecheap, Google Domains, or Cloudflare ($10–15/year for `.com`)
2. In your hosting provider (Vercel/Netlify), go to domain settings
3. Add your custom domain and follow the DNS setup instructions
4. HTTPS is automatically configured

---

## Project Structure (Vite)

```
deploy-vite/
├── index.html          ← Entry HTML
├── package.json        ← Dependencies & scripts
├── vite.config.js      ← Build configuration
├── tailwind.config.js  ← Tailwind CSS config
├── postcss.config.js   ← PostCSS config
├── public/
│   └── sun.svg         ← Favicon
└── src/
    ├── main.jsx        ← React entry point
    ├── index.css       ← Tailwind imports + custom styles
    └── App.jsx         ← Full application (~2340 lines)
```

---

## Troubleshooting

**"npm install" fails:** Make sure Node.js 18+ is installed. Run `node -v` to check.

**Blank page after deploy:** Check the browser console for errors. The most common issue is the `base` path in `vite.config.js` not matching your hosting path.

**Weather still shows "unavailable":** The Open-Meteo API requires HTTPS and an actual domain. It won't work on `localhost` if your browser blocks mixed content, but will work on deployed HTTPS sites.

**Tailwind classes not applying (Vite build):** Make sure `tailwind.config.js` content paths include `./src/**/*.{js,jsx}`.
