# Pressing LIC — Frontend (Angular)

SPA Angular consommant l'API Laravel `pressing-api` : catalogue de services,
dépôt et suivi de commandes côté client, gestion des tickets/services/statistiques
côté gestionnaire.

## Stack technique

- **Framework** : Angular 22 (composants standalone, signals, Reactive Forms)
- **Authentification** : token Bearer (Laravel Sanctum), intercepteur HTTP dédié
- **Statistiques** : Chart.js
- **Tests** : Vitest (nouveau test runner Angular)
- **Style** : CSS fait main, pas de framework externe

## Prérequis

- Node.js 22+
- npm
- L'API Laravel `pressing-api` doit tourner en parallèle (voir son propre README)

## Installation

```bash
git clone <url-du-depot>
cd pressing-frontend

npm install
```

L'URL de l'API est configurée dans `src/environments/environment.development.ts` :

```ts
export const environment = {
  production: false,
  apiUrl: 'http://localhost:8000/api',
};
```

Adapte le port si ton API Laravel tourne ailleurs qu'en local sur `8000`.

## Lancer le projet

Le frontend seul ne suffit pas : il faut aussi l'API Laravel active (et son worker
de file d'attente si `QUEUE_CONNECTION=database` côté API, voir son README).

```bash
# Terminal 1 — API Laravel (voir pressing-api/README.md)
php artisan serve

# Terminal 2 — worker de file d'attente (emails), si besoin
php artisan queue:work

# Terminal 3 — frontend Angular
ng serve
```

L'application est alors accessible sur `http://localhost:4200`.

## Comptes de test

Identiques à ceux créés par le seeder Laravel (voir `pressing-api/README.md`) :

| Rôle | Email | Mot de passe |
|---|---|---|
| Gestionnaire | `gestionnaire@pressing-lic.com` | `password` |
| Client | `client1@pressing-lic.com` | `password` |

## Vérifier que tout compile

```bash
npx tsc --noEmit -p tsconfig.app.json
npx tsc --noEmit -p tsconfig.spec.json
```

## Tests

```bash
npm test
```

## Build de production

```bash
npm run build
```

Les artefacts sont générés dans `dist/`.

## Structure du projet

```
src/app/
├── core/
│   ├── guards/          # authGuard, roleGuard
│   ├── interceptors/    # authInterceptor (ajoute le token Bearer)
│   ├── models/          # Interfaces alignées sur les réponses de l'API
│   └── services/        # Auth, ServiceApi, TicketApi, PaymentApi, StatsApi
├── shared/
│   └── navbar/          # Barre de navigation partagée (adapte les liens au rôle connecté)
└── features/
    ├── auth/             # Login, Register
    ├── catalogue/         # Catalogue des services (client)
    ├── tickets/
    │   ├── depot-commande/   # Dépôt d'une commande (client)
    │   ├── detail-ticket/    # Détail + actions (client et gestionnaire)
    │   ├── mes-commandes/    # Historique des commandes (client)
    │   └── liste-tickets/    # Liste de tous les tickets (gestionnaire)
    ├── services-admin/    # CRUD des services (gestionnaire)
    └── dashboard/          # Statistiques et graphiques Chart.js (gestionnaire)
```

## Routes principales

| Route | Accès | Description |
|---|---|---|
| `/login`, `/register` | Public | Authentification |
| `/catalogue` | Authentifié | Catalogue des services |
| `/deposer-commande` | Client | Dépôt d'une commande |
| `/mes-commandes` | Client | Historique des commandes |
| `/tickets/:id` | Authentifié | Détail d'un ticket |
| `/gestion/tickets` | Gestionnaire | Liste et traitement des tickets |
| `/gestion/services` | Gestionnaire | CRUD des services |
| `/gestion/dashboard` | Gestionnaire | Statistiques |