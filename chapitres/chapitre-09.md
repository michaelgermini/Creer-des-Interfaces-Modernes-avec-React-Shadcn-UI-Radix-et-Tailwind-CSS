# Chapitre 9 : Styles, thèmes et tokens

## Introduction

Un système de thèmes bien conçu permet de changer l'apparence de votre application sans toucher au code des composants. Dans ce chapitre, vous apprendrez à créer un système de thèmes flexible et maintenable.

---

## Utiliser les variables CSS et les tokens

### Qu'est-ce qu'un token de design ?

Un **token** est une valeur atomique qui définit un aspect visuel de votre interface :
- Couleurs
- Espacements
- Typographie
- Ombres
- Bordures

### Définir les tokens avec CSS Variables

```css
/* src/styles/tokens.css */
@layer base {
  :root {
    /* === Colors === */
    
    /* Brand */
    --color-brand-50: 239 246 255;
    --color-brand-100: 219 234 254;
    --color-brand-500: 59 130 246;
    --color-brand-600: 37 99 235;
    --color-brand-900: 30 58 138;
    
    /* Semantic colors */
    --color-background: 0 0% 100%;
    --color-foreground: 222 47% 11%;
    --color-primary: 221 83% 53%;
    --color-primary-foreground: 210 40% 98%;
    --color-secondary: 210 40% 96%;
    --color-secondary-foreground: 222 47% 11%;
    
    /* State colors */
    --color-success: 142 71% 45%;
    --color-warning: 38 92% 50%;
    --color-error: 0 84% 60%;
    --color-info: 199 89% 48%;
    
    /* === Spacing === */
    --spacing-xs: 0.25rem;    /* 4px */
    --spacing-sm: 0.5rem;     /* 8px */
    --spacing-md: 1rem;       /* 16px */
    --spacing-lg: 1.5rem;     /* 24px */
    --spacing-xl: 2rem;       /* 32px */
    --spacing-2xl: 3rem;      /* 48px */
    
    /* === Typography === */
    --font-sans: 'Inter', system-ui, sans-serif;
    --font-mono: 'Fira Code', monospace;
    
    --text-xs: 0.75rem;
    --text-sm: 0.875rem;
    --text-base: 1rem;
    --text-lg: 1.125rem;
    --text-xl: 1.25rem;
    --text-2xl: 1.5rem;
    
    /* === Borders === */
    --radius-sm: 0.25rem;
    --radius-md: 0.5rem;
    --radius-lg: 0.75rem;
    --radius-xl: 1rem;
    
    /* === Shadows === */
    --shadow-sm: 0 1px 2px 0 rgb(0 0 0 / 0.05);
    --shadow-md: 0 4px 6px -1px rgb(0 0 0 / 0.1);
    --shadow-lg: 0 10px 15px -3px rgb(0 0 0 / 0.1);
    --shadow-xl: 0 20px 25px -5px rgb(0 0 0 / 0.1);
  }
}
```

### Utiliser les tokens dans Tailwind

```js
// tailwind.config.js
export default {
  theme: {
    extend: {
      colors: {
        brand: {
          50: 'hsl(var(--color-brand-50))',
          100: 'hsl(var(--color-brand-100))',
          500: 'hsl(var(--color-brand-500))',
          600: 'hsl(var(--color-brand-600))',
          900: 'hsl(var(--color-brand-900))',
        },
        background: 'hsl(var(--color-background))',
        foreground: 'hsl(var(--color-foreground))',
        primary: {
          DEFAULT: 'hsl(var(--color-primary))',
          foreground: 'hsl(var(--color-primary-foreground))',
        },
        success: 'hsl(var(--color-success))',
        warning: 'hsl(var(--color-warning))',
        error: 'hsl(var(--color-error))',
        info: 'hsl(var(--color-info))',
      },
      spacing: {
        xs: 'var(--spacing-xs)',
        sm: 'var(--spacing-sm)',
        md: 'var(--spacing-md)',
        lg: 'var(--spacing-lg)',
        xl: 'var(--spacing-xl)',
        '2xl': 'var(--spacing-2xl)',
      },
      borderRadius: {
        sm: 'var(--radius-sm)',
        md: 'var(--radius-md)',
        lg: 'var(--radius-lg)',
        xl: 'var(--radius-xl)',
      },
      boxShadow: {
        sm: 'var(--shadow-sm)',
        md: 'var(--shadow-md)',
        lg: 'var(--shadow-lg)',
        xl: 'var(--shadow-xl)',
      },
    },
  },
}
```

### Utilisation dans les composants

```tsx
// Les tokens sont maintenant disponibles via Tailwind
function Card({ children }) {
  return (
    <div className="bg-background text-foreground p-md rounded-lg shadow-md">
      {children}
    </div>
  )
}

function PrimaryButton({ children }) {
  return (
    <button className="bg-primary text-primary-foreground px-lg py-sm rounded-md">
      {children}
    </button>
  )
}
```

---

## Changer de thème dynamiquement

### ThemeProvider avec Context

```tsx
// components/theme-provider.tsx
import { createContext, useContext, useEffect, useState } from 'react'

type Theme = 'light' | 'dark' | 'system'

interface ThemeContextType {
  theme: Theme
  setTheme: (theme: Theme) => void
  actualTheme: 'light' | 'dark'
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined)

export function ThemeProvider({
  children,
  defaultTheme = 'system',
  storageKey = 'app-theme',
}: {
  children: React.ReactNode
  defaultTheme?: Theme
  storageKey?: string
}) {
  const [theme, setTheme] = useState<Theme>(
    () => (localStorage.getItem(storageKey) as Theme) || defaultTheme
  )

  const [actualTheme, setActualTheme] = useState<'light' | 'dark'>('light')

  useEffect(() => {
    const root = window.document.documentElement

    // Retirer les anciennes classes
    root.classList.remove('light', 'dark')

    // Déterminer le thème effectif
    let effectiveTheme: 'light' | 'dark'

    if (theme === 'system') {
      effectiveTheme = window.matchMedia('(prefers-color-scheme: dark)').matches
        ? 'dark'
        : 'light'
    } else {
      effectiveTheme = theme
    }

    // Appliquer le thème
    root.classList.add(effectiveTheme)
    setActualTheme(effectiveTheme)
  }, [theme])

  // Écouter les changements de préférence système
  useEffect(() => {
    if (theme !== 'system') return

    const mediaQuery = window.matchMedia('(prefers-color-scheme: dark)')
    
    const handleChange = (e: MediaQueryListEvent) => {
      const root = window.document.documentElement
      root.classList.remove('light', 'dark')
      const newTheme = e.matches ? 'dark' : 'light'
      root.classList.add(newTheme)
      setActualTheme(newTheme)
    }

    mediaQuery.addEventListener('change', handleChange)
    return () => mediaQuery.removeEventListener('change', handleChange)
  }, [theme])

  const value = {
    theme,
    setTheme: (newTheme: Theme) => {
      localStorage.setItem(storageKey, newTheme)
      setTheme(newTheme)
    },
    actualTheme,
  }

  return <ThemeContext.Provider value={value}>{children}</ThemeContext.Provider>
}

export function useTheme() {
  const context = useContext(ThemeContext)
  if (context === undefined) {
    throw new Error('useTheme must be used within ThemeProvider')
  }
  return context
}
```

### Définir les thèmes dark et light

```css
/* src/styles/themes.css */
@layer base {
  :root {
    /* Light theme */
    --color-background: 0 0% 100%;
    --color-foreground: 222 47% 11%;
    --color-primary: 221 83% 53%;
    --color-primary-foreground: 210 40% 98%;
    
    --color-card: 0 0% 100%;
    --color-card-foreground: 222 47% 11%;
    
    --color-muted: 210 40% 96%;
    --color-muted-foreground: 215 16% 47%;
    
    --color-border: 214 32% 91%;
  }

  .dark {
    /* Dark theme */
    --color-background: 222 47% 11%;
    --color-foreground: 210 40% 98%;
    --color-primary: 217 91% 60%;
    --color-primary-foreground: 222 47% 11%;
    
    --color-card: 222 47% 11%;
    --color-card-foreground: 210 40% 98%;
    
    --color-muted: 217 33% 17%;
    --color-muted-foreground: 215 20% 65%;
    
    --color-border: 217 33% 17%;
  }
}
```

### Composant Theme Toggle

```tsx
// components/theme-toggle.tsx
import { Moon, Sun } from 'lucide-react'
import { Button } from '@/components/ui/button'
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuTrigger,
} from '@/components/ui/dropdown-menu'
import { useTheme } from './theme-provider'

export function ThemeToggle() {
  const { theme, setTheme } = useTheme()

  return (
    <DropdownMenu>
      <DropdownMenuTrigger asChild>
        <Button variant="ghost" size="icon">
          <Sun className="h-5 w-5 rotate-0 scale-100 transition-all dark:-rotate-90 dark:scale-0" />
          <Moon className="absolute h-5 w-5 rotate-90 scale-0 transition-all dark:rotate-0 dark:scale-100" />
          <span className="sr-only">Changer de thème</span>
        </Button>
      </DropdownMenuTrigger>
      <DropdownMenuContent align="end">
        <DropdownMenuItem onClick={() => setTheme('light')}>
          ☀️ Clair
        </DropdownMenuItem>
        <DropdownMenuItem onClick={() => setTheme('dark')}>
          🌙 Sombre
        </DropdownMenuItem>
        <DropdownMenuItem onClick={() => setTheme('system')}>
          💻 Système
        </DropdownMenuItem>
      </DropdownMenuContent>
    </DropdownMenu>
  )
}
```

---

## Créer un thème personnalisé

### Thèmes multiples

```css
/* src/styles/themes.css */
@layer base {
  /* Thème par défaut (bleu) */
  :root {
    --color-primary: 221 83% 53%;
  }

  /* Thème vert */
  .theme-green {
    --color-primary: 142 71% 45%;
    --color-primary-foreground: 144 61% 20%;
  }

  /* Thème violet */
  .theme-purple {
    --color-primary: 262 83% 58%;
    --color-primary-foreground: 265 89% 98%;
  }

  /* Thème orange */
  .theme-orange {
    --color-primary: 25 95% 53%;
    --color-primary-foreground: 26 100% 97%;
  }

  /* Variants dark */
  .dark.theme-green {
    --color-primary: 142 76% 55%;
  }

  .dark.theme-purple {
    --color-primary: 262 83% 68%;
  }

  .dark.theme-orange {
    --color-primary: 25 95% 63%;
  }
}
```

### Multi-theme Provider

```tsx
// components/multi-theme-provider.tsx
import { createContext, useContext, useEffect, useState } from 'react'

type ColorTheme = 'default' | 'green' | 'purple' | 'orange'
type ModeTheme = 'light' | 'dark' | 'system'

interface MultiThemeContextType {
  colorTheme: ColorTheme
  setColorTheme: (theme: ColorTheme) => void
  modeTheme: ModeTheme
  setModeTheme: (theme: ModeTheme) => void
  actualMode: 'light' | 'dark'
}

const MultiThemeContext = createContext<MultiThemeContextType | undefined>(undefined)

export function MultiThemeProvider({ children }: { children: React.ReactNode }) {
  const [colorTheme, setColorTheme] = useState<ColorTheme>(
    () => (localStorage.getItem('color-theme') as ColorTheme) || 'default'
  )
  
  const [modeTheme, setModeTheme] = useState<ModeTheme>(
    () => (localStorage.getItem('mode-theme') as ModeTheme) || 'system'
  )
  
  const [actualMode, setActualMode] = useState<'light' | 'dark'>('light')

  // Gérer le mode (light/dark)
  useEffect(() => {
    const root = window.document.documentElement
    root.classList.remove('light', 'dark')

    let effectiveMode: 'light' | 'dark'
    if (modeTheme === 'system') {
      effectiveMode = window.matchMedia('(prefers-color-scheme: dark)').matches
        ? 'dark'
        : 'light'
    } else {
      effectiveMode = modeTheme
    }

    root.classList.add(effectiveMode)
    setActualMode(effectiveMode)
  }, [modeTheme])

  // Gérer le thème de couleur
  useEffect(() => {
    const root = window.document.documentElement
    root.classList.remove('theme-green', 'theme-purple', 'theme-orange')
    
    if (colorTheme !== 'default') {
      root.classList.add(`theme-${colorTheme}`)
    }
  }, [colorTheme])

  const value = {
    colorTheme,
    setColorTheme: (theme: ColorTheme) => {
      localStorage.setItem('color-theme', theme)
      setColorTheme(theme)
    },
    modeTheme,
    setModeTheme: (theme: ModeTheme) => {
      localStorage.setItem('mode-theme', theme)
      setModeTheme(theme)
    },
    actualMode,
  }

  return <MultiThemeContext.Provider value={value}>{children}</MultiThemeContext.Provider>
}

export function useMultiTheme() {
  const context = useContext(MultiThemeContext)
  if (!context) {
    throw new Error('useMultiTheme must be used within MultiThemeProvider')
  }
  return context
}
```

### Sélecteur de thème

```tsx
// components/theme-customizer.tsx
import { useMultiTheme } from './multi-theme-provider'
import { Button } from '@/components/ui/button'
import { Card, CardHeader, CardTitle, CardContent } from '@/components/ui/card'

const colorThemes = [
  { value: 'default', label: 'Bleu', color: 'bg-blue-500' },
  { value: 'green', label: 'Vert', color: 'bg-green-500' },
  { value: 'purple', label: 'Violet', color: 'bg-purple-500' },
  { value: 'orange', label: 'Orange', color: 'bg-orange-500' },
] as const

export function ThemeCustomizer() {
  const { colorTheme, setColorTheme, modeTheme, setModeTheme } = useMultiTheme()

  return (
    <Card>
      <CardHeader>
        <CardTitle>Personnaliser le thème</CardTitle>
      </CardHeader>
      <CardContent className="space-y-6">
        {/* Couleur */}
        <div>
          <h3 className="text-sm font-medium mb-3">Couleur</h3>
          <div className="grid grid-cols-4 gap-2">
            {colorThemes.map((theme) => (
              <button
                key={theme.value}
                onClick={() => setColorTheme(theme.value)}
                className={cn(
                  'flex flex-col items-center gap-2 p-3 rounded-lg border-2 transition-all',
                  colorTheme === theme.value
                    ? 'border-primary bg-primary/10'
                    : 'border-transparent hover:border-muted'
                )}
              >
                <div className={cn('w-10 h-10 rounded-full', theme.color)} />
                <span className="text-xs">{theme.label}</span>
              </button>
            ))}
          </div>
        </div>

        {/* Mode */}
        <div>
          <h3 className="text-sm font-medium mb-3">Mode</h3>
          <div className="grid grid-cols-3 gap-2">
            <Button
              variant={modeTheme === 'light' ? 'default' : 'outline'}
              onClick={() => setModeTheme('light')}
            >
              ☀️ Clair
            </Button>
            <Button
              variant={modeTheme === 'dark' ? 'default' : 'outline'}
              onClick={() => setModeTheme('dark')}
            >
              🌙 Sombre
            </Button>
            <Button
              variant={modeTheme === 'system' ? 'default' : 'outline'}
              onClick={() => setModeTheme('system')}
            >
              💻 Système
            </Button>
          </div>
        </div>
      </CardContent>
    </Card>
  )
}
```

---

## Bonnes pratiques pour la cohérence visuelle

### 1. Utiliser les tokens partout

```tsx
// ❌ Éviter : valeurs codées en dur
<div className="mt-4 p-6 bg-blue-500 rounded-lg">

// ✅ Bon : utiliser les tokens
<div className="mt-md p-lg bg-primary rounded-lg">
```

### 2. Créer des variants sémantiques

```tsx
// components/ui/alert.tsx
const alertVariants = cva('rounded-lg border p-4', {
  variants: {
    variant: {
      default: 'bg-background text-foreground',
      success: 'border-success/50 bg-success/10 text-success-foreground',
      warning: 'border-warning/50 bg-warning/10 text-warning-foreground',
      error: 'border-error/50 bg-error/10 text-error-foreground',
      info: 'border-info/50 bg-info/10 text-info-foreground',
    },
  },
  defaultVariants: {
    variant: 'default',
  },
})
```

### 3. Documenter les tokens

```tsx
// design-system/tokens/README.md
/**
 * Color Tokens
 * 
 * ## Usage
 * 
 * ### Background colors
 * - `--color-background`: Main background color
 * - `--color-foreground`: Main text color
 * 
 * ### Brand colors
 * - `--color-primary`: Primary brand color
 * - `--color-primary-foreground`: Text color on primary
 * 
 * ### State colors
 * - `--color-success`: Success state (green)
 * - `--color-warning`: Warning state (yellow/orange)
 * - `--color-error`: Error state (red)
 * - `--color-info`: Information state (blue)
 */
```

### 4. Tester les thèmes

```tsx
// Composant de test pour vérifier tous les thèmes
function ThemePreview() {
  return (
    <div className="space-y-4">
      <Card>
        <CardHeader>
          <CardTitle>Couleurs</CardTitle>
        </CardHeader>
        <CardContent className="grid grid-cols-2 gap-4">
          <div className="space-y-2">
            <div className="h-12 bg-background border rounded flex items-center justify-center text-sm">
              Background
            </div>
            <div className="h-12 bg-primary text-primary-foreground rounded flex items-center justify-center text-sm">
              Primary
            </div>
            <div className="h-12 bg-secondary text-secondary-foreground rounded flex items-center justify-center text-sm">
              Secondary
            </div>
          </div>
          <div className="space-y-2">
            <div className="h-12 bg-success rounded flex items-center justify-center text-sm text-white">
              Success
            </div>
            <div className="h-12 bg-warning rounded flex items-center justify-center text-sm">
              Warning
            </div>
            <div className="h-12 bg-error rounded flex items-center justify-center text-sm text-white">
              Error
            </div>
          </div>
        </CardContent>
      </Card>

      <Card>
        <CardHeader>
          <CardTitle>Typographie</CardTitle>
        </CardHeader>
        <CardContent className="space-y-2">
          <p className="text-xs">Extra Small Text</p>
          <p className="text-sm">Small Text</p>
          <p className="text-base">Base Text</p>
          <p className="text-lg">Large Text</p>
          <p className="text-xl">Extra Large Text</p>
          <p className="text-2xl">2XL Text</p>
        </CardContent>
      </Card>
    </div>
  )
}
```

### 5. Animations de transition

```tsx
// Transition douce lors du changement de thème
useEffect(() => {
  // Désactiver temporairement les transitions
  document.documentElement.classList.add('no-transitions')
  
  // Appliquer le thème
  // ...
  
  // Réactiver les transitions après un frame
  requestAnimationFrame(() => {
    document.documentElement.classList.remove('no-transitions')
  })
}, [theme])
```

```css
/* styles/globals.css */
* {
  transition: background-color 0.2s, border-color 0.2s, color 0.2s;
}

.no-transitions * {
  transition: none !important;
}
```

---

## Résumé du chapitre

Dans ce chapitre, nous avons exploré les thèmes et tokens :

✅ **Tokens** : Variables CSS pour valeurs atomiques
✅ **ThemeProvider** : Gestion du thème avec Context
✅ **Light/Dark** : Mode clair et sombre
✅ **Multi-thèmes** : Plusieurs thèmes de couleur
✅ **Bonnes pratiques** : Cohérence et maintenabilité

### Points clés à retenir

💡 Les tokens garantissent la cohérence visuelle

💡 Les CSS variables permettent les thèmes dynamiques

💡 Toujours offrir un mode sombre aujourd'hui

💡 Documenter et tester vos thèmes

---

## Exercices

### Exercice 1 : Système de thèmes complet
Créez un système avec :
- 3 thèmes de couleur
- Light et dark mode
- Persistence localStorage
- Composant de sélection

### Exercice 2 : Thème par utilisateur
Implémentez :
- Sauvegarde du thème en base de données
- Synchronisation entre appareils
- Thème par défaut selon le profil

### Exercice 3 : Générateur de thème
Créez un outil qui :
- Permet de choisir une couleur primaire
- Génère automatiquement les variants
- Exporte les CSS variables
- Preview en temps réel

---

**Prêt à rendre votre app accessible ?** → [Chapitre 10 : Accessibilité et UX](./chapitre-10.md)

