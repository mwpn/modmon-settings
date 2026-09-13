# Settings v1 Compliance Report

**Module:** Settings (`Modules/Settings`)  
**Repository:** [mwpn/modmon-settings](https://github.com/mwpn/modmon-settings)  
**Version:** 1.0.0  
**Standard:** Module Authoring Standard v1 §14–15 (portable Definition of Done)  
**Date:** 2026-09-13  
**Certified on:** ModMon Foundation host (store Phase 1 + compliance Phase 2)

**Result:** **FULL COMPLIANCE** for Phase 1 scope (runtime store +
`RuntimeSettingsContract`). Admin UI / Experience contributions are
explicitly out of scope and marked N/A.

## Portability procedure

Isolated SQLite proof on a Foundation host (no host source edits):

1. Force isolated SQLite database for the proof.
2. Clear `storage/app/modules.json` (discovered-only).
3. Baseline host migrate.
4. Capture SHA-256 of watched host files.
5. `module:doctor settings` → all checks passed.
6. `module:install settings` → migrations applied; enabled;
   `settings.runtime` registered; contract bound.
7. `RuntimeSettingsContract::set/get` → values round-trip.
8. `module:disable settings` → capability removed; rows preserved.
9. `module:enable settings` → capability restored; values restored.
10. Re-hash watched host files → **identical**.

Watched host files (must remain unchanged):

- `bootstrap/app.php`
- `routes/web.php`
- `config/app.php`
- `config/auth.php`
- `composer.json`
- `composer.lock`
- `app/Foundation/FoundationServiceProvider.php`
- `app/Foundation/Runtime/ModuleManager.php`

Automated coverage: `Modules/Settings/Tests/Feature/SettingsComplianceTest.php`
(same checklist + migration fail-closed + empty requires).

## Certification checklist

### Manifest & identity

| Check | Status | Notes |
|-------|--------|-------|
| Valid `module.json` | PASS | Schema 1, code `settings`, version `1.0.0`, type `platform` |
| Provider loadable | PASS | `Modules\Settings\SettingsServiceProvider` |

### Lifecycle

| Check | Status | Notes |
|-------|--------|-------|
| Discovered before install | PASS | State null; `settings_entries` absent; capability absent; contract unbound |
| `module:doctor settings` before install | PASS | All checks passed |
| `module:install settings` owns migration | PASS | Creates `settings_entries`; reports Migrations applied |
| `settings.runtime` after install | PASS | Capability + contract available |
| Contract works after install | PASS | get/set/has/forget/all (Phase 1 suite) |
| Disable removes runtime capability | PASS | `settings.runtime` false; Experience N/A (none declared) |
| Disable preserves rows | PASS | Keys survive disable |
| Re-enable restores capability + values | PASS | Capability true; persisted values returned |
| Migration failure fail-closed | PASS | Conflicting table → Migration failed; NOT installed; no capability |

### Boundaries

| Check | Status | Notes |
|-------|--------|-------|
| No Identity/RBAC dependency | PASS | `requires.capabilities: []` |
| No Identity/RBAC/Foundation Runtime/Experience imports | PASS | `SettingsBoundaryTest` |
| Public-contract only | PASS | Consumers use `RuntimeSettingsContract` |
| No unrelated host source edits | PASS | Watched SHA-256 identical across install/disable/enable |

### Contributions (Phase 1 scope)

| Check | Status | Notes |
|-------|--------|-------|
| Routes / nav / permissions / dashboard | N/A | None in Phase 1 (by design) |
| Capability `settings.runtime` | PASS | Registered on enable; removed on disable |

### Documentation & tests

| Check | Status | Notes |
|-------|--------|-------|
| README contract sections | PASS | Provides, requires, contract semantics, ownership, install |
| Module tests | PASS | `php artisan test Modules/Settings/Tests` |

## Foundation gaps

| Gap | Blocker? | Action |
|-----|----------|--------|
| No `ContributesSettings` schema contribution API | No | Reported in Phase 0; not required for store/contract. Do not patch until Architecture Change Protocol. |

No Foundation runtime change is required to install this module.

## Verdict

**FULL COMPLIANCE** for Settings Phase 1 portable store.

## Install (copy-only)

```bash
git clone https://github.com/mwpn/modmon-settings.git /tmp/modmon-settings
cp -r /tmp/modmon-settings/Modules/Settings ./Modules/Settings
php artisan module:doctor settings
php artisan module:install settings
```

No host source surgery. No Identity/RBAC required.
