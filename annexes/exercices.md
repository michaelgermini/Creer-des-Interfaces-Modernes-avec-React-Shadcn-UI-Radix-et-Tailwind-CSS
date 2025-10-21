# Annexe C : Exercices récapitulatifs et corrigés

## Introduction

Cette annexe regroupe des exercices pratiques pour consolider vos connaissances. Chaque exercice est accompagné d'un corrigé détaillé.

---

## Exercice 1 : Composant Card réutilisable

### Énoncé

Créez un composant `Card` réutilisable avec les fonctionnalités suivantes :
- Variants : `default`, `outlined`, `elevated`
- Sections : Header, Content, Footer (optionnelles)
- Support du dark mode
- Hover effect
- TypeScript strict

### Corrigé

```tsx
// components/ui/card.tsx
import * as React from 'react'
import { cva, type VariantProps } from 'class-variance-authority'
import { cn } from '@/lib/utils'

const cardVariants = cva(
  'rounded-lg transition-all',
  {
    variants: {
      variant: {
        default: 'bg-card text-card-foreground border',
        outlined: 'border-2 bg-transparent',
        elevated: 'bg-card text-card-foreground shadow-lg hover:shadow-xl',
      },
    },
    defaultVariants: {
      variant: 'default',
    },
  }
)

interface CardProps
  extends React.HTMLAttributes<HTMLDivElement>,
    VariantProps<typeof cardVariants> {}

const Card = React.forwardRef<HTMLDivElement, CardProps>(
  ({ className, variant, ...props }, ref) => (
    <div
      ref={ref}
      className={cn(cardVariants({ variant }), className)}
      {...props}
    />
  )
)
Card.displayName = 'Card'

const CardHeader = React.forwardRef<
  HTMLDivElement,
  React.HTMLAttributes<HTMLDivElement>
>(({ className, ...props }, ref) => (
  <div
    ref={ref}
    className={cn('flex flex-col space-y-1.5 p-6', className)}
    {...props}
  />
))
CardHeader.displayName = 'CardHeader'

const CardTitle = React.forwardRef<
  HTMLParagraphElement,
  React.HTMLAttributes<HTMLHeadingElement>
>(({ className, ...props }, ref) => (
  <h3
    ref={ref}
    className={cn('text-2xl font-semibold leading-none tracking-tight', className)}
    {...props}
  />
))
CardTitle.displayName = 'CardTitle'

const CardDescription = React.forwardRef<
  HTMLParagraphElement,
  React.HTMLAttributes<HTMLParagraphElement>
>(({ className, ...props }, ref) => (
  <p
    ref={ref}
    className={cn('text-sm text-muted-foreground', className)}
    {...props}
  />
))
CardDescription.displayName = 'CardDescription'

const CardContent = React.forwardRef<
  HTMLDivElement,
  React.HTMLAttributes<HTMLDivElement>
>(({ className, ...props }, ref) => (
  <div ref={ref} className={cn('p-6 pt-0', className)} {...props} />
))
CardContent.displayName = 'CardContent'

const CardFooter = React.forwardRef<
  HTMLDivElement,
  React.HTMLAttributes<HTMLDivElement>
>(({ className, ...props }, ref) => (
  <div
    ref={ref}
    className={cn('flex items-center p-6 pt-0', className)}
    {...props}
  />
))
CardFooter.displayName = 'CardFooter'

export { Card, CardHeader, CardFooter, CardTitle, CardDescription, CardContent }
```

### Utilisation

```tsx
import { Card, CardHeader, CardTitle, CardDescription, CardContent, CardFooter } from '@/components/ui/card'
import { Button } from '@/components/ui/button'

function Example() {
  return (
    <div className="grid grid-cols-3 gap-4">
      <Card variant="default">
        <CardHeader>
          <CardTitle>Default Card</CardTitle>
          <CardDescription>This is a default card</CardDescription>
        </CardHeader>
        <CardContent>
          <p>Card content goes here</p>
        </CardContent>
        <CardFooter>
          <Button>Action</Button>
        </CardFooter>
      </Card>

      <Card variant="outlined">
        <CardHeader>
          <CardTitle>Outlined Card</CardTitle>
        </CardHeader>
        <CardContent>
          <p>With outlined style</p>
        </CardContent>
      </Card>

      <Card variant="elevated">
        <CardHeader>
          <CardTitle>Elevated Card</CardTitle>
        </CardHeader>
        <CardContent>
          <p>With shadow effect</p>
        </CardContent>
      </Card>
    </div>
  )
}
```

---

## Exercice 2 : Formulaire avec validation

### Énoncé

Créez un formulaire d'inscription avec :
- Champs : nom, email, mot de passe, confirmation
- Validation en temps réel
- Messages d'erreur
- Loading state
- TypeScript

### Corrigé

```tsx
// components/forms/register-form.tsx
import { useState } from 'react'
import { Button } from '@/components/ui/button'
import { Input } from '@/components/ui/input'
import { Label } from '@/components/ui/label'
import { Loader2 } from 'lucide-react'

interface FormData {
  name: string
  email: string
  password: string
  confirmPassword: string
}

interface FormErrors {
  name?: string
  email?: string
  password?: string
  confirmPassword?: string
}

export function RegisterForm() {
  const [formData, setFormData] = useState<FormData>({
    name: '',
    email: '',
    password: '',
    confirmPassword: '',
  })

  const [errors, setErrors] = useState<FormErrors>({})
  const [loading, setLoading] = useState(false)
  const [touched, setTouched] = useState<Record<string, boolean>>({})

  const validateField = (name: keyof FormData, value: string): string | undefined => {
    switch (name) {
      case 'name':
        if (!value.trim()) return 'Le nom est requis'
        if (value.trim().length < 2) return 'Le nom doit contenir au moins 2 caractères'
        break
      
      case 'email':
        if (!value.trim()) return 'L\'email est requis'
        if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value)) return 'Email invalide'
        break
      
      case 'password':
        if (!value) return 'Le mot de passe est requis'
        if (value.length < 8) return 'Le mot de passe doit contenir au moins 8 caractères'
        if (!/[A-Z]/.test(value)) return 'Le mot de passe doit contenir une majuscule'
        if (!/[0-9]/.test(value)) return 'Le mot de passe doit contenir un chiffre'
        break
      
      case 'confirmPassword':
        if (!value) return 'La confirmation est requise'
        if (value !== formData.password) return 'Les mots de passe ne correspondent pas'
        break
    }
    return undefined
  }

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target
    setFormData(prev => ({ ...prev, [name]: value }))

    // Valider si le champ a déjà été touché
    if (touched[name]) {
      const error = validateField(name as keyof FormData, value)
      setErrors(prev => ({ ...prev, [name]: error }))
    }

    // Re-valider confirmPassword si password change
    if (name === 'password' && touched.confirmPassword) {
      const confirmError = validateField('confirmPassword', formData.confirmPassword)
      setErrors(prev => ({ ...prev, confirmPassword: confirmError }))
    }
  }

  const handleBlur = (e: React.FocusEvent<HTMLInputElement>) => {
    const { name, value } = e.target
    setTouched(prev => ({ ...prev, [name]: true }))
    
    const error = validateField(name as keyof FormData, value)
    setErrors(prev => ({ ...prev, [name]: error }))
  }

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault()

    // Valider tous les champs
    const newErrors: FormErrors = {}
    let hasErrors = false

    Object.keys(formData).forEach((key) => {
      const error = validateField(key as keyof FormData, formData[key as keyof FormData])
      if (error) {
        newErrors[key as keyof FormErrors] = error
        hasErrors = true
      }
    })

    setErrors(newErrors)
    setTouched({
      name: true,
      email: true,
      password: true,
      confirmPassword: true,
    })

    if (hasErrors) return

    // Soumettre le formulaire
    setLoading(true)
    try {
      // Simuler un appel API
      await new Promise(resolve => setTimeout(resolve, 2000))
      
      console.log('Inscription réussie', formData)
      alert('Inscription réussie !')
      
      // Réinitialiser le formulaire
      setFormData({ name: '', email: '', password: '', confirmPassword: '' })
      setTouched({})
    } catch (error) {
      console.error('Erreur lors de l\'inscription', error)
    } finally {
      setLoading(false)
    }
  }

  return (
    <form onSubmit={handleSubmit} className="space-y-4 max-w-md mx-auto p-6">
      <div className="space-y-2">
        <Label htmlFor="name">
          Nom complet <span className="text-destructive">*</span>
        </Label>
        <Input
          id="name"
          name="name"
          value={formData.name}
          onChange={handleChange}
          onBlur={handleBlur}
          className={errors.name ? 'border-destructive' : ''}
        />
        {errors.name && (
          <p className="text-sm text-destructive">{errors.name}</p>
        )}
      </div>

      <div className="space-y-2">
        <Label htmlFor="email">
          Email <span className="text-destructive">*</span>
        </Label>
        <Input
          id="email"
          name="email"
          type="email"
          value={formData.email}
          onChange={handleChange}
          onBlur={handleBlur}
          className={errors.email ? 'border-destructive' : ''}
        />
        {errors.email && (
          <p className="text-sm text-destructive">{errors.email}</p>
        )}
      </div>

      <div className="space-y-2">
        <Label htmlFor="password">
          Mot de passe <span className="text-destructive">*</span>
        </Label>
        <Input
          id="password"
          name="password"
          type="password"
          value={formData.password}
          onChange={handleChange}
          onBlur={handleBlur}
          className={errors.password ? 'border-destructive' : ''}
        />
        {errors.password && (
          <p className="text-sm text-destructive">{errors.password}</p>
        )}
        <p className="text-xs text-muted-foreground">
          Au moins 8 caractères, une majuscule et un chiffre
        </p>
      </div>

      <div className="space-y-2">
        <Label htmlFor="confirmPassword">
          Confirmer le mot de passe <span className="text-destructive">*</span>
        </Label>
        <Input
          id="confirmPassword"
          name="confirmPassword"
          type="password"
          value={formData.confirmPassword}
          onChange={handleChange}
          onBlur={handleBlur}
          className={errors.confirmPassword ? 'border-destructive' : ''}
        />
        {errors.confirmPassword && (
          <p className="text-sm text-destructive">{errors.confirmPassword}</p>
        )}
      </div>

      <Button type="submit" className="w-full" disabled={loading}>
        {loading && <Loader2 className="mr-2 h-4 w-4 animate-spin" />}
        {loading ? 'Inscription en cours...' : 'S\'inscrire'}
      </Button>
    </form>
  )
}
```

---

## Exercice 3 : Modal accessible avec animations

### Énoncé

Créez un modal accessible qui :
- Utilise Radix Dialog
- Animations d'entrée/sortie avec Framer Motion
- Piège le focus
- Se ferme avec Escape ou clic sur overlay
- TypeScript strict

### Corrigé

```tsx
// components/ui/animated-dialog.tsx
import * as React from 'react'
import * as DialogPrimitive from '@radix-ui/react-dialog'
import { X } from 'lucide-react'
import { motion, AnimatePresence } from 'framer-motion'
import { cn } from '@/lib/utils'

const Dialog = DialogPrimitive.Root
const DialogTrigger = DialogPrimitive.Trigger
const DialogPortal = DialogPrimitive.Portal
const DialogClose = DialogPrimitive.Close

const overlayVariants = {
  hidden: { opacity: 0 },
  visible: { opacity: 1 },
}

const contentVariants = {
  hidden: {
    opacity: 0,
    scale: 0.95,
    y: -20,
  },
  visible: {
    opacity: 1,
    scale: 1,
    y: 0,
    transition: {
      type: 'spring',
      damping: 25,
      stiffness: 300,
    },
  },
  exit: {
    opacity: 0,
    scale: 0.95,
    y: 20,
    transition: {
      duration: 0.2,
    },
  },
}

const DialogOverlay = React.forwardRef<
  React.ElementRef<typeof DialogPrimitive.Overlay>,
  React.ComponentPropsWithoutRef<typeof DialogPrimitive.Overlay>
>(({ className, ...props }, ref) => (
  <DialogPrimitive.Overlay asChild>
    <motion.div
      ref={ref}
      variants={overlayVariants}
      initial="hidden"
      animate="visible"
      exit="hidden"
      className={cn(
        'fixed inset-0 z-50 bg-black/50',
        className
      )}
      {...props}
    />
  </DialogPrimitive.Overlay>
))
DialogOverlay.displayName = 'DialogOverlay'

interface DialogContentProps
  extends React.ComponentPropsWithoutRef<typeof DialogPrimitive.Content> {
  showClose?: boolean
}

const DialogContent = React.forwardRef<
  React.ElementRef<typeof DialogPrimitive.Content>,
  DialogContentProps
>(({ className, children, showClose = true, ...props }, ref) => (
  <DialogPortal>
    <AnimatePresence>
      <DialogOverlay />
      <DialogPrimitive.Content asChild>
        <motion.div
          ref={ref}
          variants={contentVariants}
          initial="hidden"
          animate="visible"
          exit="exit"
          className={cn(
            'fixed left-[50%] top-[50%] z-50 w-full max-w-lg translate-x-[-50%] translate-y-[-50%]',
            'bg-background p-6 shadow-lg rounded-lg',
            'focus:outline-none',
            className
          )}
          {...props}
        >
          {children}
          {showClose && (
            <DialogPrimitive.Close className="absolute right-4 top-4 rounded-sm opacity-70 ring-offset-background transition-opacity hover:opacity-100 focus:outline-none focus:ring-2 focus:ring-ring focus:ring-offset-2 disabled:pointer-events-none">
              <X className="h-4 w-4" />
              <span className="sr-only">Fermer</span>
            </DialogPrimitive.Close>
          )}
        </motion.div>
      </DialogPrimitive.Content>
    </AnimatePresence>
  </DialogPortal>
))
DialogContent.displayName = 'DialogContent'

const DialogHeader = ({
  className,
  ...props
}: React.HTMLAttributes<HTMLDivElement>) => (
  <div
    className={cn('flex flex-col space-y-1.5 text-center sm:text-left', className)}
    {...props}
  />
)
DialogHeader.displayName = 'DialogHeader'

const DialogTitle = React.forwardRef<
  React.ElementRef<typeof DialogPrimitive.Title>,
  React.ComponentPropsWithoutRef<typeof DialogPrimitive.Title>
>(({ className, ...props }, ref) => (
  <DialogPrimitive.Title
    ref={ref}
    className={cn('text-lg font-semibold leading-none tracking-tight', className)}
    {...props}
  />
))
DialogTitle.displayName = 'DialogTitle'

const DialogDescription = React.forwardRef<
  React.ElementRef<typeof DialogPrimitive.Description>,
  React.ComponentPropsWithoutRef<typeof DialogPrimitive.Description>
>(({ className, ...props }, ref) => (
  <DialogPrimitive.Description
    ref={ref}
    className={cn('text-sm text-muted-foreground', className)}
    {...props}
  />
))
DialogDescription.displayName = 'DialogDescription'

const DialogFooter = ({
  className,
  ...props
}: React.HTMLAttributes<HTMLDivElement>) => (
  <div
    className={cn('flex flex-col-reverse sm:flex-row sm:justify-end sm:space-x-2', className)}
    {...props}
  />
)
DialogFooter.displayName = 'DialogFooter'

export {
  Dialog,
  DialogTrigger,
  DialogContent,
  DialogHeader,
  DialogFooter,
  DialogTitle,
  DialogDescription,
  DialogClose,
}
```

### Utilisation

```tsx
import {
  Dialog,
  DialogTrigger,
  DialogContent,
  DialogHeader,
  DialogTitle,
  DialogDescription,
  DialogFooter,
  DialogClose,
} from '@/components/ui/animated-dialog'
import { Button } from '@/components/ui/button'

function Example() {
  return (
    <Dialog>
      <DialogTrigger asChild>
        <Button>Ouvrir le modal</Button>
      </DialogTrigger>
      <DialogContent>
        <DialogHeader>
          <DialogTitle>Confirmer l'action</DialogTitle>
          <DialogDescription>
            Cette action est irréversible. Êtes-vous sûr de vouloir continuer ?
          </DialogDescription>
        </DialogHeader>
        <DialogFooter>
          <DialogClose asChild>
            <Button variant="outline">Annuler</Button>
          </DialogClose>
          <Button variant="destructive">Confirmer</Button>
        </DialogFooter>
      </DialogContent>
    </Dialog>
  )
}
```

---

## Exercice 4 : Système de thèmes complet

### Énoncé

Implémentez un système de thèmes avec :
- Light/Dark mode
- 3 thèmes de couleur (bleu, vert, violet)
- Persistence localStorage
- Composant de sélection
- TypeScript

### Corrigé

```tsx
// components/theme-provider.tsx
import { createContext, useContext, useEffect, useState } from 'react'

type Mode = 'light' | 'dark' | 'system'
type ColorTheme = 'blue' | 'green' | 'purple'

interface ThemeContextType {
  mode: Mode
  setMode: (mode: Mode) => void
  colorTheme: ColorTheme
  setColorTheme: (theme: ColorTheme) => void
  actualMode: 'light' | 'dark'
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined)

export function ThemeProvider({ children }: { children: React.ReactNode }) {
  const [mode, setMode] = useState<Mode>(() => {
    const stored = localStorage.getItem('theme-mode')
    return (stored as Mode) || 'system'
  })

  const [colorTheme, setColorTheme] = useState<ColorTheme>(() => {
    const stored = localStorage.getItem('color-theme')
    return (stored as ColorTheme) || 'blue'
  })

  const [actualMode, setActualMode] = useState<'light' | 'dark'>('light')

  // Gérer le mode clair/sombre
  useEffect(() => {
    const root = window.document.documentElement
    root.classList.remove('light', 'dark')

    let effectiveMode: 'light' | 'dark'
    if (mode === 'system') {
      effectiveMode = window.matchMedia('(prefers-color-scheme: dark)').matches
        ? 'dark'
        : 'light'
    } else {
      effectiveMode = mode
    }

    root.classList.add(effectiveMode)
    setActualMode(effectiveMode)
    localStorage.setItem('theme-mode', mode)
  }, [mode])

  // Gérer le thème de couleur
  useEffect(() => {
    const root = window.document.documentElement
    root.classList.remove('theme-blue', 'theme-green', 'theme-purple')
    root.classList.add(`theme-${colorTheme}`)
    localStorage.setItem('color-theme', colorTheme)
  }, [colorTheme])

  const value = {
    mode,
    setMode,
    colorTheme,
    setColorTheme,
    actualMode,
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

```css
/* globals.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  :root {
    --primary: 221 83% 53%;
    --primary-foreground: 210 40% 98%;
  }

  .theme-blue {
    --primary: 221 83% 53%;
  }

  .theme-green {
    --primary: 142 71% 45%;
  }

  .theme-purple {
    --primary: 262 83% 58%;
  }

  .dark.theme-blue {
    --primary: 217 91% 60%;
  }

  .dark.theme-green {
    --primary: 142 76% 55%;
  }

  .dark.theme-purple {
    --primary: 262 83% 68%;
  }
}
```

```tsx
// components/theme-customizer.tsx
import { useTheme } from './theme-provider'
import { Button } from '@/components/ui/button'
import { Card } from '@/components/ui/card'
import { Sun, Moon, Monitor } from 'lucide-react'

const colorThemes = [
  { value: 'blue', label: 'Bleu', color: 'bg-blue-500' },
  { value: 'green', label: 'Vert', color: 'bg-green-500' },
  { value: 'purple', label: 'Violet', color: 'bg-purple-500' },
] as const

export function ThemeCustomizer() {
  const { mode, setMode, colorTheme, setColorTheme } = useTheme()

  return (
    <Card className="p-6 space-y-6">
      <div>
        <h3 className="font-semibold mb-3">Mode d'affichage</h3>
        <div className="grid grid-cols-3 gap-2">
          <Button
            variant={mode === 'light' ? 'default' : 'outline'}
            onClick={() => setMode('light')}
          >
            <Sun className="mr-2 h-4 w-4" />
            Clair
          </Button>
          <Button
            variant={mode === 'dark' ? 'default' : 'outline'}
            onClick={() => setMode('dark')}
          >
            <Moon className="mr-2 h-4 w-4" />
            Sombre
          </Button>
          <Button
            variant={mode === 'system' ? 'default' : 'outline'}
            onClick={() => setMode('system')}
          >
            <Monitor className="mr-2 h-4 w-4" />
            Système
          </Button>
        </div>
      </div>

      <div>
        <h3 className="font-semibold mb-3">Thème de couleur</h3>
        <div className="grid grid-cols-3 gap-2">
          {colorThemes.map((theme) => (
            <button
              key={theme.value}
              onClick={() => setColorTheme(theme.value)}
              className={`
                flex flex-col items-center gap-2 p-4 rounded-lg border-2 transition-all
                ${colorTheme === theme.value
                  ? 'border-primary bg-primary/10'
                  : 'border-transparent hover:border-muted'
                }
              `}
            >
              <div className={`w-12 h-12 rounded-full ${theme.color}`} />
              <span className="text-sm">{theme.label}</span>
            </button>
          ))}
        </div>
      </div>
    </Card>
  )
}
```

---

## Conclusion

Ces exercices couvrent les aspects essentiels de React, Tailwind, Radix et Shadcn/UI. Pratiquez-les et expérimentez avec vos propres variations !

**Conseil** : N'hésitez pas à combiner plusieurs exercices pour créer des projets plus complexes.

---

[← Références](./references.md) | [Retour au sommaire](../README.md)

