# 01 — Environment Setup: Node.js, npm, and Vite

## Learning Objectives

By the end of this page, you will be able to:

- Install Node.js and npm on Windows and Linux
- Understand what npm is and how it manages packages
- Create a React + TypeScript project with Vite
- Understand the generated project structure
- Start the development server and view your app
- Install and configure VS Code extensions for React development

---

## What You Need Before Starting

| Tool | Purpose | Required |
|------|---------|---------|
| **Node.js** | JavaScript runtime + npm package manager | Yes |
| **npm** | Installs and manages packages (comes with Node.js) | Yes |
| **VS Code** | Code editor with excellent React/TypeScript support | Strongly recommended |
| **Git** | Version control (covered in Section 6) | Recommended |

---

## Installing Node.js

### Windows

**Option 1 — Official Installer (simplest):**
1. Go to [nodejs.org](https://nodejs.org)
2. Download the **LTS** version (Long Term Support — more stable than Current)
3. Run the installer — accept all defaults
4. Open **Windows Terminal** or **PowerShell** and verify:

```powershell
node --version    # v20.x.x or higher
npm --version     # 10.x.x or higher
```

**Option 2 — Windows Subsystem for Linux (WSL) — Recommended for developers:**

WSL gives you a real Linux environment inside Windows — the same as what servers run:

```powershell
# In PowerShell as Administrator:
wsl --install    # installs Ubuntu by default
# Restart your computer, then open Ubuntu from Start menu
```

Then follow the Linux instructions below inside the Ubuntu terminal.

### Linux (Ubuntu/Debian)

Use **nvm** (Node Version Manager) — makes it easy to switch Node versions:

```bash
# Install nvm:
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

# Reload your shell config:
source ~/.bashrc   # or source ~/.zshrc if you use zsh

# Install and use the latest LTS:
nvm install --lts
nvm use --lts

# Verify:
node --version    # v20.x.x
npm --version     # 10.x.x
```

### macOS

```bash
# Using Homebrew (recommended):
brew install node

# Or using nvm (same as Linux):
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.zshrc
nvm install --lts
```

---

## What Is npm?

**npm** (Node Package Manager) is the package manager for JavaScript. It lets you:

- Install libraries your project depends on (`npm install react`)
- Run scripts defined in `package.json` (`npm run dev`)
- Publish your own packages to the npm registry

```bash
npm install packagename         # install a package and save to dependencies
npm install --save-dev package  # install as dev dependency (not in production build)
npm uninstall packagename       # remove a package
npm run scriptname              # run a script from package.json
npm list                        # list installed packages
```

### `package.json` — The Project Manifest

Every Node.js project has a `package.json` that describes the project:

```json
{
  "name": "my-calculator",
  "version": "1.0.0",
  "scripts": {
    "dev":     "vite",
    "build":   "tsc && vite build",
    "preview": "vite preview",
    "test":    "vitest"
  },
  "dependencies": {
    "react":     "^18.3.1",
    "react-dom": "^18.3.1"
  },
  "devDependencies": {
    "@types/react":       "^18.3.1",
    "@types/react-dom":   "^18.3.1",
    "@vitejs/plugin-react": "^4.3.1",
    "typescript":         "^5.5.3",
    "vite":               "^5.4.2",
    "vitest":             "^2.0.5"
  }
}
```

- **dependencies** — packages needed to run the app
- **devDependencies** — packages needed only during development (tooling, type definitions)
- **scripts** — shortcuts you run with `npm run <name>`

### `node_modules/` — Never Commit This

When you run `npm install`, npm downloads all packages into `node_modules/`. This folder can contain tens of thousands of files and hundreds of megabytes. It's always in `.gitignore`.

Anyone who clones your repository runs `npm install` to recreate it from `package.json`.

---

## Creating a React + TypeScript Project with Vite

**Vite** is a modern build tool that:
- Starts the dev server instantly (no bundling on startup)
- Updates your browser in milliseconds when you save a file (Hot Module Replacement)
- Builds an optimized production bundle with `npm run build`

```bash
# Create a new project:
npm create vite@latest my-calculator -- --template react-ts
cd my-calculator
npm install
npm run dev
```

You'll see:
```
  VITE v5.x.x  ready in 300 ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
```

Open `http://localhost:5173` in your browser. You'll see the default Vite + React welcome page.

> **Important:** Keep this terminal open. Vite watches your files — save a file, the browser updates instantly.

---

## Project Structure

```
my-calculator/
├── public/                 ← static assets served as-is (favicon, etc.)
│   └── vite.svg
├── src/                    ← your application source code
│   ├── assets/             ← images, fonts imported by your code
│   ├── App.css             ← styles for App component
│   ├── App.tsx             ← root App component
│   ├── index.css           ← global styles
│   ├── main.tsx            ← entry point — mounts React into the HTML
│   └── vite-env.d.ts       ← Vite-specific TypeScript declarations
├── index.html              ← the one HTML file; Vite injects the bundle here
├── package.json            ← project config and dependencies
├── tsconfig.json           ← TypeScript configuration
├── tsconfig.app.json       ← TypeScript config for the app code
├── tsconfig.node.json      ← TypeScript config for Vite config files
└── vite.config.ts          ← Vite configuration
```

### The Entry Point: `main.tsx`

```tsx
import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client'
import './index.css'
import App from './App.tsx'

createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <App />
  </StrictMode>,
)
```

This mounts your entire React app into the `<div id="root">` in `index.html`. Everything in React renders into this one div.

### `.tsx` vs `.ts`

- `.ts` — TypeScript without JSX (utilities, classes, pure logic)
- `.tsx` — TypeScript with JSX (React components that return JSX)

---

## VS Code Extensions

Install these for the best React/TypeScript development experience:

| Extension | Publisher | What It Does |
|-----------|-----------|-------------|
| **ES7+ React Snippets** | dsznajder | Type `rfc` → Tab for a React component |
| **TypeScript Vue Plugin** | — | (skip this one — it's for Vue) |
| **ESLint** | Microsoft | Inline lint errors |
| **Prettier** | Prettier | Format on save |
| **Auto Rename Tag** | Jun Han | Rename matching open/close JSX tags simultaneously |
| **GitLens** | GitKraken | Supercharged Git integration |

### Recommended `.vscode/settings.json`

Create this file in your project:

```json
{
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit"
  },
  "typescript.preferences.importModuleSpecifier": "relative"
}
```

---

## Clean Up the Generated Project

The default Vite template has demo content. Clean it up before building:

**`src/App.tsx`** — replace with:
```tsx
function App() {
  return (
    <div>
      <h1>Hello, React!</h1>
    </div>
  )
}

export default App
```

**`src/App.css`** — clear the contents.

**`src/index.css`** — keep or replace with your reset/base styles.

Save and your browser hot-reloads instantly showing the simplified page.

---

## Key Commands Reference

```bash
npm run dev       # start dev server (hot reload)
npm run build     # compile TypeScript + bundle for production
npm run preview   # preview the production build locally
npm run test      # run Vitest (if configured)
npm install       # install all dependencies from package.json
```

**Further Reading:**
- [Vite Guide](https://vitejs.dev/guide/)
- [Node.js Docs](https://nodejs.org/en/docs)
- [npm Docs](https://docs.npmjs.com/)
- [nvm GitHub](https://github.com/nvm-sh/nvm)

---

## Check Your Understanding

1. What is the difference between `dependencies` and `devDependencies` in `package.json`?
2. Why should `node_modules/` always be in `.gitignore`?
3. What does `npm run dev` do? What happens when you save a file?
4. What is the difference between a `.ts` file and a `.tsx` file?
5. What is `src/main.tsx` responsible for?

---

*Next: [02 — What Is React?](02-what-is-react.md)*
