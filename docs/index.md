# Project Documentation Index

> Generated: 2026-03-16 | Scan Level: Deep | Workflow: initial_scan

## Project Overview

- **Package:** `hpwebdeveloper/laravel-failed-jobs`
- **Type:** Monolith (Composer library/package)
- **Primary Language:** PHP 7.3+/8.0+
- **Framework:** Laravel 8.x – 11.x
- **Frontend:** Vue.js 2 SPA (dashboard UI)
- **Architecture Pattern:** Laravel Package (service provider, publishable resources, middleware + Vue SPA)

## Quick Reference

- **Namespace:** `HPWebdeveloper\LaravelFailedJobs`
- **Entry Point:** `FailedJobsServiceProvider` (auto-discovered)
- **Dashboard Route:** `/{failedjobs_path}/` (default: `/failedjobs/`)
- **API Routes:** `GET /failedjobs/api/` (list), `GET /failedjobs/api/{uuid}` (detail)
- **Build:** Laravel Mix 6 (`npm run dev` / `npm run prod`)
- **Tests:** `composer test` (requires Redis)

## Generated Documentation

- [Project Overview](./project-overview.md) — Purpose, tech stack summary, key features, operational modes
- [Architecture](./architecture.md) — Backend/frontend architecture, auth flow, data access, dependencies
- [Source Tree Analysis](./source-tree-analysis.md) — Annotated directory structure, critical folders, entry points
- [Development Guide](./development-guide.md) — Setup, build commands, testing, CI/CD, environment variables

## Existing Documentation

- [README](../README.md) — Installation instructions (basic & remote mode), dashboard access, licensing
- [CHANGELOG](../CHANGELOG.md) — Version history
- [UPGRADE](../UPGRADE.md) — Upgrade instructions between versions
- [LICENSE](../LICENSE.md) — MIT License

## Getting Started

1. Clone the repo and run `composer install && npm install`
2. Start Redis locally
3. Run `composer exec testbench serve` for the workbench dev environment
4. Access dashboard at `http://localhost:8000/failedjobs`
5. Run tests with `composer test`
6. Build frontend assets with `npm run dev` (or `npm run watch` for live reload)
