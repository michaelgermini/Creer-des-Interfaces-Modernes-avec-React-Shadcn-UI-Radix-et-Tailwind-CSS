# Chapitre 7 : Architecture d'un projet React moderne

## Introduction

Une bonne architecture est la clé d'un projet maintenable et scalable. Dans ce chapitre, vous apprendrez à structurer votre projet React pour qu'il reste organisé, même quand il grandit.

---

## Organisation des dossiers

### Structure de base recommandée

```
src/
├── app/                    # Routes (si Next.js App Router)
├── components/             # Composants réutilisables
│   ├── ui/                # Composants Shadcn/UI
│   │   ├── button.tsx
│   │   ├── dialog.tsx
│   │   └── ...
│   ├── layout/            # Layout components
│   │   ├── header.tsx
│   │   ├── sidebar.tsx
│   │   └── footer.tsx
│   ├── forms/             # Composants de formulaires
│   │   ├── login-form.tsx
│   │   └── register-form.tsx
│   └── features/          # Composants métier
│       ├── user-profile/
│       ├── dashboard/
│       └── settings/
├── hooks/                 # Custom hooks
│   ├── use-auth.ts
│   ├── use-toast.ts
│   └── use-media-query.ts
├── lib/                   # Utilitaires et helpers
│   ├── utils.ts           # Fonctions utilitaires
│   ├── api.ts             # Client API
│   └── constants.ts       # Constantes
├── types/                 # Types TypeScript
│   ├── user.ts
│   ├── api.ts
│   └── index.ts
├── styles/                # Styles globaux
│   ├── globals.css
│   └── themes/
├── assets/                # Images, fonts, etc.
│   ├── images/
│   └── icons/
├── config/                # Configuration
│   ├── site.ts
│   └── routes.ts
└── App.tsx                # Point d'entrée
```

---

## Approche modulaire et scalable

### Organisation par fonctionnalité

Pour les grandes applications, organisez par fonctionnalité plutôt que par type :

```
src/
├── features/
│   ├── auth/
│   │   ├── components/
│   │   │   ├── login-form.tsx
│   │   │   └── register-form.tsx
│   │   ├── hooks/
│   │   │   └── use-auth.ts
│   │   ├── api/
│   │   │   └── auth-api.ts
│   │   ├── types/
│   │   │   └── auth.types.ts
│   │   └── index.ts       # Public API du module
│   │
│   ├── dashboard/
│   │   ├── components/
│   │   │   ├── stats-card.tsx
│   │   │   └── recent-activity.tsx
│   │   ├── hooks/
│   │   │   └── use-dashboard-data.ts
│   │   └── index.ts
│   │
│   └── products/
│       ├── components/
│       ├── hooks/
│       ├── api/
│       └── index.ts
│
└── shared/                # Code partagé entre features
    ├── components/
    ├── hooks/
    ├── utils/
    └── types/
```

### Index barrel exports

```ts
// features/auth/index.ts
export { LoginForm } from './components/login-form'
export { RegisterForm } from './components/register-form'
export { useAuth } from './hooks/use-auth'
export type { AuthUser, LoginCredentials } from './types/auth.types'
```

```tsx
// Utilisation propre
import { LoginForm, useAuth } from '@/features/auth'
```

---

## Structurer le code pour la réutilisabilité

### Pattern : Composants UI purs

```tsx
// components/ui/button.tsx
// ✅ Pas de logique métier
// ✅ Pas d'appels API
// ✅ Réutilisable partout

interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'primary' | 'secondary'
  size?: 'sm' | 'md' | 'lg'
  loading?: boolean
}

export function Button({ variant, size, loading, children, ...props }: ButtonProps) {
  return (
    <button
      className={buttonVariants({ variant, size })}
      disabled={loading || props.disabled}
      {...props}
    >
      {loading && <Spinner />}
      {children}
    </button>
  )
}
```

### Pattern : Composants métier

```tsx
// features/products/components/product-card.tsx
// ✅ Contient la logique métier
// ✅ Utilise les composants UI
// ✅ Gère les états et interactions

import { Button } from '@/components/ui/button'
import { Card } from '@/components/ui/card'
import { useCart } from '@/features/cart'

interface ProductCardProps {
  product: Product
}

export function ProductCard({ product }: ProductCardProps) {
  const { addToCart, isInCart } = useCart()
  
  const handleAddToCart = () => {
    addToCart(product)
  }
  
  return (
    <Card>
      <img src={product.image} alt={product.name} />
      <h3>{product.name}</h3>
      <p>{product.price} €</p>
      <Button 
        onClick={handleAddToCart}
        disabled={isInCart(product.id)}
      >
        {isInCart(product.id) ? 'Dans le panier' : 'Ajouter'}
      </Button>
    </Card>
  )
}
```

### Pattern : Custom hooks

```ts
// hooks/use-local-storage.ts
import { useState, useEffect } from 'react'

export function useLocalStorage<T>(key: string, initialValue: T) {
  // État pour stocker la valeur
  const [storedValue, setStoredValue] = useState<T>(() => {
    try {
      const item = window.localStorage.getItem(key)
      return item ? JSON.parse(item) : initialValue
    } catch (error) {
      console.error(error)
      return initialValue
    }
  })

  // Fonction pour sauvegarder
  const setValue = (value: T | ((val: T) => T)) => {
    try {
      const valueToStore = value instanceof Function ? value(storedValue) : value
      setStoredValue(valueToStore)
      window.localStorage.setItem(key, JSON.stringify(valueToStore))
    } catch (error) {
      console.error(error)
    }
  }

  return [storedValue, setValue] as const
}
```

```tsx
// Utilisation
function MyComponent() {
  const [theme, setTheme] = useLocalStorage('theme', 'light')
  
  return (
    <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
      Thème actuel : {theme}
    </button>
  )
}
```

### Pattern : API Layer

```ts
// lib/api.ts
import axios from 'axios'

const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL,
  headers: {
    'Content-Type': 'application/json',
  },
})

// Intercepteur pour ajouter le token
api.interceptors.request.use((config) => {
  const token = localStorage.getItem('token')
  if (token) {
    config.headers.Authorization = `Bearer ${token}`
  }
  return config
})

// Intercepteur pour gérer les erreurs
api.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      // Rediriger vers login
      window.location.href = '/login'
    }
    return Promise.reject(error)
  }
)

export { api }
```

```ts
// features/products/api/products-api.ts
import { api } from '@/lib/api'
import type { Product } from '../types'

export const productsApi = {
  getAll: async (): Promise<Product[]> => {
    const { data } = await api.get('/products')
    return data
  },
  
  getById: async (id: string): Promise<Product> => {
    const { data } = await api.get(`/products/${id}`)
    return data
  },
  
  create: async (product: Omit<Product, 'id'>): Promise<Product> => {
    const { data } = await api.post('/products', product)
    return data
  },
  
  update: async (id: string, product: Partial<Product>): Promise<Product> => {
    const { data } = await api.patch(`/products/${id}`, product)
    return data
  },
  
  delete: async (id: string): Promise<void> => {
    await api.delete(`/products/${id}`)
  },
}
```

---

## Mise en place d'un design system partagé

### Structure du design system

```
src/
├── design-system/
│   ├── tokens/
│   │   ├── colors.ts
│   │   ├── spacing.ts
│   │   ├── typography.ts
│   │   └── index.ts
│   ├── components/
│   │   ├── primitives/     # Composants de base
│   │   │   ├── button/
│   │   │   ├── input/
│   │   │   └── card/
│   │   ├── composites/     # Composants composés
│   │   │   ├── form/
│   │   │   ├── modal/
│   │   │   └── table/
│   │   └── layouts/        # Layouts
│   │       ├── container/
│   │       └── grid/
│   └── index.ts            # Export centralisé
```

### Tokens de design

```ts
// design-system/tokens/colors.ts
export const colors = {
  // Brand
  primary: {
    50: '#eff6ff',
    100: '#dbeafe',
    500: '#3b82f6',
    600: '#2563eb',
    900: '#1e3a8a',
  },
  
  // Neutral
  gray: {
    50: '#f9fafb',
    100: '#f3f4f6',
    500: '#6b7280',
    900: '#111827',
  },
  
  // Semantic
  success: {
    DEFAULT: '#10b981',
    light: '#6ee7b7',
    dark: '#047857',
  },
  error: {
    DEFAULT: '#ef4444',
    light: '#fca5a5',
    dark: '#dc2626',
  },
  warning: {
    DEFAULT: '#f59e0b',
    light: '#fcd34d',
    dark: '#d97706',
  },
  info: {
    DEFAULT: '#3b82f6',
    light: '#93c5fd',
    dark: '#1e40af',
  },
} as const

export type ColorToken = keyof typeof colors
```

```ts
// design-system/tokens/spacing.ts
export const spacing = {
  0: '0',
  1: '0.25rem',   // 4px
  2: '0.5rem',    // 8px
  3: '0.75rem',   // 12px
  4: '1rem',      // 16px
  5: '1.25rem',   // 20px
  6: '1.5rem',    // 24px
  8: '2rem',      // 32px
  10: '2.5rem',   // 40px
  12: '3rem',     // 48px
  16: '4rem',     // 64px
  20: '5rem',     // 80px
} as const

export type SpacingToken = keyof typeof spacing
```

```ts
// design-system/tokens/typography.ts
export const typography = {
  fontFamily: {
    sans: ['Inter', 'system-ui', 'sans-serif'],
    mono: ['Fira Code', 'Consolas', 'monospace'],
  },
  
  fontSize: {
    xs: '0.75rem',      // 12px
    sm: '0.875rem',     // 14px
    base: '1rem',       // 16px
    lg: '1.125rem',     // 18px
    xl: '1.25rem',      // 20px
    '2xl': '1.5rem',    // 24px
    '3xl': '1.875rem',  // 30px
    '4xl': '2.25rem',   // 36px
  },
  
  fontWeight: {
    light: '300',
    normal: '400',
    medium: '500',
    semibold: '600',
    bold: '700',
  },
  
  lineHeight: {
    tight: '1.25',
    normal: '1.5',
    relaxed: '1.75',
  },
} as const
```

### Composants avec tokens

```tsx
// design-system/components/primitives/button/button.tsx
import { cva, type VariantProps } from 'class-variance-authority'
import { cn } from '@/lib/utils'

const buttonVariants = cva(
  // Base styles utilisant les tokens
  'inline-flex items-center justify-center rounded-md font-medium transition-colors',
  {
    variants: {
      variant: {
        primary: 'bg-primary-500 text-white hover:bg-primary-600',
        secondary: 'bg-gray-200 text-gray-900 hover:bg-gray-300',
        success: 'bg-success text-white hover:bg-success-dark',
        error: 'bg-error text-white hover:bg-error-dark',
        ghost: 'hover:bg-gray-100',
      },
      size: {
        sm: 'h-9 px-3 text-sm',
        md: 'h-10 px-4 text-base',
        lg: 'h-11 px-8 text-lg',
      },
    },
    defaultVariants: {
      variant: 'primary',
      size: 'md',
    },
  }
)

interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {
  asChild?: boolean
}

export function Button({ 
  className, 
  variant, 
  size, 
  ...props 
}: ButtonProps) {
  return (
    <button
      className={cn(buttonVariants({ variant, size }), className)}
      {...props}
    />
  )
}
```

### Documentation du design system

```tsx
// design-system/components/primitives/button/button.stories.tsx
// Storybook (optionnel mais recommandé)

import type { Meta, StoryObj } from '@storybook/react'
import { Button } from './button'

const meta: Meta<typeof Button> = {
  title: 'Primitives/Button',
  component: Button,
  tags: ['autodocs'],
}

export default meta
type Story = StoryObj<typeof Button>

export const Primary: Story = {
  args: {
    variant: 'primary',
    children: 'Button',
  },
}

export const AllVariants: Story = {
  render: () => (
    <div className="flex gap-2">
      <Button variant="primary">Primary</Button>
      <Button variant="secondary">Secondary</Button>
      <Button variant="success">Success</Button>
      <Button variant="error">Error</Button>
      <Button variant="ghost">Ghost</Button>
    </div>
  ),
}
```

---

## Gestion des environnements

### Variables d'environnement

```bash
# .env.development
VITE_API_URL=http://localhost:3000/api
VITE_APP_NAME=My App Dev
VITE_ENABLE_ANALYTICS=false

# .env.production
VITE_API_URL=https://api.myapp.com
VITE_APP_NAME=My App
VITE_ENABLE_ANALYTICS=true
```

```ts
// config/env.ts
export const env = {
  apiUrl: import.meta.env.VITE_API_URL,
  appName: import.meta.env.VITE_APP_NAME,
  enableAnalytics: import.meta.env.VITE_ENABLE_ANALYTICS === 'true',
  isDevelopment: import.meta.env.DEV,
  isProduction: import.meta.env.PROD,
} as const

// Validation au démarrage
if (!env.apiUrl) {
  throw new Error('VITE_API_URL is not defined')
}
```

---

## Configuration TypeScript optimale

```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,

    /* Bundler mode */
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",

    /* Linting */
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,

    /* Paths */
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"],
      "@/components/*": ["./src/components/*"],
      "@/hooks/*": ["./src/hooks/*"],
      "@/lib/*": ["./src/lib/*"],
      "@/types/*": ["./src/types/*"],
      "@/features/*": ["./src/features/*"]
    }
  },
  "include": ["src"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

---

## Résumé du chapitre

Dans ce chapitre, nous avons exploré l'architecture d'un projet React moderne :

✅ **Organisation** : Structure de dossiers claire et scalable
✅ **Modularité** : Organisation par fonctionnalité
✅ **Réutilisabilité** : Patterns de composants et hooks
✅ **Design system** : Tokens et composants cohérents
✅ **Configuration** : TypeScript, environnements, API

### Points clés à retenir

💡 Organisez par fonctionnalité pour les grandes apps

💡 Séparez composants UI et composants métier

💡 Créez un design system avec tokens

💡 Utilisez des barrel exports pour des imports propres

---

## Exercices

### Exercice 1 : Restructurer un projet
Prenez un projet existant et réorganisez-le avec :
- Dossiers par fonctionnalité
- Barrel exports
- Alias TypeScript

### Exercice 2 : Créer un design system minimal
Créez :
- Tokens de couleurs et espacements
- 3 composants primitifs (Button, Input, Card)
- Documentation avec exemples

### Exercice 3 : API Layer
Implémentez :
- Client API avec intercepteurs
- Gestion des erreurs
- Hook useQuery personnalisé
- Types TypeScript pour les endpoints

---

**Prêt à créer des composants réutilisables ?** → [Chapitre 8 : Création de composants réutilisables](./chapitre-08.md)

