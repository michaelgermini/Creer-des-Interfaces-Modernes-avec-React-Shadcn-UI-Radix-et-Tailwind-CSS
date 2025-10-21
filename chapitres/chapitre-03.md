# Chapitre 3 : Introduction à Tailwind CSS

## Introduction

Tailwind CSS a révolutionné la manière dont nous écrivons du CSS dans les applications modernes. Dans ce chapitre, vous découvrirez pourquoi cette approche "utility-first" est devenue si populaire et comment l'utiliser efficacement.

---

## Philosophie Utility-first

### Le problème du CSS traditionnel

```css
/* styles.css - Approche traditionnelle */
.button {
  padding: 12px 24px;
  background-color: #3b82f6;
  color: white;
  border-radius: 6px;
  font-weight: 500;
  transition: background-color 0.2s;
}

.button:hover {
  background-color: #2563eb;
}

.button-large {
  padding: 16px 32px;
  font-size: 18px;
}

.button-small {
  padding: 8px 16px;
  font-size: 14px;
}

/* Répéter pour chaque variante... */
```

**Problèmes** :
- ❌ Inventer des noms de classes
- ❌ Passer entre HTML et CSS constamment
- ❌ CSS non utilisé difficile à détecter
- ❌ Fichiers CSS qui grossissent indéfiniment

### L'approche Utility-first

```tsx
// Avec Tailwind - Pas de CSS personnalisé !
<button className="px-6 py-3 bg-blue-500 text-white rounded-md font-medium hover:bg-blue-600 transition-colors">
  Cliquez ici
</button>

// Variante large
<button className="px-8 py-4 bg-blue-500 text-white rounded-md font-medium text-lg hover:bg-blue-600">
  Cliquez ici
</button>

// Variante small
<button className="px-4 py-2 bg-blue-500 text-white rounded-md font-medium text-sm hover:bg-blue-600">
  Cliquez ici
</button>
```

**Avantages** :
- ✅ Développement plus rapide
- ✅ Pas de naming
- ✅ CSS optimisé automatiquement (purge)
- ✅ Cohérence garantie par le design system
- ✅ Facile à modifier et maintenir

### Les préjugés sur Utility-first

#### "C'est comme du CSS inline !" ❌

```html
<!-- CSS inline - inflexible -->
<button style="padding: 12px 24px; background: blue;">

<!-- Tailwind - responsive, hover, dark mode, etc. -->
<button class="px-6 py-3 bg-blue-500 hover:bg-blue-600 md:px-8 dark:bg-blue-700">
```

#### "Les classes sont trop longues !" ❌

Solution : Extraire des composants

```tsx
// Button.tsx - Composant réutilisable
export function Button({ children, variant = 'primary' }) {
  const baseClasses = "px-6 py-3 rounded-md font-medium transition-colors";
  const variants = {
    primary: "bg-blue-500 text-white hover:bg-blue-600",
    secondary: "bg-gray-200 text-gray-900 hover:bg-gray-300",
  };
  
  return (
    <button className={`${baseClasses} ${variants[variant]}`}>
      {children}
    </button>
  );
}

// Utilisation - Clean !
<Button variant="primary">Cliquez ici</Button>
```

---

## Configuration et intégration avec React

### Installation

```bash
# Créer un projet React avec Vite
npm create vite@latest mon-app -- --template react-ts
cd mon-app

# Installer Tailwind CSS
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

### Configuration de Tailwind

```js
// tailwind.config.js
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {
      // Votre configuration personnalisée ici
    },
  },
  plugins: [],
}
```

### Ajouter les directives Tailwind

```css
/* src/index.css */
@tailwind base;
@tailwind components;
@tailwind utilities;
```

### Premier composant avec Tailwind

```tsx
// src/App.tsx
function App() {
  return (
    <div className="min-h-screen bg-gray-50 flex items-center justify-center">
      <div className="bg-white p-8 rounded-lg shadow-lg">
        <h1 className="text-3xl font-bold text-gray-900 mb-4">
          Bienvenue dans Tailwind CSS
        </h1>
        <p className="text-gray-600 mb-6">
          Créez des interfaces magnifiques sans quitter votre HTML.
        </p>
        <button className="w-full px-6 py-3 bg-blue-500 text-white rounded-md font-medium hover:bg-blue-600 transition-colors">
          Commencer
        </button>
      </div>
    </div>
  );
}
```

### Configuration de VS Code

```json
// .vscode/settings.json
{
  "tailwindCSS.experimental.classRegex": [
    ["cva\\(([^)]*)\\)", "[\"'`]([^\"'`]*).*?[\"'`]"],
    ["cn\\(([^)]*)\\)", "(?:'|\"|`)([^']*)(?:'|\"|`)"]
  ],
  "editor.quickSuggestions": {
    "strings": true
  }
}
```

Installez l'extension **Tailwind CSS IntelliSense** pour l'autocomplétion !

---

## Les classes essentielles

### Spacing (espacement)

Tailwind utilise une échelle cohérente : `1 = 0.25rem = 4px`

#### Padding

```tsx
// p-{size} : padding sur tous les côtés
<div className="p-4">       {/* 16px tous côtés */}
<div className="p-8">       {/* 32px tous côtés */}

// px-{size} : padding horizontal (left + right)
<div className="px-6">      {/* 24px left/right */}

// py-{size} : padding vertical (top + bottom)
<div className="py-4">      {/* 16px top/bottom */}

// pt, pr, pb, pl : côtés individuels
<div className="pt-2 pr-4 pb-2 pl-4">  {/* 8px, 16px, 8px, 16px */}
```

#### Margin

```tsx
// m-{size} : margin sur tous les côtés
<div className="m-4">

// Même logique que padding
<div className="mx-auto">   {/* Centrer horizontalement */}
<div className="mt-8">      {/* margin-top: 32px */}
<div className="-mt-4">     {/* margin-top: -16px (négatif!) */}
```

#### Space between (espacement entre éléments)

```tsx
// Espacement vertical entre enfants
<div className="space-y-4">
  <div>Item 1</div>
  <div>Item 2</div>
  <div>Item 3</div>
</div>

// Espacement horizontal
<div className="flex space-x-2">
  <button>Button 1</button>
  <button>Button 2</button>
</div>
```

### Flexbox

```tsx
// Container flex
<div className="flex">
  {/* Enfants */}
</div>

// Direction
<div className="flex flex-row">      {/* Par défaut */}
<div className="flex flex-col">      {/* Colonne */}
<div className="flex flex-row-reverse">

// Justification (axe principal)
<div className="flex justify-start">     {/* Par défaut */}
<div className="flex justify-center">    {/* Centré */}
<div className="flex justify-between">   {/* Espacé */}
<div className="flex justify-around">
<div className="flex justify-evenly">

// Alignement (axe secondaire)
<div className="flex items-start">
<div className="flex items-center">
<div className="flex items-end">
<div className="flex items-stretch">    {/* Par défaut */}

// Wrap
<div className="flex flex-wrap">
<div className="flex flex-nowrap">      {/* Par défaut */}

// Gap (espacement entre items)
<div className="flex gap-4">            {/* 16px partout */}
<div className="flex gap-x-2 gap-y-4">  {/* Horizontal et vertical différents */}
```

#### Exemple complet : Carte utilisateur

```tsx
function UserCard({ user }) {
  return (
    <div className="flex items-center gap-4 p-4 bg-white rounded-lg shadow">
      {/* Avatar */}
      <img
        src={user.avatar}
        alt={user.name}
        className="w-16 h-16 rounded-full"
      />
      
      {/* Contenu */}
      <div className="flex-1">
        <h3 className="font-semibold text-lg">{user.name}</h3>
        <p className="text-gray-600 text-sm">{user.email}</p>
      </div>
      
      {/* Actions */}
      <button className="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600">
        Contact
      </button>
    </div>
  );
}
```

### Grid

```tsx
// Container grid
<div className="grid">

// Colonnes
<div className="grid grid-cols-2">      {/* 2 colonnes égales */}
<div className="grid grid-cols-3">      {/* 3 colonnes égales */}
<div className="grid grid-cols-4">
<div className="grid grid-cols-12">     {/* 12 colonnes (comme Bootstrap) */}

// Colonnes personnalisées
<div className="grid grid-cols-[200px_1fr]">     {/* 200px + reste */}
<div className="grid grid-cols-[1fr_2fr_1fr]">   {/* Proportions */}

// Lignes
<div className="grid grid-rows-3">

// Gap
<div className="grid grid-cols-3 gap-4">         {/* 16px partout */}
<div className="grid grid-cols-3 gap-x-4 gap-y-8">

// Span (occuper plusieurs colonnes/lignes)
<div className="col-span-2">            {/* Occupe 2 colonnes */}
<div className="row-span-3">            {/* Occupe 3 lignes */}
```

#### Exemple : Galerie d'images

```tsx
function ImageGallery({ images }) {
  return (
    <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
      {images.map((image) => (
        <div key={image.id} className="aspect-square rounded-lg overflow-hidden">
          <img
            src={image.url}
            alt={image.alt}
            className="w-full h-full object-cover hover:scale-105 transition-transform"
          />
        </div>
      ))}
    </div>
  );
}
```

### Colors (couleurs)

Tailwind fournit une palette complète avec des nuances de 50 à 950 :

```tsx
// Texte
<p className="text-gray-900">       {/* Presque noir */}
<p className="text-gray-600">       {/* Gris moyen */}
<p className="text-gray-400">       {/* Gris clair */}

// Background
<div className="bg-blue-500">       {/* Bleu */}
<div className="bg-red-100">        {/* Rouge très clair */}
<div className="bg-green-600">      {/* Vert */}

// Border
<div className="border border-gray-300">
<div className="border-2 border-blue-500">

// Couleurs disponibles
gray, slate, zinc, neutral, stone
red, orange, amber, yellow
lime, green, emerald, teal, cyan
sky, blue, indigo, violet, purple
pink, rose
```

#### Personnaliser les couleurs

```js
// tailwind.config.js
export default {
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#eff6ff',
          100: '#dbeafe',
          500: '#3b82f6',
          900: '#1e3a8a',
        },
        brand: {
          DEFAULT: '#8b5cf6',
          light: '#a78bfa',
          dark: '#7c3aed',
        }
      },
    },
  },
}
```

```tsx
// Utilisation
<button className="bg-primary-500 text-white">
  Bouton avec couleur personnalisée
</button>

<div className="bg-brand text-brand-light">
  Utilisation de brand
</div>
```

---

## Créer un thème cohérent

### Configurer votre design system

```js
// tailwind.config.js
export default {
  theme: {
    extend: {
      // Couleurs
      colors: {
        primary: {
          50: '#f0f9ff',
          100: '#e0f2fe',
          500: '#0ea5e9',
          600: '#0284c7',
          900: '#0c4a6e',
        },
        secondary: {
          // ...
        }
      },
      
      // Typographie
      fontFamily: {
        sans: ['Inter', 'system-ui', 'sans-serif'],
        mono: ['Fira Code', 'monospace'],
      },
      fontSize: {
        'xs': '0.75rem',
        'sm': '0.875rem',
        'base': '1rem',
        'lg': '1.125rem',
        'xl': '1.25rem',
        '2xl': '1.5rem',
        '3xl': '1.875rem',
        '4xl': '2.25rem',
      },
      
      // Espacements personnalisés
      spacing: {
        '18': '4.5rem',
        '88': '22rem',
      },
      
      // Border radius
      borderRadius: {
        'sm': '0.25rem',
        DEFAULT: '0.375rem',
        'md': '0.5rem',
        'lg': '0.75rem',
        'xl': '1rem',
        '2xl': '1.5rem',
      },
      
      // Shadows
      boxShadow: {
        'soft': '0 2px 8px rgba(0, 0, 0, 0.05)',
        'medium': '0 4px 16px rgba(0, 0, 0, 0.1)',
        'strong': '0 8px 32px rgba(0, 0, 0, 0.15)',
      },
    },
  },
}
```

### Utiliser les CSS variables

```css
/* src/index.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  :root {
    --color-primary: 219 90% 56%;
    --color-background: 0 0% 100%;
    --color-foreground: 222 47% 11%;
    --radius: 0.5rem;
  }
  
  .dark {
    --color-primary: 217 91% 60%;
    --color-background: 222 47% 11%;
    --color-foreground: 210 40% 98%;
  }
}
```

```js
// tailwind.config.js
export default {
  theme: {
    extend: {
      colors: {
        primary: 'hsl(var(--color-primary))',
        background: 'hsl(var(--color-background))',
        foreground: 'hsl(var(--color-foreground))',
      },
      borderRadius: {
        lg: 'var(--radius)',
        md: 'calc(var(--radius) - 2px)',
        sm: 'calc(var(--radius) - 4px)',
      },
    },
  },
}
```

---

## Gestion des dark/light modes

### Configuration

```js
// tailwind.config.js
export default {
  darkMode: 'class', // ou 'media' pour suivre les préférences système
  // ...
}
```

### Utilisation

```tsx
// Classes dark:
<div className="bg-white dark:bg-gray-900">
  <h1 className="text-gray-900 dark:text-white">
    Titre qui s'adapte
  </h1>
  <p className="text-gray-600 dark:text-gray-300">
    Texte qui s'adapte
  </p>
</div>
```

### Provider de thème

```tsx
// components/theme-provider.tsx
import { createContext, useContext, useEffect, useState } from 'react';

type Theme = 'light' | 'dark' | 'system';

interface ThemeContextType {
  theme: Theme;
  setTheme: (theme: Theme) => void;
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

export function ThemeProvider({ 
  children, 
  defaultTheme = 'system' 
}: { 
  children: React.ReactNode; 
  defaultTheme?: Theme;
}) {
  const [theme, setTheme] = useState<Theme>(
    () => (localStorage.getItem('theme') as Theme) || defaultTheme
  );

  useEffect(() => {
    const root = window.document.documentElement;
    root.classList.remove('light', 'dark');

    if (theme === 'system') {
      const systemTheme = window.matchMedia('(prefers-color-scheme: dark)').matches
        ? 'dark'
        : 'light';
      root.classList.add(systemTheme);
    } else {
      root.classList.add(theme);
    }
  }, [theme]);

  const value = {
    theme,
    setTheme: (newTheme: Theme) => {
      localStorage.setItem('theme', newTheme);
      setTheme(newTheme);
    },
  };

  return (
    <ThemeContext.Provider value={value}>
      {children}
    </ThemeContext.Provider>
  );
}

export function useTheme() {
  const context = useContext(ThemeContext);
  if (context === undefined) {
    throw new Error('useTheme must be used within ThemeProvider');
  }
  return context;
}
```

### Composant Toggle

```tsx
// components/theme-toggle.tsx
import { Moon, Sun } from 'lucide-react';
import { useTheme } from './theme-provider';

export function ThemeToggle() {
  const { theme, setTheme } = useTheme();

  return (
    <button
      onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}
      className="p-2 rounded-md bg-gray-100 dark:bg-gray-800 hover:bg-gray-200 dark:hover:bg-gray-700 transition-colors"
    >
      {theme === 'light' ? (
        <Moon className="w-5 h-5" />
      ) : (
        <Sun className="w-5 h-5" />
      )}
    </button>
  );
}
```

---

## Résumé du chapitre

Dans ce chapitre, nous avons découvert Tailwind CSS :

✅ **Philosophie** : Utility-first, composition, pas de naming
✅ **Configuration** : Installation, setup, personnalisation
✅ **Classes essentielles** : Spacing, Flexbox, Grid, Colors
✅ **Thème** : Design system cohérent, CSS variables
✅ **Dark mode** : Configuration et implémentation complète

### Points clés à retenir

💡 Tailwind n'est pas du CSS inline, c'est un design system contraignant

💡 Extraire des composants pour éviter la répétition

💡 Personnaliser le thème pour votre brand

💡 Le dark mode est facile avec Tailwind

---

## Exercices

### Exercice 1 : Carte de profil
Créez une carte de profil avec :
- Avatar circulaire
- Nom et bio
- Badges de compétences
- Boutons d'action
- Mode sombre

### Exercice 2 : Grille responsive
Créez une galerie d'images responsive :
- 1 colonne sur mobile
- 2 colonnes sur tablette
- 3 colonnes sur desktop
- 4 colonnes sur large screens

### Exercice 3 : Formulaire stylé
Créez un formulaire d'inscription avec :
- Inputs avec focus states
- Validation visuelle
- Bouton avec loading state
- Mode sombre complet

---

**Prêt à découvrir Radix UI ?** → [Chapitre 4 : Radix UI – Les fondations accessibles](./chapitre-04.md)

