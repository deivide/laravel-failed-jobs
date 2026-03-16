# Laravel Failed Jobs - Architecture

## Executive Summary

This is a Laravel package providing a self-contained dashboard for viewing failed queue jobs. The architecture follows Laravel's package conventions (service provider, publishable resources, middleware) with a Vue.js 2 SPA frontend served via a catch-all route.

## Package Architecture

```
┌─────────────────────────────────────────────────────┐
│ Host Laravel Application                            │
│                                                     │
│  ┌───────────────────────────────────────────────┐  │
│  │ FailedJobsServiceProvider (auto-discovered)   │  │
│  │  - Registers routes under /failedjobs prefix  │  │
│  │  - Loads Blade views                          │  │
│  │  - Publishes config, assets, provider stub    │  │
│  │  - Registers Artisan commands                 │  │
│  └───────────────────┬───────────────────────────┘  │
│                      │                              │
│  ┌───────────────────▼───────────────────────────┐  │
│  │ Routes (web.php)                              │  │
│  │  GET /failedjobs/api/         → index (JSON)  │  │
│  │  GET /failedjobs/api/{id}     → show (JSON)   │  │
│  │  GET /failedjobs/{view?}      → SPA catch-all │  │
│  └───────────────────┬───────────────────────────┘  │
│                      │                              │
│  ┌───────────────────▼───────────────────────────┐  │
│  │ Authenticate Middleware                       │  │
│  │  - Checks FailedJobs::check($request)         │  │
│  │  - Throws ForbiddenException (403) on failure │  │
│  └───────────────────┬───────────────────────────┘  │
│                      │                              │
│  ┌───────────────────▼───────────────────────────┐  │
│  │ Controllers                                   │  │
│  │  FailedJobsController: DB query → JSON        │  │
│  │  HomeController: Serves Blade layout (SPA)    │  │
│  └───────────────────────────────────────────────┘  │
│                                                     │
│  ┌───────────────────────────────────────────────┐  │
│  │ Vue.js 2 SPA (compiled to public/app.js)      │  │
│  │  - Vue Router (history mode)                  │  │
│  │  - Axios HTTP client → /failedjobs/api/*      │  │
│  │  - Screens: index (list), job (detail)        │  │
│  │  - Components: SchemeToggler, Stacktrace      │  │
│  └───────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────┘
```

## Backend Architecture

### Service Providers

| Provider | Role |
|----------|------|
| `FailedJobsServiceProvider` | Core package provider. Registers routes, views, config, commands. Auto-discovered. |
| `FailedJobsApplicationServiceProvider` | Base class for the publishable user-side provider. Defines Gate and auth callback. |

### Data Access

The package queries the `failed_jobs` table directly via `DB::table('failed_jobs')` — no Eloquent models, no custom migrations. It relies on Laravel's built-in `failed_jobs` migration.

**FailedJobsController:**
- `index()` — Paginated query (50 per page), ordered by `failed_at` desc, JSON decodes payload
- `show($uuid)` — Single record lookup by UUID with validation

### Authentication Flow

```
Request → Authenticate Middleware
           → FailedJobs::check($request)
              → Custom callback (set via FailedJobs::auth())
              → OR default: app()->environment('local')
           → Pass / Throw ForbiddenException (403)
```

The host app customizes auth via a publishable `FailedJobsServiceProvider` that extends `FailedJobsApplicationServiceProvider` and defines a `viewFailedJobs` Gate.

### Configuration

Published to `config/failedjobs.php`:
- `domain` — Optional subdomain (env: `FAILEDJOBS_DOMAIN`)
- `path` — URI prefix, default `failedjobs` (env: `FAILEDJOBS_PATH`)
- `middleware` — Route middleware, default `['web']`

### Artisan Commands

| Command | Purpose |
|---------|---------|
| `failedjobs:install` | Publishes provider, assets, and config. Registers provider in app config. |
| `failedjobs:publish` | Republishes assets (force overwrite). Used for upgrades. |

## Frontend Architecture

### Technology

- **Vue.js 2** with Vue Router 3 (history mode)
- **Bootstrap 4** for layout/styling
- **SASS** with light (`app.scss`) and dark (`app-dark.scss`) themes
- **Laravel Mix 6** (Webpack) for compilation

### Screens

| Screen | Route | Component |
|--------|-------|-----------|
| Dashboard (job list) | `/` | `screens/failedJobs/index.vue` |
| Job detail | `/:jobId` | `screens/failedJobs/job.vue` |

### Components

| Component | Purpose |
|-----------|---------|
| `SchemeToggler.vue` | Light/dark theme toggle |
| `Stacktrace.vue` | Exception stack trace renderer with syntax highlighting |

### Data Flow

1. Vue SPA loads via `layout.blade.php` catch-all route
2. `window.FailedJobs` global object set from PHP (contains `path` config)
3. Axios calls `GET /failedjobs/api/` with pagination params
4. Auto-refresh every 3 seconds, checks for new entries by comparing latest UUID
5. Job detail deserializes PHP serialized commands using `phpunserialize` JS library

### Build Pipeline

- Source: `resources/js/` → `public/app.js`
- Styles: `resources/sass/` → `public/app.css`, `public/app-dark.css`
- Images: `resources/img/` → `public/img/`
- Published to host app: `public/vendor/failedjobs/`

## Testing Architecture

- **Framework:** PHPUnit with Orchestra Testbench
- **Base class:** `IntegrationTest` (extends `TestCase`, sets up Redis, registers package provider)
- **Controller base:** `ControllerTest` (extends `IntegrationTest`, sets app key, bypasses auth)
- **Test suites:** Unit (`tests/Unit/`), Feature (`tests/Feature/`)
- **CI matrix:** PHP 7.3–8.3 × Laravel 8–11 on GitHub Actions with Redis service

### Existing Tests

| Test | Coverage |
|------|----------|
| `AuthTest` | Authentication callback, middleware pass/fail, ForbiddenException |

## Dependencies

### Runtime (require)

| Package | Purpose |
|---------|---------|
| `illuminate/contracts` | Laravel contracts |
| `illuminate/queue` | Queue integration |
| `illuminate/support` | Laravel support utilities |
| `nesbot/carbon` | Date handling |
| `ramsey/uuid` | UUID support |
| `symfony/process` | Process management |
| `symfony/error-handler` | Error handling |

### Development (require-dev)

| Package | Purpose |
|---------|---------|
| `orchestra/testbench` | Laravel package testing |
| `phpunit/phpunit` | Test framework |
| `phpstan/phpstan` | Static analysis |
| `mockery/mockery` | Mocking |
| `predis/predis` | Redis client for tests |
