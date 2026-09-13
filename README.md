# modmon-settings

Portable Settings platform module for [ModMon Foundation](https://github.com/mwpn/modmon-foundation)
hosts. Provides database-backed runtime application/module configuration
via capability `settings.runtime` and public `RuntimeSettingsContract`.

**Status:** v1.0.0 — certified portable ([compliance report](docs/reports/settings-compliance-v1.md)).

This repository contains **only** the Settings module. It is not a
standalone Laravel application. Install it into a compatible Foundation
host.

## Requirements

| Requirement | Constraint |
|-------------|------------|
| PHP         | `^8.3`     |
| Laravel     | `^13.0`    |
| Foundation  | `^1.0`     |

No Identity or RBAC capability is required for the store.

Foundation must include accepted runtime fixes for portable modules
(migration path handling via Migrator API, post-boot route name lookup
refresh, provider `register()` re-invoke on enable). Foundation `main`
at or after the Settings v1 extraction satisfies this.

## Install from Git

```bash
# 1. In your ModMon Foundation host project root
git clone https://github.com/mwpn/modmon-settings.git /tmp/modmon-settings
cp -r /tmp/modmon-settings/Modules/Settings ./Modules/Settings

# 2. Verify and install (no host source edits required)
php artisan module:doctor settings
php artisan module:install settings
```

On Windows (PowerShell):

```powershell
git clone https://github.com/mwpn/modmon-settings.git $env:TEMP\modmon-settings
Copy-Item -Recurse $env:TEMP\modmon-settings\Modules\Settings .\Modules\Settings
php artisan module:doctor settings
php artisan module:install settings
```

Copying the module never runs migrations or mutates runtime state.
Migrations run only during `module:install`.

No changes to `bootstrap/app.php`, `routes/web.php`, host config, or
`.env` are required.

## Module documentation

Canonical module README (capabilities, contract semantics, ownership,
disable/enable):

[`Modules/Settings/README.md`](Modules/Settings/README.md)

## Testing (inside a Foundation host)

After copying the module into a host:

```bash
php artisan test Modules/Settings/Tests
```

## Repository layout

```
modmon-settings/
├── Modules/Settings/          # Portable module (copy this folder into host)
├── docs/reports/              # Compliance and certification artifacts
└── README.md                  # This file
```

## License

Proprietary — same terms as ModMon Foundation.
