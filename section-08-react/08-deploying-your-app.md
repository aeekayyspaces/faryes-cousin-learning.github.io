# 08 — Deploying Your App

## Learning Objectives

By the end of this page, you will be able to:

- Build your React app for production with `npm run build`
- Deploy to Vercel with zero configuration
- Deploy to GitHub Pages with a small configuration
- Understand what a production build contains
- Set environment variables for different environments

---

## The Production Build

Your development app runs through Vite's dev server with unoptimized, source-mapped code. Before deploying, build an optimized production bundle:

```bash
npm run build
```

This runs TypeScript type checking (`tsc`) then Vite's bundler. The output goes into `dist/`:

```
dist/
├── index.html          ← the single HTML file
├── assets/
│   ├── index-Bg3xNz8k.js    ← your app code, minified and bundled
│   └── index-Ck2mAp9r.css   ← your CSS, minified
```

The hashes in filenames (`Bg3xNz8k`) are content hashes — change the code, the hash changes, browsers download the new version automatically. Unchanged files keep their hash and stay cached.

### Preview the Production Build Locally

```bash
npm run preview
# Preview server at http://localhost:4173/
```

Always preview before deploying — production bundles sometimes behave differently than the dev server.

---

## Option 1: Vercel (Easiest — Recommended)

**Vercel** is the hosting platform built by the creators of Next.js. Zero configuration for Vite apps.

### Deploy in 3 Steps

**Step 1:** Push your project to GitHub (see Section 6):

```bash
git init
echo "node_modules/\ndist/" > .gitignore
git add .
git commit -m "Initial calculator app"
# create repo on GitHub, then:
git remote add origin git@github.com:yourusername/my-calculator.git
git push -u origin main
```

**Step 2:** Connect to Vercel:

1. Go to [vercel.com](https://vercel.com) and sign in with GitHub
2. Click **"Add New Project"**
3. Select your `my-calculator` repository
4. Vercel auto-detects Vite — all settings are correct
5. Click **Deploy**

**Step 3:** Your app is live at `https://my-calculator-yourusername.vercel.app` within about 60 seconds.

### Automatic Deployments

From this point on, every `git push` to `main` automatically triggers a new deployment. Push a change → Vercel builds → your live URL updates in under a minute.

Pull Requests also get **preview deployments** — a unique URL for each PR so you can test before merging.

---

## Option 2: GitHub Pages

GitHub Pages hosts static files directly from a GitHub repository.

### Setup

**Step 1:** Install the GitHub Pages deployment plugin:

```bash
npm install --save-dev gh-pages
```

**Step 2:** Add scripts to `package.json`:

```json
{
  "scripts": {
    "dev":     "vite",
    "build":   "tsc && vite build",
    "preview": "vite preview",
    "predeploy": "npm run build",
    "deploy":  "gh-pages -d dist"
  }
}
```

**Step 3:** Set the base URL in `vite.config.ts`:

```typescript
import { defineConfig } from "vite"
import react from "@vitejs/plugin-react"

export default defineConfig({
  plugins: [react()],
  base: "/my-calculator/",   // ← your repo name
})
```

**Step 4:** Deploy:

```bash
npm run deploy
```

This builds the app, pushes `dist/` to a `gh-pages` branch, and GitHub Pages serves it.

**Step 5:** Enable GitHub Pages:

1. Your repo on GitHub → Settings → Pages
2. Source: **Deploy from a branch** → Branch: `gh-pages` → `/root`
3. Save

Your app is live at `https://yourusername.github.io/my-calculator/`

### Re-deploying

```bash
git add .
git commit -m "Update calculator"
git push                  # push source code
npm run deploy            # push built app to Pages
```

---

## Custom Domain (Optional)

Both Vercel and GitHub Pages support custom domains:

**Vercel:** Project → Settings → Domains → Add your domain. Vercel handles HTTPS automatically via Let's Encrypt.

**GitHub Pages:** Repository → Settings → Pages → Custom domain. Add a `CNAME` file to your repo.

You'll need to buy a domain from a registrar (Namecheap, Cloudflare, etc.) and configure DNS — typically a CNAME pointing to `yourusername.github.io` or Vercel's DNS servers.

---

## Environment Variables

Hard-coding API keys in source code is a security risk — they end up in your GitHub repository and in the compiled JavaScript anyone can read.

### Vite Environment Variables

Vite reads `.env` files at build time:

```
.env                ← loaded always
.env.local          ← loaded always, gitignored (personal secrets)
.env.development    ← loaded in dev mode
.env.production     ← loaded in build mode
```

```bash
# .env.local (gitignored — never commit this)
VITE_WEATHER_API_KEY=your_secret_key_here
VITE_API_BASE_URL=https://api.example.com
```

**Important:** Variables must start with `VITE_` to be exposed to your app code.

Access in code:

```tsx
const apiKey = import.meta.env.VITE_WEATHER_API_KEY
const baseUrl = import.meta.env.VITE_API_BASE_URL
```

### Add `.env.local` to `.gitignore`

```gitignore
# .gitignore
node_modules/
dist/
.env.local
.env*.local
```

### Vercel Environment Variables

For production secrets, set them in Vercel's dashboard:

1. Project → Settings → Environment Variables
2. Add `VITE_WEATHER_API_KEY` → your value → Production environment
3. Redeploy

Vercel injects these at build time — they never appear in your repository.

---

## What Happens at Build Time

Vite's production build:

1. **TypeScript compilation** — checks all types, emits no type information in output
2. **Tree shaking** — removes unused code (imports you have but never call)
3. **Minification** — strips whitespace, shortens variable names
4. **Code splitting** — large dependencies can be split into separate chunks loaded on demand
5. **Asset hashing** — fingerprints files for cache-busting

Result: small, fast files that browsers can cache aggressively.

### Analyzing Bundle Size

```bash
npm install --save-dev rollup-plugin-visualizer
```

```typescript
// vite.config.ts
import { visualizer } from "rollup-plugin-visualizer"

export default defineConfig({
  plugins: [react(), visualizer({ open: true })]
})
```

```bash
npm run build
# Opens an interactive treemap showing what's in your bundle
```

Use this to identify large dependencies you could lazy-load or replace.

---

## Deployment Checklist

Before deploying:

- [ ] `npm run build` completes with no TypeScript errors
- [ ] `npm run preview` — app works correctly in production mode
- [ ] No API keys or secrets in source code
- [ ] `.env.local` is in `.gitignore`
- [ ] `README.md` explains how to run and build the project
- [ ] App works without a console in DevTools

**Further Reading:**
- [Vite Deployment Guide](https://vitejs.dev/guide/static-deploy.html)
- [Vercel Documentation](https://vercel.com/docs)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)

---

## Check Your Understanding

1. What does `npm run build` produce? Where does the output go?
2. Why do filenames in the `dist/` folder contain hashes like `index-Bg3xNz8k.js`?
3. What is the difference between deploying to Vercel and GitHub Pages in terms of effort and features?
4. Why must environment variable names start with `VITE_` in Vite projects?
5. What does tree-shaking do to the production bundle?

---

*Previous: [07 — Project: Calculator App](07-project-calculator.md)*
*Next: [09 — Quiz and Checkpoints](09-quiz-and-checkpoints.md)*
