# Chapitre 10 : Accessibilité et UX

## 📊 Résumé exécutif

**Objectif** : Créer des interfaces web accessibles à tous, conformes aux standards WCAG 2.1, et offrant une expérience utilisateur optimale pour 100% des utilisateurs, y compris les personnes en situation de handicap.

**Concepts clés** :
- WCAG 2.1 (Web Content Accessibility Guidelines)
- ARIA (Accessible Rich Internet Applications)
- HTML sémantique
- Navigation au clavier
- Contraste des couleurs

**Métriques de succès** :
- Score Lighthouse Accessibility : > 95/100
- Conformité WCAG : Niveau AA minimum (AAA recommandé)
- Contraste minimum : 4.5:1 (texte normal), 7:1 (AAA)
- Couverture clavier : 100% des fonctionnalités

---

## 🗺️ Carte mentale du chapitre

```
Accessibilité & UX
│
├── 🎯 WCAG 2.1 (4 principes POUR)
│   ├── Perceptible
│   │   ├── Textes alternatifs
│   │   ├── Sous-titres vidéo
│   │   ├── Contraste couleurs
│   │   └── Adaptabilité contenu
│   │
│   ├── Utilisable
│   │   ├── Navigation clavier
│   │   ├── Temps suffisant
│   │   ├── Pas de crises convulsives
│   │   └── Navigable
│   │
│   ├── Compréhensible
│   │   ├── Lisible
│   │   ├── Prévisible
│   │   └── Aide à la saisie
│   │
│   └── Robuste
│       ├── Compatible technologies
│       └── HTML sémantique
│
├── ♿ ARIA
│   ├── Roles (button, dialog, tab...)
│   ├── États (aria-expanded, aria-checked...)
│   ├── Propriétés (aria-label, aria-describedby...)
│   └── Live regions (aria-live)
│
├── ⌨️ Navigation clavier
│   ├── Tab / Shift+Tab
│   ├── Enter / Space
│   ├── Escape
│   ├── Flèches directionnelles
│   └── Focus management
│
├── 🎨 Contraste & Couleurs
│   ├── Ratios WCAG (4.5:1, 7:1)
│   ├── Calculs de luminance
│   ├── Dark mode accessible
│   └── Pas de couleur seule
│
├── 🔧 Radix UI
│   ├── Accessibilité native
│   ├── Focus trap
│   ├── ARIA automatique
│   └── Keyboard navigation
│
└── ✅ Tests
    ├── Lighthouse
    ├── Axe DevTools
    ├── Lecteurs d'écran
    ├── Navigation clavier
    └── Tests utilisateurs
```

---

## 📈 Statistiques : L'importance de l'accessibilité

### Tableau 1 : Population mondiale concernée

| Type de handicap | Pourcentage | Population mondiale | En France |
|------------------|-------------|---------------------|-----------|
| **Visuel** | 2.2% | ~176 millions | ~1.7 million |
| **Auditif** | 5% | ~466 millions | ~4 million |
| **Moteur** | 1.5% | ~120 millions | ~1.2 million |
| **Cognitif** | 2% | ~160 millions | ~1.5 million |
| **Temporaire** | 10% | ~800 millions | ~6.7 million |
| **TOTAL** | **15%** | **~1.3 milliard** | **~10 million** |

**Note** : Les handicaps temporaires incluent bras cassé, fatigue oculaire, environnement bruyant, etc.

### Tableau 2 : Impact business de l'accessibilité

| Métrique | Sans accessibilité | Avec accessibilité | Amélioration |
|----------|-------------------|-------------------|--------------|
| **Audience potentielle** | 85% | 100% | **+15%** 🎯 |
| **Taux de conversion** | 2.5% | 3.2% | **+28%** 💰 |
| **Bounce rate** | 45% | 32% | **-29%** ✅ |
| **Session duration** | 2m 30s | 3m 45s | **+50%** ⏱️ |
| **SEO ranking** | Position moyenne 15 | Position moyenne 8 | **+47%** 📈 |
| **Risques juridiques** | Élevé (amendes) | Faible | **-95%** ⚖️ |
| **Satisfaction utilisateur** | 72% | 89% | **+24%** 😊 |

---

## 🧮 Calcul 1 : Ratio de contraste WCAG

### Formule complète du contraste

```
Ratio de contraste = (L1 + 0.05) / (L2 + 0.05)

Où L = Luminance relative calculée avec :

L = 0.2126 × R_linear + 0.7152 × G_linear + 0.0722 × B_linear

Pour linéariser RGB :
Si RGB_sRGB ≤ 0.03928 :
    RGB_linear = RGB_sRGB / 12.92
Sinon :
    RGB_linear = ((RGB_sRGB + 0.055) / 1.055)^2.4

Normes WCAG 2.1 :
- AA (Normal) : ≥ 4.5:1
- AA (Large) : ≥ 3:1
- AAA (Normal) : ≥ 7:1
- AAA (Large) : ≥ 4.5:1
```

### Exemple concret : Texte bleu sur fond blanc

**Couleur 1** : Blanc `#FFFFFF` → RGB(255, 255, 255)
**Couleur 2** : Bleu `#3B82F6` → RGB(59, 130, 246)

**Étape 1 : Normaliser RGB (0-1)**
```
Blanc :
R = 255/255 = 1.0
G = 255/255 = 1.0
B = 255/255 = 1.0

Bleu :
R = 59/255 = 0.2314
G = 130/255 = 0.5098
B = 246/255 = 0.9647
```

**Étape 2 : Linéariser**
```
Blanc (tous > 0.03928) :
R_lin = ((1.0 + 0.055) / 1.055)^2.4 = 1.0
G_lin = 1.0
B_lin = 1.0

Bleu :
R_lin = ((0.2314 + 0.055) / 1.055)^2.4 = 0.0457
G_lin = ((0.5098 + 0.055) / 1.055)^2.4 = 0.2323
B_lin = ((0.9647 + 0.055) / 1.055)^2.4 = 0.9241
```

**Étape 3 : Calculer les luminances**
```
L_blanc = 0.2126×1.0 + 0.7152×1.0 + 0.0722×1.0
        = 1.0

L_bleu = 0.2126×0.0457 + 0.7152×0.2323 + 0.0722×0.9241
       = 0.0097 + 0.1662 + 0.0667
       = 0.2426
```

**Étape 4 : Ratio de contraste**
```
Contraste = (1.0 + 0.05) / (0.2426 + 0.05)
          = 1.05 / 0.2926
          = 3.59:1
```

**Résultat** : ❌ **3.59:1** → Échec WCAG AA (< 4.5:1)

**Solution** : Utiliser un bleu plus foncé `#1E40AF` → Contraste **8.2:1** ✅ (AAA)

---

## 🧮 Calcul 2 : Taille minimale des zones de touch

### Standards ergonomiques

```
Taille minimale recommandée :

WCAG 2.1 (Niveau AAA) : 44×44 pixels
Apple iOS : 44×44 points
Material Design : 48×48 dp
Microsoft : 44×44 pixels

Espacement minimum entre éléments : 8 pixels
```

### Calcul de la zone effective

**Scénario** : Bouton de 40×40px avec padding de 8px

```
Zone effective = (Largeur + 2×Padding) × (Hauteur + 2×Padding)
               = (40 + 2×8) × (40 + 2×8)
               = 56 × 56
               = 3,136 px²

Zone minimale WCAG AAA = 44 × 44 = 1,936 px²

3,136 > 1,936 ✅ Conforme WCAG AAA
```

### Impact sur le taux d'erreur

```
Formule de Fitts (temps pour atteindre une cible) :

T = a + b × log₂(D/W + 1)

Où :
- T = Temps de mouvement
- D = Distance à la cible
- W = Largeur de la cible
- a, b = Constantes empiriques

Exemple :
Cible 1 : 32×32px → T = 150ms (erreur 15%)
Cible 2 : 44×44px → T = 120ms (erreur 5%)
Cible 3 : 56×56px → T = 100ms (erreur 2%)

Amélioration de 32px à 56px :
- Temps : -33%
- Erreurs : -87%
```

---

## 🧮 Calcul 3 : Impact de l'ordre de tabulation

### Complexité de navigation

```
Complexité = Nombre d'appuis Tab pour atteindre l'élément cible

Ordre optimal : Complexité minimale
Ordre chaotique : Complexité maximale
```

### Exemple concret : Formulaire de connexion

**Ordre logique (optimal)** :
```
1. Email input          (1 Tab)
2. Password input       (2 Tabs)
3. "Mot de passe oublié" (3 Tabs)
4. Bouton "Se connecter" (4 Tabs)

Moyenne : (1+2+3+4)/4 = 2.5 Tabs
```

**Ordre chaotique (mauvais)** :
```
1. Footer link          (1 Tab)
2. Bouton "Se connecter" (2 Tabs)
3. Logo header          (3 Tab)
4. Email input          (4 Tabs)
5. Nav menu            (5 Tabs)
6. Password input       (6 Tabs)

Pour remplir le formulaire : 4+6 = 10 Tabs (vs 2 optimal)
Perte de temps : 400%
```

**Impact utilisateur** :
```
Formulaire avec ordre optimal :
- Temps moyen : 15 secondes
- Taux d'abandon : 5%

Formulaire avec ordre chaotique :
- Temps moyen : 45 secondes (+200%)
- Taux d'abandon : 35% (+600%)
```

---

## 🧮 Calcul 4 : ROI de l'accessibilité

### Investissement initial

```
Temps de mise en conformité WCAG AA :
- Audit initial : 8h
- Corrections HTML sémantique : 20h
- Ajout ARIA : 15h
- Tests accessibilité : 10h
- Documentation : 5h
Total : 58h × 50€/h = 2,900€
```

### Gains financiers annuels

**Audience élargie** :
```
Traffic actuel : 100,000 visiteurs/mois
Audience exclue (15%) : 15,000 visiteurs/mois

Après accessibilité :
Nouveaux visiteurs : +15,000/mois = +180,000/an

Taux de conversion : 3%
Panier moyen : 80€

Revenus additionnels = 180,000 × 3% × 80€
                     = 432,000€/an
```

**Éviter les amendes** :
```
Amendes potentielles (non-conformité) :
- France : jusqu'à 25,000€
- USA (ADA) : jusqu'à 75,000€
- UE : jusqu'à 4% du CA

Risque moyen évité : ~50,000€
```

**Amélioration SEO** :
```
Amélioration ranking : Position 15 → Position 8
Trafic organique : +35%

Traffic actuel : 100,000/mois
Nouveau traffic : 135,000/mois (+35,000)

Valeur SEO : 35,000 × 12 × 3% × 80€ = 100,800€/an
```

**Total des gains** :
```
Gains annuels :
+ Audience élargie : 432,000€
+ SEO amélioré : 100,800€
+ Amendes évitées : 50,000€
Total : 582,800€/an

ROI = (582,800 - 2,900) / 2,900 × 100
    = 19,996%

Point d'équilibre : 2,900€ / 582,800€ = 0.005 an ≈ 2 jours
```

**Interprétation** : L'accessibilité est **rentabilisée en 2 jours**, puis génère **582,800€ par an** ! 🚀

---

## 🧮 Calcul 5 : Temps de navigation au clavier

### Benchmark de productivité

**Scénario** : Remplir un formulaire de 10 champs

**Utilisateur souris** :
```
Temps par champ :
- Déplacer souris : 0.8s
- Cliquer : 0.2s
- Taper : 3s
Total par champ : 4s

Total 10 champs : 10 × 4s = 40s
```

**Utilisateur clavier (navigation optimisée)** :
```
Temps par champ :
- Tab : 0.1s
- Taper : 3s
Total par champ : 3.1s

Total 10 champs : 10 × 3.1s = 31s

Gain de temps : (40 - 31) / 40 × 100 = 22.5%
```

**Utilisateur clavier (navigation NON optimisée)** :
```
Temps par champ :
- Tabs multiples : 2s (ordre chaotique)
- Taper : 3s
Total par champ : 5s

Total 10 champs : 10 × 5s = 50s

Perte de temps : (50 - 31) / 31 × 100 = +61%
```

**Conclusion** : Une navigation clavier bien conçue est **22.5% plus rapide** que la souris pour les power users !

---

## 📐 Diagramme : Architecture de l'accessibilité

```
┌─────────────────────────────────────────────────────────────┐
│                      USER (100%)                             │
│                                                              │
│  ├── 85% Sans handicap                                      │
│  │   ├── Souris + Clavier                                   │
│  │   └── Écran standard                                     │
│  │                                                           │
│  └── 15% En situation de handicap                           │
│      ├── 2.2% Visuel                                        │
│      ├── 5% Auditif                                         │
│      ├── 1.5% Moteur                                        │
│      ├── 2% Cognitif                                        │
│      └── 10% Temporaire                                     │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
          ┌──────────────────────────────┐
          │   TECHNOLOGIES D'ASSISTANCE  │
          │                              │
          │  ├── Lecteurs d'écran        │
          │  │   (NVDA, JAWS, VoiceOver) │
          │  │                            │
          │  ├── Navigation clavier      │
          │  │   (Tab, Enter, Escape)    │
          │  │                            │
          │  ├── Agrandisseurs d'écran   │
          │  │   (ZoomText, Magnifier)   │
          │  │                            │
          │  ├── Synthèse vocale         │
          │  │                            │
          │  └── Contrôle vocal          │
          │      (Dragon, Voice Control) │
          └───────────────┬──────────────┘
                          │
                          ▼
          ┌───────────────────────────────┐
          │      INTERFACE HTML           │
          │                               │
          │  ┌─────────────────────────┐  │
          │  │  HTML Sémantique        │  │
          │  │  <nav>, <main>, <button>│  │
          │  └─────────────────────────┘  │
          │                               │
          │  ┌─────────────────────────┐  │
          │  │  ARIA Attributes        │  │
          │  │  role, aria-label, etc. │  │
          │  └─────────────────────────┘  │
          │                               │
          │  ┌─────────────────────────┐  │
          │  │  Focus Management       │  │
          │  │  tabIndex, :focus       │  │
          │  └─────────────────────────┘  │
          │                               │
          │  ┌─────────────────────────┐  │
          │  │  Contraste & Couleurs   │  │
          │  │  WCAG ratios            │  │
          │  └─────────────────────────┘  │
          └───────────────┬───────────────┘
                          │
                          ▼
          ┌───────────────────────────────┐
          │      REACT COMPONENTS         │
          │                               │
          │  ┌─────────────────────────┐  │
          │  │  Radix UI Primitives    │  │
          │  │  (Accessibilité native) │  │
          │  └─────────────────────────┘  │
          │                               │
          │  ┌─────────────────────────┐  │
          │  │  Custom Hooks           │  │
          │  │  useKeyboard, useFocus  │  │
          │  └─────────────────────────┘  │
          └───────────────────────────────┘
```

---

## 📊 Classification des niveaux WCAG

### Tableau de conformité WCAG 2.1

| Niveau | Critères | Difficulté | Couverture | Usage recommandé |
|--------|----------|------------|------------|------------------|
| **A** | 30 critères | ★☆☆☆☆ | 60% handicaps | Minimum légal |
| **AA** | 50 critères | ★★★☆☆ | 85% handicaps | **Standard recommandé** ⭐ |
| **AAA** | 78 critères | ★★★★★ | 95% handicaps | Excellence, sites gouvernementaux |

### Détails par principe

| Principe | Niveau A | Niveau AA | Niveau AAA | Total |
|----------|----------|-----------|------------|-------|
| **Perceptible** | 9 critères | +7 critères | +7 critères | 23 |
| **Utilisable** | 11 critères | +5 critères | +8 critères | 24 |
| **Compréhensible** | 7 critères | +3 critères | +6 critères | 16 |
| **Robuste** | 3 critères | +0 critères | +12 critères | 15 |
| **TOTAL** | **30** | **+20** | **+28** | **78** |

---

## 📊 Matrice de décision : HTML vs ARIA

```
Complexité du composant
     ↑
     │
Très │                      ┌──────────────┐
     │                      │  Custom ARIA │
     │                      │  (Dialog,    │
     │                      │   Tabs,      │
 😰  │          ┌───────────┤   Dropdown)  │
     │          │           └──────────────┘
     │          │    Radix UI Primitives
     │          │    (ARIA automatique) ⭐
     │          │   
 😐  │  ┌───────┴──────────────────────────┐
     │  │                                  │
     │  │   HTML Sémantique enrichi        │
     │  │   (<button>, <nav> + ARIA)       │
 😊  │  └──────────────────────────────────┘
     │  
     │  HTML Sémantique pur
     │  (<button>, <a>, <input>)
     │
     └──────────┬──────────┬──────────┬──────────→
             Simple    Moyen    Complexe   Support
                                          natif

✅ Préférer TOUJOURS l'HTML sémantique quand possible
⭐ Utiliser Radix pour les composants complexes
⚠️ N'utiliser ARIA custom qu'en dernier recours
```

---

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

## 📚 Résumé du chapitre

Dans ce chapitre, nous avons exploré l'accessibilité :

✅ **Bases** : WCAG, ARIA, HTML sémantique
✅ **Radix** : Composants accessibles par défaut
✅ **Contraste** : Couleurs lisibles pour tous
✅ **Préférences** : Reduced motion, taille de texte
✅ **Tests** : Automatiques et manuels

---

## 📊 Récapitulatif visuel : Cycle de vie de l'accessibilité

```
┌─────────────────────────────────────────────────────────────┐
│              PROCESSUS D'ACCESSIBILITÉ COMPLET               │
└─────────────────────────────────────────────────────────────┘

1️⃣  AUDIT INITIAL (8h)
    ├── Lighthouse audit (score : 45/100)
    ├── Axe DevTools scan (127 erreurs)
    ├── Test navigation clavier (❌ 35% fonctionnel)
    ├── Test lecteur d'écran (❌ 50% annoncé)
    └── Test contraste (❌ 23 éléments non conformes)

              ↓

2️⃣  CORRECTIONS HTML (20h)
    ├── Remplacer <div> par éléments sémantiques
    │   • <div> → <button>, <nav>, <main>
    ├── Ajouter labels à tous les inputs
    ├── Structurer headings (h1 → h6)
    ├── Ajouter textes alternatifs (alt)
    └── Ordre tabulation logique

              ↓

3️⃣  AJOUT ARIA (15h)
    ├── Roles pour composants custom
    ├── aria-label pour boutons icônes
    ├── aria-expanded pour accordions
    ├── aria-live pour notifications
    └── aria-describedby pour contexte

              ↓

4️⃣  CONTRASTE & COULEURS (5h)
    ├── Vérifier tous les ratios (> 4.5:1)
    ├── Ajuster couleurs non conformes
    ├── Tester mode sombre
    └── Ajouter indicateurs non-couleur

              ↓

5️⃣  NAVIGATION CLAVIER (10h)
    ├── Focus visible sur tous les éléments
    ├── Skip links (sauter la navigation)
    ├── Focus trap dans modals
    ├── Shortcuts clavier (Esc, Enter)
    └── Ordre Tab optimisé

              ↓

6️⃣  TESTS FINAUX (10h)
    ├── Lighthouse : 98/100 ✅
    ├── Axe DevTools : 0 erreur ✅
    ├── Navigation clavier : 100% ✅
    ├── NVDA/VoiceOver : 100% annoncé ✅
    └── Tests utilisateurs réels

              ↓

7️⃣  RÉSULTAT
    ├── Conformité WCAG AA ✅
    ├── +15% d'audience (1.3 milliard)
    ├── +28% conversion
    ├── ROI : 19,996% (2 jours pour rentabilité)
    └── 0 risque juridique
```

---

## 📊 Métriques clés du chapitre

| Métrique | Avant | Après | Amélioration |
|----------|-------|-------|--------------|
| **Score Lighthouse Accessibility** | 45/100 | 98/100 | **+118%** 🎯 |
| **Erreurs Axe DevTools** | 127 erreurs | 0 erreur | **-100%** ✅ |
| **Contraste minimum** | 2.8:1 ❌ | 8.2:1 ✅ | **+193%** |
| **Navigation clavier** | 35% fonctionnel | 100% | **+186%** ⌨️ |
| **Audience accessible** | 85% | 100% | **+15%** (+1.3 milliard) 🌍 |
| **Taux de conversion** | 2.5% | 3.2% | **+28%** 💰 |
| **Bounce rate** | 45% | 32% | **-29%** ✅ |
| **Temps de session** | 2m 30s | 3m 45s | **+50%** ⏱️ |
| **ROI accessibilité** | - | 19,996% | **Rentabilisé en 2 jours** 🚀 |
| **Risques juridiques** | Élevé | Faible | **-95%** ⚖️ |

**Score global de qualité** : **98/100** 🏆

---

## 📊 Checklist d'accessibilité complète

### 🎯 Niveau 1 : Fondamentaux (WCAG A) - Obligatoire

#### HTML & Structure
- [ ] Utilise éléments HTML sémantiques (`<nav>`, `<main>`, `<button>`)
- [ ] Tous les `<img>` ont un attribut `alt` (ou `alt=""` si décoratif)
- [ ] Structure de headings hiérarchique (h1 → h2 → h3)
- [ ] Labels associés à tous les inputs (`<label for="id">`)
- [ ] Langue de la page définie (`<html lang="fr">`)

#### Navigation clavier
- [ ] Tous les éléments interactifs accessibles au Tab
- [ ] Ordre de tabulation logique (tabIndex si nécessaire)
- [ ] Pas de keyboard trap (sauf modals intentionnels)
- [ ] Liens skip-to-content en haut de page

#### Couleurs & Contraste
- [ ] Contraste ≥ 4.5:1 pour texte normal
- [ ] Contraste ≥ 3:1 pour texte large (18pt+)
- [ ] Information non véhiculée uniquement par couleur
- [ ] Indicateurs visuels pour états (hover, focus, actif)

---

### 🎯 Niveau 2 : Standard (WCAG AA) - Recommandé ⭐

#### ARIA
- [ ] `aria-label` pour boutons sans texte visible
- [ ] `aria-expanded` pour éléments repliables
- [ ] `aria-live` pour notifications dynamiques
- [ ] `aria-describedby` pour contexte additionnel
- [ ] Roles ARIA seulement si HTML sémantique impossible

#### Formulaires
- [ ] Messages d'erreur explicites et accessibles
- [ ] Validation accessible (annoncée aux lecteurs)
- [ ] Champs requis indiqués (`required`, `aria-required`)
- [ ] Instructions claires avant les champs
- [ ] Autocomplete approprié (`autocomplete="email"`)

#### Interactions
- [ ] Focus visible sur tous les éléments (`:focus-visible`)
- [ ] Modals piègent le focus (focus trap)
- [ ] Modals se ferment avec Escape
- [ ] Focus retourne au trigger après fermeture
- [ ] Animations respectent `prefers-reduced-motion`

#### Zones de touch (Mobile)
- [ ] Taille minimum 44×44px pour tous les boutons
- [ ] Espacement ≥ 8px entre éléments cliquables
- [ ] Pas de hover-only interactions
- [ ] Gestes alternatifs fournis (swipe optionnel)

---

### 🎯 Niveau 3 : Excellence (WCAG AAA) - Idéal

#### Contraste renforcé
- [ ] Contraste ≥ 7:1 pour texte normal
- [ ] Contraste ≥ 4.5:1 pour texte large
- [ ] Contraste ≥ 3:1 pour éléments UI et graphiques

#### Texte & Typo
- [ ] Taille de texte en `rem` (adaptatif)
- [ ] Line-height ≥ 1.5 pour paragraphes
- [ ] Espacement lettres ajustable (CSS `letter-spacing`)
- [ ] Pas de justification (`text-align: justify`)
- [ ] Largeur ligne ≤ 80 caractères

#### Temps & Timing
- [ ] Pas de limite de temps (ou extensible)
- [ ] Pas d'auto-play vidéo/audio
- [ ] Animations pausables
- [ ] Défilement automatique désactivable
- [ ] Notifications restent jusqu'à action utilisateur

#### Navigation avancée
- [ ] Breadcrumbs pour orientation
- [ ] Sitemap accessible
- [ ] Recherche sur site
- [ ] Raccourcis clavier documentés
- [ ] First-letter navigation dans menus

---

## 📊 Comparaison : Avant vs Après accessibilité

### Impact utilisateur

| Utilisateur | Avant | Après | Transformation |
|-------------|-------|-------|----------------|
| **Utilisateur voyant** | ✅ Utilisable | ✅ Expérience améliorée | +20% satisfaction |
| **Malvoyant** | ⚠️ Difficile | ✅ Pleinement utilisable | +300% utilisabilité |
| **Aveugle (lecteur écran)** | ❌ Inutilisable | ✅ 100% fonctionnel | **De 0% à 100%** 🎯 |
| **Handicap moteur** | ⚠️ Souris requise | ✅ Navigation clavier | +250% autonomie |
| **Daltonien** | ⚠️ Perte d'info | ✅ Accessible | +100% compréhension |
| **Power user** | ⚠️ Souris lente | ✅ Clavier rapide | +22.5% productivité |

---

## 📊 Diagramme : Flux de test accessibilité

```
┌─────────────────────────────────────────────────────────────┐
│                  DÉBUT DES TESTS                             │
└────────────────────────┬────────────────────────────────────┘
                         │
          ┌──────────────┼──────────────┐
          ▼              ▼              ▼
   ┌───────────┐  ┌───────────┐  ┌───────────┐
   │ Lighthouse│  │Axe DevTools│  │   W3C     │
   │   Audit   │  │   Scan    │  │ Validator │
   └─────┬─────┘  └─────┬─────┘  └─────┬─────┘
         │              │              │
         └──────────────┼──────────────┘
                        ▼
          ┌─────────────────────────┐
          │  Tests automatisés OK ? │
          └──────────┬──────────────┘
                     │
          ┌──────────┼──────────┐
          │ NON      │ OUI      │
          ▼          ▼          │
   ┌──────────┐  ┌──────────┐  │
   │ Corriger │  │ Passer   │  │
   │ erreurs  │  │ aux tests│  │
   └────┬─────┘  │ manuels  │  │
        │        └────┬─────┘  │
        └─────────────┘        │
                               ▼
          ┌─────────────────────────────┐
          │     TESTS MANUELS           │
          │                             │
          │  ┌──────────────────────┐   │
          │  │ 1. Navigation clavier│   │
          │  │    (Tab, Enter, Esc) │   │
          │  └──────────────────────┘   │
          │                             │
          │  ┌──────────────────────┐   │
          │  │ 2. Lecteur d'écran   │   │
          │  │    (NVDA/VoiceOver)  │   │
          │  └──────────────────────┘   │
          │                             │
          │  ┌──────────────────────┐   │
          │  │ 3. Zoom (100-200%)   │   │
          │  └──────────────────────┘   │
          │                             │
          │  ┌──────────────────────┐   │
          │  │ 4. Reduced motion    │   │
          │  └──────────────────────┘   │
          │                             │
          │  ┌──────────────────────┐   │
          │  │ 5. Contraste visuel  │   │
          │  └──────────────────────┘   │
          └─────────────┬───────────────┘
                        ▼
          ┌─────────────────────────┐
          │  Tous les tests OK ?    │
          └──────────┬──────────────┘
                     │
          ┌──────────┼──────────┐
          │ NON      │ OUI      │
          ▼          ▼          │
   ┌──────────┐  ┌──────────┐  │
   │ Corriger │  │ Tests    │  │
   │ problèmes│  │utilisateur│  │
   └────┬─────┘  │  réels   │  │
        │        └────┬─────┘  │
        └─────────────┘        │
                               ▼
          ┌─────────────────────────┐
          │   CONFORMITÉ WCAG ✅    │
          │   Score : 98/100        │
          └─────────────────────────┘
```

---

### Points clés à retenir

💡 **15% de la population** = **1.3 milliard de personnes** concernées par le handicap → Audience massive

💡 **ROI de 19,996%** : L'accessibilité est rentabilisée en **2 jours**, puis génère **582,800€/an**

💡 **HTML sémantique > ARIA** : `<button>` est meilleur que `<div role="button">` (natif, robuste, simple)

💡 **Radix UI** : Accessibilité native (focus trap, ARIA, keyboard) → Gain de 15h par composant

💡 **Contraste 8.2:1** (AAA) vs 3.59:1 ❌ : Utilisez un bleu foncé `#1E40AF` au lieu de `#3B82F6`

💡 **Navigation clavier 22.5% plus rapide** que la souris pour les power users → UX améliorée pour tous

💡 **44×44px minimum** pour zones de touch → **-87% d'erreurs** vs 32×32px

💡 **Tests = Lighthouse + Axe + Lecteurs d'écran** : Les outils automatiques ne couvrent que 30-40% des problèmes

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

