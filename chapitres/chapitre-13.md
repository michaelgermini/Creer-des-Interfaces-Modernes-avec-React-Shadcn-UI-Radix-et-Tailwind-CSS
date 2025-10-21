# Chapitre 13 : Déploiement et optimisation

## 📊 Résumé exécutif

**Objectif** : Préparer, optimiser et déployer l'application en production avec un score Lighthouse > 95, un temps de chargement < 2s, et une mise en production automatisée via CI/CD.

**Étapes clés** :
- Préparation (variables env, métadonnées, optimisations)
- Build optimisé (minification, tree-shaking, code splitting)
- Analyse performance (Lighthouse, Web Vitals)
- Déploiement (Vercel, Netlify, CI/CD)
- Monitoring (Sentry, Analytics)

**Métriques de succès** :
- Score Lighthouse : > 95/100
- FCP (First Contentful Paint) : < 1.8s
- LCP (Largest Contentful Paint) : < 2.5s
- Bundle size : < 250 KB (gzippé)
- Uptime : > 99.9%

---

## 🗺️ Carte mentale du déploiement

```
Déploiement & Optimisation
│
├── 🔧 Préparation
│   ├── Variables d'environnement (.env.production)
│   ├── Supprimer console.log
│   ├── Optimiser images (WebP, compression)
│   ├── Métadonnées SEO (title, description, OG)
│   └── Favicon & PWA manifest
│
├── 📦 Build Production
│   ├── Minification (Terser)
│   ├── Tree-shaking (dead code elimination)
│   ├── Code splitting (lazy loading)
│   ├── Purge Tailwind (classes inutilisées)
│   └── Bundle analysis (Visualizer)
│
├── ⚡ Optimisations
│   ├── Compression (Gzip, Brotli)
│   ├── Caching headers
│   ├── Lazy loading images/routes
│   ├── React.memo & useMemo
│   └── Service Worker (PWA)
│
├── 📊 Analyse Performance
│   ├── Lighthouse (4 métriques)
│   ├── Web Vitals (CLS, FID, FCP, LCP, TTFB)
│   ├── Bundle size (rollup-plugin-visualizer)
│   └── React DevTools Profiler
│
├── 🚀 Déploiement
│   ├── Vercel
│   │   ├── Auto-deploy (GitHub)
│   │   ├── Edge Network (CDN global)
│   │   ├── Serverless Functions
│   │   └── Analytics intégré
│   ├── Netlify
│   │   ├── Continuous Deployment
│   │   ├── CDN global
│   │   ├── Split testing
│   │   └── Form handling
│   └── GitHub Pages / AWS / autres
│
├── 🔄 CI/CD
│   ├── GitHub Actions
│   ├── Tests automatiques
│   ├── Build & Deploy
│   ├── Preview deployments
│   └── Rollback automatique
│
└── 📈 Monitoring
    ├── Sentry (error tracking)
    ├── Google Analytics (user tracking)
    ├── Web Vitals (performance)
    ├── Uptime monitoring
    └── Error rate alerts
```

---

## 📊 Comparaison des plateformes d'hébergement

### Tableau 1 : Features par plateforme

| Feature | Vercel | Netlify | GitHub Pages | AWS S3 + CloudFront |
|---------|--------|---------|--------------|---------------------|
| **CDN Global** | ✅ 100+ edge | ✅ 100+ edge | ✅ GitHub CDN | ✅ CloudFront |
| **Auto-deploy Git** | ✅ Instant | ✅ Instant | ✅ GitHub Actions | ⚠️ Manuel |
| **Serverless Functions** | ✅ Oui | ✅ Oui | ❌ Non | ⚠️ Lambda séparé |
| **Analytics** | ✅ Intégré | ✅ Intégré | ❌ Non | ⚠️ CloudWatch |
| **SSL/HTTPS** | ✅ Auto | ✅ Auto | ✅ Auto | ⚠️ Config ACM |
| **Preview deploys** | ✅ Oui | ✅ Oui | ❌ Non | ❌ Non |
| **Rollback** | ✅ 1-click | ✅ 1-click | ⚠️ Git revert | ⚠️ Manuel |
| **Custom domain** | ✅ Gratuit | ✅ Gratuit | ✅ Gratuit | ✅ Route53 |
| **Build time** | ~1-2 min | ~1-2 min | ~2-5 min | N/A |
| **Cold start** | < 100ms | < 100ms | N/A | < 50ms |

### Tableau 2 : Performance & Coûts

| Plateforme | Bande passante | Builds/mois | Prix | Performance |
|------------|----------------|-------------|------|-------------|
| **Vercel (Free)** | 100 GB | Illimité | **Gratuit** | ★★★★★ |
| **Vercel (Pro)** | 1 TB | Illimité | 20$/mois | ★★★★★ |
| **Netlify (Free)** | 100 GB | 300 min | **Gratuit** | ★★★★★ |
| **Netlify (Pro)** | 400 GB | 1000 min | 19$/mois | ★★★★★ |
| **GitHub Pages** | Soft limit | Via Actions | **Gratuit** | ★★★★☆ |
| **AWS S3+CF** | 50 GB (1er an) | N/A | ~5$/mois | ★★★★★ |

**Recommandation** : **Vercel** ou **Netlify** pour simplicité + performance ⭐

---

## 📐 Diagramme : Pipeline de déploiement CI/CD

```
┌─────────────────────────────────────────────────────────────┐
│                  DEVELOPER WORKFLOW                          │
└─────────────────────────────────────────────────────────────┘

Developer local
├── Code changes
├── git add .
├── git commit -m "Feature X"
└── git push origin main

              ↓

┌─────────────────────────────────────────────────────────────┐
│                    GITHUB (Repository)                       │
│  Trigger: Push to 'main' branch                             │
└─────────────────┬───────────────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────────────┐
│              GITHUB ACTIONS (CI/CD)                          │
│                                                              │
│  Step 1: Checkout code          (10s)                       │
│  Step 2: Setup Node.js          (15s)                       │
│  Step 3: Install dependencies   (30s)                       │
│  Step 4: Run linter            (10s)                        │
│  Step 5: Run tests             (20s)                        │
│  Step 6: Build production      (45s)                        │
│  Step 7: Analyze bundle        (5s)                         │
│                                                              │
│  Total time: ~2min 15s                                       │
│                                                              │
│  ✅ All checks passed                                        │
└─────────────────┬───────────────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────────────┐
│                  VERCEL DEPLOYMENT                           │
│                                                              │
│  1. Receive build artifacts (dist/)                         │
│  2. Upload to edge network (100+ locations)                 │
│  3. Invalidate old cache                                    │
│  4. Update DNS                                              │
│  5. Generate deployment URL                                 │
│                                                              │
│  Time: ~30s                                                  │
│                                                              │
│  ✅ Deployed to: https://myapp-xyz.vercel.app               │
│  ✅ Production URL: https://myapp.com                        │
└─────────────────┬───────────────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────────────┐
│                GLOBAL CDN (Edge Network)                     │
│                                                              │
│   🌍 Paris (8ms)     🌍 New York (12ms)                     │
│   🌍 Tokyo (15ms)    🌍 Sydney (18ms)                       │
│   🌍 Mumbai (10ms)   🌍 São Paulo (14ms)                    │
│                                                              │
│   Assets cached at edge for fast delivery                   │
└─────────────────┬───────────────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────────────┐
│                   END USER (Browser)                         │
│                                                              │
│   FCP: 1.2s  ✅                                              │
│   LCP: 1.8s  ✅                                              │
│   TTI: 2.4s  ✅                                              │
└─────────────────────────────────────────────────────────────┘

TOTAL TIME FROM PUSH TO LIVE:
git push → GitHub Actions (2min 15s) → Vercel Deploy (30s) → LIVE
Total: ~2min 45s 🚀
```

---

## 🧮 Calcul 1 : Gains de compression et minification

### Bundle avant optimisation

```
Code source (development):
- React components: 1,850 lignes × 50 chars/ligne × 1 byte = 92 KB
- node_modules: React + deps = 2,500 KB
Total dev: ~2,600 KB ❌
```

### Après minification (Terser)

```
Minification removes:
- Whitespace & newlines: -30%
- Comments: -5%
- Variable shortening: -15%
- Dead code elimination: -10%

Total reduction: ~60%

Bundle minifié: 2,600 × 0.4 = 1,040 KB
```

### Après Tree-shaking

```
Tree-shaking removes unused code:
- Unused React features: -200 KB
- Unused Radix components: -150 KB
- Unused Tailwind classes: -800 KB

Bundle après tree-shaking: 1,040 - 1,150 = ~290 KB
```

### Après compression Gzip

```
Gzip compression ratio: ~70%

290 KB × 0.3 = 87 KB (gzippé)

Avec Brotli (meilleur): ~75%
290 KB × 0.25 = 72 KB (brotli)
```

### Résultat final

```
DEV (non optimisé): 2,600 KB
PROD (optimisé): 87 KB (gzip) / 72 KB (brotli)

Réduction: (2,600 - 87) / 2,600 × 100 = 96.7% ✅

Économie de bande passante:
10,000 utilisateurs/mois × (2,600 - 87) KB = 24.5 GB économisés
```

---

## 🧮 Calcul 2 : Impact sur le temps de chargement

### Temps de téléchargement selon connexion

**Bundle: 87 KB (gzippé)**

| Type connexion | Vitesse | Temps téléchargement | Réaliste |
|----------------|---------|---------------------|----------|
| **Fibre (100 Mbps)** | 12.5 MB/s | 87 KB / 12.5 MB = **0.007s** | Paris, NY ✅ |
| **4G (25 Mbps)** | 3.1 MB/s | 87 KB / 3.1 MB = **0.028s** | Mobile urbain ✅ |
| **3G (3 Mbps)** | 375 KB/s | 87 KB / 375 KB = **0.23s** | Mobile rural ⚠️ |
| **2G (0.5 Mbps)** | 62.5 KB/s | 87 KB / 62.5 KB = **1.4s** | Connexion faible ❌ |

### Temps de chargement total

```
FCP (First Contentful Paint) =
  DNS lookup (50ms) +
  TCP handshake (100ms) +
  TLS handshake (100ms) +
  TTFB (200ms) +
  Download (28ms @ 4G) +
  Parse JS (150ms) +
  React hydration (300ms)

Total FCP = 928ms ≈ 0.93s ✅

LCP (Largest Contentful Paint) =
  FCP (928ms) +
  Load images (500ms) +
  Render (200ms)

Total LCP = 1,628ms ≈ 1.63s ✅

Objectif:
FCP < 1.8s ✅ (0.93s, 48% plus rapide)
LCP < 2.5s ✅ (1.63s, 35% plus rapide)
```

### Comparaison avec bundle non optimisé

```
Bundle non optimisé: 2,600 KB

Temps téléchargement @ 4G:
2,600 KB / 3.1 MB = 0.84s (vs 0.028s)

FCP total: 928ms + (840 - 28)ms = 1,740ms ≈ 1.74s
LCP total: 1,740ms + 700ms = 2,440ms ≈ 2.44s

Gain avec optimisation:
FCP: (1,740 - 928) / 1,740 × 100 = 47% plus rapide ✅
LCP: (2,440 - 1,628) / 2,440 × 100 = 33% plus rapide ✅
```

---

## 🧮 Calcul 3 : Coûts d'hébergement et ROI

### Comparaison des coûts annuels

**Scénario**: 100,000 visiteurs/mois, 3 GB de bande passante/utilisateur

**Vercel (Free)** :
```
Bande passante: 100 GB/mois inclus
Trafic réel: 100,000 × 87 KB = 8.7 GB/mois ✅ (dans la limite)

Coût: 0€/an
```

**Vercel (Pro - si dépassement)** :
```
Coût: 20$/mois × 12 = 240$/an ≈ 220€/an
```

**Netlify (Free)** :
```
Bande passante: 100 GB/mois inclus
Trafic: 8.7 GB/mois ✅

Coût: 0€/an
```

**AWS S3 + CloudFront** :
```
S3 storage (10 GB): 0.23$/mois
CloudFront (8.7 GB × 12 mois = 104 GB/an):
- First 10 TB: 0.085$/GB
- 104 GB × 0.085$ = 8.84$/mois

Total: (0.23 + 8.84) × 12 = 108.84$/an ≈ 100€/an
```

**Serveur VPS traditionnel** :
```
VPS (2 vCPU, 4 GB RAM): 20€/mois
Maintenance: 2h/mois × 50€/h = 100€/mois
SSL: Gratuit (Let's Encrypt)

Total: (20 + 100) × 12 = 1,440€/an ❌
```

### ROI de l'optimisation bundle

**Économie de bande passante** :
```
Avant optimisation: 2,600 KB/utilisateur
Après optimisation: 87 KB/utilisateur

Économie: (2,600 - 87) KB = 2,513 KB/utilisateur

Pour 100,000 utilisateurs/mois:
Économie mensuelle: 100,000 × 2,513 KB = 245 GB
Économie annuelle: 245 × 12 = 2,940 GB ≈ 3 TB

Coût économisé (CloudFront):
3,000 GB × 0.085$/GB = 255$/an ≈ 235€/an
```

**Temps développeur investi** :
```
Optimisation bundle: 4h × 50€/h = 200€
Setup CI/CD: 3h × 50€/h = 150€
Total investissement: 350€

ROI = (235€ - 350€) / 350€ × 100 = -33% (break-even après 1.5 an)

Mais gains indirects:
- Meilleure UX → +15% conversion → +3,000€/an
- Meilleur SEO → +10% trafic → +2,000€/an
- Moins d'abandon → +5% retention → +1,500€/an

Total gains: 6,500€/an

ROI réel = (6,500 - 350) / 350 × 100 = 1,757% ✅
```

---

## 🧮 Calcul 4 : Impact SEO et Core Web Vitals

### Score Google PageSpeed avant/après

**Avant optimisation** :
```
Performance: 72/100 ⚠️
FCP: 2.8s
LCP: 4.2s
CLS: 0.15
TTI: 5.1s

Pénalité SEO: -20 positions (estimation)
```

**Après optimisation** :
```
Performance: 96/100 ✅
FCP: 0.93s (-67%)
LCP: 1.63s (-61%)
CLS: 0.05 (-67%)
TTI: 2.4s (-53%)

Amélioration SEO: +15 positions (estimation)
```

### Impact sur le trafic organique

```
Positions gagnées: +15 (moyenne de #35 à #20)

CTR moyen:
- Position #35: 0.5%
- Position #20: 2.0%

Pour 1,000,000 recherches/mois:
Avant: 1,000,000 × 0.5% = 5,000 visites
Après: 1,000,000 × 2.0% = 20,000 visites

Gain: +15,000 visites/mois (+300%)

Valeur (à 2€/visite):
15,000 × 2€ × 12 mois = 360,000€/an de trafic organique gagné 🚀
```

---

## 🧮 Calcul 5 : Temps gagné avec CI/CD

### Avant CI/CD (déploiement manuel)

```
Étapes manuelles:
1. git pull → 30s
2. npm install → 1min 30s
3. npm run build → 1min 30s
4. Vérifier build → 1min
5. Upload via FTP/SSH → 2min
6. Tester en prod → 2min
7. Rollback si bug → 5min (si nécessaire)

Temps moyen par déploiement: 8min 30s

Déploiements/semaine: 10
Temps/semaine: 10 × 8.5min = 85min ≈ 1h25
Temps/an: 85 × 52 = 4,420min ≈ 74h

Coût: 74h × 50€/h = 3,700€/an ❌
```

### Avec CI/CD (automatisé)

```
Étapes automatiques:
1. git push → 10s
2. GitHub Actions → 2min 15s (auto)
3. Vercel deploy → 30s (auto)

Temps développeur actif: 10s (juste git push!)

Déploiements/semaine: 10
Temps/semaine: 10 × 10s = 1min 40s
Temps/an: 100 × 52 = 5,200s ≈ 87min ≈ 1.5h

Coût: 1.5h × 50€/h = 75€/an ✅
```

### Économie avec CI/CD

```
Temps économisé: 74h - 1.5h = 72.5h/an
Coût économisé: 3,700€ - 75€ = 3,625€/an

Investissement CI/CD:
Setup initial: 3h × 50€/h = 150€
Maintenance: 1h/an × 50€/h = 50€/an

ROI = (3,625 - 150 - 50) / 200 × 100 = 1,712% ✅

Break-even: 200€ / (3,625€/an) = 0.055 an ≈ 20 jours
```

**Interprétation** : CI/CD **rentabilisé en 20 jours**, puis économie de **3,625€/an** ! 💰

---

## 📊 Diagramme : Optimisations et leur impact

```
Impact des optimisations
     ↑
     │                      ┌─────────────────────┐
Très │                      │ Code Splitting      │
Élevé│                      │ Lazy Loading        │
     │                      │ -40% FCP            │
     │          ┌───────────┴─────────────────────┤
     │          │                                 │
     │          │  Minification + Tree-shaking    │
Élevé│          │  -60% bundle size               │
     │          │                                 │
     │  ┌───────┴─────────────────────────────────┤
     │  │                                         │
Moyen│  │  Compression Gzip/Brotli                │
     │  │  -70% transfer size                     │
     │  │                                         │
     │  └─────────────────────────────────────────┘
Faible│  Image optimization
     │  CDN caching
     │  React.memo
     │
     └────────┬────────┬────────┬────────┬────────→
          Facile  Moyen  Difficile  Effort
                       Complexité

Recommandation:
1. Minification + Gzip (facile, impact élevé) ⭐
2. Code splitting (moyen, impact très élevé) ⭐
3. CDN (facile, impact moyen) ⭐
```

---

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

## 📚 Résumé du chapitre

Dans ce chapitre, nous avons préparé notre app pour la production :

✅ **Préparation** : Variables, optimisations, métadonnées
✅ **Build** : Minification, purge, code splitting
✅ **Performance** : Lighthouse, Web Vitals, optimisations
✅ **Déploiement** : Vercel, Netlify, CI/CD
✅ **Monitoring** : Sentry, Analytics

---

## 📊 Métriques du déploiement

| Métrique | Avant optimisation | Après optimisation | Amélioration |
|----------|-------------------|-------------------|--------------|
| **Bundle size** | 2,600 KB | 87 KB (gzip) | **-96.7%** 🚀 |
| **FCP** | 2.8s | 0.93s | **-67%** ⚡ |
| **LCP** | 4.2s | 1.63s | **-61%** ⚡ |
| **TTI** | 5.1s | 2.4s | **-53%** ⚡ |
| **Lighthouse** | 72/100 | 96/100 | **+33%** ✅ |
| **Position SEO** (moy) | #35 | #20 | **+15 positions** 📈 |
| **Trafic organique** | 5,000/mois | 20,000/mois | **+300%** 📊 |
| **Temps déploiement** | 8min 30s | 10s (dev) | **-98.8%** ⏱️ |
| **Coût hébergement** | 1,440€/an (VPS) | 0€/an (Vercel free) | **-100%** 💰 |

**Score global** : **96/100** 🏆

---

## 📊 Checklist : Déploiement production

### 🎯 Phase 1 : Préparation (2h)

#### Variables d'environnement
- [ ] Créer `.env.production` avec toutes les variables
- [ ] Valider les variables au build (`import.meta.env`)
- [ ] Ajouter variables sur Vercel/Netlify dashboard
- [ ] Tester avec variables de production localement

#### Optimisations code
- [ ] Installer `vite-plugin-remove-console`
- [ ] Config Terser pour drop console/debugger
- [ ] Vérifier aucun `console.log` critique
- [ ] Supprimer imports inutilisés

#### Images & Assets
- [ ] Installer `vite-plugin-imagemin`
- [ ] Convertir images en WebP si possible
- [ ] Compresser images (qualité 80-90)
- [ ] Ajouter `loading="lazy"` sur images

#### Métadonnées SEO
- [ ] Title, description, keywords
- [ ] Open Graph (og:title, og:image, og:url)
- [ ] Twitter Card
- [ ] Favicon (16x16, 32x32, 192x192, 512x512)
- [ ] manifest.json (PWA optional)

---

### 🎯 Phase 2 : Build optimisé (1h)

#### Configuration Vite
- [ ] `target: 'esnext'` pour navigateurs modernes
- [ ] `minify: 'terser'` avec options compress
- [ ] `manualChunks` pour vendor splitting
- [ ] `chunkSizeWarningLimit: 1000`

#### Tailwind purge
- [ ] Vérifier `content` paths dans `tailwind.config.js`
- [ ] Inclure tous fichiers `.tsx`, `.ts`, `.html`
- [ ] Tester build (classes utilisées conservées)

#### Code splitting
- [ ] Lazy loading pages avec `React.lazy()`
- [ ] `<Suspense>` avec fallback loading
- [ ] Split vendors (react, ui, animation)
- [ ] Vérifier chunks générés (`dist/assets/`)

#### Bundle analysis
- [ ] Installer `rollup-plugin-visualizer`
- [ ] Générer rapport (`npm run build`)
- [ ] Identifier gros modules
- [ ] Optimiser si > 250 KB (gzippé)

---

### 🎯 Phase 3 : Tests performance (1h)

#### Lighthouse audit
- [ ] `npm run build && npm run preview`
- [ ] Chrome DevTools → Lighthouse
- [ ] Score Performance > 90
- [ ] Score Accessibility > 90
- [ ] Score Best Practices > 90
- [ ] Score SEO > 90

#### Web Vitals
- [ ] Installer `web-vitals` package
- [ ] Mesurer FCP, LCP, FID, CLS, TTFB
- [ ] FCP < 1.8s ✅
- [ ] LCP < 2.5s ✅
- [ ] FID < 100ms ✅
- [ ] CLS < 0.1 ✅

#### React Performance
- [ ] React DevTools Profiler
- [ ] Identifier composants lents
- [ ] Ajouter `memo()` si nécessaire
- [ ] Vérifier re-renders inutiles

---

### 🎯 Phase 4 : Déploiement (30min)

#### Choix plateforme
- [ ] **Vercel** (recommandé) : simplicité, analytics
- [ ] **Netlify** : alternative, split testing
- [ ] **GitHub Pages** : projets open-source
- [ ] **AWS S3+CloudFront** : contrôle total

#### Setup Vercel/Netlify
- [ ] Créer compte
- [ ] Connecter repository GitHub
- [ ] Config build command: `npm run build`
- [ ] Config output directory: `dist`
- [ ] Config redirects SPA (/* → /index.html)
- [ ] Ajouter domaine custom (optional)

#### Premier déploiement
- [ ] Push sur `main` branch
- [ ] Vérifier build réussi
- [ ] Tester preview URL
- [ ] Valider production URL
- [ ] Tester toutes les pages

---

### 🎯 Phase 5 : CI/CD (2h)

#### GitHub Actions
- [ ] Créer `.github/workflows/deploy.yml`
- [ ] Steps: checkout, setup node, install, test, build
- [ ] Trigger sur `push` to `main`
- [ ] Ajouter secrets (VERCEL_TOKEN, etc.)
- [ ] Tester workflow complet

#### Tests automatiques
- [ ] Linter (ESLint)
- [ ] Tests unitaires (Vitest)
- [ ] Build validation
- [ ] Bundle size check

#### Preview deployments
- [ ] Config preview pour PRs
- [ ] Review URL générée automatiquement
- [ ] Tests automatiques sur PR
- [ ] Merge → déploiement auto prod

---

### 🎯 Phase 6 : Monitoring (1h)

#### Error tracking (Sentry)
- [ ] Créer compte Sentry
- [ ] Installer `@sentry/react`
- [ ] Config DSN
- [ ] Ajouter `ErrorBoundary`
- [ ] Tester capture erreur

#### Analytics (Google Analytics)
- [ ] Créer propriété GA4
- [ ] Installer `react-ga4`
- [ ] Config tracking ID
- [ ] Hook page views (`useLocation`)
- [ ] Tester events

#### Uptime monitoring
- [ ] UptimeRobot (gratuit) ou Pingdom
- [ ] Config check HTTP (5min interval)
- [ ] Alertes email si down
- [ ] Status page public (optional)

---

## 📊 Récapitulatif : Pipeline complet

```
LOCAL → GITHUB → CI/CD → VERCEL → CDN → USERS

1️⃣ LOCAL (Developer)
   ├── Code changes (30min)
   ├── git add/commit (30s)
   └── git push (10s)

2️⃣ GITHUB (Repository)
   └── Trigger webhook (instant)

3️⃣ CI/CD (GitHub Actions) — 2min 15s
   ├── Checkout (10s)
   ├── Setup Node (15s)
   ├── Install deps (30s)
   ├── Lint (10s)
   ├── Test (20s)
   ├── Build (45s)
   └── Analyze (5s)

4️⃣ VERCEL (Deployment) — 30s
   ├── Upload artifacts (10s)
   ├── Edge network sync (15s)
   └── DNS update (5s)

5️⃣ CDN (Global Edge) — < 10ms
   └── Cache 100+ locations

6️⃣ USERS (End-users)
   ├── FCP: 0.93s ✅
   ├── LCP: 1.63s ✅
   └── TTI: 2.4s ✅

TOTAL TIME: ~2min 45s from push to live 🚀
```

---

## 📊 Comparaison : Avant vs Après déploiement optimisé

| Aspect | Avant (Débutant) | Après (Professionnel) | Transformation |
|--------|-----------------|----------------------|----------------|
| **Bundle** | 2,600 KB | 87 KB (gzip) | **-96.7%** 🚀 |
| **FCP** | 2.8s | 0.93s | **-67%** ⚡ |
| **Score Lighthouse** | 72/100 | 96/100 | **+33%** ✅ |
| **Déploiement** | Manuel (8.5min) | Auto (10s) | **-98.8%** ⏱️ |
| **Coût hébergement** | 1,440€/an | 0€/an | **-100%** 💰 |
| **Uptime** | ~95% (VPS) | 99.9% (Vercel) | **+5%** 🛡️ |
| **SSL/HTTPS** | Config manuel | Auto | **Simplifié** 🔒 |
| **CDN** | Aucun | 100+ edge | **Global** 🌍 |
| **Rollback** | Manuel (15min) | 1-click (30s) | **-97%** ⏪ |
| **Monitoring** | Aucun | Sentry + GA | **Visibilité** 📊 |
| **SEO position** | #35 (0.5% CTR) | #20 (2.0% CTR) | **+300% trafic** 📈 |
| **Trafic organique** | 5,000/mois | 20,000/mois | **+15,000 visites** 🎯 |

**Progression globale** : De **amateur** à **production-ready** 🏆

---

## 📊 ROI global des optimisations

### Investissement initial

```
Temps développeur:
- Optimisation bundle: 4h
- Setup CI/CD: 3h
- Config monitoring: 1h
- Tests performance: 1h
Total: 9h × 50€/h = 450€
```

### Gains annuels

```
1. Hébergement économisé:
   VPS (1,440€) - Vercel Free (0€) = 1,440€/an

2. Temps déploiement économisé:
   74h - 1.5h = 72.5h × 50€/h = 3,625€/an

3. Bande passante économisée:
   3 TB × 0.085$/GB × 1.1€/$ = 235€/an

4. SEO + Conversion améliorés:
   - +15,000 visites/mois × 2€/visite = 30,000€/mois
   - +15% conversion → +3,000€/mois
   Total: 33,000€/mois × 12 = 396,000€/an

Total gains: 401,300€/an
```

### ROI final

```
ROI = (401,300 - 450) / 450 × 100 = 89,077% 🚀

Break-even: 450€ / (401,300€/12) = 0.013 mois ≈ 10 heures

Interprétation:
Les optimisations sont rentabilisées en 10 heures,
puis génèrent 401,300€/an de valeur ! 💰💰💰
```

**Note** : Les chiffres SEO sont très dépendants du contexte (niche, concurrence, etc.). Même avec des gains plus conservateurs (÷10), le ROI reste excellent : **8,900%**.

---

### Points clés à retenir

💡 **Bundle -96.7%** : Minification + Tree-shaking + Gzip transforme 2,600 KB en 87 KB

💡 **FCP 0.93s** : 67% plus rapide que sans optimisation, largement sous la cible de 1.8s

💡 **CI/CD = 10s** : Git push et c'est déployé automatiquement, vs 8.5min manuellement

💡 **Vercel/Netlify Free** : 100 GB/mois gratuit, CDN global, SSL auto, analytics intégré

💡 **ROI 89,077%** : Rentabilisé en 10h, puis 401,300€/an de gains (SEO + conversion + économies)

💡 **Lighthouse 96/100** : Performance + Accessibility + Best Practices + SEO tous excellents

💡 **Position SEO +15** : Core Web Vitals améliorés → meilleur ranking → +300% trafic organique

💡 **Monitoring essentiel** : Sentry (errors) + GA (analytics) + Web Vitals (performance) = visibilité totale

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

