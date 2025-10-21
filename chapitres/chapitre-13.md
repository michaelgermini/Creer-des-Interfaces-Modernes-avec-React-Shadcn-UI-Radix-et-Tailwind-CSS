# Chapitre 13 : Déploiement et optimisation

## Introduction

Votre application est prête ! Dans ce chapitre, nous allons la préparer pour la production, l'optimiser et la déployer.

---

## Préparation pour la production

### Checklist avant déploiement

#### 1. Variables d'environnement

```bash
# .env.production
VITE_API_URL=https://api.myapp.com
VITE_APP_NAME=My App
VITE_ANALYTICS_ID=UA-XXXXXXXXX-X
```

```tsx
// Valider les variables au build
if (!import.meta.env.VITE_API_URL) {
  throw new Error('VITE_API_URL is required')
}
```

#### 2. Supprimer les console.log

```bash
# Installer le plugin
npm install -D vite-plugin-remove-console
```

```ts
// vite.config.ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import removeConsole from 'vite-plugin-remove-console'

export default defineConfig({
  plugins: [
    react(),
    removeConsole(), // Retire les console.log en production
  ],
})
```

#### 3. Optimiser les images

```bash
# Installer le plugin d'optimisation
npm install -D vite-plugin-imagemin
```

```ts
// vite.config.ts
import viteImagemin from 'vite-plugin-imagemin'

export default defineConfig({
  plugins: [
    react(),
    viteImagemin({
      gifsicle: {
        optimizationLevel: 7,
      },
      optipng: {
        optimizationLevel: 7,
      },
      mozjpeg: {
        quality: 80,
      },
      pngquant: {
        quality: [0.8, 0.9],
        speed: 4,
      },
      svgo: {
        plugins: [
          {
            name: 'removeViewBox',
          },
        ],
      },
    }),
  ],
})
```

#### 4. Mettre à jour les métadonnées

```html
<!-- index.html -->
<!DOCTYPE html>
<html lang="fr">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/favicon.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    
    <!-- SEO -->
    <meta name="description" content="Description de votre application" />
    <meta name="keywords" content="react, dashboard, tailwind" />
    <meta name="author" content="Votre Nom" />
    
    <!-- Open Graph -->
    <meta property="og:title" content="Mon Application" />
    <meta property="og:description" content="Description de l'application" />
    <meta property="og:image" content="https://myapp.com/og-image.jpg" />
    <meta property="og:url" content="https://myapp.com" />
    
    <!-- Twitter Card -->
    <meta name="twitter:card" content="summary_large_image" />
    <meta name="twitter:title" content="Mon Application" />
    <meta name="twitter:description" content="Description de l'application" />
    <meta name="twitter:image" content="https://myapp.com/twitter-image.jpg" />
    
    <title>Mon Application</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>
```

---

## Minification et purge Tailwind

### Configuration Tailwind optimale

```js
// tailwind.config.js
export default {
  content: [
    './index.html',
    './src/**/*.{js,ts,jsx,tsx}',
  ],
  // Purge automatique en production
  // Tailwind ne génère que les classes utilisées
}
```

### Build de production

```bash
# Build avec Vite
npm run build

# Résultat dans dist/
dist/
├── assets/
│   ├── index-[hash].js
│   ├── index-[hash].css
│   └── logo-[hash].svg
└── index.html
```

### Analyser la taille du bundle

```bash
# Installer le visualizer
npm install -D rollup-plugin-visualizer
```

```ts
// vite.config.ts
import { visualizer } from 'rollup-plugin-visualizer'

export default defineConfig({
  plugins: [
    react(),
    visualizer({
      open: true, // Ouvre automatiquement le rapport
      gzipSize: true,
      brotliSize: true,
    }),
  ],
})
```

```bash
# Build et voir le rapport
npm run build
```

### Code splitting

```tsx
// Lazy loading des pages
import { lazy, Suspense } from 'react'

const Dashboard = lazy(() => import('./pages/dashboard'))
const Users = lazy(() => import('./pages/users'))
const Settings = lazy(() => import('./pages/settings'))

function App() {
  return (
    <Suspense fallback={<LoadingSpinner />}>
      <Routes>
        <Route path="/" element={<Dashboard />} />
        <Route path="/users" element={<Users />} />
        <Route path="/settings" element={<Settings />} />
      </Routes>
    </Suspense>
  )
}
```

---

## Analyse des performances

### Lighthouse audit

```bash
# 1. Build de production
npm run build

# 2. Servir localement
npm run preview

# 3. Ouvrir Chrome DevTools
# 4. Aller dans l'onglet Lighthouse
# 5. Générer le rapport
```

### Web Vitals

```bash
npm install web-vitals
```

```tsx
// src/main.tsx
import { getCLS, getFID, getFCP, getLCP, getTTFB } from 'web-vitals'

function sendToAnalytics(metric: any) {
  // Envoyer à votre service d'analytics
  console.log(metric)
}

getCLS(sendToAnalytics)
getFID(sendToAnalytics)
getFCP(sendToAnalytics)
getLCP(sendToAnalytics)
getTTFB(sendToAnalytics)
```

### Performance React

```tsx
// 1. React DevTools Profiler
// Installer React DevTools extension
// Utiliser l'onglet Profiler pour identifier les re-renders

// 2. Optimiser les composants
import { memo } from 'react'

const ExpensiveComponent = memo(({ data }) => {
  // Composant lourd
  return <div>{/* ... */}</div>
})

// 3. Lazy loading des images
function LazyImage({ src, alt }: { src: string; alt: string }) {
  return (
    <img
      src={src}
      alt={alt}
      loading="lazy" // Native lazy loading
    />
  )
}
```

### Optimisations Vite

```ts
// vite.config.ts
export default defineConfig({
  build: {
    // Target modern browsers
    target: 'esnext',
    
    // Minification
    minify: 'terser',
    terserOptions: {
      compress: {
        drop_console: true,
        drop_debugger: true,
      },
    },
    
    // Code splitting
    rollupOptions: {
      output: {
        manualChunks: {
          // Vendor chunks
          react: ['react', 'react-dom', 'react-router-dom'],
          ui: ['@radix-ui/react-dialog', '@radix-ui/react-dropdown-menu'],
          animation: ['framer-motion'],
        },
      },
    },
    
    // Chunk size warning
    chunkSizeWarningLimit: 1000,
  },
})
```

---

## Hébergement sur Vercel ou Netlify

### Déploiement sur Vercel

#### 1. Préparer le projet

```json
// package.json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  }
}
```

#### 2. Créer vercel.json

```json
// vercel.json
{
  "buildCommand": "npm run build",
  "outputDirectory": "dist",
  "devCommand": "npm run dev",
  "installCommand": "npm install",
  "framework": "vite",
  "rewrites": [
    {
      "source": "/(.*)",
      "destination": "/index.html"
    }
  ]
}
```

#### 3. Déployer

```bash
# Installer Vercel CLI
npm install -g vercel

# Login
vercel login

# Déployer
vercel

# Production
vercel --prod
```

**Ou via GitHub** :
1. Push sur GitHub
2. Aller sur [vercel.com](https://vercel.com)
3. Import repository
4. Deploy automatique !

### Déploiement sur Netlify

#### 1. Créer netlify.toml

```toml
# netlify.toml
[build]
  command = "npm run build"
  publish = "dist"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200

[[headers]]
  for = "/assets/*"
  [headers.values]
    Cache-Control = "public, max-age=31536000, immutable"
```

#### 2. Déployer

```bash
# Installer Netlify CLI
npm install -g netlify-cli

# Login
netlify login

# Déployer
netlify deploy

# Production
netlify deploy --prod
```

**Ou via GitHub** :
1. Push sur GitHub
2. Aller sur [netlify.com](https://netlify.com)
3. Connect repository
4. Deploy automatique !

### Variables d'environnement

**Vercel** :
1. Dashboard → Project → Settings → Environment Variables
2. Ajouter : `VITE_API_URL`, etc.

**Netlify** :
1. Site settings → Build & deploy → Environment
2. Ajouter les variables

---

## Monitoring et analytics

### Sentry (Error tracking)

```bash
npm install @sentry/react
```

```tsx
// src/main.tsx
import * as Sentry from '@sentry/react'

Sentry.init({
  dsn: 'YOUR_SENTRY_DSN',
  environment: import.meta.env.MODE,
  integrations: [
    new Sentry.BrowserTracing(),
    new Sentry.Replay(),
  ],
  tracesSampleRate: 1.0,
  replaysSessionSampleRate: 0.1,
  replaysOnErrorSampleRate: 1.0,
})

// Wrapper l'app
const root = ReactDOM.createRoot(document.getElementById('root')!)
root.render(
  <Sentry.ErrorBoundary fallback={<ErrorFallback />}>
    <App />
  </Sentry.ErrorBoundary>
)
```

### Google Analytics

```bash
npm install react-ga4
```

```tsx
// src/main.tsx
import ReactGA from 'react-ga4'

ReactGA.initialize('G-XXXXXXXXXX')

// Track page views
import { useEffect } from 'react'
import { useLocation } from 'react-router-dom'

function usePageTracking() {
  const location = useLocation()
  
  useEffect(() => {
    ReactGA.send({ 
      hitType: 'pageview', 
      page: location.pathname + location.search 
    })
  }, [location])
}
```

---

## CI/CD avec GitHub Actions

### Workflow de déploiement

```yaml
# .github/workflows/deploy.yml
name: Deploy to Production

on:
  push:
    branches: [main]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run tests
        run: npm test
      
      - name: Build
        run: npm run build
        env:
          VITE_API_URL: ${{ secrets.VITE_API_URL }}
      
      - name: Deploy to Vercel
        uses: amondnet/vercel-action@v20
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.ORG_ID }}
          vercel-project-id: ${{ secrets.PROJECT_ID }}
          vercel-args: '--prod'
```

---

## Résumé du chapitre

Dans ce chapitre, nous avons préparé notre app pour la production :

✅ **Préparation** : Variables, optimisations, métadonnées
✅ **Build** : Minification, purge, code splitting
✅ **Performance** : Lighthouse, Web Vitals, optimisations
✅ **Déploiement** : Vercel, Netlify, CI/CD
✅ **Monitoring** : Sentry, Analytics

### Points clés à retenir

💡 Toujours tester en production avant le déploiement

💡 Monitorer les performances avec Lighthouse

💡 Utiliser le code splitting pour réduire la taille initiale

💡 Mettre en place un monitoring d'erreurs

---

## Exercices

### Exercice 1 : Optimisation
Optimisez votre projet :
- Analysez avec Lighthouse
- Corrigez les problèmes identifiés
- Atteignez un score > 90 sur toutes les métriques

### Exercice 2 : Déploiement
Déployez votre application :
- Sur Vercel ou Netlify
- Configurez les variables d'environnement
- Mettez en place le CI/CD

### Exercice 3 : Monitoring
Ajoutez le monitoring :
- Sentry pour les erreurs
- Google Analytics pour le tracking
- Web Vitals pour les performances

---

**Prêt à aller plus loin ?** → [Chapitre 14 : Aller plus loin](./chapitre-14.md)

