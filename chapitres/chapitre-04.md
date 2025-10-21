# Chapitre 4 : Radix UI – Les fondations accessibles

## Introduction

Créer des composants UI accessibles est complexe. Gérer le focus, le clavier, ARIA, les lecteurs d'écran... Radix UI résout ces problèmes en fournissant des **primitives** non stylées mais totalement accessibles.

---

## Qu'est-ce qu'une primitive Radix ?

### Définition

Une **primitive** est un composant de base, non stylé, qui gère toute la logique d'accessibilité et de comportement.

```tsx
// Radix fournit la logique
import * as Dialog from '@radix-ui/react-dialog';

// Vous ajoutez les styles
<Dialog.Root>
  <Dialog.Trigger className="px-4 py-2 bg-blue-500 text-white rounded">
    Ouvrir
  </Dialog.Trigger>
  <Dialog.Portal>
    <Dialog.Overlay className="fixed inset-0 bg-black/50" />
    <Dialog.Content className="fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 bg-white p-6 rounded-lg">
      <Dialog.Title className="text-xl font-bold">Titre</Dialog.Title>
      <Dialog.Description className="text-gray-600 mt-2">
        Description du dialog
      </Dialog.Description>
      <Dialog.Close className="mt-4 px-4 py-2 bg-gray-200 rounded">
        Fermer
      </Dialog.Close>
    </Dialog.Content>
  </Dialog.Portal>
</Dialog.Root>
```

### Philosophie de Radix

**Radix ne décide pas de l'apparence** :
- ✅ Aucun CSS imposé
- ✅ Liberté totale de design
- ✅ Compatible avec Tailwind, CSS-in-JS, CSS modules...

**Radix gère la complexité** :
- ✅ Accessibilité (ARIA)
- ✅ Navigation clavier
- ✅ Gestion du focus
- ✅ Comportements attendus
- ✅ États (ouvert, fermé, désactivé...)

---

## L'accessibilité par conception

### Pourquoi l'accessibilité est cruciale

**Légal** :
- Obligation dans de nombreux pays (ADA aux US, lois européennes)
- Risques juridiques si non conforme

**Éthique** :
- 15% de la population mondiale a un handicap
- Tous méritent d'accéder au web

**Business** :
- Plus d'utilisateurs = plus de revenus
- Meilleur SEO
- Image de marque positive

**Technique** :
- Code de meilleure qualité
- Moins de bugs
- Meilleure expérience pour tous

### Ce que Radix gère automatiquement

#### 1. **Attributs ARIA corrects**

```tsx
// Radix génère automatiquement :
<button
  type="button"
  aria-haspopup="dialog"
  aria-expanded="false"
  data-state="closed"
>
  Ouvrir Dialog
</button>

<div
  role="dialog"
  aria-modal="true"
  aria-labelledby="dialog-title"
  aria-describedby="dialog-description"
>
  {/* Contenu */}
</div>
```

#### 2. **Navigation au clavier**

- `Tab` / `Shift+Tab` : Navigation entre éléments focusables
- `Enter` / `Space` : Activer les éléments
- `Escape` : Fermer les dialogs, menus, etc.
- `Arrow keys` : Navigation dans les menus, tabs, etc.

#### 3. **Gestion du focus**

- Focus piégé dans les dialogs (focus trap)
- Retour du focus à l'élément trigger après fermeture
- Ordre de tabulation logique

#### 4. **Compatibilité lecteurs d'écran**

Testé avec :
- NVDA (Windows)
- JAWS (Windows)
- VoiceOver (macOS, iOS)
- TalkBack (Android)

---

## Découverte des primitives essentielles

### Installation

```bash
# Installer les primitives individuellement
npm install @radix-ui/react-dialog
npm install @radix-ui/react-dropdown-menu
npm install @radix-ui/react-tabs
npm install @radix-ui/react-slider

# Ou toutes en une fois (non recommandé en production)
npm install @radix-ui/react-primitive
```

💡 **Astuce** : Installez seulement ce dont vous avez besoin pour réduire la taille du bundle.

---

### Dialog

Un dialog (modal) accessible et personnalisable.

```tsx
// components/dialog-example.tsx
import * as Dialog from '@radix-ui/react-dialog';
import { X } from 'lucide-react';

export function DialogExample() {
  return (
    <Dialog.Root>
      {/* Trigger - Bouton qui ouvre le dialog */}
      <Dialog.Trigger asChild>
        <button className="px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600">
          Ouvrir le dialog
        </button>
      </Dialog.Trigger>

      {/* Portal - Rend le contenu à la racine du DOM */}
      <Dialog.Portal>
        {/* Overlay - Fond sombre */}
        <Dialog.Overlay className="fixed inset-0 bg-black/50 data-[state=open]:animate-in data-[state=closed]:animate-out" />

        {/* Content - Contenu du dialog */}
        <Dialog.Content className="fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 bg-white rounded-lg shadow-xl p-6 w-full max-w-md">
          {/* Title - Titre accessible */}
          <Dialog.Title className="text-xl font-bold text-gray-900 mb-2">
            Confirmer l'action
          </Dialog.Title>

          {/* Description - Description accessible */}
          <Dialog.Description className="text-gray-600 mb-6">
            Êtes-vous sûr de vouloir continuer ? Cette action est irréversible.
          </Dialog.Description>

          {/* Actions */}
          <div className="flex gap-3 justify-end">
            <Dialog.Close asChild>
              <button className="px-4 py-2 bg-gray-200 text-gray-900 rounded-md hover:bg-gray-300">
                Annuler
              </button>
            </Dialog.Close>
            <Dialog.Close asChild>
              <button className="px-4 py-2 bg-red-500 text-white rounded-md hover:bg-red-600">
                Confirmer
              </button>
            </Dialog.Close>
          </div>

          {/* Close button */}
          <Dialog.Close asChild>
            <button
              className="absolute top-4 right-4 p-1 rounded-md text-gray-400 hover:text-gray-600 hover:bg-gray-100"
              aria-label="Fermer"
            >
              <X className="w-4 h-4" />
            </button>
          </Dialog.Close>
        </Dialog.Content>
      </Dialog.Portal>
    </Dialog.Root>
  );
}
```

**Fonctionnalités automatiques** :
- ✅ Focus piégé dans le dialog
- ✅ `Escape` ferme le dialog
- ✅ Click sur l'overlay ferme le dialog
- ✅ Scroll bloqué sur le body
- ✅ Focus retourne au trigger après fermeture

---

### Dropdown Menu

Un menu déroulant avec navigation clavier complète.

```tsx
// components/dropdown-example.tsx
import * as DropdownMenu from '@radix-ui/react-dropdown-menu';
import { User, Settings, LogOut, ChevronRight } from 'lucide-react';

export function DropdownExample() {
  return (
    <DropdownMenu.Root>
      {/* Trigger */}
      <DropdownMenu.Trigger asChild>
        <button className="flex items-center gap-2 px-4 py-2 bg-white border border-gray-300 rounded-md hover:bg-gray-50">
          Mon compte
          <ChevronRight className="w-4 h-4" />
        </button>
      </DropdownMenu.Trigger>

      {/* Portal pour éviter les problèmes de z-index */}
      <DropdownMenu.Portal>
        {/* Content */}
        <DropdownMenu.Content
          className="min-w-[200px] bg-white rounded-md shadow-lg border border-gray-200 p-1"
          sideOffset={5}
        >
          {/* Label (non sélectionnable) */}
          <DropdownMenu.Label className="px-2 py-1.5 text-xs font-semibold text-gray-500">
            Mon compte
          </DropdownMenu.Label>

          {/* Separator */}
          <DropdownMenu.Separator className="h-px bg-gray-200 my-1" />

          {/* Items */}
          <DropdownMenu.Item className="flex items-center gap-2 px-2 py-2 text-sm text-gray-700 rounded hover:bg-gray-100 cursor-pointer outline-none">
            <User className="w-4 h-4" />
            Profil
          </DropdownMenu.Item>

          <DropdownMenu.Item className="flex items-center gap-2 px-2 py-2 text-sm text-gray-700 rounded hover:bg-gray-100 cursor-pointer outline-none">
            <Settings className="w-4 h-4" />
            Paramètres
          </DropdownMenu.Item>

          {/* Sub-menu */}
          <DropdownMenu.Sub>
            <DropdownMenu.SubTrigger className="flex items-center justify-between gap-2 px-2 py-2 text-sm text-gray-700 rounded hover:bg-gray-100 cursor-pointer outline-none">
              Plus
              <ChevronRight className="w-4 h-4" />
            </DropdownMenu.SubTrigger>
            <DropdownMenu.Portal>
              <DropdownMenu.SubContent
                className="min-w-[150px] bg-white rounded-md shadow-lg border border-gray-200 p-1"
                sideOffset={2}
                alignOffset={-5}
              >
                <DropdownMenu.Item className="px-2 py-2 text-sm text-gray-700 rounded hover:bg-gray-100 cursor-pointer outline-none">
                  Aide
                </DropdownMenu.Item>
                <DropdownMenu.Item className="px-2 py-2 text-sm text-gray-700 rounded hover:bg-gray-100 cursor-pointer outline-none">
                  Documentation
                </DropdownMenu.Item>
              </DropdownMenu.SubContent>
            </DropdownMenu.Portal>
          </DropdownMenu.Sub>

          <DropdownMenu.Separator className="h-px bg-gray-200 my-1" />

          {/* Item avec action destructive */}
          <DropdownMenu.Item className="flex items-center gap-2 px-2 py-2 text-sm text-red-600 rounded hover:bg-red-50 cursor-pointer outline-none">
            <LogOut className="w-4 h-4" />
            Déconnexion
          </DropdownMenu.Item>

          {/* Arrow (flèche pointant vers le trigger) */}
          <DropdownMenu.Arrow className="fill-white" />
        </DropdownMenu.Content>
      </DropdownMenu.Portal>
    </DropdownMenu.Root>
  );
}
```

**Fonctionnalités automatiques** :
- ✅ Navigation avec flèches haut/bas
- ✅ `Enter` / `Space` activent l'item
- ✅ `Escape` ferme le menu
- ✅ Ferme automatiquement après sélection
- ✅ Gestion des sub-menus
- ✅ Positionnement intelligent

---

### Tabs

Système d'onglets accessible.

```tsx
// components/tabs-example.tsx
import * as Tabs from '@radix-ui/react-tabs';

export function TabsExample() {
  return (
    <Tabs.Root defaultValue="tab1" className="w-full">
      {/* Liste des onglets */}
      <Tabs.List className="flex border-b border-gray-200">
        <Tabs.Trigger
          value="tab1"
          className="px-4 py-2 text-sm font-medium text-gray-600 border-b-2 border-transparent data-[state=active]:text-blue-600 data-[state=active]:border-blue-600 hover:text-gray-900 transition-colors"
        >
          Profil
        </Tabs.Trigger>
        <Tabs.Trigger
          value="tab2"
          className="px-4 py-2 text-sm font-medium text-gray-600 border-b-2 border-transparent data-[state=active]:text-blue-600 data-[state=active]:border-blue-600 hover:text-gray-900 transition-colors"
        >
          Paramètres
        </Tabs.Trigger>
        <Tabs.Trigger
          value="tab3"
          className="px-4 py-2 text-sm font-medium text-gray-600 border-b-2 border-transparent data-[state=active]:text-blue-600 data-[state=active]:border-blue-600 hover:text-gray-900 transition-colors"
        >
          Notifications
        </Tabs.Trigger>
      </Tabs.List>

      {/* Contenu des onglets */}
      <Tabs.Content value="tab1" className="p-4">
        <h3 className="text-lg font-semibold mb-2">Profil</h3>
        <p className="text-gray-600">
          Gérez vos informations personnelles et votre avatar.
        </p>
      </Tabs.Content>

      <Tabs.Content value="tab2" className="p-4">
        <h3 className="text-lg font-semibold mb-2">Paramètres</h3>
        <p className="text-gray-600">
          Configurez vos préférences d'application.
        </p>
      </Tabs.Content>

      <Tabs.Content value="tab3" className="p-4">
        <h3 className="text-lg font-semibold mb-2">Notifications</h3>
        <p className="text-gray-600">
          Gérez comment vous souhaitez être notifié.
        </p>
      </Tabs.Content>
    </Tabs.Root>
  );
}
```

**Fonctionnalités automatiques** :
- ✅ Navigation avec flèches gauche/droite
- ✅ `Home` / `End` pour premier/dernier onglet
- ✅ ARIA roles et états corrects
- ✅ Affichage du contenu associé

---

### Slider

Un curseur accessible pour sélectionner des valeurs.

```tsx
// components/slider-example.tsx
import * as Slider from '@radix-ui/react-slider';
import { useState } from 'react';

export function SliderExample() {
  const [value, setValue] = useState([50]);

  return (
    <div className="w-full max-w-md">
      <label className="block text-sm font-medium text-gray-700 mb-2">
        Volume : {value[0]}%
      </label>

      <Slider.Root
        className="relative flex items-center w-full h-5 select-none touch-none"
        value={value}
        onValueChange={setValue}
        max={100}
        step={1}
      >
        {/* Track (piste) */}
        <Slider.Track className="relative bg-gray-200 rounded-full h-1 flex-grow">
          {/* Range (partie remplie) */}
          <Slider.Range className="absolute bg-blue-500 rounded-full h-full" />
        </Slider.Track>

        {/* Thumb (curseur) */}
        <Slider.Thumb className="block w-5 h-5 bg-white border-2 border-blue-500 rounded-full hover:bg-blue-50 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2" />
      </Slider.Root>

      {/* Range Slider (deux curseurs) */}
      <div className="mt-8">
        <label className="block text-sm font-medium text-gray-700 mb-2">
          Fourchette de prix
        </label>
        <Slider.Root
          className="relative flex items-center w-full h-5"
          defaultValue={[25, 75]}
          max={100}
          step={5}
        >
          <Slider.Track className="relative bg-gray-200 rounded-full h-1 flex-grow">
            <Slider.Range className="absolute bg-green-500 rounded-full h-full" />
          </Slider.Track>
          <Slider.Thumb className="block w-5 h-5 bg-white border-2 border-green-500 rounded-full" />
          <Slider.Thumb className="block w-5 h-5 bg-white border-2 border-green-500 rounded-full" />
        </Slider.Root>
      </div>
    </div>
  );
}
```

**Fonctionnalités automatiques** :
- ✅ Navigation avec flèches
- ✅ `Home` / `End` pour min/max
- ✅ `PageUp` / `PageDown` pour sauts plus grands
- ✅ Support du tactile
- ✅ Multi-thumbs (plusieurs curseurs)

---

## Customisation avec Tailwind

### Data attributes

Radix expose des data attributes pour styliser selon l'état :

```tsx
// États disponibles
data-state="open"       // Ouvert
data-state="closed"     // Fermé
data-disabled           // Désactivé
data-highlighted        // Survolé (dropdown items)
data-orientation        // horizontal / vertical
```

### Exemple : Animations avec Tailwind

```tsx
// Dialog avec animations
<Dialog.Overlay className="
  fixed inset-0 bg-black/50
  data-[state=open]:animate-in
  data-[state=open]:fade-in-0
  data-[state=closed]:animate-out
  data-[state=closed]:fade-out-0
" />

<Dialog.Content className="
  fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2
  bg-white rounded-lg p-6
  data-[state=open]:animate-in
  data-[state=open]:fade-in-0
  data-[state=open]:zoom-in-95
  data-[state=closed]:animate-out
  data-[state=closed]:fade-out-0
  data-[state=closed]:zoom-out-95
" />
```

### Configurer les animations dans Tailwind

```js
// tailwind.config.js
export default {
  theme: {
    extend: {
      keyframes: {
        'fade-in': {
          from: { opacity: 0 },
          to: { opacity: 1 },
        },
        'fade-out': {
          from: { opacity: 1 },
          to: { opacity: 0 },
        },
        'zoom-in': {
          from: { transform: 'scale(0.95)' },
          to: { transform: 'scale(1)' },
        },
      },
      animation: {
        'fade-in': 'fade-in 0.2s ease-out',
        'fade-out': 'fade-out 0.2s ease-in',
        'zoom-in': 'zoom-in 0.2s ease-out',
      },
    },
  },
}
```

---

## Intégration dans un design system

### Créer des composants réutilisables

```tsx
// components/ui/dialog.tsx
import * as DialogPrimitive from '@radix-ui/react-dialog';
import { X } from 'lucide-react';
import { cn } from '@/lib/utils';

// Root - Réexporter tel quel
const Dialog = DialogPrimitive.Root;
const DialogTrigger = DialogPrimitive.Trigger;
const DialogPortal = DialogPrimitive.Portal;
const DialogClose = DialogPrimitive.Close;

// Overlay - Avec styles par défaut
const DialogOverlay = ({ className, ...props }: DialogPrimitive.DialogOverlayProps) => (
  <DialogPrimitive.Overlay
    className={cn(
      'fixed inset-0 z-50 bg-black/50',
      'data-[state=open]:animate-in data-[state=open]:fade-in-0',
      'data-[state=closed]:animate-out data-[state=closed]:fade-out-0',
      className
    )}
    {...props}
  />
);

// Content - Avec styles par défaut
const DialogContent = ({ 
  className, 
  children, 
  ...props 
}: DialogPrimitive.DialogContentProps) => (
  <DialogPortal>
    <DialogOverlay />
    <DialogPrimitive.Content
      className={cn(
        'fixed left-1/2 top-1/2 z-50 -translate-x-1/2 -translate-y-1/2',
        'w-full max-w-lg',
        'bg-white rounded-lg shadow-lg p-6',
        'data-[state=open]:animate-in data-[state=open]:fade-in-0 data-[state=open]:zoom-in-95',
        'data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=closed]:zoom-out-95',
        className
      )}
      {...props}
    >
      {children}
      <DialogPrimitive.Close className="absolute right-4 top-4 rounded-sm opacity-70 hover:opacity-100">
        <X className="h-4 w-4" />
        <span className="sr-only">Fermer</span>
      </DialogPrimitive.Close>
    </DialogPrimitive.Content>
  </DialogPortal>
);

// Title - Avec styles par défaut
const DialogTitle = ({ className, ...props }: DialogPrimitive.DialogTitleProps) => (
  <DialogPrimitive.Title
    className={cn('text-xl font-semibold text-gray-900', className)}
    {...props}
  />
);

// Description - Avec styles par défaut
const DialogDescription = ({ className, ...props }: DialogPrimitive.DialogDescriptionProps) => (
  <DialogPrimitive.Description
    className={cn('text-sm text-gray-600 mt-2', className)}
    {...props}
  />
);

// Exporter tous les composants
export {
  Dialog,
  DialogTrigger,
  DialogContent,
  DialogTitle,
  DialogDescription,
  DialogClose,
};
```

### Utilisation simplifiée

```tsx
// Maintenant l'utilisation est clean !
import {
  Dialog,
  DialogContent,
  DialogDescription,
  DialogTitle,
  DialogTrigger,
} from '@/components/ui/dialog';

function MyComponent() {
  return (
    <Dialog>
      <DialogTrigger asChild>
        <button>Ouvrir</button>
      </DialogTrigger>
      <DialogContent>
        <DialogTitle>Mon Dialog</DialogTitle>
        <DialogDescription>
          Une description accessible
        </DialogDescription>
        {/* Contenu */}
      </DialogContent>
    </Dialog>
  );
}
```

---

## Résumé du chapitre

Dans ce chapitre, nous avons découvert Radix UI :

✅ **Primitives** : Composants non stylés avec logique complète
✅ **Accessibilité** : ARIA, clavier, focus, lecteurs d'écran
✅ **Composants** : Dialog, Dropdown Menu, Tabs, Slider
✅ **Customisation** : Data attributes, Tailwind, animations
✅ **Design system** : Wrapper les primitives pour la réutilisabilité

### Points clés à retenir

💡 Radix gère toute la complexité de l'accessibilité

💡 Vous gardez le contrôle total sur les styles

💡 Les data attributes permettent des animations conditionnelles

💡 Wrapper les primitives dans votre design system pour la cohérence

---

## Exercices

### Exercice 1 : Dialog de confirmation
Créez un dialog de confirmation avec :
- Titre et description clairs
- Boutons Annuler et Confirmer
- Animation d'entrée/sortie
- Callback onConfirm

### Exercice 2 : Menu utilisateur
Créez un dropdown menu avec :
- Avatar comme trigger
- Nom et email de l'utilisateur
- Items de navigation
- Item de déconnexion en rouge

### Exercice 3 : Settings avec Tabs
Créez une page de paramètres avec :
- Tabs pour différentes sections
- Formulaires dans chaque tab
- Navigation clavier fonctionnelle

---

**Prêt à découvrir Shadcn/UI ?** → [Chapitre 5 : Découverte de Shadcn/UI](./chapitre-05.md)

