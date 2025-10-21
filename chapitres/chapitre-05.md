# Chapitre 5 : Découverte de Shadcn/UI

## Introduction

Shadcn/UI n'est pas une bibliothèque de composants traditionnelle. C'est une **collection de composants réutilisables** que vous copiez et collez dans votre projet. Cette approche unique vous donne un contrôle total tout en bénéficiant de composants professionnels.

---

## Origine et philosophie du projet

### L'histoire de Shadcn/UI

Créé par [shadcn](https://twitter.com/shadcn) en 2023, Shadcn/UI est né d'une frustration commune :
- Les bibliothèques de composants sont trop rigides
- La personnalisation est limitée
- Les dépendances s'accumulent
- Les mises à jour peuvent casser votre code

### La philosophie : "Own your code"

**Shadcn/UI ne s'installe pas** :
```bash
# ❌ Pas de :
npm install shadcn-ui

# ✅ À la place :
npx shadcn-ui@latest add button
```

Le composant est **copié** dans votre projet :
```
src/
  components/
    ui/
      button.tsx  ← Votre code maintenant !
```

### Avantages de cette approche

✅ **Contrôle total** : Le code est à vous, modifiez-le comme vous voulez

✅ **Pas de dépendance cachée** : Vous voyez exactement ce qui se passe

✅ **Pas de breaking changes** : Les mises à jour de Shadcn n'affectent pas votre code

✅ **Apprentissage** : Vous pouvez lire et comprendre chaque ligne

✅ **Personnalisation illimitée** : Changez tout ce que vous voulez

✅ **Performance** : Pas de code inutilisé

### Inconvénients potentiels

⚠️ **Pas de mises à jour automatiques** : Vous devez gérer les mises à jour manuellement

⚠️ **Code dupliqué entre projets** : Chaque projet a sa propre copie

💡 **Solution** : Créez votre propre bibliothèque interne si vous avez plusieurs projets

---

## Fonctionnement de la CLI

### Installation et initialisation

```bash
# Créer un nouveau projet React
npm create vite@latest mon-app -- --template react-ts
cd mon-app
npm install

# Initialiser Shadcn/UI
npx shadcn-ui@latest init
```

### Le processus d'initialisation

La CLI vous posera plusieurs questions :

```
Which style would you like to use? › Default
Which color would you like to use as base color? › Slate
Would you like to use CSS variables for colors? › yes
Where is your global CSS file? › src/index.css
Where is your tailwind.config.js located? › tailwind.config.js
Configure the import alias for components: › @/components
Configure the import alias for utils: › @/lib/utils
```

### Ce que l'initialisation fait

1. **Installe les dépendances** :
```bash
npm install tailwindcss-animate class-variance-authority clsx tailwind-merge lucide-react
```

2. **Configure Tailwind** :
```js
// tailwind.config.js
export default {
  darkMode: ["class"],
  content: [
    './pages/**/*.{ts,tsx}',
    './components/**/*.{ts,tsx}',
    './app/**/*.{ts,tsx}',
    './src/**/*.{ts,tsx}',
  ],
  theme: {
    // Configuration des couleurs, etc.
  },
  plugins: [require("tailwindcss-animate")],
}
```

3. **Crée `components.json`** :
```json
{
  "style": "default",
  "rsc": false,
  "tsx": true,
  "tailwind": {
    "config": "tailwind.config.js",
    "css": "src/index.css",
    "baseColor": "slate",
    "cssVariables": true
  },
  "aliases": {
    "components": "@/components",
    "utils": "@/lib/utils"
  }
}
```

4. **Crée les fichiers utilitaires** :
```ts
// src/lib/utils.ts
import { type ClassValue, clsx } from "clsx"
import { twMerge } from "tailwind-merge"

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}
```

5. **Ajoute les CSS variables** :
```css
/* src/index.css */
@layer base {
  :root {
    --background: 0 0% 100%;
    --foreground: 222.2 84% 4.9%;
    --primary: 222.2 47.4% 11.2%;
    /* ... */
  }

  .dark {
    --background: 222.2 84% 4.9%;
    --foreground: 210 40% 98%;
    --primary: 210 40% 98%;
    /* ... */
  }
}
```

---

## Le rôle de `components.json`

### Structure complète

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
    "utils": "@/lib/utils"
  }
}
```

### Explication des options

#### `style`
Le style de design :
- `"default"` : Style moderne par défaut
- `"new-york"` : Style alternatif plus compact

#### `rsc`
React Server Components (Next.js App Router) :
- `true` : Utilise `"use client"` quand nécessaire
- `false` : Pas d'annotations RSC

#### `tsx`
Utiliser TypeScript :
- `true` : Fichiers `.tsx`
- `false` : Fichiers `.jsx`

#### `tailwind.baseColor`
Couleur de base du thème :
- `slate`, `gray`, `zinc`, `neutral`, `stone`
- Affecte toutes les couleurs neutres du thème

#### `tailwind.cssVariables`
Utiliser les CSS variables :
- `true` : Thème avec variables CSS (recommandé pour dark mode)
- `false` : Classes Tailwind directes

#### `aliases`
Chemins d'import :
```tsx
// Avec alias
import { Button } from "@/components/ui/button"
import { cn } from "@/lib/utils"

// Sans alias
import { Button } from "../../components/ui/button"
import { cn } from "../../lib/utils"
```

---

## Générer et personnaliser un composant

### Ajouter un composant

```bash
# Voir tous les composants disponibles
npx shadcn-ui@latest add

# Ajouter un composant spécifique
npx shadcn-ui@latest add button

# Ajouter plusieurs composants
npx shadcn-ui@latest add button input label

# Ajouter tous les composants (non recommandé)
npx shadcn-ui@latest add --all
```

### Anatomie d'un composant : Button

```tsx
// components/ui/button.tsx
import * as React from "react"
import { Slot } from "@radix-ui/react-slot"
import { cva, type VariantProps } from "class-variance-authority"
import { cn } from "@/lib/utils"

// Définition des variants avec CVA
const buttonVariants = cva(
  // Classes de base (toujours appliquées)
  "inline-flex items-center justify-center whitespace-nowrap rounded-md text-sm font-medium ring-offset-background transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50",
  {
    variants: {
      variant: {
        default: "bg-primary text-primary-foreground hover:bg-primary/90",
        destructive: "bg-destructive text-destructive-foreground hover:bg-destructive/90",
        outline: "border border-input bg-background hover:bg-accent hover:text-accent-foreground",
        secondary: "bg-secondary text-secondary-foreground hover:bg-secondary/80",
        ghost: "hover:bg-accent hover:text-accent-foreground",
        link: "text-primary underline-offset-4 hover:underline",
      },
      size: {
        default: "h-10 px-4 py-2",
        sm: "h-9 rounded-md px-3",
        lg: "h-11 rounded-md px-8",
        icon: "h-10 w-10",
      },
    },
    defaultVariants: {
      variant: "default",
      size: "default",
    },
  }
)

// Interface TypeScript
export interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {
  asChild?: boolean
}

// Composant
const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(
  ({ className, variant, size, asChild = false, ...props }, ref) => {
    const Comp = asChild ? Slot : "button"
    return (
      <Comp
        className={cn(buttonVariants({ variant, size, className }))}
        ref={ref}
        {...props}
      />
    )
  }
)
Button.displayName = "Button"

export { Button, buttonVariants }
```

### Utilisation du Button

```tsx
import { Button } from "@/components/ui/button"

function MyComponent() {
  return (
    <div className="space-y-4">
      {/* Variants */}
      <Button variant="default">Default</Button>
      <Button variant="destructive">Destructive</Button>
      <Button variant="outline">Outline</Button>
      <Button variant="secondary">Secondary</Button>
      <Button variant="ghost">Ghost</Button>
      <Button variant="link">Link</Button>

      {/* Sizes */}
      <Button size="sm">Small</Button>
      <Button size="default">Default</Button>
      <Button size="lg">Large</Button>
      <Button size="icon">
        <SearchIcon />
      </Button>

      {/* Disabled */}
      <Button disabled>Disabled</Button>

      {/* AsChild : rendre comme un autre élément */}
      <Button asChild>
        <a href="/login">Login</a>
      </Button>

      {/* Custom className */}
      <Button className="bg-gradient-to-r from-purple-500 to-pink-500">
        Gradient
      </Button>
    </div>
  )
}
```

### Personnaliser le composant

Le composant est maintenant dans votre projet, vous pouvez le modifier !

#### Ajouter un nouveau variant

```tsx
const buttonVariants = cva(
  "...",
  {
    variants: {
      variant: {
        default: "...",
        destructive: "...",
        // ✅ Nouveau variant
        success: "bg-green-500 text-white hover:bg-green-600",
      },
      size: {
        // ✅ Nouvelle taille
        xs: "h-8 rounded-md px-2 text-xs",
        default: "h-10 px-4 py-2",
        // ...
      },
    },
  }
)
```

```tsx
// Utilisation
<Button variant="success" size="xs">
  Success
</Button>
```

#### Ajouter des props personnalisées

```tsx
export interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {
  asChild?: boolean
  // ✅ Nouvelles props
  loading?: boolean
  leftIcon?: React.ReactNode
  rightIcon?: React.ReactNode
}

const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(
  ({ 
    className, 
    variant, 
    size, 
    asChild = false, 
    loading = false,
    leftIcon,
    rightIcon,
    children,
    ...props 
  }, ref) => {
    const Comp = asChild ? Slot : "button"
    return (
      <Comp
        className={cn(buttonVariants({ variant, size, className }))}
        ref={ref}
        disabled={loading || props.disabled}
        {...props}
      >
        {loading && <Loader2 className="mr-2 h-4 w-4 animate-spin" />}
        {!loading && leftIcon && <span className="mr-2">{leftIcon}</span>}
        {children}
        {rightIcon && <span className="ml-2">{rightIcon}</span>}
      </Comp>
    )
  }
)
```

```tsx
// Utilisation
<Button 
  loading={isSubmitting}
  leftIcon={<Save />}
>
  Enregistrer
</Button>
```

---

## Maintenir la cohérence du design

### Utiliser les tokens de couleur

```tsx
// ✅ Bon : utiliser les tokens
<div className="bg-primary text-primary-foreground">

// ❌ Éviter : couleurs codées en dur
<div className="bg-blue-500 text-white">
```

### Créer des composants composés

```tsx
// components/ui/card.tsx
import { cn } from "@/lib/utils"

const Card = ({ className, ...props }: React.HTMLAttributes<HTMLDivElement>) => (
  <div
    className={cn("rounded-lg border bg-card text-card-foreground shadow-sm", className)}
    {...props}
  />
)

const CardHeader = ({ className, ...props }: React.HTMLAttributes<HTMLDivElement>) => (
  <div className={cn("flex flex-col space-y-1.5 p-6", className)} {...props} />
)

const CardTitle = ({ className, ...props }: React.HTMLAttributes<HTMLHeadingElement>) => (
  <h3 className={cn("text-2xl font-semibold leading-none tracking-tight", className)} {...props} />
)

const CardDescription = ({ className, ...props }: React.HTMLAttributes<HTMLParagraphElement>) => (
  <p className={cn("text-sm text-muted-foreground", className)} {...props} />
)

const CardContent = ({ className, ...props }: React.HTMLAttributes<HTMLDivElement>) => (
  <div className={cn("p-6 pt-0", className)} {...props} />
)

const CardFooter = ({ className, ...props }: React.HTMLAttributes<HTMLDivElement>) => (
  <div className={cn("flex items-center p-6 pt-0", className)} {...props} />
)

export { Card, CardHeader, CardFooter, CardTitle, CardDescription, CardContent }
```

```tsx
// Utilisation : composition claire
import { Card, CardHeader, CardTitle, CardDescription, CardContent, CardFooter } from "@/components/ui/card"
import { Button } from "@/components/ui/button"

function UserCard({ user }) {
  return (
    <Card>
      <CardHeader>
        <CardTitle>{user.name}</CardTitle>
        <CardDescription>{user.email}</CardDescription>
      </CardHeader>
      <CardContent>
        <p>{user.bio}</p>
      </CardContent>
      <CardFooter>
        <Button>Contact</Button>
      </CardFooter>
    </Card>
  )
}
```

### Pattern de l'utilitaire `cn()`

```tsx
import { cn } from "@/lib/utils"

// Permet de combiner et surcharger des classes
<Button className={cn(
  "default-classes",
  condition && "conditional-classes",
  className // Classes passées en prop
)} />
```

---

## Résumé du chapitre

Dans ce chapitre, nous avons découvert Shadcn/UI :

✅ **Philosophie** : Composants copiés, pas installés - vous possédez le code
✅ **CLI** : Initialisation et génération de composants
✅ **`components.json`** : Configuration centralisée
✅ **Personnalisation** : Modifier librement les composants
✅ **Cohérence** : Utiliser les tokens et patterns établis

### Points clés à retenir

💡 Shadcn/UI n'est pas une dépendance, c'est un générateur de code

💡 Vous avez le contrôle total sur chaque composant

💡 Les composants utilisent Radix + Tailwind + CVA

💡 La personnalisation est encouragée et facilitée

---

## Exercices

### Exercice 1 : Installer Shadcn/UI
1. Créez un nouveau projet React
2. Initialisez Shadcn/UI
3. Ajoutez les composants Button, Input, Card

### Exercice 2 : Personnaliser le Button
Ajoutez au composant Button :
- Un variant `gradient`
- Une prop `loading` avec spinner
- Des props `leftIcon` et `rightIcon`

### Exercice 3 : Créer une Card personnalisée
Créez une `ProductCard` qui utilise :
- `Card` de Shadcn
- Image du produit
- Prix et description
- Button "Ajouter au panier"
- Mode sombre supporté

---

**Prêt à configurer Shadcn/UI avancé ?** → [Chapitre 6 : Configuration avancée de Shadcn/UI](./chapitre-06.md)

