# Laravel Failed Jobs - Development Guide

## Prerequisites

- **PHP** >= 7.3 (or 8.0+ recommended)
- **Composer** v2
- **Node.js** (for frontend asset compilation)
- **Redis** (required for tests — uses predis client)

## Installation (Development)

```bash
# Clone the repository
git clone https://github.com/HPWebdeveloper/laravel-failed-jobs.git
cd laravel-failed-jobs

# Install PHP dependencies
composer install

# Install JS dependencies
npm install
```

## Local Development with Workbench

The project uses [Orchestra Testbench](https://packages.tools/testbench/) with a workbench setup for local development.

Configuration is in `testbench.yaml`:
- Starts at `/failedjobs` route
- Uses predis Redis client
- Runs migrations and seeds a test user (`panjeh@gmail.com`)
- Syncs `public/` to `public/vendor/failedjobs`

```bash
# Serve the workbench application
composer exec testbench serve

# Or with specific options
composer exec testbench workbench:install
composer exec testbench serve --port=8000
```

## Frontend Development

### Build Commands

```bash
# Development build (with source maps)
npm run dev

# Watch for changes (auto-rebuild)
npm run watch

# Watch with polling (for VMs/Docker)
npm run watch-poll

# Hot module replacement
npm run hot

# Production build (minified, no console.log)
npm run prod
```

### Build Configuration

Build is configured in `webpack.mix.js`:
- Input: `resources/js/app.js` → Output: `public/app.js`
- Input: `resources/sass/app.scss` → Output: `public/app.css`
- Input: `resources/sass/app-dark.scss` → Output: `public/app-dark.css`
- Images copied from `resources/img/` to `public/img/`
- Versioned output (cache busting via `mix-manifest.json`)
- Production: `console.log` statements are stripped via Terser

## Testing

### Running Tests

```bash
# Run all tests
composer test
# or
vendor/bin/phpunit

# Run specific test suite
vendor/bin/phpunit --testsuite=Feature
vendor/bin/phpunit --testsuite=Unit
```

**Note:** Tests require a running Redis instance (default `localhost:6379`). The `REDIS_CLIENT` is set to `predis` in `phpunit.xml.dist`.

### Test Structure

| File | Type | Description |
|------|------|-------------|
| `tests/IntegrationTest.php` | Abstract base | Sets up Redis, registers package provider via Testbench |
| `tests/ControllerTest.php` | Abstract base | Extends IntegrationTest, sets app key, bypasses auth |
| `tests/Feature/AuthTest.php` | Feature test | Tests auth callback, middleware pass/fail |

### Static Analysis

```bash
# Run PHPStan
composer lint
# or
vendor/bin/phpstan analyse
```

## CI/CD

GitHub Actions workflows in `.github/workflows/`:

| Workflow | Trigger | Description |
|----------|---------|-------------|
| `tests.yml` | push, PR, daily cron | PHPUnit across PHP 7.3–8.3 × Laravel 8–11 matrix with Redis |
| `static-analysis.yml` | push, PR | PHPStan analysis |
| `compile-assets.yml` | — | Frontend asset compilation |
| `update-changelog.yml` | — | Automated changelog updates |

## Publishing (for package consumers)

When installed in a host Laravel application:

```bash
# First-time install
php artisan failedjobs:install
# Publishes: service provider, assets, config

# Upgrade assets after composer update
php artisan failedjobs:publish
# Force-republishes assets only
```

## Key Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `FAILEDJOBS_DOMAIN` | `null` | Subdomain for the dashboard |
| `FAILEDJOBS_PATH` | `failedjobs` | URI prefix for dashboard routes |
| `FAILEDJOBS_SERVER_ACCESS_TOKEN` | — | Remote mode: server-side access token |
| `FAILEDJOBS_DASHBOARD_ACCESS_TOKEN` | — | Remote mode: client-side access token |
| `AXIOS_BASE_URL` | `''` | Remote mode: base URL of the main application |
