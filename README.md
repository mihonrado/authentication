# Laravel 13 + Nuxt SaaS Auth Starter

A production-ready authentication starter kit for building SaaS applications, combining a **Laravel 13** API backend with a **Nuxt 3** frontend. Includes everything you need to spin up a multi-tenant, subscription-ready SaaS product without rebuilding auth from scratch.

---

## Features

- 🔐 **Token-based authentication** via Laravel Sanctum (SPA cookie auth)
- ✉️ Email verification & password reset flows
- 👥 Team / organization support with role-based access control
- 🏢 Multi-tenancy ready (single-DB or DB-per-tenant patterns)
- 💳 Subscription billing scaffolding (Stripe-ready via Laravel Cashier)
- 🧩 Social login (Google, GitHub) via Laravel Socialite
- 🛡️ Two-factor authentication (TOTP)
- 🎨 Nuxt 3 frontend with Tailwind CSS, Pinia state management, and typed API composables
- 🔄 Auto-refreshing session middleware and route guards
- 🧪 Pest/PHPUnit test suite for backend, Vitest for frontend
- 🐳 Docker & Docker Compose setup for local development

---

## Tech Stack

| Layer      | Technology                              |
|------------|------------------------------------------|
| Backend    | Laravel 13, PHP 8.3+, Sanctum            |
| Frontend   | Nuxt 3, Vue 3, TypeScript, Tailwind CSS  |
| Database   | MySQL 8 / PostgreSQL (configurable)      |
| State      | Pinia                                    |
| Auth       | Laravel Sanctum (SPA cookie-based)       |
| Billing    | Laravel Cashier (Stripe)                 |
| Testing    | Pest, Vitest                             |
| Tooling    | Docker, Vite, ESLint, Prettier           |

---

## Project Structure

```
.
├── backend/                # Laravel 13 API
│   ├── app/
│   │   ├── Http/Controllers/Auth/
│   │   ├── Models/
│   │   └── Providers/
│   ├── routes/
│   │   ├── api.php
│   │   └── web.php
│   ├── config/
│   └── database/migrations/
│
├── frontend/                # Nuxt 3 SPA
│   ├── composables/
│   │   └── useAuth.ts
│   ├── middleware/
│   │   └── auth.global.ts
│   ├── pages/
│   │   ├── login.vue
│   │   ├── register.vue
│   │   └── dashboard/
│   ├── stores/
│   │   └── auth.ts
│   └── nuxt.config.ts
│
├── docker-compose.yml
└── README.md
```

---

## Prerequisites

- PHP 8.3+
- Composer 2.x
- Node.js 20+ and npm/pnpm
- MySQL 8 or PostgreSQL 15
- Docker & Docker Compose (optional, recommended)

---

## Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/your-org/laravel-nuxt-saas-starter.git
cd laravel-nuxt-saas-starter
```

### 2. Backend setup (Laravel)

```bash
cd backend
cp .env.example .env
composer install
php artisan key:generate
php artisan migrate --seed
php artisan storage:link
```

Update `.env` with your database credentials and the frontend URL:

```env
APP_URL=http://localhost:8000
FRONTEND_URL=http://localhost:3000
SANCTUM_STATEFUL_DOMAINS=localhost:3000
SESSION_DOMAIN=localhost
```

Start the API server:

```bash
php artisan serve
```

### 3. Frontend setup (Nuxt)

```bash
cd frontend
cp .env.example .env
npm install
```

Update `.env`:

```env
NUXT_PUBLIC_API_BASE=http://localhost:8000
```

Start the dev server:

```bash
npm run dev
```

The app will be available at `http://localhost:3000`.

### 4. Or run everything with Docker

```bash
docker compose up -d
```

---

## Authentication Flow

1. Nuxt requests a CSRF cookie from `/sanctum/csrf-cookie` before login/register.
2. Credentials are posted to `/login` or `/register`; Sanctum issues a session cookie.
3. The `auth.global.ts` middleware checks session state on each route change and redirects unauthenticated users to `/login`.
4. Authenticated requests to `/api/*` automatically include the session cookie.
5. `useAuth()` composable exposes `user`, `login()`, `logout()`, `register()`, and `fetchUser()` helpers backed by the Pinia auth store.

---

## Environment Variables

### Backend (`backend/.env`)

| Variable                     | Description                            |
|-------------------------------|-----------------------------------------|
| `APP_URL`                    | Base URL of the Laravel API             |
| `FRONTEND_URL`                | URL of the Nuxt app (used for CORS/redirects) |
| `SANCTUM_STATEFUL_DOMAINS`    | Domains allowed to use SPA auth         |
| `SESSION_DOMAIN`              | Shared cookie domain                    |
| `DB_*`                        | Database connection settings            |
| `STRIPE_KEY` / `STRIPE_SECRET`| Stripe billing credentials              |
| `GOOGLE_CLIENT_ID` / `SECRET` | Social login (Google)                   |
| `GITHUB_CLIENT_ID` / `SECRET` | Social login (GitHub)                   |

### Frontend (`frontend/.env`)

| Variable                | Description                    |
|--------------------------|---------------------------------|
| `NUXT_PUBLIC_API_BASE`  | Base URL of the Laravel API     |

---

## Testing

```bash
# Backend
cd backend
php artisan test

# Frontend
cd frontend
npm run test
```

---

## Deployment Notes

- Serve the Laravel API and Nuxt app on the same top-level domain (or configure `SANCTUM_STATEFUL_DOMAINS` and CORS carefully) so cookie-based auth works across subdomains.
- Use HTTPS in production — Sanctum's SPA authentication requires secure cookies for cross-site requests.
- Run `php artisan config:cache` and `npm run build` as part of your CI/CD pipeline.
- Queue workers are required for email verification/password reset notifications (`php artisan queue:work`).

---

## Roadmap

- [ ] Magic link login
- [ ] Passkey / WebAuthn support
- [ ] Audit log for account activity
- [ ] Usage-based billing metering

---

## License

MIT License. Use it, modify it, ship your SaaS with it.

---

## Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you'd like to change.
