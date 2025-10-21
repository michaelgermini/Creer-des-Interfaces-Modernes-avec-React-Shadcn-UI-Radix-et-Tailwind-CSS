# Annexe A : Glossaire des termes techniques

## A

**Accessibilité (a11y)**
: Pratique consistant à rendre les sites web utilisables par tous, y compris les personnes en situation de handicap.

**ARIA (Accessible Rich Internet Applications)**
: Ensemble d'attributs qui définissent des moyens pour rendre le contenu web et les applications web plus accessibles.

**Atomic Design**
: Méthodologie de conception d'interfaces basée sur la composition de petits composants réutilisables.

**Attribute**
: Propriété HTML ou ARIA qui fournit des informations supplémentaires sur un élément.

## B

**Barrel Export**
: Pattern consistant à regrouper plusieurs exports dans un fichier `index.ts` pour simplifier les imports.

**Breakpoint**
: Point de rupture dans le design responsive où la mise en page change (mobile, tablet, desktop).

**Bundle**
: Fichier JavaScript regroupant tout le code de l'application après le processus de build.

**Build**
: Processus de compilation et d'optimisation du code pour la production.

## C

**Class Variance Authority (CVA)**
: Bibliothèque pour gérer les variants de classes CSS de manière type-safe.

**Client-side Rendering (CSR)**
: Rendu de l'application côté client (navigateur) via JavaScript.

**Component**
: Bloc de code réutilisable qui retourne un élément de l'interface utilisateur.

**Composant contrôlé**
: Composant dont l'état est géré par React (via `useState`).

**Composant non contrôlé**
: Composant qui gère son propre état en interne (via ref).

**Context API**
: API React pour partager des données entre composants sans props drilling.

**CSS-in-JS**
: Approche d'écriture de CSS directement dans les fichiers JavaScript.

**CSS Variables (Custom Properties)**
: Variables CSS natives permettant de stocker et réutiliser des valeurs.

## D

**Dark Mode**
: Schéma de couleurs sombres pour réduire la fatigue oculaire et économiser l'énergie.

**Design System**
: Ensemble de standards, composants et guidelines pour créer des interfaces cohérentes.

**Design Tokens**
: Variables qui stockent les valeurs de design (couleurs, espacements, typographie).

**Destructuring**
: Syntaxe JavaScript pour extraire des valeurs d'objets ou tableaux.

**DOM (Document Object Model)**
: Représentation en arbre de la structure HTML d'une page.

## E

**Effect**
: Action secondaire dans un composant React (appels API, subscriptions, etc.).

**Environment Variables**
: Variables de configuration définies en dehors du code source.

## F

**Focus Trap**
: Technique qui maintient le focus clavier à l'intérieur d'un élément (modal, dialog).

**Forwarding Ref**
: Pattern React permettant de passer une ref à travers un composant vers un enfant.

**Fragment**
: Élément React (`<>...</>`) qui permet de grouper des éléments sans ajouter de nœud DOM.

**Framer Motion**
: Bibliothèque d'animation pour React.

## H

**Higher-Order Component (HOC)**
: Fonction qui prend un composant et retourne un nouveau composant avec des props supplémentaires.

**Hook**
: Fonction React qui permet d'utiliser des fonctionnalités React (état, effets, etc.) dans les composants fonctionnels.

**HSL (Hue, Saturation, Lightness)**
: Modèle de couleur basé sur la teinte, la saturation et la luminosité.

## I

**Immutability**
: Principe consistant à ne jamais modifier directement les données, mais à créer de nouvelles copies.

**Import Alias**
: Raccourci pour les chemins d'import (ex: `@/components` au lieu de `../../components`).

## J

**JSX (JavaScript XML)**
: Extension de syntaxe permettant d'écrire du HTML dans du JavaScript.

**JIT (Just-In-Time)**
: Mode de compilation Tailwind qui génère les classes CSS à la demande.

## K

**Key**
: Prop spéciale React utilisée pour identifier les éléments dans une liste.

## L

**Lazy Loading**
: Technique de chargement différé des ressources pour améliorer les performances.

**Layout Shift**
: Mouvement inattendu du contenu pendant le chargement de la page.

**Linter**
: Outil d'analyse statique du code pour détecter les erreurs et maintenir la qualité.

**Lucide React**
: Bibliothèque d'icônes pour React.

## M

**Memo (React.memo)**
: HOC qui optimise les performances en évitant les re-renders inutiles.

**Micro-interaction**
: Petite animation ou feedback visuel lors d'une interaction utilisateur.

**Minification**
: Processus de réduction de la taille du code en supprimant les espaces et commentaires.

## P

**Pattern**
: Solution réutilisable à un problème courant en programmation.

**Portal**
: Fonctionnalité React pour rendre un composant en dehors de sa hiérarchie DOM.

**Primitive**
: Composant de base non stylé qui fournit la logique et l'accessibilité.

**Props (Properties)**
: Données passées d'un composant parent à un composant enfant.

**Props Drilling**
: Anti-pattern consistant à passer des props à travers plusieurs niveaux de composants.

**Purge CSS**
: Processus de suppression des styles CSS non utilisés.

## R

**Radix UI**
: Collection de primitives React accessibles et non stylées.

**React Server Components (RSC)**
: Composants React qui s'exécutent uniquement côté serveur.

**Reconciliation**
: Processus React de comparaison du Virtual DOM avec le DOM réel.

**Ref**
: Référence à un élément DOM ou à une valeur qui persiste entre les renders.

**Render**
: Processus de génération de l'interface utilisateur à partir des composants.

**Responsive Design**
: Approche de conception qui s'adapte à différentes tailles d'écran.

## S

**Semantic HTML**
: Utilisation de balises HTML qui ont une signification (nav, article, header, etc.).

**Server-Side Rendering (SSR)**
: Rendu de l'application côté serveur avant l'envoi au client.

**Shadcn/UI**
: Collection de composants React copiables construits avec Radix UI et Tailwind.

**Side Effect**
: Opération qui affecte quelque chose en dehors du composant (API, DOM, timers).

**Slot**
: Pattern Radix permettant de rendre un composant comme un autre élément.

**State**
: Données qui peuvent changer au fil du temps dans un composant.

**Static Site Generation (SSG)**
: Génération de pages HTML statiques au moment du build.

**Storybook**
: Outil de développement et documentation de composants UI.

## T

**Tailwind CSS**
: Framework CSS utility-first pour construire des interfaces rapidement.

**Theme**
: Ensemble de valeurs de design (couleurs, espacements, etc.) appliqué à l'interface.

**Token**
: Valeur atomique d'un design system (couleur, espacement, typographie).

**Tree Shaking**
: Processus de suppression du code JavaScript non utilisé lors du build.

**TypeScript**
: Sur-ensemble de JavaScript avec typage statique.

## U

**Utility-first CSS**
: Approche CSS basée sur des classes utilitaires à fonction unique.

**useCallback**
: Hook React pour mémoriser des fonctions entre les renders.

**useContext**
: Hook React pour consommer un Context.

**useEffect**
: Hook React pour gérer les effets de bord.

**useMemo**
: Hook React pour mémoriser des valeurs calculées.

**useRef**
: Hook React pour créer une référence persistante.

**useState**
: Hook React pour gérer l'état local d'un composant.

## V

**Variant**
: Différente version visuelle d'un composant (primary, secondary, etc.).

**Virtual DOM**
: Représentation en mémoire du DOM réel utilisée par React pour optimiser les mises à jour.

**Vite**
: Outil de build moderne et rapide pour les applications web.

## W

**WCAG (Web Content Accessibility Guidelines)**
: Ensemble de recommandations pour rendre le contenu web accessible.

**Web Vitals**
: Métriques essentielles pour mesurer l'expérience utilisateur web.

**Webpack**
: Bundler de modules JavaScript (alternative à Vite).

## Z

**z-index**
: Propriété CSS qui contrôle l'ordre d'empilement des éléments.

---

## Acronymes courants

| Acronyme | Signification |
|----------|---------------|
| **a11y** | Accessibility (11 lettres entre 'a' et 'y') |
| **API** | Application Programming Interface |
| **ARIA** | Accessible Rich Internet Applications |
| **CLS** | Cumulative Layout Shift |
| **CSR** | Client-Side Rendering |
| **CSS** | Cascading Style Sheets |
| **CVA** | Class Variance Authority |
| **DOM** | Document Object Model |
| **DX** | Developer Experience |
| **FCP** | First Contentful Paint |
| **FID** | First Input Delay |
| **HOC** | Higher-Order Component |
| **HTML** | HyperText Markup Language |
| **i18n** | Internationalization |
| **JSX** | JavaScript XML |
| **LCP** | Largest Contentful Paint |
| **RSC** | React Server Components |
| **SEO** | Search Engine Optimization |
| **SPA** | Single Page Application |
| **SSG** | Static Site Generation |
| **SSR** | Server-Side Rendering |
| **TTFB** | Time To First Byte |
| **TTI** | Time To Interactive |
| **UI** | User Interface |
| **UX** | User Experience |
| **WCAG** | Web Content Accessibility Guidelines |

---

[← Retour au sommaire](../README.md) | [Références →](./references.md)

