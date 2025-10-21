# Chapitre 2 : Fondamentaux de React

## Introduction

Ce chapitre vous permettra de consolider vos connaissances en React avant d'aborder les outils plus avancés. Nous allons explorer les concepts essentiels qui forment la base de toute application React moderne.

💡 **Note** : Si vous maîtrisez déjà ces concepts, ce chapitre servira d'excellent rappel et de référence.

---

## Rappels sur JSX et les composants

### Qu'est-ce que JSX ?

JSX (JavaScript XML) est une extension de syntaxe pour JavaScript qui ressemble à du HTML mais possède toute la puissance de JavaScript.

```tsx
// JSX - ce que vous écrivez
const element = <h1 className="title">Bonjour le monde!</h1>;

// JavaScript - ce que Babel transforme
const element = React.createElement(
  'h1',
  { className: 'title' },
  'Bonjour le monde!'
);
```

### Les règles fondamentales de JSX

#### 1. Un seul élément racine

```tsx
// ❌ Incorrect : plusieurs éléments racines
function MyComponent() {
  return (
    <h1>Titre</h1>
    <p>Paragraphe</p>
  );
}

// ✅ Correct : un élément racine
function MyComponent() {
  return (
    <div>
      <h1>Titre</h1>
      <p>Paragraphe</p>
    </div>
  );
}

// ✅ Encore mieux : Fragment
function MyComponent() {
  return (
    <>
      <h1>Titre</h1>
      <p>Paragraphe</p>
    </>
  );
}
```

#### 2. Toutes les balises doivent être fermées

```tsx
// ❌ Incorrect
<img src="photo.jpg">
<input type="text">

// ✅ Correct
<img src="photo.jpg" />
<input type="text" />
```

#### 3. camelCase pour les attributs

```tsx
// HTML
<div class="container" tabindex="0"></div>

// JSX
<div className="container" tabIndex={0}></div>
```

### JavaScript dans JSX

Utilisez les accolades `{}` pour insérer du JavaScript :

```tsx
function Greeting({ name, age }: { name: string; age: number }) {
  const message = `Bienvenue ${name}`;
  
  return (
    <div>
      {/* Expression simple */}
      <h1>{message}</h1>
      
      {/* Calcul */}
      <p>Dans 5 ans, vous aurez {age + 5} ans</p>
      
      {/* Conditionnel */}
      {age >= 18 ? <p>Majeur</p> : <p>Mineur</p>}
      
      {/* Condition && */}
      {age >= 65 && <p>Senior</p>}
      
      {/* Fonction */}
      <p>{message.toUpperCase()}</p>
    </div>
  );
}
```

### Composants : Fonctions et Classes

Aujourd'hui, les **composants fonctionnels** sont la norme :

```tsx
// ✅ Composant fonctionnel moderne
function Button({ text, onClick }: { text: string; onClick: () => void }) {
  return <button onClick={onClick}>{text}</button>;
}

// Arrow function (équivalent)
const Button = ({ text, onClick }: { text: string; onClick: () => void }) => {
  return <button onClick={onClick}>{text}</button>;
};

// ⚠️ Composant classe (legacy, éviter pour nouveau code)
class Button extends React.Component {
  render() {
    return <button onClick={this.props.onClick}>{this.props.text}</button>;
  }
}
```

---

## Les hooks essentiels

Les hooks ont révolutionné React en permettant l'utilisation de l'état et d'autres fonctionnalités dans les composants fonctionnels.

### useState : Gérer l'état local

```tsx
import { useState } from 'react';

function Counter() {
  // Déclaration d'une variable d'état
  const [count, setCount] = useState(0);
  
  // Plusieurs états indépendants
  const [name, setName] = useState('');
  const [isVisible, setIsVisible] = useState(true);
  
  return (
    <div>
      <p>Compteur : {count}</p>
      <button onClick={() => setCount(count + 1)}>
        Incrémenter
      </button>
      <button onClick={() => setCount(count - 1)}>
        Décrémenter
      </button>
      <button onClick={() => setCount(0)}>
        Réinitialiser
      </button>
    </div>
  );
}
```

#### État avec objets et tableaux

```tsx
function UserForm() {
  // État objet
  const [user, setUser] = useState({
    name: '',
    email: '',
    age: 0,
  });
  
  // Mise à jour d'une propriété (spread operator)
  const updateName = (newName: string) => {
    setUser(prev => ({ ...prev, name: newName }));
  };
  
  // État tableau
  const [todos, setTodos] = useState<string[]>([]);
  
  // Ajouter un élément
  const addTodo = (todo: string) => {
    setTodos(prev => [...prev, todo]);
  };
  
  // Supprimer un élément
  const removeTodo = (index: number) => {
    setTodos(prev => prev.filter((_, i) => i !== index));
  };
  
  return (
    <div>
      <input
        value={user.name}
        onChange={(e) => updateName(e.target.value)}
        placeholder="Nom"
      />
      {/* ... */}
    </div>
  );
}
```

#### Forme fonctionnelle de setState

```tsx
function Counter() {
  const [count, setCount] = useState(0);
  
  // ❌ Problème : utilise la valeur capturée
  const incrementThreeTimes = () => {
    setCount(count + 1); // count = 0, devient 1
    setCount(count + 1); // count = 0, devient 1
    setCount(count + 1); // count = 0, devient 1
    // Résultat final : 1 (pas 3!)
  };
  
  // ✅ Solution : forme fonctionnelle
  const incrementThreeTimesCorrect = () => {
    setCount(prev => prev + 1); // 0 -> 1
    setCount(prev => prev + 1); // 1 -> 2
    setCount(prev => prev + 1); // 2 -> 3
    // Résultat final : 3 ✓
  };
  
  return (
    <button onClick={incrementThreeTimesCorrect}>
      Compteur : {count}
    </button>
  );
}
```

### useEffect : Effets de bord

```tsx
import { useState, useEffect } from 'react';

function UserProfile({ userId }: { userId: string }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  
  // Effect qui s'exécute quand userId change
  useEffect(() => {
    let cancelled = false;
    
    async function fetchUser() {
      setLoading(true);
      try {
        const response = await fetch(`/api/users/${userId}`);
        const data = await response.json();
        
        // Éviter la mise à jour si le composant est démonté
        if (!cancelled) {
          setUser(data);
        }
      } catch (error) {
        console.error(error);
      } finally {
        if (!cancelled) {
          setLoading(false);
        }
      }
    }
    
    fetchUser();
    
    // Cleanup function
    return () => {
      cancelled = true;
    };
  }, [userId]); // Dépendances
  
  if (loading) return <div>Chargement...</div>;
  
  return <div>{user?.name}</div>;
}
```

#### Cas d'usage courants de useEffect

```tsx
function ExamplesUseEffect() {
  // 1. S'exécute à chaque render
  useEffect(() => {
    console.log('Rendu effectué');
  });
  
  // 2. S'exécute une seule fois au montage
  useEffect(() => {
    console.log('Composant monté');
    
    return () => {
      console.log('Composant démonté');
    };
  }, []); // Tableau vide = pas de dépendances
  
  // 3. S'exécute quand 'count' change
  const [count, setCount] = useState(0);
  useEffect(() => {
    document.title = `Compteur : ${count}`;
  }, [count]);
  
  // 4. Abonnement et désabonnement
  useEffect(() => {
    const handleResize = () => {
      console.log('Fenêtre redimensionnée');
    };
    
    window.addEventListener('resize', handleResize);
    
    // Nettoyage
    return () => {
      window.removeEventListener('resize', handleResize);
    };
  }, []);
  
  return <div>Exemples useEffect</div>;
}
```

### useContext : Partager des données

```tsx
import { createContext, useContext, useState } from 'react';

// 1. Créer le contexte
interface ThemeContextType {
  theme: 'light' | 'dark';
  toggleTheme: () => void;
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

// 2. Créer le Provider
function ThemeProvider({ children }: { children: React.ReactNode }) {
  const [theme, setTheme] = useState<'light' | 'dark'>('light');
  
  const toggleTheme = () => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light');
  };
  
  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

// 3. Créer un hook personnalisé pour l'utiliser
function useTheme() {
  const context = useContext(ThemeContext);
  if (context === undefined) {
    throw new Error('useTheme doit être utilisé dans un ThemeProvider');
  }
  return context;
}

// 4. Utiliser dans les composants
function ThemeToggle() {
  const { theme, toggleTheme } = useTheme();
  
  return (
    <button onClick={toggleTheme}>
      Mode actuel : {theme}
    </button>
  );
}

function App() {
  return (
    <ThemeProvider>
      <ThemeToggle />
    </ThemeProvider>
  );
}
```

---

## Composition, props et gestion des états

### Composition de composants

La composition est le principe fondamental de React : assembler des composants simples pour créer des interfaces complexes.

```tsx
// Composants simples
function Card({ children, className }: { 
  children: React.ReactNode; 
  className?: string;
}) {
  return (
    <div className={`rounded-lg border p-4 ${className}`}>
      {children}
    </div>
  );
}

function CardHeader({ children }: { children: React.ReactNode }) {
  return <div className="mb-4">{children}</div>;
}

function CardTitle({ children }: { children: React.ReactNode }) {
  return <h3 className="text-xl font-bold">{children}</h3>;
}

function CardContent({ children }: { children: React.ReactNode }) {
  return <div>{children}</div>;
}

// Composition
function UserCard({ user }: { user: User }) {
  return (
    <Card>
      <CardHeader>
        <CardTitle>{user.name}</CardTitle>
      </CardHeader>
      <CardContent>
        <p>Email : {user.email}</p>
        <p>Âge : {user.age}</p>
      </CardContent>
    </Card>
  );
}
```

### Props : Passer des données

```tsx
// Types de props
interface ButtonProps {
  // Requis
  children: React.ReactNode;
  onClick: () => void;
  
  // Optionnel
  variant?: 'primary' | 'secondary' | 'danger';
  size?: 'sm' | 'md' | 'lg';
  disabled?: boolean;
  className?: string;
  
  // Valeur par défaut
  type?: 'button' | 'submit' | 'reset';
}

function Button({
  children,
  onClick,
  variant = 'primary',
  size = 'md',
  disabled = false,
  className = '',
  type = 'button',
}: ButtonProps) {
  const baseClasses = 'rounded font-medium transition-colors';
  
  const variantClasses = {
    primary: 'bg-blue-500 text-white hover:bg-blue-600',
    secondary: 'bg-gray-200 text-gray-900 hover:bg-gray-300',
    danger: 'bg-red-500 text-white hover:bg-red-600',
  };
  
  const sizeClasses = {
    sm: 'px-3 py-1 text-sm',
    md: 'px-4 py-2',
    lg: 'px-6 py-3 text-lg',
  };
  
  return (
    <button
      type={type}
      onClick={onClick}
      disabled={disabled}
      className={`
        ${baseClasses}
        ${variantClasses[variant]}
        ${sizeClasses[size]}
        ${disabled ? 'opacity-50 cursor-not-allowed' : ''}
        ${className}
      `}
    >
      {children}
    </button>
  );
}

// Utilisation
<Button onClick={() => console.log('Click!')} variant="primary" size="lg">
  Cliquez ici
</Button>
```

### Spread props et rest

```tsx
// Transmettre toutes les props natives d'un élément
function Input({ className, ...props }: React.InputHTMLAttributes<HTMLInputElement>) {
  return (
    <input
      className={`border rounded px-3 py-2 ${className}`}
      {...props}
    />
  );
}

// Utilisation : toutes les props HTML sont supportées
<Input
  type="email"
  placeholder="votre@email.com"
  required
  autoComplete="email"
/>
```

### Remonter l'état (Lifting State Up)

```tsx
// Parent gère l'état partagé
function TodoApp() {
  const [todos, setTodos] = useState<string[]>([]);
  
  const addTodo = (todo: string) => {
    setTodos(prev => [...prev, todo]);
  };
  
  const removeTodo = (index: number) => {
    setTodos(prev => prev.filter((_, i) => i !== index));
  };
  
  return (
    <div>
      {/* Enfants reçoivent l'état et les fonctions */}
      <TodoForm onAdd={addTodo} />
      <TodoList todos={todos} onRemove={removeTodo} />
    </div>
  );
}

function TodoForm({ onAdd }: { onAdd: (todo: string) => void }) {
  const [input, setInput] = useState('');
  
  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    if (input.trim()) {
      onAdd(input);
      setInput('');
    }
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input
        value={input}
        onChange={(e) => setInput(e.target.value)}
        placeholder="Nouvelle tâche"
      />
      <button type="submit">Ajouter</button>
    </form>
  );
}

function TodoList({ 
  todos, 
  onRemove 
}: { 
  todos: string[]; 
  onRemove: (index: number) => void;
}) {
  return (
    <ul>
      {todos.map((todo, index) => (
        <li key={index}>
          {todo}
          <button onClick={() => onRemove(index)}>Supprimer</button>
        </li>
      ))}
    </ul>
  );
}
```

---

## Composants contrôlés et non contrôlés

### Composants contrôlés (recommandé)

React contrôle la valeur de l'input :

```tsx
function ControlledForm() {
  const [formData, setFormData] = useState({
    username: '',
    email: '',
    password: '',
  });
  
  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setFormData(prev => ({ ...prev, [name]: value }));
  };
  
  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    console.log('Données soumises:', formData);
    // Validation, envoi API, etc.
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input
        name="username"
        value={formData.username}
        onChange={handleChange}
        placeholder="Nom d'utilisateur"
      />
      <input
        name="email"
        type="email"
        value={formData.email}
        onChange={handleChange}
        placeholder="Email"
      />
      <input
        name="password"
        type="password"
        value={formData.password}
        onChange={handleChange}
        placeholder="Mot de passe"
      />
      <button type="submit">S'inscrire</button>
    </form>
  );
}
```

### Composants non contrôlés (cas spécifiques)

Le DOM conserve la valeur :

```tsx
import { useRef } from 'react';

function UncontrolledForm() {
  const usernameRef = useRef<HTMLInputElement>(null);
  const emailRef = useRef<HTMLInputElement>(null);
  
  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    
    const username = usernameRef.current?.value;
    const email = emailRef.current?.value;
    
    console.log('Données:', { username, email });
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input ref={usernameRef} placeholder="Nom d'utilisateur" />
      <input ref={emailRef} type="email" placeholder="Email" />
      <button type="submit">Envoyer</button>
    </form>
  );
}
```

💡 **Quand utiliser les composants non contrôlés ?**
- Intégration avec du code non-React
- Upload de fichiers
- Performance critique sur de très gros formulaires

---

## Optimisation avec memo, useCallback, useMemo

### React.memo : Éviter les re-renders inutiles

```tsx
import { memo } from 'react';

// Composant qui se re-render à chaque fois
function ExpensiveComponent({ data }: { data: Data }) {
  console.log('Render ExpensiveComponent');
  // Calculs coûteux...
  return <div>{data.value}</div>;
}

// Version optimisée
const MemoizedComponent = memo(ExpensiveComponent);

// Ne se re-render que si 'data' change
function Parent() {
  const [count, setCount] = useState(0);
  const data = { value: 'Test' };
  
  return (
    <div>
      <button onClick={() => setCount(c => c + 1)}>
        Compteur : {count}
      </button>
      {/* Se re-render même si data ne change pas */}
      <ExpensiveComponent data={data} />
      {/* Ne se re-render pas si data n'a pas changé */}
      <MemoizedComponent data={data} />
    </div>
  );
}
```

#### Comparaison personnalisée

```tsx
const MemoizedUser = memo(
  UserCard,
  (prevProps, nextProps) => {
    // Retourner true si les props sont égales (pas de re-render)
    return prevProps.user.id === nextProps.user.id;
  }
);
```

### useCallback : Mémoriser des fonctions

```tsx
import { useState, useCallback, memo } from 'react';

// Enfant mémorisé
const TodoItem = memo(({ 
  todo, 
  onRemove 
}: { 
  todo: string; 
  onRemove: () => void;
}) => {
  console.log('Render TodoItem:', todo);
  return (
    <li>
      {todo}
      <button onClick={onRemove}>×</button>
    </li>
  );
});

function TodoList() {
  const [todos, setTodos] = useState(['Tâche 1', 'Tâche 2']);
  const [count, setCount] = useState(0);
  
  // ❌ Nouvelle fonction à chaque render
  const removeTodo = (index: number) => {
    setTodos(prev => prev.filter((_, i) => i !== index));
  };
  
  // ✅ Fonction mémorisée
  const removeTodoMemoized = useCallback((index: number) => {
    setTodos(prev => prev.filter((_, i) => i !== index));
  }, []); // Pas de dépendances, fonction stable
  
  return (
    <div>
      <button onClick={() => setCount(c => c + 1)}>
        Compteur : {count}
      </button>
      <ul>
        {todos.map((todo, index) => (
          <TodoItem
            key={index}
            todo={todo}
            onRemove={() => removeTodoMemoized(index)}
          />
        ))}
      </ul>
    </div>
  );
}
```

### useMemo : Mémoriser des calculs

```tsx
import { useMemo, useState } from 'react';

function ExpensiveCalculation({ numbers }: { numbers: number[] }) {
  const [count, setCount] = useState(0);
  
  // ❌ Recalculé à chaque render
  const sum = numbers.reduce((acc, n) => acc + n, 0);
  
  // ✅ Recalculé seulement si 'numbers' change
  const memoizedSum = useMemo(() => {
    console.log('Calcul de la somme...');
    return numbers.reduce((acc, n) => acc + n, 0);
  }, [numbers]);
  
  return (
    <div>
      <p>Somme : {memoizedSum}</p>
      <button onClick={() => setCount(c => c + 1)}>
        Compteur : {count}
      </button>
    </div>
  );
}
```

#### Cas d'usage typiques de useMemo

```tsx
function UserList({ users, searchTerm }: { users: User[]; searchTerm: string }) {
  // Filtrer les utilisateurs (opération coûteuse si beaucoup d'utilisateurs)
  const filteredUsers = useMemo(() => {
    return users.filter(user =>
      user.name.toLowerCase().includes(searchTerm.toLowerCase())
    );
  }, [users, searchTerm]);
  
  // Trier les utilisateurs
  const sortedUsers = useMemo(() => {
    return [...filteredUsers].sort((a, b) => a.name.localeCompare(b.name));
  }, [filteredUsers]);
  
  return (
    <ul>
      {sortedUsers.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

⚠️ **Attention** : N'optimisez pas prématurément ! Utilisez ces hooks seulement quand vous avez identifié un problème de performance réel.

---

## Résumé du chapitre

Dans ce chapitre, nous avons couvert les fondamentaux essentiels de React :

✅ **JSX** : Syntaxe, règles, expressions JavaScript
✅ **Composants** : Fonctionnels, composition, props
✅ **Hooks essentiels** : useState, useEffect, useContext
✅ **Gestion d'état** : Local, remonté, partagé
✅ **Formulaires** : Contrôlés vs non contrôlés
✅ **Optimisation** : memo, useCallback, useMemo

### Points clés à retenir

💡 Préférez les composants fonctionnels avec hooks

💡 Utilisez la composition pour créer des interfaces complexes

💡 Remontez l'état au niveau du parent commun le plus proche

💡 Optimisez seulement quand nécessaire, après avoir mesuré les performances

---

## Exercices

### Exercice 1 : Compteur avancé
Créez un compteur avec :
- Boutons pour incrémenter/décrémenter
- Possibilité de définir le pas (step)
- Historique des valeurs
- Bouton pour annuler la dernière action

### Exercice 2 : Formulaire de recherche
Créez un composant de recherche qui :
- Filtre une liste d'utilisateurs en temps réel
- Affiche le nombre de résultats
- Surligne les termes recherchés
- Optimisez avec useMemo

### Exercice 3 : Gestionnaire de thème
Implémentez un système de thème avec Context :
- Light/Dark mode
- Persistence avec localStorage
- Bouton toggle dans plusieurs composants

---

**Prêt à découvrir Tailwind CSS ?** → [Chapitre 3 : Introduction à Tailwind CSS](./chapitre-03.md)

