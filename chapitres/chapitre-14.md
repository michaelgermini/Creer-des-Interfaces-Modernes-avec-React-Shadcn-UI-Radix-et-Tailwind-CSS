# Chapitre 14 : Aller plus loin

## Introduction

Vous maîtrisez maintenant les fondamentaux de React, Tailwind, Radix et Shadcn/UI. Dans ce chapitre final, nous explorons des sujets avancés pour perfectionner votre pratique.

---

## Créer ses propres composants Shadcn personnalisés

### Comprendre la structure d'un composant Shadcn

Tous les composants Shadcn suivent un pattern similaire :

```tsx
// Structure type
import * as React from 'react'
import * as RadixPrimitive from '@radix-ui/react-primitive'
import { cva, type VariantProps } from 'class-variance-authority'
import { cn } from '@/lib/utils'

// 1. Définir les variants avec CVA
const componentVariants = cva(
  'base classes',
  {
    variants: {
      variant: {
        default: 'variant classes',
      },
      size: {
        default: 'size classes',
      },
    },
    defaultVariants: {
      variant: 'default',
      size: 'default',
    },
  }
)

// 2. Interface TypeScript
export interface ComponentProps
  extends React.HTMLAttributes<HTMLElement>,
    VariantProps<typeof componentVariants> {
  // Props personnalisées
}

// 3. Composant avec forwardRef
const Component = React.forwardRef<HTMLElement, ComponentProps>(
  ({ className, variant, size, ...props }, ref) => {
    return (
      <element
        ref={ref}
        className={cn(componentVariants({ variant, size, className }))}
        {...props}
      />
    )
  }
)
Component.displayName = 'Component'

// 4. Export
export { Component, componentVariants }
```

### Créer un composant Timeline

```tsx
// components/ui/timeline.tsx
import * as React from 'react'
import { cva, type VariantProps } from 'class-variance-authority'
import { cn } from '@/lib/utils'

const Timeline = React.forwardRef<
  HTMLOListElement,
  React.HTMLAttributes<HTMLOListElement>
>(({ className, ...props }, ref) => (
  <ol
    ref={ref}
    className={cn('relative border-l border-border ml-3', className)}
    {...props}
  />
))
Timeline.displayName = 'Timeline'

const timelineItemVariants = cva(
  'mb-10 ml-6',
  {
    variants: {
      status: {
        default: '',
        active: 'font-semibold',
        completed: 'text-muted-foreground',
      },
    },
    defaultVariants: {
      status: 'default',
    },
  }
)

interface TimelineItemProps
  extends React.HTMLAttributes<HTMLLIElement>,
    VariantProps<typeof timelineItemVariants> {}

const TimelineItem = React.forwardRef<HTMLLIElement, TimelineItemProps>(
  ({ className, status, ...props }, ref) => (
    <li
      ref={ref}
      className={cn(timelineItemVariants({ status, className }))}
      {...props}
    />
  )
)
TimelineItem.displayName = 'TimelineItem'

const timelineDotVariants = cva(
  'absolute flex items-center justify-center w-6 h-6 rounded-full -left-3 ring-8 ring-background',
  {
    variants: {
      variant: {
        default: 'bg-muted',
        primary: 'bg-primary',
        success: 'bg-green-500',
        warning: 'bg-yellow-500',
        error: 'bg-red-500',
      },
    },
    defaultVariants: {
      variant: 'default',
    },
  }
)

interface TimelineDotProps
  extends React.HTMLAttributes<HTMLSpanElement>,
    VariantProps<typeof timelineDotVariants> {}

const TimelineDot = React.forwardRef<HTMLSpanElement, TimelineDotProps>(
  ({ className, variant, ...props }, ref) => (
    <span
      ref={ref}
      className={cn(timelineDotVariants({ variant, className }))}
      {...props}
    />
  )
)
TimelineDot.displayName = 'TimelineDot'

const TimelineContent = React.forwardRef<
  HTMLDivElement,
  React.HTMLAttributes<HTMLDivElement>
>(({ className, ...props }, ref) => (
  <div ref={ref} className={cn('', className)} {...props} />
))
TimelineContent.displayName = 'TimelineContent'

const TimelineTime = React.forwardRef<
  HTMLTimeElement,
  React.TimeHTMLAttributes<HTMLTimeElement>
>(({ className, ...props }, ref) => (
  <time
    ref={ref}
    className={cn('mb-1 text-sm font-normal leading-none text-muted-foreground', className)}
    {...props}
  />
))
TimelineTime.displayName = 'TimelineTime'

const TimelineTitle = React.forwardRef<
  HTMLHeadingElement,
  React.HTMLAttributes<HTMLHeadingElement>
>(({ className, ...props }, ref) => (
  <h3
    ref={ref}
    className={cn('text-lg font-semibold', className)}
    {...props}
  />
))
TimelineTitle.displayName = 'TimelineTitle'

const TimelineDescription = React.forwardRef<
  HTMLParagraphElement,
  React.HTMLAttributes<HTMLParagraphElement>
>(({ className, ...props }, ref) => (
  <p
    ref={ref}
    className={cn('text-base font-normal text-muted-foreground', className)}
    {...props}
  />
))
TimelineDescription.displayName = 'TimelineDescription'

export {
  Timeline,
  TimelineItem,
  TimelineDot,
  TimelineContent,
  TimelineTime,
  TimelineTitle,
  TimelineDescription,
}
```

### Utilisation du Timeline

```tsx
import {
  Timeline,
  TimelineItem,
  TimelineDot,
  TimelineContent,
  TimelineTime,
  TimelineTitle,
  TimelineDescription,
} from '@/components/ui/timeline'

function ActivityTimeline() {
  return (
    <Timeline>
      <TimelineItem>
        <TimelineDot variant="primary" />
        <TimelineContent>
          <TimelineTime>Il y a 2 heures</TimelineTime>
          <TimelineTitle>Nouveau commentaire</TimelineTitle>
          <TimelineDescription>
            Jean a commenté votre publication
          </TimelineDescription>
        </TimelineContent>
      </TimelineItem>
      
      <TimelineItem>
        <TimelineDot variant="success" />
        <TimelineContent>
          <TimelineTime>Il y a 5 heures</TimelineTime>
          <TimelineTitle>Vente réalisée</TimelineTitle>
          <TimelineDescription>
            Commande #1234 validée
          </TimelineDescription>
        </TimelineContent>
      </TimelineItem>
    </Timeline>
  )
}
```

---

## Contribution au design system

### Documenter vos composants

#### 1. README du composant

```markdown
<!-- components/ui/timeline/README.md -->

# Timeline

Un composant Timeline pour afficher des événements chronologiques.

## Installation

```bash
npx shadcn-ui@latest add timeline
```

## Usage

```tsx
import { Timeline, TimelineItem, TimelineDot, TimelineContent } from '@/components/ui/timeline'

<Timeline>
  <TimelineItem>
    <TimelineDot variant="primary" />
    <TimelineContent>
      {/* Contenu */}
    </TimelineContent>
  </TimelineItem>
</Timeline>
```

## API

### Timeline

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `className` | `string` | - | Classes CSS additionnelles |

### TimelineDot

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `variant` | `'default' \| 'primary' \| 'success' \| 'warning' \| 'error'` | `'default'` | Couleur du point |

## Exemples

### Timeline simple

```tsx
<Timeline>
  <TimelineItem>
    <TimelineDot />
    <TimelineContent>
      <p>Événement 1</p>
    </TimelineContent>
  </TimelineItem>
</Timeline>
```

### Timeline avec statuts

```tsx
<Timeline>
  <TimelineItem status="completed">
    <TimelineDot variant="success" />
    <TimelineContent>Complété</TimelineContent>
  </TimelineItem>
  <TimelineItem status="active">
    <TimelineDot variant="primary" />
    <TimelineContent>En cours</TimelineContent>
  </TimelineItem>
</Timeline>
```

## Accessibilité

- Utilise une liste sémantique `<ol>`
- Support du clavier
- ARIA labels appropriés
```

#### 2. Tests

```tsx
// components/ui/timeline/timeline.test.tsx
import { describe, it, expect } from 'vitest'
import { render, screen } from '@testing-library/react'
import { Timeline, TimelineItem, TimelineDot, TimelineContent } from './timeline'

describe('Timeline', () => {
  it('renders correctly', () => {
    render(
      <Timeline>
        <TimelineItem>
          <TimelineDot />
          <TimelineContent>Test content</TimelineContent>
        </TimelineItem>
      </Timeline>
    )
    
    expect(screen.getByText('Test content')).toBeInTheDocument()
  })
  
  it('applies variant classes', () => {
    const { container } = render(
      <Timeline>
        <TimelineItem>
          <TimelineDot variant="primary" />
        </TimelineItem>
      </Timeline>
    )
    
    const dot = container.querySelector('.bg-primary')
    expect(dot).toBeInTheDocument()
  })
})
```

### Partager avec la communauté

#### 1. Créer un registry entry

```json
// registry/timeline.json
{
  "name": "timeline",
  "type": "components:ui",
  "files": [
    {
      "name": "timeline.tsx",
      "content": "... contenu du fichier ..."
    }
  ],
  "dependencies": [
    "class-variance-authority"
  ]
}
```

#### 2. Publier sur GitHub

```bash
# 1. Créer un repo GitHub
# 2. Ajouter votre composant
# 3. Documenter dans le README
# 4. Ajouter des exemples
```

#### 3. Soumettre à Shadcn/UI

Créer une PR sur [shadcn/ui](https://github.com/shadcn/ui) avec :
- Le composant
- La documentation
- Les tests
- Les exemples

---

## Intégration avec Storybook

### Installation

```bash
npx storybook@latest init
```

### Configurer Tailwind

```ts
// .storybook/preview.ts
import type { Preview } from '@storybook/react'
import '../src/index.css' // Importer vos styles

const preview: Preview = {
  parameters: {
    controls: {
      matchers: {
        color: /(background|color)$/i,
        date: /Date$/,
      },
    },
  },
}

export default preview
```

### Créer des stories

```tsx
// components/ui/button/button.stories.tsx
import type { Meta, StoryObj } from '@storybook/react'
import { Button } from './button'
import { Save, Trash } from 'lucide-react'

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
        <Save className="mr-2 h-4 w-4" />
        Save
      </>
    ),
  },
}

export const AllVariants: Story = {
  render: () => (
    <div className="flex flex-col gap-4">
      <div className="flex gap-2">
        <Button variant="default">Default</Button>
        <Button variant="destructive">Destructive</Button>
        <Button variant="outline">Outline</Button>
      </div>
      <div className="flex gap-2">
        <Button variant="secondary">Secondary</Button>
        <Button variant="ghost">Ghost</Button>
        <Button variant="link">Link</Button>
      </div>
    </div>
  ),
}

export const Sizes: Story = {
  render: () => (
    <div className="flex items-center gap-2">
      <Button size="sm">Small</Button>
      <Button size="default">Default</Button>
      <Button size="lg">Large</Button>
      <Button size="icon">
        <Save className="h-4 w-4" />
      </Button>
    </div>
  ),
}

export const Loading: Story = {
  render: () => {
    const [loading, setLoading] = React.useState(false)
    return (
      <Button 
        onClick={() => {
          setLoading(true)
          setTimeout(() => setLoading(false), 2000)
        }}
        disabled={loading}
      >
        {loading && <Loader2 className="mr-2 h-4 w-4 animate-spin" />}
        {loading ? 'Loading...' : 'Click me'}
      </Button>
    )
  },
}
```

### Dark mode dans Storybook

```tsx
// .storybook/preview.tsx
import { ThemeProvider } from '../src/components/theme-provider'

export const decorators = [
  (Story) => (
    <ThemeProvider defaultTheme="system">
      <Story />
    </ThemeProvider>
  ),
]

export const parameters = {
  darkMode: {
    darkClass: 'dark',
    lightClass: 'light',
    stylePreview: true,
  },
}
```

---

## Migration et maintenance à long terme

### Stratégie de mise à jour

#### 1. Versionning sémantique

```json
// package.json
{
  "version": "1.2.3"
  // MAJOR.MINOR.PATCH
  // MAJOR: Breaking changes
  // MINOR: Nouvelles fonctionnalités
  // PATCH: Bug fixes
}
```

#### 2. Changelog

```markdown
<!-- CHANGELOG.md -->

# Changelog

## [1.2.0] - 2024-01-15

### Added
- Nouveau composant Timeline
- Support du dark mode pour tous les composants

### Changed
- Amélioration des performances du Button
- Mise à jour de la palette de couleurs

### Fixed
- Correction du bug de focus sur Input
- Fix de l'accessibilité du Dialog

### Deprecated
- `OldButton` sera supprimé en v2.0.0

## [1.1.0] - 2023-12-01

...
```

#### 3. Migration guides

```markdown
<!-- MIGRATION.md -->

# Migration Guide

## v1.x to v2.0

### Breaking Changes

#### Button component

**Before:**
```tsx
<Button color="primary">Click</Button>
```

**After:**
```tsx
<Button variant="default">Click</Button>
```

### New Features

#### Timeline component

```tsx
import { Timeline } from '@/components/ui/timeline'

<Timeline>
  {/* ... */}
</Timeline>
```
```

### Dependency updates

```bash
# Vérifier les updates
npm outdated

# Update interactif
npx npm-check-updates -i

# Update automatique (attention !)
npx npm-check-updates -u
npm install
```

### Monitoring des dépendances

```bash
# Installer Dependabot (GitHub)
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 10
```

---

## Patterns avancés

### Compound Components Pattern

```tsx
// components/ui/accordion-advanced.tsx
import * as React from 'react'

interface AccordionContextValue {
  openItems: Set<string>
  toggleItem: (id: string) => void
}

const AccordionContext = React.createContext<AccordionContextValue | undefined>(undefined)

function Accordion({ children, ...props }: { children: React.ReactNode }) {
  const [openItems, setOpenItems] = React.useState<Set<string>>(new Set())

  const toggleItem = (id: string) => {
    setOpenItems((prev) => {
      const next = new Set(prev)
      if (next.has(id)) {
        next.delete(id)
      } else {
        next.add(id)
      }
      return next
    })
  }

  return (
    <AccordionContext.Provider value={{ openItems, toggleItem }}>
      <div {...props}>{children}</div>
    </AccordionContext.Provider>
  )
}

function useAccordion() {
  const context = React.useContext(AccordionContext)
  if (!context) {
    throw new Error('useAccordion must be used within Accordion')
  }
  return context
}

function AccordionItem({ 
  id, 
  children 
}: { 
  id: string
  children: React.ReactNode 
}) {
  return <div data-id={id}>{children}</div>
}

function AccordionTrigger({ 
  id, 
  children 
}: { 
  id: string
  children: React.ReactNode 
}) {
  const { openItems, toggleItem } = useAccordion()
  const isOpen = openItems.has(id)

  return (
    <button onClick={() => toggleItem(id)}>
      {children}
      <span>{isOpen ? '−' : '+'}</span>
    </button>
  )
}

function AccordionContent({ 
  id, 
  children 
}: { 
  id: string
  children: React.ReactNode 
}) {
  const { openItems } = useAccordion()
  const isOpen = openItems.has(id)

  if (!isOpen) return null
  return <div>{children}</div>
}

// Usage
<Accordion>
  <AccordionItem id="1">
    <AccordionTrigger id="1">Question 1</AccordionTrigger>
    <AccordionContent id="1">Réponse 1</AccordionContent>
  </AccordionItem>
</Accordion>
```

### Render Props Pattern

```tsx
function DataFetcher<T>({ 
  url, 
  children 
}: { 
  url: string
  children: (data: {
    data: T | null
    loading: boolean
    error: Error | null
  }) => React.ReactNode
}) {
  const [data, setData] = React.useState<T | null>(null)
  const [loading, setLoading] = React.useState(true)
  const [error, setError] = React.useState<Error | null>(null)

  React.useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false))
  }, [url])

  return <>{children({ data, loading, error })}</>
}

// Usage
<DataFetcher<User> url="/api/user">
  {({ data, loading, error }) => {
    if (loading) return <Spinner />
    if (error) return <Error message={error.message} />
    return <UserProfile user={data} />
  }}
</DataFetcher>
```

---

## Résumé du chapitre

Dans ce chapitre final, nous avons exploré :

✅ **Composants personnalisés** : Créer des composants Shadcn
✅ **Contribution** : Documenter et partager
✅ **Storybook** : Design system vivant
✅ **Migration** : Stratégie de mise à jour
✅ **Patterns avancés** : Compound components, Render props

### Points clés à retenir

💡 Suivez les patterns Shadcn pour la cohérence

💡 Documentez et testez vos composants

💡 Storybook facilite la collaboration

💡 Planifiez les migrations avec des guides clairs

---

## Exercices finaux

### Exercice 1 : Composant personnalisé
Créez un composant `Stepper` :
- Étapes avec états (completed, active, pending)
- Navigation entre étapes
- Validation à chaque étape
- Documentation et tests

### Exercice 2 : Storybook complet
Configurez Storybook pour votre projet :
- Stories pour tous vos composants
- Dark mode
- Contrôles interactifs
- Documentation automatique

### Exercice 3 : Design system interne
Créez un design system pour votre entreprise :
- Composants personnalisés
- Guidelines de design
- Exemples d'utilisation
- Process de contribution

---

## Conclusion

**Félicitations !** Vous avez terminé ce livre sur React, Shadcn/UI, Radix et Tailwind CSS.

Vous maîtrisez maintenant :
- Les fondamentaux de React moderne
- La création d'interfaces avec Tailwind CSS
- L'accessibilité avec Radix UI
- Les composants réutilisables avec Shadcn/UI
- L'architecture scalable
- Les animations performantes
- Le déploiement en production

**Continuez à apprendre et à construire !** 🚀

---

**Ressources complémentaires** → [Annexes](../annexes/references.md)

