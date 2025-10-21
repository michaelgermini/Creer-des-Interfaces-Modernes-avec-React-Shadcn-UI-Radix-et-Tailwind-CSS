# Chapitre 10 : Accessibilité et UX

## Introduction

L'accessibilité n'est pas une option, c'est une nécessité. Dans ce chapitre, vous apprendrez à créer des interfaces utilisables par tous, quelles que soient leurs capacités.

---

## Les bases de l'accessibilité

### Pourquoi l'accessibilité est cruciale

**Pour les utilisateurs** :
- 15% de la population mondiale a un handicap
- Tous méritent l'égalité d'accès
- Bénéficie également aux utilisateurs sans handicap

**Pour votre business** :
- Augmente votre audience potentielle
- Améliore le SEO
- Réduit les risques juridiques
- Améliore l'expérience pour tous

**Pour la technique** :
- Force un code de meilleure qualité
- Encourage la sémantique HTML
- Améliore la maintenabilité

### Les 4 principes de WCAG

**POUR** (Perceivable, Operable, Understandable, Robust)

#### 1. Perceptible
L'information doit être présentée de manière à ce que les utilisateurs puissent la percevoir.

```tsx
// ✅ Bon : Texte alternatif pour les images
<img src="product.jpg" alt="iPhone 15 Pro en titane naturel" />

// ❌ Mauvais : Pas de texte alternatif
<img src="product.jpg" />

// ✅ Bon : Labels pour les inputs
<label htmlFor="email">Email</label>
<input id="email" type="email" />

// ❌ Mauvais : Input sans label
<input type="email" placeholder="Email" />
```

#### 2. Utilisable
L'interface doit être opérable (navigation clavier, temps suffisant, etc.)

```tsx
// ✅ Bon : Navigation au clavier
<button onClick={handleClick}>Cliquer</button>

// ❌ Mauvais : Div cliquable sans accessibilité
<div onClick={handleClick}>Cliquer</div>

// ✅ Bon : Ordre de tabulation logique
<form>
  <input tabIndex={1} />
  <input tabIndex={2} />
  <button tabIndex={3}>Submit</button>
</form>
```

#### 3. Compréhensible
L'information et l'interface doivent être compréhensibles.

```tsx
// ✅ Bon : Messages d'erreur clairs
<Input 
  error="L'email doit contenir un @" 
/>

// ❌ Mauvais : Message d'erreur vague
<Input 
  error="Invalide" 
/>

// ✅ Bon : Instructions claires
<form aria-describedby="password-requirements">
  <input type="password" />
  <p id="password-requirements">
    Le mot de passe doit contenir au moins 8 caractères, 
    une majuscule et un chiffre.
  </p>
</form>
```

#### 4. Robuste
Le contenu doit être interprétable par une large variété de technologies.

```tsx
// ✅ Bon : HTML sémantique
<nav>
  <ul>
    <li><a href="/home">Accueil</a></li>
    <li><a href="/about">À propos</a></li>
  </ul>
</nav>

// ❌ Mauvais : Divs pour tout
<div>
  <div onClick={() => navigate('/home')}>Accueil</div>
  <div onClick={() => navigate('/about')}>À propos</div>
</div>
```

---

## ARIA (Accessible Rich Internet Applications)

### Qu'est-ce qu'ARIA ?

ARIA ajoute des informations sémantiques aux éléments HTML pour les technologies d'assistance.

### Les 3 types d'attributs ARIA

#### 1. Roles

Définissent le type d'élément :

```tsx
// Roles de structure
<div role="banner">Header</div>
<div role="navigation">Nav</div>
<div role="main">Contenu principal</div>
<div role="complementary">Sidebar</div>
<div role="contentinfo">Footer</div>

// Roles d'interaction
<div role="button">Cliquer</div>
<div role="checkbox">Option</div>
<div role="dialog">Modal</div>
<div role="tab">Onglet</div>

// Roles de structure
<div role="list">
  <div role="listitem">Item 1</div>
  <div role="listitem">Item 2</div>
</div>
```

💡 **Note** : Préférez toujours les éléments HTML sémantiques quand c'est possible !

```tsx
// ✅ Préféré : HTML sémantique
<button>Cliquer</button>
<nav>Navigation</nav>

// ⚠️ Seulement si nécessaire : ARIA sur div
<div role="button" tabIndex={0}>Cliquer</div>
```

#### 2. États et propriétés

Décrivent l'état actuel d'un élément :

```tsx
// aria-expanded : ouvert ou fermé
<button aria-expanded={isOpen}>
  Menu
</button>

// aria-selected : sélectionné ou non
<div role="tab" aria-selected={isActive}>
  Onglet
</div>

// aria-checked : coché ou non
<div role="checkbox" aria-checked={isChecked}>
  Option
</div>

// aria-disabled : désactivé
<button aria-disabled={isLoading}>
  Envoyer
</button>

// aria-hidden : caché des lecteurs d'écran
<div aria-hidden="true">
  <DecorativeIcon />
</div>

// aria-label : label alternatif
<button aria-label="Fermer le modal">
  <X />
</button>

// aria-describedby : description détaillée
<input
  aria-describedby="email-description"
/>
<p id="email-description">
  Nous ne partagerons jamais votre email
</p>

// aria-labelledby : label par référence
<div
  role="dialog"
  aria-labelledby="dialog-title"
  aria-describedby="dialog-description"
>
  <h2 id="dialog-title">Confirmer</h2>
  <p id="dialog-description">Êtes-vous sûr ?</p>
</div>
```

#### 3. Live regions

Annoncent les changements dynamiques :

```tsx
// aria-live : annonce les changements
<div aria-live="polite" aria-atomic="true">
  {message}
</div>

// Valeurs :
// - "off" : pas d'annonce (défaut)
// - "polite" : annonce quand l'utilisateur est inactif
// - "assertive" : annonce immédiatement

// Exemple : Toast notification
function Toast({ message }: { message: string }) {
  return (
    <div
      role="status"
      aria-live="polite"
      aria-atomic="true"
      className="toast"
    >
      {message}
    </div>
  )
}

// Exemple : Compteur en direct
function LiveCounter({ count }: { count: number }) {
  return (
    <div aria-live="polite" aria-atomic="true">
      Compteur : {count}
    </div>
  )
}
```

---

## Composants accessibles avec Radix

### Dialog accessible

```tsx
import * as Dialog from '@radix-ui/react-dialog'
import { X } from 'lucide-react'

function AccessibleDialog() {
  return (
    <Dialog.Root>
      <Dialog.Trigger asChild>
        <button className="btn-primary">
          Ouvrir
        </button>
      </Dialog.Trigger>

      <Dialog.Portal>
        <Dialog.Overlay className="dialog-overlay" />
        
        <Dialog.Content className="dialog-content">
          {/* Title et Description sont obligatoires pour l'accessibilité */}
          <Dialog.Title className="dialog-title">
            Confirmer l'action
          </Dialog.Title>
          
          <Dialog.Description className="dialog-description">
            Cette action est irréversible. Êtes-vous sûr de vouloir continuer ?
          </Dialog.Description>

          <div className="dialog-actions">
            <Dialog.Close asChild>
              <button className="btn-secondary">
                Annuler
              </button>
            </Dialog.Close>
            <button className="btn-destructive">
              Confirmer
            </button>
          </div>

          <Dialog.Close asChild>
            <button 
              className="dialog-close-button"
              aria-label="Fermer"
            >
              <X className="w-4 h-4" />
            </button>
          </Dialog.Close>
        </Dialog.Content>
      </Dialog.Portal>
    </Dialog.Root>
  )
}
```

**Ce que Radix gère automatiquement** :
- ✅ Focus piégé dans le dialog
- ✅ `Escape` ferme le dialog
- ✅ Focus retourne au trigger après fermeture
- ✅ `aria-labelledby` et `aria-describedby` automatiques
- ✅ `role="dialog"` et `aria-modal="true"`
- ✅ Scroll bloqué sur le body

### Dropdown Menu accessible

```tsx
import * as DropdownMenu from '@radix-ui/react-dropdown-menu'

function AccessibleDropdown() {
  return (
    <DropdownMenu.Root>
      <DropdownMenu.Trigger asChild>
        <button className="btn">
          Options
        </button>
      </DropdownMenu.Trigger>

      <DropdownMenu.Portal>
        <DropdownMenu.Content className="dropdown-content">
          <DropdownMenu.Item className="dropdown-item">
            Modifier
          </DropdownMenu.Item>
          
          <DropdownMenu.Item className="dropdown-item">
            Dupliquer
          </DropdownMenu.Item>

          <DropdownMenu.Separator className="dropdown-separator" />

          <DropdownMenu.Item 
            className="dropdown-item text-red-600"
            onSelect={() => console.log('Supprimer')}
          >
            Supprimer
          </DropdownMenu.Item>
        </DropdownMenu.Content>
      </DropdownMenu.Portal>
    </DropdownMenu.Root>
  )
}
```

**Navigation clavier automatique** :
- ✅ `↓` / `↑` : Naviguer dans les items
- ✅ `Enter` / `Space` : Sélectionner
- ✅ `Escape` : Fermer
- ✅ First letter navigation

---

## Contraste, motion et préférences utilisateur

### Contraste des couleurs

#### Ratio minimum WCAG

- **AA** : 4.5:1 pour texte normal, 3:1 pour texte large
- **AAA** : 7:1 pour texte normal, 4.5:1 pour texte large

#### Vérifier le contraste

```tsx
// ✅ Bon : contraste suffisant
<p className="text-gray-900 dark:text-gray-100">
  Texte lisible
</p>

// ❌ Mauvais : faible contraste
<p className="text-gray-400">
  Texte difficile à lire
</p>
```

Outils pour vérifier :
- Chrome DevTools (Lighthouse)
- [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/)
- Extension Axe DevTools

#### Composant avec contraste garanti

```tsx
// components/ui/text.tsx
const textVariants = cva('', {
  variants: {
    variant: {
      // Contrastes garantis WCAG AA
      default: 'text-foreground',
      muted: 'text-muted-foreground', // Minimum 4.5:1
      subtle: 'text-gray-600 dark:text-gray-400', // 4.5:1
    },
    size: {
      sm: 'text-sm',
      base: 'text-base',
      lg: 'text-lg', // Texte large = ratio 3:1 acceptable
    },
  },
})
```

### Reduced Motion

Respecter la préférence utilisateur pour les animations :

```css
/* styles/globals.css */

/* Animations par défaut */
.animate-fade-in {
  animation: fadeIn 0.3s ease-out;
}

.animate-slide-in {
  animation: slideIn 0.3s ease-out;
}

/* Désactiver si l'utilisateur préfère réduire les animations */
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

```tsx
// Hook pour détecter la préférence
import { useState, useEffect } from 'react'

export function usePrefersReducedMotion() {
  const [prefersReducedMotion, setPrefersReducedMotion] = useState(false)

  useEffect(() => {
    const mediaQuery = window.matchMedia('(prefers-reduced-motion: reduce)')
    setPrefersReducedMotion(mediaQuery.matches)

    const handleChange = (e: MediaQueryListEvent) => {
      setPrefersReducedMotion(e.matches)
    }

    mediaQuery.addEventListener('change', handleChange)
    return () => mediaQuery.removeEventListener('change', handleChange)
  }, [])

  return prefersReducedMotion
}
```

```tsx
// Utilisation
function AnimatedComponent() {
  const prefersReducedMotion = usePrefersReducedMotion()

  return (
    <motion.div
      animate={{ opacity: 1, y: 0 }}
      initial={{ opacity: 0, y: prefersReducedMotion ? 0 : 20 }}
      transition={{ duration: prefersReducedMotion ? 0 : 0.3 }}
    >
      Contenu
    </motion.div>
  )
}
```

### Focus visible

```css
/* styles/globals.css */

/* Focus visible seulement au clavier, pas au clic */
:focus-visible {
  outline: 2px solid hsl(var(--primary));
  outline-offset: 2px;
}

/* Pas de focus au clic de souris */
:focus:not(:focus-visible) {
  outline: none;
}
```

```tsx
// Composant Button avec focus visible
const buttonVariants = cva(
  'focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2',
  // ...
)
```

### Taille du texte

```css
/* Respecter la taille de texte de l'utilisateur */
/* Utiliser rem (relative) plutôt que px (absolue) */

/* ✅ Bon */
.text-base {
  font-size: 1rem; /* S'adapte aux préférences utilisateur */
}

/* ❌ Éviter */
.text-base {
  font-size: 16px; /* Fixe, ignore les préférences */
}
```

---

## Tester l'accessibilité

### Tests automatiques

#### 1. Lighthouse (Chrome DevTools)

```bash
# Ouvrir DevTools > Lighthouse
# Cocher "Accessibility"
# Générer le rapport
```

#### 2. Axe DevTools

```bash
# Extension Chrome/Firefox
# Analyser la page
# Corriger les erreurs détectées
```

#### 3. Tests automatisés

```bash
npm install -D @axe-core/react
```

```tsx
// main.tsx (development only)
if (process.env.NODE_ENV !== 'production') {
  import('@axe-core/react').then((axe) => {
    axe.default(React, ReactDOM, 1000)
  })
}
```

### Tests manuels

#### 1. Navigation au clavier

Testez sans souris :
- `Tab` : Naviguer vers l'avant
- `Shift + Tab` : Naviguer vers l'arrière
- `Enter` / `Space` : Activer
- `Escape` : Fermer/Annuler
- `↑` `↓` `←` `→` : Navigation contextuelle

#### 2. Lecteur d'écran

**Windows** : NVDA (gratuit)
**macOS** : VoiceOver (intégré)
**Linux** : Orca

```tsx
// Tester que tous les éléments sont annoncés correctement
function TestAccessibility() {
  return (
    <>
      {/* Button */}
      <button>Cliquer ici</button>
      {/* Annoncé : "Cliquer ici, button" */}

      {/* Link */}
      <a href="/about">En savoir plus</a>
      {/* Annoncé : "En savoir plus, link" */}

      {/* Image */}
      <img src="photo.jpg" alt="Photo de profil de Jean Dupont" />
      {/* Annoncé : "Photo de profil de Jean Dupont, image" */}

      {/* Form */}
      <label htmlFor="email">Email</label>
      <input id="email" type="email" required />
      {/* Annoncé : "Email, edit text, required" */}
    </>
  )
}
```

#### 3. Zoom

Testez avec différents niveaux de zoom (jusqu'à 200%) :
- Le contenu doit rester lisible
- Pas de scroll horizontal
- Pas de contenu coupé

```css
/* Layout responsive au zoom */
.container {
  max-width: 100%;
  padding: 1rem;
}

/* Texte qui s'adapte */
body {
  font-size: 1rem; /* Utiliser rem */
}
```

---

## Checklist d'accessibilité

### Images et médias

- [ ] Toutes les images ont un attribut `alt`
- [ ] Les images décoratives ont `alt=""`
- [ ] Les vidéos ont des sous-titres
- [ ] Les GIFs/animations peuvent être mis en pause

### Formulaires

- [ ] Tous les inputs ont des labels
- [ ] Les erreurs sont explicites et annoncées
- [ ] La validation en temps réel est accessible
- [ ] L'ordre de tabulation est logique

### Navigation

- [ ] Navigation au clavier complète
- [ ] Focus visible sur tous les éléments interactifs
- [ ] Skip links pour sauter la navigation
- [ ] Breadcrumbs pour l'orientation

### Couleurs et contraste

- [ ] Contraste minimum 4.5:1 (texte normal)
- [ ] Contraste minimum 3:1 (texte large)
- [ ] L'information n'est pas véhiculée uniquement par la couleur
- [ ] Mode sombre avec contraste suffisant

### Interactivité

- [ ] Tous les composants fonctionnent au clavier
- [ ] Les modals piègent le focus
- [ ] Les notifications live sont annoncées
- [ ] Reduced motion respecté

---

## Résumé du chapitre

Dans ce chapitre, nous avons exploré l'accessibilité :

✅ **Bases** : WCAG, ARIA, HTML sémantique
✅ **Radix** : Composants accessibles par défaut
✅ **Contraste** : Couleurs lisibles pour tous
✅ **Préférences** : Reduced motion, taille de texte
✅ **Tests** : Automatiques et manuels

### Points clés à retenir

💡 L'accessibilité bénéficie à tous les utilisateurs

💡 Utilisez HTML sémantique avant ARIA

💡 Radix gère la complexité pour vous

💡 Testez avec de vrais outils d'assistance

---

## Exercices

### Exercice 1 : Audit d'accessibilité
Auditez un site web avec :
- Lighthouse
- Axe DevTools
- Navigation clavier
- Listez 5 problèmes et proposez des solutions

### Exercice 2 : Formulaire accessible
Créez un formulaire de contact avec :
- Labels explicites
- Validation accessible
- Messages d'erreur annoncés
- Navigation clavier complète

### Exercice 3 : Modal accessible
Implémentez un modal qui :
- Piège le focus
- Se ferme avec Escape
- Retourne le focus après fermeture
- Annonce son ouverture

---

**Prêt à animer votre interface ?** → [Chapitre 11 : Animation et interactions](./chapitre-11.md)

