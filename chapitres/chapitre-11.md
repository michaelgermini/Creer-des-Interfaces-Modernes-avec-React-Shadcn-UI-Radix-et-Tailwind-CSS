# Chapitre 11 : Animation et interactions

## Introduction

Les animations et micro-interactions rendent une interface vivante et agréable à utiliser. Dans ce chapitre, vous apprendrez à créer des animations performantes et significatives.

---

## Introduction à Framer Motion

### Pourquoi Framer Motion ?

**Framer Motion** est la bibliothèque d'animation la plus populaire pour React :
- ✅ API intuitive et déclarative
- ✅ Performance optimale (GPU)
- ✅ Animations complexes simplifiées
- ✅ Gestion des gestes (drag, swipe, etc.)
- ✅ Layout animations automatiques
- ✅ TypeScript natif

### Installation

```bash
npm install framer-motion
```

### Premier exemple

```tsx
import { motion } from 'framer-motion'

function FadeInComponent() {
  return (
    <motion.div
      initial={{ opacity: 0 }}
      animate={{ opacity: 1 }}
      transition={{ duration: 0.5 }}
    >
      Je fais un fade in !
    </motion.div>
  )
}
```

### Propriétés principales

```tsx
<motion.div
  // État initial
  initial={{ opacity: 0, y: 20 }}
  
  // État final
  animate={{ opacity: 1, y: 0 }}
  
  // État au survol
  whileHover={{ scale: 1.05 }}
  
  // État lors du clic
  whileTap={{ scale: 0.95 }}
  
  // État au focus
  whileFocus={{ outline: '2px solid blue' }}
  
  // État en cours de drag
  whileDrag={{ scale: 1.1 }}
  
  // Configuration de la transition
  transition={{ 
    duration: 0.3, 
    ease: 'easeOut' 
  }}
>
  Contenu animé
</motion.div>
```

---

## Micro-interactions et transitions douces

### Button avec micro-interaction

```tsx
import { motion } from 'framer-motion'

function AnimatedButton({ children, onClick }: { 
  children: React.ReactNode
  onClick?: () => void 
}) {
  return (
    <motion.button
      className="px-6 py-3 bg-primary text-white rounded-lg"
      onClick={onClick}
      
      // Hover : légère élévation
      whileHover={{ 
        scale: 1.02,
        boxShadow: '0 8px 16px rgba(0, 0, 0, 0.1)'
      }}
      
      // Tap : légère compression
      whileTap={{ scale: 0.98 }}
      
      // Transition douce
      transition={{ 
        type: 'spring', 
        stiffness: 400, 
        damping: 17 
      }}
    >
      {children}
    </motion.button>
  )
}
```

### Card avec effet de survol

```tsx
import { motion } from 'framer-motion'

function ProductCard({ product }: { product: Product }) {
  return (
    <motion.div
      className="bg-card rounded-lg overflow-hidden border"
      
      // Animation au survol
      whileHover={{ y: -8 }}
      
      // Transition douce
      transition={{ duration: 0.2 }}
    >
      <motion.img
        src={product.image}
        alt={product.name}
        
        // Image zoom au survol du parent
        whileHover={{ scale: 1.1 }}
        transition={{ duration: 0.3 }}
      />
      
      <div className="p-4">
        <h3 className="font-semibold">{product.name}</h3>
        <p className="text-muted-foreground">{product.price} €</p>
      </div>
    </motion.div>
  )
}
```

### Input avec focus animation

```tsx
import { motion } from 'framer-motion'
import { useState } from 'react'

function AnimatedInput({ label, ...props }: { label: string } & React.InputHTMLAttributes<HTMLInputElement>) {
  const [isFocused, setIsFocused] = useState(false)

  return (
    <div className="relative">
      <motion.label
        className="absolute left-3 text-sm"
        
        // Position et taille selon le focus
        animate={{
          top: isFocused || props.value ? '0.5rem' : '1rem',
          fontSize: isFocused || props.value ? '0.75rem' : '1rem',
          color: isFocused ? 'hsl(var(--primary))' : 'hsl(var(--muted-foreground))',
        }}
        
        transition={{ duration: 0.2 }}
      >
        {label}
      </motion.label>
      
      <motion.input
        className="w-full px-3 pt-6 pb-2 border rounded-lg"
        
        onFocus={() => setIsFocused(true)}
        onBlur={() => setIsFocused(false)}
        
        // Animation de la bordure
        animate={{
          borderColor: isFocused 
            ? 'hsl(var(--primary))' 
            : 'hsl(var(--border))',
        }}
        
        {...props}
      />
    </div>
  )
}
```

### Toggle/Switch animé

```tsx
import { motion } from 'framer-motion'
import { useState } from 'react'

function AnimatedSwitch({ 
  checked: controlledChecked, 
  onChange 
}: { 
  checked?: boolean
  onChange?: (checked: boolean) => void 
}) {
  const [internalChecked, setInternalChecked] = useState(false)
  const checked = controlledChecked ?? internalChecked

  const handleToggle = () => {
    const newValue = !checked
    setInternalChecked(newValue)
    onChange?.(newValue)
  }

  return (
    <button
      onClick={handleToggle}
      className={`
        relative w-14 h-8 rounded-full transition-colors
        ${checked ? 'bg-primary' : 'bg-gray-300'}
      `}
    >
      <motion.div
        className="absolute top-1 w-6 h-6 bg-white rounded-full shadow"
        
        // Position selon l'état
        animate={{
          x: checked ? 28 : 4,
        }}
        
        // Transition spring
        transition={{
          type: 'spring',
          stiffness: 500,
          damping: 30,
        }}
      />
    </button>
  )
}
```

---

## Gérer la performance des animations

### Propriétés performantes

Les propriétés qui n'affectent pas le layout sont plus performantes :

```tsx
// ✅ Très performant : GPU accelerated
transform: 'translateX(100px)'
transform: 'scale(1.5)'
transform: 'rotate(45deg)'
opacity: 0.5

// ⚠️ Moins performant : recalcul du layout
width: '200px'
height: '100px'
top: '50px'
left: '100px'
```

### Utiliser `transform` au lieu de position

```tsx
// ❌ Mauvais : modifie le layout
<motion.div
  animate={{ left: 100, top: 100 }}
/>

// ✅ Bon : utilise transform
<motion.div
  animate={{ x: 100, y: 100 }}
/>
```

### Layout animations

Framer Motion gère automatiquement les changements de layout :

```tsx
import { motion } from 'framer-motion'

function AnimatedList({ items }: { items: string[] }) {
  return (
    <div>
      {items.map((item) => (
        <motion.div
          key={item}
          
          // Active les layout animations
          layout
          
          // Animations lors de l'apparition/disparition
          initial={{ opacity: 0, scale: 0.8 }}
          animate={{ opacity: 1, scale: 1 }}
          exit={{ opacity: 0, scale: 0.8 }}
          
          // Transition
          transition={{ duration: 0.2 }}
        >
          {item}
        </motion.div>
      ))}
    </div>
  )
}
```

### AnimatePresence pour les montages/démontages

```tsx
import { motion, AnimatePresence } from 'framer-motion'

function ConditionalComponent({ show }: { show: boolean }) {
  return (
    <AnimatePresence>
      {show && (
        <motion.div
          initial={{ opacity: 0, y: -20 }}
          animate={{ opacity: 1, y: 0 }}
          exit={{ opacity: 0, y: 20 }}
          transition={{ duration: 0.2 }}
        >
          Je peux être animé à la disparition !
        </motion.div>
      )}
    </AnimatePresence>
  )
}
```

### Lazy motion (code splitting)

Pour réduire la taille du bundle :

```tsx
import { LazyMotion, domAnimation, m } from 'framer-motion'

function App() {
  return (
    <LazyMotion features={domAnimation}>
      {/* Utiliser 'm' au lieu de 'motion' */}
      <m.div
        animate={{ opacity: 1 }}
        initial={{ opacity: 0 }}
      >
        Contenu
      </m.div>
    </LazyMotion>
  )
}
```

---

## Animation conditionnelle des composants UI

### Variants system

Les variants permettent de définir des animations réutilisables :

```tsx
import { motion } from 'framer-motion'

// Définir les variants
const cardVariants = {
  hidden: { 
    opacity: 0, 
    y: 20 
  },
  visible: { 
    opacity: 1, 
    y: 0,
    transition: {
      duration: 0.5,
    }
  },
  hover: {
    scale: 1.05,
    transition: {
      duration: 0.2,
    }
  },
}

function Card() {
  return (
    <motion.div
      variants={cardVariants}
      initial="hidden"
      animate="visible"
      whileHover="hover"
    >
      Contenu
    </motion.div>
  )
}
```

### Stagger children (animation en cascade)

```tsx
const containerVariants = {
  hidden: { opacity: 0 },
  visible: {
    opacity: 1,
    transition: {
      // Délai entre chaque enfant
      staggerChildren: 0.1,
    },
  },
}

const itemVariants = {
  hidden: { opacity: 0, y: 20 },
  visible: { 
    opacity: 1, 
    y: 0,
    transition: {
      duration: 0.5,
    }
  },
}

function StaggeredList({ items }: { items: string[] }) {
  return (
    <motion.ul
      variants={containerVariants}
      initial="hidden"
      animate="visible"
    >
      {items.map((item) => (
        <motion.li
          key={item}
          variants={itemVariants}
        >
          {item}
        </motion.li>
      ))}
    </motion.ul>
  )
}
```

### Modal avec animation

```tsx
import { motion, AnimatePresence } from 'framer-motion'

const backdropVariants = {
  hidden: { opacity: 0 },
  visible: { opacity: 1 },
}

const modalVariants = {
  hidden: { 
    opacity: 0, 
    scale: 0.8,
    y: -50,
  },
  visible: { 
    opacity: 1, 
    scale: 1,
    y: 0,
    transition: {
      type: 'spring',
      damping: 25,
      stiffness: 300,
    }
  },
  exit: {
    opacity: 0,
    scale: 0.8,
    y: 50,
    transition: {
      duration: 0.2,
    }
  }
}

function AnimatedModal({ 
  isOpen, 
  onClose, 
  children 
}: { 
  isOpen: boolean
  onClose: () => void
  children: React.ReactNode 
}) {
  return (
    <AnimatePresence>
      {isOpen && (
        <>
          {/* Backdrop */}
          <motion.div
            className="fixed inset-0 bg-black/50 z-50"
            variants={backdropVariants}
            initial="hidden"
            animate="visible"
            exit="hidden"
            onClick={onClose}
          />

          {/* Modal */}
          <motion.div
            className="fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 bg-white rounded-lg p-6 z-50 max-w-md w-full"
            variants={modalVariants}
            initial="hidden"
            animate="visible"
            exit="exit"
          >
            {children}
          </motion.div>
        </>
      )}
    </AnimatePresence>
  )
}
```

### Skeleton loading avec animation

```tsx
import { motion } from 'framer-motion'

function SkeletonCard() {
  return (
    <div className="bg-card rounded-lg p-4 space-y-4">
      {/* Avatar skeleton */}
      <motion.div
        className="w-12 h-12 bg-muted rounded-full"
        animate={{
          opacity: [0.5, 1, 0.5],
        }}
        transition={{
          duration: 1.5,
          repeat: Infinity,
          ease: 'easeInOut',
        }}
      />

      {/* Text skeletons */}
      <div className="space-y-2">
        <motion.div
          className="h-4 bg-muted rounded w-3/4"
          animate={{
            opacity: [0.5, 1, 0.5],
          }}
          transition={{
            duration: 1.5,
            repeat: Infinity,
            ease: 'easeInOut',
            delay: 0.1,
          }}
        />
        <motion.div
          className="h-4 bg-muted rounded w-1/2"
          animate={{
            opacity: [0.5, 1, 0.5],
          }}
          transition={{
            duration: 1.5,
            repeat: Infinity,
            ease: 'easeInOut',
            delay: 0.2,
          }}
        />
      </div>
    </div>
  )
}
```

### Notification toast avec animation

```tsx
import { motion, AnimatePresence } from 'framer-motion'
import { X } from 'lucide-react'

const toastVariants = {
  hidden: { 
    opacity: 0, 
    y: -50,
    scale: 0.3,
  },
  visible: { 
    opacity: 1, 
    y: 0,
    scale: 1,
    transition: {
      type: 'spring',
      damping: 20,
      stiffness: 300,
    }
  },
  exit: {
    opacity: 0,
    x: 200,
    transition: {
      duration: 0.2,
    }
  }
}

interface Toast {
  id: string
  message: string
  type?: 'success' | 'error' | 'info'
}

function ToastContainer({ toasts }: { toasts: Toast[] }) {
  return (
    <div className="fixed top-4 right-4 z-50 space-y-2">
      <AnimatePresence>
        {toasts.map((toast) => (
          <motion.div
            key={toast.id}
            layout
            variants={toastVariants}
            initial="hidden"
            animate="visible"
            exit="exit"
            className={`
              px-4 py-3 rounded-lg shadow-lg flex items-center gap-3
              ${toast.type === 'success' ? 'bg-green-500 text-white' : ''}
              ${toast.type === 'error' ? 'bg-red-500 text-white' : ''}
              ${toast.type === 'info' ? 'bg-blue-500 text-white' : ''}
            `}
          >
            <p>{toast.message}</p>
            <button className="ml-auto">
              <X className="w-4 h-4" />
            </button>
          </motion.div>
        ))}
      </AnimatePresence>
    </div>
  )
}
```

### Page transitions

```tsx
import { motion, AnimatePresence } from 'framer-motion'
import { useLocation, Outlet } from 'react-router-dom'

const pageVariants = {
  initial: {
    opacity: 0,
    x: -20,
  },
  animate: {
    opacity: 1,
    x: 0,
    transition: {
      duration: 0.3,
      ease: 'easeOut',
    },
  },
  exit: {
    opacity: 0,
    x: 20,
    transition: {
      duration: 0.2,
      ease: 'easeIn',
    },
  },
}

function AnimatedRoutes() {
  const location = useLocation()

  return (
    <AnimatePresence mode="wait">
      <motion.div
        key={location.pathname}
        variants={pageVariants}
        initial="initial"
        animate="animate"
        exit="exit"
      >
        <Outlet />
      </motion.div>
    </AnimatePresence>
  )
}
```

---

## Animations avancées

### Drag & Drop

```tsx
import { motion } from 'framer-motion'

function DraggableCard() {
  return (
    <motion.div
      className="w-64 h-32 bg-primary text-white rounded-lg p-4 cursor-grab active:cursor-grabbing"
      
      // Activer le drag
      drag
      
      // Contraindre le drag
      dragConstraints={{ 
        top: -100, 
        left: -100, 
        right: 100, 
        bottom: 100 
      }}
      
      // Élasticité
      dragElastic={0.1}
      
      // Animation lors du drag
      whileDrag={{ scale: 1.1 }}
    >
      Glissez-moi !
    </motion.div>
  )
}
```

### Scroll-triggered animations

```tsx
import { motion, useScroll, useTransform } from 'framer-motion'
import { useRef } from 'react'

function ParallaxSection() {
  const ref = useRef(null)
  
  // Suivre la position de scroll
  const { scrollYProgress } = useScroll({
    target: ref,
    offset: ['start end', 'end start']
  })
  
  // Transformer la valeur de scroll
  const y = useTransform(scrollYProgress, [0, 1], ['0%', '50%'])
  const opacity = useTransform(scrollYProgress, [0, 0.5, 1], [0, 1, 0])

  return (
    <section ref={ref} className="relative h-screen">
      <motion.div
        style={{ y, opacity }}
        className="sticky top-0"
      >
        Contenu avec parallax
      </motion.div>
    </section>
  )
}
```

### Path animations (SVG)

```tsx
import { motion } from 'framer-motion'

function AnimatedCheckmark() {
  return (
    <svg width="100" height="100" viewBox="0 0 100 100">
      <motion.path
        d="M 20 50 L 40 70 L 80 30"
        fill="none"
        stroke="#10b981"
        strokeWidth="8"
        strokeLinecap="round"
        
        // Animation du path
        initial={{ pathLength: 0 }}
        animate={{ pathLength: 1 }}
        transition={{ 
          duration: 0.5, 
          ease: 'easeInOut' 
        }}
      />
    </svg>
  )
}
```

---

## Respect de `prefers-reduced-motion`

### Hook personnalisé

```tsx
import { useState, useEffect } from 'react'

export function usePrefersReducedMotion() {
  const [prefersReducedMotion, setPrefersReducedMotion] = useState(false)

  useEffect(() => {
    const mediaQuery = window.matchMedia('(prefers-reduced-motion: reduce)')
    setPrefersReducedMotion(mediaQuery.matches)

    const handleChange = () => setPrefersReducedMotion(mediaQuery.matches)
    mediaQuery.addEventListener('change', handleChange)

    return () => mediaQuery.removeEventListener('change', handleChange)
  }, [])

  return prefersReducedMotion
}
```

### Utilisation avec Framer Motion

```tsx
import { motion } from 'framer-motion'
import { usePrefersReducedMotion } from '@/hooks/use-prefers-reduced-motion'

function AccessibleAnimation() {
  const prefersReducedMotion = usePrefersReducedMotion()

  return (
    <motion.div
      animate={{ opacity: 1, y: 0 }}
      initial={{ 
        opacity: 0, 
        y: prefersReducedMotion ? 0 : 20 
      }}
      transition={{ 
        duration: prefersReducedMotion ? 0 : 0.5 
      }}
    >
      Animation accessible
    </motion.div>
  )
}
```

---

## Résumé du chapitre

Dans ce chapitre, nous avons exploré les animations :

✅ **Framer Motion** : API puissante et intuitive
✅ **Micro-interactions** : Boutons, inputs, cards
✅ **Performance** : Transform, GPU, layout animations
✅ **Variants** : Animations réutilisables et organisées
✅ **Accessibilité** : Respect de `prefers-reduced-motion`

### Points clés à retenir

💡 Les animations doivent améliorer l'UX, pas la dégrader

💡 Utilisez `transform` et `opacity` pour la performance

💡 Framer Motion simplifie les animations complexes

💡 Respectez toujours `prefers-reduced-motion`

---

## Exercices

### Exercice 1 : Button interactif
Créez un bouton avec :
- Animation au hover
- Animation au clic
- Loading state animé
- Success state avec checkmark

### Exercice 2 : Liste animée
Implémentez une liste qui :
- Anime l'apparition des items en stagger
- Anime la suppression d'items
- Réorganise avec layout animations
- Supporte le drag & drop

### Exercice 3 : Modal complexe
Créez un modal avec :
- Animation d'entrée/sortie
- Backdrop animé
- Multi-step avec transitions
- Gestes de swipe pour fermer

---

**Prêt pour le projet fil rouge ?** → [Chapitre 12 : Projet fil rouge — Interface complète](./chapitre-12.md)

