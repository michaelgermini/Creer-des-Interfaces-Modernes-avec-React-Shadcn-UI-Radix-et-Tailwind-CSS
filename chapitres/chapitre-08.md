# Chapitre 8 : Création de composants réutilisables

## Introduction

Créer des composants réutilisables est un art. Dans ce chapitre, vous apprendrez les patterns et techniques pour construire des composants flexibles, maintenables et professionnels.

---

## Le pattern "UI primitives"

### Qu'est-ce qu'une UI primitive ?

Une **primitive** est un composant de base qui :
- ✅ A une seule responsabilité
- ✅ Est hautement configurable
- ✅ N'a pas de logique métier
- ✅ Peut être composé avec d'autres primitives

### Exemple : Button primitive

```tsx
// components/ui/button.tsx
import * as React from 'react'
import { cva, type VariantProps } from 'class-variance-authority'
import { Slot } from '@radix-ui/react-slot'
import { cn } from '@/lib/utils'

const buttonVariants = cva(
  'inline-flex items-center justify-center gap-2 whitespace-nowrap rounded-md text-sm font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50',
  {
    variants: {
      variant: {
        default: 'bg-primary text-primary-foreground hover:bg-primary/90',
        destructive: 'bg-destructive text-destructive-foreground hover:bg-destructive/90',
        outline: 'border border-input bg-background hover:bg-accent hover:text-accent-foreground',
        secondary: 'bg-secondary text-secondary-foreground hover:bg-secondary/80',
        ghost: 'hover:bg-accent hover:text-accent-foreground',
        link: 'text-primary underline-offset-4 hover:underline',
      },
      size: {
        default: 'h-10 px-4 py-2',
        sm: 'h-9 rounded-md px-3',
        lg: 'h-11 rounded-md px-8',
        icon: 'h-10 w-10',
      },
    },
    defaultVariants: {
      variant: 'default',
      size: 'default',
    },
  }
)

export interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {
  asChild?: boolean
}

const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(
  ({ className, variant, size, asChild = false, ...props }, ref) => {
    const Comp = asChild ? Slot : 'button'
    return (
      <Comp
        className={cn(buttonVariants({ variant, size, className }))}
        ref={ref}
        {...props}
      />
    )
  }
)
Button.displayName = 'Button'

export { Button, buttonVariants }
```

### Composition de primitives

```tsx
// components/ui/card.tsx
import { cn } from '@/lib/utils'

const Card = React.forwardRef<HTMLDivElement, React.HTMLAttributes<HTMLDivElement>>(
  ({ className, ...props }, ref) => (
    <div
      ref={ref}
      className={cn('rounded-lg border bg-card text-card-foreground shadow-sm', className)}
      {...props}
    />
  )
)

const CardHeader = React.forwardRef<HTMLDivElement, React.HTMLAttributes<HTMLDivElement>>(
  ({ className, ...props }, ref) => (
    <div ref={ref} className={cn('flex flex-col space-y-1.5 p-6', className)} {...props} />
  )
)

const CardTitle = React.forwardRef<HTMLParagraphElement, React.HTMLAttributes<HTMLHeadingElement>>(
  ({ className, ...props }, ref) => (
    <h3 ref={ref} className={cn('text-2xl font-semibold leading-none tracking-tight', className)} {...props} />
  )
)

const CardDescription = React.forwardRef<HTMLParagraphElement, React.HTMLAttributes<HTMLParagraphElement>>(
  ({ className, ...props }, ref) => (
    <p ref={ref} className={cn('text-sm text-muted-foreground', className)} {...props} />
  )
)

const CardContent = React.forwardRef<HTMLDivElement, React.HTMLAttributes<HTMLDivElement>>(
  ({ className, ...props }, ref) => (
    <div ref={ref} className={cn('p-6 pt-0', className)} {...props} />
  )
)

const CardFooter = React.forwardRef<HTMLDivElement, React.HTMLAttributes<HTMLDivElement>>(
  ({ className, ...props }, ref) => (
    <div ref={ref} className={cn('flex items-center p-6 pt-0', className)} {...props} />
  )
)

export { Card, CardHeader, CardFooter, CardTitle, CardDescription, CardContent }
```

```tsx
// Utilisation : composition claire et flexible
import { Card, CardHeader, CardTitle, CardDescription, CardContent, CardFooter } from '@/components/ui/card'
import { Button } from '@/components/ui/button'

function ProductCard({ product }) {
  return (
    <Card className="w-[350px]">
      <CardHeader>
        <CardTitle>{product.name}</CardTitle>
        <CardDescription>{product.category}</CardDescription>
      </CardHeader>
      <CardContent>
        <img src={product.image} alt={product.name} className="w-full rounded-md" />
        <p className="mt-4 text-lg font-bold">{product.price} €</p>
      </CardContent>
      <CardFooter className="flex gap-2">
        <Button className="flex-1">Ajouter au panier</Button>
        <Button variant="outline" size="icon">
          <Heart />
        </Button>
      </CardFooter>
    </Card>
  )
}
```

---

## Boutons, formulaires et modales

### Button avec loading state

```tsx
// components/ui/button-with-loading.tsx
import { Button, ButtonProps } from './button'
import { Loader2 } from 'lucide-react'

interface ButtonWithLoadingProps extends ButtonProps {
  loading?: boolean
}

export function ButtonWithLoading({ 
  loading, 
  disabled, 
  children, 
  ...props 
}: ButtonWithLoadingProps) {
  return (
    <Button disabled={loading || disabled} {...props}>
      {loading && <Loader2 className="h-4 w-4 animate-spin" />}
      {children}
    </Button>
  )
}
```

### Input primitive

```tsx
// components/ui/input.tsx
import * as React from 'react'
import { cn } from '@/lib/utils'

export interface InputProps extends React.InputHTMLAttributes<HTMLInputElement> {
  error?: string
}

const Input = React.forwardRef<HTMLInputElement, InputProps>(
  ({ className, type, error, ...props }, ref) => {
    return (
      <div className="w-full">
        <input
          type={type}
          className={cn(
            'flex h-10 w-full rounded-md border border-input bg-background px-3 py-2 text-sm ring-offset-background file:border-0 file:bg-transparent file:text-sm file:font-medium placeholder:text-muted-foreground focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:cursor-not-allowed disabled:opacity-50',
            error && 'border-destructive focus-visible:ring-destructive',
            className
          )}
          ref={ref}
          {...props}
        />
        {error && (
          <p className="mt-1 text-sm text-destructive">{error}</p>
        )}
      </div>
    )
  }
)
Input.displayName = 'Input'

export { Input }
```

### Label et Form Field

```tsx
// components/ui/label.tsx
import * as React from 'react'
import { cn } from '@/lib/utils'

export interface LabelProps extends React.LabelHTMLAttributes<HTMLLabelElement> {
  required?: boolean
}

const Label = React.forwardRef<HTMLLabelElement, LabelProps>(
  ({ className, required, children, ...props }, ref) => (
    <label
      ref={ref}
      className={cn('text-sm font-medium leading-none peer-disabled:cursor-not-allowed peer-disabled:opacity-70', className)}
      {...props}
    >
      {children}
      {required && <span className="ml-1 text-destructive">*</span>}
    </label>
  )
)
Label.displayName = 'Label'

export { Label }
```

```tsx
// components/ui/form-field.tsx
import { Input, InputProps } from './input'
import { Label } from './label'

interface FormFieldProps extends InputProps {
  label: string
  required?: boolean
  helperText?: string
}

export function FormField({ 
  label, 
  required, 
  helperText, 
  error, 
  id, 
  ...props 
}: FormFieldProps) {
  const inputId = id || label.toLowerCase().replace(/\s/g, '-')
  
  return (
    <div className="space-y-2">
      <Label htmlFor={inputId} required={required}>
        {label}
      </Label>
      <Input id={inputId} error={error} {...props} />
      {helperText && !error && (
        <p className="text-sm text-muted-foreground">{helperText}</p>
      )}
    </div>
  )
}
```

### Formulaire complet

```tsx
// components/forms/login-form.tsx
import { useState } from 'react'
import { FormField } from '@/components/ui/form-field'
import { ButtonWithLoading } from '@/components/ui/button-with-loading'

interface LoginFormData {
  email: string
  password: string
}

interface LoginFormErrors {
  email?: string
  password?: string
}

export function LoginForm() {
  const [formData, setFormData] = useState<LoginFormData>({
    email: '',
    password: '',
  })
  
  const [errors, setErrors] = useState<LoginFormErrors>({})
  const [loading, setLoading] = useState(false)
  
  const validate = (): boolean => {
    const newErrors: LoginFormErrors = {}
    
    if (!formData.email) {
      newErrors.email = 'L\'email est requis'
    } else if (!/\S+@\S+\.\S+/.test(formData.email)) {
      newErrors.email = 'Email invalide'
    }
    
    if (!formData.password) {
      newErrors.password = 'Le mot de passe est requis'
    } else if (formData.password.length < 8) {
      newErrors.password = 'Le mot de passe doit contenir au moins 8 caractères'
    }
    
    setErrors(newErrors)
    return Object.keys(newErrors).length === 0
  }
  
  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault()
    
    if (!validate()) return
    
    setLoading(true)
    try {
      // Appel API
      await loginApi(formData)
      // Redirection, etc.
    } catch (error) {
      setErrors({ email: 'Email ou mot de passe incorrect' })
    } finally {
      setLoading(false)
    }
  }
  
  const handleChange = (field: keyof LoginFormData) => (
    e: React.ChangeEvent<HTMLInputElement>
  ) => {
    setFormData(prev => ({ ...prev, [field]: e.target.value }))
    // Effacer l'erreur quand l'utilisateur tape
    if (errors[field]) {
      setErrors(prev => ({ ...prev, [field]: undefined }))
    }
  }
  
  return (
    <form onSubmit={handleSubmit} className="space-y-4">
      <FormField
        label="Email"
        type="email"
        value={formData.email}
        onChange={handleChange('email')}
        error={errors.email}
        required
        autoComplete="email"
      />
      
      <FormField
        label="Mot de passe"
        type="password"
        value={formData.password}
        onChange={handleChange('password')}
        error={errors.password}
        required
        autoComplete="current-password"
      />
      
      <ButtonWithLoading type="submit" loading={loading} className="w-full">
        Se connecter
      </ButtonWithLoading>
    </form>
  )
}
```

---

## États visuels (hover, focus, disabled)

### States complets avec Tailwind

```tsx
// Button avec tous les états
<button
  className={cn(
    // Base
    'rounded-md px-4 py-2 font-medium transition-all',
    
    // Default state
    'bg-primary text-white',
    
    // Hover
    'hover:bg-primary/90 hover:shadow-md',
    
    // Focus
    'focus:outline-none focus:ring-2 focus:ring-primary focus:ring-offset-2',
    
    // Active (pressed)
    'active:scale-95',
    
    // Disabled
    'disabled:opacity-50 disabled:cursor-not-allowed disabled:hover:bg-primary',
    
    // Loading state
    loading && 'cursor-wait',
  )}
>
  Button
</button>
```

### Pattern : Compound variants avec CVA

```tsx
import { cva } from 'class-variance-authority'

const inputVariants = cva(
  'w-full rounded-md border px-3 py-2 text-sm transition-colors',
  {
    variants: {
      variant: {
        default: 'border-input bg-background',
        filled: 'border-transparent bg-secondary',
      },
      state: {
        default: '',
        error: 'border-destructive focus:ring-destructive',
        success: 'border-success focus:ring-success',
      },
    },
    compoundVariants: [
      {
        variant: 'filled',
        state: 'error',
        class: 'bg-destructive/10',
      },
      {
        variant: 'filled',
        state: 'success',
        class: 'bg-success/10',
      },
    ],
    defaultVariants: {
      variant: 'default',
      state: 'default',
    },
  }
)
```

---

## Gestion des icônes avec Lucide

### Installation

```bash
npm install lucide-react
```

### Utilisation de base

```tsx
import { Search, User, Settings, ChevronDown, Loader2 } from 'lucide-react'

function MyComponent() {
  return (
    <div className="flex gap-2">
      <Search className="w-5 h-5" />
      <User className="w-5 h-5 text-blue-500" />
      <Settings className="w-5 h-5" />
      <ChevronDown className="w-4 h-4" />
      <Loader2 className="w-5 h-5 animate-spin" />
    </div>
  )
}
```

### Button avec icônes

```tsx
import { Button } from '@/components/ui/button'
import { Save, Trash, Download } from 'lucide-react'

function Actions() {
  return (
    <div className="flex gap-2">
      {/* Icône à gauche */}
      <Button>
        <Save className="w-4 h-4" />
        Enregistrer
      </Button>
      
      {/* Icône seule */}
      <Button size="icon" variant="destructive">
        <Trash className="w-4 h-4" />
      </Button>
      
      {/* Icône à droite */}
      <Button>
        Télécharger
        <Download className="w-4 h-4" />
      </Button>
    </div>
  )
}
```

### Composant Icon Button

```tsx
// components/ui/icon-button.tsx
import { Button, ButtonProps } from './button'
import { LucideIcon } from 'lucide-react'
import { cn } from '@/lib/utils'

interface IconButtonProps extends Omit<ButtonProps, 'children'> {
  icon: LucideIcon
  label: string // Pour l'accessibilité
}

export function IconButton({ icon: Icon, label, className, ...props }: IconButtonProps) {
  return (
    <Button
      size="icon"
      aria-label={label}
      className={cn('relative', className)}
      {...props}
    >
      <Icon className="h-4 w-4" />
      <span className="sr-only">{label}</span>
    </Button>
  )
}
```

```tsx
// Utilisation
import { IconButton } from '@/components/ui/icon-button'
import { Trash, Edit, Eye } from 'lucide-react'

<div className="flex gap-2">
  <IconButton icon={Eye} label="Voir" variant="ghost" />
  <IconButton icon={Edit} label="Modifier" variant="ghost" />
  <IconButton icon={Trash} label="Supprimer" variant="destructive" />
</div>
```

---

## Tester et documenter les composants

### Tests unitaires avec Vitest et React Testing Library

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom @testing-library/user-event
```

```tsx
// components/ui/button.test.tsx
import { describe, it, expect, vi } from 'vitest'
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { Button } from './button'

describe('Button', () => {
  it('renders correctly', () => {
    render(<Button>Click me</Button>)
    expect(screen.getByRole('button')).toHaveTextContent('Click me')
  })
  
  it('handles click events', async () => {
    const handleClick = vi.fn()
    render(<Button onClick={handleClick}>Click me</Button>)
    
    await userEvent.click(screen.getByRole('button'))
    expect(handleClick).toHaveBeenCalledTimes(1)
  })
  
  it('is disabled when disabled prop is true', () => {
    render(<Button disabled>Click me</Button>)
    expect(screen.getByRole('button')).toBeDisabled()
  })
  
  it('applies variant classes correctly', () => {
    const { rerender } = render(<Button variant="destructive">Delete</Button>)
    expect(screen.getByRole('button')).toHaveClass('bg-destructive')
    
    rerender(<Button variant="ghost">Cancel</Button>)
    expect(screen.getByRole('button')).not.toHaveClass('bg-destructive')
  })
})
```

### Documentation avec JSDoc

```tsx
/**
 * Button component with multiple variants and sizes.
 *
 * @component
 * @example
 * ```tsx
 * <Button variant="primary" size="lg" onClick={() => alert('Clicked!')}>
 *   Click me
 * </Button>
 * ```
 */
export interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  /**
   * Visual style variant
   * @default "default"
   */
  variant?: 'default' | 'destructive' | 'outline' | 'secondary' | 'ghost' | 'link'
  
  /**
   * Size of the button
   * @default "default"
   */
  size?: 'default' | 'sm' | 'lg' | 'icon'
  
  /**
   * Render as a child element (using Radix Slot)
   * @default false
   */
  asChild?: boolean
}
```

### Storybook (optionnel)

```bash
npx storybook@latest init
```

```tsx
// components/ui/button.stories.tsx
import type { Meta, StoryObj } from '@storybook/react'
import { Button } from './button'
import { Save } from 'lucide-react'

const meta = {
  title: 'UI/Button',
  component: Button,
  parameters: {
    layout: 'centered',
  },
  tags: ['autodocs'],
  argTypes: {
    variant: {
      control: 'select',
      options: ['default', 'destructive', 'outline', 'secondary', 'ghost', 'link'],
    },
    size: {
      control: 'select',
      options: ['default', 'sm', 'lg', 'icon'],
    },
  },
} satisfies Meta<typeof Button>

export default meta
type Story = StoryObj<typeof meta>

export const Default: Story = {
  args: {
    children: 'Button',
  },
}

export const Destructive: Story = {
  args: {
    variant: 'destructive',
    children: 'Delete',
  },
}

export const WithIcon: Story = {
  args: {
    children: (
      <>
        <Save className="w-4 h-4" />
        Save
      </>
    ),
  },
}

export const AllVariants: Story = {
  render: () => (
    <div className="flex flex-col gap-2">
      <Button variant="default">Default</Button>
      <Button variant="destructive">Destructive</Button>
      <Button variant="outline">Outline</Button>
      <Button variant="secondary">Secondary</Button>
      <Button variant="ghost">Ghost</Button>
      <Button variant="link">Link</Button>
    </div>
  ),
}
```

### README du composant

```markdown
<!-- components/ui/button/README.md -->

# Button

A versatile button component with multiple variants and sizes.

## Usage

```tsx
import { Button } from '@/components/ui/button'

function MyComponent() {
  return (
    <Button variant="default" size="md">
      Click me
    </Button>
  )
}
```

## Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `variant` | `'default' \| 'destructive' \| 'outline' \| 'secondary' \| 'ghost' \| 'link'` | `'default'` | Visual style variant |
| `size` | `'default' \| 'sm' \| 'lg' \| 'icon'` | `'default'` | Size of the button |
| `asChild` | `boolean` | `false` | Render as child element |

## Examples

### With Icon

```tsx
import { Save } from 'lucide-react'

<Button>
  <Save className="w-4 h-4" />
  Save
</Button>
```

### As Link

```tsx
<Button asChild>
  <a href="/about">About</a>
</Button>
```

## Accessibility

- Uses semantic `<button>` element
- Supports keyboard navigation
- Proper focus states
- ARIA attributes when needed
```

---

## Résumé du chapitre

Dans ce chapitre, nous avons appris à créer des composants réutilisables :

✅ **UI Primitives** : Composants de base flexibles et composables
✅ **Formulaires** : Input, Label, FormField avec validation
✅ **États visuels** : Hover, focus, disabled, loading
✅ **Icônes** : Intégration de Lucide React
✅ **Tests** : Tests unitaires et documentation

### Points clés à retenir

💡 Les primitives sont simples, flexibles et sans logique métier

💡 Utilisez CVA pour gérer les variants complexes

💡 Tous les états interactifs doivent avoir un feedback visuel

💡 Documentez et testez vos composants réutilisables

---

## Exercices

### Exercice 1 : Input avancé
Créez un composant Input qui supporte :
- Types : text, email, password, number
- Icône à gauche ou à droite
- Clear button
- Loading state
- Character counter

### Exercice 2 : Select personnalisé
Créez un Select accessible avec :
- Radix UI Select
- Recherche
- Multi-select
- Groupes d'options
- Styles Tailwind

### Exercice 3 : Table réutilisable
Créez un composant Table avec :
- Tri par colonne
- Pagination
- Sélection de lignes
- Actions par ligne
- États (loading, empty, error)

---

**Prêt à gérer les thèmes ?** → [Chapitre 9 : Styles, thèmes et tokens](./chapitre-09.md)

