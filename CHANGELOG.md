# Release Notes

## [v0.0.3](https://github.com/deivide/laravel-failed-jobs/releases/tag/v0.0.3) - 2026-03-16

### Added
- Retry button for failed jobs in listing page (icon-only) and detail page (icon + text)
- UUID field displayed in job detail page below ID
- Refresh icon on retry buttons
- Auto-release GitHub Actions workflow for Packagist publishing
- Project documentation (architecture, source tree, development guide)

### Changed
- Package renamed from `hpwebdeveloper/laravel-failed-jobs` to `deivide/laravel-failed-jobs`
- README updated with fork credits and retry feature description
- Sidebar removed from dashboard layout (full-width content)
- Build artifacts removed from repo, built in CI during release
- Release workflow tags detached build commits to keep main clean

### Fixed
- Auto Load Entries toggle not disabling when clicked again
- Auto Load Entries polling starting automatically on page load instead of only when enabled
- Release workflow tag detection for tags on detached build commits

## [v1.0.0](https://github.com/hpwebdeveloper) - 2024-01-16

* Works and functional
