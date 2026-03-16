# Laravel Failed Jobs - Project Overview

## Purpose

Laravel-Failed-Jobs is a Composer package that provides a Horizon-like dashboard UI for viewing Laravel failed jobs. It reads directly from the `failed_jobs` database table and works with **any queue driver** — not just Redis/Horizon. The package is view-only (no retry/delete management) and can run locally or remotely from another Laravel application.

**Package:** `hpwebdeveloper/laravel-failed-jobs`
**Author:** Hamed Panjeh
**License:** MIT

## Tech Stack Summary

| Category | Technology | Version | Notes |
|----------|-----------|---------|-------|
| Language | PHP | ^7.3 \| ^8.0 | Backend logic |
| Framework | Laravel | 8.x – 11.x | Service provider, routes, middleware |
| Frontend | Vue.js | 2.x | SPA dashboard |
| Routing (FE) | Vue Router | 3.x | History mode |
| CSS | Bootstrap + SASS | 4.x | Light/dark theme support |
| Build | Laravel Mix (Webpack) | 6.x | Asset compilation |
| HTTP Client | Axios | ^1.6 | API calls from frontend |
| Testing | PHPUnit + Orchestra Testbench | ^9.0 \| ^10.4 | Package testing |
| Static Analysis | PHPStan | ^1.10 | Linting |
| Serialization | phpunserialize (JS) | 1.x | Deserializing PHP job payloads in browser |

## Architecture Type

**Library** — Distributed as a Composer package that integrates into Laravel applications via:
- Auto-discovered service provider (`FailedJobsServiceProvider`)
- Publishable config, assets, and service provider stub
- Artisan commands for installation and asset publishing

The package bundles a **Vue.js 2 SPA** dashboard that communicates with internal API routes to display failed job data.

## Repository Structure

- **Single-part monolith** — one cohesive codebase
- **Namespace:** `HPWebdeveloper\LaravelFailedJobs`
- **Entry point:** `FailedJobsServiceProvider` (auto-discovered via `composer.json` `extra.laravel.providers`)

## Key Features

1. **Failed Jobs Dashboard** — Paginated list view with job name, queue, attempts, and failure timestamp
2. **Job Detail View** — Full payload display (JSON pretty-print), exception stack trace, metadata
3. **Auto-refresh** — Polls for new entries every 3 seconds with optional auto-load toggle
4. **Dark Mode** — Light/dark theme toggler built into the UI
5. **Authentication** — Gate-based access control mirroring Laravel Horizon's pattern
6. **Remote Mode** — Can connect to a remote Laravel app's `failed_jobs` table via token-based auth
7. **PHP Unserialization** — Deserializes PHP serialized job command payloads in the browser

## Operational Modes

1. **Basic Mode** — Install in same app, reads local `failed_jobs` table, Gate-based auth
2. **Remote Mode** — Install in both apps, token-based auth (`FAILEDJOBS_SERVER_ACCESS_TOKEN`), CORS configuration required
