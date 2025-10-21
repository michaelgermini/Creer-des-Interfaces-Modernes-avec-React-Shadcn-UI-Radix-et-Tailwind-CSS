# Chapitre 12 : Projet fil rouge — Interface complète

## Introduction

Il est temps de mettre en pratique tout ce que vous avez appris ! Dans ce chapitre, nous allons construire un **tableau de bord interactif** complet qui intègre tous les concepts du livre.

---

## Plan du projet : un tableau de bord interactif

### Vue d'ensemble

Nous allons créer un tableau de bord d'administration moderne avec :
- **Dashboard** : Statistiques et graphiques
- **Utilisateurs** : Liste et gestion
- **Paramètres** : Configuration de l'application
- **Profil** : Page de profil utilisateur

### Stack technique

```
React 18 + TypeScript
Vite
Tailwind CSS
Shadcn/UI
Radix UI
Framer Motion
Recharts (pour les graphiques)
React Router (navigation)
```

### Structure du projet

```
dashboard-app/
├── src/
│   ├── components/
│   │   ├── ui/              # Shadcn/UI components
│   │   ├── layout/
│   │   │   ├── sidebar.tsx
│   │   │   ├── header.tsx
│   │   │   └── layout.tsx
│   │   └── dashboard/
│   │       ├── stats-card.tsx
│   │       ├── recent-activity.tsx
│   │       └── chart-card.tsx
│   ├── pages/
│   │   ├── dashboard.tsx
│   │   ├── users.tsx
│   │   ├── settings.tsx
│   │   └── profile.tsx
│   ├── lib/
│   │   ├── utils.ts
│   │   └── api.ts
│   ├── hooks/
│   │   ├── use-sidebar.ts
│   │   └── use-theme.ts
│   └── App.tsx
└── package.json
```

---

## Conception des composants : sidebar, header, cards

### Layout principal

```tsx
// components/layout/layout.tsx
import { Sidebar } from './sidebar'
import { Header } from './header'
import { Outlet } from 'react-router-dom'

export function Layout() {
  return (
    <div className="flex h-screen bg-background">
      {/* Sidebar */}
      <Sidebar />
      
      {/* Main content */}
      <div className="flex-1 flex flex-col overflow-hidden">
        <Header />
        
        <main className="flex-1 overflow-y-auto p-6">
          <Outlet />
        </main>
      </div>
    </div>
  )
}
```

### Sidebar avec navigation

```tsx
// components/layout/sidebar.tsx
import { Home, Users, Settings, BarChart3, User, LogOut } from 'lucide-react'
import { NavLink } from 'react-router-dom'
import { cn } from '@/lib/utils'
import { motion } from 'framer-motion'

const navItems = [
  { icon: Home, label: 'Dashboard', to: '/' },
  { icon: BarChart3, label: 'Analytics', to: '/analytics' },
  { icon: Users, label: 'Utilisateurs', to: '/users' },
  { icon: Settings, label: 'Paramètres', to: '/settings' },
]

export function Sidebar() {
  return (
    <motion.aside
      className="w-64 bg-card border-r"
      initial={{ x: -100, opacity: 0 }}
      animate={{ x: 0, opacity: 1 }}
      transition={{ duration: 0.3 }}
    >
      {/* Logo */}
      <div className="h-16 flex items-center px-6 border-b">
        <h1 className="text-xl font-bold text-primary">DashboardApp</h1>
      </div>

      {/* Navigation */}
      <nav className="p-4 space-y-1">
        {navItems.map((item) => (
          <NavLink
            key={item.to}
            to={item.to}
            className={({ isActive }) =>
              cn(
                'flex items-center gap-3 px-3 py-2 rounded-lg transition-colors',
                isActive
                  ? 'bg-primary text-primary-foreground'
                  : 'text-muted-foreground hover:bg-accent hover:text-accent-foreground'
              )
            }
          >
            <item.icon className="w-5 h-5" />
            <span>{item.label}</span>
          </NavLink>
        ))}
      </nav>

      {/* User section */}
      <div className="absolute bottom-0 w-64 p-4 border-t">
        <div className="flex items-center gap-3">
          <div className="w-10 h-10 rounded-full bg-primary/10 flex items-center justify-center">
            <User className="w-5 h-5 text-primary" />
          </div>
          <div className="flex-1">
            <p className="text-sm font-medium">John Doe</p>
            <p className="text-xs text-muted-foreground">john@example.com</p>
          </div>
          <button className="p-2 hover:bg-accent rounded-md">
            <LogOut className="w-4 h-4" />
          </button>
        </div>
      </div>
    </motion.aside>
  )
}
```

### Header avec recherche et notifications

```tsx
// components/layout/header.tsx
import { Search, Bell, Menu } from 'lucide-react'
import { Input } from '@/components/ui/input'
import { Button } from '@/components/ui/button'
import { ThemeToggle } from '@/components/theme-toggle'
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuTrigger,
} from '@/components/ui/dropdown-menu'

export function Header() {
  return (
    <header className="h-16 border-b bg-card px-6 flex items-center gap-4">
      {/* Mobile menu button */}
      <Button variant="ghost" size="icon" className="md:hidden">
        <Menu className="w-5 h-5" />
      </Button>

      {/* Search */}
      <div className="flex-1 max-w-md">
        <div className="relative">
          <Search className="absolute left-3 top-1/2 -translate-y-1/2 w-4 h-4 text-muted-foreground" />
          <Input
            placeholder="Rechercher..."
            className="pl-10"
          />
        </div>
      </div>

      {/* Actions */}
      <div className="flex items-center gap-2">
        {/* Notifications */}
        <DropdownMenu>
          <DropdownMenuTrigger asChild>
            <Button variant="ghost" size="icon" className="relative">
              <Bell className="w-5 h-5" />
              <span className="absolute top-1 right-1 w-2 h-2 bg-red-500 rounded-full" />
            </Button>
          </DropdownMenuTrigger>
          <DropdownMenuContent align="end" className="w-80">
            <div className="p-2">
              <p className="font-semibold mb-2">Notifications</p>
              <div className="space-y-2">
                <div className="p-2 hover:bg-accent rounded">
                  <p className="text-sm">Nouveau commentaire</p>
                  <p className="text-xs text-muted-foreground">Il y a 5 minutes</p>
                </div>
                <div className="p-2 hover:bg-accent rounded">
                  <p className="text-sm">Mise à jour disponible</p>
                  <p className="text-xs text-muted-foreground">Il y a 1 heure</p>
                </div>
              </div>
            </div>
          </DropdownMenuContent>
        </DropdownMenu>

        {/* Theme toggle */}
        <ThemeToggle />
      </div>
    </header>
  )
}
```

### Stats Card

```tsx
// components/dashboard/stats-card.tsx
import { LucideIcon } from 'lucide-react'
import { Card, CardContent } from '@/components/ui/card'
import { motion } from 'framer-motion'

interface StatsCardProps {
  title: string
  value: string | number
  icon: LucideIcon
  trend?: {
    value: number
    isPositive: boolean
  }
  index?: number
}

export function StatsCard({ 
  title, 
  value, 
  icon: Icon, 
  trend,
  index = 0 
}: StatsCardProps) {
  return (
    <motion.div
      initial={{ opacity: 0, y: 20 }}
      animate={{ opacity: 1, y: 0 }}
      transition={{ duration: 0.3, delay: index * 0.1 }}
    >
      <Card>
        <CardContent className="p-6">
          <div className="flex items-center justify-between">
            <div>
              <p className="text-sm text-muted-foreground">{title}</p>
              <p className="text-3xl font-bold mt-2">{value}</p>
              {trend && (
                <p className={cn(
                  'text-sm mt-2',
                  trend.isPositive ? 'text-green-600' : 'text-red-600'
                )}>
                  {trend.isPositive ? '↑' : '↓'} {Math.abs(trend.value)}%
                </p>
              )}
            </div>
            <div className="w-12 h-12 rounded-full bg-primary/10 flex items-center justify-center">
              <Icon className="w-6 h-6 text-primary" />
            </div>
          </div>
        </CardContent>
      </Card>
    </motion.div>
  )
}
```

---

## Page Dashboard

```tsx
// pages/dashboard.tsx
import { StatsCard } from '@/components/dashboard/stats-card'
import { ChartCard } from '@/components/dashboard/chart-card'
import { RecentActivity } from '@/components/dashboard/recent-activity'
import { Users, DollarSign, ShoppingCart, TrendingUp } from 'lucide-react'

const stats = [
  {
    title: 'Total Utilisateurs',
    value: '12,345',
    icon: Users,
    trend: { value: 12.5, isPositive: true },
  },
  {
    title: 'Revenu',
    value: '45,678 €',
    icon: DollarSign,
    trend: { value: 8.2, isPositive: true },
  },
  {
    title: 'Commandes',
    value: '892',
    icon: ShoppingCart,
    trend: { value: 3.1, isPositive: false },
  },
  {
    title: 'Croissance',
    value: '+23%',
    icon: TrendingUp,
    trend: { value: 15.3, isPositive: true },
  },
]

export function DashboardPage() {
  return (
    <div className="space-y-6">
      <div>
        <h1 className="text-3xl font-bold">Dashboard</h1>
        <p className="text-muted-foreground">
          Vue d'ensemble de vos statistiques
        </p>
      </div>

      {/* Stats Grid */}
      <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
        {stats.map((stat, index) => (
          <StatsCard key={stat.title} {...stat} index={index} />
        ))}
      </div>

      {/* Charts */}
      <div className="grid grid-cols-1 lg:grid-cols-2 gap-6">
        <ChartCard title="Revenus mensuels" type="line" />
        <ChartCard title="Répartition des ventes" type="pie" />
      </div>

      {/* Recent Activity */}
      <RecentActivity />
    </div>
  )
}
```

---

## Page Utilisateurs avec table

```tsx
// pages/users.tsx
import { useState } from 'react'
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from '@/components/ui/table'
import { Button } from '@/components/ui/button'
import { Input } from '@/components/ui/input'
import { Search, Plus, MoreVertical } from 'lucide-react'
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuTrigger,
} from '@/components/ui/dropdown-menu'

const users = [
  { id: 1, name: 'Jean Dupont', email: 'jean@example.com', role: 'Admin', status: 'Active' },
  { id: 2, name: 'Marie Martin', email: 'marie@example.com', role: 'User', status: 'Active' },
  { id: 3, name: 'Paul Bernard', email: 'paul@example.com', role: 'User', status: 'Inactive' },
]

export function UsersPage() {
  const [search, setSearch] = useState('')

  const filteredUsers = users.filter(user =>
    user.name.toLowerCase().includes(search.toLowerCase()) ||
    user.email.toLowerCase().includes(search.toLowerCase())
  )

  return (
    <div className="space-y-6">
      {/* Header */}
      <div className="flex items-center justify-between">
        <div>
          <h1 className="text-3xl font-bold">Utilisateurs</h1>
          <p className="text-muted-foreground">
            Gérez vos utilisateurs
          </p>
        </div>
        <Button>
          <Plus className="w-4 h-4 mr-2" />
          Nouvel utilisateur
        </Button>
      </div>

      {/* Search */}
      <div className="flex items-center gap-4">
        <div className="relative flex-1 max-w-sm">
          <Search className="absolute left-3 top-1/2 -translate-y-1/2 w-4 h-4 text-muted-foreground" />
          <Input
            placeholder="Rechercher un utilisateur..."
            value={search}
            onChange={(e) => setSearch(e.target.value)}
            className="pl-10"
          />
        </div>
      </div>

      {/* Table */}
      <div className="border rounded-lg">
        <Table>
          <TableHeader>
            <TableRow>
              <TableHead>Nom</TableHead>
              <TableHead>Email</TableHead>
              <TableHead>Rôle</TableHead>
              <TableHead>Statut</TableHead>
              <TableHead className="w-[50px]"></TableHead>
            </TableRow>
          </TableHeader>
          <TableBody>
            {filteredUsers.map((user) => (
              <TableRow key={user.id}>
                <TableCell className="font-medium">{user.name}</TableCell>
                <TableCell>{user.email}</TableCell>
                <TableCell>{user.role}</TableCell>
                <TableCell>
                  <span className={cn(
                    'px-2 py-1 rounded-full text-xs',
                    user.status === 'Active'
                      ? 'bg-green-100 text-green-700 dark:bg-green-900 dark:text-green-300'
                      : 'bg-gray-100 text-gray-700 dark:bg-gray-800 dark:text-gray-300'
                  )}>
                    {user.status}
                  </span>
                </TableCell>
                <TableCell>
                  <DropdownMenu>
                    <DropdownMenuTrigger asChild>
                      <Button variant="ghost" size="icon">
                        <MoreVertical className="w-4 h-4" />
                      </Button>
                    </DropdownMenuTrigger>
                    <DropdownMenuContent align="end">
                      <DropdownMenuItem>Modifier</DropdownMenuItem>
                      <DropdownMenuItem>Désactiver</DropdownMenuItem>
                      <DropdownMenuItem className="text-red-600">
                        Supprimer
                      </DropdownMenuItem>
                    </DropdownMenuContent>
                  </DropdownMenu>
                </TableCell>
              </TableRow>
            ))}
          </TableBody>
        </Table>
      </div>
    </div>
  )
}
```

---

## Page Settings avec Tabs

```tsx
// pages/settings.tsx
import { Tabs, TabsContent, TabsList, TabsTrigger } from '@/components/ui/tabs'
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from '@/components/ui/card'
import { Label } from '@/components/ui/label'
import { Input } from '@/components/ui/input'
import { Button } from '@/components/ui/button'
import { Switch } from '@/components/ui/switch'

export function SettingsPage() {
  return (
    <div className="space-y-6">
      <div>
        <h1 className="text-3xl font-bold">Paramètres</h1>
        <p className="text-muted-foreground">
          Gérez les paramètres de votre application
        </p>
      </div>

      <Tabs defaultValue="general" className="space-y-4">
        <TabsList>
          <TabsTrigger value="general">Général</TabsTrigger>
          <TabsTrigger value="notifications">Notifications</TabsTrigger>
          <TabsTrigger value="security">Sécurité</TabsTrigger>
        </TabsList>

        <TabsContent value="general" className="space-y-4">
          <Card>
            <CardHeader>
              <CardTitle>Informations générales</CardTitle>
              <CardDescription>
                Configurez les paramètres de base de l'application
              </CardDescription>
            </CardHeader>
            <CardContent className="space-y-4">
              <div className="space-y-2">
                <Label htmlFor="app-name">Nom de l'application</Label>
                <Input id="app-name" defaultValue="DashboardApp" />
              </div>
              <div className="space-y-2">
                <Label htmlFor="app-url">URL de l'application</Label>
                <Input id="app-url" defaultValue="https://app.example.com" />
              </div>
              <Button>Enregistrer les modifications</Button>
            </CardContent>
          </Card>
        </TabsContent>

        <TabsContent value="notifications" className="space-y-4">
          <Card>
            <CardHeader>
              <CardTitle>Préférences de notification</CardTitle>
              <CardDescription>
                Choisissez comment vous souhaitez être notifié
              </CardDescription>
            </CardHeader>
            <CardContent className="space-y-4">
              <div className="flex items-center justify-between">
                <div>
                  <p className="font-medium">Notifications email</p>
                  <p className="text-sm text-muted-foreground">
                    Recevoir des notifications par email
                  </p>
                </div>
                <Switch />
              </div>
              <div className="flex items-center justify-between">
                <div>
                  <p className="font-medium">Notifications push</p>
                  <p className="text-sm text-muted-foreground">
                    Recevoir des notifications push
                  </p>
                </div>
                <Switch />
              </div>
              <Button>Enregistrer les préférences</Button>
            </CardContent>
          </Card>
        </TabsContent>

        <TabsContent value="security" className="space-y-4">
          <Card>
            <CardHeader>
              <CardTitle>Sécurité</CardTitle>
              <CardDescription>
                Gérez vos paramètres de sécurité
              </CardDescription>
            </CardHeader>
            <CardContent className="space-y-4">
              <div className="space-y-2">
                <Label htmlFor="current-password">Mot de passe actuel</Label>
                <Input id="current-password" type="password" />
              </div>
              <div className="space-y-2">
                <Label htmlFor="new-password">Nouveau mot de passe</Label>
                <Input id="new-password" type="password" />
              </div>
              <div className="space-y-2">
                <Label htmlFor="confirm-password">Confirmer le mot de passe</Label>
                <Input id="confirm-password" type="password" />
              </div>
              <Button>Changer le mot de passe</Button>
            </CardContent>
          </Card>
        </TabsContent>
      </Tabs>
    </div>
  )
}
```

---

## Configuration du routing

```tsx
// App.tsx
import { BrowserRouter, Routes, Route } from 'react-router-dom'
import { ThemeProvider } from './components/theme-provider'
import { Layout } from './components/layout/layout'
import { DashboardPage } from './pages/dashboard'
import { UsersPage } from './pages/users'
import { SettingsPage } from './pages/settings'

function App() {
  return (
    <ThemeProvider>
      <BrowserRouter>
        <Routes>
          <Route element={<Layout />}>
            <Route path="/" element={<DashboardPage />} />
            <Route path="/users" element={<UsersPage />} />
            <Route path="/settings" element={<SettingsPage />} />
          </Route>
        </Routes>
      </BrowserRouter>
    </ThemeProvider>
  )
}

export default App
```

---

## Résumé du chapitre

Dans ce chapitre, nous avons construit un projet complet :

✅ **Layout** : Sidebar, Header avec navigation
✅ **Dashboard** : Stats cards, graphiques, activité récente
✅ **Utilisateurs** : Table avec recherche et actions
✅ **Paramètres** : Tabs avec formulaires
✅ **Routing** : Navigation entre pages
✅ **Thème** : Dark/light mode intégré

### Points clés à retenir

💡 Organisez votre code par fonctionnalité

💡 Réutilisez les composants autant que possible

💡 Les animations améliorent l'expérience utilisateur

💡 Testez sur différents appareils et thèmes

---

**Prêt à déployer ?** → [Chapitre 13 : Déploiement et optimisation](./chapitre-13.md)

