# Chapitre 6 : Configuration avancée de Shadcn/UI

## Introduction

Dans ce chapitre, nous allons explorer en profondeur la configuration de Shadcn/UI. Vous apprendrez à personnaliser chaque aspect pour créer un design system unique à votre projet.

---

## Analyse détaillée de `components.json`

### Configuration complète expliquée

```json
{
  "$schema": "https://ui.shadcn.com/schema.json",
  "style": "default",
  "rsc": false,
  "tsx": true,
  "tailwind": {
    "config": "tailwind.config.js",
    "css": "src/index.css",
    "baseColor": "slate",
    "cssVariables": true,
    "prefix": ""
  },
  "aliases": {
    "components": "@/components",
    "utils": "@/lib/utils",
    "ui": "@/components/ui"
  }
}
```

### Propriété par propriété

#### `$schema`
Permet l'autocomplétion et la validation dans VS Code.

```json
"$schema": "https://ui.shadcn.com/schema.json"
```

#### `style`
Deux styles disponibles :

**"default"** :
- Espacement généreux
- Coins arrondis moyens
- Style moderne et aéré

**"new-york"** :
- Plus compact
- Coins plus arrondis
- Ombres plus subtiles

```bash
# Changer de style
npx shadcn-ui@latest init --style new-york
```

#### `rsc` (React Server Components)
Important pour Next.js App Router :

```json
// rsc: true
"use client"  // Ajouté automatiquement si nécessaire

// rsc: false
// Pas d'annotations "use client"
```

#### `tsx`
```json
// tsx: true → Fichiers .tsx
// tsx: false → Fichiers .jsx
```

---

## Gestion des alias et du typage

### Configuration de TypeScript

```json
// tsconfig.json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

### Configuration de Vite

```ts
// vite.config.ts
import path from "path"
import react from "@vitejs/plugin-react"
import { defineConfig } from "vite"

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./src"),
    },
  },
})
```

### Alias personnalisés

```json
// components.json
{
  "aliases": {
    "components": "@/components",
    "utils": "@/lib/utils",
    "ui": "@/components/ui",
    "hooks": "@/hooks",
    "lib": "@/lib",
    "types": "@/types"
  }
}
```

```tsx
// Utilisation
import { Button } from "@/components/ui/button"
import { useToast } from "@/hooks/use-toast"
import { cn } from "@/lib/utils"
import type { User } from "@/types/user"
```

---

## Choisir la palette de base et le style

### Base colors disponibles

Shadcn/UI offre plusieurs palettes neutres :

| Couleur | Caractère | Usage |
|---------|-----------|-------|
| `slate` | Bleuté, moderne | Défaut, interfaces tech |
| `gray` | Neutre pur | Universel |
| `zinc` | Légèrement chaud | Interfaces élégantes |
| `neutral` | Entre gray et zinc | Polyvalent |
| `stone` | Chaud, terreux | Interfaces organiques |

### Changer la base color

```bash
# À l'initialisation
npx shadcn-ui@latest init

# Après coup, modifier components.json
{
  "tailwind": {
    "baseColor": "zinc"  // Changer ici
  }
}

# Puis regénérer les CSS variables
```

### Personnaliser complètement les couleurs

```css
/* src/index.css */
@layer base {
  :root {
    /* Background & Foreground */
    --background: 0 0% 100%;
    --foreground: 222.2 47.4% 11.2%;

    /* Primary */
    --primary: 221.2 83.2% 53.3%;
    --primary-foreground: 210 40% 98%;

    /* Secondary */
    --secondary: 210 40% 96.1%;
    --secondary-foreground: 222.2 47.4% 11.2%;

    /* Muted */
    --muted: 210 40% 96.1%;
    --muted-foreground: 215.4 16.3% 46.9%;

    /* Accent */
    --accent: 210 40% 96.1%;
    --accent-foreground: 222.2 47.4% 11.2%;

    /* Destructive */
    --destructive: 0 84.2% 60.2%;
    --destructive-foreground: 210 40% 98%;

    /* Border */
    --border: 214.3 31.8% 91.4%;
    --input: 214.3 31.8% 91.4%;
    --ring: 221.2 83.2% 53.3%;

    /* Radius */
    --radius: 0.5rem;
  }

  .dark {
    --background: 222.2 84% 4.9%;
    --foreground: 210 40% 98%;

    --primary: 217.2 91.2% 59.8%;
    --primary-foreground: 222.2 47.4% 11.2%;

    --secondary: 217.2 32.6% 17.5%;
    --secondary-foreground: 210 40% 98%;

    --muted: 217.2 32.6% 17.5%;
    --muted-foreground: 215 20.2% 65.1%;

    --accent: 217.2 32.6% 17.5%;
    --accent-foreground: 210 40% 98%;

    --destructive: 0 62.8% 30.6%;
    --destructive-foreground: 210 40% 98%;

    --border: 217.2 32.6% 17.5%;
    --input: 217.2 32.6% 17.5%;
    --ring: 224.3 76.3% 48%;
  }
}
```

### Générer une palette personnalisée

```tsx
// Outil pour convertir hex en HSL
function hexToHSL(hex: string) {
  // Convertir hex en RGB
  const r = parseInt(hex.slice(1, 3), 16) / 255;
  const g = parseInt(hex.slice(3, 5), 16) / 255;
  const b = parseInt(hex.slice(5, 7), 16) / 255;

  // Trouver min et max
  const max = Math.max(r, g, b);
  const min = Math.min(r, g, b);
  let h = 0, s = 0, l = (max + min) / 2;

  if (max !== min) {
    const d = max - min;
    s = l > 0.5 ? d / (2 - max - min) : d / (max + min);
    
    switch (max) {
      case r: h = ((g - b) / d + (g < b ? 6 : 0)) / 6; break;
      case g: h = ((b - r) / d + 2) / 6; break;
      case b: h = ((r - g) / d + 4) / 6; break;
    }
  }

  return {
    h: Math.round(h * 360),
    s: Math.round(s * 100),
    l: Math.round(l * 100)
  };
}

// Utilisation
const brandColor = hexToHSL('#6366f1'); // Indigo-500
// { h: 239, s: 84, l: 67 }

// Dans votre CSS
// --primary: 239 84% 67%;
```

---

## Activer/désactiver les CSS variables

### Avec CSS variables (recommandé)

```json
// components.json
{
  "tailwind": {
    "cssVariables": true
  }
}
```

**Avantages** :
- ✅ Changement de thème dynamique (JS)
- ✅ Dark mode facile
- ✅ Thèmes multiples
- ✅ Personnalisation par utilisateur

```tsx
// Changer les couleurs dynamiquement
document.documentElement.style.setProperty('--primary', '210 100% 50%');
```

```css
/* Styles générés */
.bg-primary {
  background-color: hsl(var(--primary));
}
```

### Sans CSS variables

```json
// components.json
{
  "tailwind": {
    "cssVariables": false
  }
}
```

**Avantages** :
- ✅ Plus simple
- ✅ Pas de variables à gérer

**Inconvénients** :
- ❌ Pas de changement dynamique
- ❌ Dark mode plus complexe

```tsx
// Styles générés directement
.bg-primary {
  background-color: hsl(221.2 83.2% 53.3%);
}

.dark .bg-primary {
  background-color: hsl(217.2 91.2% 59.8%);
}
```

### Thèmes multiples avec CSS variables

```css
/* src/index.css */
@layer base {
  /* Thème par défaut */
  :root {
    --primary: 221.2 83.2% 53.3%;
  }

  /* Thème alternatif */
  .theme-green {
    --primary: 142 71% 45%;
  }

  .theme-purple {
    --primary: 262 83% 58%;
  }

  /* Dark variants */
  .dark.theme-green {
    --primary: 142 71% 55%;
  }
}
```

```tsx
// Provider de thème
function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('default');
  
  useEffect(() => {
    document.documentElement.classList.remove('theme-green', 'theme-purple');
    if (theme !== 'default') {
      document.documentElement.classList.add(`theme-${theme}`);
    }
  }, [theme]);
  
  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}
```

---

## Intégration complète avec Tailwind

### Configuration Tailwind optimale

```js
// tailwind.config.js
import tailwindcssAnimate from "tailwindcss-animate"

/** @type {import('tailwindcss').Config} */
export default {
  darkMode: ["class"],
  content: [
    './pages/**/*.{ts,tsx}',
    './components/**/*.{ts,tsx}',
    './app/**/*.{ts,tsx}',
    './src/**/*.{ts,tsx}',
  ],
  theme: {
    container: {
      center: true,
      padding: "2rem",
      screens: {
        "2xl": "1400px",
      },
    },
    extend: {
      colors: {
        border: "hsl(var(--border))",
        input: "hsl(var(--input))",
        ring: "hsl(var(--ring))",
        background: "hsl(var(--background))",
        foreground: "hsl(var(--foreground))",
        primary: {
          DEFAULT: "hsl(var(--primary))",
          foreground: "hsl(var(--primary-foreground))",
        },
        secondary: {
          DEFAULT: "hsl(var(--secondary))",
          foreground: "hsl(var(--secondary-foreground))",
        },
        destructive: {
          DEFAULT: "hsl(var(--destructive))",
          foreground: "hsl(var(--destructive-foreground))",
        },
        muted: {
          DEFAULT: "hsl(var(--muted))",
          foreground: "hsl(var(--muted-foreground))",
        },
        accent: {
          DEFAULT: "hsl(var(--accent))",
          foreground: "hsl(var(--accent-foreground))",
        },
        popover: {
          DEFAULT: "hsl(var(--popover))",
          foreground: "hsl(var(--popover-foreground))",
        },
        card: {
          DEFAULT: "hsl(var(--card))",
          foreground: "hsl(var(--card-foreground))",
        },
      },
      borderRadius: {
        lg: "var(--radius)",
        md: "calc(var(--radius) - 2px)",
        sm: "calc(var(--radius) - 4px)",
      },
      keyframes: {
        "accordion-down": {
          from: { height: "0" },
          to: { height: "var(--radix-accordion-content-height)" },
        },
        "accordion-up": {
          from: { height: "var(--radix-accordion-content-height)" },
          to: { height: "0" },
        },
      },
      animation: {
        "accordion-down": "accordion-down 0.2s ease-out",
        "accordion-up": "accordion-up 0.2s ease-out",
      },
    },
  },
  plugins: [tailwindcssAnimate],
}
```

### Ajouter des utilitaires personnalisés

```js
// tailwind.config.js
export default {
  theme: {
    extend: {
      // Fonts personnalisées
      fontFamily: {
        sans: ['Inter', 'system-ui', 'sans-serif'],
        mono: ['Fira Code', 'monospace'],
      },
      
      // Tailles personnalisées
      fontSize: {
        'xxs': '0.65rem',
      },
      
      // Espacements personnalisés
      spacing: {
        '18': '4.5rem',
        '88': '22rem',
      },
      
      // Animations personnalisées
      keyframes: {
        'fade-in': {
          from: { opacity: 0 },
          to: { opacity: 1 },
        },
        'slide-in': {
          from: { transform: 'translateY(-10px)', opacity: 0 },
          to: { transform: 'translateY(0)', opacity: 1 },
        },
      },
      animation: {
        'fade-in': 'fade-in 0.2s ease-out',
        'slide-in': 'slide-in 0.3s ease-out',
      },
    },
  },
}
```

### Classes utilitaires custom

```css
/* src/index.css */
@layer utilities {
  .text-balance {
    text-wrap: balance;
  }
  
  .scrollbar-hide {
    -ms-overflow-style: none;
    scrollbar-width: none;
  }
  
  .scrollbar-hide::-webkit-scrollbar {
    display: none;
  }
}

@layer components {
  .btn-glow {
    @apply relative before:absolute before:inset-0 before:rounded-lg before:bg-primary/20 before:blur-xl;
  }
}
```

---

## Configuration avancée : Prefix

### Ajouter un prefix aux classes Tailwind

Utile si vous intégrez Tailwind dans un projet existant avec du CSS.

```json
// components.json
{
  "tailwind": {
    "prefix": "tw-"
  }
}
```

```js
// tailwind.config.js
export default {
  prefix: 'tw-',
  // ...
}
```

```tsx
// Utilisation
<button className="tw-px-4 tw-py-2 tw-bg-primary tw-text-white">
  Button
</button>
```

---

## Configuration avancée : Optimisation

### Purge CSS optimal

```js
// tailwind.config.js
export default {
  content: [
    './index.html',
    './src/**/*.{js,ts,jsx,tsx}',
    // ⚠️ Ne pas inclure node_modules sauf besoin spécifique
  ],
  safelist: [
    // Classes générées dynamiquement
    {
      pattern: /bg-(red|green|blue)-(100|500|900)/,
      variants: ['hover', 'focus'],
    },
  ],
}
```

### Mode JIT (Just-In-Time)

Par défaut depuis Tailwind v3 :

```js
// tailwind.config.js
export default {
  // Mode JIT activé par défaut
  // Génère uniquement les classes utilisées
  // Compilation ultra-rapide
}
```

**Avantages** :
- ⚡ Build instantané
- 📦 CSS minimal
- 🎨 Valeurs arbitraires : `w-[137px]`, `bg-[#1da1f2]`

---

## Résumé du chapitre

Dans ce chapitre, nous avons exploré la configuration avancée de Shadcn/UI :

✅ **`components.json`** : Chaque propriété expliquée
✅ **Alias** : Configuration TypeScript et Vite
✅ **Palettes** : Choisir et personnaliser les couleurs
✅ **CSS Variables** : Thèmes dynamiques vs statiques
✅ **Tailwind** : Configuration complète et optimisation

### Points clés à retenir

💡 Les CSS variables permettent les thèmes dynamiques

💡 Choisir la base color selon le caractère de votre app

💡 Les alias simplifient les imports dans tout le projet

💡 Le mode JIT de Tailwind optimise automatiquement le CSS

---

## Exercices

### Exercice 1 : Thème personnalisé
Créez un thème complet avec :
- Couleur primaire : Orange (#f97316)
- Couleur secondaire : Teal (#14b8a6)
- Mode clair et sombre
- CSS variables configurées

### Exercice 2 : Multi-thèmes
Implémentez 3 thèmes :
- Default (Bleu)
- Nature (Vert)
- Sunset (Orange-Rouge)
- Sélecteur de thème avec preview

### Exercice 3 : Configuration complète
Configurez un projet avec :
- Alias personnalisés (@ui, @hooks, @lib)
- Fonts personnalisées (Google Fonts)
- Animations custom
- Purge CSS optimisée

---

**Prêt à architecturer votre projet ?** → [Chapitre 7 : Architecture d'un projet React moderne](./chapitre-07.md)

