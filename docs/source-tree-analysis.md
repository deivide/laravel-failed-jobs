# Laravel Failed Jobs - Source Tree Analysis

## Directory Structure

```
laravel-failed-jobs/
├── config/
│   └── failedjobs.php              # Package configuration (domain, path, middleware)
├── public/                          # Compiled frontend assets (committed)
│   ├── app.js                       # Compiled Vue.js SPA bundle
│   ├── app.css                      # Compiled light theme styles
│   ├── app-dark.css                 # Compiled dark theme styles
│   ├── mix-manifest.json            # Laravel Mix manifest for versioned assets
│   └── img/
│       └── favicon.png              # Dashboard favicon
├── resources/                       # Frontend source files
│   ├── img/
│   │   └── favicon.png              # Source favicon
│   ├── js/
│   │   ├── app.js                   # ⚡ Vue app entry point (Vue Router, Axios, plugins)
│   │   ├── base.js                  # Vue mixin (date formatting, auto-load toggle)
│   │   ├── routes.js                # Vue Router route definitions
│   │   ├── components/
│   │   │   ├── SchemeToggler.vue    # Light/dark theme toggle component
│   │   │   └── Stacktrace.vue       # Exception stack trace renderer
│   │   └── screens/
│   │       └── failedJobs/
│   │           ├── index.vue        # Dashboard: paginated failed jobs list
│   │           └── job.vue          # Job detail: payload, exception, metadata
│   ├── sass/
│   │   ├── app.scss                 # Light theme entry
│   │   ├── app-dark.scss            # Dark theme entry
│   │   ├── base.scss                # Base styles
│   │   ├── _colors.scss             # Color variables
│   │   └── syntaxhighlight.scss     # Syntax highlighting styles
│   └── views/
│       └── layout.blade.php         # Blade template: SPA shell, loads JS/CSS
├── routes/
│   └── web.php                      # API routes (index, show) + SPA catch-all
├── src/                             # ⚡ Package PHP source (PSR-4 autoloaded)
│   ├── FailedJobs.php               # Facade-like class: auth, config, asset checks
│   ├── FailedJobsServiceProvider.php         # ⚡ Core service provider (routes, views, config, commands)
│   ├── FailedJobsApplicationServiceProvider.php  # Base class for publishable user provider
│   ├── Console/
│   │   ├── InstallCommand.php       # `failedjobs:install` Artisan command
│   │   └── PublishCommand.php       # `failedjobs:publish` Artisan command
│   ├── Exceptions/
│   │   └── ForbiddenException.php   # 403 HTTP exception
│   ├── Http/
│   │   ├── Controllers/
│   │   │   ├── Controller.php       # Base controller (applies Authenticate middleware)
│   │   │   ├── FailedJobsController.php  # API: failed_jobs DB queries, JSON responses
│   │   │   └── HomeController.php   # SPA: serves Blade layout view
│   │   └── Middleware/
│   │       └── Authenticate.php     # Auth middleware: checks FailedJobs::check()
├── stubs/
│   └── FailedJobsServiceProvider.stub  # Publishable service provider template
├── tests/
│   ├── ControllerTest.php           # Abstract controller test base
│   ├── IntegrationTest.php          # Abstract integration test base (Redis, Testbench)
│   ├── Feature/
│   │   └── AuthTest.php             # Authentication callback and middleware tests
│   └── Unit/                        # (empty)
├── workbench/                       # Orchestra Workbench (local dev environment)
│   ├── app/Providers/
│   │   └── FailedJobsServiceProvider.php  # Workbench-specific provider
│   └── database/
│       ├── migrations/              # (uses Laravel default)
│       └── seeders/
│           └── DatabaseSeeder.php   # Seeds test user for workbench
├── .github/
│   └── workflows/
│       ├── tests.yml                # CI: PHP 7.3–8.3 × Laravel 8–11 matrix
│       ├── static-analysis.yml      # PHPStan analysis
│       ├── compile-assets.yml       # Frontend asset compilation
│       └── ...                      # Issues, PRs, changelog workflows
├── composer.json                    # ⚡ PHP dependencies & autoload config
├── package.json                     # JS dev dependencies (Vue, Bootstrap, Mix)
├── webpack.mix.js                   # Laravel Mix build configuration
├── phpunit.xml.dist                 # PHPUnit configuration
├── phpstan.neon.dist                # PHPStan configuration
├── testbench.yaml                   # Orchestra Testbench configuration
├── README.md                        # Full installation & usage docs
├── CHANGELOG.md                     # Version history
├── UPGRADE.md                       # Upgrade instructions
└── LICENSE.md                       # MIT License
```

## Critical Folders

| Folder | Purpose |
|--------|---------|
| `src/` | All PHP package source code — service providers, controllers, middleware, commands |
| `resources/js/` | Vue.js 2 frontend source — app entry, routes, screens, components |
| `resources/sass/` | SASS stylesheets — light/dark themes |
| `resources/views/` | Blade template — SPA shell layout |
| `routes/` | API and catch-all route definitions |
| `config/` | Publishable package configuration |
| `public/` | Compiled/built frontend assets |
| `tests/` | PHPUnit test suites |

## Entry Points

| Entry Point | Type | Description |
|-------------|------|-------------|
| `src/FailedJobsServiceProvider.php` | PHP | Package bootstrap — auto-discovered by Laravel |
| `resources/js/app.js` | JS | Vue.js SPA initialization |
| `routes/web.php` | PHP | HTTP route definitions |
| `config/failedjobs.php` | PHP | Configuration defaults |
